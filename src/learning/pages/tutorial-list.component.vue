<template>
    <div>
      <div class="card">
        <pv-toolbar class="mb-4">
          <template #start>
            <pv-button
                label="New"
                icon="pi pi-plus"
                class="p-button-success mr-2"
                @click=""
            />
            <pv-button
                label="Delete"
                icon="pi pi-trash"
                class="p-button-danger"
                @click=""
                :disabled="true"
            />
          </template>
  
          <template #end>
            <pv-button
                label="Export"
                icon="pi pi-download"
                class="p-button-help"
                @click="exportToCSV($event)"
            />
          </template>
        </pv-toolbar>
  
        <pv-data-table
            ref="dt"
            :value="tutorials"
            v-model:selection="selectedTutorials"
            dataKey="id"
            :paginator="true"
            :rows="10"
            :filters="filters"
            paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
            :rowsPerPageOptions="[5, 10, 25]"
            currentPageReportTemplate="Showing {first} to {last} of {totalRecords} tutorials"
            responsiveLayout="scroll"
        >
          <template #header>
            <div class="table-header flex flex-column md:flex-row md:justify-content-between">
              <h5 class="mb-2 md:m-0 p-as-md-center text-xl">Manage Tutorials</h5>
              <span class="p-input-icon-left">
                <i class="pi pi-search" />
                <pv-input-text
                    v-model="filters['global'].value"
                    placeholder="Search..."
                />
              </span>
            </div>
          </template>

  
          <pv-column
              selectionMode="multiple"
              style="width: 3rem"
              :exportable="false"
          ></pv-column>
          <pv-column
              field="id"
              header="Id"
              :sortable="true"
              style="min-width: 12rem"
          ></pv-column>
          <pv-column
              field="title"
              header="Title"
              :sortable="true"
              style="min-width: 16rem"
          ></pv-column>
          <pv-column
              field="description"
              header="Description"
              :sortable="true"
              style="min-width: 16rem"
          ></pv-column>
          <pv-column
              field="status"
              header="Status"
              :sortable="true"
              style="min-width: 12rem"
          >
              <template #body="slotProps">
                  <pv-tag v-if="slotProps.data.status === 'Published'" severity="success">
                      {{ slotProps.data.status }}
                  </pv-tag>
                  <pv-tag v-else severity="info">{{ slotProps.data.status }}</pv-tag>
              </template>
          </pv-column>
          <pv-column :exportable="false" style="min-width: 8rem">
              <template #body="slotProps">
                  <pv-button
                      icon="pi pi-pencil"
                      class="p-button-text p-button-rounded"
                      @click=""
                  />
                  <pv-button
                      icon="pi pi-trash"
                      class="p-button-text p-button-rounded"
                      @click=""
                  />
              </template>
          </pv-column>
        </pv-data-table>
      </div>
  
    </div>
  </template>
  
  <script>
  import { TutorialsApiService } from "../services/tutorials-api.service";
  import { FilterMatchMode } from "primevue/api";
  
  export default {
    name: "tutorial-list",
    data() {
      return {
        tutorials: [],
        tutorial: {},
        selectedTutorials: null,
        statuses: [
          { label: "Published", value: "published" },
          { label: "Unpublished", value: "unpublished" },
        ],
        tutorialsService: null,
        filters: {},
      };
    },
    created() {
      this.tutorialsService = new TutorialsApiService();
      this.tutorialsService.getAll()
          .then((response) => {
              this.tutorials = response.data;
              console.log(this.tutorials);
              this.tutorials.forEach(
                  (tutorial) => this.getDisplayableTutorial(tutorial)
              );
          console.log(this.tutorials);
      });
      this.initFilters();
    },
  
    methods: {
      getDisplayableTutorial(tutorial) {
        tutorial.status = tutorial.published ? this.statuses[0].label : this.statuses[1].label;
        return tutorial;
      },
      initFilters() {
        this.filters = {
          global: { value: null, matchMode: FilterMatchMode.CONTAINS },
        };
      },
      exportToCSV() {
        this.$refs.dt.exportCSV();
      },
    },
  };
  </script>
  
  <style scoped>
  .table-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    @media screen and (max-width: 960px) {
      align-items: start;
    }
  }
  
  @media screen and (max-width: 960px) {
    ::v-deep(.p-toolbar) {
      flex-wrap: wrap;
      .p-button {
        margin-bottom: 0.25rem;
      }
    }
  }
  </style>
  