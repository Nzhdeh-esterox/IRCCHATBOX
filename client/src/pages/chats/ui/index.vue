<template>
  <div class="flex flex-col h-screen overflow-hidden relative">
    <div class="flex flex-1 overflow-hidden">
      <!-- Chat Area -->
      <div class="flex flex-col flex-1 overflow-hidden">
        <div ref="chatContainer" class="flex flex-1 overflow-y-auto">
          <div v-if="isFetched" class="w-full px-4 mx-auto">
            <Chat :title="chatTitle" :messages="messages" />
          </div>
        </div>

        <!-- Input Bar -->
        <div class="border-t dark:border-t-gray-950 flex dark:bg-darkBlue bg-white py-2 px-4">
          <div class="w-full mx-auto">
            <InputBar @send="handleNewMessage" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, nextTick, onBeforeMount, ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import { storeToRefs } from 'pinia';

import Chat from '@/widgets/Chat/ui/Chat.vue';
import InputBar from '@/components/InputBar.vue';

import api from '@/services/api';
import { useChannelsStore } from '@/shared/stores/channels';
import { useMessagesStore } from '@/shared/stores/messages';
import { useDirectMessagesStore } from '@/shared/stores/directMessages';
import { CHAT_TYPES } from '@/shared/constants/chat';
import { useTheme } from '@/shared/stores/composables/useTheme';

const route = useRoute();
const chatContainer = ref(null);
const { isDark } = useTheme();
// Route params
const chatType = computed(() => route.params.chat); // 'channel' or 'dm'
const slug = computed(() => route.params.name); // channel name or username

// Stores
const dmsStore = useDirectMessagesStore();
const messagesStore = useMessagesStore();
const channelsStore = useChannelsStore();
const dmMessages = computed(() => dmsStore.getDirectMessages);
// Store refs
const { messages: channelMessages, hasFetchedMessages } = storeToRefs(messagesStore);

// Logic
const isChannel = computed(() => chatType.value !== 'dm');

const messages = computed(() =>
  chatType.value !== 'dm' ? channelMessages.value : dmMessages.value
);
const isFetched = computed(() => (isChannel.value ? hasFetchedMessages.value : true));

const chatTitle = computed(() =>
  isChannel.value ? `#${channelsStore.getChannelName(slug.value)}` : slug.value
);

const scrollToBottom = () => {
  const el = chatContainer.value;
  if (!el) return;
  el.scrollTop = el.scrollHeight;
};

const fetchAndScroll = async () => {
  if (isChannel.value) {
    await messagesStore.fetchMessages(slug.value, isDark);
  } else {
    await dmsStore.fetchDMs(slug.value, isDark);
  }
  await nextTick();
  scrollToBottom();
};

const handleNewMessage = async (msg) => {
  if (isChannel.value) {
    await api.post(`/${CHAT_TYPES.CHANNEL}/${slug.value}`, msg);
    channelMessages.value.push(msg);
  } else {
    await dmsStore.sendDM(slug.value, msg);
  }
  await nextTick();
  scrollToBottom();
};
// Lifecycle
onBeforeMount(fetchAndScroll);
watch(() => `${chatType.value}/${slug.value}`, fetchAndScroll);
</script>
