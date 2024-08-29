<template>
  <div id="signUpPage" class="bg-yellow">
    <div class="conatiner signUpPage vhContainer">
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
          <h2 class="formControls_txt">註冊帳號</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            v-model="email"
            type="text"
            id="email"
            name="email"
            placeholder="請輸入 email"
            required
          />
          <label class="formControls_label" for="name">您的暱稱</label>
          <input
            class="formControls_input"
            v-model="nickName"
            type="text"
            name="name"
            id="name"
            placeholder="請輸入您的暱稱"
          />
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            class="formControls_input"
            v-model="password"
            type="password"
            name="pwd"
            id="pwd"
            placeholder="請輸入密碼"
            required
          />
          <label class="formControls_label" for="pwdagain">再次輸入密碼</label>
          <input
            class="formControls_input"
            v-model="passwordAgain"
            type="password"
            name="pwdagain"
            id="pwdagain"
            placeholder="請再次輸入密碼"
            required
          />
          <span v-if="resultMessage">{{ resultMessage }}</span>
          <input class="formControls_btnSubmit" type="button" @click="signUp" value="註冊帳號" />
          <RouterLink to="/" class="formControls_btnLink" href="#loginPage">登入</RouterLink>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { RouterLink } from 'vue-router'

const baseUrl = 'https://todolist-api.hexschool.io/users'

const email = ref('')
const nickName = ref('')
const password = ref('')
const passwordAgain = ref('')

const resultMessage = ref('')

//註冊帳號
const signUp = async () => {
  try {
    if (checkPassword()) return
    const result = await axios.post(`${baseUrl}/sign_up`, {
      email: email.value,
      password: password.value,
      nickname: nickName.value
    })
    const { status } = result.data
    if (status) resultMessage.value = '註冊成功!!'
    //console.log(result.data)
  } catch (err) {
    const { message, status } = err.response.data
    resultMessage.value = message
  }
}

//檢查再次輸入密碼是否一致
const checkPassword = () => {
  if (password.value != passwordAgain.value) {
    resultMessage.value = '密碼不一致，請確認!!'
    return true
  }
  return false
}
</script>
