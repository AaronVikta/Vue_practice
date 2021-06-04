<template>
  <div id="app">
    <current-time class="col-4"/>
    <task-input class="col-6"
    @add-task ='addNewTask'/>
    <div class="col-12">
      <div class="cardBox">
        <div class="container">
          <h2>My Tasks</h2>
          <hr/>
          <div class="col-12">
            <div class="col-4">
            <input type="checkbox"
            v-model="hideDone"
            id="hideDone"
            name="hideDone"
            />
            <label for="hideDone">Hide Done</label>
          </div>
          <div class="col-4">
            <input type="checkbox"
            v-model="reverse"
            id="reverse"
            name="reverse"
            />
            <label for="reverse">Reverse Order</label>
          </div>
          <div class="col-4">
            <input type="checkbox"
            id="sortById"
            name="sortById"
            />
            <label for="sortById"> 
              Sort By Id
            </label>
          </div>
          </div>
          <ul class="taskList">
            <li 
            v-for="(taskItem, index) in displayList"
            :key='`${index}_${Math.random()}`'>
              <input type="checkbox"
              :checked="!!taskItem.finishedAt"
              @input="changeStatus(taskItem.id)"
              />
              #{{taskItem.id}} = {{taskItem.task}}
              <span v-if="taskItem.finishedAt">
               Done at:  {{formatDate(taskItem.finishedAt)}}
              </span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CurrentTime from './components/currentTime.vue'
import TaskInput from './components/TaskInput.vue'

export default {
  name: 'App',
  components: {
    CurrentTime,
    TaskInput
  },
  data: ()=>({
    taskList: [],
    hideDone:false,
    reverse: false,
    sortById: false
  }),
 computed:{
   displayList(){
     const taskList =[...this.sortedList]
     return this.reverse
     ? taskList.reverse()
     : taskList
   },
   baseList(){
     return [...this.taskList].map((t, index) => ({
       ...t,
       id: index + 1
     }))
   },
   filteredList(){
     return this.hideDone ? [...this.baseList]
     .filter(t => !t.finishedAt)
     : [...this.baseList]
   },
   sortedList(){
     return [...this.filteredList]
      .sort((a, b) => (
      this.sortById
      ? b.id - a.id
      : (a.finishedAt || 0) - (b.finishedAt || 0)
      ));
   }
 },
  methods:{
    addNewTask(task){
      this.taskList.push({
        task,
        createdAt: Date.now(),
        finishedAt: undefined
      })
    },
    changeStatus(taskId){
      const task = this.taskList[taskId - 1];
      if(task.finishedAt){
        task.finishedAt = undefined
      }
      else{
        task.finishedAt = Date.now()
      }
    },
    formatDate(value){
      if(!value) return "";
      if (typeof value !== 'number') return value;
      const browserLocale = navigator.languages && navigator.languages.length
      ? navigator.languages[0]
      :navigator.language;
      const intlDateTime = new Intl.DateTimeFormat(
        browserLocale,
        {
          year: 'numeric',
          month: 'numeric',
          day: 'numeric',
          hour: 'numeric',
          minute: 'numeric'
        })
        return intlDateTime.format(new Date(value))
    }
  }
}
</script>


<style
    CurrentTimeyle>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.taskList li{
  text-align: left;
}
</style>
