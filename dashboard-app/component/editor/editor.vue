<template lang="html">
  <div id="editor">
    <editor-menu-bar :editor="editor">
      <div slot-scope="{ commands, isActive }" class="editor-button-wrapper">
        <button
          :class="{ 'is-active': isActive.italic() }"
          @click="commands.italic"
        >
          <i class="material-icons-round">
            format_italic
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.code() }"
          @click="commands.code"
        >
          <i class="material-icons-round">
            code
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.heading({ level: 1 }) }"
          @click="commands.heading({ level: 1 })"
        >
          H1
        </button>
        <button
          :class="{ 'is-active': isActive.heading({ level: 2 }) }"
          @click="commands.heading({ level: 2 })"
        >
          H2
        </button>
        <button
          :class="{ 'is-active': isActive.heading({ level: 3 }) }"
          @click="commands.heading({ level: 3 })"
        >
          H3
        </button>
        <button
          :class="{ 'is-active': isActive.bold() }"
          @click="commands.bold"
        >
          <i class="material-icons-round">
            format_bold
          </i>
        </button>
        <DropdownSlot class="dropdown-wrapper" icon="format_color_text">
          <button
            v-for="itemColor in colors"
            :class="{ 'is-active': isActive.textcolor({ color: itemColor }) }"
            @click="commands.textcolor({ color: itemColor })"
          >
            <div :style="{ 'background-color': itemColor }"></div>
          </button>
        </DropdownSlot>
        <button
          :class="{ 'is-active': isActive.bullet_list() }"
          @click="commands.bullet_list"
        >
          <i class="material-icons-round">
            format_list_bulleted
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.ordered_list() }"
          @click="commands.ordered_list"
        >
          <i class="material-icons-round">
            format_list_numbered
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.code_block() }"
          @click="commands.code_block"
        >
          <i class="material-icons-round">
            code
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.paragraph({ textAlign: 'left' }) }"
          @click="commands.paragraph({ textAlign: 'left' })"
        >
          <i class="material-icons-round">
            format_align_left
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.paragraph({ textAlign: 'center' }) }"
          @click="commands.paragraph({ textAlign: 'center' })"
        >
          <i class="material-icons-round">
            format_align_center
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.paragraph({ textAlign: 'right' }) }"
          @click="commands.paragraph({ textAlign: 'right' })"
        >
          <i class="material-icons-round">
            format_align_right
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.paragraph({ textAlign: 'justify' }) }"
          @click="commands.paragraph({ textAlign: 'justify' })"
        >
          <i class="material-icons-round">
            format_align_justify
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.blockquote() }"
          @click="commands.blockquote"
        >
          <i class="material-icons-round">
            format_quote
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.strike() }"
          @click="commands.strike"
        >
          <i class="material-icons-round">
            format_strikethrough
          </i>
        </button>
        <button
          :class="{ 'is-active': isActive.underline() }"
          @click="commands.underline"
        >
          <i class="material-icons-round">
            format_underlined
          </i>
        </button>
        <button @click="showImagePrompt(commands.image)">
          <i class="material-icons-round">
            insert_photo
          </i>
        </button>
      </div>
    </editor-menu-bar>
    <div
      id="editor-wrapper"
      v-bind:class="{ 'focus': hasEditorFocus }">
      <VuePerfectScrollbar class="scroll-area">
        <editor-content :editor="editor" />
      </VuePerfectScrollbar>
    </div>
    <label
      v-show="errorMessage"
      id="input-error-message">
      {{ $t(errorMessage) }}
    </label>
    <label
      v-show="!errorMessage"
      id="input-helper-message">
      {{ $t(helperMessage) }}
    </label>
  </div>
</template>

<script>
import VuePerfectScrollbar from 'vue-perfect-scrollbar'
import { Editor, EditorContent, EditorMenuBar } from 'tiptap'
import {
  Blockquote,
  CodeBlock,
  HardBreak,
  Heading,
  OrderedList,
  BulletList,
  ListItem,
  TodoItem,
  TodoList,
  Bold,
  Code,
  Italic,
  Link,
  Strike,
  Underline,
  History,
  Image,
} from 'tiptap-extensions'
import Paragraph from './Paragraph'
import Button from '../templates/button.vue'
import TextColor from './color'
import DropdownSlot from '../templates/dropdown-slot.vue'

export default {
  props: [
    'content',
    'onChangeContent',
    'helperMessage',
    'errorMessage',
  ],
  data() {
    return {
      editorContent: '',
      editor: null,
      hasEditorFocus: false,
      editorProps: {
        extensions: [
          new Blockquote(),
          new CodeBlock(),
          new HardBreak(),
          new Heading({ levels: [1, 2, 3] }),
          new BulletList(),
          new OrderedList(),
          new ListItem(),
          new TodoItem(),
          new TodoList(),
          new Bold(),
          new Code(),
          new Italic(),
          new Link(),
          new Strike(),
          new Underline(),
          new History(),
          new Image(),
          new TextColor(),
          new Paragraph(),
        ],
        content: '',
        onUpdate: this.onUpdate,
        onFocus: this.onFocusEditor,
        onBlur: this.onBlurEditor,
      },
      colors: [
        '#f44336',
        '#e91e63',
        '#9c27b0',
        '#673ab7',
        '#3f51b5',
        '#2196f3',
        '#03a9f4',
        '#00bcd4',
        '#009688',
        '#4caf50',
        '#8bc34a',
        '#cddc39',
        '#ffeb3b',
        '#ffc107',
        '#ff9800',
        '#ff5722',
        '#000000',
        '#333333',
        '#666666',
        '#aaaaaa',
      ],
    }
  },
  components: {
    VuePerfectScrollbar,
    EditorMenuBar,
    EditorContent,
    Button,
    DropdownSlot,
  },
  created() {
    this.editor = new Editor(this.editorProps)
  },
  watch: {
    content (newVal, oldVal) {
      this.editor.setContent(newVal)
    },
  },
  methods: {
    onUpdate({ getJSON, getHTML }) {
      this.onChangeContent({ getJSON, getHTML })
    },
    showImagePrompt(command) {
      const src = prompt('Enter the url of your image here')
      if (src !== null) {
        command({ src })
      }
    },
    onFocusEditor (event) {
      this.hasEditorFocus = true
    },
    onBlurEditor (event) {
      this.hasEditorFocus = false
    },
  },
  beforeDestroy() {
    this.editor.destroy()
  },
}
</script>

<style scoped lang="css">
#editor {
  margin: 0 0 25px 0;
  position: relative;
}

.editor-button-wrapper {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

button {
  align-self: center;
  background: transparent;
  border-radius: 20px;
  border: none;
  color: var(--main-text-color);
  cursor: pointer;
  display: block;
  display: flex;
  font-size: var(--main-font-size);
  font-weight: 600;
  justify-content: center;
  margin-left: 3px;
  margin-right: 3px;
  outline: none;
  padding: 7px;
  position: relative;
  text-transform: uppercase;
}

button:hover {
  background-color: var(--main-hover-color);
  color: var(--main-accent-color);
}

button i {
  font-size: 20px;
  margin: auto;
}

#input-error-message, #input-helper-message {
  font-size: var(--main-font-size);
  font-weight: 500;
  position: absolute;
  top: calc(100% + 2px);
  width: 100%;
}

#input-error-message {
  color: #ff4949;
}

#input-helper-message {
  color: #777;
}

#input-title.error {
  color: #ff4949;
}

#editor-wrapper {
  height: 150px;
  overflow: auto;
  border-bottom: 1px solid var(--main-text-color);
}

#editor-wrapper.focus {
  border-bottom: 1px solid var(--main-accent-color);
}
</style>

<style lang="css">
.ProseMirror {
  background: transparent;
  caret-color: var(--main-accent-color);
  font-size: var(--main-font-size);
  font-weight: 600;
  margin: 0;
  outline: none;
  padding: 0;
  width: 100%;
}

.dropdown-wrapper button {
  display: inline-flex;
  margin: 0px;
  padding: 0px;
}

.dropdown-wrapper button div {
  height: 30px;
  width: 30px;
}

button.is-active {
  background-color: var(--main-hover-color);
}
</style>
