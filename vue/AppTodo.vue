<template>
    <div class="container" style="height: 40vh; margin-top: 7rem;">
    <div class="row">
      <div class="col-12">
        <div class="card">
          <div class="card-header">
            Todo App
          </div>
          <div class="card-body">
            <form @submit.prevent="newTask">
              <div class="input-group">
                <input v-model="taskInput" type="text" class="form-control">
                <button type="submit" class="btn btn-primary">Ekle/Düzenle</button>
              </div>
            </form>
          </div>
        </div>

        <div class="card mt-3">
          <div class="card-header controls">
            <div class="filters">
              <span :class="{ active: activeFilter === 'all' }" @click="filterTasks('all')">Hepsi</span>
              <span :class="{ active: activeFilter === 'pending' }" @click="filterTasks('pending')">Yapılacaklar</span>
              <span :class="{ active: activeFilter === 'completed' }" @click="filterTasks('completed')">Tamamlananlar</span>
            </div>
            <button @click="clearAllTasks" class="btn btn-danger btn-sm float-end">Temizle</button>
          </div>
          <ul class="list-group list-group-flush">
            <li v-for="task in filteredTasks" :key="task.id" class="task list-group-item">
              <div class="form-check">
                <input type="checkbox" @change="updateStatus(task)" :id="task.id" class="form-check-input" :checked="task.durum === 'completed'">
                <label :for="task.id" class="form-check-label" :class="{ checked: task.durum === 'completed' }">{{ task.gorevAdi }}</label>
              </div>
              <div class="dropdown">
                <button class="btn btn-link dropdown-toggle" type="button" id="dropdownMenuButton1" data-bs-toggle="dropdown" aria-expanded="false">
                  <i class="fa-solid fa-ellipsis"></i>
                </button>
                <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton1">
                  <li><a @click="deleteTask(task.id)" class="dropdown-item" href="#"><i class="fa-solid fa-trash-can"></i> Sil</a></li>
                  <li><a @click="editTask(task.id, task.gorevAdi)" class="dropdown-item" href="#"><i class="fa-solid fa-pen-to-square"></i> Düzenle</a></li>
                </ul>
              </div>
            </li>
          </ul>
          <div v-if="filteredTasks.length === 0" class="p-2 mt-3 alert alert-warning" role="alert">
            Görev listeniz boş !
          </div>
        </div>
      </div>
    </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        taskInput: '',
        gorevListesi: [],
        editId: null,
        isEditTask: false,
        activeFilter: 'all',
      };
    },
    computed: {
      filteredTasks() {
        if (this.activeFilter === 'all') {
          return this.gorevListesi;
        } else {
          return this.gorevListesi.filter((task) => task.durum === this.activeFilter);
        }
      },
    },
    methods: {
      newTask() {
        if (this.taskInput === '') {
          alert('Bir görev girmelisiniz.');
        } else {
          if (!this.isEditTask) {
            this.gorevListesi.push({ id: this.gorevListesi.length + 1, gorevAdi: this.taskInput, durum: 'pending' });
          } else {
            for (let i = 0; i < this.gorevListesi.length; i++) {
              if (this.gorevListesi[i].id === this.editId) {
                this.gorevListesi[i].gorevAdi = this.taskInput;
              }
            }
            this.isEditTask = false;
          }
          this.taskInput = '';
          localStorage.setItem('gorevListesi', JSON.stringify(this.gorevListesi));
        }
      },
      deleteTask(id) {
        const index = this.gorevListesi.findIndex((task) => task.id === id);
        if (index !== -1) {
          this.gorevListesi.splice(index, 1);
          localStorage.setItem('gorevListesi', JSON.stringify(this.gorevListesi));
        }
      },
      editTask(taskId, taskName) {
        this.editId = taskId;
        this.isEditTask = true;
        this.taskInput = taskName;
      },
      clearAllTasks() {
        this.gorevListesi = [];
        localStorage.setItem('gorevListesi', JSON.stringify(this.gorevListesi));
      },
      updateStatus(task) {
        task.durum = task.durum === 'completed' ? 'pending' : 'completed';
        localStorage.setItem('gorevListesi', JSON.stringify(this.gorevListesi));
      },
      filterTasks(filter) {
        this.activeFilter = filter;
      },
    },
    mounted() {
      if (localStorage.getItem('gorevListesi') !== null) {
        this.gorevListesi = JSON.parse(localStorage.getItem('gorevListesi'));
      }
    },
  };
  </script>
  
  
    <style scoped>
    
    .dropdown-toggle::after {
        display: none !important;
    }

    .task {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .task label.checked{
        text-decoration: line-through;
    }
    .controls{
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .filters span.active{
        color: #3cb7ff;
    }
    .filters span{
        margin-right: 5px;
        font-size: 15px;
        column-rule-color: #444;
        cursor: pointer;
    }
    
/* .dropdown-menu {
    display: none;
    color: rgb(0, 0, 0) !important;
    position: absolute;
    background-color: #ffffff;
    width: 0;
    overflow: hidden;
    transition: width 0.5s ease-in-out;
    border: none !important;
    border-radius: 2rem !important;
    padding: 2rem !important;
    
  } */
/*   
  .dropdown-menu.show {
    width: 160px;
    animation: fadeIn 0.5s ease-in-out;
    box-shadow: 4px 4px 8px rgba(0, 0, 0, 0.1);
    box-sizing: content-box;
  }  */

</style>