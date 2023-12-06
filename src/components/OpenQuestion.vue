<template>
  <div id="open-question">
      <div class="content-container">
          <span class="question"> {{ ques.question }} </span>
          <textarea class="input" rows="4" cols="50" v-model="inputValue"></textarea>
          <span v-if="showEmpty && !checked" class="error-message">אופס, נראה שלא ענית על השאלה</span>
          <span v-if="checked" class="answer">
            <span>בדקו את עצמכם!</span>
            <br><span>תשובה: {{ ques.answer }} </span>
          </span>
          <button v-else class="general-btn" @click="checkAnswer">אני רוצה לבדוק!</button>
          <button class="general-btn" v-show="checked" @click="proceed">הבנתי</button>
      </div>
  </div>
</template>

<script>
  
  export default {
    name: "open-question",
    props: ["ques"],
    data() {
      return {
          checked: false,
          showEmpty: false,
          inputValue: ''
      }
    },
    methods: {
      proceed() {
        this.inputValue = '';
        this.checked = false;
        this.$emit('finished');
      },
      checkAnswer() {
        if (!this.inputValue) {
          this.showEmpty = true;
        } else {
          this.showEmpty = false;
          this.checked = true;
        }
      }
    }
  }
</script>

<style scoped>
  .content-container {
    width: 150%;
    height: 100%;
    /* background-image: url("@/assets/background-text.svg");
    background-repeat: no-repeat;
    background-size: cover; */
    padding: 10%;
    display: flex;
    flex-direction: column;
    width: 20rem;
  }

  .question, 
  .input,
  .answer,
  .check,
  /* .error-message, */
  .continue {
    margin: 1rem;
    font-family: assistant;
  }

  .input {
    width: 90%;
    height: fit-content;
    resize: none;
    font-size: 0.8rem;
  }

  .error-message {
    font-size: 0.6rem;
    color: red;
    margin-right: 1rem;
  }

  .question  {
    font-size: 1.5rem;
    text-align: center;
  }
  .answer {
    font-size: 0.8rem;
  }

</style>