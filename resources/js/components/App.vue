<template>
    <div class="app-component">

        <loading :active.sync="isLoading"></loading>

        <table class="table">
            <thead>
                <tr>
                    <th>Task Title</th>
                    <th>Priority Level</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <task-component v-for="task in tasks" :key="task.id" :task="task" @delete="remove"></task-component>
                <tr>
                    <td>
                        <input v-model="task.title" type="text" id="task" class="form-control">
                    </td>
                    <td>
                        <select v-model="task.priority" id="select" class="form-control">
                            <option>Low</option>
                            <option>Medium</option>
                            <option>High</option>
                        </select>
                    </td>
                    <td>
                        <button @click="store" class="btn btn-primary form-control">ADD</button>
                    </td>

                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

    import TaskComponent from './Task.vue';
    import Loading from 'vue-loading-overlay';
    import 'vue-loading-overlay/dist/vue-loading.css';

    export default {

        data(){
            return {
                tasks:[],
                isLoading: false,
                task:{title:'',priority:''}
            }
        },
        components: {
            TaskComponent,Loading
        },
        methods: {
            getTasks(){
                window.axios.get('/api/tasks/').then(({data})=> {

                    data.forEach(task=>{
                        this.tasks.push(task);
                    });
                });
            },
            store(){
                if(this.checkInputs()){
                    this.isLoading = true;
                    window.axios.post('/api/tasks', this.task).then(savedTask => {
                        //console.log(savedTask);
                        this.tasks.push(savedTask.data);
                        this.task.title     = '';
                        this.task.priority  = '';
                        this.isLoading = false;
                    })
                }
                //console.log(this.task.title, this.task.priority);
            },
            checkInputs(){
                if(this.task.title && this.task.priority) return true;
            },
            remove(id){
                this.isLoading = true;
                //console.log(id);
                window.axios.delete(`/api/tasks/${id}`).then(()=>{
                    let index = this.tasks.findIndex(task => task.id === id);
                    this.tasks.splice(index,1);
                    this.isLoading = false;
                });
            }
        },
        created(){
            this.isLoading = true;
            this.getTasks();
            this.isLoading = false;
        },
    }

</script>

<style>

</style>