<template>
  這是登入畫面
  <form id="form" class="form-signin">
    <div class="form-floating mb-3">
      <input type="email" class="form-control email" id="username"
        placeholder="name@example.com" required autofocus v-model="user.username">
      <label for="username">Email address</label>
    </div>
    <div class="form-floating">
      <input type="password" class="form-control password" id="password"
        placeholder="Password" required v-model="user.password">
      <label for="password">Password</label>
    </div>
    <button class="btn btn-lg btn-primary w-100 mt-3 login" @click.prevent="login" type="submit">
      登入
    </button>
  </form>
</template>

<script>
const { VITE_APP_URL } = import.meta.env
export default {
  data () {
    return {
      user: {
        username: '',
        password: ''
      }
    }
  },
  created () {

  },
  methods: {
    login () {
      // 1.發送 API 至遠端並登入（並儲存 Token）
      this.$http.post(`${VITE_APP_URL}/v2/admin/signin`, this.user)
        .then(res => {
          const { token, expired } = res.data
          console.log(token, expired)
          // 儲存cookie
          document.cookie = `hexToken=${token}; expires=${new Date(expired)};`
          // document.cookie = `hexschool=${token}; expires=${new Date(expired)};`;

          // 轉址
          this.$router.push('/admin/products')
        }).catch(error => {
          console.dir(error)
        })
    }
  }
}
</script>
