<template>
    <div id="search-contan">
        <input type="text" placeholder="请输入查询地点" v-model="inputObj.userInput" :id="inputObj.InputId">
        <!-- <button id="searchBtn" @click="send">search</button> -->
        <el-button type="primay" id="searchBtn" @click="send">查询</el-button>
    </div>
</template>

<script >
import EventBus from '@/EventBus/EventBus';
export default {
    data() {
        return {
            inputObj: {
                userInput: '',
                InputId: 'searchInput'
            }
        }
    },
    methods: {
        send() {
            EventBus.$emit("sharUserInput", this.inputObj)
        }
    },
    mounted() {
        this.send()
    }
}
</script>

<style lang="less" scoped>
#search-contan {
    background-color: aliceblue;
    border-radius: 10px;
    background-color: rgba(255, 255, 255, 0.3);
    display: flex;
    justify-content: space-between;
    justify-items: center;

    input {
        width: 400px;
        height: 40px;
        padding: 0 30px;
    }

    #searchBtn {
        // height: 40px;
        margin-left: 5px;
        // width: 70px;
    }
}
</style>