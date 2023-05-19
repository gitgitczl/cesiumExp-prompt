<template>
    <div id="mapContainer"></div>
    <div class="toolbar">
        <a-button class="toolbar-btn" type="primary" @click="local">定点提示框</a-button>
        <a-button class="toolbar-btn" type="primary" @click="move">移动提示框</a-button>
    </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import Prompt from "./js/prompt/prompt.js"
import util from "./js/util"
let viewer = undefined;
let mapContainer = ref()
onMounted(() => {
    viewer = new Cesium.Viewer('mapContainer', {
        imageryProvider: new Cesium.ArcGisMapServerImageryProvider({
            url: "https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer"
        }),
        terrainProvider: new Cesium.CesiumTerrainProvider({
            url: "http://data.marsgis.cn/terrain"
        }),
        animation: false,  // 设置动画小组件关闭展示
        timeline: false, // 时间轴关闭展示
        infoBox: false, // 信息盒子关闭展示
        geocoder: false, // 地理编码搜索关闭展示
        baseLayerPicker: false, // 基础图层选择器关闭展示
        sceneModePicker: false, // 场景选择器关闭展示
        fullscreenButton: false, // 全屏按钮关闭展示
        navigationInstructionsInitiallyVisible: false, // 导航说明是否最初展示
        navigationHelpButton: false, // 导航帮助按钮关闭展示
        homeButton: false
    });
    document.getElementsByClassName("cesium-viewer-bottom")[0].style.display = "none";
    util.setCameraView({
        "x": 116.98180343031277,
        "y": 31.65625287717623,
        "z": 55533.08874146516,
        "heading": 359.31730998394744,
        "pitch": -55.72609786048885,
        "roll": 359.97907544797624,
        "duration": 0
    }, viewer)
})

let prompt1 = undefined;
const local = () => {
    util.setCameraView({
        "x": 116.98180343031277,
        "y": 31.65625287717623,
        "z": 55533.08874146516,
        "heading": 359.31730998394744,
        "pitch": -55.72609786048885,
        "roll": 359.97907544797624,
        "duration": 0
    }, viewer)
    if (prompt1) {
        prompt1.destroy();
        prompt1 = undefined;
    }
    // 1、固定点位提示框
    prompt1 = new Prompt(viewer, {
        type: 2,
        content: "我是定点提示框",
        position: [117, 32, 100], // 支持多种形式传参 cartesian3 || array || object
        close: function () {
            alert("easy3d--三维可视化类库！");
            return false
        } // 点击关闭按钮的回调函数
    });
}

let movePrompt = undefined;
let moveListener = undefined;
const move = () => {
    if (movePrompt) {
        movePrompt.destroy();
        movePrompt = undefined;
    }

    movePrompt = new Prompt(viewer, {
        type: 1,
        content: "我是移动提示框"
    })

    if (moveListener) {
        document.removeEventListener("mousemove", moveListener);
        moveListener = undefined;
    }

    moveListener = document.addEventListener("mousemove", function (evt) {
        if (!movePrompt) return;
        movePrompt.update({
            x: evt.clientX,
            y: evt.clientY
        })
    })
}


</script>

<style scoped>
.toolbar {
    position: absolute;
    top: 20px;
    left: 20px;
    z-index: 999;
}

.toolbar-btn {
    margin: 10px;
}

#mapContainer{
    margin: 0;
    padding: 0;
    height: 100%;
}
</style>

