<template>
    <div>
        <input type="text" class="todo-input" placeholder="Enter New Todo" v-model="newTodo" @keyup.enter="addTodo" />
        <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
            <Todo v-for="(todo, index) in todosFiltered" :key="todo.id" :todo="todo" :index="index">
            </Todo>
        </transition-group>
        <div class="extra-container">
            <div>
                <label>
                    <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos" />
                    Check All
                </label>
            </div>
            <div>
                {{ remaining }} items left
            </div>
        </div>
        <div class="extra-container">
            <div>
                <button :class="{ active : filter == 'all'}" @click="filter = 'all'">
                    All
                </button>
                 <button :class="{ active : filter == 'active'}" @click="filter = 'active'">
                    Active
                </button>
                 <button :class="{ active : filter == 'completed'}" @click="filter = 'completed'">
                    Completed
                </button>
            </div>
            <div>
                <transition name="fade">
                    <button v-if="showClearCompleted" @click="clearCompleted">
                        Clear Completed
                    </button>
                </transition>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import Vue from 'vue';
import Todo from './Todo.vue'

export default Vue.extend({
  name: 'TodoList',
  components: {
      Todo
  },
  data: () => {
      return {
          newTodo: '', 
          todoId: 2,
          beforeEditCache: '',
          filter: 'all',
          todos: [
              {
                  id: 1,
                  title: 'finish todo app',
                  completed: false,
                  editing: false
              }
            ]
      }
  },
  methods: {
      addTodo(){
          if(this.newTodo.trim().length === 0) return
          this.todos.push({
              id: this.todoId,
              title: this.newTodo,
              completed: false,
              editing: false
          })
          this.newTodo = ''
          this.todoId++
      },
      removeTodo(index: number){
          this.todos.splice(index, 1)
      },
      editTodo(todo: any){
          this.beforeEditCache = todo.title
          todo.editing = true
      },
      doneEdit(todo: any){
          if(todo.title.trim().length === 0){
              todo.title = this.beforeEditCache
          }
          todo.editing = false
      },
      cancelEdit(todo: any){
          todo.title = this.beforeEditCache
          todo.editing = true
      },
      checkAllTodos(event: any){
          this.todos.forEach(todo => todo.completed = event.target.checked)
      },
      clearCompleted(){
          this.todos = this.todos.filter(todo => !todo.completed)
      }
  },
  computed: {
      remaining():number{
          return this.todos.filter(todo => !todo.completed).length
      }, 
      anyRemaining():boolean{
          return this.remaining !== 0
      },
      todosFiltered():any{
          if(this.filter == 'all'){
              return this.todos
          } else if(this.filter == 'active'){
              return this.todos.filter(todo => !todo.completed)
          } else if(this.filter == 'completed'){
              return this.todos.filter(todo => todo.completed)
          }
      },
      showClearCompleted():boolean{
          return this.todos.filter(todo => todo.completed).length > 0
      }
  },
  directives: {
      focus: {
          inserted: (el) => {
              el.focus()
          }
      }
  },
  props: {
  },
});
</script>

<style lang="scss" scoped>
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
  &:focus {
    outline: 0;
  }
}
.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;
}
.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}
.todo-item-left {
  display: flex;
  align-items: center;
}
.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}
.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc; //override defaults
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  &:focus {
    outline: none;
  }
}
.completed {
  text-decoration: line-through;
  color: grey;
}
.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}
button {
  font-size: 14px;
  background-color: white;
  appearance: none;
  &:hover {
    background: lightgreen;
  }
  &:focus {
    outline: none;
  }
}
.active {
  background: lightgreen;
}
// CSS Transitions
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
