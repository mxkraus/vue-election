<template>
  <div class='election-card'>

    <header></header>
      
    <div class="row listenkreuz">
        <div class="holder">

        </div>
        <div class="holder">
            <button class="votefield" @click='toggleCross'>
                <div id="toggleCross" class="cross" v-if="showCross" ref="cross">X</div>
            </button>
        </div>
        <div class="liner">
            <span>Sie haben noch {{ total }} Stimmen. <br> Gehäufelte Stimmen: {{ accumulated }}</span>
        </div>
    </div>

    <div class="row" v-for="(item, index) in this.candidates" v-bind:key="index">
        <div class="holder" >
            <span>00{{item}}</span>
        </div>
        <div class="holder">
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
                allCandidates: [],
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
            this.allCandidates = this.$refs.candidate;

            // Häufelung zurückesetzen wenn mehr S
            if(this.accumulated < 0){
                this.accumulated = 0;
            }

            /**
             * Alle Kandidaten sammlen, die irgend eine Stimme erhalten haben
             */
            this.candidatesWithVoting = [];
            this.allCandidates.forEach((element, index) => {
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

                let aV = parseInt(event.srcElement.value); // Eingegebener Wert
                let assignedVote = Number.isNaN(aV) ? '' : aV;

                if( assignedVote > 3 ){ // nur max. 3 Stimmen gültig
                    try {
                        // altes Voting wiederherstellen
                        event.srcElement.value = this.candidatesWithVoting[index].vote;
                    } catch(err) {
                        event.srcElement.value = '';
                    }
                    alert("Bitte nicht mehr als 3 Stimmen je Kandidat vergeben!")
                    return;
                }

                try{
                    // Altes Voting mit index aus Array lesen
                    let prevVoting = parseInt(this.candidatesWithVoting.find( c => c.idx == index ).vote);
                    let diff = 0;

                    if( assignedVote == '' ){
                        this.total += prevVoting;
                        this.accumulated -= prevVoting;
                        return;
                    }else if( assignedVote < prevVoting ){
                        diff = prevVoting - assignedVote;
                        this.total += diff;
                        this.accumulated -= assignedVote == 1 ? prevVoting : diff;
                        return;
                    }else if( assignedVote > prevVoting ){
                        diff = assignedVote - prevVoting;
                        // Nur abziehen wenn eh nicht schon alle Stimmen weg sind
                        this.total -= this.showCross == false ? diff : 0; 
                        this.accumulated += prevVoting == 1 ? assignedVote : diff;

                        if(this.showCross == true){

                            this.candidatesWithAccumulation = [];
                            this.allCandidates.forEach((element, index) => {
                                if(element.value > 1){
                                    this.candidatesWithAccumulation.push(index);
                                }
                            });
                        
                            let until = this.accumulated - this.candidatesWithAccumulation.length;
                            for(let i=this.candidates; i>this.candidates - until; i--){
                                this.$refs.candidate[i-1].value = '';
                            }
                        }

                        return;
                    }

                }catch(err){
                    console.log(err);
                }


                if( this.total > 0 ){ 

                    // Stimmen wurden gehäufelt
                    if( assignedVote > 1 && assignedVote <= 3 ){
                        this.accumulated += parseInt(assignedVote);
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

                    if(this.total == 0){
                        return;
                    }

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

    .election-card{
        margin: 0 auto;
        width: 70%;
        background-color: white;
        border-collapse: collapse;
        box-shadow: 2px 2px 10px #aaa;

        header{
            content: '';
            height: 20px;
            width: 100%;
            display: block;
            background-color: #2a7fb7;
        }

        @media only screen and (max-width: 768px) {
            width: 100%;
        }

        .listenkreuz{
            .liner{
                height: 80px;
                span{
                    position: relative;
                    bottom: -16px;
                }
            }
        }

        .votefield{
            width: 40px;
            height: 40px;
            display: block;
            background-color: #fff;
            border: 1px solid #2a7fb7;
            border-radius: 40px;
            margin: 0 auto;
            line-height: 50px;
            position: relative;
            text-align: center;
            font-size: 20px;
            margin: 5px;

            &:active,
            &:focus{
                outline:none;
                border: 1px solid #2a7fb7;
            }
        }

        .cross{
            font-size: 30px;
            font-weight: bold;
            color: #2a7fb7;
            position: relative;
            top: -2px;
            left: 1px;
        }
        
        .row{
            border: 1px solid #2a7fb7;
            display:table-row;
            height: 50px;
            width: 100%;
            position: relative;
        }

        .liner{
            display: table-cell;
            border: inherit;
            height: 50px;
        }

        .holder{
            display: table-cell;
            //border: inherit;
            width: 10%;
            height: 50px;
            vertical-align: middle;
        }
    }
    
</style>