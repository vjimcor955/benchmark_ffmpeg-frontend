<template>
  <div class="home">
    <div class="home__content">
      <h2 class="home__content--title">Welcome to FFmpeg Benchmark</h2>
      <p class="home__content--message">Run different FFmpeg commands simultaneously to obtain performance test results with the ability to compare the resulting videos in the same player.</p>
      <RouterLink :to="{name: 'login'}" class="primary_button" v-if="!isLogged">Sign in</RouterLink>
      <RouterLink :to="{name: 'convert-video'}" class="primary_button" v-else>Convert video</RouterLink>
    </div>
    <img src="../assets/video-editing.webp" alt="Imagen home">
</div>
</template>


<script>
  import {useAuthStore} from '../stores/authStore.js'

  export default {
    name: 'HomeView',
    computed: {
      isLogged() {
        return useAuthStore().isLogged
      }
    },
  }
</script>


<style lang="scss">
  @import "../assets/sass/main.scss";

  .home {
    min-height: inherit;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    gap: 5%;

    .home__content {
      height: 300px;
      width: 30%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: flex-start;
      gap: 45px;

      &--title {
        font-size: 1.9rem;
        font-weight: bold;
        font-family: Orbitron;
      }

      &--message {
        font-size: 1.2rem;
      }

      @include primary_button($accent_color);
    } 

    img {
      width: auto;
      height: 350px;
      @include box-gradient($details_color);
    }
  }

  @media (max-width: 768px) {
    .home {
      flex-direction: column;
      gap: 20px;

      .home__content {
        width: 80%;
        gap: 20px;

        &--title {
          font-size: 1.5rem;
        }

        &--message {
          font-size: 1rem;
        }
      }

      img {
        width: 100%;
        height: auto;
      }
    }
  }
</style>