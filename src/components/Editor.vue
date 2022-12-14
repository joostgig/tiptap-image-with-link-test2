<template>
  <div class="editor" v-if="editor">
    <div style="margin: 0 10px">
      <button @click="setImage">setImage</button>
      <button @click="setLink">setLink</button>
      <button @click="toggleBlockquote">setBlockquote</button>
      <button @click="setLinkImage">setLinkImage</button>
    </div>
    <editor-content :editor="editor" />
  </div>
</template>

<script>
import { Editor, EditorContent } from "@tiptap/vue-2";
import { StarterKit } from "@tiptap/starter-kit";
import Image from "@tiptap/extension-image";
import Link from "@tiptap/extension-link";
import Blockquote from "@tiptap/extension-blockquote";

export default {
  components: {
    EditorContent,
  },

  data() {
    return {
      editor: null,
    };
  },

  mounted() {
    this.editor = new Editor({
      extensions: [
        StarterKit,
        Image,
        Blockquote,
        Link.configure({ openOnClick: true }),
      ],
      editorProps: {
        attributes: {
          class: "Editor",
        },
      },
      content: `
        <h1>Link example</h1>
        <a href="/click-me"><img src="https://images.pexels.com/photos/8197511/pexels-photo-8197511.jpeg?w=320&h=160"></a>
      `,
    });
  },

  methods: {
    toggleBlockquote() {
      this.editor.chain().focus().toggleBlockquote().run();
    },
    setImage() {
      const url = window.prompt("URL");

      if (url) {
        this.editor.chain().focus().setImage({ src: url }).run();
      }
    },
    setLink() {
      const previousUrl = this.editor.getAttributes("link").href;
      const url = window.prompt("URL", previousUrl);

      console.log("set link");

      // cancelled
      if (url === null) {
        return;
      }

      console.log("isActive link", this.editor.isActive("link"));
      console.log("isActive image", this.editor.isActive("image"));

      // empty
      if (url === "") {
        this.editor.chain().focus().extendMarkRange("link").unsetLink().run();

        return;
      }

      // update link
      this.editor
        .chain()
        .focus()
        // .extendMarkRange("link")
        .setLink({ href: url })
        .run();
    },
    setLinkImage() {
      console.log("setLinkImage");
      this.editor
        .chain()
        .focus()
        .extendMarkRange("blockquote")
        .setLink({ href: "/test123" })
        .run();
    },
  },
};
</script>