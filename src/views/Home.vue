<script setup>
import { ElButton } from 'element-plus';
import { useRouter } from 'vue-router';

const router = useRouter();

const goToDesignPage = () => {
  router.push('/design');
};
</script>

<template>
  <div>
    <ElButton @click="goToDesignPage">跳转设计页面</ElButton>
  </div>
</template>

<style scoped>
/* 可以添加样式 */
</style>