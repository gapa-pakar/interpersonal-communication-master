<template>
  <div id="background">
    <div class="sky background-element"></div>
    <div :class="['buildings', 'background-element', currPage >= 2 ? 'building-animation' : '']"></div>
  </div>
</template>

<script>

export default {
  name: "background",
  props: ["currPage"],
  components: {

  },
  data() {
    return {

    }
  },
  methods: {

  }
}
</script>

<style scoped>
.background-element {
  height: 100vh;
  width: 100vw;
  background-size: cover;
  background-repeat: no-repeat;
}

.sky {
  background-image: url("@/assets/sky.svg");
  position: relative;
}

.buildings {
  background-image: url("@/assets/buildings.svg");
  position: absolute;
  bottom: -10%;
}

.building-animation {
  animation: buildingAni 3s ease-in-out forwards;
}

@keyframes buildingAni {
  0% {
    bottom: -10%;
  }
  15% {
    bottom: -8%;
  }

  100% {
    bottom: -40%;
  }
}
</style>
  