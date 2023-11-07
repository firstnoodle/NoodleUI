<template>
    <ui-application-wrapper>
        <ui-horizontal-layout
            borders
            :aside-left-visible="asideLeftVisible"
            :aside-left-width="asideLeftWidth"
            :aside-right-visible="asideRightVisible"
            :aside-right-width="asideRightWidth"
        >
            <template #aside-left>
                <main class="p-8 h-full">
                    <div class="w-full h-full rounded bg-pink-100 p-4">Left is the best place to be</div>
                </main>
            </template>
            <template #main>
                <main class="p-8 h-full">
                    <div class="w-full h-full rounded bg-purple-100 p-4">
                        <section class="flex items-center justify-between">
                            <section class="flex flex-col space-y-2">
                                <ui-button
                                    label="Show/hide"
                                    @click="onToggleAsideLeft"
                                />
                                <ui-button
                                    label="Toggle size"
                                    @click="onToggleAsideLeftSize"
                                />
                            </section>
                            <section class="flex flex-col items-end space-y-2">
                                <ui-button
                                    label="Show/hide"
                                    @click="onToggleAsideRight"
                                />
                                <ui-button
                                    label="Toggle size"
                                    @click="onToggleAsideRightSize"
                                />
                            </section>
                        </section>
                    </div>
                </main>
            </template>
            <template #aside-right>
                <main class="p-8 h-full">
                    <div class="w-full h-full rounded bg-pink-100 p-4">Right</div>
                </main>
            </template>
        </ui-horizontal-layout>
    </ui-application-wrapper>
</template>

<script setup>
import { ref } from 'vue'
import UiApplicationWrapper from '../components/ApplicationWrapper/index.vue'
import UiHorizontalLayout from '../components/HorizontalLayout/index.vue'
import UiButton from '../components/Button/index.vue'

const asideLeftVisible = ref(true)
const asideRightVisible = ref(true)
const asideLeftWidth = ref(400)
const asideRightWidth = ref(400)
const onToggleAsideLeft = () => asideLeftVisible.value = !asideLeftVisible.value
const onToggleAsideLeftSize = () => asideLeftWidth.value = asideLeftWidth.value === 180 ? 380 : 180
const onToggleAsideRight = () => asideRightVisible.value = !asideRightVisible.value
const onToggleAsideRightSize = () => asideRightWidth.value = asideRightWidth.value === 180 ? 240 : 180
</script>