<script setup lang="ts">
document.addEventListener("DOMContentLoaded", ()=> {
    const table = document.querySelector("table")
    table.addEventListener("click", (event)=>{
        let tr = event.target as HTMLTableRowElement

        if(tr.tagName != "TR"){
            tr = tr.closest("tr")
        }
        if(tr.getAttribute("id") != "tr-header"){
            if(tr.classList.contains("tr-selected")){
                tr.classList.remove("tr-selected")
            }
            else{
                tr.classList.add("tr-selected")
            }
        }
    })
})
</script>


<template>
    <div>
        <button type="button" id="collage">Collage</button>
        <button type="button" id="graph">Graph</button>
        <button type="button" id="history">Hist.</button>
        <button type="button" id="clear">Clear</button>
    </div>
</template>


<style scoped>
button{
    background-color: #326732;
    padding: 10px 15px;
    border: none;
    color: white;
    margin: 0px 5px;
    border-radius: 10px;
    font-weight: 600;
    transition: 1s;
}
button#clear{
    background-color: darkred;
}
button:hover{
    cursor: pointer;
    padding: 15px 20px;
    transition: 1s;
}
</style>