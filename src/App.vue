<script setup>
import { useRoute, useRouter } from 'vue-router'
import { useUserStore } from '@/stores'
const route = useRoute()
const router = useRouter()
const go = () => {
  console.log(router, route)
}
const userStore = useUserStore()
</script>

<template>
  <router-view></router-view>
  <hr />
  <div>app</div>
  <el-button @click="$router.push('/home')">1</el-button>
  <el-button @click="go">2</el-button>
  <hr />
  <p>{{ userStore.token }}</p>
  <el-button @click="userStore.setToken('1111')">登录</el-button>
  <el-button @click="userStore.removeToken">登出</el-button>
</template>

<style scoped></style>
