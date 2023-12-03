<template>
  <template v-if="formState">
    <el-form-item :label="formState.payload.label">
      <template v-if="formState.type === 'input'">
        <el-input v-model="formState.payload.value" placeholder="Please input" />
      </template>
      <template v-else-if="formState.type === 'radio'">
        <el-radio-group v-model="formState.payload.value">
          <el-radio v-for="option in formState
            .payload.options" :key="option.label" :label="option.value">{{ option.label }}</el-radio>
        </el-radio-group>
      </template>
      <template v-else-if="formState.type === 'checkbox'">
        <el-radio-group v-model="formState.payload.value">
          <el-radio v-for="option in formState
            .payload.options" :key="option.label" :label="option.value">{{ option.label }}</el-radio>
        </el-radio-group>
      </template>
      <template v-else-if="formState.type === 'select'">
        <el-select v-model="formState.payload.value" placeholder="Select">
          <el-option v-for="option in formState.payload.options" :key="option.value" :label="option.label" :value="option.value" />
        </el-select>
      </template>
    </el-form-item>
    <form-item-comp :form-state="getNext()" />
  </template>
</template>

<script setup>
const props = defineProps({
  formState: {}
})
const getNext = () => {
  let current = props.formState
  if (!current) {
    return null
  }
  const acients = [
  ]
  acients.unshift(current)
  while (current = current.parent) {
    acients.unshift(current)
  }
  return  props.formState.next(props.formState, acients)
}

</script>
<style scoped lang="scss"></style>