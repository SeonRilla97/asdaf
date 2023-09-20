<script setup>
import { onBeforeMount, reactive, ref } from 'vue';


//state
const SERVER_URL = "http://localhost:9090"
const todo = reactive({
  list:[],
  select: null
})
const isModalOpen = ref(false);


onBeforeMount(()=>{
  getList()
})

// 전체 목록 불러와서 list에 삽입
function getList() {
  let options ={
    "methods" : 'GET'
  }
  fetch(`${SERVER_URL}/todo`, options)
  .then(res=>res.json())
  .then(result=>{
    todo.list=result
    console.log(todo.list);
  })
}

function setSelect(item) {
  let select = todo.select;
  if(item.id=== select) {
    todo.select= null;
    return;
  }
  todo.select = item.id;
}

function updateTodo(item) {
  let res;
  let myHeaders = new Headers();
  myHeaders.append("Content-Type", "application/json");

  let raw = JSON.stringify(item);

  let requestOptions = {
    method: 'PUT',
    headers: myHeaders,
    body: raw,
    redirect: 'follow'
  };

  fetch("http://localhost:9090/todo", requestOptions)
    .then(response => response.json())
    .catch(error => false);
}

function chkboxClickHnadler(item){
  item.isChecked = !item.isChecked;
  const hi=updateTodo(item)
  console.log(hi);
  
  
}
//effect
</script>

<template>
  <div class="container">
    <!-- Todo Component -->
    <div class="todo">
      <h1 class="title">Todo List</h1>
      <!-- Update Delete Button -->
      <div class="btn-con">
        <div class="btn normal update center">Update</div>
        <div class="btn normal delete center">Delete</div>
      </div>
      <!-- Todo List -->
      <ul class="list">
        
        <li class="item" v-for="item in todo.list" @click="setSelect(item)" :class="todo.select===item.id? 'select':''">
          <span class="no-select">{{item.todo}}</span>
          <div>
            <input :id="'chk'+item.id" type="checkbox" >
            <label class="c-pointer" :for="'chk'+item.id" @click="chkboxClickHnadler(item)"></label>
          </div>
        </li>
      </ul>

      <div class="center">
          <div class="btn create" @click="isModalOpen=true">create</div>
      </div>
    </div>
  </div>


  <div class="modal-container" :class="isModalOpen? '':'hidden'">
  <div class="modal-content">
    <div class="center title">Todo Create</div>
    <div class="input-con">
      <!-- <label class="reg-label">Name</label> -->
      <input class="reg-input" type="text" required>
      <span class="reg-span"></span>
    </div>
    <div class="btn-con flex-j-spacear mg-t-16">
      <div class="btn large center close"  @click="isModalOpen=false">닫기</div>
      <div class="btn large center register">생성</div>
    </div>
  </div>
</div>
</template>

<style scoped>
.container{
  width: 100vw;
  height:100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: beige;
}
.todo{
  width:40%;
  height: 60%;
  background-color: bisque;
  border-radius: 32px;
  box-sizing: border-box;
  padding:8px;
  display:grid;
  grid-template-rows: 0.1fr 0.1fr 1fr 0.15fr;
}


.todo .title{
  font-size: 2rem;
  height:48px;
  position:sticky;
  top:0;
  display:flex;
  justify-content: center;
  align-items: center;
}

.list{
  box-sizing: border-box;
  margin-top: 8px;
  padding:8px;
  width:100%;
  overflow-y: scroll;
}
    /* 스크롤바의 폭 너비 */
    .list::-webkit-scrollbar {
      width: 5px;
  }

    .list::-webkit-scrollbar-thumb {
        background: rgb(192, 107, 125); /* 스크롤바 색상 */
        border-radius: 10px; /* 스크롤바 둥근 테두리 */
    }

     .list::-webkit-scrollbar-track {
        /* background: rgba(221, 186, 193, 0);  스크롤바 뒷 배경 색상 */
    } 
.list .item{
  width:  100%;
  height: 64px;
  background-color: #a4a8a420;
  margin-bottom: 8px;
  display:flex;
  align-items: center;
  justify-content: space-between;
  padding: 0px 16px;
  box-sizing: border-box;
}
    .list .item.select{
      background-color: #75797540;
    }



    /* 모달 */
.modal-container {
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: #f8d1e8;
  width: 300px;
  height: 150px;
  padding: 15px;
}

.modal-container.hidden {
  display: none;
}

.modalContent .title{
  font-weight: bold;
  font-size: 1.5rem;
}

.reg-input{
  width:100%;
  margin-top: 32px;
}
 .reg-input:focus{
    outline: none;;
 }
</style>
