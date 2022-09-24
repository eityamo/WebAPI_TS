<script setup lang="ts">
import { reactive } from 'vue'
import { ref } from 'vue'
import axios from 'axios'

type User = {
  name: string
  login: string
  location: string
  publicRepos: number
  avatarUrl: string
  htmlUrl: string
}

type Response = {
  login: string
  id: number
  node_id: string
  avatar_url: string
  gravatar_id: string
  url: string
  html_url: string
  followers_url: string
  following_url: string
  gists_url: string
  starred_url: string
  subscriptions_url: string
  organizations_url: string
  repos_url: string
  events_url: string
  received_events_url: string
  type: string
  site_admin: boolean
  name: string
  company: string
  blog: string
  location: string
  email: string
  hireable: string
  bio: string
  twitter_username: string
  public_repos: number
  public_gists: number
  followers: number
  following: number
  created_at: string
  updated_at: string
}

// リアクティブな変数 プリミティブref オブジェクトreactive
// データプロパティ userInfo
const userInfo = reactive<User>({
  name: '',
  login: '',
  location: '',
  publicRepos: 0,
  avatarUrl: '',
  htmlUrl: ''
})

const userId = ref('eityamo')
const status = ref<'loading' | 'normal' | 'unknown'>('normal')

const fetchUserInfo = (userId: string): void => {
  status.value = 'loading'
  axios.get<Response>(`https://api.github.com/users/${userId}`)
  .then(( response ) => {
    const { name, login, location, public_repos, avatar_url, html_url } = response.data
    userInfo.name = name
    userInfo.login = login
    userInfo.location = location
    userInfo.publicRepos = public_repos
    userInfo.avatarUrl = avatar_url
    userInfo.htmlUrl = html_url

    status.value = 'normal'
  }).catch(error => {
    status.value = 'unknown'
  })
}

</script>

<template>
  <h2>Github情報を取得</h2>
  <h3>ログイン名を入力</h3>
  <input v-model="userId" />
  <button @click="fetchUserInfo(userId)">取得</button>
  <div v-if="status === 'loading'">
    <p>読み込み中</p>
  </div>
  <div v-if="status === 'unknown'">
    <p>アカウントは見つかりませんでした</p>
  </div>
  <div v-else>
    <h4>{{ userInfo.name }}</h4>
    <h4>{{ userInfo.login }}</h4>
    <img :src="userInfo.avatarUrl" :alt="userInfo.login" height="100">
    <dl>
      <dt>Location</dt>
      <dd>{{ userInfo.location }}</dd>

      <dt>Repositories</dt>
      <dd>{{ userInfo.publicRepos }}</dd>

      <dt>Github_Link</dt>
      <dd>{{ userInfo.htmlUrl }}</dd>
    </dl>
  </div>
</template>

<style scoped>
</style>
