<template>
  <div id="nav-bar">
    <ol class="progress">
      <li @click="changeScreen(subj1)" :class="[currPage >= 3 && currPage <= 10 ? 'is-active' : '', subj1Hover ? 'show-title' : '']"><span>מושגים בתקשורת</span></li>
      <li @click="changeScreen(subj2)" :class="[currPage >= 11 && currPage <= 15 ? 'is-active' : '', subj2Hover ? 'show-title' : '']"><span>מודלים תקשורתיים</span></li>
      <li @click="changeScreen(subj3)" :class="[currPage >= 16 && currPage <= 18 ? 'is-active' : '', subj3Hover ? 'show-title' : '']"><span>סיבות לרעשים תקשורתיים</span></li>
      <li @click="changeScreen(subj4)" :class="[currPage >= 19 && currPage <= 20 ? 'is-active' : '', subj4Hover ? 'show-title' : '']"><span>פתרונות לרעשים תקשורתיים</span></li>
    </ol>
    <svg id="Layer_1" data-name="Layer 1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1203 33.94">
      <line class="cls-1" y1="16.97" x2="1203" y2="16.97" />
      <rect @click="changeScreen(subj4)" class="cls-2" x="243.87" y="4.97" width="24" height="24" transform="translate(86.94 -175.96) rotate(45)" />
      <rect @click="changeScreen(subj3)" class="cls-2" x="478.25" y="4.97" width="24" height="24" transform="translate(155.59 -341.69) rotate(45)" />
      <rect @click="changeScreen(subj2)" class="cls-2" x="713.62" y="4.97" width="24" height="24" transform="translate(224.53 -508.12) rotate(45)" />
      <rect @click="changeScreen(subj1)" class="cls-2" x="933" y="4.97" width="24" height="24" transform="translate(288.78 -663.25) rotate(45)" />
    </svg>
  </div>
</template>

<script>

export default {
  name: "nav-bar",
  props: ["currPage"],
  data() {
    return {
      subj1: 3,
      subj2: 11,
      subj3: 16,
      subj4: 19,
    };
  },
  methods: {
    changeScreen(page) {
      if (this.currPage !== 10 && this.currPage !== 15 && this.currPage !== 18 && this.currPage !== 20) {
        this.$emit('change-page', page);
      }
    }
  },
}
</script>
  
<style scoped>

:root {
  --color-white: #fff;
  --color-black: #333;
  --color-gray: #75787b;
  --color-gray-light: #bbb;
  --color-gray-disabled: #e8e8e8;
}

* {
  box-sizing: border-box;
  font-family: assistant;
}

body {
  color: var(--color-black);
  direction: rtl;
}

#Layer_1 {
  width: 40rem;
  position: relative;
  top: -2rem;
}

.progress {
  display: flex;
  justify-content: space-between;
  list-style: none;
  margin: 0 5rem 1rem 0;
  width: 27rem;
  color: white;
  position: relative;
  z-index: 2;
}

.progress li {
  flex: 2;
  position: relative;
  padding: 0 0 14px 0;
  font-size: 0.6rem;
  line-height: 1.2;
  color: var(--color-black);
  font-weight: 600;
  white-space: nowrap;
  overflow: visible;
  width: 3rem;
  text-align: center;
  border-bottom: 2px solid var(--color-gray-disabled);
  padding-bottom: 2rem;
}

.progress li:first-child,
.progress li:last-child {
  flex: 1;
}

.progress li:last-child {
  text-align: center;
}

.progress li::before {
  content: "";
  display: block;
  width: 8px;
  height: 8px;
  background-color: var(--color-gray-disabled);
  border-radius: 50%;
  border: 2px solid var(--color-white);
  position: absolute;
  left: calc(50% - 6px);
  bottom: -7px;
  z-index: 3;
  transition: all .2s ease-in-out;
}

.progress li:first-child::before {
  left: 0;
}

.progress li:last-child::before {
  right: 0;
  left: auto;
}

.progress span {
  transition: opacity .3s ease-in-out;
}

.progress li:not(.is-active) span {
  opacity: 0;
}

.progress .is-complete:not(:first-child)::after,
.progress .is-active:not(:first-child)::after {
  content: "";
  display: block;
  width: 100%;
  position: absolute;
  bottom: -2px;
  left: -50%;
  z-index: 2;
  border-bottom: 2px solid var(--color-yellow);
}

.progress li:last-child span {
  width: 200%;
  display: inline-block;
  position: absolute;
  left: -100%;
}

.progress .is-complete:last-child::after,
.progress .is-active:last-child::after {
  width: 200%;
  left: -100%;
}

.progress .is-complete::before {
  background-color: var(--color-yellow);
}

.progress .is-active::before,
.progress li:hover::before,
.progress .is-hovered::before {
  background-color: var(--color-white);
  border-color: var(--color-yellow);
}

.progress li:hover::before,
.progress .is-hovered::before {
  transform: scale(1.33);
}

.progress li:hover span,
.progress li:hover,
.progress li.is-hovered span {
  opacity: 1;
  cursor: pointer;
}


.progress:hover li:not(:hover) span {
  opacity: 0;
}

.progress .has-changes {
  opacity: 1 !important;
}

.progress .has-changes::before {
  content: "";
  display: block;
  width: 8px;
  height: 8px;
  position: absolute;
  left: calc(50% - 4px);
  bottom: -20px;
}

.progress li .is-active span {
  opacity: 1 !important;
}

.cls-1 {
  fill:none;
  stroke:#fff;
  stroke-miterlimit:10;
  stroke-width:6px;
}

.cls-2 {
  fill:#fff;
}

.cls-2:hover {
  cursor: pointer;
}

.show-title {
  opacity: 1!important;
  cursor: pointer!important;
}

</style>
