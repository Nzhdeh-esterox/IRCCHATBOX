<template>
  <button
    class="absolute bottom-[-3.95px] text-black dark:text-white right-0 opacity-0 group-hover:opacity-100 text-xs rounded"
    @click="emojiClick"
  >
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="16"
      height="16"
      viewBox="0 0 24 24"
      id="add-reaction"
    >
      <rect width="24" height="24" fill="none" />
      <path
        d="M8.4,0a8.4,8.4,0,0,0,0,16.8h0a8.4,8.4,0,0,0,8.4-8.4"
        transform="translate(2.4 4.8)"
        fill="none"
        stroke="currentColor"
        stroke-miterlimit="10"
        stroke-width="1.5"
      />
      <path
        d="M0,0A4.807,4.807,0,0,0,1.042,1.557,4.785,4.785,0,0,0,4.436,2.963h0A4.785,4.785,0,0,0,7.83,1.557,4.807,4.807,0,0,0,8.872,0"
        transform="translate(6.364 15.037)"
        fill="none"
        stroke="currentColor"
        stroke-miterlimit="10"
        stroke-width="1.5"
      />
      <circle
        cx="1.2"
        cy="1.2"
        r="1.2"
        transform="translate(7.2 9.6)"
        fill="#ffffff"
        stroke="currentColor"
        stroke-width="1"
      />
      <circle
        cx="1.2"
        cy="1.2"
        r="1.2"
        transform="translate(12 9.6)"
        fill="#ffffff"
        stroke="currentColor"
        stroke-width="1"
      />
      <path
        d="M1.2,0V7.2"
        transform="translate(18 1.2)"
        fill="none"
        stroke="currentColor"
        stroke-linecap="square"
        stroke-miterlimit="10"
        stroke-width="1.5"
      />
      <path
        d="M0,1.2H7.2"
        transform="translate(15.6 3.6)"
        fill="none"
        stroke="currentColor"
        stroke-linecap="square"
        stroke-miterlimit="10"
        stroke-width="1.5"
      />
    </svg>
  </button>
</template>
<script setup>
defineProps({
  emojiClick: Function
});
</script>
