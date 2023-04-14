<template>
  <v-app>
    <v-row class="centrar">
      <img src="@/static/lg.png" alt="" class="otro">
      <Nuxt />
    </v-row>
  </v-app>
</template>

<script>
export default {
  name: 'Login'
}
</script>

<style scoped>
.centrar{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #42A5F5;
}
.otro{
  width: 5%;
  padding-bottom: 30px;
  height: auto;
  align-items: center;
  justify-content: center;
  background-color: transparent;
}
</style>
