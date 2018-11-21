<template>
	<div id="Timer" class="ui text container">
    <h6>
      {{ minutes | zeroPad }} :
      {{ seconds | zeroPad }} :
      {{ milliSeconds | zeroPad(3) }}
		</h6>
      <div>
        <button class="button is-info is-outlined" @click="startTimer" :disabled="isRunning">START</button>
        <button class="button is-danger is-outlined" @click="stopTimer" :disabled="!isRunning">STOP</button>
      </div>
  </div>
</template>

<script>
export default({
	name: "Timer",
	data(){
		return {
			times: [],
			animateFrame: 0,
			nowTime: 0,
			diffTime: 0,
			startTime: 0,
			isRunning: false
		};
  },
  methods: {
    setSubtractStartTime: function () {
      let time = typeof time !== 'undefined' ? time : 0;
      this.startTime = Math.floor(performance.now() - time);
    },
    startTimer: function () {
      let vm = this;
      vm.setSubtractStartTime(vm.diffTime);
      (function loop(){
        vm.nowTime = Math.floor(performance.now());
        vm.diffTime = vm.nowTime - vm.startTime;
        vm.animateFrame = requestAnimationFrame(loop);
      }());
      vm.isRunning = true;
    },
    stopTimer: function () {
      this.isRunning = false;
      cancelAnimationFrame(this.animateFrame);
    },
    pushTime: function () {
      this.times.push({
        hours: this.hours,
        minutes: this.minutes,
        seconds: this.seconds,
        milliSeconds: this.milliSeconds
				});
    },
  },
  computed: {
    hours: function () {
      return Math.floor(this.diffTime / 1000 / 60 / 60);
    },
    minutes: function () {
      return Math.floor(this.diffTime / 1000 / 60) % 60;
    },
    seconds: function () {
      return Math.floor(this.diffTime / 1000) % 60;
    },
    milliSeconds: function () {
      return Math.floor(this.diffTime % 1000);
    }
  },
  filters: {
    zeroPad: function(value){
      let num = typeof num !== 'undefined' ? num : 2;
      return value.toString().padStart(num,"0");
    }
  }
});
</script>
