<template>
    <div 
    class="relative overflow-auto flex flex-column align-items-center justify-content-start py-6" 
    style="height: 100% !important;"
    >
        <Dialog v-model:visible="isShowConfirmDialog" modal :header="'Finish creating the test?'" :style="{ width: '30rem' }">
            <div class="flex justify-content-end gap-2 pr-2">
                <Button type="button" label="Cancel" severity="secondary" text raised @click="isShowConfirmDialog = false"></Button>
                <Button type="button" label="Yes" text raised @click="handlerConfirmCreateTest"></Button>
            </div>
        </Dialog>

        <div 
            class="w-full h-full flex flex-column align-items-center gap-3 justify-content-center"
            v-if="!props.questions.length"
            >
                <h1 class="light-text">Start adding questions</h1>
                <div class="w-max">
                    <Button 
                    label="Create" 
                    icon="pi pi-plus" 
                    text raised 
                    iconPos="top"
                    @click="handlerAppendNewQuestion"
                    />
                </div>
        </div>
        <form class="flex w-max h-max" @submit.prevent v-for="question in props.questions" :key="question.number">
            <questionItem 
            @update-question="(data: Question) => emit('updateQuestion', data)"
            :question-data="question"
            />
        </form>
        <Button
        class="mt-auto"
        v-show="props.questions.length"
        label="Add" 
        icon="pi pi-plus" 
        text raised 
        @click="handlerAppendNewQuestion"
        />

        <!-- КНОПКА СОЗДАТЬ ТЕСТ -->
        <Button
        class="w-4 mt-5 mb-2"
        v-show="props.questions.length"
        label="Create a test" 
        icon="pi pi-folder-plus" 
        raised 
        :loading="props.isLoadingCreate"
        @click="isShowConfirmDialog = true"
        />
    </div>
</template>

<script setup lang="ts">
import { defineProps, defineEmits, ref } from 'vue';
import type { Question } from '@/types/testTypes';
import questionItem from './questions/questionItem.vue';

const props = defineProps<{
    questions: Question[];
    isLoadingCreate?: boolean;
}>();

const emit = defineEmits({
    appendQuestion: (question: Question) => question,
    updateQuestion: (data: Question) => data,
    confirmCreationTest: () => true,
});

const isShowConfirmDialog = ref(false);

function handlerConfirmCreateTest() {
    try {
        emit('confirmCreationTest');
        isShowConfirmDialog.value = false;
    } catch (err) {
        console.error('/src/components/MainComponents/createTest/questionsTestForm.vue: handlerConfirmCreateTest => ', err);
        throw err;
    }
}

function handlerAppendNewQuestion() {
    let no = 0;
    if(props.questions) {
        no = props.questions.length + 1;
    }
    emit('appendQuestion', { number: no, question: '', type: 'text', });
}

</script>

<style scoped>

</style>