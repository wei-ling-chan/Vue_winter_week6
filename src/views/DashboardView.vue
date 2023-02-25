<template>
  這是後台頁面
  <router-link to="/admin/products">後臺產品列表</router-link> |
  <router-link to="/admin/orders">後台訂單列表</router-link> |
  <router-link to="/">回前台首頁</router-link> |
  <a href="#" @click.prevent="logout">登出</a>
  <hr/>
  <router-view></router-view>
</template>

<script>
import { RouterView } from 'vue-router'
const { VITE_APP_URL } = import.meta.env
export default {
  components: {
    RouterView
  },
  methods: {
    logout () {
      document.cookie = `hexToken=; expires=${new Date()};`
      this.$router.push('/login')
    },
    check () {
      // 取出token
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/, '$1')
      // 預設token帶入header
      this.$http.defaults.headers.common.Authorization = token
      this.$http.post(`${VITE_APP_URL}/v2/api/user/check`)
        .then(res => {
          console.log(res)
        }
        ).catch(error => {
          console.dir(error)
          this.$router.push('/login')
        })
    }
  },
  mounted () {
    this.check()
  }
}
</script>
