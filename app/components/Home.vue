<template>
  <Page  class="todo">
    <StackLayout>
        <TextField v-model="newText" hint="Добавить задачу"></TextField>
        <Button text="Добавить" @tap="newTask"></Button>       
        <ListView  class="task" for="task in tasks">
          <v-template>
            <GridLayout columns="200%, 70, 70">
            <label  class="task-text done" v-if="task.done" textWrap="true" col="0">{{task.title}}</label>
            <label  class="task-text" v-else  @tap="change(task.id, task.title)" textWrap="true" col="0">{{task.title}}</label>
            <Button  class="complete" text="!" @tap="done(task.id)" col="1"/>
            <Button  class="remove" text="🗑" @tap="remove(task.id)" col="2"/> 
            </GridLayout>
          </v-template>
        </ListView>     
    </StackLayout>
  </Page>
</template>

<script>
import * as ApplicationSettings from "@nativescript/core/application-settings";
export default {
  data () {
    return {
      newText: '',
      tasks: []
    }
  },
  mounted(){
    if(ApplicationSettings.getString('tasks')){
      this.tasks=Object.values(JSON.parse(ApplicationSettings.getString('tasks')));
    }
  },
  methods: {
    newTask () {
      if(this.newText != ''){
        this.tasks.push({
          id: Math.random(),
          title: this.newText,
          done: false
        });
        this.newText = '';
      }
      this.save();
    },
    done (id) {
      this.tasks = this.tasks.map(task => {
        if (task.id == id) task.done = !task.done;
        return task;
      })
      this.save();
    },
    remove (id) {
      this.tasks = this.tasks.filter(task => task.id !== id);
      this.save();
    },
    save(){
      let toSave = Object.assign({}, this.tasks);
      ApplicationSettings.setString('tasks', JSON.stringify(toSave));
    },
    
    change(id, old_text) {
      prompt({
        message: "Изменить текущую задачу:",
        okButtonText: "Изменить",
        cancelButtonText: "Отмена",
        defaultText: old_text,
      })
      .then(result => {
         this.tasks.forEach(task => {
          if (task.id == id && result.text != ''){
            task.title = result.text;
            this.save();
          }    
         });
      })
    }
  }
}
</script>

<style>
.todo{
    background-color: rgba(199, 188, 188, 0.658);
}
.task-text{
  background-color: rgba(199, 188, 188, 0.658);
  margin: 10px 100px;  
  color: #000000;
  font-weight: 900;
  text-align: center;
}
.done {
  text-decoration: line-through;
  background-color: rgba(134, 126, 126, 0.658)
}
.complete {
background-color: rgb(201, 188, 5);
color: black;
font-weight: 900;
font-size: 25px;
margin: 30px 10px;
}
.remove {
background-color:red;
font-size: 20px;
margin: 30px 10px;
}
</style>