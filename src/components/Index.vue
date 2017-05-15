<template>
  <q-layout>

    <div slot="header" class="toolbar white no-shadow">
      <q-toolbar-title :padding="0" class="text-center">
        My Lists
      </q-toolbar-title>
    </div>
    <q-tabs slot="navigation" class="white no-shadow nav">
      <q-tab route="/asd" exact replace>Lists</q-tab>
      <q-tab route="/layout/alarm" exact replace>All Tasks</q-tab>
    </q-tabs>


    <div class="layout-view">

      <div class="container">

        <div class="todo-list-container">
          <div class="todo-list-inner new-list" v-touch-hold="editList" @click="addNew">
            <div class="todo-list-icon-container">
              <i>add</i>
            </div>
            <div class="todo-list-title text-center">
              NEW LIST
            </div>
          </div>
        </div>

        <div class="todo-list-container" v-for="(list, index) in $root.lists">
          <router-link :to="{ path: '/tasks/' + index }">
            <div class="todo-list-inner" v-touch-hold="editList">
              <div class="todo-list-icon-container">
                <i>timeline</i>
              </div>
              <div class="todo-list-title text-center">
                {{ list.name }}
              </div>
              <div class="todo-list-subtitle text-center">
                {{ countActive(list) }} PENDING
              </div>
            </div>
          </router-link>
        </div>

      </div>

    </div>

  </q-layout>
</template>

<script>
import { Dialog, Toast, LocalStorage, Utils } from 'quasar'
import draggable from 'vuedraggable'

export default {
  components: {
    draggable
  },
  data () {
    return {

    }
  },
  created () {
    if (!LocalStorage.has('lists')) {
      LocalStorage.set('lists', [])
    }
    this.$root.lists = LocalStorage.get.item('lists')
  },
  methods: {
    addNew () {
      var self = this
      Dialog.create({
        title: 'New List',
        form: {
          name: {
            type: 'textbox',
            label: 'List Name (max 25 characters)',
            model: ''
          }
        },
        buttons: [
          'Cancel',
          {
            label: 'Ok',
            handler (data) {
              if (data.name.length > 0) {
                if (data.name.length <= 25) {
                  var list = {
                    name: data.name,
                    tasks: []
                  }
                  self.$root.lists.unshift(list)
                  Toast.create.positive('Your list is was created!')
                  LocalStorage.set('lists', self.$root.lists)
                }
                else {
                  Toast.create.negative('Please use 25 characters or less.')
                }
              }
              else {
                Toast.create.negative('Your list could not be created! Please fill in all fields.')
              }
            }
          }
        ]
      })
    },
    editList (e) {
      console.log(e)
    },
    countActive (list) {
      return Utils.filter('false', {field: 'done', list: list.tasks}).length
    }
  }
}
</script>

<style lang="styl">
* {
  -moz-user-select: none;
   -khtml-user-select: none;
   -webkit-user-select: none;
   -ms-user-select: none;
   user-select: none;
}

.container {
  padding: 0px;
}

.layout-view {
  overflow-x: hidden;
}

.nav {
  color: #1d1d26!important;
}

.toolbar {
  color: #1d1d26!important;
}

.todo-list-container {
  width: 50%;
  float: left;
  box-sizing: border-box;
}

.todo-list-inner {
  background-color: #FFF;
  padding: 5px;
  height: 180px;
  position: relative;
  border: 1px solid #f4f4f4;
}

.todo-list-title {
  width: 100%;
  font-size: 20px;
  color: #1d1d26;
}

.todo-list-subtitle {
  color: #1d1d26;
  font-size: 12px;
  position: absolute;
  width: 100%;
  bottom: 5px;
}

.todo-list-icon-container {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  margin: 0 auto;
  text-align: center;
  line-height: 70px;
  margin-top: 20px;
  margin-bottom: 20px;
  border: 1px solid #f4f4f4;
}

.todo-list-icon-container i {
  font-size: 30px;
  color: #21ba45;
}

.new-list {
  background-color: #21ba45;
}

.new-list .todo-list-icon-container {
  background-color: #FFF;
}

.new-list .todo-list-icon-container i {
  color: #21ba45;
  font-size: 35px;
  font-weight: bold;
}

.new-list .todo-list-title {
  color: #FFF;
  position: absolute;
  bottom: 35px;
  width: 100%;
  text-align: center;
}

.blur {
  filter: blur(2px);
  opacity: 0.4;
}
</style>
