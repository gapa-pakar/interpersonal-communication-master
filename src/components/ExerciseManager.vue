<template>
  <div id="exercise-manager">
    <div>
      <open-question v-if="question['type'] === 'open-question'" :ques="question" @finished="next"></open-question>
      <multiple-choice v-if="question['type'] === 'multiple-choice'" :ques="question" @finished="next"></multiple-choice>
      <complete-sentence v-if="question['type'] === 'copmlete-sentance'" :ques="question" @finished="next"></complete-sentence>
      <connect-two v-if="question['type'] === 'connect-two'" :ques="question" @finished="next"></connect-two>
      <drag-and-drop v-if="question['type'] === 'drag-and-drop'" :ques="question" @finished="next"></drag-and-drop>
    </div>
  </div>
</template>

<script>
  import json from '@/data.json';
  import OpenQuestion from './OpenQuestion.vue';
  import MultipleChoice from './MultipleChoice.vue';
  import CompleteSentence from './CompleteSentence.vue';
  import ConnectTwo from './ConnectTwo.vue';
  import DragAndDrop from './DragAndDrop.vue';

  export default {
    name: "exercise-manager",
    components: {
      OpenQuestion,
      MultipleChoice,
      CompleteSentence,
      ConnectTwo,
      DragAndDrop
    },
    props: ["currPage"],
    data() {
      return {
        currQuestion: 0, 
      }
    },
    methods: {
      next() {
        this.currQuestion++;
        if (this.question === undefined) {
          this.currQuestion = 0;
          this.$emit('next-page');
        }
      }
    },
    computed: {
      question() {
        return (json["Questions"][0][`page${this.currPage}`][this.currQuestion]);
      }
    }
  }
</script>

<style scoped>


</style>
