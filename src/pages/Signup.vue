<template>
  <section class="ui centered grid" style="margin-top:30px;">
    <div class="column" style="max-width:450px;">
      <form class="ui segment large form">
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
          <button class="ui button fluid large green" @click="signUpButtonPressed">Sign up</button>
        </div>
      </form>
      <router-link :to="{ name: 'Signin' }" tag="a" class="ui button basic">Log in</router-link>
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
  mounted() {},
  methods: {
    signUpButtonPressed() {
      firebase
        .auth()
        .createUserWithEmailAndPassword(this.email, this.password)
        .then(user => {
          this.addUserToDB(user);
        })
        .catch(error => {
          this.error = error.message;
        });
    },
    async addUserToDB({ user }) {
      try {
        const db = firebase.firestore();
        _ = await db
          .collection("users")
          .doc(user.uid)
          .set({
            email: user.email,
            active: false
          });
        this.$router.push({
          name: "Driver"
        });
      } catch (error) {
        console.log(error.message);
      }
    }
  }
};
</script>

