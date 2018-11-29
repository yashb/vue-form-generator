<template lang="pug">
.timerset
	h2.label {{schema.title}}
	.columnsn
		.column.is-2
			div.settimer.label {{ minutes }}:{{ seconds }}:{{ milliSeconds }}
		.column.is-1
			a.button.is-info.is-outlined.is-normal(@click='startTimer', :disabled='isRunning') START
		.column.is-1
			a.button.is-danger.is-outlined.is-normal(@click='stopTimer',  :disabled='!isRunning') STOP
		.column.is-9
			input.form-control.input(
			:id='getFieldID(schema)',
			:type='schema.inputType.toLowerCase()',
			:value='value',
			@input='onInput',
			@blur='onBlur',
			:class='schema.fieldClasses',
			@change='schema.onChange || null',
			:disabled='disabled',
			:accept='schema.accept',
			:alt='schema.alt',
			:autocomplete='schema.autocomplete',
			:checked='schema.checked',
			:dirname='schema.dirname',
			:formaction='schema.formaction',
			:formenctype='schema.formenctype',
			:formmethod='schema.formmethod',
			:formnovalidate='schema.formnovalidate',
			:formtarget='schema.formtarget',
			:height='schema.height',
			:list='schema.list',
			:max='schema.max',
			:maxlength='schema.maxlength',
			:min='schema.min',
			:minlength='schema.minlength',
			:multiple='schema.multiple',
			:name='schema.inputName',
			:pattern='schema.pattern',
			:placeholder='schema.placeholder',
			:readonly='schema.readonly',
			:required='schema.required',
			:size='schema.size',
			:src='schema.src',
			:step='schema.step',
			:width='schema.width',
			:files='schema.files')
			span.helper(v-if="schema.inputType.toLowerCase() === 'color' || schema.inputType.toLowerCase() === 'range'") {{ value }}
</template>

<script>
import abstractField from "../abstractField";
import { isFunction, isNumber } from "lodash";
import fecha from "fecha";

const DATETIME_FORMATS = {
	"date": "YYYY-MM-DD",
	"datetime": "YYYY-MM-DD HH:mm:ss",
	"datetime-local": "YYYY-MM-DDTHH:mm:ss",
};


export default({
//name: 'app',
	// id: null,

	mixins: [ abstractField ],
	data(){
		return {
			times: [],
			animateFrame: 0,
			nowTime: 0,
			diffTime: 0,
			startTime: 0,
			isRunning: false,
			// id: null
		};

	},

	mounted() {
		console.log("TimerField Mounted called");
		this.finalValue = this.value;
	},

	// watch: {
	// 	diffTime: function(val, oldVal) {
	//
	// 	}
	// },

	methods: {

		formatValueToModel(value) {
			if (value != null) {
				switch (this.schema.inputType.toLowerCase()) {
					case "date":
					case "datetime":
					case "datetime-local":
					case "number":
					case "range":
						// debounce
						return (newValue, oldValue) => {
							this.debouncedFormatFunc(value, oldValue);
						};
				}
			}

			return value;
		},
		formatDatetimeToModel(newValue, oldValue) {
			let defaultFormat = DATETIME_FORMATS[this.schema.inputType.toLowerCase()];
			let m = fecha.parse(newValue, defaultFormat);
			if (m !== false) {
				if (this.schema.format) {
					newValue = fecha.format(m, this.schema.format);
				} else {
					newValue = m.valueOf();
				}
			}
			this.updateModelValue(newValue, oldValue);
		},
		formatNumberToModel(newValue, oldValue) {
			if(!isNumber(newValue)) {
				newValue = NaN;
			}
			this.updateModelValue(newValue, oldValue);
		},
		onInput($event) {
			let value = $event.target.value;
			switch(this.schema.inputType.toLowerCase()) {
				case "number":
				case "range":
					if(isNumber($event.target.valueAsNumber)) {
						value = $event.target.valueAsNumber;
					}
					break;
			}
			this.value = value;
			this.finalValue = value;
			//this.seconds = Math.floor(value % 60);
		},
		onBlur() {
			if(isFunction(this.debouncedFormatFunc)) {
				this.debouncedFormatFunc.flush();
			}
		},



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
			this.value = this.diffTime; // (this.minutes * 60) + this.seconds; //total seconds
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
		finalValue:{
			get() {
				return this.diffTime;
			},
			set(value){
				this.diffTime = value;
			}
		}
	},

});
</script>
<style lang="css">
    .settimer {
        font-size: 23px;
        margin-top: 2px;
    }
</style>
