<template>
    <v-dialog max-width="800">
  <template v-slot:activator="{ props: activatorProps }">
    <v-btn
      v-bind="activatorProps"
      color="primary"
      text="Create Task"
      variant="flat"
      class="my-2"
    ></v-btn>
  </template>

  <template ref="formDialog" v-slot:default="{ isActive }">
    <v-card title="CREATE NEW TASK">
      <v-card-text>
        SEND TASK TITLE AND TASK AUTHOR
      </v-card-text>
      <v-sheet class="mx-auto" width="300">
  
  <v-form ref="form">
    <v-text-field
      v-model="title"
      :rules="titleRules"
      label="Task title"
      required
    ></v-text-field>

    <v-select
      v-model="select"
      :items="authors"
      :rules="[v => !!v || 'Item is required']"
      label="Author"
      required
    ></v-select>


    <div class="d-flex flex-column">

      <v-btn
        class="mt-4"
        color="error"
        block
        @click="reset"
      >
        Reset
      </v-btn>
    </div>
  </v-form>
</v-sheet>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
          text="Create"
          color="green"
          @click="createTask(isActive)"
        ></v-btn>
        <v-btn
          text="Close"
          @click="isActive.value = false"
        ></v-btn>
      </v-card-actions>
    </v-card>
  </template>
</v-dialog>
  </template>
  <script>
  import * as api from '@/api/api';
import axios from 'axios';
    export default {
       async mounted(){
        await this.getAllUsers()
        console.log("authors: ",this.authors)
      },
      data: () => ({
        authors: [],
        title: '',
        titleRules: [
          v => !!v || 'title is required',
        ],
        select: null,
        checkbox: false,
      }),
  
      methods: {
        async getAllUsers() {
          try {
          const response = await axios.get(api.apiLink + '/users')
          console.log("USERS: ", response.data)
          this.authors = response.data.map(author => author.id)
          } catch(e) {
            console.error(e)
          }
        },
        async validate () {
          const { valid } = await this.$refs.form.validate()
  
          if (valid) { return true }
          else { return false } 
        },
        reset () {
          this.$refs.form.reset()
        },
        async createTask(isActive){
          const formValidation = await this.validate()
          if(formValidation){
              try{
              // primitive id generator is for test 
              const id = Math.floor(Math.random() * 100) + 300;
              console.log(id)
              const task = {
                  title: this.title,
                  userId: this.author,
                  id: id,
                  completed: false
                }
              const response = await axios.post(api.apiLink + '/todos',
              task
              )
              if(response.status === 201) {
                this.$emit('addNewTask', task)
                this.reset();
                isActive.value = false
              }
              else {
                alert('ERROR ON SERVER')
              }
            } catch (e) {
              console.log(e)
            }
          }
          else{
            alert('FORM VALIDATION FAILED')
          }
        }
      },
    }
  </script>