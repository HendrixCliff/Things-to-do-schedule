<template>
        <h1> Things To do  </h1>
 <div>
  <div>
    <input v-model="newLead" @keyup.enter="addTodo"
    placeholder="Enter new schedules" class="input-field"/>
    <ul >
       <li v-for="todo in filteredTodos" :key="todo.id" class="todo-items">
            <span>{{ todo.text }}</span>
            <button @click="editTodo(todo)" class="edit">Edit</button>
            <button @click="deleteTodo(todo)" class="delete">Delete</button>
          
          </li>
    </ul>
    <button @click="eraseAllTodos" class="clear-all">Erase All</button>
    <button @click="eraseCompletedTodos" class="clear-completed">Erase Completed</button>
  </div>
  <div>
    <label for="filter-status">Filter by status:</label>
      <select id="filter-staus" v-model="filterState">
        <option value="all">All</option>
        <option value="Accomplished">Accomplished</option>
        <option value="Unaccomplished">Unaccomplished</option>
      </select>
  </div>
    <div>
      <label for="filter-text"> Filter by word match: 
      </label>
        <input id="filter-text" type="text" 
          v-model="filterWords" class="last-input"/>
   </div>
</div>
</template>



<script>
import { ref , computed, watch } from "vue"

export default {
  
   setup() {
     const newLead = ref("");
     const todos = ref([]);
    const filterState= ref("all");
     const filterWords = ref("");
     const filteredTodos = computed(() => {
        return  todos.value.filter((todo) => {
          const isFilteredByState= filterState.value === "all" 
          || filterState.value === todo.done;
          const isFilteredByText = !filterWords.value || todo.text.includes( filterWords.value);
          return isFilteredByState && isFilteredByText;
         
        });
     });
 
      const addTodo = () =>  {
        if (!newLead.value.trim()) 
        return;

         todos.value.push({
          id: Date.now(),
          text: newLead.value.trim(),
          done: false,
        });
        newLead.value = ""; 
      }
    


        const deleteTodo = (todo) => {
          const index = todos.value.indexOf(todo);
           if (index !== -1) {
            todos.value.splice(index, 1);
           }
        };
         const editTodo = (todo) => {
          const newText = prompt("Enter new todo text:", todo.text);
          if (newText) {
            todo.text = newText;
            }
         };

         const toggleTodoState = (todo) => {
         todo.done = !todo.done;
        
       }
       const eraseAllTodos= () => {
        todos.value = [];     
        };
        const eraseCompletedTodos = () => {
          todos.value = todos.value.filter((todo) => !todo.done);
        };
        watch(
          todos, () => {
            localStorage.setItem("todos", JSON.stringify(todos.value));
          },
          {deep: true }
        );
        const storageFactory = localStorage.getItem("todos");
        if (storageFactory) {
          todos.value = JSON.parse(storageFactory);  
      }
        

        return {
          newLead,
          todos,
          filterState,
          filterWords,
          filteredTodos,
          addTodo,
          deleteTodo,
          editTodo,
          toggleTodoState,
          eraseAllTodos,
          eraseCompletedTodos,
        };
     },
};



</script>


<style scoped>
.read-the-docs {
  color: #888;
}
body {
    background: #ADA996;  /* fallback for old browsers */
    background: -webkit-linear-gradient(to right, #EAEAEA, #DBDBDB, #F2F2F2, #ADA996);  /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(to right, #EAEAEA, #DBDBDB, #F2F2F2, #ADA996); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */

}
.input-field {
    font-size: 2rem;
    background: #0d1821;
    color: #ffffff;
}
button {
    margin-left: .5em
}
h1 {
    font-size: 4rem;
    font-weight: bolder;
    letter-spacing: .002;
    line-height: 2;
    background: #372549;
    color: #ffffff;
    box-shadow: inset;
    width: 100%;
    padding: .2em
}
span {
    margin-right: 3em;
    font-weight: bolder;
}
li {
    list-style: none;
}
.todo-items {
    font-size: 1.2rem;
    background: #bc9ec1;
    color: #000000;
    margin-top: .7em;
    padding: .4em;
}
.delete {
    background: #c3423f
}
.edit {
  background: #fde74c;
}
.clear-all {
    background: #881600;
    color: #ffffff
}
.clear-completed {
    background: #c16200;
    color: #ffffff
}
label {
    font-size: 1.1rem;
    font-weight: bold;
}
select {
    font-size: 1.1rem;
    font-weight: bold; 
    background: #1b2021;
    color: #ffffff;
}
.last-input {
    font-size: 1.1rem;
    background: #480355;
    color: #ffffff;
}
</style>
