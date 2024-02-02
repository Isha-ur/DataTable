<template>
   <div class="card">
      <DataTable
         :value="nodes"
         :expandedRows.sync="expandedRows"
         dataKey="id"
         editMode="row"
         :editingRows.sync="editingRows"
         @row-edit-save="onRowEditSave"
         ref="dt"
         showGridlines
         :filters="filters"
         class="p-datatable-sm"
         :paginator="true"
         :rows="5"
         :scrollable="true"
         scrollHeight="400px"
      >
         <template #header>
            <div class="table-header-container">
               <div
                  class="table-header flex flex-column md:flex-row md:justify-content-between"
               >
                  <div class="flex">
                     <Button
                        icon="pi pi-plus"
                        class="p-button-primary mr-2"
                        style="margin: 2px; width: 2rem; height: 2rem"
                        @click="AddNewPort2"
                        :title="addPortTitle"
                     />

                     <input
                        type="file"
                        class="input-file"
                        id="file-input"
                        @change="handleFileChange"
                        :title="uploadButtonTitle"
                     />
                     <label
                        for="file-input"
                        class="file-input-button"
                        :title="uploadButtonTitle"
                        style="
                           height: 2rem;
                           width: 2rem;
                           display: flex;
                           align-items: center;
                           justify-content: center;
                        "
                     >
                        <i class="pi pi-upload"></i>
                     </label>

                     <Button
                        icon="pi pi-download"
                        class="p-button-success"
                        style="margin: 2px; width: 2rem; height: 2rem"
                        @click="downloadTableData"
                        :title="downloadButtonTitle"
                     />
                  </div>
                  <span class="p-input-icon-left">
                     <i class="pi pi-search" />
                     <InputText
                        v-model="filerInput"
                        placeholder="Search..."
                        v-on:keyup="handleSearch"
                     />
                  </span>
               </div>
            </div>
         </template>
         <Column
            :expander="true"
            :headerStyle="{ width: '3rem' }"
            :styles="{ 'max-width': '40px' }"
         />

         <Column
            field="port"
            header="PORT"
            :styles="{ 'min-width': '200px' }"
            sortable
         >
            <template #body="slotProps">
               <span v-if="!slotProps.data.editing">{{
                  slotProps.data.port
               }}</span>
               <InputText
                  v-else
                  v-model="slotProps.data[slotProps.column.field]"
                  autofocus
                  type="number"
               />
            </template>
         </Column>

         <Column
            header="ACTION"
            style="margin-left: 0"
            :styles="{ 'min-width': '200px' }"
         >
            <template #body="slotProps">
               <Button
                  type="button"
                  icon="pi pi-plus"
                  class="p-button-rounded p-button-primary p-mr-2"
                  style="margin: 0.2em"
                  :expander="true"
                  @click="addChild2(slotProps.data)"
                  title="add"
               ></Button>

               <Button
                  type="button"
                  icon="pi pi-trash"
                  class="p-button-rounded p-button-danger p-mr-2"
                  style="margin: 0.2em"
                  @click="deleteNode(slotProps)"
                  title="Delete"
               ></Button>

               <Button
                  v-if="!slotProps.data.editing"
                  type="button"
                  icon="pi pi-pencil"
                  class="p-button-rounded p-button-warning"
                  style="margin: 0.2em"
                  @click="startEditing(slotProps)"
                  title="Edit"
               ></Button>

               <Button
                  v-if="slotProps.data.editing"
                  type="button"
                  icon="pi pi-check"
                  class="p-button-rounded p-button-success"
                  style="margin: 0.2em"
                  @click="saveChanges(slotProps)"
                  title="Save Edit"
               ></Button>

               <Button
                  v-if="slotProps.data.editing"
                  type="button"
                  icon="pi pi-times"
                  class="p-button-rounded p-button-secondary"
                  style="margin: 0.2em"
                  @click="cancelEditing(slotProps)"
                  title="Cancel Edit"
               ></Button>
            </template>
         </Column>

         <!-- Children Table -->
         <template #expansion="slotProps">
            <div style="margin-left: 2px" class="childTable">
               <h5
                  v-if="slotProps.data.children.length === 0"
                  style="color: red; margin: 1px"
               >
                  {{ slotProps.data.port }} have no children
               </h5>
               <DataTable
                  v-if="slotProps.data.children.length !== 0"
                  :value="slotProps.data.children"
                  editMode="row"
                  :editingRows.sync="editingRows"
                  @row-edit-save="onRowEditSave"
               >
                  <Column
                     field="port"
                     header="PORT"
                     :styles="{ 'min-width': '318px' }"
                     sortable
                  >
                     <template #body="slotProps">
                        <span v-if="!slotProps.data.editing">{{
                           slotProps.data.port
                        }}</span>
                        <InputText
                           v-else
                           v-model="slotProps.data[slotProps.column.field]"
                           autofocus
                           type="number"
                        />
                     </template>
                  </Column>
                  <Column
                     field="ip"
                     header="IP"
                     :styles="{ 'min-width': '318px' }"
                     sortable
                  >
                     <template #body="slotProps">
                        <span v-if="!slotProps.data.editing">{{
                           slotProps.data.ip
                        }}</span>
                        <InputText
                           v-else
                           v-model="slotProps.data[slotProps.column.field]"
                           autofocus
                        />
                     </template>
                  </Column>
                  <Column header="ACTION" :styles="{ 'min-width': '318px' }">
                     <template #body="slotProps">
                        <Button
                           type="button"
                           icon="pi pi-trash"
                           class="p-button-rounded p-button-danger p-mr-2"
                           style="margin: 0.2em"
                           @click="deleteNode(slotProps)"
                           title="Delete"
                        ></Button>

                        <Button
                           v-if="!slotProps.data.editing"
                           type="button"
                           icon="pi pi-pencil"
                           class="p-button-rounded p-button-warning"
                           style="margin: 0.2em"
                           @click="startEditing(slotProps)"
                           title="Edit"
                        ></Button>

                        <Button
                           v-if="slotProps.data.editing"
                           type="button"
                           icon="pi pi-check"
                           class="p-button-rounded p-button-success"
                           style="margin: 0.2em"
                           @click="saveChanges(slotProps)"
                           title="Save Edit"
                        ></Button>

                        <Button
                           v-if="slotProps.data.editing"
                           type="button"
                           icon="pi pi-times"
                           class="p-button-rounded p-button-secondary"
                           style="margin: 0.2em"
                           @click="cancelEditing(slotProps)"
                           title="Cancel Edit"
                        ></Button>
                     </template>
                  </Column>
               </DataTable>
            </div>
         </template>
      </DataTable>
   </div>
</template>

<script>
import DataTable from "primevue/datatable";
import Column from "primevue/column";
import Button from "primevue/button";
import Dialog from "primevue/dialog";
import InputText from "primevue/inputtext";
import Toolbar from "primevue/toolbar";
import { FilterMatchMode } from "primevue/api";
import FileUpload from "primevue/fileupload";
import Row from "primevue/row";

export default {
   components: {
      DataTable,
      Column,
      Button,
      Dialog,
      InputText,
      Toolbar,
      FilterMatchMode,
      FileUpload,
      Row,
   },
   data() {
      return {
         nodes: null,
         jsonData: null,
         filerInput: "",
         editingRows: [],
         expandedRows: [],
         filters: {},
         productService: null,
         dialogHeader: "",
         displayDialogForEdit: false,
         displayDialogForPort: false,
         displayDialogForChild: false,
         selectedNode: null,
         inputPort: "",
         inputIp: "",
         uploadButtonTitle: "Load data",
         downloadButtonTitle: "Download data",
         addPortTitle: "Add New Port",
         data2: [
            {
               id: "0",
               port: "8080",
               editing: false,
               children: [
                  {
                     id: "0-0",
                     parentId: "0",
                     port: "8081",
                     ip: "123",
                     editing: false,
                  },
                  {
                     id: "0-1",
                     parentId: "0",
                     port: "8082",
                     ip: "122",
                     editing: false,
                  },
                  {
                     id: "0-2",
                     parentId: "0",
                     port: "8082",
                     ip: "124",
                     editing: false,
                  },
               ],
            },
            {
               id: "1",
               port: "8088",
               editing: false,
               children: [
                  {
                     id: "1-0",
                     parentId: "1",
                     port: "8081",
                     ip: "123",
                     editing: false,
                  },
                  {
                     id: "1-1",
                     parentId: "1",
                     port: "8082",
                     ip: "102",
                     editing: false,
                  },
                  {
                     id: "1-2",
                     parentId: "1",
                     port: "8082",
                     ip: "124",
                     editing: false,
                  },
               ],
            },
         ],
      };
   },
   created() {
      this.originalRows = {};
   },

   mounted() {
      let savedData = localStorage.getItem("treeTableData");
      if (!savedData) {
         alert("treeTableData");
         localStorage.setItem("treeTableData", JSON.stringify(this.data2));
      }
      if (savedData) {
         this.nodes = JSON.parse(savedData);
      } else {
         this.nodes = this.data2;
      }
   },

   created() {
      this.initFilters();
   },

   methods: {
      showSuccess(strMsg) {
         console.log(strMsg);

         this.$toast.add({
            styleClass: "width:40px",
            severity: "success",
            summary: strMsg,
            life: 800,
         });
      },
      showError(strMsg) {
         console.log(strMsg);
         this.$toast.add({ severity: "error", summary: strMsg, life: 800 });
      },

      showInfo(strMsg) {
         console.log(strMsg);
         this.$toast.add({ severity: "info", summary: strMsg, life: 800 });
      },

      expandAll() {
         // this.expandedRows = this.nodes.filter((p) => p.id);
         this.expandedRows = this.nodes.filter(function (p) {
            console.log(p.id);
            return p.id;
         });
      },

      collapseAll() {
         this.expandedRows = null;
      },

      onRowEditSave(event) {
         console.log("row save");
         let { newData, index } = event;
         console.log(index);
         console.log(newData);

         if (newData.parentId) {
            let { parentId, childId } = this.getParentId(newData.id);
            this.nodes[parentId].children[childId] = newData;
            localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
            this.showSuccess("Child Edit Successfully");
         } else {
            this.nodes[index] = newData;
            localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
            this.showSuccess("Parent Edit Successfully");
            return;
         }
      },

      AddNewPort2() {
         let nLen = this.nodes.length;
         let objNewPort = {
            id: `${nLen}`,
            port: ``,
            editing: true,
            children: [],
         };
         this.nodes.unshift(objNewPort);
         // save nodes to local storage
         localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
         this.showSuccess("new port added Sucessfully");
      },

      addChild2(parentNode) {
         this.selectedNode = parentNode;
         let nParentKey = this.selectedNode.id;
         let nlengthOfChildren = this.selectedNode.children.length;
         if (parentNode.port === "") {
            this.showError("Please fill the Port by click on edit btn");
            return;
         }

         let objChildData = {
            id: `${nParentKey}-${nlengthOfChildren}`,
            parentId: `${nParentKey}`,
            editing: true,
            port: ``,
            ip: ``,
         };

         this.selectedNode.children.unshift(objChildData);

         localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
         this.displayDialogForChild = false;
         this.showSuccess("Child added Successfully!!");
      },

      getParentId(nid) {
         let str = nid;
         let parts = str.split("-");
         let parentId = parts[0];
         let childId = parts[parts.length - 1];
         if (parentId === undefined) {
            console.log("undefind");
         } else {
            return { parentId, childId };
         }
      },

      deleteNode(node) {
         this.selectedNode = node.data;
         let { parentId, childId } = this.getParentId(this.selectedNode.id);

         if (this.selectedNode.children) {
            let index = this.nodes.findIndex((node) => node.id === parentId);
            this.nodes.splice(index, 1);
            this.showSuccess("delete sucessfully!!");
            localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
            return;
         } else {
            let nCid = this.selectedNode.id;
            console.log(nCid);
            let nParentIndex = this.nodes.findIndex(
               (node) => node.id === parentId
            );
            let index = this.nodes[nParentIndex].children.findIndex(
               (node) => node.id === nCid
            );
            this.nodes[nParentIndex].children.splice(index, 1);
            console.log("children");
            this.showSuccess("delete sucessfully!!");
            localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
         }
      },

      initFilters() {
         this.filters = {
            global: { value: null, matchMode: FilterMatchMode.CONTAINS },
         };
      },

      exportCSV() {
         let csvData = this.convertTreeTableToCSV(this.nodes);
         this.downloadCSV(csvData, "tree_table_data.csv");
      },

      convertTreeTableToCSV(data) {
         let csv = "ID,Port,IP\n"; // Header
         data.forEach((node) => {
            csv += this.convertNodeToCSV(node);
         });

         return csv;
      },

      convertNodeToCSV(node) {
         let csv = `${node.id},${node.port},${node.ip || ""}\n`;
         if (node.children && node.children.length > 0) {
            node.children.forEach((child) => {
               csv += `${child.id},${child.port},${child.ip || ""}\n`;
            });
         }
         return csv;
      },

      downloadCSV(data, filename) {
         let blob = new Blob([data], { type: "text/csv;charset=utf-8;" });
         let link = document.createElement("a");
         if (link.download !== undefined) {
            let url = URL.createObjectURL(blob);
            link.setAttribute("href", url);
            link.setAttribute("download", filename);
            link.style.visibility = "hidden";
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
         }
      },

      startEditing(slotProps) {
         let { data } = slotProps;
         data.editing = true;
         showInfo("Editing is Enabel");
      },

      uniquePort(port, nodes) {
         console.log("nodes", nodes.length);
         console.log("port", port);
         for (let i = 0; i < nodes.length; i++) {
            console.log(nodes[i]);
            if (nodes[i].port == port) {
               console.log("false");
               return false;
            }
         }
         return true;
      },

      saveChanges(slotProps) {
         let { data } = slotProps;
         let originalData;
         try {
            originalData =
               JSON.parse(localStorage.getItem("treeTableData")) || [];
         } catch (error) {
            console.error(
               "Error parsing treeTableData from localStorage:",
               error
            );
            return;
         }
         /*if (
            !data.parentId &&
            this.uniquePort(data.port.trim(), originalData) === false
         ) {
            data.port = "";
            this.showError("This port already exists");
            return;
         }*/

         data.editing = false;
         // Update local storage with the modified data
         localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
         this.showSuccess("Edit Successfully");
      },

      cancelEditing(slotProps) {
         let { data } = slotProps;
         data.editing = false;
         this.showInfo("Edit Cancel");
      },

      downloadTableData() {
         let jsonData = JSON.stringify(this.nodes, null, 2);
         let blob = new Blob([jsonData], { type: "application/json" });
         let link = document.createElement("a");
         link.href = URL.createObjectURL(blob);
         link.download = "tableData.json";
         link.click();
      },

      handleSearch() {
         const searchTerm = this.filerInput.trim().toLowerCase();
         let localHostData = JSON.parse(localStorage.getItem("treeTableData"));
         console.log("localhost", localHostData);
         const originalNodes = localHostData;
         const filteredNodes = originalNodes.filter((node) => {
            return (
               node.port.toLowerCase().includes(searchTerm) ||
               this.searchInChild(searchTerm, node.children)
            );
         });
         this.nodes = filteredNodes;
         this.expandAll();
         if (searchTerm === "") {
            this.collapseAll();
         }
      },

      searchInChild(searchTerm, nodes) {
         for (let i = 0; i < nodes.length; i++) {
            if (
               nodes[i].port.toLowerCase().includes(searchTerm) ||
               nodes[i].ip.toLowerCase().includes(searchTerm)
            ) {
               return true;
            }
         }
         return false;
      },

      handleFileChange(event) {
         const file = event.target.files[0];
         if (file) {
            this.readFile(file);
         }
      },

      readFile(file) {
         const reader = new FileReader();

         reader.onload = () => {
            try {
               const jsonData = JSON.parse(reader.result);
               this.jsonData = jsonData;
               this.nodes = jsonData;
               localStorage.setItem("treeTableData", JSON.stringify(jsonData));
               this.showSuccess("File loaded sucessfully!!");
            } catch (error) {
               console.error("Error parsing JSON file:", error);
               this.showError("Error parsing JSON file");
            }
         };

         reader.readAsText(file);
      },
   },
};
</script>

<style scoped>
.card {
   margin: 10px auto;
   max-width: 100%;
   display: flex;
   justify-content: center;
   align-items: center;
   flex-direction: column;
}

.card .p-datatable {
   width: 100%;
   max-width: 100%;
   overflow-x: hidden;
}

/* Style adjustments for small datatables */
::v-deep .p-datatable.p-datatable-sm .p-datatable-tbody > tr > td {
   padding: 0 0 0 6px;
}

::v-deep .pi {
   font-size: 0.8rem;
}

/* Style adjustments for small buttons */
::v-deep .p-button.p-button-icon-only {
   width: 1.6rem;
   padding: 0px;
}

::v-deep .p-button.p-button-icon-only.p-button-rounded {
   border-radius: 50%;
   height: 1.6rem;
}

/* Responsive adjustments for datatables with resizable columns */
/* ::v-deep .p-datatable-resizable > .p-datatable-wrapper {
    overflow-x: auto;
    padding-top: 0px;
    padding-bottom: 21px;
 } */

/* Flex styles for alignment */
.flex {
   display: flex;
   align-items: center;
   justify-content: center;
}

/* Responsive Styles */
@media screen and (max-width: 768px) {
   .card {
      max-width: 100%;
   }
}

/* Row background colors for light and dark */
::v-deep .light-row {
   background-color: rgb(180, 179, 179); /* Light grey color */
}

::v-deep .dark-row {
   background-color: #e6e6e6; /* Dark grey color */
}

/* Styling for file input */
.input-file {
   opacity: 0;
   width: 0.1px;
   height: 0.1px;
   position: absolute;
   overflow: hidden;
   z-index: -1;
}

.file-input-button {
   cursor: pointer;
   color: #ffffff;
   background: #2196f3;
   border: 1px solid #2196f3;
   padding: 0.5rem 1rem;
   font-size: 1rem;
   transition: background-color 0.2s, color 0.2s, border-color 0.2s,
      box-shadow 0.2s;
   border-radius: 3px;
}

/* DataTable Header Styles */
.table-header {
   display: flex;
   align-items: center;
   justify-content: space-between;

   @media screen and (max-width: 960px) {
      align-items: start;
   }
}

::v-deep .p-datatable .p-datatable-thead > tr > th {
   text-align: left;
   padding: 1rem 1rem;
   border: 1px solid #e9ecef;
   border-width: 0 0 1px 0;
   font-weight: 600;
   color: #414142;
   transition: box-shadow 0.2s;
   font-size: 14px;
}
::v-deep .p-datatable .p-datatable-tbody > tr {
   background: #fff;
   color: #383838;
   transition: box-shadow 0.2s;
   font-weight: 500;
   font-size: 14px;
}
::v-deep .p-datatable-resizable > .p-datatable-wrapper {
   overflow-x: auto;
   padding-top: 0px;
   padding-bottom: 0;
   padding: 6px 0px;
}
::v-deep .p-datatable.p-datatable-gridlines .p-paginator-bottom {
   border-width: 0 1px 1px 1px;
   padding: 0;
}
/* ::v-deep .p-datatable.p-datatable-sm .p-datatable-header {
    padding: 0.2rem 0.2rem;
} */
::v-deep .p-paginator .p-paginator-first,
.p-paginator .p-paginator-prev,
.p-paginator .p-paginator-next,
.p-paginator .p-paginator-last {
   background-color: transparent;
   border: 0 none;
   color: #6c757d;
   min-width: 1.357rem;
   /* height: 2.357rem; */
   margin: 0.143rem;
   transition: box-shadow 0.2s;
   border-radius: 3px;
}
::v-deep .p-paginator .p-paginator-pages .p-paginator-page.p-highlight {
   background: none;
   color: #495057;
}
::v-deep .p-paginator .p-paginator-pages .p-paginator-page {
   background-color: transparent;
   border: 0 none;
   color: #6c757d;
   min-width: 2.357rem;
   height: 1.357rem;
   margin: 0.143rem;
   transition: box-shadow 0.2s;
   border-radius: 3px;
}
::v-deep .p-datatable .p-datatable-tbody > tr > td .p-row-toggler:enabled:hover,
.p-datatable .p-datatable-tbody > tr > td .p-row-editor-init:enabled:hover,
.p-datatable .p-datatable-tbody > tr > td .p-row-editor-save:enabled:hover,
.p-datatable .p-datatable-tbody > tr > td .p-row-editor-cancel:enabled:hover {
   background: none;
}

::v-deep .p-datatable .p-datatable-tbody > tr > td .p-row-toggler:focus,
.p-datatable .p-datatable-tbody > tr > td .p-row-editor-init:focus,
.p-datatable .p-datatable-tbody > tr > td .p-row-editor-save:focus,
.p-datatable .p-datatable-tbody > tr > td .p-row-editor-cancel:focus {
   box-shadow: none;
}
::v-deep .p-datatable-scrollable .p-datatable-thead {
   position: static;
   z-index: 1;
}

::v-deep
   .p-datatable.p-datatable-scrollable
   > .p-datatable-wrapper
   > .p-datatable-table
   > .p-datatable-thead,
.p-datatable.p-datatable-scrollable
   > .p-datatable-wrapper
   > .p-datatable-table
   > .p-datatable-tfoot {
   background-color: #f8f9fa;
   position: sticky;
   z-index: 1;
}
</style>
