<template>
  <v-row justify="center" class="mt-10">
    <v-col cols="12" sm="10" md="8" lg="6">

      <v-card ref="form">


        <v-toolbar
            flat
            color="teal"
            >
            <v-icon class="white--text">mdi-account-key</v-icon>
            <v-toolbar-title class="font-weight-light white--text ml-3">
                Admin Login
            </v-toolbar-title>

        </v-toolbar>

        <v-card-text>

          <v-text-field
            v-model="username"
            :rules="[() => !!username || 'This field is required']"
            label="Username"
            placeholder="John Doe"
            @keydown.enter="submit"
            required
          ></v-text-field><br/>

          <v-text-field
            v-model="password"
            :rules="[() => !!password || 'This field is required']"
            label="Password"
            placeholder="Password"
            type="password"
            @keydown.enter="submit"
            required
          ></v-text-field>

        </v-card-text>

        <v-divider class="mt-12"></v-divider>
        <v-card-actions>

          <v-btn class="ma-2" @click="clear" outlined color="warning" large >Clear</v-btn>

          <v-spacer></v-spacer>


          <v-btn class="ma-2" outlined color="success" large @click="submit">Login</v-btn>
        </v-card-actions>

        <AlertBox :show="show" :showError="message" @clickOK="handleOK" />

      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import AlertBox from "../components/DialogBox/Alertbox.vue";
import firebase from "firebase";

export default {
  
  name: "AdminLogin",

  components : {
    AlertBox
  },

  data(){
    return {
      username : '',
      password : '',
      show: false,
      message: "",
    }
  },

  methods :{

    clear(){
      this.username = '';
      this.password = '';
    },

    submit(){
      if(this.username == '' || this.password == ""){
        this.show = true;
        this.message = "Fields cannot be empty";
      }else{
        console.log(this.username);
        firebase.firestore().collection('admins').doc(this.username).get()
        .then(doc => {
          console.log("INSIDE THIS");
          if(doc.data().username == this.username && doc.data().password == this.password){

            let now_timestamp = Date.now();
            let expire_timestamp = now_timestamp + (10000000 * 60 * 60);

            console.log(now_timestamp , expire_timestamp);

            this.$store.state.currentAdmin.username = this.username;
            this.$store.state.currentAdmin.login_time = now_timestamp;
            this.$store.state.currentAdmin.logout_time = expire_timestamp;

            this.show = true;
            this.message = 'You are logged in!';

            this.$router.push("/dashadmin");

          }else{
            this.show = true;
            this.message = "Username Or password is incorrect!";
          }

        })
        .catch(() => {
          this.show = true;
          this.message = "Username Or password is incorrect!";
        })

      }
    },

    handleOK(){
      this.show = false;
    }
  }
  


};
</script>
