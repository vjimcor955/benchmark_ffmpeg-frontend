<template>
  <div class="register">
    <h2>Register</h2>
    <form class="register__form">
      <div class="register__form--field">
        <label for="nameRegister">Name:</label>
        <input v-model="registerData.name" type="text" name="nameRegister" id="nameRegister">
      </div>
      <p v-if="nameError" class="validation">Only letters are allowed in 'Name'</p>
      <div class="register__form--field">
        <label for="emailRegister">Email:</label>
        <input v-model="registerData.email" type="email" name="emailRegister" id="emailRegister">
      </div>
      <p v-if="emailError" class="validation">Incorrect email address</p>
      <div class="register__form--field">
        <label for="userRegister">User:</label>
        <input v-model="registerData.user" type="text" name="userRegister" id="userRegister">
      </div>
      <p v-if="userError" class="validation">Only letters and numbers are allowed in 'User'</p>
      <div class="register__form--field">
        <label for="passwordRegister">Password:</label>
        <input v-model="registerData.password" type="text" name="passwordRegister" id="passwordRegister">
      </div>
      <p v-if="passwordError" class="validation">The password must be at least 8 characters long, containing a letter, a number and a special character</p>
      <input type="submit" value="Register" v-bind:disabled="registerButtonDisabled" class="primary_button" @click="handleRegister">
      <p v-if="registered" class="confirmation">Successfully registered</p>
      <p v-if="emailUsed" class="validation">Email already in use</p>
      <Loader v-if="loading"/>
    </form>    
  </div>
</template>


<script>
  import axios from 'axios';
  import Loader from '../components/Loader.vue';
  import { toast } from "vue3-toastify";

  export default {
    name: 'RegisterForm',
    data() {
      return {
        registerData: {
          name: null,
          email: null,
          user: null,
          password: null,
        },
        nameError: false,
        emailError: false,
        userError: false,
        passwordError: false,
        registerButtonDisabled: true,
        registered: false,
        emailUsed: false,
        loading: false
      }
    },
    components: {
      Loader
    },
    watch: {
      'registerData.name'() {
        this.validateInput('name', this.registerData.name);
        this.check_registerButton()
      },
      'registerData.email'() {
        this.validateInput('email', this.registerData.email);
        this.check_registerButton()
        this.emailUsed = false
      },
      'registerData.user'() {
        this.validateInput('user', this.registerData.user);
        this.check_registerButton()
      },
      'registerData.password'() {
        this.validateInput('password', this.registerData.password);
        this.check_registerButton()
      },     
    },
    methods: {
      /**
       * Method that validates the input of the user
       * @param {string} type - Type of input to validate
       * @param {string} value - Value of the input to validate
       * @returns {boolean} - Returns true if the input is valid
       */
      validateInput(type, value) {
        let re;
        switch (type) {
          case 'name':
            // Name only contains letters
            re = /^[a-zA-Z\s]*$/;
            this.nameError = !re.test(value);
            break;
          case 'email':
            // Email validation
            re = /\S+@\S+\.\S+/;
            this.emailError = !re.test(value);
            break;
          case 'user':
            // Name only contains letters and numbers
            re = /^[a-zA-Z0-9]*$/;
            this.userError = !re.test(value);
            break;
          case 'password':
            // Minimum eight characters, at least one letter, one number and one special character
            re = /^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{8,}$/;
            this.passwordError = !re.test(value);
            break;
        }
        return !this[type + 'Error'];
      },
      /**
       * Method that checks if the register button should be disabled
       * @returns {boolean} - Returns true if the register button should be disabled
       */
      check_registerButton() {
        if (this.registerData.name && this.registerData.email && this.registerData.user && this.registerData.password && !this.nameError && !this.emailError && !this.userError && !this.passwordError) return this.registerButtonDisabled = false
        this.registerButtonDisabled = true
      },
      /**
       * Method that handles the register of a new user
       * @param {Event} e - Event that triggered the method
       */
      async handleRegister(e) {
        e.preventDefault()
        try {
          const response = await axios.post('https://ffmpeg-benckmark-api-646aff7ac349.herokuapp.com/api/user/register', this.registerData);
          if (response.data.message === 'Email already in use.') return this.emailUsed = true
          this.registered = true
          this.loading = true
          toast.success("Successfully registered", {
            autoClose: 1500,
            position: "top-center"
          });
          setTimeout(() => {
            this.$emit('registered', false);
          }, 2000);
        } catch (error) {
          console.error(error)
        }
      }
    }
  }
</script>



<style scoped lang="scss"> 
  @import "../assets/sass/main.scss";

  .register {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

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
      gap: 25px;
      padding: 5px 25px;
      border-radius: 5px;
  
      &--field {
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: left;
        gap: 10px;
        
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
    .register {
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
    .register {
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
