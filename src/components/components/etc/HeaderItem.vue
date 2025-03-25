<script setup lang="ts">

document.addEventListener("DOMContentLoaded", ()=>{
  const refresh = document.getElementById("refresh-button")
  let automaticUpdate = false
  let url = "https://api.smx.573.no/scores"
  let q: any;
  q = decodeURIComponent(getQueryParams("q") as any)
  let socket: any;

  document.addEventListener("updateTable", (event) => {
    const customEvent = event as CustomEvent
    if(customEvent){
      if(customEvent.detail.query != q){
        q = customEvent.detail.query
        if(socket){
          socket.close()
          socket = null
        }
      }
    }
  })

  if(refresh){
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
          refresh.style.backgroundColor = 'hsl(120, 35%, 30%)'
          refresh.style.color = 'white'
        }
        socket.onerror = function(error: any){
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
      }
    })
  }
})

function getQueryParams(key: any){
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
  background-color: #326732;
  color: white;
}
button:hover{
  padding: 10px 20px;
  transition: 1s;
}

@media only screen and (max-width: 1000px){
  h1{
    font-size: 16px;
    margin: 10px;
    width: 100px;
  }
  button{
    font-size: 12px;
    margin: 10px;
    height: 42px;
  }
}
</style>
