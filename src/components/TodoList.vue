<template>
    <el-row>
        <el-col :span="12" :offset="7" style="width: 100%">
            <h1>TodoList</h1>
            <todo-form @send-message="createTodo"></todo-form>
            <el-table :data="todos">
                <el-table-column prop="title" label="Title" width="350"/>
                <el-table-column fixed="right" label="Operations" width="200">
                    <template #default="scope">
                        <el-space wrap>
                            <el-switch v-model="scope.row.completed" @click="updateTodo(scope.row)"/>
                            <el-popconfirm confirm-button-text="Yes" cancel-button-text="No" icon="el-icon-info" icon-color="red" title="Are you sure you want to delete this?" @confirm="handleDelete(scope.row)" @cancel="cancelDelete">
                                <template #reference>
                                    <el-button size="mini" type="danger">Delete</el-button>
                                </template>
                            </el-popconfirm>
                        </el-space>
                    </template>
                </el-table-column>
            </el-table>
        </el-col>
    </el-row>
</template>

<script lang="ts">
    import { Options, Vue } from 'vue-class-component';
    import TodoForm from './TodoForm.vue';
    import { ElMessage } from "element-plus";

    interface Todo {
        id: number;
        title: string;
        completed: boolean;
    }
    
    @Options({
        components: {
            TodoForm,
        }
    })
    export default class TodoList extends Vue {
        todos = [];

        async mounted(){
            await this.loadTodos();
        }

        async loadTodos(){
            const response = await this.axios.get(`https://8073-201-19-121-179.ngrok.io/todos`);
            this.todos = response.data;
        }

        async createTodo(todo: Todo){
            debugger
            console.log("Todo", todo);
            
            await this.axios.post(`https://8073-201-19-121-179.ngrok.io/todos`, {
                title: todo.title,
                completed: todo.completed
            });

            ElMessage({
                message: "Todo Created",
                type: "success"
            })

            await this.loadTodos();
        }

        async updateTodo(todo: Todo){
            console.log("Todo", todo);
            await this.axios.put(`https://8073-201-19-121-179.ngrok.io/todos/${todo.id}`, {
                id: todo.id,
                title: todo.title,
                completed: todo.completed
            });

            ElMessage({
                message: "Todo Updated",
                type: "success"
            })

            await this.loadTodos();
        }

        async handleDelete(todo: Todo){
            await this.axios.delete(`https://8073-201-19-121-179.ngrok.io/todos/${todo.id}`);

            ElMessage({
                message: "Todo Deleted",
                type: "success"
            })

            await this.loadTodos();
        }

        cancelDelete(){
            console.log('Canceled the delete');
        }
    }
</script>