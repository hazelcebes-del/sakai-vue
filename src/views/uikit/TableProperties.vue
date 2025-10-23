<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Dropdown from 'primevue/dropdown';

// =======================
// DUMMY DATA DISTRICT UNTUK DROPDOWN
// =======================
const districts = ref([
    { id: 1, name: 'Abraj Quartier' },
    { id: 2, name: 'Doha City' },
    { id: 3, name: 'The Pearl' },
    { id: 4, name: 'Lusail Marina' }
]);

// =======================
// DUMMY DATA PROPERTIES (hardcode)
// =======================
const properties = ref([
    { id: 1, districtName: 'Abraj Quartier', name: 'Tower 1', latitude: '25.3667', longitude: '51.5333' },
    { id: 2, districtName: 'Abraj Quartier', name: 'Tower 2', latitude: '25.3672', longitude: '51.5340' },
    { id: 3, districtName: 'Doha City', name: 'City Center Mall', latitude: '25.3240', longitude: '51.5310' },
    { id: 4, districtName: 'Doha City', name: 'The Gate Mall', latitude: '25.3245', longitude: '51.5300' },
    { id: 5, districtName: 'The Pearl', name: 'Porto Arabia 10', latitude: '25.3730', longitude: '51.5405' },
    { id: 6, districtName: 'The Pearl', name: 'Qanat Quartier 3', latitude: '25.3775', longitude: '51.5430' },
    { id: 7, districtName: 'Lusail Marina', name: 'Crescent Tower A', latitude: '25.4200', longitude: '51.5360' },
    { id: 8, districtName: 'Lusail Marina', name: 'Lusail Tower', latitude: '25.4250', longitude: '51.5400' }
]);

// =======================
// STATE & LOGIC
// =======================
const addDialogVisible = ref(false);

const newProperty = ref({
    district: null,
    name: '',
    latitude: '',
    longitude: ''
});

function openAddDialog() {
    newProperty.value = { district: null, name: '', latitude: '', longitude: '' };
    addDialogVisible.value = true;
}

function addProperty() {
    if (
        newProperty.value.district &&
        newProperty.value.name &&
        newProperty.value.latitude &&
        newProperty.value.longitude
    ) {
        properties.value.push({
            id: properties.value.length + 1,
            districtName: newProperty.value.district.name,
            name: newProperty.value.name,
            latitude: newProperty.value.latitude,
            longitude: newProperty.value.longitude
        });
        addDialogVisible.value = false;
    }
}

function deleteProperty(id) {
    properties.value = properties.value.filter((p) => p.id !== id);
}
</script>

<template>
    <div class="card">
        <div class="flex justify-between items-center mb-4">
            <div class="font-semibold text-xl">Properties</div>
            <Button label="Add Property" icon="pi pi-plus" @click="openAddDialog" />
        </div>

        <DataTable :value="properties" dataKey="id" showGridlines :rows="10" :paginator="true">
            <template #empty>No properties found.</template>
            <Column field="districtName" header="District" style="min-width: 12rem"></Column>
            <Column field="name" header="Property Name" style="min-width: 14rem"></Column>
            <Column field="latitude" header="Latitude" style="min-width: 10rem"></Column>
            <Column field="longitude" header="Longitude" style="min-width: 10rem"></Column>
            <Column header="Actions" style="min-width: 8rem">
                <template #body="{ data }">
                    <div class="flex gap-2">
                        <Button icon="pi pi-pencil" text severity="info" />
                        <Button icon="pi pi-trash" text severity="danger" @click="deleteProperty(data.id)" />
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>

    <!-- Add Property Dialog -->
    <Dialog v-model:visible="addDialogVisible" modal header="Add Property" :style="{ width: '25rem' }">
        <div class="flex flex-col gap-3 mt-3">
            <div class="flex flex-col gap-2">
                <label class="font-medium">District</label>
                <Dropdown
                    v-model="newProperty.district"
                    :options="districts"
                    optionLabel="name"
                    placeholder="Select a district"
                />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">Property Name</label>
                <InputText v-model="newProperty.name" placeholder="Enter property name" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">Property Latitude</label>
                <InputText v-model="newProperty.latitude" placeholder="Enter property latitude" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">Property Longitude</label>
                <InputText v-model="newProperty.longitude" placeholder="Enter property longitude" />
            </div>
        </div>

        <template #footer>
            <div class="flex justify-end gap-2">
                <Button label="Cancel" text @click="addDialogVisible = false" />
                <Button label="Add" @click="addProperty" />
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
