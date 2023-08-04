<script>
    import './editor.css';
    import { onMount, onDestroy } from 'svelte'
    import MarkdownIt from 'markdown-it';

    import {basicSetup as CodeBasicSetup, EditorView as CodeEditorView} from "codemirror";
    import {Compartment} from "@codemirror/state";
    import {markdown} from "@codemirror/lang-markdown";

    import {EditorView as ProseEditorView} from "prosemirror-view"
    import {EditorState as ProseEditorState} from "prosemirror-state"
    import {schema as proseMarkdownSchema, defaultMarkdownParser, defaultMarkdownSerializer} from "../prosemirror-markdown/index.ts";
    import {exampleSetup as proseSetup} from "prosemirror-example-setup"

    let proseElement;
    let proseEditorView;

    let codeElement;
    let codeEditorView;
    let languageConf = new Compartment;

    let viewMode = 'prose';
    let currentEditor = 'prose';
    let markdownContent;
    const md = new MarkdownIt();

    function createProseEditor(content) {
        proseEditorView = new ProseEditorView(
            proseElement,
            {
                state: ProseEditorState.create({
                    doc: defaultMarkdownParser.parse(content),
                    plugins: proseSetup({schema: proseMarkdownSchema})
                }),
            }
        );

    }

    function createCodeEditor(content) {
        codeEditorView = new CodeEditorView({
            parent: codeElement,
            doc: content,
            extensions: [
                CodeBasicSetup,
                languageConf.of(markdown())
            ]
        });
    }

    onMount(() => {
        createProseEditor(`
Hello world!

* item1
* item2
        `);
        // createCodeEditor('Hello world');
    });

    onDestroy(() => {
        if (proseEditorView) {
            proseEditorView.destroy()
        }
        if (codeEditorView) {
            codeEditorView.destroy();
        }
    })

    $: if ((viewMode == 'markdown') && (currentEditor != 'markdown')) {
        currentEditor = 'markdown';
        proseEditorView.destroy();
        markdownContent = defaultMarkdownSerializer.serialize(proseEditorView.state.doc);
        createCodeEditor(markdownContent);
    } else if ((viewMode == 'prose') && (currentEditor != 'prose')) {
        currentEditor = 'prose';
        markdownContent = codeEditorView.state.doc.toString();
        codeEditorView.destroy();
        createProseEditor(markdownContent);
    }
</script>


<div
    style:display={(viewMode == 'prose' ? 'block' : 'none')}
    bind:this={proseElement}
></div>
<div
    style:display={(viewMode == 'markdown' ? 'block' : 'none')}
    bind:this={codeElement}
></div>

{#if viewMode == 'preview'}
    {@html md.render(markdownContent)}
{/if}

<p>
    <label for="prose">Prose </label><input bind:group={viewMode} type="radio" id="prose" name="view_mode" value="prose" />|
    <label for="markdown">Markdown </label><input bind:group={viewMode} type="radio" id="markdown" name="view_mode" value="markdown" />|
    <label for="preview">Preview </label><input bind:group={viewMode} type="radio" id="preview" name="view_mode" value="preview" />
</p>

