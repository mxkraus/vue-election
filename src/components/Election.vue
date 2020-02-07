<template>
  <div class='election'>
      
    <div class="listenkreuz">
        <div class="holder">
            <button class="votefield" @click.once='handleListCross'>
                <div class="cross" ref="cross"></div>
            </button>
        </div>
        <div class="liner">
            <div>
                Sie haben {{ votes }} Stimmen!
            </div>
        </div>
    </div>

    <div class="row" v-for="(item, index) in this.candidates" v-bind:key="index">
        <div class="holder">
            <div class="nr" >
                <p>00{{item}}</p>
            </div>
            <input type="text" class="votefield" @change="handleVoting" :value="currentVote" /> <!--v-model="currentVote"-->
        </div>
        <div class="liner"></div>
    </div>
      
  </div>
</template>

<script>

    export default {
        data() {
            return {
                votes: 24,
                candidates: 24,
                cross: "",
                currentVote: ""
            }
        },
        methods: {
            handleListCross(event){
                event.target.childNodes[0].innerText = 'X';
                this.cross = true;
                // console.log(this.votes);
                const voteDist = this.candidates / this.votes;
                this.currentVote = voteDist;

            },
            handleVoting(event){
                let assigned = event.srcElement.value;
                this.votes = this.votes - assigned;
                // console.log(this.votes); 
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