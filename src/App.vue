<script setup>
import TaskTable from './components/TaskTable.vue';
import TaskDialog from './components/TaskDialog.vue';
import { ref } from 'vue';

const taskDialogRef = ref(null);

function openDialog() {
  taskDialogRef.value?.openDialog();
}
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
        <TaskTable />
        <TaskDialog ref="taskDialogRef" />
      </v-container>
    </v-main>
  </v-app>
</template>

<style>
.v-application {
  background: white !important;
}

.text-center {
  text-align: center;
}
</style>