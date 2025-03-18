<script setup lang="ts">
document.addEventListener("DOMContentLoaded", ()=> {
    //#region Select rows
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
    //#endregion

    const collage = document.getElementById("collage") as HTMLButtonElement
    const graph = document.getElementById("graph") as HTMLButtonElement
    const histogram = document.getElementById("histogram") as HTMLButtonElement
    const clear = document.getElementById("clear") as HTMLButtonElement

    function openTab(name){
        const selected = Array.from(document.getElementsByClassName("tr-selected"))
            let ids = []
            for(const tr of selected){
                if(tr.classList.contains("pc-layout")){
                    for(const child of tr.children){
                        if(child.classList.contains("score")){
                            ids.push(child.getAttribute("id"))
                        }
                    }
                }
                else{
                    const childList = tr.firstChild.firstChild as HTMLElement
                    for(const child of childList.children){
                        if(child.classList.contains("data_1")){
                            ids.push(child.getAttribute("id"))
                        }
                    }
                }
            }
        window.open("https://smx.573.no/"+name+"?scores="+ids)
    }

    collage.addEventListener("click", () => {    openTab("collage")  })
    graph.addEventListener("click", () => {   openTab("graph")    })
    histogram.addEventListener("click", () => {   openTab("histogram")    })

    clear.addEventListener("click", () => {
        const selected = Array.from(document.getElementsByClassName("tr-selected"))
        for(const tr of selected){
            tr.classList.remove("tr-selected")
        }
    })
})
</script>


<template>
    <div>
        <button type="button" id="collage">Collage</button>
        <button type="button" id="graph">Graph</button>
        <button type="button" id="histogram">Hist.</button>
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
    padding: 10px 20px;
    transition: 1s;
}
</style>