<template>
  <div class='election'>
      
    <div class="row listenkreuz">
        <div class="holder">
            <button class="votefield" @click='toggleCross'>
                <div class="cross" v-if="showCross" ref="cross">X</div>
            </button>
        </div>
        <div class="liner">
            <p>
                Sie haben noch {{ total }} Stimmen!
            </p>
            <p>
                Gehäufelte Stimmen: {{ accumulated }}
            </p>
        </div>
    </div>

    <div class="row" v-for="(item, index) in this.candidates" v-bind:key="index">
        <div class="holder">
            <div class="nr" >
                <p>00{{item}}</p>
            </div>
            <input type="text" maxlength="1" ref="candidate" class="votefield" @change="handleVoting(index, $event)"/>
        </div>
        <div class="liner"></div>
    </div>
      
  </div>
</template>

<script>

    export default {
        data() {
            return {
                total: 24,        // Gesamtstimmen
                candidates: 24,
                accumulated: 0,
                previousVote: 0,
                showCross: false,
                candidatesWithAccumulation: [],
                candidatesWithVoting: [],
                candidateVote: "" // hält die Stimmen, die der Kandidat bekommen hat
            }
        },
        beforeCreate() {    /*console.log('beforeCreated');*/   },
        created() {         /*console.log('created');*/         },
        beforeMount() {     /*console.log('beforeMount');*/     },
        mounted() {         /*console.log('mountd');*/          },
        beforeUpdate() {    /*console.log('beforeUpdate');*/    },
        updated() {

            // Alle Kandidaten sammeln
            let allCandidates = this.$refs.candidate;

            /**
             * Hier muss der Array zurückgesetzt werden, sonst zähl er das neue daszu
             */
            this.candidatesWithAccumulation = [];
            allCandidates.forEach((element, index) => {
                if(element.value > 1){
                    this.candidatesWithAccumulation.push(index);
                }
            });

            /**
             * Alle Kandidaten sammlen, die irgend eine Stimme erhalten haben
             */
            this.candidatesWithVoting = [];
            allCandidates.forEach((element, index) => {
                if(element.value >= 1){
                    let candi = {
                        idx: index,
                        vote: element.value
                    };
                    this.candidatesWithVoting.push(candi);
                }
            });

        },
        methods: {
            handleVoting(index, event){

                let assignedVote = event.srcElement.value; // Eingegebener Wert

                if( assignedVote == '' ){
                    try{
                        this.total += parseInt(this.candidatesWithVoting.find( c => c.idx == index ).vote);
                    }catch(err){
                        console.log(err);
                    }
                    return;
                }

                if( this.total > 0 ){ // Noch Stimmen verfügbar

                    // Stimmen wurden gehäufelt
                    if( assignedVote > 1 && assignedVote <= 3 ){

                        this.accumulated += parseInt(assignedVote);

                    }else if( assignedVote > 3 ){

                        try {
                            event.srcElement.value = this.candidatesWithVoting[index].vote;
                        } catch(err) {
                            event.srcElement.value = '';
                        }

                        alert("Bitte nicht mehr als 3 Stimmen je Kandidat vergeben!")
                        return;

                    }

                    // Angegebene Stimmen von Gesamtstimmen abziehen
                    this.total -= assignedVote;

                }else{ // Keine Stimmen mehr verfügbar

                    event.srcElement.value = '';
                    alert("Alle Stimmen bereits vergeben!");
                    return; 

                }
                
            },
            toggleCross(){

                // Listenkreuz anzeigen
                this.showCross = !this.showCross;

                if( this.showCross == true ){

                    for(let i=0; i<this.candidates; i++){

                        if(this.$refs.candidate[i].value == 2 || this.$refs.candidate[i].value == 3){
                            continue; // Kandidaten mit gehäufelten Stimmen nicht beachten
                        }

                        this.$refs.candidate[i].value = 1;
                        this.total--;
                        if(this.total == 0){return;}

                    }
                    
                }else{

                    // Stimmen wiederherstellen
                    this.candidateVote = '';   
                    this.total = this.candidates - this.accumulated;

                    for(let i=0; i<this.candidates; i++){
                        if(this.$refs.candidate[i].value == 2 || this.$refs.candidate[i].value == 3){
                            continue; // Kandidaten mit gehäufelten Stimmen nicht beachten 
                        }
                        this.$refs.candidate[i].value = '';
                    }
                }

            }


        }
        
    }

</script>

<style scoped lang="scss">

    .election{
        margin: 0 auto;
        width: 70%;
        background-color: white;
        display: table;
        border-collapse: collapse;

        .votefield{
            width: 40px;
            height: 40px;
            display: block;
            background-color: aliceblue;
            border-radius: 40px;
            margin: 0 auto;
            line-height: 50px;
            position: relative;
            top: -14px;
            border: none;
            text-align: center;
            font-size: 2em;

            &:active,
            &:focus{
                outline:none;
                border: none;
            }
        }

        .cross{
            font-size: 30px;
            font-weight: bold;
            color: blue;
            position: absolute;
            z-index: 1;
            top: -6px;
            left: 8px;
        }
        
        .row{
            border: 1px solid #000;
            display:table-row;
            height: 50px;
            width: 100%;
            position: relative;
            
            
        }

        .liner{
                display: table-cell;
                border: inherit;
                width: 80%;
                height: 50px;
            }

        .holder{
            display: table-cell;
            border: inherit;
            width: 10%;
            height: 50px;
        
            
            
        }
    }
    
</style>