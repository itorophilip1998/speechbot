<template>
  <div class="vh-100">
      <Loading v-if="loading"/>

    <Header :info="info" />
    <div class="databox">
      <form class="mt-5 pt-2 p-3">
        <div v-if="error" class="alert alert-danger" role="alert">
          Oops! An Error Occured!
        </div>
        <input
          type="text"
          minlength="5"
          maxlength="10"
          v-model="details.username"
          required
          class="w-100 mt-4 p-2"
          placeholder="username"
        />
        <input
          required
          v-model="details.password"
          minlength="5"
          maxlength="10"
          type="password"
          class="w-100 mt-4 mb-1 p-2"
          placeholder="Password"
          ref="password"
        />
        <i
          v-if="!passwordCheckData"
          class="fa fa-eye link password-check text-primary"
          @click="passwordCheck('show')"
        ></i>
        <i
          v-if="passwordCheckData"
          class="fa fa-eye-slash password-check link text-primary"
          @click="passwordCheck('open')"
        ></i>
        <button
          class="btn-primary text-white shadow w-100 mt-5 btn rounded-pill p-2"
          @click="register()"
          type="button"
        >
          <b> Join Speechbot</b>
        </button>
        <button
          class="bg-white shadow w-100 mt-3 btn rounded-pill p-2"
          type="button"
          @click="$router.push('/login')"
        >
          I dont have an account? <b class="text-primary">login</b>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import Header from '@/components/auth-header'
import axios from '@nuxtjs/axios'
import Loading from "./Loader.vue"

export default {
  auth: false,

  components: {
    Header,
    Loading,
  },
  data() {
    return {
      details: {
        username: '',
        password: '',
      },
      passwordCheckData: false,
      info: {
        name: 'Register',
        short_name: 'Welcome!',
        details: 'Join Speechbot Community to see AI in action.',
        icon: 'fa-sign-in',
        dashboard: false,
      },
      error: false,
      loading:false
    }
  },
  methods: {
    passwordCheck(data) {
      if (data == 'show') {
        this.passwordCheckData = !this.passwordCheckData
        this.$refs.password.type = 'text'
      } else {
        this.passwordCheckData = !this.passwordCheckData
        this.$refs.password.type = 'password'
      }
    },

    register() {
          this.loading=true;

      const config = {
        headers: {
          'Content-Type': `application/json`,
        },
      }
      this.$axios
        .post('/signup', this.details, config)
        .then((res) => {
          this.loading=false;

          localStorage.setItem('token', res.data.access_token)
          localStorage.setItem('username', res.data.result.username)
          localStorage.setItem('_id', res.data.result._id)
          this.$router.push('/chat')
        })
        .catch((err) => {
          this.loading=false;

          this.error = true
          setTimeout(() => {
            this.error = false
          }, 2000)
        })
    },
  },
}
</script>
<style>
.vh-100 {
  height: 100vh;
}

i.fa-eye-slash,
i.fa-eye {
  position: absolute;
  right: 30px;
  margin-top: -32px;
}
</style>
