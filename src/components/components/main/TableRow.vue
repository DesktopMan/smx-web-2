<script lang="ts">
import icon from "../../../assets/icon.png"
//Sends request to API
export async function sendRequest(q: string | null) {
  let response
  let data
  if (q) {
    const url = `https://api.smx.573.no/scores?params={${q}}`
    response = await fetch(url)
  } else {
    response = await fetch('https://api.smx.573.no/scores')
  }
  if (response) {
    data = await response.json()
  }
  return data
}

export function formatDate(timestamp){
  var date = new Date(timestamp)

  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  const hour = String(date.getHours()).padStart(2, '0')
  const minute = String(date.getMinutes()).padStart(2, '0')

  const returner = `${year}-${month}-${day} ${hour}:${minute}`
  return returner
}

export function profilePicture(link){
  if(!link){
    return icon
  }
  else{
    return 'https://data.stepmaniax.com/' + link
  }
}

export function grade(grade, cleared){
  grade = 7 - grade
  let icon = "https://smx.573.no/img/grades/"
  if(cleared){
    icon += grade + ".png"
  }
  else{
    icon += "f" + grade + ".png"
  }
  return icon
}

export function formatNull(number){
  if(!number){
    return 0
  }
  else{
    return number
  }
}

export function personalBest(score, pb, pbp, showNull){
  let difference
  if(score === pb){
    difference = pb - pbp
  }
  else{
    difference = score - pb
  }
  if(difference == pb){
    if(!showNull){
      difference = ""
    }
    else{
      difference = 0
    }
  }
  if(difference > 0){
    difference = "+"+difference
  }
  return difference
}
</script>

<script setup lang="ts">
import { format } from 'path'
import { sendRequest } from './TableRow.vue'
import { ref } from 'vue'
import { time } from 'console'
const rows = ref([])

async function fetchData(q: any) {
  const data = await sendRequest(q)
  rows.value = data
  const arrayLength = new CustomEvent("arrayLength",  {
    detail:{
      dataLength: data.length
    }
  })
  document.dispatchEvent(arrayLength)
}
const query = getQueryParams("q")
if (query == "" || !query)
{
  fetchData(null)
}

document.addEventListener("DOMContentLoaded", () => {
  const qid = document.getElementById('q') as HTMLElement

    //#region Update Data
  document.addEventListener("updateTable", (event) => {
    if(event.detail.query){
      fetchData(event.detail.query)
    }
    else{
      fetchData(null)
    }
  })

  //#endregion

  const refreshButton = document.getElementById('refresh-button') as HTMLButtonElement
  refreshButton.addEventListener("click", () => {
    if(qid.innerHTML === ""){
      fetchData(null)
    }
    else{
      fetchData(qid.getHTML().toString())
    }
  })

  const clearButton = document.getElementById('clear-button') as HTMLButtonElement
  clearButton.addEventListener("click", () => {
    fetchData(null)
    const clearEvent = new CustomEvent("clearEvent")
    document.dispatchEvent(clearEvent)
  })

})

function getQueryParams(key){
    const url = new URL(window.location.href)
    return url.searchParams.get(key)
  }

  function updateUrl(key, value){
    const url = new URL(window.location.href)
    url.searchParams.set(key, value)

    window.history.replaceState({}, "", url.toString())
  }

</script>

<template>
  <!-- PC-Layout -->
  <tr v-for="(row, index) in rows" :key="index" id="pc-layout">
    <td class="datetime created_at">{{ formatDate(row.created_at) }}</td>
    <td class="profile gamer">
      <div class="subdiv flex">
        <div class="picture_path">
          <img class="gamer.picture_path"
             :src="profilePicture(row.gamer.picture_path)" />
        </div>
        <p class="gamer.username">{{ row.gamer.username }}</p>
      </div>
    </td>
    <td class="song">
      <div class="subdiv flex">
        <img
          class="song.cover"
          :src="'https://data.stepmaniax.com/' + row.song.cover"
        />
        <p class="song.title song.artist">{{ row.song.title }} - {{ row.song.artist }}</p>
      </div>
    </td>
    <td class="difficulty chart">
      <div class="subdiv flex">
        <p class="chart.difficulty_name flex">{{ row.chart.difficulty_name }}</p>
        <p class="chart.difficulty flex">{{ row.chart.difficulty }}</p>
      </div>
    </td>
    <td class="grade"><img class="grade" :src="grade(row.grade, row.cleared)"></td>
    <td class="score"><a target="_blank" :href="'https://scores.stepmaniax.com/' +  row._id ">{{ row.score }}</a></td>
    <td class="perfect1">{{ row.perfect1 }}</td>
    <td class="perfect2">{{ row.perfect2 }}</td>
    <td class="early">{{ row.early }}</td>
    <td class="late">{{ row.late }}</td>
    <td class="miss">{{ row.misses }}</td>
    <td class="green">{{ row.green }}</td>
    <td class="yellow">{{ row.yellow }}</td>
    <td class="red">{{ row.red }}</td>
    <td class="personal_best_difference">{{ personalBest(row.score, row.personal_best, row.personal_best_previous, true) }}</td>
  </tr>

  <!-- Mobile Layout -->
  <tr v-for="(row, index) in rows" :key="index" id="mobile-layout">
    <td>
      <div class="mobile-data">
        <div class="datetime created_at">
          {{ formatDate(row.created_at) }}
        </div>
        <div class="profile gamer chart">
          <div class="subdiv flex">
            <img class="gamer.picture_path" :src="profilePicture(row.gamer.picture_path)">
            <p class="gamer.username">{{ row.gamer.username }}</p>
            <p class="chart">{{ row.chart.difficulty_name }} {{ row.chart.difficulty }}</p>
          </div>
        </div>
        <div class="song">
          <div class="subdiv flex">
            <img class="song.cover" :src="profilePicture(row.song.cover)">
            <p class="song.title">{{ row.song.title }} - {{ row.song.artist }}</p>
          </div>
        </div>
        <div class="data_1">
          <div class="subdiv flex">
            <img class="grade cleared" :src="grade(row.grade, row.cleared)">
            <p class="score">
              <a target="_blank" :href="'https://scores.stepmaniax.com/' +  row._id ">{{ row.score }}</a>
            </p>
            <p>|</p>
            <p class="perfect1">{{ row.perfect1 }}</p>
            <p class="perfect2">{{ row.perfect2 }}</p>
            <p class="early">{{ row.early }}</p>
            <p class="late">{{ row.late }}</p>
            <p class="miss">{{ formatNull(row.miss) }}</p>
            <p class="green">{{ row.green }}</p>
            <p class="yellow">{{ row.yellow }}</p>
            <p class="red">{{ row.red }}</p>
          </div>
        </div>
        <div class="data_2">
          <div class="subdiv flex">
            <img src="https://cdn.discordapp.com/emojis/614203000222646278.png">
            <p class="personal_best">{{ row.personal_best }}</p>
            <p class="personal_best_difference">{{ personalBest(row.score, row.personal_best, row.personal_best_previous, false) }}</p>
          </div>
        </div>
      </div>
    </td>
  </tr>
</template>

<style scoped>
@import '../../../assets/base.css';


.flex {
  display: flex;
}
a{
  text-decoration: none;
}
p {
  margin: auto 0;
}
tr{
  overflow: scroll;
}
td {
  margin: 0 5px;
  height: 40px;
  width: min-content;
  max-width: 250px;
  padding: 5px 15px;
  text-align: left;
  border-bottom: solid 1px var(--dark-3);
  overflow: hidden;
}
img {
  height: 40px;
  border-radius: 5px;
  aspect-ratio: 1;
  margin-right: 10px;
}
td.datetime{
  max-width: 100px;
}
.subdiv > p {
  margin-right: 5px;
}
.perfect2 {
  color: bisque;
}
.early {
  color: greenyellow;
}
.late {
  color: lightsteelblue;
}
.miss {
  color: lightcoral;
}
.green {
  color: forestgreen;
}
.yellow {
  color: gold;
}
.red {
  color: red;
}
td.personal_best_difference{
  text-align: center;
}


#mobile-layout{
  display: none;
}
@media only screen and (max-width: 1000px){
  #pc-layout{
    display: none;
  }

  *{
    font-size: 12px;
    text-align: center;
  }
  #mobile-layout{
    display: table
  }
  div.mobile-data{
    width: 90vw;
    border: 2px solid var(--dark-3);
    border-radius: 10px;
    padding: 5px;
  }
  div.mobile-data > div{
    width: 100%;
  }
  .mobile-data > div.datetime, .mobile-data > div.profile, .mobile-data > div.song, div.data_1{
    padding-bottom: 3px;
    margin-bottom: 5px;
    border-bottom: solid 1px var(--dark-3);
  }
  div.datetime{
    text-align: center;
    font-weight: 800;
  }
  p.chart{
    width: 100%;
    text-align: right;
  }
  p.personal_best{
    color: gold;
  }
  p.personal_best_difference{
    width: 100%;
    text-align: right;
  }
}
</style>