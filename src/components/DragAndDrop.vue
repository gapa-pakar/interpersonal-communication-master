<template>
  <div id="drag-and-drop">
    <span class="question">גררו את ההגדרה למושג המתאים לה</span>
    <div class="content-container">
      <div class="draggable-container">
        <p v-for="(definition, keyNameD) in definitions" :key="keyNameD" :class="['draggable', failedAnimation && chosenDefinitionKey === keyNameD ? 'failed' : '', dragged ? 'dragged' : '']" 
        draggable="true" @drag="dragging" @dragend="dragEnd" @dragstart="drag($event)" :id="`drag${keyNameD}`">{{ definition }}</p>
      </div>
      <div class="droppable-container">
          <p v-for="(term, keNameT) in terms" :key="keNameT" :id="`box-droppable${keNameT}`" class="box-droppable" @drop.prevent="drop($event)" @dragover="allowDrop($event)">{{ term }}</p>
      </div>
      <p class="txtFinish" v-show="allAnswered">מעולה! כל הכבוד</p>
    </div>
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
        chosenTermKey: -1,
        chosenDefinitionKey: -1,
        termsNum: this.ques.term.length,
        answered: [],
        failedAnimation: false,
        allAnswered: false,
        connectionNum: 0,
        dragged: false,
        root: document.querySelector(':root')
      }
    },
    mounted() {
      //reorder definitions and terms randomly
      for (let i = 0; i < this.termsNum; i++) {
        let random = Math.round(Math.random() * this.termsNum);
        while(`index${random}` in this.terms || random >= this.termsNum) {
          random = Math.round(Math.random() * this.termsNum);
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
        //prevent dragganle from going back to its original place
        event.preventDefault();
      },
      dragging() {
        this.root.style.setProperty('--cursor', 'grab');
      },
      dragEnd() {
        this.root.style.setProperty('--cursor', 'auto');
      },
      drag(event) {
        //set the chosen definition key name as the currently dragged item
        this.dragged = true;
        setTimeout(() => {
          event.target.classList.add("dragged");
        }, 50);
        this.root.style.setProperty('--cursor', 'grab');
        let defKey = event.currentTarget.id;
        defKey = defKey.replace('drag', '');
        this.chosenDefinitionKey = defKey;
        event.dataTransfer.setData("text", event.target.id);
      },
      drop(event) {
        //set the chosen term key name as the currently dropped-on item
        this.dragged = false;
        this.root.style.setProperty('--cursor', 'auto');
        let termKey = event.currentTarget.id;
        termKey = termKey.replace('box-droppable', '');
        this.chosenTermKey = termKey;
        //check if the definition and term match
        if (this.chosenTermKey === this.chosenDefinitionKey) {
          //prevent dragganle from going back to its original place, redefine variables to empty, update paired items number
          event.preventDefault();
          let data = event.dataTransfer.getData("text");
          event.target.appendChild(document.getElementById(data));
          this.answered[event.currentTarget.id] = data;
          this.chosenTermKey = -1;
          this.chosenDefinitionKey = -1;
          this.connectionNum++;
        } else {
          //activate fail animation, redefine variables to empty
          this.failedAnimation = true;
          setTimeout(() => {
            this.failedAnimation = false;
            this.chosenTermKey = -1;
            this.chosenDefinitionKey = -1;
          }, 1500);
        }
        //check if all items are paired, show finish text, redefine all variables to empty, move to next page
        if (this.connectionNum === this.termsNum) {
          this.allAnswered = true;
          setTimeout(() => {
            this.terms = {};
            this.definitions = {};
            this.allAnswered = false;
            this.$emit('finished');
          }, 2000);
        }
      },
    },
  }
</script>

<style scoped>

  :root {
    --cursor: auto;
  }

  #drag-and-drop {
    cursor: var(--cursor) !important;
    width: fit-content;
    height: fit-content;
    direction: rtl;
  }

  .question {
    top: 3rem;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    right: 1rem;
  }

  .content-container {
    padding: 10%;
    width: 30rem;
    height: 17rem;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    direction: ltr;
  }

  .draggable, .box-droppable {
    width: 4rem;
    border: 0.05rem solid #aaaaaa;
    border-radius: 1rem;
    display: block;
    text-align: center;
    font-size: 0.7rem;
    margin: 2rem;
    padding: 1rem;
  }

  .draggable{
    color: black;
    background-color: white;
    margin: 0 0.3rem;
    height: 4rem;
  }

  .draggable:hover {
    cursor: pointer;
  }

  .draggable:active {
    cursor: grab !important; 
  }

  .box-droppable {
    background-color: darkgrey;
    padding: 1rem 0;
    margin: 0 1rem;
    height: 3rem;
  }

  .droppable-container, 
  .draggable-container {
    display: flex;
    position: absolute;
    flex-direction: row;
    justify-content: space-between;
    width: 80%;
    height: 15%;
  }

  .droppable-container{
    top: -6rem;
  }
  
  .draggable-container {
    bottom: 22rem;
  }

  .dragged * {
    cursor: move; /* fallback if grab cursor is unsupported */
    cursor: grab !important;
    cursor: -moz-grab;
    cursor: -webkit-grab !important;
  }

  .dragged:active {
    cursor: grabbing !important;
    cursor: -moz-grabbing;
    cursor: -webkit-grabbing !important; 
}



  @keyframes failedConnection {
  0% {
    background-color: rgb(255, 255, 255);
  }

  50% {
    background-color: rgb(250, 195, 195);
  }

  100% {
    background-color: rgba(255, 255, 255);
  }
}

@-webkit-keyframes failedConnection {
  0% {
    background-color: rgba(255, 255, 255);
  }

  50% {
    background-color: rgb(250, 195, 195);
  }

  100% {
    background-color: rgba(255, 255, 255);
  }
}

.failed {
  animation: failedConnection 0.5s ease 3;
  -webkit-animation: failedConnection 0.5s ease 3;
}

.txtFinish {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  bottom: 20rem;
}

@media screen and (max-width: 1500px) {
  .draggable-container {
    bottom: 19rem;
  }

  .box-droppable {
    margin: 0px 0rem;
}
}

</style>