<template>
  <div id="app">
    <router-view />
  </div>
</template>
<script>
import "@/styles/style.scss";
export default {
  data() {
    return {
      isLoading: false
    };
  }
};
</script>
