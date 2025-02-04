<script setup lang="ts">
import { error } from 'console';
import { ref } from 'vue';


document.addEventListener("DOMContentLoaded", ()=>{
  const refresh = document.getElementById("refresh-button")
  let automaticUpdate = false
  let url = "https://api.smx.573.no/scores"
  let q = decodeURIComponent(getQueryParams("q"))
  let socket

  document.addEventListener("updateTable", (event) => {
    if (event.detail.query == q){

    } 
    else{
      q = event.detail.query
      if(socket){
        socket.close()
        socket = null
      }
    }
  })

  refresh.addEventListener("click", ()=>{
    if (!automaticUpdate){
      automaticUpdate = true
      socket = new WebSocket(url)
      refresh.style.backgroundColor = 'lightgreen'
      refresh.style.color = 'black'

      socket.onopen = function(){
        console.log("Socket opened to: "+ url)
      }
      socket.onclose = function(){
        console.log("Socket closed.")
      }
      socket.onerror = function(error){
        console.log("Socket error: "+ error)
      }
      socket.onmessage = function(){
        if (q == ""){
          q = null
        }
        const updateEvent = new CustomEvent("updateTable", {
          detail: {
            query: q
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
      refresh.style.backgroundColor = 'hsl(120, 35%, 30%)'
      refresh.style.color = 'white'
    }
  })
})

function getQueryParams(key){
    const url = new URL(window.location.href)
    return url.searchParams.get(key)
  }

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
  transition: 1s;
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
  padding: 10px 20px;
  transition: 1s;
}
</style>
