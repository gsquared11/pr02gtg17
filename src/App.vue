<script setup>
import TaskTable from './components/TaskTable.vue';
import { ref } from 'vue';
import toastr from 'toastr';
import 'toastr/build/toastr.min.css';

toastr.options = {
  positionClass: 'toast-bottom-right',
  timeOut: 3000,
  closeButton: true,
};

const taskTableRef = ref(null);

function openDialog() {
  taskTableRef.value?.showDialog();
}

function showMessage(message) {
  toastr.success(message);
}

defineExpose({ showMessage });
</script>

<template>
  <v-app>
    <v-app-bar color="primary">
      <v-app-bar-title prepend-icon="mdi-menu" class="text-center">
        <v-icon icon="mdi-menu" class="me-2"></v-icon>
        FRAMEWORKS
      </v-app-bar-title>
      <v-btn prepend-icon="mdi-plus-circle" elevation="2" @click="openDialog" class="light-btn">
        ADD
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container>
        <TaskTable ref="taskTableRef" @show-message="showMessage" />
      </v-container>
    </v-main>
  </v-app>
</template>

<style>
.light-btn {
  background-color: rgb(54, 125, 238) !important;
}
</style>