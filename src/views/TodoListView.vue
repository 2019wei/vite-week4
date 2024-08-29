<template>
  <div id="todoListPage" class="bg-half">
    <nav>
      <h1><a href="#">ONLINE TODO LIST</a></h1>
      <ul>
        <li class="todo_sm">
          <a href="#"
            ><span>{{ nickname }}</span></a
          >
        </li>
        <li><a href="#loginPage" @click.prevent="signOut">登出</a></li>
      </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input
            type="text"
            v-model="tempTodo"
            placeholder="請輸入待辦事項"
            @keyup.enter="addTodoList"
          />
          <a href="#" @click.prevent="addTodoList">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <div v-if="tempData.length == 0">目前尚無待辦事項</div>
        <div class="todoList_list" v-else>
          <ul class="todoList_tab">
            <li>
              <a
                href="#"
                :class="listIndex == 0 ? 'active' : ''"
                @click.prevent="changeListIndex(0)"
                >全部</a
              >
            </li>
            <li>
              <a
                href="#"
                :class="listIndex == 1 ? 'active' : ''"
                @click.prevent="changeListIndex(1)"
                >待完成</a
              >
            </li>
            <li>
              <a
                href="#"
                :class="listIndex == 2 ? 'active' : ''"
                @click.prevent="changeListIndex(2)"
                >已完成</a
              >
            </li>
          </ul>
          <div class="todoList_items">
            <ul class="todoList_item" v-for="itme in todoList" :key="itme.id">
              <li>
                <label class="todoList_label">
                  <input
                    class="todoList_input"
                    type="checkbox"
                    v-model="itme.status"
                    @click="patchTodoList(itme.id)"
                  />
                  <span>{{ itme.content }}</span>
                </label>
                <a href="#" @click.prevent="deleteTodoList(itme.id)">
                  <i class="fa fa-times" aria-hidden="true"></i>
                </a>
              </li>
            </ul>
            <div class="todoList_statistics">
              <p>{{ pendingCount }} 個待完成項目</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { computed, onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'

const baseUrl = 'https://todolist-api.hexschool.io'
const router = useRouter()

const tokenCheck = ref('')
const nickname = ref('')

//驗證
const checkOut = async () => {
  try {
    const result = await axios.get(`${baseUrl}/users/checkout`, {
      headers: {
        Authorization: tokenCheck.value
      }
    })
    //console.log(result.data)
    nickname.value = result.data.nickname
  } catch (err) {
    //console.log(err.message)
    alert('驗證失敗，請回登入頁面')
    router.push('/')
  }
}

const todoList = ref([])
const tempData = ref([])

//取得代辦清單
const getTodoList = async () => {
  try {
    const result = await axios.get(`${baseUrl}/todos`, {
      headers: {
        Authorization: tokenCheck.value
      }
    })
    console.log(result.data.data)
    tempData.value = result.data.data
    changeIndexTodoList()
  } catch (err) {
    alert('資料取得失敗!!')
  }
}

const tempTodo = ref('')
//新增代辦清單
const addTodoList = async () => {
  try {
    const result = await axios.post(
      `${baseUrl}/todos`,
      {
        content: `${tempTodo.value}`
      },
      {
        headers: {
          Authorization: tokenCheck.value
        }
      }
    )
    //成功後清空輸入列
    tempTodo.value = ''
    getTodoList()
    alert('新增代辦成功!!')
  } catch (err) {
    alert('新增失敗!!')
  }
}

//切換代辦狀態
const patchTodoList = async (id) => {
  try {
    const result = await axios.patch(
      `${baseUrl}/todos/${id}/toggle`,
      { id: id },
      {
        headers: {
          Authorization: tokenCheck.value
        }
      }
    )
    //getTodoList()
    changeIndexTodoList()
  } catch (err) {
    alert('切換狀態失敗!!')
  }
}

//刪除代辦事項
const deleteTodoList = async (id) => {
  try {
    const result = await axios.delete(`${baseUrl}/todos/${id}`, {
      headers: {
        Authorization: tokenCheck.value
      }
    })
    getTodoList()
  } catch (err) {
    alert('刪除失敗!!')
  }
}

const listIndex = ref(0)

//切頁
const changeListIndex = (idx) => {
  listIndex.value = idx
  changeIndexTodoList()
}
//切頁TodoList清單內容
const changeIndexTodoList = () => {
  if (listIndex.value == 0) {
    todoList.value = tempData.value
  } else if (listIndex.value == 1) {
    todoList.value = tempData.value.filter((x) => x.status == false)
  } else if (listIndex.value == 2) {
    todoList.value = tempData.value.filter((x) => x.status == true)
  }
}

//待完成筆數
let pendingCount = ref(0)

pendingCount = computed(() => tempData.value.filter((x) => x.status == false).length)

//登出
const signOut = async () => {
  try {
    const response = await axios.post(
      `${baseUrl}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: tokenCheck.value
        }
      }
    )
    //console.log(response.data)
    router.push('/')
  } catch (error) {
    alert('登出錯誤!!')
  }
}

//cookie
const TodoToken = document.cookie
  .split('; ')
  .find((row) => row.startsWith('hexschoolTodo='))
  ?.split('=')[1]

onMounted(() => {
  if (TodoToken) {
    tokenCheck.value = TodoToken
    checkOut()
    getTodoList()
  } else {
    alert('驗證失敗，請回登入頁面')
    router.push('/')
  }
})
</script>
