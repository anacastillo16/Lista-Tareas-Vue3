<template>
    <div class="flex items-center justify-between p-6 bg-gray-100 rounded-lg shadow-d">
        <div class="flex items-center space-x-4">
            <input type="checkbox" :checked="task.completed" @change="markAsCompleted" class="h-5 w-5 text-blue-600 focus:ring-0">
            <p :class="{'line-through text-gray-500':task.completed}" class="text-lg flex-1">{{ task.text }}</p>
        </div>
        <button @click="removeTask" class="px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600">Eliminar</button>
    </div>
</template>

<script setup lang="ts">
    import { defineProps, defineEmits } from 'vue';
    
    interface Task {
        text:string;
        completed:boolean;
    }

    const props = defineProps<{
        task: Task;
        index:number;
    }>();

    const emit = defineEmits<{
        (event:'task-completed', index:number) : void;
        (event:'remove-task', index:number) : void;
    }>();

    function markAsCompleted() {
        emit('task-completed', props.index);
    }

    function removeTask() {
        emit('remove-task', props.index);
    }
</script>