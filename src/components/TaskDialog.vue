<script setup>
import { ref } from 'vue';

const emit = defineEmits(['add-task', 'update-task']);
const dialog = ref(false);
const deadlineRef = ref(null);
const task = ref({
  title: '',
  description: '',
  deadline: '',
  priority: 'Low',
  isComplete: false,
});

const titleError = ref('');
const descriptionError = ref('');
const deadlineError = ref('');
const attemptedSubmit = ref(false);
let checkTitleDuplicate = null;

function validateTitle() {
  if (!task.value.title) {
    titleError.value = 'Title is Required!';
    return false;
  }
  if (checkTitleDuplicate && checkTitleDuplicate(task.value.title, task.value.id)) {
    titleError.value = 'A task with this title already exists!';
    return false;
  }
  titleError.value = '';
  return true;
}

function validateDescription() {
  if (!task.value.description) {
    descriptionError.value = 'Description is Required!';
    return false;
  }
  descriptionError.value = '';
  return true;
}

function validateDeadline() {
  if (!task.value.deadline) {
    deadlineError.value = 'Deadline is Required!';
    return false;
  }
  deadlineError.value = '';
  return true;
}

function openDialog(existingTask = null, titleDuplicateCheck = null) {
  checkTitleDuplicate = titleDuplicateCheck;
  if (existingTask) {
    task.value = { ...existingTask };
  } else {
    task.value = {
      title: '',
      description: '',
      deadline: '',
      priority: 'Low',
      isComplete: false,
    };
  }
  titleError.value = '';
  descriptionError.value = '';
  deadlineError.value = '';
  attemptedSubmit.value = false;
  dialog.value = true;
}

function handleSubmit() {
  attemptedSubmit.value = true;
  
  const titleValid = validateTitle();
  const descriptionValid = validateDescription();
  const deadlineValid = validateDeadline();
  
  const isValid = titleValid && descriptionValid && deadlineValid;
  
  if (!isValid) {
    return;
  }
  
  if (task.value.id) {
    emit('update-task', { ...task.value });
  } else {
    emit('add-task', { ...task.value });
  }
  
  dialog.value = false;
}

defineExpose({ openDialog });
</script>

<template>
  <v-dialog v-model="dialog" max-width="400px">
    <v-card>
      <v-card-title class="text-white bg-primary py-4">
        <v-icon icon="mdi-square-edit-outline" v-if="task.id" class="me-2"></v-icon>
        <v-icon icon="mdi-plus-circle" v-else class="me-2"></v-icon>
        <template v-if="task.id">Edit Task</template>
        <template v-else>Add Task</template>
      </v-card-title>

      <v-card-text class="pt-4">
        <v-container>
          <v-row>
            <v-col cols="12" v-if="!task.id">
              <v-text-field
                v-model="task.title"
                label="Title"
                variant="outlined"
                :error-messages="titleError"
                :error="attemptedSubmit && (!task.title || (checkTitleDuplicate && checkTitleDuplicate(task.title, task.id)))"
                class="mb-4"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field
                v-model="task.description"
                label="Description"
                variant="outlined"
                :error-messages="descriptionError"
                :error="attemptedSubmit && !task.description"
                class="mb-4"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field
                ref="deadlineRef"
                v-model="task.deadline"
                label="Deadline"
                type="date"
                variant="outlined"
                :error-messages="deadlineError"
                :error="attemptedSubmit && !task.deadline"
                class="mb-4 date-input"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <div class="mb-2">Priority</div>
              <v-radio-group v-model="task.priority" class="priority-group" inline>
                <v-radio label="Low" value="Low"></v-radio>
                <v-radio label="Med" value="Med"></v-radio>
                <v-radio label="High" value="High"></v-radio>
              </v-radio-group>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <v-card-actions class="pa-4">
        <v-spacer></v-spacer>
        <v-btn
          variant="elevated"
          color="primary"
          :prepend-icon="task.id ? 'mdi-square-edit-outline' : 'mdi-plus-circle'"
          @click="handleSubmit"
          class="me-2"
        >
          {{ task.id ? 'EDIT' : 'ADD' }}
        </v-btn>
        <v-btn
          variant="elevated"
          color="red"
          prepend-icon="mdi-cancel"
          @click="dialog = false"
        >
          CANCEL
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<style>
/*Moves date picker icon to the right*/
.date-input ::-webkit-calendar-picker-indicator {
  position: absolute;
  right: 10px;
  cursor: pointer;
}
</style>
