<template>
    這是後台頁面唷~
    <router-link to="/admin/products">後台產品列表</router-link> |
    <router-link to="/admin/orders">後台訂單列表</router-link> |
    <router-link to="/">回前台首頁</router-link> |
    <a href="#" @click.prevent="logout">登出</a>
    <hr>
    <RouterView></RouterView>
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
      document.cookie = `hexToken=;expires=${new Date()}; path=/`
      this.$router.push('/login')
    },
    checkLogin () {
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)myToken\s*=\s*([^;]*).*$)|^.*$/,
        '$1')
      this.$http.defaults.headers.common.Authorization = token

      const checkUrl = `${VITE_APP_URL}api/user/check`
      this.$http.post(checkUrl) // 驗證
        .then((res) => {
          console.log(res)
          if (!res.data.success) {
            this.$router.push('/login')
          }
        })
        .catch((err) => {
          alert(err.data.message)
        })
    }
  },
  mounted () {
    this.checkLogin()
  }
}
</script>
