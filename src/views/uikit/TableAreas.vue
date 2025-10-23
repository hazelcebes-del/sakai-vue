<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';

// =======================
// DATA DUMMY (hardcode)
// =======================
const areas = ref([
    { id: 1, name: 'Central Park', latitude: '-6.1751', longitude: '106.8650' },
    { id: 2, name: 'Downtown Area', latitude: '-6.2000', longitude: '106.8167' },
    { id: 3, name: 'Harbor District', latitude: '-6.1214', longitude: '106.7741' },
    { id: 4, name: 'Industrial Zone', latitude: '-6.1100', longitude: '106.8700' },
    { id: 5, name: 'Residential Area', latitude: '-6.1900', longitude: '106.8451' },
    { id: 6, name: 'Mountain View', latitude: '-6.2500', longitude: '106.9500' },
    { id: 7, name: 'Lakeside', latitude: '-6.2100', longitude: '106.8000' },
    { id: 8, name: 'Business Park', latitude: '-6.1700', longitude: '106.8300' },
    { id: 9, name: 'University District', latitude: '-6.3600', longitude: '106.8200' },
    { id: 10, name: 'Airport Area', latitude: '-6.1300', longitude: '106.6550' }
]);

// =======================
// STATE DAN LOGIC
// =======================
const addDialogVisible = ref(false);

const newArea = ref({
    name: '',
    latitude: '',
    longitude: ''
});

function openAddDialog() {
    newArea.value = { name: '', latitude: '', longitude: '' };
    addDialogVisible.value = true;
}

function addArea() {
    if (newArea.value.name && newArea.value.latitude && newArea.value.longitude) {
        areas.value.push({
            id: areas.value.length + 1,
            ...newArea.value
        });
        addDialogVisible.value = false;
    }
}

function deleteArea(areaId) {
    areas.value = areas.value.filter((a) => a.id !== areaId);
}
</script>

<template>
    <div class="card">
        <div class="flex justify-between items-center mb-4">
            <div class="font-semibold text-xl">Areas</div>
            <Button label="Add Area" icon="pi pi-plus" @click="openAddDialog" />
        </div>

        <DataTable :value="areas" dataKey="id" showGridlines :rows="10" :paginator="true">
            <template #empty>No areas found.</template>
            <Column field="name" header="Area Name" style="min-width: 14rem"></Column>
            <Column field="latitude" header="Latitude" style="min-width: 10rem"></Column>
            <Column field="longitude" header="Longitude" style="min-width: 10rem"></Column>
            <Column header="Actions" style="min-width: 8rem">
                <template #body="{ data }">
                    <div class="flex gap-2">
                        <Button icon="pi pi-pencil" text severity="info" />
                        <Button icon="pi pi-trash" text severity="danger" @click="deleteArea(data.id)" />
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>

    <!-- Add Dialog -->
    <Dialog v-model:visible="addDialogVisible" modal header="Add Area" :style="{ width: '25rem' }">
        <div class="flex flex-col gap-3 mt-3">
            <div class="flex flex-col gap-2">
                <label class="font-medium">Area Name</label>
                <InputText v-model="newArea.name" placeholder="Enter area name" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">Area Latitude</label>
                <InputText v-model="newArea.latitude" placeholder="Enter area latitude" />
            </div>

            <div class="flex flex-col gap-2">
                <label class="font-medium">Area Longitude</label>
                <InputText v-model="newArea.longitude" placeholder="Enter area longitude" />
            </div>
        </div>

        <template #footer>
            <div class="flex justify-end gap-2">
                <Button label="Cancel" text @click="addDialogVisible = false" />
                <Button label="Add" @click="addArea" />
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
