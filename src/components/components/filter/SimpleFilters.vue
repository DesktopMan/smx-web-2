

<script setup lang="ts">
import { sendRequest } from '../main/TableRow.vue';
import Tagify from '../../../assets/tagify/src/tagify.js'
import { babelParse } from 'vue/compiler-sfc';
import { after } from 'node:test';
import { url } from 'inspector';

document.addEventListener('DOMContentLoaded', () => {
  //#region Code for showing and hiding filters

  const hideButton = Array.from(document.getElementsByClassName('exit') as HTMLCollectionOf<HTMLElement>)
  const selector = document.getElementById('filterSelect')
  const allInputs = [
    'datetimes',
    'userInput',
    'idInput',
    'songInput',
    'artistInput',
    'scoreInput',
    'gradeInput',
    'chartInput',
    'grouping'
  ]
  const startInputs = [
    allInputs[1],
    allInputs[3],
    allInputs[5]
  ]

  //#endregion

  //#region Hide and Show Inputs
  
  // When refreshing the page
  for (let i = 0; i < allInputs.length; i++) {
    const value = allInputs[i]
    if(!startInputs.includes(value)) {
      if (selector) {
        const options = Array.from(selector.children as HTMLCollectionOf<HTMLElement>)
        const showOption = options.find(option => option instanceof HTMLOptionElement && option.value === value)
        if (showOption) {
          showOption.style.display = 'block'
        }
      }
      const hideElement = document.getElementById(value) as HTMLElement
      hideElement.style.display = 'none'
    }
  }

  // Hides filters
  function hideInputs(element: HTMLElement) {
    element.style.display = 'none'
    const id = element.id
    if (selector) {
      const options = Array.from(selector.children as HTMLCollectionOf<HTMLElement>)
      const showOption = options.find(option => option instanceof HTMLOptionElement && option.value === id)
      if (showOption) {
        showOption.style.display = 'block'
      }
    }
  }

  // Shows filters
  function showInputs(element: HTMLElement) {
    element.style.display = 'flex'
    const id = element.id
    if (selector) {
      const options = Array.from(selector.children as HTMLCollectionOf<HTMLElement>)
      const showOption = options.find(option => option instanceof HTMLOptionElement && option.value === id)
      if (showOption) {
        showOption.style.display = 'none'
      }
    }
  }

  // Event listener for the 'hide button'
  for (const e of hideButton) {
    e.addEventListener('click', (event) => {
      event.preventDefault()
      const target1 = event.target as HTMLElement
      const target2 = target1.parentElement
      if (target2){
        const element = target2.parentElement
        if (element){
          hideInputs(element)
        }
      }
    })
  }

  // Event listener for the filter selector
  if (selector) {
    selector.addEventListener('change', (event) => {
      const target = event.target as HTMLSelectElement
      const selectedOption = target.options[target.selectedIndex]

      if (selectedOption) {
        const elementId = selectedOption.value
        const element = document.getElementById(elementId)
        target.value = ""

        if (element) {
          showInputs(element)
        }
      }
    })
  }

  //#endregion

  //#region  Variables for parameters and tagging system
  const tags = Array.from(document.getElementsByClassName('tags') as HTMLCollectionOf<HTMLInputElement>);
  const fullTags = Array.from(document.querySelectorAll('input.tagify__input') as HTMLCollectionOf<HTMLInputElement>)
  const datetimeInputs = Array.from(document.getElementsByName('created_at') as HTMLCollectionOf<HTMLInputElement>);
    const dataLength = document.getElementById("dataLength") as HTMLInputElement
    const group_input = document.getElementById('group-by') as HTMLInputElement

  const qid = document.getElementById('q') as HTMLElement;
  let q: Array<object> = [];

  // Adds Tagify to each tag-input, and eventListener for changes
  for (const input of tags){
    const tagify = new Tagify(input, {
    delimiters:",",
    pattern: null,
    mode: null,
    tagTextProp: input.getAttribute("name"),
    placeholder: input.getAttribute("placeholder"),
    maxTags: Infinity,
    duplicates: false,
    userInput: true,
    mixTagsInterpolator: ['[[', ']]'],
    trim: true,
    pasteAsTags: true,
    focusable: true,
  })

    input.addEventListener("change", function(){
      updateParams();
  });
  }

  const tagifyGroup = new Tagify(group_input, {
    delimiters:",",
    pattern: null,
    mode: null,
    tagTextProp: group_input.getAttribute("name"),
    placeholder: group_input.getAttribute("placeholder"),
    maxTags: Infinity,
    duplicates: false,
    userInput: true,
    mixTagsInterpolator: ['[[', ']]'],
    trim: true,
    pasteAsTags: true,
    focusable: true,

    enforceWhitelist: false,
    whitelist: ["gamer_id", "song_chart_id"],
    dropdown: {
      enabled: 0,
      maxItems: 7,
      position: "input",
      closeOnSelect: false
    }
  })

  group_input.addEventListener("change", function(){
    updateParams();
  })

  // eventListener for date-time inputs (they don't use tags)
  for (const input of datetimeInputs){
    input.addEventListener("change", function(){
      updateParams();
    })
  }

  // Updates parameters
  function updateParams(){
    let skip = q["_skip"]
    q = {}; // Resets 'q' every time function is called

    // Loops through each input field to collect the values
    for (const input of fullTags){
      // Checks if value is not empty
      if (input.value.trim() !== ""){
        let finalValue = {};

        // Parses value to JSON
        let parsed = JSON.parse(input.value);
        
        // Deletes the "value" parameter from each parsed object
        parsed.forEach(obj => {
          delete obj.value;

          let key = Object.keys(obj)[0]
          let value = String(Object.values(obj))
          
          let ops = {
            ">=":"gte",
            "<=":"lte",
            ">":"gt",
            "<":"lt",
            "!":"nin"
          }

          for(const op in ops){
            if(value.startsWith(op)){ // ">=5000"
              let eValue = value.replace(op, "")   // "5000"
              if(parseInt(eValue) == eValue){
                eValue = parseInt(eValue) // 5000
              }
              if(op != "!"){
                finalValue[ops[op]] = eValue // {"gte": 5000}
              }
              // Operator "nin" requires an array. This is made
              else{
                if(!finalValue[ops[op]]){
                  finalValue[ops[op]] = []
                }
                finalValue[ops[op]].push(eValue) // {"nin": [5000]}
              }
              break;
            }
          }

          if (!q[key]){
            q[key] = []
          }

          if(JSON.stringify(finalValue) === '{}'){
            if(parseInt(value) == value){
              value = parseInt(value)
            }
            if(!Array.isArray(q[key])){
              q[key] = []
            }
            q[key].push(value)
          }
          else{
            
            q[key] = finalValue
          }
          
        });
        
      }
    }

    // Loops through the date-time input fields to collect the values
    for (const input of datetimeInputs){
      // Checks if value is not empty
      if (input.value.trim() !== ""){
        let dateValue = {};
        let date = new Date(input.value)
        date = date.toISOString()
        
        let key = "created_at"

        if(!q[key]){
           q[key] = []
         }
        else{
          dateValue = q[key]
        }

         if (input.getAttribute("id") === "dtimeFrom"){
           dateValue["gte"] = date
         }
         else if(input.getAttribute("id") === "dtimeTo"){
           dateValue["lte"] = date
         }

         q[key] = dateValue
      }
    }

    if(dataLength.value != 100){
      q["_take"] = parseInt(dataLength.value)
    }

  // Updates the placeholder text display
  q["_skip"] = skip
  let finished = JSON.stringify(q)
  finished = finished.slice(1, -1)

  // Updates URL and sends request
  let urlReady = finished.toString()
  updateUrl("q", urlReady)
  console.log(urlReady)

  qid.innerHTML = finished
  const updateEvent = new CustomEvent("updateTable", {
    detail: {
      query: finished 
    }
  })
  document.dispatchEvent(updateEvent)

  }
  //#endregion

  //#region Update fields from from URL
  let fullParams = getQueryParams("q")
  fullParams = "{"+ fullParams+ "}"
  fullParams = JSON.parse(fullParams)

  for (const tag of fullTags){
    if(fullParams[tag.getAttribute("name")]){
      const parent = tag.parentNode
      const children = Array.from(parent.children)
      for (const child of children){
        if (child.classList.contains("tagify")){
          showInputs(parent)
          const newChild = child.querySelector('span')
          newChild.focus()
          newChild.innerHTML = fullParams[tag.getAttribute("name")]
          newChild.blur()
        }
      }
    }
  }

  //#endregion

  //#region Placeholders
  const tagFields = Array.from(document.querySelectorAll('span.tagify__input') as HTMLCollectionOf<HTMLInputElement>)
  for(const t of tagFields){
    t.classList.add('placeholder')

    t.addEventListener("click", ()=>{
      t.classList.remove('placeholder')
    })
    t.addEventListener("blur", ()=>{
      t.classList.add('placeholder')
    })
  }
  //#endregion

  //#region Clear-button
  const clearButton = document.getElementById('clear-button') as HTMLButtonElement
  clearButton.addEventListener("click", () => {
    for (const e of fullTags  ){
      e.value = ""
    }
    for (const e of datetimeInputs){
      e.value = null
    }
    qid.innerHTML = ""
    updateUrl("q", "")
  })
  //#endregion

});


//#region  Sleep-function
function sleep(ms){
  return new Promise(resolve => setTimeout(resolve, ms))
}
//#endregion

//#region URL Query Return
function getQueryParams(key){
    const url = new URL(window.location.href)
    return url.searchParams.get(key)
  }

  function updateUrl(key, value){
    const url = new URL(window.location.href)
    url.searchParams.set(key, value)

    window.history.replaceState({}, "", url.toString())
  }
//#endregion


</script>

<template>
  <div class="main">
    <div class="main-filter">
      <p id="q"></p>
      <div id="datetimes">
        <input type="datetime-local" name="created_at" id="dtimeFrom">
        <input type="datetime-local" name="created_at" id="dtimeTo">
        <div class="flex-col">
          <button id="dtimeButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>

      <div id="userInput">
        <input type="text" name="gamer.username" id="username" placeholder="Username" class="tags tagify__input">
        <div class="flex-col">
          <button id="userButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>
      <div id="idInput">
        <input type="text" name="gamer.id" id="userId" placeholder="User ID" class="tags tagify__input">
        <div class="flex-col">
          <button id="idButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>

      <div id="songInput">
        <input type="text" name="song.title" id="song" placeholder="Song Title" class="tags tagify__input">
        <div class="flex-col">
          <button id="songButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>
      <div id="artistInput">
        <input type="text" name="song.artist" id="artist" placeholder="Artist" class="tags tagify__input">
        <div class="flex-col">
          <button id="artistButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>

      <div id="scoreInput">
        <input type="text" name="score" id="score" placeholder="Score" class="tags tagify__input">
        <div class="flex-col">
          <button id="scoreButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>
      <div id="gradeInput">
        <input type="text" name="grade" id="grade" placeholder="Grade" class="tags tagify__input">
        <div class="flex-col">
          <button id="gradeButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>
      <div id="chartInput">
        <!--<select name="chart.difficulty_name" id="chartSelector">
          <option value="">-Select Chart-</option>
          <option value="basic">Basic</option>
          <option value="easy,easy+">Easy (+)</option>
          <option value="easy">Easy</option>
          <option value="easy+">Easy+</option>
          <option value="hard,hard+">Hard (+)</option>
          <option value="hard">Hard</option>
          <option value="hard+">Hard+</option>
          <option value="wild">Wild</option>
          <option value="dual,dual+">Dual (+)</option>
          <option value="dual">Dual</option>
          <option value="dual+">Dual+</option>
          <option value="full,full+">Full (+)</option>
          <option value="full">Full</option>
          <option value="full+">Full+</option>
        </select>-->
        <input type="text" name="chart.difficulty" id="difficulty" class="tags tagify__input" placeholder="Difficulty">
        <div class="flex-col">
          <button id="chartButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>

      <div id="grouping">
        <input type="text" name="_group_by" id="group-by" placeholder="Grouping" class="tagify__input">
        <div class="flex-col">
          <button id="gradeButton">?</button>
          <button class="exit">x</button>
        </div>
      </div>
    </div>
    
    <div class="select-filters">
      <select name="selectFilters" id="filterSelect">
        <option value="">-Select Filter-</option>
        <option value="datetimes">Date & Time</option>
        <option value="userInput">Username</option>
        <option value="idInput">User ID</option>
        <option value="songInput">Song Title</option>
        <option value="artistInput">Artist</option>
        <option value="scoreInput">Score</option>
        <option value="gradeInput">Grade</option>
        <option value="chartInput">Difficulty</option>
        <option value="grouping">Grouping</option>
      </select>
    </div>
    
  </div>
    
  

</template>

<style scoped>
@import '../../../assets/base.css';

/*#q{
  display: none;
}*/

div{
  display: flex;
  flex-wrap: wrap;
  overflow: hidden;
  max-width: 100%;
}
.flex-col{
  display: flex;
  flex-direction: column;
}
.select-filters{
  width: 250px;
}
.select-filters select{
  width: 100%;
  height: 40px;
  margin: 10px;
}
.main-filter{
  width: 1200px;
}
.main-filter > div{
  min-width: 247px;
  max-width: fit-content;
  min-height: 40px;
  margin: 10px;

  font-size: 20px;

}
.tags, #group-by{
  opacity: 0;
  position: absolute;
  pointer-events: none;
  z-index: -1;
}

input{
  width: 210px;
  margin-right: 5px;
  padding: 5px;
  background-color: var(--dark-1);
  border: solid 1px dimgrey;
  border-radius: 10px;
  color: darkgrey;
}
input::placeholder{
  opacity: 1 !important;
  color: darkgrey !important;
}
button{
  width: 20px;
  height: 20px;
  font-size: 10px;
  border: solid dimgrey 1px;
  background-color: var(--dark-3);
  color: white;
  border-radius: 50%;

  font-family: sans-serif;
  cursor: pointer;
}
select{
  appearance: none;
  border-radius: 10px;
  margin-right: 10px;
  padding: 5px;
  background-color: var(--dark-1);
  color: darkgrey;
}
option{
  display: none;
}
.main-filter select{

  width: 120px;
}

#datetimes{
  width: 374px;
}
#datetimes input{
  width: 160px;
}
</style>