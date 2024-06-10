<template>

  <div class="wheel">
    <div class="wheel-state">
      <div v-if="winnerResult">
        Winner: <span :style="{'color': winnerResult.color}">{{ winnerResult.text }}</span> 🎉
      </div>
      <div v-else-if="isSpinning">
        Spinning...
      </div>
      <div v-else>
        Ready to spin?
      </div>
    </div>
    <VueWheelSpinner
        ref="spinner"
        :slices="slices"
        :winner-index="winnerIndex"
        :spin-duration="spinDuration"
        :spin-button-label="spinButtonLabel"
        :spin-button-size="spinButtonSize"
        :spin-button-background-color="spinButtonBackgroundColor"
        :spin-button-label-color="spinButtonLabelColor"
        :needle-background-color="needleBackgroundColor"
        :needle-scale="needleScale"
        :is-spinning="isSpinning"
        :sounds="sounds"
        @spin-start="onSpinStart"
        @spin-end="onSpinEnd"
    />
  </div>

  <div>
    <button v-for="(slice, index) in slices" :disabled="isSpinning" @click="spinFor(index)">
      Win for {{ slice.text }}
    </button>
  </div>

</template>

<script>
import VueWheelSpinner from 'vue-wheel-spinner';

import wonSound from './sounds/won.mp3';
import clickSound from './sounds/click.mp3';
import hoverSound from './sounds/hover.mp3';
import leaveSound from './sounds/leave.mp3';
import spinningSound from './sounds/spinning.mp3';

export default {
  components: {
    VueWheelSpinner
  },
  data() {
    return {
      winnerResult: null,
      slices: [
        {color: '#FF0000', text: 'Prize 1'},
        {color: '#00FF00', text: 'Prize 2'},
        {color: '#0000FF', text: 'Prize 3'},
        {color: '#FFFF00', text: 'Prize 4'},
        {color: '#FFA500', text: 'Prize 5'},
        {color: '#800080', text: 'Prize 6'},
      ],
      winnerIndex: 1,
      spinDuration: 4000,
      isSpinning: false,
      spinButtonLabel: 'Spin',
      spinButtonSize: 20,
      spinButtonBackgroundColor: '#fff',
      spinButtonLabelColor: '#000',
      needleBackgroundColor: '#fff',
      needleScale: 1.5,
      sounds: {
        won: wonSound,
        spinButtonClick: clickSound,
        spinButtonHover: hoverSound,
        spinButtonLeave: leaveSound,
        spinning: spinningSound
      }
    };
  },
  methods: {
    spinFor(index) {
      this.winnerIndex = index;
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
  }
};
</script>

<style scoped>

.wheel {
  padding: 30px 0;
  width: 500px;
  max-width: 100vw;
  margin: 0 auto;
}

.wheel-state {
  font-size: 24px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 20px;
  color: white;
}

button {
  transition: all 0.3s;
  padding: 10px 20px;
  margin: 10px;
  border: none;
  border-radius: 5px;
  background-color: #eaeaea;
  color: #000;
  cursor: pointer;
}

button:active {
  transform: scale(0.95);
}

button:focus {
  outline: none;
}

button:hover {
  background-color: #f0f0f0;
}

button:disabled {
  background-color: #ccc;
  color: #666;
  cursor: not-allowed;
}

</style>
