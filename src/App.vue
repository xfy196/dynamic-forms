<template>
    <el-form :model="formData" ref="formRef">
        <FormItem :form-state="formState" />
    </el-form>
    <el-button type="primary" @click="handleClick">确认</el-button>
</template>

<script setup>
import { onBeforeMount, isReactive, reactive, ref, toRaw, shallowRef, shallowReactive } from 'vue';

import FormItem from "./components/form-item-comp.vue"
let formState = reactive({})
const formRef = ref(null)

let formData = shallowReactive({})
const createFormItem = (formItemType, payload = {}, next = null, parent = null) => {
    if (!next) {
        next = () => null
    }
    if (!parent) {
        parent = null
    }
    const nextFunc = (current, acients) => {
        let nextItem = next(current, acients)
        if (!nextItem) {
            acients.forEach(ac => {
                formData[ac.payload.prop] = ac.payload.value
            })
            console.log("🚀 ~ file: App.vue:28 ~ nextFunc ~ formData:", formData)
            return null
        }
        nextItem.parent = current
        if (!isReactive(nextItem)) {
            nextItem = reactive(nextItem)
        }
        return nextItem
    }
    const formItem = reactive({
        type: formItemType,
        payload,
        next: nextFunc,
        parent
    })
    return formItem
}
onBeforeMount(() => {
    const item2 = createFormItem("select", {
        label: "select",
        prop: "select",
        value: "test2-1", options: [
            {
                label: "test2-1",
                value: "test2-1"
            },
            {
                label: "test2-2",
                value: "test2-2"
            },
            {
                label: "test2-3",
                value: "test2-3"
            },
        ]
    },
        (current) => {
            if (current.payload.value === "test2-2") {
                return item3
            } else if (current.payload.value === "test2-3") {
                return null
            }
        }
    )
    const item3 = createFormItem("checkbox", {
        label: "checkbox",
        value: "test3-2",
        prop: "checkbox",
        options: [
            {
                label: "test3-1",
                value: "test3-1"
            },
            {
                label: "test3-2",
                value: "test3-2"
            },
            {
                label: "test3-3",
                value: "test3-3"
            }
        ]
    })
    const item1 = createFormItem("input", { label: "input", prop: "input", value: '' }, (current) => {
        return current.payload.value === "test1" ? item2 : item3
    })
    formState = item1
})
const handleClick = async () => {
    await formRef.value.validate((valid, fields) => {
        if (valid) {
            console.log(formData)
        } else {
            console.log("🚀 ~ file: App.vue:94 ~ formRef.value.validate ~ fields:", fields)
        }
    })
}
</script>
<style scoped lang="scss"></style>