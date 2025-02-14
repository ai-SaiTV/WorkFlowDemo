<script setup lang="ts">
import { ref } from 'vue';
import { useChat } from '../composables/useChat';
import { useChatPolling } from '../composables/useChatPolling';
import InputField from './InputField.vue';
import MessageInput from './MessageInput.vue';
import ResponseDisplay from './ResponseDisplay.vue';
import StatusIndicator from './StatusIndicator.vue';

const apiKey = ref('');
const botId = ref('');
const message = ref('');

const { error: chatError, sendMessage } = useChat();
const {
  currentResponse: response,
  chatMessages,
  isPolling,
  error: pollingError,
  startPolling
} = useChatPolling();

const handleSubmit = async () => {
  if (!apiKey.value || !botId.value || !message.value) {
    return;
  }

  try {
    const initialResponse = await sendMessage(apiKey.value, botId.value, message.value);
    await startPolling(apiKey.value, initialResponse);
  } catch (err) {
    // Error handling is already done in useChat composable
  }
};
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-600 via-pink-500 to-orange-400">
    <div class="container mx-auto px-4 py-8">
      <div class="max-w-2xl mx-auto bg-white/90 backdrop-blur-lg rounded-lg shadow-xl p-6">
        <h1 class="text-3xl font-bold text-center mb-8 bg-gradient-to-r from-purple-600 to-pink-600 bg-clip-text text-transparent">
          Classroom Planning Assistant
        </h1>

        <div class="space-y-4">
          <InputField
            v-model="apiKey"
            label="API Key"
            type="password"
            placeholder="Enter your API key"
          />

          <InputField
            v-model="botId"
            label="Bot ID"
            placeholder="Enter your Bot ID"
          />

          <MessageInput 
            v-model="message"
            :disabled="isPolling"
          />

          <div class="flex flex-col space-y-2">
            <button
              @click="handleSubmit"
              :disabled="isPolling || !apiKey || !botId || !message"
              class="w-full bg-gradient-to-r from-purple-600 to-pink-600 text-white py-2 px-4 rounded-lg hover:opacity-90 transition-opacity disabled:opacity-50"
            >
              {{ isPolling ? 'Generating Response...' : 'Send Message' }}
            </button>

            <StatusIndicator 
              :status="response?.data.status || null" 
            />
          </div>

          <div v-if="chatError || pollingError" class="text-red-500 text-sm mt-2">
            {{ chatError || pollingError }}
          </div>

          <ResponseDisplay 
            :response="response"
            :messages="chatMessages"
          />
        </div>
      </div>
    </div>
  </div>
</template>