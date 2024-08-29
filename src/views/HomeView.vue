<template>
  <main>
    <div id="loginPage" class="bg-yellow">
      <div class="conatiner loginPage vhContainer">
        <div class="side">
          <a href="#"
            ><img
              class="logoImg"
              src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png"
              alt=""
          /></a>
          <img
            class="d-m-n"
            src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png"
            alt="workImg"
          />
        </div>
        <div>
          <form class="formControls" action="index.html">
            <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
            <label class="formControls_label" for="email">Email</label>
            <input
              class="formControls_input"
              type="text"
              id="email"
              name="email"
              v-model="mailAccount"
              placeholder="請輸入 email"
              required
            />
            <span v-if="isAccountNull">此欄位不可留空</span>
            <label class="formControls_label" for="pwd">密碼</label>
            <input
              class="formControls_input"
              type="password"
              name="pwd"
              id="pwd"
              v-model="password"
              placeholder="請輸入密碼"
              required
            />
            <span v-if="resultMessage">{{ resultMessage }}</span>
            <input class="formControls_btnSubmit" type="button" @click="signIn" value="登入" />
            <RouterLink to="/signup" class="formControls_btnLink">註冊帳號</RouterLink>
          </form>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const baseUrl = 'https://todolist-api.hexschool.io/users'

const mailAccount = ref('')
const password = ref('')
const token = ref('')
const isAccountNull = ref(false)
const resultMessage = ref('')

const router = useRouter()

//註冊
const signIn = async () => {
  try {
    if (checkAccount()) return
    const result = await axios.post(`${baseUrl}/sign_in`, {
      email: mailAccount.value,
      password: password.value
    })
    token.value = result.data.token
    console.log(result)
    setCookie()
    router.push('/todolist')
  } catch (err) {
    const { message, status } = err.response.data
    resultMessage.value = message
  }
}

//設定cookie
const setCookie = () => {
  const tomorrow = new Date()
  tomorrow.setDate(tomorrow.getDate() + 1)
  document.cookie = `hexschoolTodo=${token.value}; expires=${tomorrow.toUTCString()}`
}

//檢查帳號欄位不得為空
const checkAccount = () => {
  isAccountNull.value = mailAccount.value == '' ? true : false

  return isAccountNull.value
}
</script>
