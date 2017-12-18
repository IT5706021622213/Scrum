<template>
  <div class="content has-text-centered">
    <br><br><br><br><br>
    <p>
      <img width="400" src="~assets/NewScrum.png" :alt="description">
    </p>

    <!-- <h1 class="is-title is-bold">{{ name.replace('-', ' ') }}</h1> -->

    <!-- <p>
      <strong>{{ description }}</strong>,
      <a :href="homepage">Live Demo</a>
    </p> -->

    <p>Supports Vue 2.0 and Bulma 0.3!</p>
  </div>
</template>

<script>
export default {

  data () {
    return this.$store.state.pkg
  }

}
</script>

<style lang="scss" scoped>
.is-title {
  text-transform: capitalize;
}
</style>
