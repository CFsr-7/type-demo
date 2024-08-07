<template>
  <router-view></router-view>
</template>

<script setup lang="ts">

    // 禁用页面的ctrl功能，来禁止ctrl+s保存功能
    window.addEventListener('keydown', function (e) {
        if(e.keyCode == 83 && (navigator.platform.match('Mac') ? e.metaKey : e.ctrlKey)) {
            e.preventDefault();
        }
    })

</script>

<style>
  * {
    padding: 0;
    margin: 0;
  }
  body {
    background: #282c34;
  }

</style>
