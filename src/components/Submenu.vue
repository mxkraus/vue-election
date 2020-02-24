<template>

    <div id="submenu">
        <ul>
            <li>
                <button value="" class="voteButton remove" @click="$emit('choose-vote', '')">
                    <span>+</span>
                </button>
            </li>
            <li>
                <button value="1" class="voteButton" @click="$emit('choose-vote', 1)">1</button>
            </li>
            <li>
                <button value="2" class="voteButton" @click="$emit('choose-vote', 2)">2</button>
            </li>
            <li>
                <button value="3" class="voteButton" @click="$emit('choose-vote', 3)">3</button>
            </li>
            
        </ul>  
    </div>
    
</template>
<script>


export default {
    deta(){
        return {
            
        }
    },
    methods: {

    }
}
</script>

<style scoped lang="scss">

#submenu{
    position: fixed;
    bottom: 0;
    margin-top: 10%;
    top: 0px;
    right: 0;
    @media only screen and (max-width: 768px) {
        margin-top: 60%;
    }
    ul{
        padding: 0;
        width: 160px;
        display: flex;
        justify-content: space-between;
        flex-direction: column;
        height: 350px;
        li{
            list-style:none;
            .voteButton{
                font-size: 3rem;
                width: 75px;
                height: 75px;
                -webkit-border-radius: 500px;
                -moz-border-radius: 500px;      
                border-radius: 40px;
                border: 2px solid #0054d3;
                background-color: #324b72;
                color: #fff;
                cursor: pointer;
                outline: none;
                &.remove{
                    border-color: #b00c0c;
                    background-color: #fff;
                    color: #b00c0c;
                    border-width: 8px;
                    position: relative;
                    & span{
                        transform: rotate(45deg);
                        position: absolute;
                        font-size: 1.5em;
                        font-weight: bold;
                        left: 11px;
                        top: -16px;
                    }
                }
            }
        }
    }
}
</style>