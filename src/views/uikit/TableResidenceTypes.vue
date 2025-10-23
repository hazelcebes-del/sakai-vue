<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Textarea from 'primevue/textarea';

// Data residence type
const residenceTypes = ref([]);
const addDialogVisible = ref(false);

const newResidenceType = ref({
    name: '',
    description: ''
});

function openAddDialog() {
    newResidenceType.value = { name: '', description: '' };
    addDialogVisible.value = true;
}

function addResidenceType() {
    if (newResidenceType.value.name && newResidenceType.value.description) {
        residenceTypes.value.push({
            id: residenceTypes.value.length + 1,
            name: newResidenceType.value.name,
            description: newResidenceType.value.description
        });
        addDialogVisible.value = false;
    }
}

function deleteResidenceType(id) {
    residenceTypes.value = residenceTypes.value.filter((r) => r.id !== id);
}
</script>

<template>
    <div class="card">
        <div class="flex justify-between items-center mb-4">
            <div class="font-semibold text-xl">Residence Types</div>
            <Button label="Add Residence Type" icon="pi pi-plus" @click="openAddDialog" />
        </div>

        <DataTable :value="residenceTypes" dataKey="id" showGridlines :rows="10" :paginator="true">
            <template #empty>No residence types found.</template>
            <Column field="name" header="Residence Type Name" style="min-width: 14rem"></Column>
            <Column field="description" header="Description" style="min-width: 20rem"></Column>
            <Column header="Actions" style="min-width: 8rem">
                <template #body="{ data }">
                    <div class="flex gap-2">
                        <Button icon="pi pi-pencil" text severity="info" />
                        <Button icon="pi pi-trash" text severity="danger" @click="deleteResidenceType(data.id)" />
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>

    <!-- Add Residence Type Dialog -->
    <Dialog v-model:visible="addDialogVisible" modal header="Add Residence Type" :style="{ width: '25rem' }">
        <div class="flex flex-col gap-3 mt-3">
            <div class="flex flex-col gap-2">
                <label class="font-medium">Residence Type Name</label>
                <InputText v-model="newResidenceType.name" placeholder="Enter residence type name" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">Description</label>
                <Textarea v-model="newResidenceType.description" rows="3" placeholder="Enter description" />
            </div>
        </div>

        <template #footer>
            <div class="flex justify-end gap-2">
                <Button label="Cancel" text @click="addDialogVisible = false" />
                <Button label="Add" @click="addResidenceType" />
            </div>
        </template>
    </Dialog>
</template>

<style scoped lang="scss">
:deep(.p-datatable) {
    .p-datatable-thead > tr > th {
        background: var(--surface-section);
        font-weight: 600;
    }
}

:deep(.p-dialog .p-dialog-header) {
    font-weight: 600;
}

:deep(.p-dialog .p-dialog-content) {
    padding-top: 0;
}
</style>
