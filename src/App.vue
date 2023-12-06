<script setup> // Composition API 
   import { ref, onMounted, computed, watch } from 'vue'


  const todos = ref([]);
  const name = ref('')
  const input_content = ref("")
  const input_category = ref(null)


  const todos_asc = computed(() => todos.value.sort((a , b ) => {
      return b.createdAt - a.createdAt
    }))

    const addToDo = () => {
      if(input_content.value.trim() === '' || input_content.value === null){
        return
      } 
      todos.value.push({
        content : input_content.value,
        category : input_category.value,
        done : false,
        createdAt: new Date().getTime()
      })

      input_content.value = "",
      input_category.value = ""
  }

  const removeTodo = todo => {
    todos.value = todos.value.filter(t => t!== todo)
  }

  watch(todos, newVal => {
    localStorage.setItem('name' , JSON.stringify(newVal))
  } , {
    deep: true  
    // deep: true is specified to watch for changes deeply within the object,
    //  in case your todos object contains nested objects or arrays.
  } )

  // Watch for changes in a reactive variable
  watch(name, (newVal) => {
    // Code to be executed when myVariable changes
    localStorage.setItem('name' , newVal)
  });

   // Use the onMounted lifecycle hook
   onMounted(() => {
    // Code to be executed when the component is mounted
    name.value = localStorage.getItem(('name') || '')
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  });


</script>
<template>
   <main class="app">
       <section class="greeting">
          <h2 class="title">
            what's up?
          </h2>
       </section>
       <section class="create-todo">
           <h3>CREATE TODO</h3>
           <form @submit.prevent="addToDo">
              <h4>Whats on your to do list?</h4>
              <input type="text" placeholder="e.g make a video" v-model="input_content"/>

              <h4>Pick a category</h4>
              <div class="options">
               <label>
                  <input type="radio" name="category" id="category1" value="business" v-model="input_category"/>
                  <span class="bubble business"></span>
                  <div>business</div>
               </label>
               <label>
                  <input type="radio" name="category" id="category1" value="personal"  v-model="input_category"/>
                  <span class="bubble personal"></span>
                  <div>personal</div>
               </label>
              </div>
              <input type="submit" value="ADD TO-DO"/>
           </form>
       </section>

       <section class="todo-list">
          <h3>TODO LIST</h3>
          <div class="list" id="todo-list">
            <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
               <label>
                   <input type="checkbox" v-model="todo.done"/>
                   <span :class="`bubble ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
               </label>
               <div class="todo-content">
                   <input type="text" v-model="todo.content"/>   
               </div>

               <div class="actions">
                   <button class="delete" @click="removeTodo(todo)">Delete</button>
               </div>
            </div>
          </div>
        </section>

   </main>
</template>
<style scoped>
</style>
