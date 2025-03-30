<script setup>
import { ref } from 'vue';
import TaskDialog from './TaskDialog.vue';
import { useDisplay } from 'vuetify';

const dialog = ref(null);
const snackbar = ref(false);
const snackbarText = ref('');
const { mobile } = useDisplay();

defineExpose({ showDialog });

const tasks = ref([
  {
    id: 1,
    title: 'Learn Vue',
    description: 'Master Vue.js framework',
    deadline: '2024-03-01',
    priority: 'High',
    isComplete: false,
  },
  {
    id: 2,
    title: 'Learn React',
    description: 'Study React fundamentals',
    deadline: '2024-03-15',
    priority: 'Medium',
    isComplete: false,
  },
]);

function showDialog(task = null) {
  dialog.value.openDialog(task);
}

function addTask(newTask) {
  const task = {
    ...newTask,
    id: Math.max(0, ...tasks.value.map((t) => t.id)) + 1,
    isComplete: false,
  };
  tasks.value.push(task);
  snackbarText.value = 'Task added successfully!';
  snackbar.value = true;
}

function updateTask(updatedTask) {
  const index = tasks.value.findIndex((t) => t.id === updatedTask.id);
  if (index !== -1) {
    tasks.value[index] = updatedTask;
    snackbarText.value = 'Task updated successfully!';
    snackbar.value = true;
  }
}

function deleteTask(taskId) {
  const index = tasks.value.findIndex((t) => t.id === taskId);
  if (index !== -1) {
    tasks.value.splice(index, 1);
    snackbarText.value = 'Task deleted successfully!';
    snackbar.value = true;
  }
}
</script>

<template>
  <div>
    <v-row class="mb-4">
      <v-col cols="12" class="text-right"> </v-col>
    </v-row>

    <v-table>
      <thead>
        <tr>
          <th>Title</th>
          <th>Description</th>
          <th>Deadline</th>
          <th>Priority</th>
          <th>Is Complete</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td>{{ task.title }}</td>
          <td>{{ task.description }}</td>
          <td>{{ task.deadline }}</td>
          <td>{{ task.priority }}</td>
          <td>
            <v-checkbox v-model="task.isComplete" hide-details></v-checkbox>
          </td>
          <td>
            <div class="d-flex flex-column gap-4">
              <v-btn
                v-if="!task.isComplete"
                color="primary"
                size="small"
                @click="showDialog(task)"
                prepend-icon="mdi-pencil"
              >
                Update
              </v-btn>
              <v-btn
                color="error"
                size="small"
                @click="deleteTask(task.id)"
                prepend-icon="mdi-close-circle"
                class="delete-btn"
              >
                Delete
              </v-btn>
            </div>
          </td>
        </tr>
      </tbody>
    </v-table>

    <TaskDialog ref="dialog" @add-task="addTask" @update-task="updateTask" />

    <v-snackbar v-model="snackbar" :timeout="3000" color="success">
      {{ snackbarText }}
    </v-snackbar>
  </div>
</template>

<style scoped>
.v-table {
  background: white;
}

.delete-btn :deep(.v-btn__prepend) {
  background: white;
  border-radius: 50%;
  margin-right: 8px;
}
</style>