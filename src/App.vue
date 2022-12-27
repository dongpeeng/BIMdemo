<template>
  <div class="scene-view" ref="canvassceneBox">
    <div id="canvas-container" class="scene-container" v-show="canvasVisible">
      <canvas
        id="canvas"
        ref="canvasRef"
        class="canvas-box"
        oncontextmenu="event.preventDefault()"
        tabindex="1"
      ></canvas>
    </div>
    <el-progress
      type="circle"
      :percentage="percentage"
      v-show="showpercentage"
    ></el-progress>
    <div id="Div" class="info" v-if="showInfo">
      <div>name:{{ itemInfo.m_strSelActorName }}</div>
      <div>id:{{ itemInfo.m_uSelActorSubID_L32 }}</div>
    </div>
    <div id="Div" class="info" v-if="showProjectInfo">
      <div>2222</div>
    </div>
    <!-- <div id="Div" class="info">
      <select id="select" >
        <option selected hdden disabled value="-1">please choose</option>
        <option value="0">1</option>
        <option value="1">2</option>
        <option value="2">3</option>
      </select>
    </div> -->
    <div>
    </div>
  </div>
</template>

<script>
//import axios from "axios";
import el from "element";
export default {
  data() {
    return {
      percentage: 0, // 进度条数值
      showpercentage: true, // 是否显示进度条
      canvasVisible: false,
      pos: "",
      flag: false,
      select: "",
      index: "",
      visible: false,
      showInfo: false,
      showProjectInfo: false,
      itemInfo: {},
    };
  },

  mounted() {
    let existFlag = this.scriptIsExist();

    if (!existFlag) {
      window.BlackHole3D = window.BlackHole3D ? window.BlackHole3D : {};
      window.BlackHole3D["canvas"] = this.$refs.canvasRef;

      if (typeof window.RE2SDKCreateModule != "undefined") {
        window.BlackHole3D = window.RE2SDKCreateModule(window.BlackHole3D);
      } else {
        document.addEventListener(
          "RealEngineToBeReady",
          this.RealEngineToBeReady
        );
      }
      this.createScript("./js/ReUtility.js", "ReUtility");
      this.createScript("./js/RealBIMWeb.js", "RealBIMWeb");
    }

    this.addEventListener();
  },
  beforeDestroy() {
    this.removeEventListener();
  },
  methods: {
    scriptIsExist() {
      let dom = document.getElementsByTagName("script");
      let existFlag = false;
      for (let i = 0; i < dom.length; i++) {
        if (dom[i].src.includes("RealBIMWeb.js")) {
          existFlag = true;
          break;
        } else {
          existFlag = false;
        }
      }
      return existFlag;
    },
    createScript(src, id) {
      const script = document.createElement("script");
      script.setAttribute("async", "async");
      script.src = src;
      script.setAttribute("id", id);
      const body = document.querySelector("body");
      body.append(script);
    },
    RealEngineToBeReady() {
      // if(!window.RE2SDKCreateModule) ;

      setTimeout(() => {
        window.BlackHole3D = window.RE2SDKCreateModule(window.BlackHole3D);
      }, 500);
      // window.BlackHole3D.REreleaseEngine();
      // if (typeof GLctx != "undefined") {
      //   if (GLctx.getExtension("WEBGL_lose_context") != null) {
      //     GLctx.getExtension("WEBGL_lose_context").loseContext();
      //   }
      // }
      console.log(window.BlackHole3D, "引擎模块");
    },
    addEventListener() {
      document.addEventListener("RealEngineReady", this.RealBIMInitSys);
      document.addEventListener("RealBIMInitSys", this.RealBIMLoadMainSce);
      document.addEventListener("RealBIMLoadMainSce", this.MainSceDown);
      document.addEventListener("RealEngineRenderReady", this.showCanvas);
      document.addEventListener("RealBIMLoadProgress", this.LoadProgress);
      window.addEventListener("onbeforeunload", this.onBeforeUnload);
      window.addEventListener("resize", this.onResize);
      document.addEventListener("RealBIMSelModel", this.GetCurProbeRet);
      document.addEventListener("RealBIMSelShape", this.REgetCurShpProbeRet);
    },
    removeEventListener() {
      document.removeEventListener("RealEngineReady", this.RealBIMInitSys);
      document.removeEventListener("RealBIMLoadProgress", this.LoadProgress);
      document.removeEventListener("RealBIMLoadMainSce", this.MainSceDown);
      document.removeEventListener("RealBIMInitSys", this.RealBIMLoadMainSce);
      document.removeEventListener("RealEngineRenderReady", this.showCanvas);
      document.removeEventListener(
        "RealEngineVisibleSwitch",

        this.RealEngineVisibleSwitch
      );
      document.removeEventListener("RealBIMSelModel", this.GetCurProbeRet);
      document.removeEventListener("RealBIMSelShape", this.REgetCurShpProbeRet);
    },
    RealBIMInitSys() {
      var workerjspath = "./js/RealBIMWeb_Worker.js";
      var width = this.$refs.canvassceneBox.clientWidth;
      var height = this.$refs.canvassceneBox.clientHeight;
      var commonurl =
        "https://demo.bjblackhole.com/default.aspx?dir=url_res03&path=res_gol001";
      var username = "admin";
      // var password = "xiyangyang";
      var password = "admin";
      window.BlackHole3D.REinitSys(
        workerjspath,
        width,
        height,
        commonurl,
        username,
        password
      );
    },
    RealBIMLoadMainSce() {
      var projInfo = [
        // {
        //   projName: "pro01",
        //   urlRes:
        //     "https://isuse.bjblackhole.com/default.aspx?dir=blackhole_res12&path=",
        //   projResName: "a0f17cbd76544a659f2ae11c12052bca",
        //   useNewVer: true,
        //   verInfo: 0,
        //   useTransInfo: false,
        //   transInfo: "",
        // },
        {
          projName: "31167a812d6d4597a94a92a1bb57d926",
          urlRes:
            "https://isuse.bjblackhole.com/default.aspx?dir=blackhole_res12&path=",
          projResName: "31167a812d6d4597a94a92a1bb57d926",
          useNewVer: true,
          verInfo: 0,
          useTransInfo: true,
          transInfo: [
            [1.155843, 1.155843, 1.155843],
            [0.0, 0.0, 0.0, 1.0],
            [12845353.537529, -333890.993892, 0.0],
          ],
        },
        {
          projName: "3929303bfe28491d8d847e7dba7d2ae0",
          urlRes:
            "https://isuse.bjblackhole.com/default.aspx?dir=blackhole_res13&path=",
          projResName: "3929303bfe28491d8d847e7dba7d2ae0",
          useNewVer: true,
          verInfo: 0,
          useTransInfo: true,
          transInfo: [
            [1.155844, 1.155844, 1.155844],
            [0.0, 0.0, 0.0, 1.0],
            [12845353.206713, -333893.194837, 0.0],
          ],
        },
        {
          projName: "34cb5baa4e4b43abab3dd5b3f9a6735d",
          urlRes:
            "https://isuse.bjblackhole.com/default.aspx?dir=blackhole_res15&path=",
          projResName: "34cb5baa4e4b43abab3dd5b3f9a6735d",
          useNewVer: true,
          verInfo: 0,
          useTransInfo: false,
        },
      ];
      window.BlackHole3D.REloadMainSce_projs(projInfo, true);
      window.BlackHole3D.REsetMaxResMemMB(5500);
      window.BlackHole3D.REsetExpectMaxInstMemMB(4500);
      window.BlackHole3D.REsetExpectMaxInstDrawFaceNum(50000000);
      window.BlackHole3D.REsetPageLoadLev(2);
      this.canvasVisible = true;
      this.showpercentage = false;
    },
    //场景模型加载完成，此时可浏览完整模型，所有和模型相关的操作只能在场景加载完成后执行
    MainSceDown() {
      this.visible = true;
      console.log("1");
      window.BlackHole3D.RElocateCamTo(
        [13436105.495034024, 3524017.5830469187, 2752.8006660619144],
        [
          0.366642311391329, 0.11438061792517402, 0.27497119384517227,
          0.8814087203426323,
        ],
        0.0,
        0
      );

      window.BlackHole3D.canvas.focus();
    },
    LoadProgress(e) {
      this.percentage = e.detail.progress;
      if (e.detail.progress === 100) {
        this.canvasVisible = true;
        this.showpercentage = false;
        console.log("2");
        this.showProjectInfo = true;
      }
    },
    onResize() {
      window.BlackHole3D.m_re_em_window_width = window.innerWidth;
      window.BlackHole3D.m_re_em_window_height = window.innerHeight;
    },
    GetCurProbeRet() {
      this.showProjectInfo = false;
      // console.log(e.detail.button); //button值为0表示左键单击事件；button值为1表示右键单击事件
      var proberet = window.BlackHole3D.REgetCurProbeRet();
      this.itemInfo = proberet;
      this.showInfo = true;
      console.log(proberet); //获取当前选中点相关参数
    },
    REgetCurShpProbeRet() {
      this.showInfo = false;
      var REgetCurCombProbeRet = window.BlackHole3D.REgetCurCombProbeRet();
      if (
        REgetCurCombProbeRet.m_strProjName == "" &&
        REgetCurCombProbeRet.m_strType == ""
      ) {
        this.showProjectInfo = true;
        this.showInfo = false;
      } else {
        this.showInfo = true;
      }
      console.log(REgetCurCombProbeRet);
      var shpproberet_norm = window.BlackHole3D.REgetCurShpProbeRet();
      //获取当前拾取到的矢量(锚点、标签)相关信息
      console.log(shpproberet_norm);
    },
    showCanvas() {
      this.canvasVisible = true;
      //window.BlackHole3D.canvas.focus();
    },
    RealEngineVisibleSwitch(e) {
      console.log(e.detail.visible, "弹窗");
    },
    onBeforeUnload() {
      this.releaseEngine();
    },
    releaseEngine() {
      window.BlackHole3D.REreleaseEngine();
      if (typeof window.BlackHole3D.GLctx != "undefined") {
        if (
          window.BlackHole3D.GLctx.getExtension("WEBGL_lose_context") != null
        ) {
          window.BlackHole3D.GLctx.getExtension(
            "WEBGL_lose_context"
          ).loseContext();
        }
      }
    },
  },
};
</script>

<style lang="less">
label {
  display: block;
  margin: 10px;
}

.flicker label img {
  vertical-align: middle;
  width: 15px;
  height: 15px;
}
.info {
  position: absolute;
  top: 50%;
  right: 20px;
  transform: translateY(-50%);
  width: 400px;
  padding: 20px 30px;
  background-color: rgba(0, 0, 0, 0.3);
  color: #fff;
  border-radius: 5px;
  overflow: hidden;
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
body {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

ul {
  list-style: none;
}

a {
  color: #ffffff;
  text-decoration: none;
}
a:hover {
  color: #2badfb;
}

#app {
  // font-family: Arial, "PingFang SC", "Segoe UI", "Microsoft Yahei",
  //   "Microsoft Jhenghei", "Hiragino Sans GB", Helvetica, Arial, sans-serif,
  //   "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-family: "PingFang SC";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100%;
  height: 100%;
  text-align: center;
  color: #fff;
}

.scene-view {
  height: 100%;

  .scene-container {
    width: 100%;
    height: 100%;

    .canvas-box {
      width: 100%;
      height: 100%;
    }
  }

  .el-progress {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translateY(-50%);
    transform: translateX(-50%);
  }
}

// 修改全局滚动条样式
::-webkit-scrollbar {
  width: 7px; /*对垂直流动条有效*/
  height: 10px; /*对水平流动条有效*/
}
/*定义滚动条的轨道颜色、内阴影及圆角*/
::-webkit-scrollbar-track {
  background-color: #f9f9f9;
  border-radius: 3px;
}
/*定义滑块颜色、内阴影及圆角*/
::-webkit-scrollbar-thumb {
  border-radius: 7px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  background-color: #e6e6e6;
}

/*定义两端按钮的样式*/
::-webkit-scrollbar-button {
  display: none;
}
</style>