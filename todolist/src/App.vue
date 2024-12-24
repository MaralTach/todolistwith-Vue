<script setup>
import {ref, onMounted, computed,watch} from 'vue'

const todos = ref([])  //todos: Başlangıçta boş bir dizi. İçerisine öğeler eklenip çıkarıldıkça Vue DOM'u günceller.

const name = ref('')     //name: Kullanıcının adını saklayan reaktif bir string. Değeri değiştiğinde Vue otomatik olarak ilgili DOM'u günceller.

const input_content = ref('')     //input_content: Kullanıcının giriş yapmayı sağlayan reaktif bir string. Değeri değiştiğinde Vue otomatik olarak ilgili DOM'u günceller.

const input_category =ref(null) // yeni input icin kategory secer

const todos_asc = computed(() => todos.value.sort((a,b) => {  // cislosyna gore yazar. taze bir zat gosulanda in ustde bolar
  return b.createdAt - a.createdAt
}))

const addTodo = () => { // ulanijynyn yazdygy content bosh bolsa ve ve kategoriya saylamadyk bolsa todolari goshulmaz 
  if (input_content.value.trim() === '' || input_category.value === null){
    return 
  }

  todos.value.push({ // ulanicinin girdesi yazdyrylsa todos listeye gosular. 
    content: input_content.value,  // ulanyjynyn yazadygy content
    category: input_category.value,  // ulanyjynyn yazadygy kategori
    done: false,  
    createdAt: new Date().getTime()  // ulanyjynyn yazdygy wagt
  })
  input_content.value = ''   //taze todo gosulandan sonra input_content bos bolsun
  input_category.value = null   //saylanan kategoriya bos bolsun
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos,newVal => {
  localStorage.setItem("todos",JSON.stringify(newVal))
}, {deep: true})


watch(name,(newVal)=>{
localStorage.setItem('name', newVal)
})

onMounted(()=>{
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem("todos")) || []
})

</script>

<template>

 <main class="app">

    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create A ToDo</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo?</h4>
        <input 
        type="text" 
        placeholder="e.g. make a video" 
        v-model="input_content"/>

       <h4>Pick a category</h4>

       <div class="options">

        <label>
          <input 
          type="radio" 
          name="category" 
          value="business"
          v-model="input_category"/>
          <span class="bubble business"></span>
          <div>Business</div>
        </label>

        <label>
          <input 
          type="radio" 
          name="category"
          value="personel"
          v-model="input_category"/>
          <span class="bubble personal"></span>
          <div>Personel</div>
        </label>

    

       </div>

       <input type="submit" value="Add todo">

      </form>
    </section>

    <section class="todo-list">

      <h3>ToDo List</h3>
      <div class="list">
          <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

            <label>              
              <input type="checkbox" v-model="todo.done"/>
              <span :class="`bubble ${todo.category }`"></span>
            </label>


            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>

          </div>
        </div>
    </section>


 </main>
</template>

