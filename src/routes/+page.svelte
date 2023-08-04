<script>
    import './editor.css';
    import { onMount, onDestroy } from 'svelte'
    import MarkdownIt from 'markdown-it';
    /*
    class MarkdownView {
        constructor(target, content) {
            this.textarea = target.appendChild(document.createElement("textarea"))
            this.textarea.value = content
        }

        get content() { return this.textarea.value }
        focus() { this.textarea.focus() }
        destroy() { this.textarea.remove() }
    }
    */

    import {EditorView} from "prosemirror-view"
    import {EditorState} from "prosemirror-state"
    import {schema, defaultMarkdownParser, defaultMarkdownSerializer} from "../prosemirror-markdown/index.ts";
    import {exampleSetup} from "prosemirror-example-setup"

    let element;
    let editor;
    let viewMode = 'prose';
    let currentEditor = 'prose';
    let markdownContent;
    const md = new MarkdownIt();

    function createProseEditor(content) {
        editor = new EditorView(
            element,
            {
                state: EditorState.create({
                    doc: defaultMarkdownParser.parse(content),
                    plugins: exampleSetup({schema})
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
        if (editor) {
            editor.destroy()
        }
    })

    $: if ((viewMode == 'markdown') && (currentEditor != 'markdown')) {
        currentEditor = 'markdown';
        editor.destroy();
        markdownContent = defaultMarkdownSerializer.serialize(editor.state.doc);
    } else if ((viewMode == 'prose') && (currentEditor != 'prose')) {
        currentEditor = 'prose';
        createProseEditor(markdownContent);
    }
</script>

<div
    style:display={(viewMode == 'prose' ? 'block' : 'none')}
    bind:this={element}
></div>

{#if viewMode == 'preview'}
    {@html md.render(defaultMarkdownSerializer.serialize(editor.state.doc))}
{:else if viewMode == 'markdown'}
    <textarea bind:value={markdownContent} style="height: 20em;"></textarea>
{/if}

<p>
    <label for="prose">Prose </label><input bind:group={viewMode} type="radio" id="prose" name="view_mode" value="prose" />|
    <label for="markdown">Markdown </label><input bind:group={viewMode} type="radio" id="markdown" name="view_mode" value="markdown" />|
    <label for="preview">Preview </label><input bind:group={viewMode} type="radio" id="preview" name="view_mode" value="preview" />
</p>

