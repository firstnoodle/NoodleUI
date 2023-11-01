<template>
    <transition
        v-if="$slots['aside-right']"
        enter-active-class="transform"
        enter-class="-translate-x-full"
        enter-to-class=""
        leave-active-class="transform"
        leave-class="translate-x-0"
        leave-to-class="translate-x-full"
        @after-enter="$emit('transition-done')"
    >
        <aside
            v-show="visible"
            tabindex="0"
            ref="asideRight"
            style="transition-property: width, transform;"
            class="hidden md:block absolute z-10 left-0 h-full border-r overflow-x-hidden duration-500 ease-in-out"
            :class="classes"
        >
            <slot name="aside-right" />
        </aside>
    </transition>
</template>

<script setup lang="ts">
import { defineProps, withDefaults } from 'vue'

interface Props {
    visible?: string
}
const props = withDefaults(defineProps<Props>(), {
    visible: true
})
</script>