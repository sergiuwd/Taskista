<template>
  <div class="expand-wrap shadow-1" v-bind:class="['expand-wrap', 'shadow-1', { 'expanded': formExpanded }]">
    <div role="button" class="expand-btn" @click="toggleForm">+</div>
    <div class="content">
      <h1 class="form-title">Add New Task</h1>
      <p class="caption">I need to...</p>
      <input v-model="newTask.title" placeholder="... do what?">
      <button class="form-button positive" @click="addTask"><i class="on-right">done</i> Add Task</button>
    </div>
  </div>
</template>

<script>
import { Toast } from 'quasar'

export default {
  props: [
    'list'
  ],
  data () {
    return {
      formExpanded: false,
      newTask: {
        title: '',
        done: false
      }
    }
  },
  methods: {
    toggleForm () {
      if (this.formExpanded) {
        this.formExpanded = false
        this.resetTask()
      }
      else {
        this.formExpanded = true
      }
    },
    addTask () {
      var self = this
      if(self.newTask.title.length > 0) {
        if(self.newTask.title.length <= 25) {
          self.list.tasks.push(self.newTask)
          self.toggleForm()
          self.resetTask()
        }
        else {
          Toast.create.negative('The task title must have no more than 25 characters.')
        }
      }
      else {
        Toast.create.negative('You must give your task a title!')
      }
    },
    resetTask () {
      this.newTask = {
        title: '',
        done: false
      }
    }
  }
}
</script>

<style>

input {
  text-align: center;
  font-size: 20px;
  width: 90%;
}

input::placeholder {
  color: #CCC;
  font-style: italic;
}

.expand-wrap {
  position: fixed;
  right: 20px;
  bottom: 20px;
  width: 3.5rem;
  height: 3.5rem;
  margin-top: 30px;
  border-radius: 2.5rem;
  background: #21ba45;
  color: white;
  transition: .4s;
  white-space: nowrap;
  overflow: hidden;
  z-index: 99;
  font-family: 'Montserrat', sans-serif;
}
.expand-wrap.expanded {
  width: 100%;
  height: 100%;
  border-radius: 3px;
  background-color: #FFF;
  color: #CCC;
}
.expand-wrap:hover {
  will-change: width, border-radius;
}
.expand-btn {
  display: inline-block;
  width: 3.5rem;
  height: 3.5rem;
  line-height: 3.5rem;
  text-align: center;
  border-radius: inherit;
  font-weight: bold;
  font-size: 2.5em;
  cursor: pointer;
  vertical-align: middle;
  transition: .2s;
}
.expand-btn:hover {
  will-change: transform;
}
.expanded {
  position: fixed;
  bottom: 0px;
  right: 0px;
  width: 100%;
  height: 100%;
  border-radius: 0px!important;
}
.expanded .expand-btn {
  transform: rotate(-45deg);
}
.expand-text {
  display: inline-block;
  visibility: hidden;
  transition: visibility .4s;
}
.expand-wrap:hover .expand-text {
  will-change: visibility;
}
.expand-wrap.expanded .expand-text {
  visibility: visible;
}
.content {
  transition: .2s;
  display: none;
}
.expanded .content {
  display: block;
  transition: .2s;
}

.content {
  text-align: center;
}

.form-title {
  font-size: 35px;
}

.form-button {
  position: absolute;
  bottom: 0px;
  left: 0px;
  width: 100%;
  height: 50px;
  text-align: center;
  transition: .2s;
}


</style>
