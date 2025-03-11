<script setup lang="ts">
import { createTypeReferenceDirectiveResolutionCache } from 'typescript';
import TableRow from './TableRow.vue'

document.addEventListener('DOMContentLoaded', () => {
  const headers = Array.from(document.getElementsByClassName('displayHeader') as HTMLCollectionOf<HTMLElement>)
  for (const h of headers){
    h.addEventListener("click", () => {
      let name = h.getAttribute("name").slice(2)
      updateSorting(name)
    });
  }

  function updateSorting(name :string){
    let q = getQueryParams("q")
    q = JSON.parse(`{${decodeURIComponent(q)}}`)

    let oldName = q['_sort_by']
    q['_sort_by'] = name
    if(q['_order'] != 'desc' || oldName != name){
      q['_order'] = 'desc'
    }
    else{
      q['_order'] = 'asc'
    }

    let finished = JSON.stringify(q).slice(1,-1)
    const updateEvent = new CustomEvent("updateTable", {
      detail: {
        query: finished 
      }
    })
    document.dispatchEvent(updateEvent)
  }
})

function updateUrl(key, value){
    const url = new URL(window.location.href)
    url.searchParams.set(key, value)

    window.history.replaceState({}, "", url.toString())
  }
  function getQueryParams(key){
    const url = new URL(window.location.href)
    return url.searchParams.get(key)
  }

</script>

<template>
  <div class="main-display">
    <table>
      <thead>
      <tr>
        <th id="datetimeDisplay" class="datetime displayHeader" name="h_created_at">
          Datetime
        </th>
        <th id="profileDisplay" class="profile displayHeader" name="h_gamer.username">
          User
        </th>
        <th id="songDisplay" class="song displayHeader" name="h_song.title">
          Song
        </th>
        <th id="difficultyDisplay" class="difficulty displayHeader" name="h_chart.difficulty">
          Difficulty
        </th>
        <th id="gradeDisplay" class="grade displayHeader" name="h_grade">
          Grade
        </th>
        <th id="scoreDisplay" class="score displayHeader" name="h_score">
          Score
        </th>
        <th id="perfect1Display" class="perfect1 displayHeader" name="h_perfect1">
          Perfect!!
        </th>
        <th id="perfect2Display" class="perfect2 displayHeader" name="h_perfect2">
          Perfect
        </th>
        <th id="earlyDisplay" class="early displayHeader" name="h_early">
          Early
        </th>
        <th id="lateDisplay" class="late displayHeader" name="h_late">
          Late
        </th>
        <th id="missDisplay" class="miss displayHeader" name="h_miss">
          Miss
        </th>
        <th id="greenDisplay" class="green displayHeader" name="h_green">
          Green
        </th>
        <th id="yellowDisplay" class="yellow displayHeader" name="h_yellow">
          Yellow
        </th>
        <th id="redDisplay" class="red displayHeader" name="h_red">
          Red
        </th>
        <th id="pbdDisplay" class="personal_best_difference pbdHeader" name="h_pbd">
          PB Diff
        </th>
      </tr>
      </thead>
      <tbody>
        <TableRow />
      </tbody>
    </table>
  </div>
</template>

<style scoped>
@import '../../../assets/base.css';


div{
  display: flex;
}
.main-display {
  margin-left: 10px;
  font-size: 14px;
  width: auto;
}
td{
  margin: 0 5px;
  height: 40px;
  padding: 5px 15px;
  text-align: center;
  border-bottom: solid 1px var(--dark-3);
}
th{
  color: powderblue;
  padding: 5px 5px;
  border-bottom: solid 1px var(--dark-3);
  cursor: pointer;
}

@media only screen and (max-width: 1000px){
  tr{
    display: none;
  }
}
</style>
