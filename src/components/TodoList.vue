<template>
  <v-container class="fill-height">
    <v-responsive
      class="align-centerfill-height mx-auto"
      max-width="900"
    >
    <h1> TODO LIST TEST TASK </h1>
    <EditTaskComponent @addNewTask="addNewTask"></EditTaskComponent>
    <v-skeleton-loader v-if="allTodos.length < 1" type="card"></v-skeleton-loader>
      <v-row v-else>
        <v-col cols="12" v-for="task in allTodos" :key="task.id">
          <Task
            :title="task.title"
            @removeTask="removeTask"
            @changeStatus="changeStatus"
            :taskID="task.id"
            :completed="task.completed"
          />
        </v-col>  
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script>
import axios from 'axios';
import Task from './Task.vue';
import EditTaskComponent from './EditTaskComponent.vue';
import * as api from '@/api/api';
export default {
  data() {
    return {
      allTodos: [],
      allUsers: []
    }
  },
  components: {
    EditTaskComponent,
    Task
  },
  async mounted() {

    await this.getAllTodos()
  },
  methods: {
    async getAllTodos() {
      try {
      const response = await axios.get( api.apiLink + '/todos')
      console.log(response)
      this.allTodos = response.data
      } catch(e) {
        console.error(e)
      }
    },
    async removeTask(taskID){
      try{
        const response = await axios.delete(api.apiLink + '/todos/'+ taskID)
        if(response.status === 200){
          this.allTodos = this.allTodos.filter((task) => task.id !== taskID)
        } else{
          throw Error('Error on server side')
        }
      } catch (e) {
        console.error(e)
      }
    },
    addNewTask(task){
      this.allTodos.unshift(task)
    },
    async changeStatus(taskID, newStatus){
      try {
      const response = await axios.patch(api.apiLink + '/todos/'+ taskID,
        {completed: newStatus}
      )
        if(response.status === 200){
          const taskIndex = this.allTodos.findIndex(task => task.id == taskID);
          this.allTodos[taskIndex].completed = newStatus
        } else{
          throw Error('Error on server side')
        }
    } catch (e) {
      console.error(e)
    }
  },
  },
}
</script>
