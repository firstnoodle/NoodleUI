<template>
    <div class="relative w-full h-full overflow-hidden">
        <ui-aside
            v-if="$slots['aside-left']"
            side="left"
            :visible="asideLeftVisible"
            :width="localAsideLeftWidth"
            @resize-start="resizing = true"
            @resize="onResizeAsideLeft"
            @resize-end="resizing = false"
            @transitioning="onTransitionAsideLeft"
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
                paddingLeft: paddingLeft+'px',
                paddingRight: paddingRight+'px',
            }"
        >
            <slot name="main" />
        </main>
        <ui-aside
            v-if="$slots['aside-right']"
            side="right"
            :visible="asideRightVisible"
            :width="localAsideRightWidth"
            @resize-start="resizing = true"
            @resize="onResizeAsideRight"
            @resize-end="resizing = false"
            @transitioning="onTransitionAsideRight"
        >
            <slot name="aside-right" />
        </ui-aside>
    </div>
</template>


<script setup lang="ts">
import { defineEmits, defineProps, ref, watch, withDefaults } from 'vue'
import UiAside from './Aside.vue';

const emit = defineEmits([
    'aside-left-transition-end',
    'aside-right-transition-end'
])

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

// Resizing is when one of the asides is resized by dragging - then we remove transition classes
const resizing = ref(false)

// we cannot mutate props, so we create a local copy - TODO... should we not just emit the new value
const localAsideLeftWidth = ref(props.asideLeftWidth);
const localAsideRightWidth = ref(props.asideRightVisible ? props.asideRightWidth : 0);
const paddingLeft = ref(props.asideLeftWidth);
const paddingRight = ref(props.asideRightWidth);
const onResizeAsideLeft = (size:number) => localAsideLeftWidth.value = paddingLeft.value = size
const onTransitionAsideLeft = (transitioning: boolean) => console.log('transitioning left', transitioning)
const onResizeAsideRight = (size:number) => localAsideRightWidth.value = paddingRight.value = size
const onTransitionAsideRight = (transitioning: boolean) => console.log('transitioning right', transitioning)

// For transitioning the padding on <main>
watch(() => props.asideLeftVisible, (newValue) => paddingLeft.value = newValue ? localAsideLeftWidth.value : 0)
watch(() => props.asideRightVisible, (newValue) => paddingRight.value = newValue ? localAsideRightWidth.value : 0)

// For triggering transition to a specific width
watch(() => props.asideLeftWidth, newValue => localAsideLeftWidth.value = paddingLeft.value = newValue)
watch(() => props.asideRightWidth, newValue => localAsideRightWidth.value = paddingRight.value = newValue)

</script>