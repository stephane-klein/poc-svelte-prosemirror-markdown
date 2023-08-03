<script>
    import './editor.css';
    import { onMount, onDestroy } from 'svelte'
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

    onMount(() => {
        editor = new EditorView(
            element,
            {
                state: EditorState.create({
                    doc: defaultMarkdownParser.parse(`
Hello
                    `),
                    plugins: exampleSetup({schema})
                }),
            }
        );
    });

    onDestroy(() => {
        if (editor) {
            editor.destroy()
        }
    })
</script>
Hello

<div bind:this={element}></div>

<p>
    <label for="prose">Prose </label><input bind:group={viewMode} type="radio" id="prose" name="view_mode" value="prose" />|
    <label for="markdown">Markdown </label><input bind:group={viewMode} type="radio" id="markdown" name="view_mode" value="markdown" />|
    <label for="preview">Preview </label><input bind:group={viewMode} type="radio" id="preview" name="view_mode" value="preview" />
</p>

{viewMode}

<p>
    <button
        on:click={
        () => {
            console.log('ici1');
            console.log(defaultMarkdownSerializer.serialize(editor.state.doc));
        }
        }
    >Export to markdown</button> (see result in console log)
</p>
