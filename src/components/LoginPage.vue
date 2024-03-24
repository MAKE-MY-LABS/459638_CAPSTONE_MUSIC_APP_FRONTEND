<template>
  <q-page-container class="form-page">
    <!-- login page component -->
    //create dive with class as q-pa-md and style as max-width 400px
    //create h5 tag with text as Login to your account
    //create q-form tag with @submit as onSubmit and @reset as onReset and class as q-gutter-md
    //create q-input tag with filled, v-model as name, label as Email address, lazy-rules and :rules as array of function
    //create q-input tag with filled, type as password, v-model as password, label as Password, lazy-rules and :rules as array of function
    //create span tag
    //create router-link tag with to as /register and text as Don't have an account? Click to register.
    //create div tag
    //create q-btn tag with label as Login, type as submit, color as red
    //create q-btn tag with label as Reset, type as reset, flat, class as q-ml-sm, color as red
    //close div tag
    //close q-form tag
    //close div tag
    //close q-page-container tag

    <div class="q-pa-md" style="max-width: 400px">
      <h5>Login to your account</h5>
      <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
        <q-input
          filled
          v-model="name"
          label="Email address"
          lazy-rules
          :rules="[
            (val) => (val && val.length > 0) || 'Please enter a valid email',
          ]"
        />

        <q-input
          filled
          type="password"
          v-model="password"
          label="Password"
          lazy-rules
          :rules="[
            (val) =>
              (val !== null && val !== '') || 'Please type your password',
          ]"
        />
        <span>
          <router-link to="/register"
            >Don't have an account? Click to register.
          </router-link>
        </span>

        <div>
          <q-btn label="Login" type="submit" color="red" />
          <q-btn label="Reset" type="reset" flat class="q-ml-sm" color="red" />
        </div>
      </q-form>
    </div>
  </q-page-container>
</template>

<script>
import { ref } from "vue";
// import axios
import axios from "axios";
// import authenticationUtl method to save token in local storage
import { setUserAndToken } from "../util/authenticationUtil";
// import vue-router
import { useRouter } from "vue-router";

export default {
  setup() {
    // name, password,
    const name = ref("");
    const password = ref("");
    // router
    const router = useRouter();

    // onSubmit
    const onSubmit = async () => {
      // use http://localhost:8090/api/gaana/user/login using axios to get login token
      const response = await axios.post(
        "http://localhost:8090/api/gaana/user/login",
        {
          emailId: name.value,
          password: password.value,
        }
      );

      // get token from this response:- response.data = "User logged in successfully !! Token:eyJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE3MDk1MjY1MzEsInN1YiI6ImdhdXJhdkBjdHMuY29tIn0.jQ-oen2dks_i0uP_J0pPNyJfbP-tCWqKpCuk5Gsp7no"
      // find index of "Token:" and split the string to get the token
      if (response.data) {
        const token = response.data.split("Token:")[1];
        // save token in local storage
        setUserAndToken(token);
        // redirect to trending page
        router.push("/trending");
      }
    };

    // onReset
    const onReset = () => {
      name.value = "";
      password.value = "";
    };

    return {
      name,
      password,
      onSubmit,
      onReset,
    };
  },
};
</script>
<style scoped>
.form-page {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 3%;
  width: 100%;
}
</style>