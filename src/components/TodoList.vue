<template>
    <div>
    <input type="text" class ="todo-input" placeholder = " Add tasks to be done here "
    v-model="newTodo" @keyup.enter="addTodo()">

        <div v-for ="(todo,index) in todosFiltered" :key="todo.id" class ="todo-item">
            
            <div class="todo-item-left">
                <input type="checkbox" v-model ="todo.completed" class = "completed">
                <div v-if="!todo.editing" class="todo-item-label" :class="{ completed :todo.completed }" @dblclick="editTodo(todo)">
                    {{todo.title}}
                </div>

                <input type="text" v-focus v-model="todo.title" v-else class="todo-item-edit" 
                @keyup.enter="doneEdit(todo)" @blur="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
                
                
            </div>
            <div class="remove-item" @click="removeTodo(index)">
                &times;
            </div>
            
        </div>

    <div class="extra-container">
        <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos()">Check All</label></div>
        <div>{{remaining}} items left</div>

    </div>
    
    <div class="extra-container">
        <div>
            <button :class="{ active :filter == 'all'}" @click = "filter = 'all'">All</button>
            <button :class="{ active :filter == 'active'}" @click = "filter = 'active'">Active</button>
            <button :class="{ active :filter == 'completed'}" @click = "filter = 'completed'">Completed</button>
        </div>
        <div><button v-if="showClearCompletedButton" @click="clearCompleted()">Clear Completed</button></div>
    </div>

    </div>

    

</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
        newTodo:'',
        idForTodo: 3,
        beforeEditCache : '',
        filter : 'all',
        todos:[
            {
                'id': 1 ,
                'title' : "make vue todos app",
                'completed' : false,
                'editing' :false,
            },
            {
                'id': 2 ,
                'title' : "take over the world",
                'completed' : false,
                'editing' :false,
            },
        ]
    }
  },
  computed :{

      remaining(){
          return this.todos.filter(todo =>!todo.completed).length
      },

      anyRemaining(){
          return this.remaining != 0
      },

      todosFiltered(){
          if (this.filter == 'all'){
              return this.todos

          }
          else if (this.filter == 'active'){
              return this.todos.filter(todo => !todo.completed)
          }
          else if (this.filter == 'completed'){
              return this.todos.filter(todo => todo.completed)
          }
      },
      showClearCompletedButton(){

          if (this.todos.filter(todo => todo.completed).length>0){
              return true
          }
          return false
      }

  },
  methods:{
      addTodo(){
          if (this.newTodo.trim() == ""){
              return
          }
          this.todos.push({
              id : this.idForTodo,
              title : this.newTodo,
              completed : false,
          })
          this.newTodo = ''
          this.idForTodo++
      },

      removeTodo(index){
          this.todos.splice(index,1);
      },

      editTodo(todo){
          this.beforeEditCache = todo.title
          todo.editing = true
      },

      doneEdit(todo){
          if (todo.title.trim() == ""){
              this.cancelEdit(todo)
          }
          todo.editing = false
      },

      cancelEdit(todo){
          todo.title = this.beforeEditCache
          todo.editing = false
      },

      checkAllTodos(){
          this.todos.forEach((todo) => todo.completed = event.target.checked)
      },
      clearCompleted(){
          this.todos = this.todos.filter(todo => !todo.completed)
      }
},

directives:{

    focus:{
        inserted : function(el){
            el.focus()
        }
    }
}

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">

    .todo-input{
        width: 300px;
        padding: 10px 18px;
        font-size: 18px;
        margin-bottom: 16px;
        &:focus{
            outline : 0;
        }
    }

    ::-webkit-input-placeholder{
        text-align: center;
    }

    .todo-item{

        margin-bottom: 12px;
        display :flex;
        align-items: center;
        justify-content: space-between;
    }

    .remove-item{
        cursor: pointer;
        margin-left: 14px;
        &:hover{
            color: black;
        }
    }

    .todo-item-left{
        display :flex;
        align-items: center;
    }

    .todo-item-label{
        padding : 10px;
        border : 1px solid white;
        margin-left: 12px;
    }

    .todo-item-edit{
        font-size: 24px;
        color : #2c3e50;
        margin-left: 12px;
        width : 100%;
        padding : 10px;
        border : 1px solid #ccc;
        font-family: 'Avenir',Arial, Helvetica, sans-serif;

        &:focus{
            outline:none;
        }
    }

    .completed{
        text-decoration: line-through;
        color: grey;
    }

    .extra-container{
        display:flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgrey;
        padding-top: 14px;
        margin-bottom:14px;
    }

    button{
        font-size: 14px;
        background-color: white;
        appearance: none;

        &:hover{
            background: lightgreen;
        }

        &:focus{
            outline:none;
        }

        .active{
            background: green;
        }
    }

</style>
