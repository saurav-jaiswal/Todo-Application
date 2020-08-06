<template>
  <q-page padding>
    <div class="typewriter text-center q-mb-lg q-mt-lg" v-if="$q.platform.is.desktop">
      <h1 class="text-bold">
        Todo Application
      </h1>
    </div>
    <div class="typewriter text-center q-mb-lg q-mt-lg" v-if="$q.platform.is.mobile">
      <h4 class="text-bold">
        Todo Application
      </h4>
    </div>
    <q-input outlined borderless dense debounce="300" v-model="filter" placeholder="Search Uncompleted Task"
             class="q-mb-sm">
      <template v-slot:append>
        <q-icon name="search"/>
      </template>
    </q-input>
    <q-banner class="bg-primary text-white" v-if="Object.keys(uncompletedTask).length == 0">
      No, Task to do Today !
      <template v-slot:action>
        <q-btn flat color="white" label="ADD TASK" @click="openAddTaskDialogBox"/>

      </template>
    </q-banner>
    <transition
      appear
      enter-active-class="animated zoomIn"
      leave-active-class="animated zoomOut"
    >
      <div v-if="Object.keys(uncompletedTask).length">
        <q-banner inline-actions class="text-white bg-orange-4 text-center">
          <span class="text-subtitle1 text-bold">TODO</span>
        </q-banner>
        <q-list bordered separator>
          <q-item v-for="(task,uncompletedtask_task_index) in filterUncompletedData"
                  :key="uncompletedtask_task_index" @click="task.selected = !task.selected"
                  :class="!task.selected ? 'bg-white' : 'bg-orange-1 '" v-ripple>
            <q-item-section side top>
              <q-checkbox v-model="task.selected"/>
            </q-item-section>

            <q-item-section>
              <q-item-label :class="{'text-strikethrough' : task.selected}">{{task.task_name}}</q-item-label>
            </q-item-section>
            <q-item-section>
              <q-item-label caption>
                <span>Date : {{task.due_date | niceDate}} </span> <br> <span>Time : {{task.due_time}}</span>
              </q-item-label>
            </q-item-section>

            <q-item-section top side>
              <div class="text-grey-8 q-gutter-xs">
                <q-btn outline icon="delete" color="red" size="xs" padding="sm" class="q-mr-xs"
                       @click="deleteUncompletedTask(task)">
                  <q-tooltip>Delete</q-tooltip>
                </q-btn>
                <q-btn outline icon="edit" color="primary" size="xs" padding="sm" @click="editTask(task)">
                  <q-tooltip>Edit</q-tooltip>
                </q-btn>
              </div>
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </transition>

    <transition
      appear
      enter-active-class="animated zoomIn"
      leave-active-class="animated zoomOut"
    >
      <div v-if="Object.keys(completedTask).length">
        <q-banner inline-actions class="text-white bg-green-4 text-center q-mt-lg">
          <span class="text-subtitle1 text-bold">Completed</span>
        </q-banner>
        <q-list bordered separator :filter="filter">
          <q-item v-for="(task,completedtask_task_index) in completedTask"
                  :key="completedtask_task_index" @click="task.selected = !task.selected"
                  :class="!task.selected ? 'bg-white' : 'bg-green-1'" v-ripple>
            <q-item-section side top>
              <q-checkbox v-model="task.selected"/>
            </q-item-section>

            <q-item-section>
              <q-item-label :class="{'text-strikethrough' : task.selected}">{{task.task_name}}</q-item-label>
            </q-item-section>
            <q-item-section>
              <q-item-label caption>
                <span>Date : {{task.due_date | niceDate}} </span> <br> <span>Time : {{task.due_time}}</span>
              </q-item-label>
            </q-item-section>

            <q-item-section top side>
              <div class="text-grey-8 q-gutter-xs">
                <q-btn outline icon="delete" color="red" size="xs" padding="sm" class="q-mr-xs"
                       @click="deleteCompletedTask(task)">
                  <q-tooltip>Delete</q-tooltip>
                </q-btn>
                <q-btn outline icon="edit" color="primary" size="xs" padding="sm" @click="editTask(task)">
                  <q-tooltip>Edit</q-tooltip>
                </q-btn>
              </div>
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </transition>


    <div class="absolute-bottom text-center q-mb-lg">
<!--      <q-btn icon="add" round color="primary" size="24px" @click="openAddTaskDialogBox"></q-btn>-->
       <q-fab color="purple" icon="keyboard_arrow_up" direction="up">
        <q-fab-action color="primary" @click="openAddTaskDialogBox" icon="add" />
      </q-fab>

    </div>
    <!--Add Task Dialog Box-->
    <q-dialog v-model="addTaskDialogBox" persistent>
      <q-card style="width: 100%">
        <q-card-section class="row">
          <div class="text-h6">Add Task</div>
        </q-card-section>

        <q-card-section>
          <div class="row q-mb-sm">
            <q-input outlined v-model="task.task_name" class="col" :rules="[val => !!val || 'Task Name is required']"
                     ref="name" label="Task Name"/>
          </div>
          <div class=" q-mb-sm">
            <q-input outlined v-model="task.due_date" mask="date" ref="date"
                     :rules="[val => !!val || 'Date is required']" label="Date">
              <template v-slot:append>
                <q-icon name="event" class="cursor-pointer">
                  <q-popup-proxy ref="due_date" transition-show="scale" transition-hide="scale">
                    <q-date v-model="task.due_date" @input="() => $refs.due_date.hide()"/>
                  </q-popup-proxy>
                </q-icon>
              </template>
            </q-input>
          </div>
          <div class=" q-mb-sm">
            <q-input outlined v-model="task.due_time" mask="time" ref="time"
                     :rules="[val => !!val || 'Time is required']" label="Time"
            >
              <template v-slot:append>
                <q-icon name="access_time" class="cursor-pointer">
                  <q-popup-proxy transition-show="scale" transition-hide="scale">
                    <q-time v-model="task.due_time"/>
                  </q-popup-proxy>
                </q-icon>
              </template>
            </q-input>
          </div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn outline label="Save" v-if="!isUpdated" @click="saveTask"
                 :disable="!task.task_name||!task.due_date||!task.due_time" color="primary" v-close-popup/>
          <q-btn outline label="Update" v-if="isUpdated" @click="updateTask"
                 :disable="!task.task_name||!task.due_date||!task.due_time" color="primary" v-close-popup/>
          <q-btn outline label="Cancel" color="negative" @click="clearTask" v-close-popup/>
        </q-card-actions>
      </q-card>
    </q-dialog>
    <!--    End Add Task Dialog Box-->
  </q-page>
</template>

<script>
  import {date} from 'quasar'

  export default {
    data() {
      return {
        addTaskDialogBox: false,
        task: {selected: false},
        task_details: [],
        isUpdated: false,
        filter: ''
      }
    },
    methods: {
      openAddTaskDialogBox() {
        this.addTaskDialogBox = true
      },
      saveTask() {
        this.$refs.name.validate()
        this.$refs.date.validate()
        this.$refs.time.validate()
        this.task_details.push(this.task)
        this.task = {selected: false}
        this.isUpdated = false
      },
      clearTask() {
        this.task = {selected: false}
        this.isUpdated = false
      },
      deleteUncompletedTask(row) {
        this.$q.dialog({
          title: 'Delete',
          message: 'Are You Sure Want to Delete Selected Task?',
          cancel: true,
          ok: 'Delete'
        }).onOk(() => {
          let index = this.task_details.indexOf(row)
          if (index !== -1) {
            this.task_details.splice(index, 1)
          }
        })
      },
      deleteCompletedTask(row) {
        this.$q.dialog({
          title: 'Delete',
          message: 'Are You Sure Want to Delete Selected Task?',
          cancel: true,
          ok: 'Delete'
        }).onOk(() => {
          let index = this.task_details.indexOf(row)
          if (index !== -1) {
            this.task_details.splice(index, 1)
          }
        })
      },

      editTask(row) {
        if (row) {
          this.task = Object.assign({}, row)
          this.task['row'] = row;
          this.addTaskDialogBox = true
          this.isUpdated = true
        }
      },
      updateTask() {
        let index = this.task_details.indexOf(this.task['row']);
        this.$set(this.task_details, index, this.task)
        this.task = {}
        this.$q.notify({
          color: 'primary',
          textColor: 'white',
          icon: 'check',
          message: 'Task Updated Successfully'
        })
        this.isUpdated = false
      },
    },
    filters: {
      niceDate(value) {
        return date.formatDate(value, 'MMM D')
      }
    },
    computed: {
      uncompletedTask() {
        return this.task_details.filter(function (item) {
          return item.selected == false
        })
      },
      completedTask() {
        return this.task_details.filter(function (item) {
          return item.selected == true
        })
      },
      filterUncompletedData() {
        let self = this;
        let temp_search = self.uncompletedTask.filter(function (item) {
          return item.task_name.toLowerCase().match(self.filter.toLowerCase())
        })
        return temp_search
      }
    }
  }
</script>

<style>
  .typewriter h1 {
    color: rgba(138, 35, 135, 0.79);
    display: inline-block;
    font-family: monospace;
    overflow: hidden; /* Ensures the content is not revealed until the animation */
    border-right: .15em solid orange; /* The typwriter cursor */
    white-space: nowrap; /* Keeps the content on a single line */
    margin: 0 auto; /* Gives that scrolling effect as the typing happens */
    letter-spacing: .15em; /* Adjust as needed */
    animation: typing 3.5s steps(30, end),
    blink-caret .5s step-end infinite;
  }
  .typewriter h4 {
    color: rgba(138, 35, 135, 0.79);
    display: inline-block;
    font-family: monospace;
    overflow: hidden; /* Ensures the content is not revealed until the animation */
    border-right: .15em solid orange; /* The typwriter cursor */
    white-space: nowrap; /* Keeps the content on a single line */
    margin: 0 auto; /* Gives that scrolling effect as the typing happens */
    letter-spacing: .15em; /* Adjust as needed */
    animation: typing 3.5s steps(30, end),
    blink-caret .5s step-end infinite;
  }

  /* The typing effect */
  @keyframes typing {
    from {
      width: 0
    }
    to {
      width: 100%
    }
  }

  /* The typewriter cursor effect */
  @keyframes blink-caret {
    from, to {
      border-color: transparent
    }
    50% {
      border-color: orange
    }
  }
</style>
