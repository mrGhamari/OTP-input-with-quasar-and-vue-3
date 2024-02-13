<template>
  <div class="fit row q-col-gutter-x-md justify-center">
    <q-input
      outlined
      class="col"
      :key="index"
      maxlength="1"
      v-bind="$attrs"
      input-class="text-center"
      v-model="fieldValues[index - 1]"
      v-for="index in props.inputLength"
      @keyup="onKeyUp($event, index - 1)"
      :ref="(el) => updateFieldRef(el, index - 1)"
      @update:model-value="onUpdate($event, index - 1)"
    />
  </div>
</template>

<script setup lang="ts">
name: 'OtpInput';
import { computed, onBeforeUpdate, ref, watch } from 'vue';

// ------ Lifecycle Hooks ------
onBeforeUpdate(() => (fields.value = []));

// ------ Interface ------
interface IOtp {
  inputLength: number;
}

// ------ Variables ------
const fields = ref([]);
const fieldValues = ref([]);
const model = defineModel();

const props = withDefaults(defineProps<IOtp>(), {
  inputLength: 5,
});

const composite = computed(() => {
  const nonNullFields = fieldValues.value.filter((value) => value);
  if (props.inputLength !== nonNullFields.length) return '';
  return nonNullFields.join('');
});

watch(composite, (newVal) => {
  if (newVal) model.value = newVal;
});

const updateFieldRef = (element, index: number) => {
  if (element) fields.value[index] = element;
};

const focus = (index: number) => {
  if (index >= 0) {
    if (index < props.inputLength) fields.value[index].select();
    else {
      if (composite.value) fields.value[index - 1].blur();
    }
  }
};

const onUpdate = (value, index: number) => {
  if (value) focus(index + 1);
};

const onKeyUp = (evnt, index: number) => {
  const key = evnt.key;
  if (['Tab', 'Shift', 'Meta', 'Control', 'Alt'].includes(key)) return;
  if (['Delete'].includes(key)) return;
  if (key === 'ArrowLeft' || key === 'Backspace') focus(index - 1);
  else if (key === 'ArrowRight') focus(index + 1);
};
</script>
