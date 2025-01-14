<script setup lang="ts">

</script>

<template>
  <div>
    <h1>StepManiaX Score Browser</h1>
    <button id="refresh-button">REFRESH</button>
    <button id="clear-button">CLEAR</button>
  </div>
  
</template>

<style scoped>
div{
  display: flex;
}
h1{
  margin-left: 10px;
  margin-right: 30px;
}
button{
  margin: 20px;
  margin-right: 0px;
  padding: 10px;

  border: none;
  border-radius: 10px;

  font-weight: 900;
  background-color: hsl(120, 35%, 30%);
  color: white;
}
button:hover{
  background-color: hsl(190, 35%, 45%);
}
</style>
