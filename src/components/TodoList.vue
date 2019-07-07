
<template>
  <div>
    <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addTodo">
    <div v-for="(todo,index) in todos" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed" :checked="saveTodo()">
        <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-lable" :class="{ completed : todo.completed }">{{ todo.title }}</div>
        <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
      </div>
      <div class="btn-remove" @click="removeTodo(index)">
        &times;
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: '',
      beforeEditCache: '',
      todos: []
    }
  },
  directives: {
    focus: {
      inserted: function(el){
        el.focus()  
      }
    }
  },
  mounted() {
    this.loadTodo();
  },
  methods: {
    loadTodo() {
      if (localStorage.getItem('todos')) this.todos = JSON.parse(localStorage.getItem('todos'));
    },
    saveTodo() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
    },
    addTodo(){
      if(this.newTodo.trim().length == 0 ){
        return;
      }

      this.todos.push({
        id: this.todos.length > 0 ? (this.todos[this.todos.length-1].id + 1) : 0,
        title: this.newTodo,
        completed: false,
        editing: false
      })

      this.newTodo = ''

      this.saveTodo();
    },
    removeTodo(index){
      this.todos.splice(index, 1);
      this.saveTodo();
    },
    editTodo(todo){
      this.beforeEditCache = todo.title;
      todo.editing = true;
      this.saveTodo();
    },
    doneEdit(todo){
      if(todo.title.trim() == '' ){
        alert('할 일을 작성 하시오.');
        return;
      }
      todo.editing = false;
      this.saveTodo();
    },
    cancelEdit(todo){
      todo.title = this.beforeEditCache
      todo.editing = false;
      this.saveTodo();
    }
  }
}
</script>

<style lang="scss">
.todo-input{
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 18px;

  &:focus{
    outline: 0;
  }
}

.todo-item{
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
}
.todo-item-left{
  display: flex;
  align-items: center;
}
.todo-item-lable{
  padding: 10px;
  border: 1px solid white;
}
.todo-item-edit{
  width:100%;
  font-size: 20px;
  padding: 10px;
  color:#2c3e50;
  border:1px solid #ccc;
  
  &:focus{
    outline: 0;
  }
}
.btn-remove{
  cursor: pointer;
  &:hober{
      color: block;
  }
}
.completed{
  text-decoration: line-through;
  color: grey;
}
</style>
