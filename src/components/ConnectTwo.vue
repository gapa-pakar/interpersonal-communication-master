<template>
  <div class="container">
    <span class="question"> {{ ques.question }} </span>
    <div id="connect-two" class="content-container">
      <svg class="svg" viewBox="0 0 400 400" xmlns="http://www.w3.org/2000/svg" ref="svg">
        <path :id="`path${index - 1}`" v-for="index in termsNum" :key="colorsArr[index - 1]"
          :stroke="colorsArr[index - 1]" stroke-width="0.5%" fill="none" :d="paths[index - 1]"></path>
      </svg>
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
    </div>
    <button v-if="!allConnected" @click="checkConnection" class="general-btn">חבר</button>
    <span v-show="showEmpty" class="error-message">נראה שלא בחרת מושג והגדרה...</span>
    <button v-if="allConnected" class="general-btn" @click="proceed">בואו נמשיך!</button>
  </div>
</template>

<script>

export default {
  name: "connect-two",
  props: ["ques"],
  emits: ['finished'],
  data() {
    return {
      terms: {},
      definitions: {},
      chosenTermIndex: -1,
      chosenTermKey: -1,
      chosenDefinitionIndex: -1,
      chosenDefinitionKey: -1,
      termsNum: this.ques.term.length,
      colorsArr: ["#32a852", "#2284e6", "#ed130c", "#fafa05", "#fc9403", "#242323"],
      paths: [],
      showEmpty: false,
      failedAnimation: false,
      allConnected: false,
      connectionNum: 0
    }
  },
  mounted() {
    // fit SVG to screen proportions - take the CSS size of the svg element and set it to the viewBox values
    this.$refs.svg.setAttribute('viewBox', `0 0 ${this.$refs.svg.clientWidth} ${this.$refs.svg.clientHeight}`);
    //place terms and definitiond randomly while saving original index
    for (let i = 0; i < this.ques.term.length; i++) {
      let random = Math.round(Math.random() * this.ques.term.length);
      while (`index${random}` in this.terms || random >= this.ques.term.length) {
        random = Math.round(Math.random() * this.ques.term.length);
      }
      this.terms[`index${random}`] = this.ques.term[random];
    }
    for (let j = 0; j < this.ques.definition.length; j++) {
      let random = Math.round(Math.random() * this.ques.definition.length);
      while (`index${random}` in this.definitions || random >= this.ques.definition.length) {
        random = Math.round(Math.random() * this.ques.definition.length);
      }
      this.definitions[`index${random}`] = this.ques.definition[random];
    }
  },
  methods: {
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
          let x1 = Number(this.chosenTermIndex) * (this.$refs.svg.clientWidth / this.termsNum) + (this.$refs.svg.clientWidth / this.termsNum / 2);
          let x2 = Number(this.chosenDefinitionIndex) * (this.$refs.svg.clientWidth / this.termsNum) + (this.$refs.svg.clientWidth / this.termsNum / 2);
          let y1 = 0;
          let y2 = this.$refs.svg.clientHeight;
          this.paths[this.chosenTermIndex] = `M ${x1} ${y1} C ${x1} ${y2 * 1.2} ${x2} ${y2 * 0.01} ${x2} ${y2}`;
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
    proceed() {
      this.terms = {};
      this.definitions = {};
      this.paths = [];
      this.showEmpty = false;
      this.allConnected = false;
      this.$emit('finished');
    },
  },
}
</script>

<style scoped>
.container {
  width: 30rem;
  height: fit-content;
  position: relative;
  padding: 10%;
  display: flex;
  flex-direction: column;
  margin-top: 2rem;
}

.content-container {
  width: 30rem;
  height: 17rem;
  display: grid;
  grid-template: repeat(3, 1fr) / repeat(v-bind("termsNum"), 1fr);
  justify-items: center;
  direction: ltr;
}

.term,
.definition {
  border: 5px solid white;
  border-radius: 20px;
  background-color: rgba(255, 255, 255, 0.301);
  padding: 5%;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 5rem;
  margin: 0 5%;
  text-align: center;
  justify-content: center;
  font-size: 0.8rem;
}

.term:hover,
.definition:hover {
  cursor: pointer;
}


.term {
  grid-row: 1 / 2;
  align-self: flex-end;
}

.definition {
  grid-row: 3/4;
  align-self: flex-start;
}

.svg {
  grid-column: 1 / -1;
  grid-row: 2 / 3;
  width: 100%;
  height: 100%;
}

.ignore {
  display: contents;
}

.chosen {
  background-color: rgba(255, 255, 255, 0.791);
}

.error-message {
  font-size: 0.6rem;
  color: red;
  position: relative;
  margin: 1rem;
  top: -5.5rem;
  align-self: center;
}

@keyframes failedConnection {
  0% {
    background-color: rgba(255, 255, 255, 0.589);
  }

  50% {
    background-color: rgb(250, 195, 195);
  }

  100% {
    background-color: rgba(255, 255, 255, 0.589);
  }
}

@-webkit-keyframes failedConnection {
  0% {
    background-color: rgba(255, 255, 255, 0.589);
  }

  50% {
    background-color: rgb(250, 195, 195);
  }

  100% {
    background-color: rgba(255, 255, 255, 0.589);
  }
}

.failed {
  animation: failedConnection 0.7s ease 3;
  -webkit-animation: failedConnection 0.7s ease 3;
}

.question {
  font-size: 1.5rem;
}</style>