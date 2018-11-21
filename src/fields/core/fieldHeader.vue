<template>
  <section class="hero" :class="schema.heroColorClass">
    <div :class="schema.heroBodyClass">
        <h1 class="title">
          {{title}}
        </h1>
        <h2 class="subtitle">
          {{subtitle}}
        </h2>
    </div>
  </section>
</template>

<script>
	import abstractField from "../abstractField";

	export default {
		mixins: [ abstractField ]
	};

</script>


<style lang="sass">
</style>
