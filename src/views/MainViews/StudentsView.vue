<template>
    <div 
    class="w-full h-full overflow-hidden flex flex-column align-items-stretch" 
    >
        <!-- ЗАГОЛОВОК -->
        <h1 class="viewer-header">Students</h1>

        <!-- ДОБАВЛЕНИЕ В ГРУППУ пользователя -->
        <Dialog v-model:visible="isShowAddInGroup" modal header="Add a student to a group" :style="{ width: '30rem' }">
            <div class="flex items-center gap-4 mb-4">
                <Select 
                v-model="selectedGroup" 
                :options="store.groups" 
                filter 
                optionLabel="title" 
                placeholder="Select a group" 
                class="w-full md:w-56"
                >
                </Select>
            </div>
            <div class="flex justify-end gap-2">
                <Button 
                class="ml-auto" 
                type="button" 
                text 
                raised 
                label="Confirm" 
                @click="handlerAddStudentToGroup"
                :loading="isLoadingAddGroup"
                ></Button>
            </div>
        </Dialog>

        <!-- КОНТЕНТНАЯ ЧАСТЬ -->
        <section class="h-full overflow-auto px-5 pb-4 pt-3">

            <!-- ОКНО ПРИВЕТСТВИЯ -->
            <div v-if="!store.students.length" class="w-full h-full flex flex-column align-items-center gap-3 justify-content-center">
                <ProgressSpinner 
                v-show="isLoadingData"
                style="width: 90px; height: 90px" 
                strokeWidth="4" 
                fill="transparent"
                animationDuration=".5s" 
                aria-label="Custom ProgressSpinner"
                />
                <h1 v-show="!isLoadingData" class="light-text">The list of students will be displayed here</h1>
            </div>

            <div v-else class="w-full h-max shadow-3 border-round-lg overflow-hidden">
                <DataTable 
                :value="store.students" 
                tableStyle="min-width: 50rem"
                :size="'small'"
                :selectionMode="'single'"
                >
                    <Column class="px-5" field="id" header="ID" style="width: 5%;"></Column>
                    <Column field="name" header="Initials" style="width: 20%;"></Column>
                    <Column field="login" header="Login" style="width: 20%;"></Column>
                    <Column field="createdAt" header="Date of registration" style="width: 20%;">
                        <template #body="{data}">
                            <span class="px-3">
                                {{ formattedDateByTemplate(data.createdAt) }}
                            </span>
                        </template>
                    </Column>
                    <Column field="updatedAt" header="Account edit date"  style="width: 30%;">
                        <template #body="{data}">
                            <span class="px-3">
                                {{ formattedDateByTemplate(data.updatedAt) }}
                            </span>
                        </template>
                    </Column>
                    <Column class="px-5" header="Действия" style="width: 5%;">
                    <template #body>
                        <div class="flex align-items-center">
                            <Button 
                            icon="pi pi-bookmark" 
                            text 
                            raised 
                            size="small" 
                            v-tooltip.bottom="'Add to group'" 
                            @click="isShowAddInGroup = true"
                            />
                        </div>
                    </template></Column>
                </DataTable>
            </div>
        </section>
    </div>
</template>

<script setup lang="ts">
import type { GroupTest } from '@/types/testTypes';
import { useMainStore } from '@/stores/mainStore';
import { formattedDateByTemplate } from '@/utils/timeUtils';
import { getUsers } from '@/api/usersApi';
import { onMounted, ref, type Ref } from 'vue';

// #########################################   COMPOSABLES   #########################################
const store = useMainStore();


// #########################################   DATA   #########################################
const selectedGroup: Ref<GroupTest | null> = ref(null);
const isLoadingAddGroup = ref(false);
const isLoadingData = ref(false); 
const isShowAddInGroup = ref(false);
const page = ref(1);
const perPage = ref(20);


// #########################################   METHODS   #########################################
function handlerAddStudentToGroup() {
    isLoadingAddGroup.value = true;
    try {
        // 
    } catch (err) {
        console.error('/src/views/MainViews/StudentsView.vue: handlerAddStudentToGroup => ', err);
        throw err;
    } finally {
        isLoadingAddGroup.value = false;
    }
}


// #########################################   LIFECYCLE HOOKS   #########################################
onMounted(async () => {
    // Получение списка пользователей
    if(store.isAuth && store.appRole === 'teacher') {
        try {
            isLoadingData.value = true;
            const { data: { users }, meta } = await getUsers(page.value, perPage.value);
            store.students = users;
        } catch (err) {
            console.error(err);
        } finally {
            isLoadingData.value = false;
        }
    }
});

</script>

<style scoped>
    
</style>