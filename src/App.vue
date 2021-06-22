<template>
  <div class="container">
  <header>
    <img src="./logo.png" alt="logo">
    <h1>ДОСКА</h1>
    <b-button class="button" @click="toggleTheme">Сменить тему</b-button>
  </header>
    <b-button class="col-12" variant="primary" @click="open=true">Создать</b-button>
    <vue-modaltor :visible="open" @hide="open=false" bg-overlay="green">
      <template #header>
      <div class="col-12 row pt-3">
        <b-icon-x-circle @click="reset" class="h1"></b-icon-x-circle>
        <h2 class="text-center">Добавьте новую карточку</h2>
      </div>
      </template>
      <template #body>
          <div class="col-12 px-3">
          <b-form @submit="add" @reset="reset" class="mb-5 py-2">
            <b-form-input
              id="input-1"
              v-model="newTask.name"
              placeholder="Новая карточка"
              required
            ></b-form-input>
            <b-form-textarea class="mt-3"
            id="textarea"
            placeholder="Описание"
            v-model="newTask.description"
            rows="3"
          ></b-form-textarea>
            <b-form-select class="form-select mt-3 mb-3"
              id="input-2"
              v-model="newTask.level"
              :options="levels"
              required
            ></b-form-select>
            <div class="row pt-3">
            <b-button type="submit" variant="primary" class="mx-auto col-2">Добавить</b-button>
            <b-button type="reset" variant="danger" class="mx-auto col-2">Сбросить</b-button>
            </div>
          </b-form>
          </div>
        </template>
    </vue-modaltor>
    <div class="row mt-3">
      <div class="col-md-4">
        <div class="p-2 alert alert-secondary backlog">
          <h3 class="text-center">БЭКЛОГ --- {{arrBacklog.length}}</h3>
          <card v-bind:del="del" v-bind:edit="edit" v-bind:left="leftOne" v-bind:right="rightOne"  namearr='backlog' v-bind:arr="arrBacklog"></card>
        </div>
      </div>

      <div class="col-md-4">
        <div class="p-2 alert alert-primary inprog">
          <h3 class="text-center">В ПРОГРЕССЕ --- {{arrInProgress.length}}</h3>
          <card v-bind:del="del" v-bind:edit="edit" v-bind:left="leftOne" v-bind:right="rightTwo" namearr='progress' v-bind:arr="arrInProgress"></card>
        </div>
      </div>

      <div class="col-md-4">
        <div class="p-2 alert alert-success done">
          <h3 class="text-center">СДЕЛАЛИ!!! --- {{arrDone.length}}</h3>
          <card v-bind:del="del" v-bind:edit="edit" v-bind:left="leftTwo" v-bind:right="rightTwo" namearr='done' v-bind:arr="arrDone"></card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import card from './components/card.vue';

export default {
  name: 'App',
  components: {
    card
  },

  mounted() {
            localStorage.setItem('theme', 'theme-light');
            document.documentElement.className = 'theme-light';
  },
 
   data(){
    return{
      levels: [1, 2, 3],
      amount : 1,
      newTask: {
        name: null,
        date: null,
        level: 1,
        number: null,
        description: null
      },
      arrBacklog: [
        {name: 'Test 1', date: '12.11.2002', level: 1, description: 'test-card', number: 1}
        ],
      arrInProgress:[],
      arrDone:[],
      open: false
    }
  },
  methods:{
    add(event){
      event.preventDefault()
      if (this.newTask){
        this.amount++
        this.arrBacklog.push({name: this.newTask.name, level: this.newTask.level, number: this.amount, description: this.newTask.description, date: this.makeCalculate()});
        this.newTask = {};
        this.open = false
      }
    },
    reset(){
      this.newTask.name = null
      this.newTask.level = null
      this.newTask.date = null
      this.newTask.description = null
      this.open = false

    },
    edit(namearr, index, editTask){
      if (editTask){
              switch (namearr){
                case 'backlog':
                  this.arrBacklog.splice(index, 1)
                  this.arrBacklog.push({name: editTask.name, level: editTask.level, description: editTask.description, date: this.makeCalculate()})
                  break;
                case 'progress':
                  this.arrInProgress.splice(index, 1)
                  this.arrInProgress.push({name: editTask.name, level: editTask.level, description: editTask.description, date: this.makeCalculate()})
                  break;
                case 'done':
                  this.arrDone.splice(index, 1)
                  this.arrDone.push({name: editTask.name, level: editTask.level, description: editTask.description, date: this.makeCalculate()})
                  break;
              }

            }
    },
    makeCalculate () {
             return this.$moment().format('DD.MM.YYYY');
    },
    setTheme(themeName) {
            localStorage.setItem('theme', themeName);
            document.documentElement.className = themeName;
        },
    toggleTheme() {
            if (localStorage.getItem('theme') === 'theme-dark') {
                this.setTheme('theme-light');
            } else {
                this.setTheme('theme-dark');
            }
        },
      rightOne(index, element){
        this.arrBacklog.splice(index, 1),
        this.arrInProgress.push(element)
      },
      leftOne(index, element){
        this.arrInProgress.splice(index, 1),
        this.arrBacklog.push(element)
      },
      rightTwo(index, element){
        this.arrInProgress.splice(index, 1),
        this.arrDone.push(element)
      },
      leftTwo(index, element){
        this.arrDone.splice(index, 1),
        this.arrInProgress.push(element)
      },
      del(index, arr){
        arr.splice(index, 1);
      }
  }  
}
</script>

<style>
  @import './style.css';
</style>
