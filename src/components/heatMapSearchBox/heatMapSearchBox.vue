<template>
    <div id="box">
        <el-checkbox v-model="checked" @change="send">热力图显示</el-checkbox>
    </div>
</template>

<script >
import EventBus from '@/EventBus/EventBus';
export default {
    data() {
        return {
            checked: false
        }
    },
    methods: {
        send() {
            EventBus.$emint('shareHeatMapShow',this.checked)
        }
    }
}
</script>

<style lang="less" scoped>
#box {
    width: 100%;
    height: 100%;
    position: relative;
}
</style>