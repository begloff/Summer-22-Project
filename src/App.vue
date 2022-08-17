<template>

  <div v-if="$store.state.user && $store.state.employeeRole">
    <AdminNavBar></AdminNavBar>
    <router-view/>
  </div>
  <div v-else-if="$store.state.user">
    <div class="banner">
      <CustomerNavBar/>
      <router-view/>
    </div>
  </div>
  <div v-else>
    <div class="banner">
      <public-nav-bar/>
      <router-view/>
    </div>
  </div>

</template>

<script>
import { onBeforeMount } from 'vue'
import { useStore } from 'vuex'
import AdminNavBar from './components/AdminNavBar.vue'
import PublicNavBar from './components/PublicNavBar.vue'
import CustomerNavBar from './components/CustomerNavBar.vue'

import {setDoc, doc, collection} from "firebase/firestore"
import { operationCollection } from "./firebase"

export default{
  components: {
    AdminNavBar,
    PublicNavBar,
    CustomerNavBar
  },

  setup(){
    const store = useStore()

    onBeforeMount( async () => {
      await store.dispatch('fetchUser')
    })
  },

}


</script>


<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale; 
    text-align: center;
    color: #2c3e50;
  }

  h3{
      padding-top: 50px;
      font-size: 32pt;
  }
  form {
    margin: 30px auto;
    background: white;
    text-align: left;
    padding: 40px;
    border-radius: 10px;
  }
  label {
    color: #aaa;
    display: inline-block;
    margin: 25px 0 15px;
    font-size: 0.6em;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-weight: bold;
  }
  input, select {
    display: block;
    padding: 10px 6px;
    width: 100%;
    box-sizing: border-box;
    border: none;
    border-bottom: 1px solid #ddd;
    color: #555;
  }
  input[type="checkbox"] {
    display: inline-block;
    width: 16px;
    margin: 0 10px 0 0;
    position: relative;
    top: 2px;
  }
  .pill {
    display: inline-block;
    margin: 20px 10px 0 0;
    padding: 6px 12px;
    background: #eee;
    border-radius: 20px;
    font-size: 12px;
    letter-spacing: 1px;
    font-weight: bold;
    color: #777;
    cursor: pointer;
  }
  button {
    background: #0b6dff;
    border: 0;
    padding: 10px 20px;
    margin-top: 20px;
    color: white;
    border-radius: 20px;
  }

  img {
    border-radius: 8px;
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 50%;  
  }

  nav {
    padding: 30px;
  }
  
  nav a {
    font-weight: bold;
    color: #2c3e50;
  }
  
  nav a.router-link-exact-active {
    color: #42b983;
  }


</style>
