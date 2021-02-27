<template>
  <div class="todo__item">
    <div class="todo__item__left">
      <input type="checkbox" :checked="completed" @change="$emit('on-toggle')">
      <div v-if="!isEditing" @dblclick="startEditing()"       class="todo__item__label" :class="{ completed : completed }"
      >
      {{ title }}
      </div>
      <input v-else type="text" v-model="newTitle" class="todo__item__edit"
        @blur="doneEdit()" @keyup.enter="doneEdit()" 
        @keyup.esc="cancelEdit()" v-focus
      >
    </div>
    <div class="todo__item__remove" @click="$emit('on-delete')">
      &times;
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoItems',
  props: {
    title: {
      type: String,
      required: true,
    },
    completed: {
      type: Boolean,
      required: true,
    }
  },
  data() {
    return {
      // beforeEditCache: '',
      newTitle: '',
      isEditing: false,
    }
  },
  directives: {
    focus: {
      inserted(el) {
        el.focus()
      }
    }
  },
  methods: {
    startEditing() {
      // this.beforeEditCache = this.title
      this.isEditing = true
      this.newTitle = this.title
    },
    doneEdit() {
      if(this.newTitle.trim() == '') {
        this.newTitle = this.beforeEditCache
      }
      this.isEditing = false
      this.$emit("on-edit", this.newTitle)
    },
    cancelEdit() {
      this.newTitle = this.title
      this.doneEdit()
    },
  }
}
</script>

<style>

</style>