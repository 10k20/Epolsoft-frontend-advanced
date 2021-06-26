<template>
  <div class="app-wrapper">
    <a class="export-button">
        <button @click="exportData">Export</button>
      </a>
      <div class="upload-button">
        <label for="file-input" class="files-input-button" @change="handleFileUpload">
          Upload
        </label>
        <input type="file" id="file-input" class="files-input" @change="handleFileUpload">
      </div>
      <div class="search">
        <label for="">Search</label>
        <input type="text" id="search" v-model="search" @input="searchData">
      </div>
    <div class="data-table">
   
      <div class="table-row first">
        <div @click="sortTable('sno')" class="sno cell"><span>SNO</span></div>
        <div @click="sortTable('emp')" class="emp cell"><span>Emp#</span></div>
        <div @click="sortTable('email')" class="email cell"><span>Email</span></div>
        <div @click="sortTable('firstName')" class="first-name cell"><span>First Name</span></div>
        <div @click="sortTable('lastName')" class="last-name cell"><span>Last Name</span></div>
        <div @click="sortTable('desginatin')" class="desginatin cell"><span>Desginatin</span></div>
        <div @click="sortTable('projectName')" class="project-name cell"><span>Project Name</span></div>
        <div class="actions cell">
          <span>Actions</span>
        </div>
      </div>
      <div class="table-row" v-for="(data, index) in tableData" :key="index">
        <div class="sno cell">
          <span>{{ data.id + 1 }}</span></div>
        <div class="emp cell">
          <input type="text" v-model="data.emp" :disabled='data.disabled' @input="equate">
        </div>
        <div class="email cell">
          <input type="text" v-model="data.email" :disabled='data.disabled' @input="equate">
        </div>
        <div class="first-name cell">
          <input type="text" v-model="data.firstName" :disabled='data.disabled' @input="equate">
        </div>
        <div class="last-name cell">
          <input type="text" v-model="data.lastName" :disabled='data.disabled' @input="equate">
        </div>
        <div class="desginatin cell">
          <input type="text" v-model="data.desginatin" :disabled='data.disabled' @input="equate">
        </div>
        <div class="project-name cell">
          <input type="text" v-model="data.projectName" :disabled='data.disabled' @input="equate">
        </div>
        <div class="actions cell">
          <button v-if="data.disabled === true" @click="edit(index)" class="edit-button">Edit</button>
          <button v-if="data.disabled === false" @click="save(index)" class="save-button">Save</button>
          <button @click="itemToDelete = data" class="delete-button">Delete</button>
        </div>
      </div>
      <div class="action-buttons">
        <button @click="submit" class="submit-button">Submit</button>
        <button v-if="search === ''" @click="add" class="add-button">Add</button>
      </div>
      <div class="delete-warning" v-if="itemToDelete">
        Вы действительно хотите удалить объект под номером {{ itemToDelete.id + 1 }}
        <div class="buttons">
          <button class="delete-button" @click="deleteData(itemToDelete.id)">Удалить</button>
          <button class="cancel-button">Отмена</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data: () => ({
    tableData: [],
    foundData: [],
    itemToDelete: undefined,
    search: '',
    dataFromFile: ''
  }),
  methods: {
    handleFileUpload(e) {
      var reader = new FileReader();
      reader.onload = this.onReaderLoad;
      reader.readAsText(e.target.files[0]);
    },
    onReaderLoad(event){
        var obj = JSON.parse(event.target.result);
        this.tableData = obj
    },
    equate() {
      this.foundData = this.tableData
    },
    edit(index) {
      this.tableData[index].disabled = false
      this.equate()
    },
    save(index) {
      this.tableData[index].disabled = true
      this.equate()
    },
    deleteData(index) {
      this.tableData = this.tableData.filter(item => {
        return item.id !== index;
      });
      this.itemToDelete = undefined
      this.equate()
    },
    add() {
      var dataObject = {
        id: this.tableData.length,
        emp: '',
        email: '',
        firstName: '',
        lastName: '',
        desginatin: '',
        projectName: '',
        disabled: true
      }
      this.tableData.push(dataObject)
      this.equate()
    },
    submit() {
      window.localStorage.setItem('tableData', JSON.stringify(this.tableData))
    },
    exportData() {
      var exportLink = document.querySelector('.export-button')
      var filename = 'data.json';
      var jsonStr = JSON.stringify(this.tableData);
      exportLink.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(jsonStr));
      exportLink.setAttribute('download', filename);
    },
    sortTable(param) {
      if (param === 'sno') {
        this.tableData.sort((a,b) => (a.id > b.id) ? 1 : ((b.id > a.id) ? -1 : 0))
      }
      if (param === 'emp') {
        this.tableData.sort((a,b) => (a.emp > b.emp) ? 1 : ((b.emp > a.emp) ? -1 : 0))
      }
      if (param === 'email') {
        this.tableData.sort((a,b) => (a.email > b.email) ? 1 : ((b.email > a.email) ? -1 : 0))
      }
      if (param === 'firstName') {
        this.tableData.sort((a,b) => (a.firstName > b.firstName) ? 1 : ((b.firstName > a.firstName) ? -1 : 0))
      }
      if (param === 'lastName') {
        this.tableData.sort((a,b) => (a.lastName > b.lastName) ? 1 : ((b.lastName > a.lastName) ? -1 : 0))
      }
      if (param === 'desginatin') {
        this.tableData.sort((a,b) => (a.desginatin > b.desginatin) ? 1 : ((b.desginatin > a.desginatin) ? -1 : 0))
      }
      if (param === 'projectName') {
        this.tableData.sort((a,b) => (a.projectName > b.projectName) ? 1 : ((b.projectName > a.projectName) ? -1 : 0))
      }
      this.equate()
    },
    searchData() {
      this.tableData = this.foundData.filter(item => (
        (item.emp.search(this.search) !== -1) ||
        (item.email.search(this.search) !== -1) ||
        (item.firstName.search(this.search) !== -1) ||
        (item.lastName.search(this.search) !== -1)) ||
        (item.desginatin.search(this.search) !== -1) ||
        (item.projectName.search(this.search) !== -1)
      )
    }
  },
  mounted() {
    if (window.localStorage.getItem('tableData')) {
      this.tableData = JSON.parse(window.localStorage.getItem('tableData'))
      this.equate()
    }
  }
}
</script>

<style lang="scss">
@import "./assets/common.scss";

#app {
  width: 100%;
}
.app-wrapper {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-top: 2rem;
  overflow: hidden;
  .search {
    display: flex;
    flex-direction: column;
    margin: 0 auto 1rem auto;
    text-align: center;
  }
  .export-button {
    margin: 0 auto 0.5rem auto;
    text-decoration: none;
    width: fit-content;
    button {
      background-color: $options-color;
    }
  }
  .upload-button {
    display: flex;
    flex-direction: column;
    margin: 0 auto 1rem auto;
    text-align: center;
    .files-input-button {
      width: fit-content;
      padding: 0.45rem 0.75rem;
      color: #fff;
      background-color: rgb(196, 196, 196);
      cursor: pointer;
      border: none;
      border-radius: 5px;
      outline: none;
    }
    .files-input {
      width: 0.1px;
      height: 0.1px;
      opacity: 0;
      overflow: hidden;
      position: absolute;
      z-index: -1;
    }
  }
  .data-table {
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-width: 80%;
    margin: 0 auto;
    .table-row {
      display: flex;
      justify-content: center;
      width: 100%;
      .sno {
        width: 9%;
      }
      .emp {
        width: 13%;
      }
      .email {
        width: 13%;
      }
      .first-name {
        width: 13%;
      }
      .last-name {
        width: 13%;
      }
      .desginatin {
        width: 13%;
      }
      .project-name {
        width: 13%;
      }
      .actions {
        width: 13%;
        .edit-button {
          background-color: $options-color;
          margin-right: 0.25rem;
        }
        .delete-button {
          background-color: $error-color;
        }
        .save-button {
          background-color: $success-color;
          margin-right: 0.25rem;
        }
      }
      span {
        opacity: 0.8;
      }
      .cell {
        border: 1px solid $border-color;
        padding: 0.5rem;
        display: flex;
        justify-content: center;
        align-items: center;
        input {
          width: 90%;
        }
      }
    }
    .first {
      border-bottom: 1px solid $border-color;
      font-weight: bolder;
      .cell {
        display: flex;
        justify-content: flex-start;
        cursor: pointer;
      }
      span {
        opacity: 1;
      }
    }
    .action-buttons {
      margin-top: 1rem;
      width: 100%;
      display: flex;
      justify-content: space-between;
      .add-button {
        margin-right: 7%;
        background-color: $add-button-color;
      }
      .submit-button {
        background-color: $success-color;
      }
    }
    .delete-warning {
      display: flex;
      flex-direction: column;
      margin-top: 2rem;
      text-align: center;
      .buttons {
        width: 15%;
        display: flex;
        margin: 0.5rem auto;
        justify-content: space-around;
        .delete-button {
          background-color: $error-color;
        }
      }
    }
  }
}

@media (max-width: 1439px) {
  .data-table {
    overflow-x: scroll;
  }
}

</style>