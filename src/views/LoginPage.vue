<template>
  <h3 style="color:white;">Duncan Hall Grille</h3>
  <form @submit.prevent="login" class="loginForm">
      <label>Email: </label>
      <input type="email" required v-model="login_form.email">
      <label>Password: </label>
      <input type="password" v-model="login_form.password" required>
      <button class="register">Login</button>
  </form>

  <img src="https://images.squarespace-cdn.com/content/v1/54dfd429e4b0549c366f98dd/1599506014278-OY3IXZWIHO6CRM7ZB6UO/5Q6A9980.jpg?format=2500w" alt="DuncanHall">


</template>

<script>

import { ref } from 'vue'
import { useStore } from 'vuex'

export default {

    setup(){

      const login_form = ref({});
      const store = useStore();


      const login = () => {
        store.dispatch('login',login_form.value)
      }
  
      return {
        login_form,
        login,
      }
    },
}

</script>

<style>
  .submit {
    text-align: center;
  }
  .error {
    color: #ff0062;
    margin-top: 10px;
    font-size: 0.8em;
    font-weight: bold;
  }
  .loginForm{
      max-width: 60%;
      background: transparent;
  }

  .banner{
      width: 100%;
      margin: 0;
      height: 100vh;
      background-image: linear-gradient(rgba(34, 34, 34, 0.75),rgba(65, 65, 65, 0.75)) ,url(../assets/duncandoor.jpeg);
      background-size: cover;
      background-position: center;
      overflow: auto;

  }

  .register{
      background: transparent;
      border: 2px solid rgb(13, 69, 253);
      box-shadow: inset 0 0 0 0 rgb(13, 69, 253);
      transition: ease-out 0.3s;
  }

  .register:hover{
      box-shadow: inset 300px 0 0 0 rgb(13, 69, 253);
      transition: ease-in 0.5s;
  }
</style>