<template>
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
  <p v-if="spinning">Spinning...</p>
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
    onSpinStart() {
      this.spinning = true;
    },
    onSpinEnd(winnerIndex) {
      this.spinning = false;
      console.log(winnerIndex);
      console.log(this.slices[winnerIndex]);
      alert(`Winner: ${this.slices[winnerIndex].text}`);
    }
  }
};
</script>

<style>
#app {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
