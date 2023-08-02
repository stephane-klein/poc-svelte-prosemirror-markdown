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
    import {schema, defaultMarkdownParser, defaultMarkdownSerializer} from "prosemirror-markdown"
    import {exampleSetup} from "prosemirror-example-setup"

    let element;
    let editor;

    onMount(() => {
        editor = new EditorView(
            element,
            {
                state: EditorState.create({
                    doc: defaultMarkdownParser.parse("Hello **world**"),
                    plugins: exampleSetup({schema})
                }),
            }
        );
    });

    onDestroy(() => {
        if (editor) {
            editor.destroy()
        }
    });
</script>
Hello

<div bind:this={element}></div>
