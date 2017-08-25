<template>
    <div class="container">
        <div class="row">
            <table class="table">
                <thead>
                    <tr>
                        <th>TODO</th>
                        <th>Description</th>
                        <th>End Date</th>
                        <th>Save</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td><input type="text" v-model="newTodo"></td>
                        <td><input type="text" v-model="newDesc"></td>
                        <td><input type="date" v-model="newEndD"></td>
                        <td><button class="btn btn-primary" @click="save">Save</button></td>
                    </tr>
                </tbody>
            </table>
            <table class="table">
                <thead>
                    <tr>
                        <th>TODO</th>
                        <th>Description</th>
                        <th>End Date</th>
                        <th>Complete</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="todo in todos">
                        <td>{{ todo.todo }}</td>
                        <td>{{ todo.description }}</td>
                        <td>{{ todo.end_at }}</td>
                        <td v-if="todo.completed_at == null">
                            <button class="btn btn-success" @click="complete(todo)">Complete</button>
                        </td>
                        <td v-else>
                            {{ todo.completed_at }}
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                todos: [],
                newTodo: "",
                newDesc: "",
                newEndD: "",
            };
        },
        methods: {
            save() {
                const todo = {
                    todo: this.newTodo,
                    description: this.newDesc,
                    end_at: this.newEndD,
                };
                axios.post('/todo', todo)
                .then(function(response) {
                    this.todos.push(response.data);
                }.bind(this))
                .catch(function(err) {
                    console.log(err);
                });
            },
            complete(todo) {
                axios.post('/todo/'+todo.id)
                .then(function(response) {
                    let index = _.findIndex(this.todos, function(t) {
                        return t.id == todo.id;
                    });
                    this.$set(this.todos, index, response.data);
                }.bind(this))
                .catch(function(err) {
                    console.log(err);
                });
            },
        },
        mounted() {
            axios.get('/todo')
            .then(function(response) {
                console.log(response.data)
                this.todos = response.data;
            }.bind(this))
            .catch(function(err) {
                console.log(err);
            });
        }
    }
</script>
