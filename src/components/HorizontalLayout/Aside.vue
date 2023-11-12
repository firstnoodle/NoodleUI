<template>
    <transition
        enter-active-class="transform"
        :enter-from-class="enterClass"
        enter-to-class=""
        leave-active-class="transform"
        leave-class="translate-x-0"
        :leave-to-class="leaveToClass"
        @before-leave="onTransition(true, false)"
        @after-leave="onTransition(false, false)"
        @before-enter="onTransition(true, false)"
        @after-enter="onTransition(false, true)"

    >
        <aside
            v-show="visible"
            tabindex="0"
            ref="aside"
            :style="{
                width: width + 'px',
                transitionProperty: dragging ? 'none' : 'transform, width'
            }"
            class="hidden md:block absolute top-0 z-10 h-full overflow-x-hidden duration-500 ease-in-out"
            :class="classes"
        >
            <slot />
            <button
                class="absolute top-0 h-full w-1 hover:border-blue-500 hover:bg-blue-500 hover:bg-opacity-50 cursor-ew-resize"
                :class="[
                    props.side === 'left' ? 'right-0 border-r' : 'left-0 border-l',
                    dragging ? 'border-blue-500 bg-blue-500 bg-opacity-50' : 'border-transparent bg-transparent'
                ]"
                @mousedown="startDrag"
            ></button>
        </aside>
    </transition>
</template>

<script setup lang="ts">
import { computed, defineEmits, defineProps, ref, withDefaults } from 'vue'
import clamp from '@/utils/clamp'

const emit = defineEmits([
    'resize-start',
    'resize',
    'resize-end',
    'transitioning'
])

interface Props {
    visible?: boolean
    side?: 'left' | 'right'
    maxWidth?: number
    minWidth?: number
    width: number
}
const props = withDefaults(defineProps<Props>(), {
    visible: true,
    side: 'left',
    maxWidth: 500,
    minWidth: 160,
    width: 256
})

const aside = ref(null)
const classes = computed(() => props.side === 'left' ? 'left-0 border-r' : 'right-0 border-l')
const enterClass = computed(() => props.side === 'left' ? '-translate-x-full' : 'translate-x-full')
const leaveToClass = computed(() =>  props.side === 'left' ? '-translate-x-full' : 'translate-x-full')

let startX:number
let startWidth:number
let dragging = ref(false)
let transitioning = ref(false)

const startDrag = (event:MouseEvent) => {
    startX = event.pageX
    startWidth = props.width
    dragging.value = true;
    window.addEventListener('mouseup', endDrag)
    window.addEventListener('mousemove', onDrag)
    emit('resize-start')
}

const onDrag = (event:MouseEvent) => {
    const deltaX = props.side === 'left' 
        ? event.pageX - startX
        : startX - event.pageX 

    const dragTo = startWidth+deltaX
    const result = clamp(dragTo, props.minWidth, props.maxWidth)

    if(result !== props.width) {
        emit('resize', result)
    }
}

const endDrag = () => {
    dragging.value = false
    window.removeEventListener('mouseup', endDrag)
    window.removeEventListener('mousemove', onDrag)
    emit('resize-end')
}

const onTransition = (transitionActive: boolean, focusAside: boolean) => {
    if(focusAside) {
        aside.value.focus()
    }
    transitioning.value = transitionActive
    emit('transitioning', transitioning.value)
}
</script>