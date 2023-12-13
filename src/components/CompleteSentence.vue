<template>
  <div id="complete-sentence" class="content-container">
    <span class="question"> השלימו את המשפט: </span>
    <div style="margin: 0.2rem 0;" v-for="(string, index) in ques.texts" :key="string">
      <span class="text"> {{ string }} </span>
      <input class="input" v-if="index < ques.answers.length" :id="`input${index}`" type="text" v-model="userInput[index]">
      <img class="indication" v-if="showAnswers && answerStatus[index] !== 'null'" :src="indicationScr(index)">
    </div>
    <div class="input-container" v-if="showContainer">
      <span>היעזרו במאגר המילים:</span>
      <div v-for="input in ques.answers" :key="input" class="item">
        <br><span> {{ input }} </span>
      </div>
    </div>
    <button v-if="!allCorrect" class="general-btn" @click="checkAnswers">בדוק אותי!</button>
    <button v-if="allCorrect" class="general-btn" style="width: 5rem;" @click="proceed">בואו נמשיך!</button>
  </div>
</template>

<script>

  export default {
    name: "complete-sentence",
    props: ["ques"],
    data() {
      return {
        userInput: [],
        showAnswers: false,
        answerStatus: [],
        showContainer: false,
        timesAnswered: 0,
        allCorrect: false
      }
    },
    methods: {
      checkAnswers() {
        //checks if the strings match
        for (let index = 0; index < this.ques.texts.length; index++) {
          let currInput = userInput[index].replace(/[^a-zA-Z0-9 ]/g, '');
          let currAnswer = this.ques.answers[index].replace(/[^a-zA-Z0-9 ]/g, '');
          if (this.userInput[index] === undefined) {
            this.answerStatus[index] = "null";
          } else if (currInput === currAnswer) {
            this.answerStatus[index] = "correct";
          } else {
            this.answerStatus[index] = "wrong";
          }
        }
        //checks if all answers are correct
        this.allCorrect = true;
        for (let i = 0; i < this.answerStatus.length; i++) {
          if (this.answerStatus[i] === "wrong") {
            this.allCorrect = false;
          }
        }
        //after 3 tries of not all correct, shows word bank
        this.timesAnswered++;
        this.timesAnswered >= 3 ? this.showContainer = true : this.showContainer = false;
        this.showAnswers = true;
      },
      //adds V or X to the answers accordingly
      indicationScr(param) {
        if (this.answerStatus[param] === "correct") {
          return new URL("@/assets/correct.svg", import.meta.url).href;
        } else {
          return new URL("@/assets/wrong.svg", import.meta.url).href;
        }
      },
      //set variables to empty, move to next page
      proceed() {
        this.userInput = [];
        this.showAnswers = false;
        this.answerStatus = [];
        this.showContainer = false;
        this.timesAnswered = 0;
        this.$emit('finished');
      },
    }
  }
</script>

<style scoped>
  .content-container {
    height: 12rem;
    padding: 10%;
    display: flex;
    flex-direction: column;
    width: 40rem;
  }

  .question {
    font-size: 2rem;
    font-weight: 600;
  }

  .input {
    width: 40%;
    height: auto;
    resize: none;
    font-size: 1rem;
    font-family: assistant;
    border: 5px solid white;
    border-radius: 20px;
    background-color: rgba(255, 255, 255, 0.301);
  }

  .text, .input-container {
    font-size: 1rem;
  }

  .input-container {
    border: 0.1rem solid rgba(255, 255, 255, 0.633);
    padding: 0.4rem;
    border-radius: 1rem;
    margin-top: 1rem;
    align-self: center;
    text-align: center;
    width: 10rem;
    position: relative;
    right: -2rem;
  }

  .indication {
    width: 1rem;
    margin: 0 0.2rem;
  }

  .item {
    line-height: 0.6rem;
  }

</style>