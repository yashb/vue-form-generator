<template>
  <section class="hero is-primary">
    <div class="hero-body">
      <div class="container">
        <h1 class="title">
          {{schema.title}}
        </h1>
        <h2 class="subtitle">
          {{schema.subtitle}}
        </h2>
      </div>
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
