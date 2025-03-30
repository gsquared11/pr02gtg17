<script setup>
import TaskTable from './components/TaskTable.vue';
import { ref } from 'vue';

const taskTableRef = ref(null);
const snackbar = ref(false);
const snackbarText = ref('');

function openDialog() {
  taskTableRef.value?.showDialog();
}

function showMessage(message) {
  snackbarText.value = message;
  snackbar.value = true;
}

defineExpose({ showMessage });
</script>

<template>
  <v-app>
    <v-app-bar color="primary">
      <v-spacer></v-spacer>
      <v-app-bar-title prepend-icon="mdi-menu" class="text-center">
        <v-icon icon="mdi-menu" class="me-2"></v-icon>
        FRAMEWORKS
      </v-app-bar-title>
      <v-spacer></v-spacer>
      <v-btn prepend-icon="mdi-plus-circle" elevation="2" @click="openDialog">
        ADD
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container>
        <TaskTable ref="taskTableRef" @show-message="showMessage" />
      </v-container>
    </v-main>

    <v-snackbar
      v-model="snackbar"
      :timeout="3000"
      color="success"
      location="bottom right"
    >
      {{ snackbarText }}
    </v-snackbar>
  </v-app>
</template>