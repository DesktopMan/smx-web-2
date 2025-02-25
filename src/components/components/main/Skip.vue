<script setup lang="ts">
document.addEventListener('DOMContentLoaded', ()=>{

    let page = 1;
    const switchPage = Array.from(document.getElementsByClassName('switch-page') as HTMLCollectionOf<HTMLButtonElement>);
        const dataLength = document.getElementById('dataLength') as HTMLInputElement;
        const qid = document.getElementById('q') as HTMLElement;
        const pageDisplay = document.getElementById('pageDisplay') as HTMLElement;

        pageDisplay.innerHTML = "Page " + page

    for (const e of switchPage){
        e.addEventListener("click", ()=> {
            let length = parseInt(dataLength.value)
            let q = "{" + qid.getHTML().toString() + "}"
            q = JSON.parse(q)

            let skip = 0;

            // Checks what button the user is pressing, and if they're allowed to
            if(e.getAttribute('id') === "pre-page" && page >= 2){
                page--;
                skip = (page - 1) * length;
                
                // Cleans up the parametres if q['_skip'] is empty
                if (skip != 0){
                    q['_skip'] = skip;
                }
                else{
                    delete q['_skip']
                }
            }
            else if(e.getAttribute('id') === "next-page" && page >= 1 && pageIsFilled){
                skip = page * length;
                page++;
                q['_skip'] = skip;
            }

            
            let finished = JSON.stringify(q).slice(1,-1)
            qid.innerHTML = finished
            const updateEvent = new CustomEvent("updateTable", {
              detail: {
                query: finished 
              }
            })
            document.dispatchEvent(updateEvent)
            window.scrollTo({top:0, behavior: 'smooth'})

            pageDisplay.innerHTML = "Page " + page
        })
    }

    document.addEventListener("clearEvent", ()=> {
        page = 1
        pageDisplay.innerHTML = "Page " + page
    })

    let pageIsFilled;

    document.addEventListener("arrayLength", (event)=>{
        if (event.detail.dataLength < dataLength.value){
            pageIsFilled = false
            
        }
        else{
            pageIsFilled = true
        }
    })
})
</script>

<template>
    <div>
        <button id="pre-page" class="switch-page">&lt;</button>
        <p id="pageDisplay">Page #</p>
        <button id="next-page" class="switch-page">&gt;</button>
    </div>

</template>

<style scoped>
@import '../../../assets/base.css';

div{
    bottom: 0;
    width: 100%;
    background-color: var(--dark-1);
    display: flex;
    height: 40px;
}
p{
    text-align: center;
    width: 100%;
    line-height: 0px;
}
button{
    position: absolute;
    background-color: var(--dark-2);
    height: 40px;
    width: 80px;
    color: white;
    font-weight: 800;
    cursor: pointer;
}
#pre-page{
    left: 0;
}
#next-page{
    right: 8px;
}
</style>