<template>
  <div class="flex flex-col h-screen">
    <TopNav />
    <div class="before:border-none after:border-none flex flex-1 overflow-hidden">
      <LeftSidebar />

      <div class="flex flex-col flex-1 overflow-hidden">
        <router-view />
      </div>

      <RightSidebar :users="rightSidebarUsers" />
    </div>
  </div>
</template>

<script setup>
import TopNav from '@/features/nav/TopNav.vue';
import LeftSidebar from '@/features/sidebars/ui/Left/LeftSidebar.vue';
import RightSidebar from '@/features/sidebars/ui/RightSidebar.vue';
import { computed, inject } from 'vue';
import { useRoute } from 'vue-router';
import { useDirectMessagesStore } from '@/shared/stores/directMessages.js';

const chatType = computed(() => route.params.chat); // 'channel' or 'dm'
const route = useRoute();
const dmsStore = useDirectMessagesStore();
const isChannel = computed(() => chatType.value === 'channel');

const uniqueUsers = computed(() => {
  if (isChannel.value) return dmsStore.getDMUsers;
  const seen = new Set();
  return dmsStore.directMessages
    .map((msg) => msg.user)
    .filter((user) => user && user !== 'system' && !seen.has(user) && seen.add(user));
});

const rightSidebarUsers = inject('rightSidebarUsers', uniqueUsers);
</script>
