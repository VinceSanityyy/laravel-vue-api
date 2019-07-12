<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">List</div>

                    <div class="card-body">

                       <div class="input-group">
                           <input type="text" class="form-control"
                            v-model="task.name"
                            @keydown.enter="create">
                           <span class="input-group-btn">
                               <button class="btn btn-success" @click="create"> Add</button>
                                </span>
                        </div>
                    </div>
                    <div class="tasks-list">
                         <div class="alert alert-danger" v-if="!tasks.length">
                            Empty
                        </div>
                        <ul class="list-unstyled">
                            <li v-for="task in tasks" :key="task.id" :class="{done: task.completed}">
                                <div class="checkbox">
                                    <label>
                                         <input type="checkbox" v-model="task.completed" @click="done(task)">{{ task.name }}
                                         </label>
                                         <div class="float-right">
                                    <a href="#" @click.prevent="remove(task)">x</a>
                                </div>
                                </div>

                            </li>
                        </ul>
                    </div>
                    <div class="panel-footer" v-if="tasks.length ">
                       <span class = "label label-info">Total {{tasks.length}}</span>
                       <span class = "label label-default">Left {{remaining()}}</span>
                       <span class = "label label-warning">Completed {{completed()}}</span>

                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            this.fetchData()
        },
        data(){
            return{
                tasks: [],
                task:{
                    name:''
                }
            }
        },
        methods: {
            remaining(){
                return this.tasks.filter(task => {return !task.completed}).length
            },
            completed(){
                return this.tasks.filter(task => {return task.completed}).length
            },
            fetchData(){
                axios.get('api/tasks')
                    .then((res)=>{
                        this.tasks = res.data
                    })
                    .catch((err)=>{
                        console.log(err)
                    })
            },
            create(){
                axios.post('/api/tasks',this.task)
                    .then((res)=>{
                        this.tasks.unshift(res.data)
                        this.task.name = ''
                    })
                    .catch((err)=>{
                        console.log(err)
                    })
            },
            done(task){
                axios.put(`/api/tasks/${task.id}`, {
                    completed: !task.completed
                })
                .then((res)=>{
                    console.log('task updated')
                })
                .catch((err)=>{
                    console.log(err)
                })
            },
            remove(task){
                axios.delete(`/api/tasks/${task.id}`,{
                    completed: task.completed
                })
                .then((res)=>{
                    const taskIndex = this.tasks.indexOf(task)
                    this.tasks.splice(taskIndex,1)
                    console.log('removed')
                })
                .catch((err)=>{
                    console.log(err)
                })
            }
        }
    }
</script>


<style>
    body, .tasks-list{
        padding-top: 20px;
    }
    .done label{
        text-decoration: line-through;

    }
</style>
