<template id="drag-and-drop">
  <span class="question">גררו את ההגדרה למושג המתאים לה</span>
    <div v-for="(string, keyName, index) in terms" :key="keyName" class="ignore">
      <div
        :class="['term', chosenTermKey === keyName ? 'chosen' : '', failedAnimation && chosenTermKey === keyName ? 'failed' : '']"
        :style="`grid-column: ${index + 1} / ${index + 2}`" @click="chosenItem('term', keyName, index)"> {{ string }}
      </div>
      <div
        :class="['definition', chosenDefinitionKey === Object.keys(definitions)[index] ? 'chosen' : '', failedAnimation && chosenDefinitionKey === Object.keys(definitions)[index] ? 'failed' : '']"
        :id="`input${index}`" :style="`grid-column: ${index + 1} / ${index + 2}`"
        @click="chosenItem('definition', Object.keys(definitions)[index], index)"> {{ Object.values(definitions)[index]
        }} </div>
    </div>
  <div class="content-container">
    <div class="draggable-container">
      <p v-for="(definition, keyNameD, indexD) in definitions" :key="keyNameD" class="draggable" draggable="true" @dragstart="drag(indexD, keyNameD)" :id="`drag${indexD}`">{{ definition }}</p>
    </div>
    <div class="droppable-container">
      <div v-for="(term, keyNameT, indexT) in terms" :key="keyNameT" :id="`box-droppable${indexT}`" class="box-droppable" @drop="drop(indexT)" @dragover="allowDrop"><p>{{ term }}</p></div>
    </div>
    <p id="txtFinish" v-show="finished">מעולה! כל הכבוד!</p>
  </div>
</template>

<script>

  export default {
    name: "drag-and-drop",
    props: ["ques"],
    data() {
      return {
        terms: {},
        definitions: {},
        chosenTermIndex: -1,
        chosenTermKey: -1,
        chosenDefinitionIndex: -1,
        chosenDefinitionKey: -1,
        termsNum: this.ques.term.length,
        answered: [],
        failedAnimation: false,
        allAnswered: false,
        connectionNum: 0
      }
    },
    mounted() {
      for (let i = 0; i < this.ques.term.length; i++) {
        let random = Math.round(Math.random() * this.ques.term.length);
        while(`index${random}` in this.terms || random >= this.ques.term.length) {
          random = Math.round(Math.random() * this.ques.term.length);
        }
        this.terms[`index${random}`] = this.ques.term[random];
      }
      for (let j = 0; j < this.ques.definition.length; j++) {
        let random = Math.round(Math.random() * this.ques.definition.length);
        while(`index${random}` in this.definitions || random >= this.ques.definition.length) {
          random = Math.round(Math.random() * this.ques.definition.length);
        }
        this.definitions[`index${random}`] = this.ques.definition[random];
      }
    },
    methods: {
      allowDrop(event) {
        event.preventDefault();
      },
      drag(indexD, keyNameD) {
        // alert(indexD);
        this.currDrag = indexD;
        this.currKey = keyNameD;
        // event.dataTransfer.setData("text", event.target.id);
      },
      drop(indexT, event) {
        // event.preventDefault();
        alert(`${indexT}, ${this.currDrag}, ${this.currKey}`);
        let data = event.dataTransfer.getData("text");
        event.target.appendChild(document.getElementById(data));
        this.answered[event.currentTarget.id] = data;
        if (Object.keys(this.answered).length === 4) {
          if(this.checkAnswers()) {
            this.finished = true;
            this.$emit("finished");
          }
        }
      },
      chosenItem(currItem, keyNameIndex, currIndex) {
        if (currItem === "term") {
          if (this.chosenTermIndex === currIndex) {
            this.chosenTermIndex = -1;
            this.chosenTermKey = -1;
          } else {
            this.chosenTermIndex = currIndex;
            this.chosenTermKey = keyNameIndex;
          }
        } else {
          if (this.chosenDefinitionIndex === currIndex) {
            this.chosenDefinitionIndex = -1;
            this.chosenDefinitionKey = -1;
          } else {
            this.chosenDefinitionIndex = currIndex;
            this.chosenDefinitionKey = keyNameIndex;
          }
        }
      },
      checkConnection() {
      if (this.chosenTermIndex === -1 || this.chosenDefinitionIndex === -1) {
        this.showEmpty = true;
      } else {
        if (this.chosenTermKey === this.chosenDefinitionKey) {
          this.chosenTermIndex = -1;
          this.chosenTermKey = -1;
          this.chosenDefinitionIndex = -1;
          this.chosenDefinitionKey = -1;
          this.connectionNum++;
        } else {
          this.failedAnimation = true;
          setTimeout(() => {
            this.failedAnimation = false;
            this.chosenTermIndex = -1;
            this.chosenTermKey = -1;
            this.chosenDefinitionIndex = -1;
            this.chosenDefinitionKey = -1;
          }, 2100);
        }
        this.showEmpty = false;
      }
      if (this.connectionNum === this.ques.term.length) {
        this.allConnected = true;
      }
    },
      checkAnswers() {
        for (let i = 0 ; i < this.ques.term.length ; i++ ) {
          if (this.answered[`box-droppable${i}`] !== this.correct[`box-droppable${i}`]) {
            return false;
          }
        }
        return true;
      }
    },
  }
</script>

<style scoped>

  #drag-and-drop {
    width: 30rem;
    height: fit-content;
    position: relative;
    padding: 10%;
    display: flex;
    flex-direction: column;
    margin-top: 2rem;
  }

  .question {
    top: 5rem;
    position: relative;  
  }

  .content-container {
    margin-top: 4rem;
    padding: 10%;
    width: 30rem;
    height: 17rem;
    display: grid;
    grid-template: repeat(2, 1fr) / repeat(v-bind("termsNum"), 1fr);
    justify-items: center;
    direction: ltr;
  }

  .term,
  .definition {
    border-radius: 0.5rem ;
    padding: 5%;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    width: 5rem;
    border: 5px solid white;
    background-color: rgba(255, 255, 255, 0.301);
    margin: 0 5%;
    text-align: center;
    justify-content: center;
    font-size: 0.8rem;
  }

  .term {
    grid-row: 1 / 2;
    align-self: flex-end;
  }

  .definition {
    grid-row: 2/3;
    align-self: flex-start;
  }

  .draggable, .box-droppable {
    width: 5rem;
    border: 0.05rem solid #aaaaaa;
    border-radius: 1rem;
    display: block;
    text-align: center;
    font-size: 0.7rem;
  }

  .draggable{
    color: black;
    background-color: white;
    padding: 1rem;
    margin: 0 0.3rem;
    height: 4rem;
  }

  .draggable:hover {
    cursor: pointer;
  }

  .draggable:active {
    cursor: grab;
  }

  .box-droppable {
    background-color: darkgrey;
    padding: 0%;
    margin: 0 1rem;
    height: 3rem;
  }
  .droppable-container{
    grid-row: 1 / 2;

  }

  .droppable-container, 
  .draggable-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    width: 80%;
    height: 15%;
  }

  .draggable-container {
    grid-row: 2/3;
  }

</style>