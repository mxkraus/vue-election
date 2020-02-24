<template>
    <footer>
      <ul>
        <li><a href="https://www.csu-stoernstein.de" target="_blank">www.csu-stoernstein.de</a></li>
        <li><a href="mailto:info@csu-stoernstein.de">info@csu-stoernstein.de</a></li>
      </ul>
    </footer>
</template>

<script>
    export default {
        data(){
            return {

            }
        }
    }
</script>

<style scoped lang="scss">

    footer{
        padding-top: 20px;
        ul{
            margin: 0;
            padding: 0;
            height: 100%;
            display: flex;
            flex-direction: column; 
            align-content: center;;

            li{
                list-style: none;
                margin-bottom: 10px;
            }
        }
    }

</style>