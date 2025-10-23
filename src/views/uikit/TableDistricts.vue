<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Dropdown from 'primevue/dropdown';

// Dummy data area untuk dropdown
const areas = ref([
    { id: 1, name: 'Doha City' },
    { id: 2, name: 'Al Rayyan' },
    { id: 3, name: 'Lusail' }
]);

const districts = ref([]);
const addDialogVisible = ref(false);

const newDistrict = ref({
    area: null,
    name: '',
    latitude: '',
    longitude: ''
});

function openAddDialog() {
    newDistrict.value = { area: null, name: '', latitude: '', longitude: '' };
    addDialogVisible.value = true;
}

function addDistrict() {
    if (
        newDistrict.value.area &&
        newDistrict.value.name &&
        newDistrict.value.latitude &&
        newDistrict.value.longitude
    ) {
        districts.value.push({
            id: districts.value.length + 1,
            ...newDistrict.value,
            areaName: newDistrict.value.area.name
        });
        addDialogVisible.value = false;
    }
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
                        <Button icon="pi pi-pencil" text severity="info" />
                        <Button icon="pi pi-trash" text severity="danger" @click="deleteDistrict(data.id)" />
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>

    <!-- Add Dialog -->
    <Dialog v-model:visible="addDialogVisible" modal header="Add District" :style="{ width: '25rem' }">
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
                <Button label="Add" @click="addDistrict" />
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
