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
    import { schema, defaultMarkdownParser, defaultMarkdownSerializer} from "../prosemirror-markdown/index.ts";
    import {exampleSetup} from "prosemirror-example-setup"

    let element;
    let editor;

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
    <button
        on:click={
        () => {
            console.log('ici1');
            console.log(defaultMarkdownSerializer.serialize(editor.state.doc));
        }
        }
    >Export to markdown</button> (see result in console log)
</p>
