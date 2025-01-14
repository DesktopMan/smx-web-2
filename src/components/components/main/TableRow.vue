<script lang="ts">
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
</script>

<script setup lang="ts">
import { sendRequest } from './TableRow.vue'
import { ref } from 'vue'
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
fetchData(null)

document.addEventListener("DOMContentLoaded", () => {
  const qid = document.getElementById('q') as HTMLElement

    //#region Update Data
  document.addEventListener("updateTable", () => {
    fetchData(qid.getHTML().toString())
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

</script>

<template>
  <tr v-for="(row, index) in rows" :key="index">
    <td class="datetime created_at">{{ row.created_at }}</td>
    <td class="profile gamer">
      <div class="subdiv">
        <img class="gamer.picture_path"
             :src="'https://data.stepmaniax.com/' + row.gamer.picture_path"
             alt="profile-picture" />
        <p class="gamer.username">{{ row.gamer.username }}</p>
      </div>
    </td>
    <td class="song">
      <div class="subdiv">
        <img
          class="song.cover"
          :src="'https://data.stepmaniax.com/' + row.song.cover"
          alt="song-cover"
        />
        <p class="song.title">{{ row.song.title }}</p>
      </div>
    </td>
    <td class="artist song.artist">{{ row.song.artist }}</td>
    <td class="difficulty chart">
      <div class="subdiv">
        <p class="chart.difficulty_name">{{ row.chart.difficulty_name }}</p>
        <p class="chart.difficulty">{{ row.chart.difficulty }}</p>
      </div>
    </td>
    <td class="grade">{{ row.grade }}</td>
    <td class="score">{{ row.score }}</td>
    <td class="perfect1">{{ row.perfect1 }}</td>
    <td class="perfect2">{{ row.perfect2 }}</td>
    <td class="early">{{ row.early }}</td>
    <td class="late">{{ row.late }}</td>
    <td class="miss">{{ row.misses }}</td>
    <td class="green">{{ row.green }}</td>
    <td class="yellow">{{ row.yellow }}</td>
    <td class="red">{{ row.red }}</td>
  </tr>
</template>

<style scoped>
@import '../../../assets/base.css';


div {
  display: flex;
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
  padding: 5px 15px;
  text-align: left;
  border-bottom: solid 1px var(--dark-3);
}
img {
  height: 40px;
  border-radius: 5px;
  aspect-ratio: 1;
  margin-right: 10px;
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
</style>
