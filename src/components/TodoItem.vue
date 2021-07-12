<template>
    <div class="todo-item">
        <div class="todo-item-left">
            <input 
                type="checkbox"
                class="todo-completed-checkbox"
                v-model="completed"
                @change="doneEdit"
            >
            <div
                :class="[{completed: completed}, 'todo-item-label']"
                v-if="!editing"
                @dblclick="editTodo"
            >
                {{ title }}
            </div>
            <input 
                type="text"
                class="todo-item-edit"
                v-else
                v-model="title"
                @keyup.enter="doneEdit"
                @blur="doneEdit"
                @keyup.esc="cancelEdit"
                v-focus
            >
        </div>
        <div
            class="remove-item"
            @click="removeTodo(index)"
        >
            &times;
        </div>
    </div>
</template>

<script>
export default {
    name: 'todo-item',
    props: {
        todo: {
            type: Object,
            required: true,
        },
        index: {
            type: Number,
            required: true,
        },
        checkAll: {
            type: Boolean,
            required: true,
        }
    },
    data() {
        return {
            'id': this.todo.id,
            'title': this.todo.title,
            'completed': this.todo.completed,
            'editing': this.todo.editing,
            'beforeEditCache': '',
        }
    },
    watch: {
        checkAll() {
            this.completed = this.checkAll ? true : this.todo.completed
        },
    },
    methods: {
        removeTodo(index) {
            this.$emit('removeTodo', index)
        },
        editTodo() {
            this.beforeEditCache = this.title
            this.editing = true
        },
        doneEdit() {
            if (this.title.trim() === '') {
                this.title = this.beforeEditCache
            }
            this.editing = false
            this.beforeEditCache = ''
            this.$emit('doneEdit', {
               'index': this.index,
               'todo': {
                   'id': this.id,
                   'title': this.title,
                   'completed': this.completed,
                   'editing': this.editing, 
               }
            })
        },
        cancelEdit() {
            this.title = this.beforeEditCache
            this.editing = false
            this.beforeEditCache = ''
        },
    },
    directives: {
        focus: {
           inserted: function (el) {
               el.focus()
           }
        }
    // the difference between vue2 and vue3 
    // 'todo-focus': function (el, binding) {
    //   if (binding.value) {
    //     el.focus();
    //   }
    // }
    // 'todo-focus': function (el) {
    //     el.focus();
    // }
  },
}
</script>

<style lang="scss">

</style>