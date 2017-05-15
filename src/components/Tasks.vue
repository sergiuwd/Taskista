<template>
  <q-layout>

    <div slot="header" class="toolbar white">
      <button v-link="{ path: '/' }">
        <i>keyboard_arrow_left</i>
      </button>
      <q-toolbar-title :padding="0" class="text-center">
        {{ list.name }}
      </q-toolbar-title>
      <button @click="listActions()">
        <i>settings</i>
      </button>
    </div>

    <div class="layout-view">

      <div class="container">

        <draggable v-model="list.tasks" @end="onMoveCallback" v-if="list.tasks.length > 0">
          <div v-bind:class="['task-container', {'done': task.done }]" class="task-container" v-for="task in list.tasks">
            <div class="task-inner">
              <div class="row small-gutter">
                <div class="checkbox-cont">
                  <q-checkbox class="positive" v-model="task.done" v-on:click.native="updateRoot"></q-checkbox>
                </div>
                <div class="title-cont auto" v-bind:class="{taskDone: task.done}">
                  {{ task.title }}
                </div>
                <div class="remove-cont width-1of5" @click="removeTask(task)">
                  <i>delete_forever</i>
                </div>
              </div>
            </div>
          </div>
        </draggable>


        <div v-else>
          <p class="no-tasks">You have no tasks on this list!</p>
          <p class="no-tasks-suggestion">Let's add some!</p>
          <p class="no-tasks">Click the <span class="text-positive">+ (plus)</span> button on the bottom right corner and fill in the fields.</p>
        </div>

      </div>

      <button class="positive fixed-bottom-right generic-margin circular shadow-2" @click="addTask">
        <i style="font-weight: bold;">add</i>
      </button>

    </div>

  </q-layout>
</template>

<script>
import { Dialog, Toast, LocalStorage, ActionSheet } from 'quasar'
import draggable from 'vuedraggable'
export default {
  props: [
    'id'
  ],
  components: {
    draggable
  },
  data () {
    return {
      list: {}
    }
  },
  created () {
    this.list = this.$root.lists[this.id]
  },
  methods: {
    addTask () {
      var self = this
      Dialog.create({
        title: 'New Task',
        form: {
          name: {
            type: 'textbox',
            label: 'Task Description',
            model: ''
          }
        },
        buttons: [
          'Cancel',
          {
            label: 'Ok',
            handler (data) {
              if (data.name.length > 0) {
                var task = {
                  title: data.name,
                  done: false
                }
                self.list.tasks.unshift(task)
                self.$root.lists[self.id] = self.list
                LocalStorage.set('lists', self.$root.lists)
              }
              else {
                Toast.create.negative('The task could not be created! Please add a task description!')
              }
            }
          }
        ]
      })
    },
    removeTask (task) {
      var self = this
      Dialog.create({
        title: 'Are you sure?',
        message: 'Are you sure you want to delete this task?',
        buttons: [
          {
            label: 'Disagree'
          },
          {
            label: 'Agree',
            handler () {
              var index = self.list.tasks.indexOf(task)
              self.list.tasks.splice(index, 1)
              LocalStorage.set('lists', self.$root.lists)
            }
          }
        ]
      })
    },
    toHome () {
      console.log('change me')
    },
    updateRoot () {
      var self = this
      self.$root.lists[self.id] = self.list
      LocalStorage.set('lists', self.$root.lists)
    },
    listActions () {
      var self = this
      ActionSheet.create({
        actions: [
          {
            label: 'Edit List',
            // Choose one of the following two:
            icon: 'edit', // specify ONLY IF using icon
            handler () {
              var oldValue = self.list.name
              Dialog.create({
                title: 'Edit List',
                message: 'Max 25 characters',
                form: {
                  name: {
                    type: 'textbox',
                    label: 'List Title',
                    model: oldValue
                  }
                },
                buttons: [
                  'Cancel',
                  {
                    label: 'Ok',
                    handler (data) {
                      if (data.name.length > 0) {
                        if (data.name.length <= 25) {
                          self.list.name = data.name
                          self.$root.lists[self.id] = self.list
                          LocalStorage.set('lists', self.$root.lists)
                          Toast.create.positive('Your changes have been saved!')
                        }
                        else {
                          Toast.create.negative('Please use 25 characters or less.')
                        }
                      }
                      else {
                        Toast.create.negative('The task could not be created! Please add a task description!')
                      }
                    }
                  }
                ]
              })
            }
          },
          {
            label: 'Delete Story',
            // Choose one of the following two:
            icon: 'delete', // specify ONLY IF using icon
            handler () {
              Dialog.create({
                title: 'Are you sure?',
                message: 'Are you sure you want to delete this story?',
                buttons: [
                  {
                    label: 'Disagree'
                  },
                  {
                    label: 'Agree',
                    handler () {
                      var index = self.$root.lists.indexOf(self.list)
                      self.$root.lists.splice(index, 1)
                      LocalStorage.set('lists', self.$root.lists)
                      self.$router.push('/')
                    }
                  }
                ]
              })
            }
          }
        ]
      })
    },
    onMoveCallback (evt, originalEvent) {
      this.updateRoot()
    }
  }
}
</script>

<style lang="styl">

div {
  padding: 0px;
  margin: 0px;
}

* {
  -moz-user-select: none;
   -khtml-user-select: none;
   -webkit-user-select: none;
   -ms-user-select: none;
   user-select: none;
}

.no-tasks {
  color: #1d1d26;
  text-align: center;
  padding-left: 10px;
  padding-right: 10px;
}

.no-tasks-suggestion {
  color: #1d1d26;
  text-align: center;
  font-weight: 600;
  font-size: 25px;
}

.task-container {
  border-top: 1px solid #f3f3f4;
  box-sizing: border-box;
  padding-bottom: 5px;
  padding-top: 5px;
  transition: .1s;
}

.task-inner {
  padding: 20px;
  padding-bottom: 10px;
  padding-top: 15px;
  border-radius: 4px;
  display: table;
  width: 100%;
  position: relative;
}

.taskDone {
  text-decoration:line-through;
}

.checkbox-cont {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px!important;
  text-align: center;
}

.title-cont {
  padding-left: 20px!important;
  display: table-cell;
  vertical-align: middle;
  font-weight: 500;
  font-size: 15px;
}

.remove-cont {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px!important;
  float: right;
}

.remove-cont i {
  font-size: 20px;
  color: #f96565;
}

.done {
  background-color: #f8f8f9;
  border-left: 2px solid #21ba45;
  transition: .1s
}

.list-title {
  text-align: center;
  font-size: 20px;
  color: #dedddd;
  margin: 10px;
}

</style>
