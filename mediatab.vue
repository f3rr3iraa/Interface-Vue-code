<template>
  <div class="container-fluid">
    <div class="row">
      <!-- DATATABLE -->
      <DataTable :isPreview="true" title="Media" :tableData="tableData" :headersData="tableDataHeaders" keyId="recordid"
        :enableCreate=true @createMe="openCreateRecord($event)" :enableEdit=true @editMe="openEditRecord($event)"
        :enableDelete="true" @deleteMe="deleteRecord($event)" :OperationIDAdd="362" :OperationIDDel="362"
        :OperationIDEdit="362" :OperationIDExport="362">
      </DataTable>

      <!-- SIDEBARS -->
      <Sidebar :showCloseIcon="false" v-model:visible="isSidebarVisible" style="width:60%" position="right">
        <MediaHeader :editingID="editingID" :curRecord="curRecord" @closeModal="closeModal"></MediaHeader>
      </Sidebar>

    </div>
  </div>
</template>

<script>
  import DataTable from "@/components/s2i/DataTable.vue";
  import Sidebar from "primevue/sidebar";
  import MediaHeader from "../Headers/MediaHeader.vue";

  export default {
    name: "Media",
    components: {
      DataTable,
      Sidebar,
      MediaHeader,
    },
    data() {
      return {
        isSidebarVisible: false,
        editingID: 0,
        curRecord: {},
        tableData: [],
        tableDataHeaders: [
          {
            objectKey: "docdate",
            objectTranslate: "docdate",
            objectType: "date",
          },
          {
            objectKey: "expiredate",
            objectTranslate: "expiredate",
            objectType: "date",
          },
          {
            objectKey: "warningdate",
            objectTranslate: "warningdate",
            objectType: "date",
          },
          {
            objectKey: "remarks",
            objectTranslate: "remarks",
            objectType: "text",
          },
          {
            objectKey: "imagebit",
            objectTranslate: "imagebit",
            objectType: "checkbox",
          },
          {
            objectKey: "imageupdatedate",
            objectTranslate: "imageupdatedate",
            objectType: "datetime-local",
          },
          {
            objectKey: "pathtoimage",
            objectTranslate: "pathtoimage",
            objectType: "text",
          },
          {
            objectKey: "type",
            objectTranslate: "type",
            objectType: "text",
          },
        ],
      };
    },
    methods: {
      async fetchTableData() {
        this.tableData = (await this.execRoute(import.meta.env.VITE_APP_API_URL + "/v1/equipments/" + this.$route.params.id + "/equipmentmedias", null, "GET")).data
      },
      async deleteRecord(id) {
        let _res = await this.execRoute(import.meta.env.VITE_APP_API_URL + "/v1/equipments/" + this.$route.params.id + "/equipmentmedias/" + id, null, "DELETE");

        if (_res.result == "OK") {
          this.fetchTableData();
        }
      },
      openEditRecord(event) {
        this.editingID = event.id;
        this.curRecord = event.data;
        this.isSidebarVisible = true;
      },
      openCreateRecord() {
        this.editingID = 0;
        this.curRecord = {}
        this.isSidebarVisible = true;
      },
      closeModal() {
        this.fetchTableData();
        this.editingID = 0;
        this.curRecord = {}
        this.isSidebarVisible = false;
      },
    },
    created() {
      this.fetchTableData();
    },
  }
</script>