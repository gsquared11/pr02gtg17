<script setup>
import { ref } from 'vue';

const emit = defineEmits(['add-task', 'update-task']);
const dialog = ref(false);
const task = ref({
  title: '',
  description: '',
  deadline: '',
  priority: 'Low',
  isComplete: false,
});

const rules = {
  required: (value) => !!value || 'Field is required',
};

function openDialog(existingTask = null) {
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
  dialog.value = true;
}

function handleSubmit() {
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
      <v-card-title class="text-white bg-blue-darken-1 py-4">
        <v-icon :icon="task.id ? 'mdi-pencil' : 'mdi-plus'" class="me-2"></v-icon>
        {{ task.id ? 'Edit Task' : 'Add Task' }}
      </v-card-title>

      <v-card-text class="pt-4">
        <v-container>
          <v-row>
            <v-col cols="12" v-if="!task.id">
              <v-text-field
                v-model="task.title"
                label="Title"
                variant="outlined"
                :rules="[rules.required]"
                error-messages="Title is Required!"
                :error="!task.title"
                hide-details="auto"
                class="mb-4"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field
                v-model="task.description"
                label="Description"
                variant="outlined"
                :rules="[rules.required]"
                error-messages="Description is Required!"
                :error="!task.description"
                hide-details="auto"
                class="mb-4"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <v-text-field
                v-model="task.deadline"
                label="Deadline"
                type="date"
                variant="outlined"
                :rules="[rules.required]"
                hide-details="auto"
                class="mb-4"
                prepend-inner-icon="mdi-calendar"
              ></v-text-field>
            </v-col>
            <v-col cols="12">
              <div class="mb-4">Priority</div>
              <v-radio-group v-model="task.priority" class="priority-group" hide-details="auto">
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
          :disabled="!task.title || !task.description || !task.deadline"
          class="me-2"
        >
          {{ task.id ? 'EDIT' : 'ADD' }}
        </v-btn>
        <v-btn
          variant="elevated"
          color="error"
          prepend-icon="mdi-close"
          @click="dialog = false"
        >
          CANCEL
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<style scoped>
.v-card-title {
  font-size: 1.25rem;
}

:deep(.v-field--error) {
  --v-field-border-width: 1px !important;
}

:deep(.priority-group) {
  display: flex;
  justify-content: space-between;
  gap: 2rem;
  padding: 0 1rem;
}

:deep(.v-radio) {
  margin-left: 0;
  margin-right: 0;
}

:deep(.v-radio .v-label) {
  font-size: 1.1rem;
  margin-left: 0.5rem;
}

:deep(.v-btn) {
  text-transform: uppercase;
  letter-spacing: 0.0892857143em;
}
</style>