<template>
    <div class="custom-tree-table  .scrollable-container p-mb-4">
      <!-- <div class="scrollable-container"> -->
      <TreeTable
        :value="nodes"
        :responsive="true"
        class="p-treetable-sm p-treetable-scrollable-body"
        :paginator="true"
        :rows="5"
        :filters="filters"
        filterMode="lenient"
        responsiveLayout="scroll"
        removableSort
      >
        <template #header>
          <div class="custom-tree-table-header">
        <div class="header-buttons">
          <Button
            type="button"
            class="p-button-primary p-mr-2"
            style="margin-right: 0.5em"
            @click="AddNewPort"
          >
            Add Port
          </Button>
  
          <FileUpload
            mode="basic"
            name="demo[]"
            url="./"
            accept=".json"
            :maxFileSize="1000000"
            @upload="onUpload"
            chooseLabel="Load"
            class="p-mr-2"
            style="margin-right: 0.5em"
            error="onError"
          >
          </FileUpload>
        </div>
        <div>
          <span class="p-input-icon-left">
            <i class="pi pi-search" />
            <InputText
              type="text"
              v-model="filters['global']"
              placeholder="Search"
              class="search-input"
            />
          </span>
        </div>
      </div>
  
        </template>
        <Column field="port" header="PORT" :expander="true" :sortable="true"></Column>
        <Column field="ip" header="IP" :sortable="true">
          <template #filter>
            <InputText
              type="text"
              v-model="filters['size']"
              class="p-column-filter"
            />
          </template>
        </Column>
       
        <Column header="Actions">
          <template #body="slotProps">
            <Button
              v-if="slotProps.node.children"
              type="button"
              icon="pi pi-plus"
              class="p-button-rounded p-button-primary p-mr-2"
              style="margin-right: 0.5em"
              @click="addChild(slotProps.node)"
            ></Button>
            <Button
              v-else-if="slotProps.node.add"
              type="button"
              icon="pi pi-plus"
              class="p-button-rounded p-button-primary p-mr-2"
              style="margin-right: 0.5em"
              @click="addChild(slotProps.node)"
            ></Button>
            <Button
              v-if="slotProps.node"
              type="button"
              icon="pi pi-pencil"
              class="p-button-rounded p-button-warning p-mr-2"
              style="margin-right: 0.5em"
              @click="editNode(slotProps.node)"
            ></Button>
            <Button
              v-if="slotProps.node"
              type="button"
              icon="pi pi-trash"
              class="p-button-rounded p-button-danger p-mr-2"
              style="margin-right: 0.5em"
              @click="deleteNode(slotProps.node)"
            ></Button>
            <Button
                  v-if="!slotProps.node.editing"
                  type="button"
                  icon="pi pi-pencil"
                  class="p-button-rounded p-button-warning"
                  style="margin: 0.2em"
                  @click="startEditing(slotProps)"
                  title="Edit"
               ></Button>

               <Button
                  v-if="slotProps.node.editing"
                  type="button"
                  icon="pi pi-check"
                  class="p-button-rounded p-button-success"
                  style="margin: 0.2em"
                  @click="saveChanges(slotProps)"
                  title="Save Edit"
               ></Button>

               <Button
                  v-if="slotProps.node.editing"
                  type="button"
                  icon="pi pi-times"
                  class="p-button-rounded p-button-secondary"
                  style="margin: 0.2em"
                  @click="cancelEditing(slotProps)"
                  title="Cancel Edit"
               ></Button>
            <!-- <Button
                      v-if="!slotProps.node.children"
                      type="button"
                      icon="pi pi-pencil"
                      class="p-button-rounded p-button-warning p-mr-2"
                      style="margin-right: .5em"
                      @click="editNode(slotProps.node)"
                    ></Button>
                    <Button
                      v-if="!slotProps.node.children"
                      type="button"
                      icon="pi pi-trash"
                      class="p-button-rounded p-button-danger p-mr-2"
                      style="margin-right: .5em"
                      @click="deleteNode(slotProps.node)"
                    ></Button> -->
          </template>
        </Column>
        <template #footer>
          <!-- <div class="p-text-left">
            <Button icon="pi pi-refresh" class="p-button-rounded p-button-text" />
          </div> -->
        </template>
      </TreeTable>
      <!-- </div> -->
  
      <!-- for edit -->
      <Dialog :visible.sync="displayDialog" :header="dialogHeader">
        <div class="p-fluid">
          <div class="p-field">
            <label for="nodeName">Name</label>
            <InputText id="nodeName" v-model="inputText" />
          </div>
          <div class="p-field">
            <label for="nodeSize">Size</label>
            <InputText id="nodeSize" v-model="inputSize" />
          </div>
          <div class="p-field">
            <label for="nodeType">Type</label>
            <InputText id="nodeType" v-model="inputType" />
          </div>
        </div>
        <div class="p-mt-4">
          <!-- <Button label="Save" icon="pi pi-check" class="p-button-rounded p-button-success" @click="saveEdit" /> -->
          <Button
            label="Save"
            icon="pi pi-check"
            class="p-button-rounded p-button-success"
            @click="saveEdit"
          />
          <Button
            label="Cancel"
            icon="pi pi-times"
            class="p-button-rounded p-button-secondary"
            @click="cancelEdit(true)"
          />
        </div>
      </Dialog>
      <!-- for Add New Port -->
      <Dialog :visible.sync="displayDialogFromAdd" :header="dialogHeader">
        <div class="p-fluid">
          <div class="p-field">
            <label for="nodeName">Name</label>
            <InputText id="nodeName" v-model="inputText" />
          </div>
          <div class="p-field">
            <label for="nodeSize">Size</label>
            <InputText id="nodeSize" v-model="inputSize" />
          </div>
          <div class="p-field">
            <label for="nodeType">Type</label>
            <InputText id="nodeType" v-model="inputType" />
          </div>
        </div>
        <div class="p-mt-4">
          <Button
            label="Add Data"
            icon="pi pi-check"
            class="p-button-rounded p-button-success"
            @click="SaveNewPort"
          />
          <!-- <Button label="Cancel" icon="pi pi-times" class="p-button-rounded p-button-secondary" @click="cancelEdit(true)" /> -->
        </div>
      </Dialog>
      <!-- for Add New Port children -->
      <Dialog :visible.sync="displayDialogFromChild" :header="dialogHeader">
        <div class="p-fluid">
          <div class="p-field">
            <label for="nodeName">Name</label>
            <InputText id="nodeName" v-model="inputText" />
          </div>
          <div class="p-field">
            <label for="nodeSize">Size</label>
            <InputText id="nodeSize" v-model="inputSize" />
          </div>
          <div class="p-field">
            <label for="nodeType">Type</label>
            <InputText id="nodeType" v-model="inputType" />
          </div>
        </div>
        <div class="p-mt-4">
          <Button
            label="Add Data"
            icon="pi pi-check"
            class="p-button-rounded p-button-success"
            @click="saveChild"
          />
          <!-- <Button label="Cancel" icon="pi pi-times" class="p-button-rounded p-button-secondary" @click="cancelEdit(true)" /> -->
        </div>
      </Dialog>
    </div>
  </template>
  
  <script>
  import TreeTable from "primevue/treetable";
  import Column from "primevue/column";
  import Button from "primevue/button";
  import Dialog from "primevue/dialog";
  import InputText from "primevue/inputtext";
  import DataTable from "primevue/datatable";
  import FileUpload from "primevue/fileupload";
  
  export default {
    components: {
      TreeTable,
      Column,
      Button,
      Dialog,
      InputText,
      DataTable,
      FileUpload,
    },
    data() {
      return {
        nodes: null,
        filters: {},
        inputText: "",
        inputSize: "",
        inputType: "",
        displayDialog: false,
        displayDialogFromAdd: false,
        selectedNode: null,
        dialogHeader: "",
        displayDialogFromChild: false,
        data: [
          {
            key: "0",
            data: {
              port: "8080",
              editing: false,
            },
            children: [
              {
                key: "0-0",
                data: {
                  port: "8908",
                  ip: "123.123",
                  editing: false,
                },
              },
              {
                key: "0-1",
                data: {
                  port: "8908",
                  ip: "123.123",
                  editing: false,
                },
              },
              {
                key: "0-2",
                data: {
                  port: "8908",
                  ip: "123.123",
                  editing: false,
                },
              },
            ],
          },
          {
            key: "1",
            data: {
              port: "7674",
              editing: false,
              
            },
            children: [
              {
                key: "1-0",
                data: {
                  port: "8908",
                  ip: "123.123",
                  editing: false,
                },
              },
              {
                key: "1-1",
                data: {
                  port: "8908",
                  ip: "123.123",
                  editing: false,
                },
              },
            ],
          },
          
        ],
      };
    },
    mounted() {
      // Try to load data from localStorage
      const savedData = localStorage.getItem("treeTableData");
      if (!savedData) {
        alert("treeTableData");
        localStorage.setItem("treeTableData", JSON.stringify(this.data));
      }
      if (savedData) {
        this.nodes = JSON.parse(savedData);
        //alert("data saved")
      } else {
        // If no saved data, use the initial data
        this.nodes = this.data;
      }
    },
    methods: {
      showSuccess(strMsg) {
        this.$toast.add({ severity: "success", summary: strMsg, life: 3000 });
      },
      showError(strMsg) {
        this.$toast.add({ severity: "error", summary: strMsg, life: 3000 });
      },
      showInfo(strMsg) {
        this.$toast.add({ severity: "info", summary: strMsg, life: 3000 });
      },
      /*
      editNode(node) {
        this.selectedNode = node;
        this.inputText = node.data.name;
        this.inputSize = node.data.size;
        this.inputType = node.data.type;
        this.displayDialog = true;
        this.dialogHeader = "Edit Node";
      },
      saveEdit() {
        if (this.selectedNode) {
          this.selectedNode.data.name = this.inputText.trim();
          this.selectedNode.data.size = this.inputSize.trim();
          this.selectedNode.data.type = this.inputType.trim();
          this.cancelEdit(false);
  
          // Save the updated data to localStorage
          localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
          this.displayDialog = false;
          this.showSuccess("Edit Successfully");
        }
      },
      cancelEdit(bIsSave) {
        this.displayDialog = false;
        this.selectedNode = null;
  
        if (bIsSave) {
          this.showInfo("Edit Discarded!!");
        }
      },
      getParentKey(key) {
        let lastDashIndex = key.lastIndexOf("-");
        return lastDashIndex !== -1 ? key.slice(0, lastDashIndex) : null;
      },
      deleteNode(node) {
        let parentKey;
        if (node.parent) {
          parentKey = node.parent.key;
        } else {
          parentKey = this.getParentKey(node.key);
        }
  
        if (parentKey) {
          let parentNode = this.findNodeByKey(this.nodes, parentKey);
  
          if (parentNode) {
            let index = parentNode.children.indexOf(node);
            if (index !== -1) {
              parentNode.children.splice(index, 1);
              this.showSuccess("Delete Successfully!!");
            } else {
              this.showError("Node not found in parent's children");
            }
          } else {
            this.showError("Parent node not found");
          }
        } else {
          let index = this.nodes.indexOf(node);
          if (index !== -1) {
            this.nodes.splice(index, 1);
            this.showSuccess("Delete Successfully!!");
          } else {
            this.showError("Node not found in root nodes");
          }
        }
  
        // Save the updated data to localStorage
        localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
      },
      findNodeByKey(nodes, key) {
        for (let node of nodes) {
          if (node.key === key) {
            return node;
          }
          if (node.children) {
            let foundNode = this.findNodeByKey(node.children, key);
            if (foundNode) {
              return foundNode;
            }
          }
        }
        return null;
      },
      addChild(parentNode) {
        this.selectedNode = parentNode;
        this.displayDialogFromChild = true;
        this.dialogHeader = "Add Child Node";
        this.inputText = "";
        this.inputSize = "";
        this.inputType = "";
      },
      saveChild() {
        console.log("save child called");
        console.log(this.selectedNode);
        let nParentKey = this.selectedNode.key;
        let nlengthOfChildren = this.selectedNode.children.length;
        console.log(
          `parentkey ${nParentKey}, childrenLenght ${nlengthOfChildren}`
        );
        let objChildData = {
          key: `${nParentKey}-${nlengthOfChildren}`,
          data: {
            name: `${this.inputText.trim()}`,
            size: `${this.inputSize.trim()}`,
            type: `${this.inputType.trim()}`,
          },
        };
        this.selectedNode.children.push(objChildData);
        localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
        this.displayDialogFromChild = false;
        this.showSuccess("Child added Successfully!!");
      },
      AddNewPort() {
        this.dialogHeader = "Add Port";
        this.displayDialogFromAdd = true;
        this.inputText = "";
        this.inputSize = "";
        this.inputType = "";
      },
      SaveNewPort() {
        console.log(this.nodes);
        let nLen = this.nodes.length;
        let objNewPort = {
          key: `${nLen}`,
          data: {
            name: `${this.inputText.trim()}`,
            size: `${this.inputSize.trim()}`,
            type: `${this.inputType.trim()}`,
          },
          children: [],
          add: true,
        };
        console.log(objNewPort.key);
        this.nodes.push(objNewPort);
        // save nodes to local storage
        localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
        this.displayDialogFromAdd = false;
        this.showSuccess("New Port Add Successfully!!");
      },
      // Updated onUpload method for handling .json file upload
      onUpload(event) {
        console.log("call");
        const reader = new FileReader();
        reader.onload = (e) => {
          try {
            const jsonData = JSON.parse(e.target.result);
            console.log(jsonData);
            this.nodes = jsonData;
            localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
            this.showSuccess("File Uploaded Successfully");
          } catch (error) {
            this.showError("Error parsing uploaded file");
          }
        };
        reader.readAsText(event.files[0]);
      },
      */
      startEditing(slotProps) {
        console.log("edit")
        console.log(slotProps.node.node)
         let { data } = slotProps.node;
         console.log(data)
         //data.editing = true;
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
         if (
            !data.parentId &&
            this.uniquePort(data.port.trim(), originalData) === false
         ) {
            data.port = "";
            this.showError("This port already exists");
            return;
         }
         data.editing = false;
         // Update local storage with the modified data
         localStorage.setItem("treeTableData", JSON.stringify(this.nodes));
         this.showInfo("Edit Successfully");
      },

      cancelEditing(slotProps) {
         let { data } = slotProps;
         data.editing = false;
         this.showInfo("Edit Cancel");
      },

    },
  };
  </script>
  
  <style scoped>
  .custom-tree-table {
    background-color: #f5f5f5;
    border: 1px solid #ddd;
    padding: 15px;
    border-radius: 10px;
  }
  
  .custom-tree-table .p-button-rounded {
    border-radius: 0.25rem;
  }
  
  .custom-tree-table .p-tree-table .p-treetable-tbody > tr > td {
    font-size: 14px;
  }
  
  .custom-tree-table .p-treetable-row {
    max-width: 200px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  
  .custom-tree-table .scrollable-container {
    height: 400px; /* Adjust the height as needed */
    overflow-y: auto;
  }
  
  .custom-tree-table .p-inputtext {
    width: 100%;
  }
  
  .custom-tree-table .p-treetable th,
  .custom-tree-table .p-treetable td {
    border: 1px solid #ddd;
  }
  
  .custom-tree-table-header {
    background-color: #b9bbbd;
    color: #fff;
    padding: 10px;
    border-radius: 10px 10px 0 0;
    display: flex;
    justify-content: space-between;
  }
  
  
  .header-buttons {
    display: flex;
    align-items: center;
  }
  
  .header-buttons Button {
    margin-right: 0.5em;
  }
  </style>
  
  