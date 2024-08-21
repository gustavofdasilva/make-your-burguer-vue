<template>
  <div>
  <Banner />
  <div class="main-container">
    <h1>Monte seu burger:</h1>
    <BurguerForm />
  </div>
  </div>
</template>

<script>
  import Banner from '@/components/Banner.vue';
  import BurguerForm from '@/components/BurguerForm.vue';

    export default {
        name: "Home",
        components: {
          Banner,
          BurguerForm
        }
    }
</script>

<style>

</style>