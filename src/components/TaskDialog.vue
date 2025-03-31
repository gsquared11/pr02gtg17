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
let checkTitleDuplicate = null;

function showDatePicker() {
  deadlineRef.value.$el.querySelector('input[type="date"]').showPicker();
}

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
  dialog.value = true;
}

function handleSubmit() {
  if (!validateTitle() || !validateDescription() || !validateDeadline()) {
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
        <v-icon icon="mdi-pencil" v-if="task.id" class="me-2"></v-icon>
        <v-icon icon="mdi-plus" v-else class="me-2"></v-icon>
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
                @input="validateTitle"
                @blur="validateTitle"
                hide-details="auto"
                class="mb-4"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field
                v-model="task.description"
                label="Description"
                variant="outlined"
                :error-messages="descriptionError"
                @input="validateDescription"
                @blur="validateDescription"
                hide-details="auto"
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
                @input="validateDeadline"
                @blur="validateDeadline"
                hide-details="auto"
                class="mb-4"
                append-inner-icon="mdi-calendar"
                @click:append-inner="showDatePicker"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <div class="mb-2">Priority</div>
              <v-radio-group v-model="task.priority" class="priority-group" hide-details="auto" inline>
                <v-radio label="Low" value="Low"></v-radio>
                <v-radio label="Med" value="Medium"></v-radio>
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
          :prepend-icon="task.id ? 'mdi-pencil' : 'mdi-plus'"
          @click="handleSubmit"
          :disabled="!task.title || !task.description || !task.deadline || (!task.id && checkTitleDuplicate && checkTitleDuplicate(task.title))"
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
