<template>

    <div id="submenu">
        <ul>
            <li>
                <button value="" class="voteButton remove" @click="$emit('choose-vote', '')">-</button>
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
    margin-left: 50%;
    left: -100px;
    ul{
        padding: 0;
        width: 160px;
        display: flex;
        justify-content: space-between;
        li{
            list-style:none;
            .voteButton{
                font-size: 20px;
                width: 45px;
                height: 45px;
                border-radius: 40px;
                border: 2px solid #0054d3;
                background-color: #fff;
                cursor: pointer;
                outline: none;
                &.remove{
                    border-color: #d35400;
                }
            }
        }
    }
}
</style>