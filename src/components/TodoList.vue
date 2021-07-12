<template>
    <div>
        <input 
            type="text" 
            class="todo-input"
            placeholder="What needs to be done"
            v-model="newTodo"
            @keyup.enter="addTodo"
        >
        <transition-group 
            enter-active-class="animate__animated animate__fadeInUp" 
            leave-active-class="animate__animated animate__fadeOutDown"
        >
            <todo-item
                v-for="(todo, index) in todosFiltered"
                :key="todo.id"
                :todo="todo"
                :index="index"
                :checkAll="!anyRemaining"
                @removeTodo="removeTodo"
                @doneEdit="doneEdit"
            >
            </todo-item>
        </transition-group>
        <div class="extra-container">
            <div>
                <label>
                    <input 
                        type="checkbox" 
                        :checked="!anyRemaining"
                        @change="checkAllTodos"
                    > Check All
                </label>
            </div>
            <div>{{ remaining }} items left</div>
        </div>
        <div class="extra-container">
            <div>
                <button :class="[{ active: filter === 'all' }, 'mr-10']" @click="filter = 'all'">
                    All
                </button>
                <button :class="[{ active: filter === 'active' }, 'mr-10']" @click="filter = 'active'">
                    Active
                </button>
                <button :class="{ active: filter === 'completed' }" @click="filter = 'completed'">
                    Completed
                </button>
            </div>

            <div>
                <transition name="fade">
                    <button 
                        v-if="showClearCompletedButton" 
                        @click="clearCompleted"
                    >
                        Clear Completed
                    </button>
                </transition>
            </div>
        </div>
    </div>
</template>

<script>
import TodoItem from './TodoItem'

export default {
    name: 'todo-list',
    components: {
        TodoItem,
    },
    data() {
        return {
            newTodo: '',
            idForNewTodo: 3,
            beforeEditCache: '',
            filter: 'all',
            todos: [
                {
                    'id': 1,
                    'title': 'Finish Vue Screencast',
                    'completed': false,
                    'editing': false,
                },
                {
                    'id': 2,
                    'title': 'Take over world',
                    'completed': false,
                    'editing': false,
                }
            ],
        }
    },
    computed: {
        todosFiltered() {
            if (this.filter === 'all') {
                return this.todos
            } else if (this.filter === 'active') {
                return this.todos.filter(todo => !todo.completed)
            } else if (this.filter === 'completed') {
                return this.todos.filter(todo => todo.completed)
            }
            return this.todos
        },
        remaining() {
            return this.todos.filter(todo => !todo.completed).length
        },
        anyRemaining() {
            return this.remaining !== 0
        },
        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length > 0
        },
    },
    methods: {
        addTodo() {
            if (this.newTodo.trim() === '') {
                this.newTodo = ''
                return
            }
            this.todos.push({
                id: this.idForNewTodo,
                title: this.newTodo,
                completed: this.completed,
            })
            this.idForNewTodo++
            this.newTodo = ''
        },
        doneEdit(data) {
            this.todos.splice(data.index, 1, data.todo)
        },
        removeTodo(index) {
            this.todos.splice(index, 1)
        },
        checkAllTodos(event) {
            this.todos.forEach(todo => todo.completed = event.target.checked)
        },
        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed)
        },
    },
}
</script>

<style lang="scss">
.mr-10 {
  margin-right: 10px;
}
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
    padding-right: 5px;
    padding-left: 5px;
    font-weight: bold;
    &:hover {
      color: red;
    }
}
.todo-item-left {
    width: 90%;
    display: flex;
    align-items: center;
}
.todo-completed-checkbox {
    margin-right: 10px;
}
.todo-item-label {
    width: 90%;
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
    cursor: pointer;
}
.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    // margin-left: 12px;
    width: 100%;
    padding: 10px 18px;
    // border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    &:focus {
      outline: 0;
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
.fade-enter-active {
    transition: opacity 1s;
}
.fade-leave-active {
    transition: opacity 0.5s;
}
.fade-leave-to, .fade-enter {
    opacity: 0;
}
</style>