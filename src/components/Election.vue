<template>

  <div class='election-card'>

    <header id="tableHeader" class="header voting">
        <p>Gesamtanzahl Kandidaten: {{ candidates }}</p>
        <p>Sie haben noch {{ total }} Stimmen.</p>
        <p>Gehäufelte Stimmen: {{ accumulated }}</p>
        <!-- <div class="subtitle">
            <h3>Wahlvorschlag 01</h3>
        </div> -->
    </header>
      
    <div class="row listenkreuz">
        <div class="holder">

        </div>
        <div class="holder">
            <button class="votefield" @click='toggleCross'>
                <div id="toggleCross" class="cross" v-if="showCross" ref="cross">X</div>
            </button>
        </div>
        <div class="liner">
            <!-- <h2><small>Kennwort</small><br/>CSU - Christlich Soziale Union</h2> -->
        </div>
    </div>

    <div class="row" v-for="(item, index) in this.candidates" v-bind:key="index">
        <div class="holder" >
            <span>{{fillItem(item)}}</span>
        </div>
        <div class="holder">
            <input 
                type="text" 
                maxlength="1"
                ref="candidate" 
                class="votefield" 
                :value="chosenVote" 
                @click="triggerSubMenu(index, $event)" 
                @change="handleVoting(index, $event)"
            />
        </div>
        <div class="liner">
            <!-- <span>
                <strong>
                    {{candData[index].name}} 
                    {{candData[index].surname}},
                </strong> 
                {{candData[index].age}} Jahre <br/> 
                {{candData[index].job}}
            </span> -->
        </div>
    </div>

        <!-- v-on:choose-vote="chosenVote = $event"  -->

    <submenu
        v-if="showSubMenu == true"
        v-on:choose-vote="handleChild"
    ></submenu>

  </div>

</template>

<script>

    import Submenu from "./Submenu.vue";

    export default {
        components: {
            Submenu
        },
        props: ['candata'],
        data() {
            return {
                total: 24,
                candidates: this.candata.length,
                candData: this.candata,
                accumulated: 0,
                previousVote: 0,
                showCross: false,
                allCandidates: [],
                candidatesWithAccumulation: [],
                candidatesWithVoting: [],
                showSubMenu: false,
                chosenVote: null,
                clickedIndex: 0
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
            handleChild( value ){
                this.$refs.candidate[this.clickedIndex].value = value;
            },
            triggerSubMenu(index){
                this.clickedIndex = index;
                this.showSubMenu = !this.showSubMenu;
            },
            fillItem(item){
                switch (item.toString().length) {
                    case 1: item = '10' + item; break;
                    case 2: item = '1' + item; break;
                    default: break;
                }
                return item;
            },
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

                if( this.total - assignedVote < 0 ){
                    try {
                        // altes Voting wiederherstellen
                        event.srcElement.value = this.candidatesWithVoting[index].vote;
                    } catch(err) {
                        event.srcElement.value = '';
                    }
                    alert("Nicht ausreichend Stimmen vorhanden!");
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
                    // console.log(err);
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

                    let i = 0;
                    let round = 0;
                    while( this.total > 0 ){

                        let currentValue = this.$refs.candidate[i].value;
                        currentValue = currentValue == '' ? 0 : parseInt(currentValue);

                        if(currentValue == 2 || currentValue == 3){
                            i++;
                            continue; // Kandidaten mit gehäufelten Stimmen nicht beachten
                        }

                        if( round == 2 ){
                            this.$refs.candidate[i].value = currentValue + 1;
                        }else{
                            if(currentValue == 1){
                                // Kandidat hatte vorher scohn 1 Stimme, soll also durch das Listenkreuz nich tnoch mehr behkommen
                                i++;
                                continue;
                            }
                            this.$refs.candidate[i].value += 1;
                        }

                        i++;
                        this.total--; 

                        if( i == this.candidates ){
                            i = 0; 
                            round = 2;
                        }

                    }
                    
                }else{

                    // Stimmen wiederherstellen
                    this.total = 24;
                    this.accumulated = 0;

                    for(let i=0; i<this.candidates; i++){
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
        width: 600px;
        background-color: white;
        border-collapse: collapse;
        box-shadow: 2px 2px 10px #aaa;
        display: table;
        margin-bottom: 10%;

        .header{
            p{margin: 0;}
            padding: 10px 20px;
            font-weight: bold;
            font-size: 1.3em;
            display: table-caption;
            background-color: #fff;
            text-align: left;
            border: 1px solid black;
            border-top: none;
            border-bottom: none;
            &.voting{
                background-color: #2a7fb7;
                color: #fff;
                text-align: center;
            }
            h3{
                margin: 0;
                height: 30px;
            }
            &.fixed{
                position: fixed;
                top: 0;
                z-index: 9;
                width: 100%;
                left: 0;
                border: none;
                font-size: 16px;
                padding: 5px 0;
            }
        }

        @media only screen and (max-width: 768px) {
            width: 100%;
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
            cursor: pointer;

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
            vertical-align: middle;
            text-align: left;
            padding-left: 1rem;
            border: inherit;
            height: 50px;
                line-height: 22px;
        }

        .listenkreuz .liner {
            h2{
                line-height: 30px;
            }   
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