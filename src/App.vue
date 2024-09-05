<template>

  <main class="container py-4 my-5">

    <div class="row align-content-start justify-content-around">

      <div class="col-12 text-center text-lg-start">
        <h1 class="mb-4 mb-lg-5">🎡 วงแชร์นุ่น</h1>
      </div>

      <div class="col-12 col-lg-5 order-1 order-lg-0 mt-5">

        <fieldset class="mb-4">

          <legend>รายชื่อ</legend>

          <div class="mb-3" v-for="(slice, index) in slices">
            <div class="input-group">
              <div class="input-group-text p-0">
                <input v-model="slice.color" class="form-control form-control-color border-0" type="color">
              </div>
              <input v-model="slice.text" class="form-control" type="text">
              <button class="btn btn-danger" @click="slices.splice(index, 1)">ลบ</button>
            </div>
          </div>

          <div class="text-center">
            <button class="btn btn-primary" @click="slices.push({ color: getRandomColor(), text: 'รายชื่อใหม่' })">เพิ่มช่อง
            </button>
          </div>

        </fieldset>

      </div>

      <div class="col-12 col-lg-7 px-lg-5 order-0 order-lg-1">

        <div class="sticky-top">

          <div class="fs-2 text-center">
            <div v-if="winnerResult">
              ผู้ชนะคือ <span>{{ winnerResult.text }}</span> 🎉
            </div>
            <div v-else-if="isSpinning">
              กำลังสุ่ม...
            </div>
            <div v-else>
              เริ่มสุ่มเลยมั้ย?
            </div>
          </div>

          <ShiningDots
              :color="shiningDotsColor"
              :border-color="shiningDotsBorderColor"
              :shine-color="shiningDotsShineColor"
              :border-width="shiningDotsBorderWidth"
              :size="shiningDotsSize"
              :count="shiningDotsCount">

            <VueWheelSpinner
                ref="spinner"
                :slices="slices"
                :winner-index="defaultWinner"
                :sounds="sounds"
                :cursor-angle="cursorAngle"
                :cursor-position="cursorPosition"
                :cursor-distance="cursorDistance"
                @spin-start="onSpinStart"
                @spin-end="onSpinEnd">

              <template #cursor>
                <img class="cursor-img" :src="cursorImage" alt="Cursor">
              </template>

              <template #default>
                <button
                    class="spin-button"
                    :disabled="isSpinning"
                    @click="spinRandom()"
                    @mouseover="handleSpinButtonHover"
                    @mouseleave="handleSpinButtonLeave">
                  สุ่ม
                </button>
              </template>

            </VueWheelSpinner>

          </ShiningDots>

          <div>
            <button
                class="btn btn-success btn-lg rounded-pill w-100 py-4"
                :disabled="isSpinning"
                @click="spinRandom()">
                <span class="spinner-border spinner-border-sm me-2" v-if="isSpinning" role="status">
                  <span class="visually-hidden">กำลังสุ่ม...</span>
                </span>
              เริ่มสุ่ม
            </button>
          </div>

        </div>

      </div>

    </div>
  </main>


</template>

<script>
import VueWheelSpinner from 'vue-wheel-spinner';
import 'bootstrap/js/src/dropdown.js'

import cursorImage from './assets/cursor.svg';
import wonSound from './sounds/won.mp3';
import clickSound from './sounds/click.mp3';
import hoverSound from './sounds/hover.mp3';
import leaveSound from './sounds/leave.mp3';
import spinningSound from './sounds/spinning.mp3';
import ShiningDots from "@/components/ShiningDots.vue";

export default {
  components: {
    ShiningDots,
    VueWheelSpinner
  },
  data() {
    return {
      winnerResult: null,
      slices: this.createColorTextArray(6),
      isSpinning: false,
      defaultWinner: 0,
      sounds: {
        won: wonSound,
        spinButtonClick: clickSound,
        spinButtonHover: hoverSound,
        spinButtonLeave: leaveSound,
        spinning: spinningSound
      },
      cursorImage,
      cursorAngle: 270,
      cursorPosition: 'edge',
      cursorDistance: -30,
      shiningDotsColor: '#ffffff',
      shiningDotsShineColor: '#ffd800',
      shiningDotsBorderColor: '#1e254c',
      shiningDotsBorderWidth: 30,
      shiningDotsSize: 8,
      shiningDotsCount: 70
    };
  },
  watch: {
    slices: {
      handler() {
        this.$refs.spinner.drawWheel();
      },
      deep: true
    },
    shiningDotsBorderWidth() {
      this.$refs.spinner.drawWheel();
    },
  },
  methods: {
    getRandomColor() {
      return '#' + Math.floor(Math.random() * 16777215).toString(16);
    },
    createColorTextArray(count) {
      const result = [];
      const colors = [
        "#eb4d4b",
  "#e78e8a",
  "#e3b3b1",
  "#e8d6d4",
  "#f4eae9",
      ];  // Predefined colors
      for (let i = 1; i <= count; i++) {
        result.push({
          color: colors[i % colors.length],  // Alternate colors
          text: 'ชื่อคนที่ ' + i,        // Convert number to string
        });
      }
      return result;
    },
    playAudio(audio) {
      if (audio) {
        audio.volume = 0.5
        audio.play();
      }
    },
    handleSpinButtonClick() {
      if (this.buttonClickAudio) {
        this.playAudio(this.buttonClickAudio)
      }
      this.$refs.spinner.spinWheel(this.defaultWinner);
    },
    handleSpinButtonHover() {
      if (this.buttonHoverAudio) {
        this.playAudio(this.buttonHoverAudio)
      }
    },
    handleSpinButtonLeave() {
      if (this.buttonLeaveAudio) {
        this.playAudio(this.buttonLeaveAudio)
      }
    },
    handleCursorPositionChange() {
      if (this.cursorPosition === 'center') {
        if (!this.cursorDistance) {
          this.cursorDistance = 50;
        }
      } else {
        this.cursorDistance = 0;
      }
    },
    spinRandom() {
      const index = 3; // ดึงค่าจากapi
      this.defaultWinner = index;
      this.$refs.spinner.spinWheel(index);
    },
    onSpinStart() {
      this.winnerResult = null;
      this.isSpinning = true;
    },
    onSpinEnd(winnerIndex) {
      this.isSpinning = false;
      this.winnerResult = this.slices[winnerIndex];
    }
  },
  mounted() {
    this.buttonHoverAudio = new Audio(hoverSound);
    this.buttonLeaveAudio = new Audio(leaveSound);
    this.buttonClickAudio = new Audio(clickSound);
  }
};
</script>

<style>

.cursor-img {
  width: 50px;
  aspect-ratio: 1 / 1;
  filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, 0.19));
}

.spin-button {
  width: 100px;
  height: 100px;
  margin: 0 auto;
  aspect-ratio: 1 / 1;
  font-size: 20px;
  cursor: pointer;
  background: #eb4d4b;
  border-radius: 50%;
  transition: all 150ms;
  border: 10px solid white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  color: white !important;
  box-shadow: inset -3px -3px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  z-index: 11;
  position: relative;
  user-select: none;

  &:hover {
    box-shadow: inset -5px -5px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  }

  &:active {
    box-shadow: inset 3px 3px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  }

  &:disabled {
    background: #ccc;
    cursor: not-allowed;
    pointer-events: none;
  }

}

</style>
