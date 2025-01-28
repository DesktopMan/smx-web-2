<script setup lang="ts">
import { error } from 'console';


document.addEventListener("DOMContentLoaded", ()=>{
  const refresh = document.getElementById("refresh-button")
  let automaticUpdate = false
  let query = "https://api.smx.573.no/scores"
  let socket

  document.addEventListener("updateTable", (event) => {
    if (event.detail.query === query){

    } 
    else{
      query = event.detail.query
      if(socket){
        socket.close()
        socket = null
      }
    }
  })

  refresh.addEventListener("click", ()=>{
    if (!automaticUpdate){
      automaticUpdate = true
      socket = new WebSocket(query)

      socket.onopen = function(){
        console.log("Socket opened to: "+ query)
      }
      socket.onclose = function(){
        console.log("Socket closed.")
      }
      socket.onerror = function(error){
        console.log("Socket error: "+ error)
      }
      socket.onmessage = function(){
        const updateEvent = new CustomEvent("updateTable", {
          detail: {
            query: query 
          }
        })
        document.dispatchEvent(updateEvent)
        console.log("Message recived. Data updated.")
      }
    }
    else{
      automaticUpdate = false
      if (socket){
        socket.close()
        socket = null
      }
    }
  })
})

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
