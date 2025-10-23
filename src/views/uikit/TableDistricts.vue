<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Dropdown from 'primevue/dropdown';
import ConfirmDialog from 'primevue/confirmdialog';
import { useConfirm } from 'primevue/useconfirm';

// =======================
// DUMMY DATA AREA UNTUK DROPDOWN
// =======================
const areas = ref([
    { id: 1, name: 'Doha City' },
    { id: 2, name: 'Al Rayyan' },
    { id: 3, name: 'Lusail' }
]);

// =======================
// DUMMY DATA DISTRICTS (hardcode)
// =======================
const districts = ref([
    { id: 1, area: { id: 1, name: 'Doha City' }, areaName: 'Doha City', name: 'West Bay', latitude: '25.3200', longitude: '51.5200' },
    { id: 2, area: { id: 1, name: 'Doha City' }, areaName: 'Doha City', name: 'Old Airport', latitude: '25.2600', longitude: '51.5500' },
    { id: 3, area: { id: 2, name: 'Al Rayyan' }, areaName: 'Al Rayyan', name: 'Education City', latitude: '25.3150', longitude: '51.4330' },
    { id: 4, area: { id: 2, name: 'Al Rayyan' }, areaName: 'Al Rayyan', name: 'Al Gharrafa', latitude: '25.3500', longitude: '51.4660' },
    { id: 5, area: { id: 3, name: 'Lusail' }, areaName: 'Lusail', name: 'Marina District', latitude: '25.4200', longitude: '51.5300' },
    { id: 6, area: { id: 3, name: 'Lusail' }, areaName: 'Lusail', name: 'Fox Hills', latitude: '25.4050', longitude: '51.5100' }
]);

// =======================
// STATE DAN LOGIC
// =======================
const addDialogVisible = ref(false);
const dialogMode = ref('add'); // 'add' | 'edit'
const confirm = useConfirm();

const newDistrict = ref({
    id: null,
    area: null,
    name: '',
    latitude: '',
    longitude: ''
});

function openAddDialog() {
    dialogMode.value = 'add';
    newDistrict.value = { area: null, name: '', latitude: '', longitude: '' };
    addDialogVisible.value = true;
}

function openEditDialog(district) {
    dialogMode.value = 'edit';
    newDistrict.value = { ...district };
    addDialogVisible.value = true;
}

function saveDistrict() {
    if (
        !newDistrict.value.area ||
        !newDistrict.value.name ||
        !newDistrict.value.latitude ||
        !newDistrict.value.longitude
    )
        return;

    if (dialogMode.value === 'add') {
        districts.value.push({
            id: districts.value.length + 1,
            ...newDistrict.value,
            areaName: newDistrict.value.area.name
        });
    } else if (dialogMode.value === 'edit') {
        const index = districts.value.findIndex((d) => d.id === newDistrict.value.id);
        if (index !== -1) {
            districts.value[index] = {
                ...newDistrict.value,
                areaName: newDistrict.value.area.name
            };
        }
    }

    addDialogVisible.value = false;
}

function confirmDelete(id) {
    confirm.require({
        message: 'Are you sure you want to delete this district?',
        header: 'Confirm Deletion',
        icon: 'pi pi-exclamation-triangle',
        acceptClass: 'p-button-danger',
        accept: () => {
            deleteDistrict(id);
        }
    });
}

function deleteDistrict(id) {
    districts.value = districts.value.filter((d) => d.id !== id);
}
</script>

<template>
    <div class="card">
        <div class="flex justify-between items-center mb-4">
            <div class="font-semibold text-xl">Districts</div>
            <Button label="Add District" icon="pi pi-plus" @click="openAddDialog" />
        </div>

        <DataTable :value="districts" dataKey="id" showGridlines :rows="10" :paginator="true">
            <template #empty>No districts found.</template>
            <Column field="areaName" header="Area" style="min-width: 10rem"></Column>
            <Column field="name" header="District Name" style="min-width: 14rem"></Column>
            <Column field="latitude" header="Latitude" style="min-width: 10rem"></Column>
            <Column field="longitude" header="Longitude" style="min-width: 10rem"></Column>
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
        :header="dialogMode === 'add' ? 'Add District' : 'Edit District'"
        :style="{ width: '25rem' }"
    >
        <div class="flex flex-col gap-3 mt-3">
            <div class="flex flex-col gap-2">
                <label class="font-medium">Area</label>
                <Dropdown
                    v-model="newDistrict.area"
                    :options="areas"
                    optionLabel="name"
                    placeholder="Select an area"
                />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">District Name</label>
                <InputText v-model="newDistrict.name" placeholder="Enter district name" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">District Latitude</label>
                <InputText v-model="newDistrict.latitude" placeholder="Enter district latitude" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">District Longitude</label>
                <InputText v-model="newDistrict.longitude" placeholder="Enter district longitude" />
            </div>
        </div>

        <template #footer>
            <div class="flex justify-end gap-2">
                <Button label="Cancel" text @click="addDialogVisible = false" />
                <Button :label="dialogMode === 'add' ? 'Add' : 'Save'" @click="saveDistrict" />
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
