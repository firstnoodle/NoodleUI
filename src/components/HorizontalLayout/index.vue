<template>
    <div class="relative w-full h-full overflow-hidden">
        <ui-aside
            v-if="$slots['aside-left']"
            side="left"
            :visible="asideLeftVisible"
            :width="localAsideLeftWidth"
            @resize-start="setResizing(true)"
            @resize="onResizeAsideLeft"
            @resize-end="setResizing(false)"
        >
            <slot name="aside-left" />
        </ui-aside>
        <main
            class="relative z-0 h-full overflow-x-hidden overflow-y-auto"
            :class="[
                props.mainBgColorClass,
                resizing ? null : 'transition-padding duration-500 ease-in-out'
            ]"
            :style="{
                paddingLeft: localAsideLeftWidth+'px',
                paddingRight: localAsideRightWidth+'px',
            }"
        >
            <slot name="main" />
        </main>
        <ui-aside
            v-if="$slots['aside-right']"
            side="right"
            :visible="asideRightVisible"
            :width="localAsideRightWidth"
            @resize-start="setResizing(true)"
            @resize="onResizeAsideRight"
            @resize-end="setResizing(false)"
        >
            <slot name="aside-right" />
        </ui-aside>
    </div>
</template>


<script setup lang="ts">
import { defineEmits, defineProps, ref, watch, withDefaults } from 'vue'
import UiAside from './Aside.vue';

const emit = defineEmits(['aside-left-transition-end', 'aside-right-transition-end'])

const defaultWidth:number = 256
const defaultBgColorClass = 'bg-white'

interface Props {
    asideLeftBgColorClass?: string
    asideLeftVisible?: boolean
    asideLeftWidth?: number
    asideRightBgColorClass?: string
    asideRightVisible?: boolean
    asideRightWidth?: number
    borders?: boolean
    mainBgColorClass?: string
}

const props = withDefaults(defineProps<Props>(), {
    asideLeftBgColorClass: defaultBgColorClass,
    asideLeftVisible: true,
    asideLeftWidth: defaultWidth,
    asideRightBgColorClass: defaultBgColorClass,
    asideRightVisible: true,
    asideRightWidth: defaultWidth,
    borders: true,
    mainBgColorClass: defaultBgColorClass,
})

const resizing = ref(false)
const localAsideLeftWidth = ref(props.asideLeftVisible ? props.asideLeftWidth : 0);
const localAsideRightWidth = ref(props.asideRightVisible ? props.asideRightWidth : 0);
const onResizeAsideLeft = (size:number) => localAsideLeftWidth.value = size
const onResizeAsideRight = (size:number) => localAsideRightWidth.value = size

watch(() => props.asideLeftVisible, (newValue) => {
    if(newValue) {
        localAsideLeftWidth.value = props.asideLeftWidth
    } else {
        localAsideLeftWidth.value = 0
    }
})

const setResizing = (value:boolean) => resizing.value = value
</script>