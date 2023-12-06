<template>
  <div id="multiple-choice">
    <div class="content-container">
      <span class="question"> {{ ques.quest }} </span>
      <br> <span class="question"> בחרו את התשובה הנכונה! </span>
      <div class="answers">
        <ul style="padding-right: 0; margin: 0; border-collapse: collapse;">
          <li v-for="(answer, index) in ques.ans" :key="index" class="ans" @click="selectAnswer(index)">
            <img class="indication" :src="indicationScr(index)">
            <span>{{ answer }}</span>
          </li>
        </ul>
      </div>
      <button v-if="!showAnswers" class="general-btn" @click="chosenAnswer !== -1 ? showAnswers = true : showEmpty = true">בדוק אותי!</button>
      <span v-if="showEmpty" class="error-message">אופס, נראה שלא ענית על השאלה</span>
      <button class="general-btn" v-show="showAnswers" @click="proceed">בואו נמשיך</button>
    </div>
  </div>
</template>

<script>

  export default {
    name: "multiple-choice",
    props: ["ques"],
    data() {
      return {
        chosenAnswer: -1,
        showAnswers: false,
        showEmpty: false
      }
    },
    methods: {
      proceed() {
        this.showAnswers = false;
        this.showEmpty = false;
        this.chosenAnswer =  -1;
        this.$emit('finished');
      },
      indicationScr(param) {
        let currSrc;
        if (this.showAnswers === false) {
          if (this.chosenAnswer === param) {
            currSrc = new URL("@/assets/multiple-btn-blue.svg", import.meta.url).href;
          } else {
            currSrc = new URL("@/assets/multiple-btn.svg", import.meta.url).href;
          }
        } else {
          if (param === this.ques.correct) {
            currSrc = new URL("@/assets/multiple-btn-green.svg", import.meta.url).href;
          } else if (this.chosenAnswer === param) {
            currSrc = new URL("@/assets/multiple-btn-red.svg", import.meta.url).href;
          } else {
            currSrc = new URL("@/assets/multiple-btn.svg", import.meta.url).href;
          }
        }
        return currSrc;
        // if (param === this.ques.correct) {
        //   return new URL("@/assets/correct.svg", import.meta.url).href;
        // } else {
        //   return new URL("@/assets/wrong.svg", import.meta.url).href;
        // }
      },
      selectAnswer(num) {
        if (!this.showAnswers) {
          if (this.chosenAnswer !== num) {
            this.chosenAnswer = num;
            this.showEmpty = false;
          } else {
            this.chosenAnswer = -1;
          }
        }
      },
    }
  }
</script>

<style scoped>
  .content-container {
    width: 25rem; 
    height: 100%;
    padding: 10%;
    display: flex;
    flex-direction: column;
    position: relative;
    right: -2rem;
  }

  .indication {
    width: 1rem;
    margin: 0 1rem;
  }

  .ans {
    list-style: none;
    margin: 0.7rem;
  }

  .question {
    font-size: 1rem;
  }
  .ans {
    font-size: 0.8rem;
  }

  .ans:hover {
    cursor: pointer;
     
  }

  .answers {
    display: flex;
  }

  .imgs {
    display: flex;
    list-style: none;
    margin-bottom: 1rem;
  }
  .error-message {
    font-size: 0.6rem;
    color: red;
    margin-right: 1rem;
  }
  
</style>