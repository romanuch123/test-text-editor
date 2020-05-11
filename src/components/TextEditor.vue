<template>
  <div class="text-editor">
    <h1>Text Editor</h1>
    <div
      id="text-editor-field"
      class="relative-position cursor_normal none_outline"
      spellcheck="false"
      contenteditable="true"
      @blur="editText"
      @mousedown="editText"
    >
      <span
        v-for="textItem in text"
        :key="textItem.id"
        :style="{ color: textItem.color, fontSize: textItem.fontSize, backgroundColor: textItem.backgroundColor }"
      >{{ textItem.text }}</span>
    </div>
    <div class="btn-group">
      <StyleButton name="Color" @change-style="changeColor"/>
      <StyleButton name="Font" @change-style="changeFontSize"/>
      <StyleButton name="Background" @change-style="changeBackgroundColor"/>
    </div>
    <Console :jsonText="jsonText" />
    <button class="btn-text-converter" @click="convertTextToJson">Text to JSON</button>
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
      jsonText: '',
      text: [
        {
          id: '1',
          text: 'Hi ',
          fontSize: '20px',
          color: 'red',
          backgroundColor: 'blue',
        },
        {
          id: '2',
          text: 'My friend ',
          fontSize: '20px',
          color: 'red',
          backgroundColor: 'green',
        },
        {
          id: '3',
          text: 'Nice ',
          fontSize: '30px',
          color: 'pink',
          backgroundColor: '',
        },
        {
          id: '4',
          text: 'to ',
          fontSize: '30px',
          color: 'grey',
          backgroundColor: 'yellow',
        },
        {
          id: '5',
          text: 'meet ',
          fontSize: '30px',
          color: 'aqua',
          backgroundColor: 'yellow',
        },
        {
          id: '6',
          text: 'you',
          fontSize: '30px',
          color: 'black',
          backgroundColor: 'yellow',
        },
      ]
    };
  },
  methods: {
    randomId() {
      return `f${(+new Date).toString(16)}`;
    },
    editText() {
      let textFieldElements = Array.from(document.getElementById('text-editor-field').children);
      textFieldElements = textFieldElements.filter(el => el.localName === 'span');
      if (!textFieldElements.length) {
        this.text.splice(0);
        const textField = document.getElementById('text-editor-field');
        if (textField.innerText) {
          this.text.push({
            id: '1',
            text: textField.innerText,
            fontSize: '20px',
            color: 'black',
            backgroundColor: '',
          });
          textField.innerText = '';
        }
      }
      textFieldElements.forEach((el, i) => {
        this.text[i].text = el.innerText;
      });
    },
    changeColor() {
      this.editText();
      const selectedText = this.selectedText();
      if (selectedText.toString()) {
        const firstSelectedBlock = selectedText.anchorNode.parentElement;
        const lastSelectedBlock = selectedText.extentNode.parentElement;
        const indexOfFirstSelectedBlock = [...firstSelectedBlock.parentElement.children].indexOf(firstSelectedBlock);
        const indexOfLastSelectedBlock = [...lastSelectedBlock.parentElement.children].indexOf(lastSelectedBlock);
        const randomColor = '#'+((1<<24)*Math.random()|0).toString(16);
        const indexOfFirstSelectedLetter = selectedText.anchorOffset;
        const indexOfLastSelectedLetter = selectedText.extentOffset;
        if (indexOfFirstSelectedBlock === indexOfLastSelectedBlock) {
          if (indexOfFirstSelectedLetter === 0 && indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text[indexOfFirstSelectedBlock].color = randomColor;
          } else if (indexOfFirstSelectedLetter === 0) {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              id: this.randomId() + '6',
              text: selectedText.toString(),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          } else if (indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              id: this.randomId() + '5',
              text: selectedText.toString(),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock].text = this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter);
          } else {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              id: this.randomId() + '4',
              text: selectedText.toString(),
              color: randomColor,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            }, {
              id: this.randomId() + '3',
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
              id: this.randomId() + '2',
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
              id: this.randomId() + '1',
              text: this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfFirstSelectedLetter);
            this.text[indexOfFirstSelectedBlock + 1].color = randomColor;
          }
        }
        selectedText.removeAllRanges();
        this.mergeTextBlocks();
      }
    },
    changeBackgroundColor() {
      this.editText();
      const selectedText = this.selectedText();
      if (selectedText.toString()) {
        const firstSelectedBlock = selectedText.anchorNode.parentElement;
        const lastSelectedBlock = selectedText.extentNode.parentElement;
        const indexOfFirstSelectedBlock = [...firstSelectedBlock.parentElement.children].indexOf(firstSelectedBlock);
        const indexOfLastSelectedBlock = [...lastSelectedBlock.parentElement.children].indexOf(lastSelectedBlock);
        const randomColor = '#'+((1<<24)*Math.random()|0).toString(16);
        const indexOfFirstSelectedLetter = selectedText.anchorOffset;
        const indexOfLastSelectedLetter = selectedText.extentOffset;
        if (indexOfFirstSelectedBlock === indexOfLastSelectedBlock) {
          if (indexOfFirstSelectedLetter === 0 && indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text[indexOfFirstSelectedBlock].backgroundColor = randomColor;
          } else if (indexOfFirstSelectedLetter === 0) {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              id: this.randomId() + '6',
              text: selectedText.toString(),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: randomColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          } else if (indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              id: this.randomId() + '5',
              text: selectedText.toString(),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: randomColor,
            });
            this.text[indexOfFirstSelectedBlock].text = this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter);
          } else {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              id: this.randomId() + '4',
              text: selectedText.toString(),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: randomColor,
            }, {
              id: this.randomId() + '3',
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
            this.text[i].backgroundColor = randomColor;
          }
          if (indexOfLastSelectedLetter === this.text[indexOfLastSelectedBlock].text.length) {
            this.text[indexOfLastSelectedBlock].backgroundColor = randomColor;
          } else {
            this.text.splice(indexOfLastSelectedBlock , 0, {
              id: this.randomId() + '2',
              text: this.text[indexOfLastSelectedBlock].text.slice(0, indexOfLastSelectedLetter),
              color: this.text[indexOfLastSelectedBlock].color,
              fontSize: this.text[indexOfLastSelectedBlock].fontSize,
              backgroundColor: randomColor,
            });
            this.text[indexOfLastSelectedBlock + 1].text = this.text[indexOfLastSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          }
          if (indexOfFirstSelectedLetter === 0) {
            this.text[indexOfFirstSelectedBlock].backgroundColor = randomColor;
          } else {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              id: this.randomId() + '1',
              text: this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfFirstSelectedLetter);
            this.text[indexOfFirstSelectedBlock + 1].backgroundColor = randomColor;
          }
        }
        selectedText.removeAllRanges();
        this.mergeTextBlocks();
      }
    },
    changeFontSize() {
      this.editText();
      const selectedText = this.selectedText();
      if (selectedText.toString()) {
        const firstSelectedBlock = selectedText.anchorNode.parentElement;
        const lastSelectedBlock = selectedText.extentNode.parentElement;
        const indexOfFirstSelectedBlock = [...firstSelectedBlock.parentElement.children].indexOf(firstSelectedBlock);
        const indexOfLastSelectedBlock = [...lastSelectedBlock.parentElement.children].indexOf(lastSelectedBlock);
        const randomFontSize = Math.floor(Math.random() * (40 - 10) + 10) + 'px';
        const indexOfFirstSelectedLetter = selectedText.anchorOffset;
        const indexOfLastSelectedLetter = selectedText.extentOffset;
        if (indexOfFirstSelectedBlock === indexOfLastSelectedBlock) {
          if (indexOfFirstSelectedLetter === 0 && indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text[indexOfFirstSelectedBlock].fontSize = randomFontSize;
          } else if (indexOfFirstSelectedLetter === 0) {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              id: this.randomId() + '6',
              text: selectedText.toString(),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: randomFontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          } else if (indexOfLastSelectedLetter === this.text[indexOfFirstSelectedBlock].text.length) {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              id: this.randomId() + '5',
              text: selectedText.toString(),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: randomFontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock].text = this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter);
          } else {
            this.text.splice(indexOfFirstSelectedBlock + 1, 0, {
              id: this.randomId() + '4',
              text: selectedText.toString(),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: randomFontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            }, {
              id: this.randomId() + '3',
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
            this.text[i].fontSize = randomFontSize;
          }
          if (indexOfLastSelectedLetter === this.text[indexOfLastSelectedBlock].text.length) {
            this.text[indexOfLastSelectedBlock].fontSize = randomFontSize;
          } else {
            this.text.splice(indexOfLastSelectedBlock , 0, {
              id: this.randomId() + '2',
              text: this.text[indexOfLastSelectedBlock].text.slice(0, indexOfLastSelectedLetter),
              color: this.text[indexOfLastSelectedBlock].color,
              fontSize: randomFontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfLastSelectedBlock + 1].text = this.text[indexOfLastSelectedBlock + 1].text.slice(indexOfLastSelectedLetter);
          }
          if (indexOfFirstSelectedLetter === 0) {
            this.text[indexOfFirstSelectedBlock].fontSize = randomFontSize;
          } else {
            this.text.splice(indexOfFirstSelectedBlock, 0, {
              id: this.randomId() + '1',
              text: this.text[indexOfFirstSelectedBlock].text.slice(0, indexOfFirstSelectedLetter),
              color: this.text[indexOfFirstSelectedBlock].color,
              fontSize: this.text[indexOfFirstSelectedBlock].fontSize,
              backgroundColor: this.text[indexOfFirstSelectedBlock].backgroundColor,
            });
            this.text[indexOfFirstSelectedBlock + 1].text = this.text[indexOfFirstSelectedBlock + 1].text.slice(indexOfFirstSelectedLetter);
            this.text[indexOfFirstSelectedBlock + 1].fontSize = randomFontSize;
          }
        }
        selectedText.removeAllRanges();
        this.mergeTextBlocks();
      }
    },
    mergeTextBlocks() {
      const newText = [];
      this.text.forEach(el => {
        if (newText[newText.length - 1] && newText[newText.length - 1].color === el.color && newText[newText.length - 1].fontSize === el.fontSize && newText[newText.length - 1].backgroundColor === el.backgroundColor) {
          newText[newText.length - 1].text += el.text 
        } else {
          newText.push(el);
        }
      });
      this.text = newText;
    },
    selectedText() {
      let text = '';
      if (window.getSelection) {
        text = window.getSelection();
      } else if (document.selection && document.selection.type != 'Control') {
        text = document.selection.createRange();
      }
      return text;
    },
    convertTextToJson() {
      this.jsonText = JSON.stringify(this.text).split(',').join(', ');
    },
  }
}
</script>

<style scoped>
  #text-editor-field {
    color: rgb(0, 0, 0);
    opacity: 1;
    border-radius: 6px; transform: rotate(0deg);
    border: 1px solid rgb(56, 142, 60);
    padding: 10px;
    text-align: center;
    letter-spacing: 0px;
    font-size: 20px;
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
  .btn-text-converter {
    background: #000000;
    border: none;
    border-radius: 5px;
    color: #ffffff;
    padding: 10px 25px;
    margin: 5px;
  }
  .btn-text-converter:hover {
    cursor: pointer;
    background: #510b0b;
  } 
  .btn-text-converter:focus {
    outline: none;
  }
</style>
