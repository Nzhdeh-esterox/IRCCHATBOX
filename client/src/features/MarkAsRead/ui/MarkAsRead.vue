<script setup>
import { MailCheck } from 'lucide-vue-next';
import { setLastViewed } from '@/helpers/storage';
import { useDirectMessagesStore } from '@/shared/stores/directMessages';
import { computed } from 'vue';
import { useChannelsStore } from '@/shared/stores/channels';
const dmStore = useDirectMessagesStore();
const channelStore = useChannelsStore();
const users = computed(() => dmStore.getDMUsers);
const channels = computed(() => channelStore.getChannels);

function markAsRead() {
  users.value.forEach((user) => {
    setLastViewed(`dm:${user}`);
  });
  channels.value.forEach((channel) => {
    setLastViewed(`channel:${channel}`);
  });
  channelStore.fetchLatestTimestamps();
  dmStore.fetchLatestTimestamps();
}
</script>

<template>
  <button
    :onclick="markAsRead"
    type="button"
    title="Mark all as read"
    class="flex items-center gap-2 my-1.5 w-fit p-1 rounded-md hover:bg-darkBlue/30 dark:hover:bg-gray-300/40"
  >
    <MailCheck :size="16" />
  </button>
</template>

<style scoped></style>
