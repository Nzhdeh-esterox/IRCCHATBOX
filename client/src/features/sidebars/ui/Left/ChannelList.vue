<template>
  <div v-if="isLoading" class="flex flex-col h-auto">
    <!-- Channels Accordion -->
    <div class="cursor-pointer flex justify-between items-center mb-1">
      <div @click="showChannels = !showChannels" class="w-full">
        <h2 class="text-xl font-bold">Channels</h2>
      </div>
      <div class="relative ml-1">
        <button
          @click="togglePopover"
          class="w-full text-sm bg-blue-600 text-white rounded hover:bg-blue-700 flex items-center justify-center gap-2"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 fill-current" viewBox="0 0 20 20">
            <path
              fill-rule="evenodd"
              d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z"
              clip-rule="evenodd"
            />
          </svg>
        </button>

        <div
          v-if="showPopover"
          class="absolute top-7 left-0 z-50 bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-950 rounded shadow-lg p-4 w-max"
        >
          <h3 class="text-sm font-semibold mb-2 text-gray-800 dark:text-gray-100">New Channel</h3>
          <input
            ref="newChannelInput"
            v-model="newChannelName"
            type="text"
            placeholder="e.g. news"
            class="w-full px-2 py-1 border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-950 text-gray-800 dark:text-gray-100 rounded mb-2 text-sm"
            @keyup.enter="createChannel"
          />
          <div class="flex flex-col">
            <div class="flex justify-end gap-2">
              <button
                @click="hidePopover"
                class="text-xs px-2 py-1 bg-gray-200 dark:bg-gray-600 text-gray-800 dark:text-gray-100 rounded"
              >
                Cancel
              </button>
              <button
                @click="createChannel"
                class="text-xs px-2 py-1 bg-blue-500 hover:bg-blue-600 text-white dark:text-white rounded"
              >
                Create
              </button>
            </div>
            <p v-if="errorMessage" class="text-red-500 text-xs mt-2">{{ errorMessage }}</p>
          </div>
        </div>
      </div>
    </div>

    <ul v-if="showChannels" class="mb-4 flex-1 overflow-auto">
      <li
        v-for="channel in channels"
        :key="channel"
        @click="goToChannel(channel)"
        :class="[
          'cursor-pointer px-2 py-1 rounded dark:hover:text-darkBlue hover:bg-gray-200 flex justify-between',
          channel === activeChannel
            ? 'font-bold text-black dark:text-white'
            : 'text-gray-800 dark:text-gray-400'
        ]"
      >
        <span># {{ channel }}</span>
        <span
          v-if="
            latestChannelTimestamps[channel] &&
            hasUnread(`channel:${channel}`, latestChannelTimestamps[channel])
          "
          class="text-blue-500 font-bold"
          >•</span
        >
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, nextTick } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import api from '@/services/api.js';
import { setLastViewed, hasUnread } from '@/helpers/storage.js';
import { useChannelsStore } from '@/shared/stores/channels.js';
import { storeToRefs } from 'pinia';
const store = useChannelsStore();
const router = useRouter();
const route = useRoute();
const { channels, latestChannelTimestamps, hasFetched } = storeToRefs(store);

const activeChannel = computed(() => route.params.name);
const isLoading = computed(() => hasFetched);
const showChannels = ref(true);
const showPopover = ref(false);
const newChannelName = ref('');
const newChannelInput = ref(null);
const errorMessage = ref('');

const goToChannel = (name) => {
  setLastViewed(`channel:${name}`);
  router.push(`/channel/${name}`);
};

const togglePopover = async () => {
  showPopover.value = !showPopover.value;

  if (showPopover.value) {
    await nextTick(); // wait for DOM to update
    newChannelInput.value?.focus();
  }
};

const hidePopover = () => {
  showPopover.value = false;
  newChannelName.value = '';
  errorMessage.value = '';
};

const createChannel = async () => {
  const name = newChannelName.value.trim();
  errorMessage.value = ''; // Reset error message

  if (!name) {
    errorMessage.value = 'Channel name is required.';
    return;
  }

  if (channels.value.includes(name)) {
    errorMessage.value = 'Channel already exists.';
    return;
  }

  try {
    await api.post('/channels', { name });
    hidePopover();
    router.push(`/channel/${name}`);
  } catch (err) {
    errorMessage.value = err.response?.data?.error || 'Failed to create channel.';
  }
};

onMounted(() => {
  store.fetchLatestTimestamps();
});
</script>
