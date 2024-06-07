<template>

  <div class="wheel">
    <VueWheelSpinner
        ref="spinner"
        :slices="slices"
        :winnerIndex="winnerIndex"
        :spinDuration="spinDuration"
        :spinButtonLabel="spinButtonLabel"
        :spinButtonBackgroundColor="spinButtonBackgroundColor"
        :spinButtonLabelColor="spinButtonLabelColor"
        @spin-start="onSpinStart"
        @spin-end="onSpinEnd"
    />
  </div>

  <div>
    <button v-for="(slice, index) in slices" :disabled="spinning" @click="spinFor(index)">
      Win for {{ slice.text }}
    </button>
  </div>

</template>

<script>
import VueWheelSpinner from 'vue-wheel-spinner';

export default {
  components: {
    VueWheelSpinner
  },
  data() {
    return {
      slices: [
        { color: '#FF0000', text: 'Prize 1' },
        { color: '#00FF00', text: 'Prize 2' },
        { color: '#0000FF', text: 'Prize 3' },
        { color: '#FFFF00', text: 'Prize 4' },
        { color: '#FFA500', text: 'Prize 5' },
        { color: '#800080', text: 'Prize 6' }
      ],
      winnerIndex: 1,
      spinDuration: 4000,
      spinning: false,
      spinButtonLabel: 'Spin',
      spinButtonBackgroundColor: '#eaeaea',
      spinButtonLabelColor: '#000'
    };
  },
  methods: {
    spinFor(index) {
      this.winnerIndex = index;
      this.$refs.spinner.spinWheel(index);
    },
    onSpinStart() {
      this.spinning = true;
    },
    onSpinEnd(winnerIndex) {
      this.spinning = false;
      alert(`Winner: ${this.slices[winnerIndex].text}`);
    }
  }
};
</script>

<style scoped>

.wheel {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 30px 0;
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
