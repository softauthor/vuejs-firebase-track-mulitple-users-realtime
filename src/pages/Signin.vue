<template>
  <section class="ui centered grid" style="margin-top:30px;">
    <div class="column" style="max-width:450px;">
      <form class="ui segment large form">
        <img class="ui centered medium image" src="@/assets/rt-logo.png" style="max-width:150px;" />
        <div class="ui segment">
          <div class="field">
            <div class="ui left icon input large">
              <input type="text" placeholder="Enter your email" v-model="email" />
              <i class="ui icon user"></i>
            </div>
          </div>
          <div class="field">
            <div class="ui left icon input large">
              <input type="password" placeholder="Enter your password" v-model="password" />
              <i class="ui icon key"></i>
            </div>
          </div>
          <button class="ui button fluid large red" @click="loginButtonPressed">Log in</button>
        </div>
        <router-link :to="{ name: 'SignUp' }" tag="a" class="ui button basic">Sign up</router-link>
      </form>
    </div>
  </section>
</template>
<script>
import firebase from "firebase";
export default {
  data() {
    return {
      email: "",
      password: ""
    };
  },
  methods: {
    loginButtonPressed() {
      firebase
        .auth()
        .signInWithEmailAndPassword(this.email, this.password)
        .then(user => {
          console.log("user is singed in ");
        })
        .catch(error => {
          console.log(error.message);
        });
    }
  }
};
</script>

