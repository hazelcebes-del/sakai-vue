<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Textarea from 'primevue/textarea';
import ConfirmDialog from 'primevue/confirmdialog';
import { useConfirm } from 'primevue/useconfirm';

// =======================
// DATA DUMMY (hardcode)
// =======================
const residenceTypes = ref([
    {
        id: 1,
        name: 'Apartment',
        description: 'A self-contained housing unit that occupies part of a building.'
    },
    {
        id: 2,
        name: 'Condominium',
        description: 'Privately owned unit in a shared property with shared facilities.'
    },
    {
        id: 3,
        name: 'Villa',
        description: 'A large, luxurious house often found in resort or suburban areas.'
    },
    {
        id: 4,
        name: 'Dormitory',
        description: 'Shared housing often used for students or workers.'
    },
    {
        id: 5,
        name: 'Townhouse',
        description: 'A multi-floor home that shares one or more walls with adjacent properties.'
    }
]);

// =======================
// STATE & LOGIC
// =======================
const addDialogVisible = ref(false);
const dialogMode = ref('add'); // 'add' | 'edit'
const confirm = useConfirm();

const newResidenceType = ref({
    id: null,
    name: '',
    description: ''
});

function openAddDialog() {
    dialogMode.value = 'add';
    newResidenceType.value = { id: null, name: '', description: '' };
    addDialogVisible.value = true;
}

function openEditDialog(residence) {
    dialogMode.value = 'edit';
    newResidenceType.value = { ...residence };
    addDialogVisible.value = true;
}

function saveResidenceType() {
    if (!newResidenceType.value.name || !newResidenceType.value.description) return;

    if (dialogMode.value === 'add') {
        residenceTypes.value.push({
            id: residenceTypes.value.length + 1,
            name: newResidenceType.value.name,
            description: newResidenceType.value.description
        });
    } else if (dialogMode.value === 'edit') {
        const index = residenceTypes.value.findIndex((r) => r.id === newResidenceType.value.id);
        if (index !== -1) {
            residenceTypes.value[index] = { ...newResidenceType.value };
        }
    }

    addDialogVisible.value = false;
}

function confirmDelete(id) {
    confirm.require({
        message: 'Are you sure you want to delete this residence type?',
        header: 'Confirm Deletion',
        icon: 'pi pi-exclamation-triangle',
        acceptClass: 'p-button-danger',
        accept: () => {
            deleteResidenceType(id);
        }
    });
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
                        <Button icon="pi pi-pencil" text severity="info" @click="openEditDialog(data)" />
                        <Button icon="pi pi-trash" text severity="danger" @click="confirmDelete(data.id)" />
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>

    <!-- Add/Edit Dialog -->
    <Dialog
        v-model:visible="addDialogVisible"
        modal
        :header="dialogMode === 'add' ? 'Add Residence Type' : 'Edit Residence Type'"
        :style="{ width: '25rem' }"
    >
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
                <Button :label="dialogMode === 'add' ? 'Add' : 'Save'" @click="saveResidenceType" />
            </div>
        </template>
    </Dialog>

    <!-- Confirm Dialog -->
    <ConfirmDialog />
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
