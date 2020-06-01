<template>
  <div class="home">
    <h1>Test Bubble</h1>
    
    <p v-if="!login.token">
      <input type="text" class="text" placeholder="email" v-model="login.user">
      <input type="password" class="text" placeholder="password" v-model="login.password">
      <button @click="doLogin()">Login</button>
    </p>
    <p v-else>
      <button @click="doLogout()">Logout</button>
    </p>

    <input placeholder="New item name" type="text" v-model="newName" name="" id="" @keydown.enter="add()">

    <div style="height:20px;" ></div>

    <button @click="fetchData()" >Refresh</button>

    <p style="color:red">
      {{errorMessage}}
    </p>

    <div v-for="item in items" :key="item._id">
      {{item.Name}}
    </div>

  </div>
</template>

<script>

import axios from "axios";

export default {
  components: {
    
  },
  data: () => {
    return {
      newName: "",
      items: [ ],
      errorMessage: "",
      login:{
        user: "raphael@vsoft.com.br",
        password: "",
        token: ""
      }
    }
  },
  methods:{
    add(){
      var data = {
        "name" : this.newName
      }
      axios.post("item-insert", data, this.getAxiosConfig(true))
      .then(res => {
        if(res.data.status == "success"){
          this.newName = ""
          this.fetchData();
        }else{
          this.errorMessage = res.data.response.message
        }
      })
      .catch(reason => {
        this.errorMessage = reason.message;
        window.console.log(reason);
      })
    },
    getAxiosConfig(auth = false){
      //var token = '1590965134115x403701199060871700';
      var headers = {
        "Content-Type": "application/json"
      }
      if(auth){
        headers["Authorization"] = 'Bearer ' + this.login.token
      }
      return {
        baseURL: "https://myapi123.bubbleapps.io/version-test/api/1.1/wf/",
        headers,
        method: "POST"
      }
    },
    fetchData(){
      this.errorMessage = ""
      axios.post("item-list", {}, this.getAxiosConfig(true))
      .then(res => {
        if(res.data.status == "success"){
          this.items = res.data.response.items
        }else{
          this.errorMessage = res.data.response.message
        }
        //window.console.log(res);
      })
      .catch(reason => {
        this.errorMessage = reason.message
        window.console.log(reason);
      })
    },
    doLogin(){
      this.errorMessage = ""
      var data = {
        user: this.login.user,
        password: this.login.password
      }
      axios.post("auth", data, this.getAxiosConfig())
      .then(res => {
        if(res.data.status == "success"){
          this.login.token = res.data.response.token
          this.login.password = ""
        }else{
          this.errorMessage = res.data.response.message
        }
      }).catch(reason => {
        this.errorMessage = reason.message
        window.console.log(reason);
      })
    },
    doLogout(){
      this.login.token = ""
    }
  }
}
</script>
