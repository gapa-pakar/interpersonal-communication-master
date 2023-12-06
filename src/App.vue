<template>
  <div id="app">
    <open-end-screen v-if="currScreen <= 2 || currScreen === 21" @next-page="currScreen++"
      :currPage="currScreen">
    </open-end-screen>

    <main-content
      v-if="!(this.currScreen <= 2 || this.currScreen === 10 || this.currScreen === 15 || this.currScreen === 18 || this.currScreen >= 20)"
      :currPage="currScreen" class="main-content">
    </main-content>

    <exercise-manager class="exercise-manager"
      v-if="(this.currScreen === 10 || this.currScreen === 15 || this.currScreen === 18 || this.currScreen === 20)"
      :currPage="currScreen" @next-page="currScreen++">
    </exercise-manager>
  </div>
  
  <background :currPage="currScreen" id="background"></background>
  <!-- <img v-show="currScreen >= 2 && currScreen <= 21" src="@/assets/background-text.svg" alt="background-text"
    class="bg-text"> -->
  <!-- <img src="@/assets/communication-towers.svg" alt="communication-towers"
    style="position: absolute; bottom: -6rem; width: 70rem; height: 48rem;"> -->
  <div class="communication-towers">
    <img src="@/assets/communication-towers-hadar.svg" class="tower" />
    <img src="@/assets/communication-towers-hadar.svg" style="transform: scaleX(-1)" class="tower" />
  </div>
  <div class="nav-container">
    <img @click="currScreen--" src="@/assets/arrow-white.svg" alt="->"
      :class="['arrow-back', !(this.currScreen <= 2 || this.currScreen === 10 || this.currScreen === 15 || this.currScreen === 18 || this.currScreen >= 20) ? '' : 'hidden']">
    <nav-bar v-show="!(this.currScreen <= 2 || this.currScreen > 20)" :currPage="currScreen" @change-page="changePage"
      class="nav-bar"></nav-bar>
    <img @click="currScreen++" src="@/assets/arrow-white.svg" alt="<-"
    :class="['arrow-forward', !(this.currScreen <= 2 || this.currScreen === 10 || this.currScreen === 15 || this.currScreen === 18 || this.currScreen >= 20) ? '' : 'hidden']">
  </div>
</template>

<script>
import Background from './components/Background.vue';
import MainContent from './components/MainContent.vue';
import NavBar from './components/NavBar.vue';
import ExerciseManager from './components/ExerciseManager.vue';
import OpenEndScreen from './components/OpenEndScreen.vue';

export default {
  name: "app",
  components: {
    Background,
    OpenEndScreen,
    MainContent,
    NavBar,
    ExerciseManager
  },
  data() {
    return {
      currSubj: 0,
      currScreen: 10,
    }
  },
  methods: {
    changePage(page) {
      this.currScreen = page;
    }
  }

}
</script>

<style>
@font-face {
  font-family: assistant;
  src: url("../src/assets/fonts/Assistant-VariableFont_wght.ttf");
}

html,
body {
  margin: 0;
  overflow: hidden;
  direction: rtl;
  font-family: assistant;
}

html {
  font-size: calc(16px + 0.7vw);
  height: 100%;
}

#app {
  display: flex;
  flex-wrap: wrap;
  align-content: center;
  justify-content: center;
  align-items: center;
  height: 100vh;
  width: 100vw;
}

.hidden {
  visibility: hidden;
}

.communication-towers {
  width: 100%;
  height: fit-content;
  position: absolute;
  bottom: -10%;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  pointer-events: none;
}

.tower {
  width: 30%;
}

.general-btn {
  font-size: 1rem;
  background-color: rgba(255, 255, 255, 0.715);
  border: none;
  font-family: assistant;
  border-radius: 20px;
  padding: 0.2rem 0.5rem;
  cursor: pointer;
  color: rgb(28 32 45);
  align-self: center;
  margin-top: 1rem;
}

.general-btn:hover {
  background-color: rgba(255, 255, 255, 0.855);
}

.main-content {
  width: 28rem;
  height: 17rem;
  position: relative;
}

.bg-text {
  width: 33rem;
  height: 23rem;
  position: absolute;
  top: 3.5rem;
  z-index: -1;
}

.arrow-forward,
.arrow-back {
  width: 4rem;
  height: 4rem;
}

.arrow-forward:hover,
.arrow-back:hover {
  cursor: pointer;
}

.arrow-forward {
  transform: rotateY(180deg);
}

.nav-container {
  width: 100%;
  display: flex;
  align-content: flex-start;
  justify-content: space-between;
  align-items: flex-start;
  flex-wrap: wrap;
  flex-direction: row;
  margin: 0 0.5rem;
  margin-bottom: 5rem;
  position: relative;
  z-index: 15;
}

#background {
  position: absolute;
  z-index: -1;
}

.exercise-manager {
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
}

button:hover {
  cursor: pointer;
}

/* .main-content, 
  .exercise-manager {
    background-image: url("@/assets/background-text.svg");
    background-repeat: no-repeat;
    background-size: cover;
  } */
</style>
