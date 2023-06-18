<template>
    <div id="ndx-container">
        <div id="search-wrap">
            <search></search>
        </div>
        <div id="map-warp">
            <MapContainer></MapContainer>
        </div>
        <div id="heatMap-wrap">
            <heatMapSearchBox></heatMapSearchBox>
        </div>
    </div>
</template>

<script>
import MapContainer from '@/components/MapContainer/MapContainer.vue'
import search from '@/components/Search/search.vue'
import heatMapSearchBox from '@/components/heatMapSearchBox/heatMapSearchBox.vue'

export default {
    components: { MapContainer, search, heatMapSearchBox }
}
</script>

<style lang="less" scoped>
#ndx-container {
    width: 100%;
    height: 100%;
    position: relative;

    #map-warp {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        height: 60%;
        width: 60%;
    }

    #search-wrap {
        position: absolute;
        top: 10%;
        left: 40%;
        height: 60px;
        width: 40px;
        transform: translate(-50%, -50%);
    }

    #heatMap-wrap {
        z-index: 99;
        position: absolute;
        top: 15%;
        left: 70%;
        transform: translate(-50%, -50%);
    }
}
</style>