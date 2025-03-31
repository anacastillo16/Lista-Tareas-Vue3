<template>
    <div class="max-w-4xl mx-auto p-6 bg-white rounded-lg shadow-lg mt-6">
        <form action="#" class="flex justify-between items-center mb-6">
            <label for="task" class="text-lg font-medium">Añadir una tarea: </label>
            <div class="flex space-x-4">
                <input type="text" v-model="newTask" class="px-4 py-2 border rounded-lg border-gray-300 focus:ring-2 focus:ring-blue-400 focus:outline-none">
                <button type="submit" @click="addTask" class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600">Añadir</button>
            </div>
        </form> 
        <div class="flex gap-2 mt-4 mb-2 justify-center">
            <button @click="filter = 'all'" class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600">Ver todas</button>
            <button @click="filter = 'pending'" class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600">Ver pendientes</button>
            <button @click="filter = 'completed'" class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600">Ver finalizadas</button>
        </div>
        <p v-if="filter==('pending') &&  filteredTasks().length==0" class="text-gray-500 text-center mt-4">No hay tareas pendientes</p>
        <p v-else-if="filter==('completed') && filteredTasks().length==0" class="text-gray-500 text-center mt-4">No hay tareas finalizadas</p>
        <p v-else class="text-gray-500 text-center mt-4">No hay tareas asignadas</p>

        <ul class="space-y-4">
            <TaskItem 
                v-for="(task, index) in filteredTasks()"
                :key="index"
                :task="task"
                :index="index"
                @task-completed="markAsCompleted"
                @remove-task="deleteTask"
            />
        </ul>
    </div>
</template>

<script setup lang="ts">
    import { ref } from "vue";
    import TaskItem from './TaskItem.vue';

    interface Task {
        text: string;
        completed: boolean;
    }

    const newTask = ref<string>("");
    const tasks = ref<Task[]>(loadTasks());
    const filter = ref<"all" | "completed" | "pending">("all");

    function loadTasks() : Task[] {
        const savedTasks = localStorage.getItem('tasks');
        if (savedTasks) {
            return JSON.parse(savedTasks);
        } else {
            return [];
        }
    }

    function saveTask() : void {
        localStorage.setItem('tasks', JSON.stringify(tasks.value));
    }

    function addTask(): void {
        if(newTask.value.trim() !== "") {
            tasks.value.push({text: newTask.value, completed:false});
            newTask.value="";
            saveTask();
        }
    }

    function markAsCompleted(index:number):void {
        tasks.value[index].completed = !tasks.value[index].completed;
        saveTask();
    }

    function deleteTask(index:number): void {
        tasks.value.splice(index, 1);
        saveTask();
    }

    function filteredTasks(): Task[] {
        if (filter.value == "completed") {
            return tasks.value.filter(task => task.completed);
        }

        if (filter.value == "pending") {
            return tasks.value.filter(task=>!task.completed);
        }
        return tasks.value;
    }
</script>
