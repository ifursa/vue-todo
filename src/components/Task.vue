<template>
<v-card :color="completed ? `dark` : `white`" class="py-2">
    <v-card-title>
        {{title}}
    </v-card-title>
    <v-card-subtitle> {{ completed ? 'COMPLETED' : ''}}</v-card-subtitle>
    <v-card-actions>
        <v-btn variant="outlined" color="green" @click="changeStatus(taskID, !completed)">
            {{ completed ? 'undo' : ' complete'}}
        </v-btn>
        <v-btn variant="outlined" color="red" @click="removeTask(taskID)"> Remove</v-btn>
    </v-card-actions>
</v-card>
</template>
<script>
export default {
    props: {
    title: String,
    taskID: Number,
    completed: Boolean
  },
  methods: {
    removeTask(taskID) {
        this.$emit('removeTask', taskID)
    },
    changeStatus(taskID, status) {
        this.$emit('changeStatus', taskID, status)
    }
  }
}
</script>