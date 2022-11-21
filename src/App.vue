<script setup>
import{ref,onMounted,computed,watch}from 'vue'

const todos =ref([])
const name =ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))




watch(todos, newVal=>{
  localStorage.setItem('todos',JSON.stringify(newVal))
},{deep:true})


  watch(name,(newVal)=>{
    localStorage.setItem('name', newVal)
  })

const removeTodo = (todo) =>{
  todos.value = todos.value.filter((t) => t !== todo)
}

const addTodo = ()=>{
  if(input_content.value.trim()===''||input_category.value===null){
    return
  }
    todos.value.push({
      content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
    })
}


  onMounted(()=>{
    name.value = localStorage.getItem('name')||""
    todos.value = JSON.parse(localStorage.getItem('todos'))||'[]'
  })



</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        今天想做啥,這位先生/小姐 <input type="text"  v-model="name"/>
      </h2>
    </section>
    <section class="create-todo">
      <h3>建立待辦清單</h3>
      <form @submit.prevent="addTodo">
        <h4>甚麼在你的待辦清單上？</h4>
      <input 
            type="text" 
            placeholder="例子:寫Vue作業" 
            v-model="input_content"/>

      <h4>選擇分類*</h4>
      <div class="options">
        <label>
          <input 
            type="radio" 
            name="category" 
            id="category1" 
            value="上課"
            v-model="input_category">
            <span class="bubble business"></span>
            <div>上課</div>
        </label>
        <label>
          <input 
            type="radio" 
            name="category" 
            id="category1" 
            value="寫作業"
            v-model="input_category">
            <span class="bubble personal"></span>
            <div>寫作業</div>
        </label>
      </div>
      <input type="submit" value="增加待辦事項">


    </form>
    </section>
    <section class="todo-list">
			<h3>待辦事項</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
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

