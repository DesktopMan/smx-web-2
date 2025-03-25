<script setup lang="ts">

//import TableItem from '@/components/components/main/TableItem.vue'

document.addEventListener("DOMContentLoaded", () => {
  // Code for showing and hiding UI section

  const mainBody = document.getElementById("main-ui-container");
  const container = document.getElementById("div-ui");

  // Code for the header
  if(mainBody && container) {
    const header = mainBody.children.namedItem("header")
    if (header) {
      header.addEventListener("click", () => {

        if (container.style.display === "block") {
          container.style.display = "none";
          header.textContent = "Extra settings +";
          mainBody.style.padding = '0px 5px 0px 5px';
        }
        else{
          container.style.display = "block";
          header.textContent = "Extra settings -";
          mainBody.style.padding = '0px 5px 5px 5px';
        }
      });
    }
  }

  // Code for showing and hiding UI in table
  const components = Array.from(document.getElementsByClassName("component") as HTMLCollectionOf<HTMLElement>);
  const main_table = document.querySelector(".main-display") as HTMLElement;

  if (components && components.length > 0) {
    for (const i of components) {
      i.addEventListener("click", () => {
        const name = i.getAttribute("id");
        if(main_table && name){
          let element;
          if (name == "hidePerfect" || name == "hideNonPerfect" || name == "hideHolds") {
            if (name == "hidePerfect") {
              const allNames = ["perfect1", "perfect2"];
              for (const i of allNames) {
                element = main_table.getElementsByClassName(i) as HTMLCollectionOf<HTMLElement>;
                if (element) {
                  for (const e of element) {
                    if (e.style.display === "none") {
                      e.style.display = "table-cell";
                    }
                    else{
                      e.style.display = "none";
                    }
                  }
                }
              }
            }
            else if (name == "hideNonPerfect") {
              const allNames = ["early", "late", "miss"];
              for (const i of allNames) {
                element = main_table.getElementsByClassName(i) as HTMLCollectionOf<HTMLElement>;
                if (element) {
                  for (const e of element) {
                    if (e.style.display === "none") {
                      e.style.display = "table-cell";
                    }
                    else{
                      e.style.display = "none";
                    }
                  }
                }
              }
            }
            else if(name == "hideHolds") {
              const allNames = ["green", "yellow", "red"];
              for (const i of allNames) {
                element = main_table.getElementsByClassName(i) as HTMLCollectionOf<HTMLElement>;
                if (element) {
                  for (const e of element) {
                    if (e.style.display === "none") {
                      e.style.display = "table-cell";
                    }
                    else{
                      e.style.display = "none";
                    }
                  }
                }
              }
            }
          }
          else{
            element = main_table.getElementsByClassName(name) as HTMLCollectionOf<HTMLElement>;
            if (element) {
              for (const e of element) {
                if (e.style.display === "none") {
                  e.style.display = "table-cell";
                }
                else{
                  e.style.display = "none";
                }
              }
            }
          }
        }
      });
    }
  }

  // Decide Data Length
  const dataLength = document.getElementById("dataLength") as HTMLInputElement;

  dataLength.addEventListener("change", ()=>{
    let q:any = getQueryParams("q")
    q = JSON.parse(`{${decodeURIComponent(q) as any}}`)

    let finished: any;
    if(q){
      q["_take"] = parseInt(dataLength.value as any)
      finished = JSON.stringify(q).slice(1,-1)
    }

    const updateEvent = new CustomEvent("updateTable", {
      detail: {
        query: finished
      }
    })
    document.dispatchEvent(updateEvent)
    let urlFinished: any = q
    if(urlFinished){
      urlFinished['_take'] = urlFinished['_take'] as any
      urlFinished = JSON.stringify(urlFinished).slice(1, -1)
      updateUrl('q', urlFinished)
    }
  })

  document.addEventListener("clearEvent", ()=> {
    dataLength.value = "100"
  })

    // Update '_take' from URL
  let qParams: any = getQueryParams("q")
  let decoded: any = decodeURIComponent(qParams)
  let _query: any = JSON.parse(`{${decoded}}`)

  if(!_query['_take']){
    _query['_take'] = 100
  }
  dataLength.value = _query['_take']


  function updateUrl(key: any, value: any){
    const url = new URL(window.location.href)
    url.searchParams.set(key, value)

    window.history.replaceState({}, "", url.toString())
  }
  function getQueryParams(key: any){
    const url = new URL(window.location.href)
    return url.searchParams.get(key)
  }


  // Open socket to ocr
  const ocrInput = document.getElementById("ocr-id") as HTMLInputElement
  const ocrButton = document.getElementById("ocr-button") as HTMLButtonElement
  let ocrUpdate = false
  let ocrurl = "https://smx.573.no/api/machines/"
  let socket: any

  ocrButton.addEventListener("click", ()=>{
    let id = ocrInput.value
    if(!ocrUpdate){
      socket = new WebSocket(ocrurl + id)

      socket.onopen = function(){
        console.log("Socket opened to: " + ocrurl + id)
        ocrButton.innerHTML = "Close socket"
        ocrUpdate = true
      }
      socket.onclose = function(){
        console.log("Socket closed")
        ocrButton.innerHTML = "Open socket"
        ocrUpdate = false
      }
      socket.onerror = function(error: any){
        console.log("Socket error: " + error)
      }
      socket.onmessage = function(){
        let q = decodeURIComponent(getQueryParams("q") as any)
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
      socket.close()
      socket = null
    }
    
  })

  ocrInput.addEventListener("change", ()=>{
    if(ocrUpdate){
      socket.close()
      socket = null
    }
  })
})
</script>

<template>
  <div class="main" id="main-ui-container">
    <h3 id="header">Extra settings +</h3>
    <div id="div-ui">
      <div class="flex">
        <div class="component" id="datetime">
        <p>Date & Time</p>
      </div>
      <div class="component" id="profile">
        <p>User</p>
      </div>
      <div class="component" id="song">
        <p>Song</p>
      </div>
      <div class="component" id="difficulty">
        <p>Difficulty</p>
      </div>
      <div class="component" id="grade">
        <p>Grade</p>
      </div>
      <div class="component" id="score">
        <p>Score</p>
      </div>
      <div class="component" id="hidePerfect">
        <p>Perfect</p>
      </div>
      <div class="component" id="hideNonPerfect">
        <p>Non-Perfect</p>
      </div>
      <div class="component" id="hideHolds">
        <p>Holds</p>
      </div>
      <div class="component" id="dataAmount">
        <input type="number" name="dataLength" id="dataLength" value="100" min="1" max="100">
      </div>
      </div>
      <br>
      <div class="ocr machine flex">
        <div class="component">
          <input type="text" name="ocr-id" id="ocr-id" placeholder="Machine ID . . .">
        </div>
        <button class="component" id="ocr-button">Open socket</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
@import '../../../assets/base.css';

h3{
  padding: 10px 5px 10px 5px;
  cursor: pointer;
}
.main{
  background-color: var(--dark-3);
  border-radius: 5px;
  padding: 0px 5px 0px 5px;
}
.flex{
  display: flex;
  flex-direction: row;
}
#div-ui{
  padding: 5px;
  border-radius: 15px;
  background-color: var(--dark-2);
  display: none;
}
.component{
  font-size: 12px;
  color: darkgrey;
  padding: 5px 20px;
  margin: 10px;
  border-radius: 15px;
  border: solid 1px dimgrey;
  width: fit-content;
  text-align: center;

  background-color: var(--dark-1);
  cursor: pointer;
}
input{
  border: none;
  border-radius: 10px;
  width: 50px;
  height: 35px;
  background-color: var(--dark-1);
  color: darkgrey;
}
#dataAmount{
  padding: 5px;
}
.ocr > div.component{
  padding: 1px;
  padding-right: 5px;
  margin-right: 0;
}
#ocr-id{
  width: 100%;
}
#ocr-id::placeholder{
  color: darkgray;
}

@media only screen and (max-width: 1000px){
  .main{
    display: none;
  }
}
</style>
