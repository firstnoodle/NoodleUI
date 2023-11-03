<template>
    <div class="relative w-full h-full overflow-hidden">
        <transition
            v-if="$slots['aside-left']"
            enter-active-class="transform"
            enter-class="-translate-x-full"
            enter-to-class=""
            leave-active-class="transform"
            leave-class="translate-x-0"
            leave-to-class="-translate-x-full"
            @after-enter="asideLeft.value.focus()"
        >
            <!-- 
                TODO: the style transition-property needs 'width' in order to be animated (in show/hide situations),
                but cannot be there when dragging. 
                So... in needs to be toggled dynamically
            -->
            <aside
                v-show="asideLeftVisible"
                tabindex="0"
                ref="asideLeft"
                style="transition-property: transform;"
                :style="{ width: localAsideLeftWidth + 'px' }"
                class="hidden md:block absolute z-10 left-0 h-full border-r overflow-x-hidden duration-500 ease-in-out"
                :class="asideLeftClasses"
            >
                <div class="relative h-full w-full bg-yellow-100">
                    <slot name="aside-left" />
                    <button
                        class="absolute right-0 top-0 h-full w-1 border-r hover:border-blue-500 hover:bg-blue-500 hover:bg-opacity-50 cursor-ew-resize"
                        :class="{
                            'border-blue-500 bg-blue-500 bg-opacity-50': dragging,
                            'border-transparent bg-transparent': !dragging
                        }"
                        @mousedown="startDrag"
                    ></button>
                </div>
            </aside>
        </transition>
        <main
            class="relative z-0 h-full overflow-x-hidden overflow-y-auto transition-padding duration-500 ease-in-out"
            :class="mainClasses"
            :style="mainStyle"
        >
            <slot name="main" />
        </main>
        <transition
            v-if="$slots['aside-right']"
            enter-active-class="transform"
            enter-class="translate-x-full"
            enter-to-class=""
            leave-active-class="transform"
            leave-class="translate-x-0"
            leave-to-class="translate-x-full"
            @after-enter="asideRight.value.focus()"
        >
            <aside
                v-show="asideRightVisible"
                tabindex="0"
                ref="asideRight"
                style="transition-property: width, transform;"
                :style="asideRightStyle"
                class="hidden md:block absolute top-0 right-0 z-10 h-full border-l overflow-y-auto overflow-x-hidden duration-500 ease-in-out"
                :class="asideRightClasses"
            >
                <slot name="aside-right" />
            </aside>
        </transition>
    </div>
</template>


<script setup lang="ts">
import { computed, defineEmits, defineProps, reactive, ref, withDefaults } from 'vue'

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

const asideLeft = ref(null)
const asideRight = ref(null)

const localAsideLeftWidth = ref(props.asideLeftWidth);
const localAsideRightWidth = ref(props.asideRightWidth);

// const paddingLeft = ref(0);
// const paddingRight = ref(0);
const mainStyle = computed(() => {
    // paddingLeft: `${localAsideLeftWidth}px`,
    // paddingRight: `${localAsideRightWidth}px`
    return {
        paddingLeft: `${localAsideLeftWidth}px`,
        paddingRight: `${localAsideRightWidth}px`
    }
})

const asideLeftClasses = computed(() => {
    const classes:Array<String> = [props.asideLeftBgColorClass ];
    classes.push();
    return classes;
})

const asideRightClasses = computed(() => {
    const classes:Array<String> = [props.asideRightBgColorClass ];
    classes.push();
    return classes;
})
const asideRightStyle = computed(() => {
    return {
        width: `${props.asideRightWidth}px`
    }
})

// TODO: use mainBgColorClass directly if no other classes are there
const mainClasses = computed(() => {
    return [
        props.mainBgColorClass,
    ].join(' ')
})

let startX:number;
let startWidth:number = localAsideLeftWidth.value;
let dragging = ref(false);

const onDrag = (event:MouseEvent) => {
    const deltaX = event.pageX - startX
    localAsideLeftWidth.value = startWidth + deltaX
    mainStyle.value.paddingLeft = `${localAsideLeftWidth}px`
    mainStyle.value.paddingRight = `${localAsideRightWidth}px`
}
const startDrag = (event:MouseEvent) => {
    startX = event.pageX;
    startWidth = localAsideLeftWidth.value;
    dragging.value = true;
    window.addEventListener('mouseup', endDrag)
    window.addEventListener('mousemove', onDrag)
}

const endDrag = () => {
    dragging.value = false;
    window.removeEventListener('mouseup', endDrag)
    window.removeEventListener('mousemove', onDrag)
}
</script>