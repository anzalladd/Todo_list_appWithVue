<template>
<div class="container">
    <img alt="Vue logo" src="../assets/logo.png">
    <div class="todo">
        <!-- Input Todo -->
        <input type="text" class="todo-input" placeholder="Apa yang ingin anda Kerjakan ?" v-model="newTodo" @keyup.enter="addTodo">
        <h3>Hal yang akan kamu kerjakan</h3>
        <!-- Isi Todo List -->
        <transition-group name="fade" enter-active-class="animated fadeInUp"
        leave-active-class="animated fadeOutDown"
        >
        <div class="isi" v-for="(todo,index) in todosFiltered" :key="todo.id">
                <div class="todo-item-left">
                <!-- Todo -->
                <input type="checkbox" v-model="todo.completed">
                <div class="todo-item-label" v-if="!todo.editing" @dblclick="editTodo(todo)" :class="{ completed : todo.completed }">
                    <p>{{todo.title}}</p>
                </div>
                <!-- Edit Todo -->
                <div class="todo-item-edit" v-else>
                    <input type="text" v-model="todo.title" @blur="cancelEdit(todo)" @keyup.enter="doneEdit(todo)" v-focus>
                </div>
            </div>
            <!-- Remove Todo -->
            <div class="remove" @click="removeTodo(index)">
                &times;
            </div>
        </div>
        </transition-group>
        <!-- Extra Container For Item Remaining -->
        <div class="extra-container">
            <div>
                <label>
                    <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All
                </label>
            </div>
            <!-- Item Remaining -->
            <div>
                {{remaining}} items Left
            </div>
        </div>
        <!-- Extra container for filter -->
        <div class="extra-container">
            <div>
                <button :class="{active : filter == 'all'}" @click="filter = 'all'" class="btn">All</button>
                <button :class="{active : filter == 'active'}" @click="filter = 'active'" class="btn">Active</button>
                <button :class="{active : filter == 'completed'}" @click="filter = 'completed'" class="btn">Completed</button>
            </div>
            <div>
                <transition name="fade">
                <button v-if="showClear" class="btn" @click="clearCompleted">Completed</button>
                </transition>
            </div>
        </div>
    </div>
</div>
</template>

<script>
export default {
    name: 'TodoList',
    data() {
        return {
            newTodo: '',
            idForTodo: 3,
            beforeEdit: '',
            filter: 'all',
            todos: [{
                    'id': 1,
                    'title': 'Finish Vue Todo app',
                    'completed': false,
                    'editing': false
                },
                {
                    'id': 2,
                    'title': '100 days coding',
                    'completed': false,
                    'editing': false
                }
            ]
        }
    },
    directives: {
        focus: {
            inserted: function (el) {
                el.focus()
            }
        }
    },
    methods: {
        addTodo() {
            if (this.newTodo.trim().length == 0) {
                return
            }
            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false,
            })

            this.newTodo = ''
            this.idForTodo++
        },
        removeTodo(index) {
            this.todos.splice(index, 1)
        },
        editTodo(todo) {
            if (todo.title.trim().length == 0) {
                todo.title = this.beforeEdit
            }
            this.beforeEdit = todo.title
            todo.editing = true;
        },
        doneEdit(todo) {
            todo.editing = false;
        },
        cancelEdit(todo) {
            todo.title = this.beforeEdit
            todo.editing = false
        },
        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed = event.target.checked)
        },
        clearCompleted(){
            this.todos = this.todos.filter(todo => !todo.completed)
        }

    },
    computed: {
        remaining() {
            return this.todos.filter(todo => !todo.completed).length
        },
        anyRemaining() {
            return this.remaining != 0
        },
        todosFiltered(){
            if(this.filter == 'all')
            {
                return this.todos
            }
            else if(this.filter == 'active'){
                return this.todos.filter(todo => !todo.completed)
            }
            else if(this.filter == 'completed'){
                return this.todos.filter(todo => todo.completed)
            }
        },
        showClear(){
            return this.todos.filter(todo=> todo.completed).length > 0
        }
    }
}
</script>

<style lang="scss" scoped>
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
    * {
        margin: 0;
        padding: 0;
    }

    .container {
        display: flex;
        flex-direction: column;
        width: 100%;
    }

    .container img {
        margin: 0 auto;
    }

    .todo {
        display: flex;
        flex-direction: column;
        margin: 50px auto 0 auto;
    }

    .isi {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 12px;
        animation-duration: 0.3s;
    }

    .todo h3 {
        padding: 30px;
    }

    .isi p {
        font-size: 20px;
    }

    .todo-input {
        width: 1000px;
        padding: 10px;
        font-size: 20px;
        border-radius: 3px;
        outline: none;
        border: 1px solid grey;
    }

    .remove {
        cursor: pointer;
        margin-left: 14px;
        font-size: 30px;
        &:hover {
            color: red;
        }
    }

    .todo-item-left {
        display: flex;
        align-items: center;
    }

    .todo-item-label {
        width: 400px;
        padding: 20px;
        border: 1px solid white;
        margin-left: 12px;
    }

    .todo-item-edit {
        font-size: 24px;
        color: #2c3e50;
        margin-left: 30px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
    }

    .todo-item-edit input {
        color: #2c3e50;
        border: none;
        font-size: 20px;
        &:focus {
            outline: none;
            border: none;
        }
    }

    .completed {
        text-decoration: line-through;
        color: grey;
    }

    .extra-container {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        padding-top: 14px;
        margin-bottom: 14px;
        border-top: 1px solid lightgray;
    }
    .btn{
        border: 1px solid grey;
        background: white;
        font-size: 15px;
        padding: 10px;
        margin-right: 20px;
        border-radius: 4px;
        font-family: Arial, Helvetica, sans-serif;
    }
    .btn:nth-child(1){
        padding: 10px 20px 10px 20px !important;
    }
    .btn:hover{
        transition: .5s;
        background-color:chartreuse;
        border: 1px solid white;
        cursor: pointer;
    }
    .active{
        transition: .5s;
        background-color:chartreuse;
        border: 1px solid white;
    }
    .fade-enter-active, .fade-leave-active{
        transition: opacity .2s;
    }
    .fade-enter, .fade-leave-to{
        opacity: 0;
    }

</style>

