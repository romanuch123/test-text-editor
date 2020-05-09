<template>
  <div class="text-editor">
    <div class="CodeMirror-cursors" style="visibility: hidden;"><div class="CodeMirror-cursor" style="left: 80.9517px; top: 0px; height: 20.9091px;">&nbsp;</div></div>
    <div
      id="text-editor-field"
      class="relative-position cursor_normal none_outline"
      spellcheck="false"
      contenteditable="true"
    >
      <span
        v-for="textItem in text"
        :key="textItem.id"
        :style="{ color: textItem.color, fontSize: textItem.fontSize, backgroundColor: textItem.backgroundColor }"
        @keydown="testik"
      >{{ textItem.text }}</span>
      <!-- <span style="background-color: rgb(248, 187, 208); padding-left:3px; padding-right: 3px; border-radius: 4px;">Hi</span>
        <br/>
        <span style="background-color: rgb(248, 187, 208); padding-left:3px; padding-right: 3px; border-radius: 4px;">My lovely</span>
        <span style="color: rgb(136, 14, 79); font-size: 54px">little</span>
      <span style="color: rgb(186, 104, 200); background-color: rgb(248, 187, 0);">Ponny</span> -->
    </div>
    <button @click="newStyle">
      runit
    </button>
    <div class="btn-group">
      <StyleButton name="Color" @change-style="changeColor"/>
      <StyleButton name="Font" @change-style="changeFontSize"/>
      <StyleButton name="Background" @change-style="changeColor"/>
    </div>
    <Console />
  </div>
</template>

<script>
import StyleButton from './StyleButton.vue';
import Console from './Console.vue';

export default {
  name: 'TextEditor',
  components: {
    StyleButton,
    Console,
  },
  props: {
  },
  data() {
    return {
      text: [
        {
          id: 1,
          text: 'Hi',
          fontSize: '20px',
          color: 'red',
          backgroundColor: 'green',
        },
        {
          id: 2,
          text: 'My lovely',
          fontSize: '20px',
          color: 'red',
          backgroundColor: 'green',
        },
        {
          id: 3,
          text: 'little',
          fontSize: '30px',
          color: 'pink',
          backgroundColor: '',
        },
        {
          id: 4,
          text: 'ponny',
          fontSize: '30px',
          color: 'pink',
          backgroundColor: 'yellow',
        },
      ]
    };
  },
  methods: {
    testik() {
      console.log('working');
    },
    addNewLine(e) {
      console.log(e);
      // trap the return key being pressed
      if (e.keyCode === 13) {
          // insert 2 br tags (if only one br tag is inserted the cursor won't go to the next line)
          document.execCommand('insertHTML', false, '<br/>');
          // prevent the default behaviour of return key pressed
          return false;
      }
    },
    changeColor() {
      const text = this.selectedText();
      console.log(text.toString());
      if (text.toString()) {
        const firstSelectedBlock = text.anchorNode.parentElement;
        const lastSelectedBlock = text.extentNode.parentElement;
        const indexOfFirstSelectedBlock = [...firstSelectedBlock.parentElement.children].indexOf(firstSelectedBlock);
        const indexOfLastSelectedBlock = [...lastSelectedBlock.parentElement.children].indexOf(lastSelectedBlock);
        const randomColor = '#'+((1<<24)*Math.random()|0).toString(16);
        const indexOfFirstSelectedLetter = text.anchorOffset;
        const indexOfLastSelectedLetter = text.extentOffset;
        if (indexOfFirstSelectedBlock === indexOfLastSelectedBlock) {
          if (indexOfFirstSelectedLetter === 0 && indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text[indexOfFirstSelectedBlock].color = randomColor;
          } else if (indexOfFirstSelectedLetter === 0) {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              text: text.toString(),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          } else if (indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              text: text.toString(),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock].text = this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter);
          } else {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              text: text.toString(),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            }, {
              text: this.text[indexOfFirstSelectedBlock].text,
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock].text = this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter);
            this.text[indexOfFirstSelectedBlock + 2].text = this.text[indexOfFirstSelectedBlock + 2].text.slice(indexOfLastSelectedLetter);
          }
        } else {
          for (let i = indexOfFirstSelectedBlock + 1; i < indexOfLastSelectedBlock; i++) {
            this.text[i].color = randomColor;
          }
          if (indexOfLastSelectedLetter === this.text[indexOfLastSelectedBlock].text.length) {
            this.text[indexOfLastSelectedBlock].color = randomColor;
          } else {
            this.text.splice(indexOfLastSelectedBlock , 0, {
              text: this.text[indexOfLastSelectedBlock].text.slice(0, indexOfLastSelectedLetter),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfLastSelectedBlock + 1].text = this.text[indexOfLastSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          }
          if (indexOfFirstSelectedLetter === 0) {
            this.text[indexOfFirstSelectedBlock].color = randomColor;
          } else {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              text: this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfFirstSelectedLetter);
            this.text[indexOfFirstSelectedBlock + 1].color = randomColor;
          }
          console.log(indexOfLastSelectedLetter);
          console.log(this.text[indexOfLastSelectedBlock].text.length);
        }
        text.removeAllRanges();
      }
    },
    changeFontSize() {
      this.text[0].color = 'black';
    },
    test() {
      console.log(1);
    },
    runit() {
          let text = "";
    if (window.getSelection) {
        text = window.getSelection();
    } else if (document.selection && document.selection.type != "Control") {
        text = document.selection.createRange();
    }
    // return text;
      console.log(text.toString());
      console.log(text);
      console.log(text.extentNode);
      console.log(window.getSelection().anchorNode.textContent.substring(
  window.getSelection().extentOffset, 
  window.getSelection().anchorOffset));
      console.log(window.getSelection().extentOffset);
      const el = text.extentNode.parentElement;
      const index = [...el.parentElement.children].indexOf(el);
      console.log(index);
      console.log(text);
      const offset = text.anchorOffset;
      this.text[index].text = this.text[index].text.slice(0, offset);
  
    },
    newStyle() {
      const text = this.selectedText();




      console.log(text.toString());
      console.log(text);
      console.log(text.extentNode);
      console.log(window.getSelection().anchorNode.textContent.substring(
  window.getSelection().extentOffset, 
  window.getSelection().anchorOffset));
      console.log(window.getSelection().extentOffset);
      const firstSelectedBlock = text.anchorNode.parentElement;
      const lastSelectedBlock = text.extentNode.parentElement;
      const indexOfFirstSelectedBlock = [...firstSelectedBlock.parentElement.children].indexOf(firstSelectedBlock);
      const indexOfLastSelectedBlock = [...lastSelectedBlock.parentElement.children].indexOf(lastSelectedBlock);

      console.log('indexes:');
      console.log(indexOfFirstSelectedBlock);
      console.log(indexOfLastSelectedBlock);
      // this.text = this.text.splice(1);
      // console.log(this.text.splice(1, 1));
      // this.text.splice(indexOfFirstSelectedBlock, 1, {
      //   text: 'yeah',
      //   color: 'green',
      //   fontSize: '40px',
      //   backgroundColor: '',
      // });
      // const offset = text.anchorOffset;
      // this.text[index].text = this.text[index].text.slice(0, offset);
  
    },
    selectedText() {
      let text = "";
      if (window.getSelection) {
          text = window.getSelection();
      } else if (document.selection && document.selection.type != "Control") {
          text = document.selection.createRange();
      }
      return text;
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #text-editor-field {
    color: rgb(255, 255, 255);
    opacity: 1;
    border-radius: 6px; transform: rotate(0deg);
    border: 1px solid rgb(70, 144, 247);
    padding: 10px;
    text-align: center;
    letter-spacing: 0px;
    font-size: 32px;
    font-family: Aleo;
    font-weight: 300;
    text-decoration: none;
    box-shadow: rgb(0, 0, 0) 0px 0px 0px 0px;
    text-transform: initial;
    font-style: normal;
    z-index: 2002;
    text-shadow: rgba(0, 0, 0, 0.2) 2px 2px 0px;
    line-height: 1.5em;
    display: inline-block;
    width: 90%;
  }
  #text-editor-field:focus {
    outline: none;
  }
  #text-editor-field span {
    border-radius: 5px;
    padding: 2px 5px;
  }
</style>
