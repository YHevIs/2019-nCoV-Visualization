<template>
  <div id="app">
    <el-container>
      <el-header>
        <router-link to="/">主页</router-link> | 
        <a href="https://yhevis.top">个人网站</a>
      </el-header>
      <router-view />
    </el-container>
  </div>
</template>

<style lang="scss">
  a{
    margin: 0 10px;
  }
</style>
