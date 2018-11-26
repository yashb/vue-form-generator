<template lang="pug">
    .timerset
      h2 {{schema.title}}
          .tbutton
              span.settimer(style='font-size:20px;') {{ minutes }}:{{ seconds }}:{{ milliSeconds }}
              a.button.is-info.is-outlined(@click='startTimer', :disabled='isRunning') START
              a.button.is-danger.is-outlined(@click='stopTimer', :disabled='!isRunning') STOP
              input.form-control.input(:id='getFieldID(schema)', :type='schema.inputType.toLowerCase()', :value='value', @input='onInput', @blur='onBlur', :class='schema.fieldClasses', @change='schema.onChange || null', :disabled='disabled', :accept='schema.accept', :alt='schema.alt', :autocomplete='schema.autocomplete', :checked='schema.checked', :dirname='schema.dirname', :formaction='schema.formaction', :formenctype='schema.formenctype', :formmethod='schema.formmethod', :formnovalidate='schema.formnovalidate', :formtarget='schema.formtarget', :height='schema.height', :list='schema.list', :max='schema.max', :maxlength='schema.maxlength', :min='schema.min', :minlength='schema.minlength', :multiple='schema.multiple', :name='schema.inputName', :pattern='schema.pattern', :placeholder='schema.placeholder', :readonly='schema.readonly', :required='schema.required', :size='schema.size', :src='schema.src', :step='schema.step', :width='schema.width', :files='schema.files')
              span.helper(v-if="schema.inputType.toLowerCase() === 'color' || schema.inputType.toLowerCase() === 'range'") {{ value }}

</template>

<script>
import abstractField from "../abstractField";
export default({
  // name: "Timer",
	mixins: [ abstractField ],
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
