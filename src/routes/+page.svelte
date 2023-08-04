<script>
    import './editor.css';
    import { onMount, onDestroy } from 'svelte'
    import MarkdownIt from 'markdown-it';

    import {EditorView as ProseEditorView} from "prosemirror-view"
    import {EditorState as ProseEditorState} from "prosemirror-state"
    import {schema as proseMarkdownSchema, defaultMarkdownParser, defaultMarkdownSerializer} from "../prosemirror-markdown/index.ts";
    import {exampleSetup as proseSetup} from "prosemirror-example-setup"

    let proseElement;
    let proseEditor;
    let viewMode = 'prose';
    let currentEditor = 'prose';
    let markdownContent;
    const md = new MarkdownIt();

    function createProseEditor(content) {
        proseEditor = new ProseEditorView(
            proseElement,
            {
                state: ProseEditorState.create({
                    doc: defaultMarkdownParser.parse(content),
                    plugins: proseSetup({schema: proseMarkdownSchema})
                }),
            }
        );
    }

    onMount(() => {
        createProseEditor(`
Hello world!

* item1
* item2
        `);
    });

    onDestroy(() => {
        if (proseEditor) {
            proseEditor.destroy()
        }
    })

    $: if ((viewMode == 'markdown') && (currentEditor != 'markdown')) {
        currentEditor = 'markdown';
        proseEditor.destroy();
        markdownContent = defaultMarkdownSerializer.serialize(proseEditor.state.doc);
    } else if ((viewMode == 'prose') && (currentEditor != 'prose')) {
        currentEditor = 'prose';
        createProseEditor(markdownContent);
    }
</script>

<div
    style:display={(viewMode == 'prose' ? 'block' : 'none')}
    bind:this={proseElement}
></div>

{#if viewMode == 'preview'}
    {@html md.render(defaultMarkdownSerializer.serialize(proseEditor.state.doc))}
{:else if viewMode == 'markdown'}
    <textarea bind:value={markdownContent} style="height: 20em;"></textarea>
{/if}

<p>
    <label for="prose">Prose </label><input bind:group={viewMode} type="radio" id="prose" name="view_mode" value="prose" />|
    <label for="markdown">Markdown </label><input bind:group={viewMode} type="radio" id="markdown" name="view_mode" value="markdown" />|
    <label for="preview">Preview </label><input bind:group={viewMode} type="radio" id="preview" name="view_mode" value="preview" />
</p>

