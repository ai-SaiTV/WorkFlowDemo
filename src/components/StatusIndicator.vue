<script setup lang="ts">
defineProps<{
  status: 'in_progress' | 'completed' | 'failed' | null;
}>();
</script>

<template>
  <div v-if="status" class="flex items-center justify-center space-x-2 p-2">
    <div 
      class="w-2 h-2 rounded-full"
      :class="{
        'bg-yellow-400 animate-pulse': status === 'in_progress',
        'bg-green-500': status === 'completed',
        'bg-red-500': status === 'failed'
      }"
    ></div>
    <span 
      class="text-sm font-medium"
      :class="{
        'text-yellow-600': status === 'in_progress',
        'text-green-600': status === 'completed',
        'text-red-600': status === 'failed'
      }"
    >
      {{ status === 'in_progress' ? 'AI is generating your response...' : 
         status === 'completed' ? 'Response complete!' : 
         'Failed to generate response' }}
    </span>
  </div>
</template>