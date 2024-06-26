<template>
  <div class="login">
    <h2>Login</h2>
    <form class="login__form">
      <div class="login__form--field">
        <label for="emailLogin">Email:</label>
        <input v-model="loginData.email" type="text" name="emailLogin" id="emailLogin">
      </div>
      <div class="login__form--field">
        <label for="passwordLogin">Password:</label>
        <input v-model="loginData.password" type="password" name="passwordLogin" id="passwordLogin">
      </div>
      <input type="submit" value="Login" v-bind:disabled="loginButtonDisabled" class="primary_button" @click="handleLogin">
      <p v-if="loginError" class="validation">Incorrect credentials</p>
      <p v-if="loggingIn" class="confirmation">Correct credentials</p>
      <Loader v-if="loading"/>
    </form>
  </div>
</template>


<script>
  import { useAuthStore } from '../stores/authStore.js'
  import axios from 'axios'
  import Loader from './Loader.vue';
  import { toast } from "vue3-toastify";

  export default {
    name: 'LoginForm',
    data() {
      return {
        loginData: {
          email: null,
          password: null,
        },
        loginError: false,
        loggingIn: false,
        loginButtonDisabled: true,
        loading: false
      }
    },
    components: {
      Loader
    },
    watch: {
      'loginData.password'() {
        this.check_loginButton()
        this.loginError = false
      },
    },
    computed: {
      isLogged() {
        return useAuthStore().isLogged
      }
    },
    methods: {
      /**
       * Method that checks if the login button should be disabled
       * @returns {boolean} - Returns true if the login button should be disabled
       * 
       */
      check_loginButton() {
        if (this.loginData.email && this.loginData.password) return this.loginButtonDisabled = false
        this.loginButtonDisabled = true
      },
      /**
       * Method that handles the login process
       * @param {Event} e - Event object
       * 
       */
      async handleLogin(e) {
        e.preventDefault()
        try {
          const response = await axios.post('https://ffmpeg-benckmark-api-646aff7ac349.herokuapp.com/api/user/login', this.loginData);
          const user = {
            id: response.data.id,
            name: response.data.name,
            email: response.data.email,
            user: response.data.user,
            password: response.data.password,
            token: response.data.token,
          }
          useAuthStore().logIn(user)
          this.loggingIn = true
          this.loading = true
          toast.success("Logging in...", {
            autoClose: 1500,
            position: "top-center",
          });
          setTimeout(() => {
            this.$router.push({name: "root-home"});
          }, 2000);
        } catch (error) {
          console.error(error)
          this.loginError = true
          this.loggingIn = false
        }
      },
    }
  }
</script>



<style scoped lang="scss">
  @import "../assets/sass/main.scss";

  .login {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 35px;

    h2 {
      text-align: center;
      font-size: 1.8em;
      font-weight: bold;
      margin: 15px;
      font-family: Orbitron;
    }
    
    &__form {
      min-height: fit-content;
      width: 350px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 40px;
      padding: 5px 25px;
      border-radius: 5px;

      &--field {
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: left;
        gap: 15px;

        label {
          font-size: 1.2rem;
          font-weight: bold;
        }

        input {
          font-size: 1rem;
          border: 2px solid $accent-color;
          border-radius: 10px;
          padding: 0.5rem;
        }
      }

      @include primary_button($accent_color);

      .validation {
        border: 1px solid red;
        color: red;
        text-align: center;
        padding: 10px;
        border-radius: 5px;
        font-size: 0.9em;
      }

      .confirmation {
        border: 1px solid green;
        color: green;
        text-align: center;
        padding: 10px;
        border-radius: 5px;
        font-size: 0.9em;
      }
    }
  }

  @media (max-width: 768px) {
    .login {
      width: 50%;
      padding: 0px 20px;

      &__form {
        gap: 20px;

        &--field {
          gap: 15px;

          label {
            font-size: 1.1rem;
          }
        }
      }
    }
  }


  @media (max-width: 480px) {
    .login {
      width: 100%;
      padding: 0px 20px;

      &__form {
        width: 100%;
        padding: 5px;
        gap: 15px;

        &--field {
          gap: 15px;

          label {
            font-size: 1.1rem;
          }
        }
      }
    }
  }
</style>