<template>
	<div class="timerset">
    <span class="settimer" style="font-size:20px" >
			<input type="hidden" name="timerBOH" class="totaltime" v-model="diffTime" />
			{{ minutes }}:{{ seconds }}:{{ milliSeconds }}
		</span>

      <div class="tbutton">
        <a class="button is-info is-outlined" @click="startTimer" :disabled="isRunning">START</a>
        <a class="button is-danger is-outlined" @click="stopTimer" :disabled="!isRunning">STOP</a>
      </div>
  </div>
	<!-- <div class="timer">
		<div>

			<span>{{ minutes }}:{{ seconds }}:{{ milliSeconds }}</span>
		</div>
    <div>
      <button class="button is-info is-outlined" @click="startTimer" :disabled="isRunning">START</button>
      <button class="button is-danger is-outlined" @click="stopTimer" :disabled="!isRunning">STOP</button>
    </div>
  </div> -->
</template>

<script>
import abstractField from "../abstractField";
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
    setSubtractStartTime: function (time) {
      // let time = typeof time !== 'undefined' ? time : 0;
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
    // clearAll: function () {
    //   this.startTime = 0;
    //   this.nowTime = 0;
    //   this.diffTime = 0;
    //   this.times = [];
    //   this.stopTimer();
    //   this.animateFrame = 0;
    // }
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
    },
    finalValue: function(){
      return this.diffTime;
    }
  },

});
</script>
<style lang="css">

</style>
