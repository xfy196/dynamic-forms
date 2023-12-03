<template>
    <FormItem :form-state="formState" />
</template>

<script setup>
import {onBeforeMount, isReactive, reactive } from 'vue';

import FormItem from "./components/form-item-comp.vue"
let formState = reactive({})
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
            return null
        }
        nextItem.parent = current
        if(!isReactive(nextItem)){
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
                return item1
            }
        }
    )
    const item3 = createFormItem("checkbox", {
        label: "checkbox",
        value: "test3-2",
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
    const item1 = createFormItem("input", { label: "input", value: '' }, (current) => {
        return current.payload.value === "test1" ? item2 : item3
    })
    formState = item1
})
</script>
<style scoped lang="scss"></style>