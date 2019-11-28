<template>
  <div id="app">
    <div v-if="signedIn">
      <div id="nav">
        <div class="amplify-signed-out">
          <amplify-sign-out />
          ようこそ、{{ username }}さん
        </div>
      </div>
    </div>
    <div v-else>
      <amplify-authenticator />
    </div>
    <div id="nav">
      <li><router-link to="/ai">AIアプリ</router-link></li>
      <li><router-link to="/chat">チャットアプリ</router-link></li>
      <li><router-link to="/analytics">分析アプリ</router-link></li>
      <li><router-link to="/webpush">Web Pushアプリ</router-link></li>
      <router-view />
    </div>
  </div>
</template>

<script>
import { Auth } from "aws-amplify";
import { AmplifyEventBus } from "aws-amplify-vue";

window.LOG_LEVEL = "VERBOSE";

export default {
  name: "app",
  data() {
    return {
      signedIn: false,
      username: ""
    };
  },
  async beforeCreate() {
    try {
      let cognitoUser = await Auth.currentAuthenticatedUser();
      this.signedIn = true;
      this.username = cognitoUser.username;
    } catch (err) {
      this.signedIn = false;
    }
    AmplifyEventBus.$on("authState", async info => {
      if (info === "signedIn") {
        let cognitoUser = await Auth.currentAuthenticatedUser();
        this.signedIn = true;
        this.username = cognitoUser.username;
      } else {
        this.signedIn = false;
      }
    });
  }
};
</script>

<style src="./assets/style.css" />
