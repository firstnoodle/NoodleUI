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
            @after-enter="asideLeft.focus()"
        >
            <aside
                v-show="asideLeftVisible"
                tabindex="0"
                ref="asideLeft"
                style="transition-property: width, transform;"
                class="hidden md:block absolute z-10 left-0 h-full border-r overflow-x-hidden duration-500 ease-in-out"
                :class="asideLeftClasses"
            >
                <slot name="aside-left" />
            </aside>
        </transition>
        <main
            class="relative z-0 h-full overflow-y-auto transition-padding duration-500 ease-in-out"
            :class="mainClasses"
        >
            <slot name="main" />
        </main>
        <transition
            v-if="$slots['aside-right']"
            enter-active-class="transform"
            enter-class="-translate-x-full"
            enter-to-class=""
            leave-active-class="transform"
            leave-class="translate-x-0"
            leave-to-class="translate-x-full"
            @after-enter="asideRight.focus()"
        >
            <aside
                v-show="asideRightVisible"
                tabindex="0"
                ref="asideRight"
                style="transition-property: width, transform;"
                class="hidden md:block absolute z-10 left-0 h-full border-r overflow-x-hidden duration-500 ease-in-out"
                :class="asideRightClasses"
            >
                <slot name="aside-right" />
            </aside>
        </transition>
    </div>
</template>


<script setup lang="ts">

/**
 * Todo:
 * [ ] Finish migration
 * [ ] Create local component for asides?
 */

import { computed, defineEmits, defineProps, ref, onMounted, withDefaults } from 'vue'

const emit = defineEmits(['change', 'delete'])

interface Props {
    asideLeftBgColorClass?: string
    asideLeftVisible?: boolean
    asideLeftWidthClass?: string
    asideRightBgColorClass?: string
    asideRightVisible?: boolean
    asideRightWidthClass?: string
    borders?: boolean
    mainBgColorClass?: string
}

const props = withDefaults(defineProps<Props>(), {
    asideLeftBgColorClass: 'bg-white',
    asideLeftVisible: true,
    asideLeftWidthClass: 'w-64',
    asideRightBgColorClass: 'bg-white',
    asideRightVisible: true,
    asideRightWidthClass: 'w-64',
    borders: true,
    mainBgColorClass: 'bg-white',
})

const asideLeft = ref(null)
const asideRight = ref(null)

const asideLeftClasses = computed(() => {
    const classes:Array<String> = [props.asideLeftBgColorClass, props.asideLeftWidthClass ];
    classes.push();
    return classes;
})
const asideRightClasses = computed(() => {
    const classes:Array<String> = [props.asideRightBgColorClass, props.asideRightWidthClass ];
    classes.push();
    return classes;
})
const mainClasses = computed(() => {
    const classes:Array<String> = [props.mainBgColorClass];
    classes.push();
    return classes;
})

// onMounted(() => {
//   input.value.focus()
// })

</script>