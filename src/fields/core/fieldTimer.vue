<template>
	<div class="timer">
		<div class="field is-grouped">

			<p class="control">{{ minutes }}:{{ seconds }}:{{ milliSeconds }}</p>
		</div>
    <div>
      <button class="button is-info is-outlined" @click="startTimer" :disabled="isRunning">START</button>
      <button class="button is-danger is-outlined" @click="stopTimer" :disabled="!isRunning">STOP</button>
    </div>
  </div>
</template>

<script>
import abstractField from "../abstractField";
export default({
	mixins: [ abstractField ],
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
});
</script>
<style>
p{
	font-size: 30px;
}
</style>
