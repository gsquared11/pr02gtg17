<script setup>
import { ref } from 'vue';
import TaskDialog from './TaskDialog.vue';
import { useDisplay } from 'vuetify';
import toastr from 'toastr';
import 'toastr/build/toastr.min.css';

const dialog = ref(null);
const { mobile } = useDisplay();

defineExpose({ showDialog });

function formatDate(dateString) {
  const date = new Date(dateString);
  return date.toLocaleDateString('en-US', {
    month: '2-digit',
    day: '2-digit',
    year: 'numeric'
  });
}

const tasks = ref([]);

function isTitleDuplicate(title, excludeTaskId = null) {
  return tasks.value.some(task => 
    task.title.toLowerCase() === title.toLowerCase() && 
    task.id !== excludeTaskId
  );
}

function showDialog(task = null) {
  dialog.value.openDialog(task, isTitleDuplicate);
}

function addTask(newTask) {
  if (isTitleDuplicate(newTask.title)) {
    toastr.error('A task with this title already exists!');
    return false;
  }
  
  const task = {
    ...newTask,
    id: Math.max(0, ...tasks.value.map((t) => t.id)) + 1,
    isComplete: false,
  };
  tasks.value.push(task);
  toastr.success('Task added successfully!');
  return true;
}

function updateTask(updatedTask) {
  const index = tasks.value.findIndex((t) => t.id === updatedTask.id);
  if (index !== -1) {
    tasks.value[index] = updatedTask;
    toastr.success('Task updated successfully!');
    return true;
  }
  return false;
}

function deleteTask(taskId) {
  const index = tasks.value.findIndex((t) => t.id === taskId);
  if (index !== -1) {
    tasks.value.splice(index, 1);
    toastr.success('Task deleted successfully!');
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
          <th class="text-grey font-weight-bold">Title</th>
          <th class="text-grey font-weight-bold">Description</th>
          <th class="text-grey font-weight-bold">Deadline</th>
          <th class="text-grey font-weight-bold">Priority</th>
          <th class="text-grey font-weight-bold">Is Complete</th>
          <th class="text-grey font-weight-bold">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td>{{ task.title }}</td>
          <td>{{ task.description }}</td>
          <td>{{ formatDate(task.deadline) }}</td>
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
                prepend-icon="mdi-square-edit-outline"
              >
                Update
              </v-btn>
              <v-btn
                color="red"
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
  </div>
</template>