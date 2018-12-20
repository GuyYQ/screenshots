<template>
  <div class="capture" :class="{ capturing }" @click="capture">
    <slot>
      <a href="javascript: void 0;">点击截屏</a>
    </slot>
  </div>
</template>

<script>
import html2canvas from "html2canvas";

const BgCanvasStyleMap = {
  position: "absolute",
  top: "0",
  right: "0",
  bottom: "0",
  left: "0",
  zIndex: "",
  overflow: "hidden"
}

const RangeBoxStyleMap = {
  position: "absolute",
  border: "1px dashed red",
  width: "0",
  height: "0",
  left: "0",
  top: "0",
  overflow: "hidden"
}

const decorateElementWithStyleMap = ($el, styleMap) => {
  const isCanvas = $el instanceof HTMLCanvasElement;
  Object.keys(styleMap).forEach((prop) => {
    if (isCanvas && (prop === 'width' || prop === 'height')) {
      $el[prop] = styleMap[prop];
      return;
    }
    $el.style[prop] = styleMap[prop];
  });
}

// 移除有父节点的 $el，其实也可以直接 $el.remove
const removeDOM = ($el) => {
  $el.parentElement.removeChild($el);
}

export default {
  name: "ScreenCapture",
  data() {
    return {
      capturing: false
    };
  },
  props: {
    zIndex: {
      type: Number,
      default: 100000000
    },
    rangeDOM: {
      validator (x) {
        return x instanceof HTMLElement;
      },
      default () {
        // 还没考虑其它的 wrapper 会有什么问题
        // 不过注意，如果是 body，需要考虑 scroll，暂时不考虑
        return document.querySelector("body");
      }
    },
    boxClassName: {
      type: String,
      default: 'capture-selecting-box'
    }
  },
  methods: {
    decorateBgCanvas ($canvas) {
      const styleMap = Object.assign({ }, BgCanvasStyleMap, { zIndex: this.zIndex });
      decorateElementWithStyleMap($canvas, styleMap);
    },

    decorateCaptureRangeBox ($box) {
      const styleMap = Object.assign({ }, RangeBoxStyleMap, { zIndex: this.zIndex + 1 });
      decorateElementWithStyleMap($box, styleMap);
    },

    // udpate width/height/marginLeft 之类
    updateCaptureRangeBox ($box, styleMap) {
      decorateElementWithStyleMap($box, styleMap);
    },

    getImageByCanvas ($canvas) {
      const $img = document.createElement('img');
      $img.src = $canvas.toDataURL();
      $img.width = $canvas.width;
      $img.height = $canvas.height;

      return $img;
    },

    capture () {
      this.capturing = true;
      let $bgCanvas = null;
      let isSelectingArea = false;
      let boxRange = {
        marginLeft: 0,
        marginTop: 0,
        startX: 0,
        startY: 0,
        width: 0,
        height: 0
      };
      let $box = null;

      const onMousedown = (evt) => {
        if (!isSelectingArea) {
          isSelectingArea = true;
          // 注意这里的 scrollTop 应该是哪个元素的 scrollTop，比入说可能为：`document.documentElement.scrollTop`
          // scrollLeft 也是如此
          const scrollTop = this.rangeDOM.scrollTop;
          const scrollLeft = this.rangeDOM.scrollLeft;
          boxRange.marginLeft = boxRange.startX = evt.clientX + scrollLeft;
          boxRange.marginTop = boxRange.startY = evt.clientY + scrollTop;
          $box = document.createElement("div");
          if (this.boxClassName) {
            $box.classList.add(this.boxClassName);
          }
          this.decorateCaptureRangeBox($box)
          this.updateCaptureRangeBox($box, {
            marginLeft: boxRange.marginLeft + "px",
            marginTop: boxRange.marginTop + "px"
          });
          this.rangeDOM.appendChild($box);
        }
      }

      const onMousemove = (evt) => {
        if (!isSelectingArea) {
          return;
        }
        const scrollTop = this.rangeDOM.scrollTop;
        const scrollLeft = this.rangeDOM.scrollLeft;
        boxRange.marginLeft = (boxRange.startX - evt.clientX - scrollLeft > 0 ? (evt.clientX + scrollLeft) : boxRange.startX) + "px";
        boxRange.marginTop = (boxRange.startY - evt.clientY - scrollTop > 0 ? (evt.clientY + scrollTop) : boxRange.startY) + "px";
        boxRange.width = Math.abs(boxRange.startX - evt.clientX - scrollLeft) + "px";
        boxRange.height = Math.abs(boxRange.startY - evt.clientY - scrollTop) + "px";
        this.updateCaptureRangeBox($box, boxRange);
      }

      const onMouseup = (evt) => {
        if (!isSelectingArea) {
          return;
        }
        isSelectingArea = false;
        const boxWidth = parseInt(boxRange.width, 10);
        const boxHeight = parseInt(boxRange.height, 10);
        const selected = boxWidth !== 0 || boxHeight !== 0
        if (selected) {
          this.capturing = false;
          const $canvas = document.createElement("canvas");
          const canvasCtx = $canvas.getContext("2d");
          $canvas.width = parseInt(boxRange.width, 10);
          $canvas.height = parseInt(boxRange.height, 10);
          $canvas.style.background = "#fff";
          // 暂时不考虑异常，注意需要 onload
          const $img = this.getImageByCanvas($bgCanvas);
          $img.onload = () => {
            canvasCtx.drawImage(
              $img,
              parseInt(boxRange.marginLeft, 10), parseInt(boxRange.marginTop, 10),
              $canvas.width, $canvas.height,
              0, 0,
              $canvas.width, $canvas.height
            );
            this.$emit('captured', { error: null, imageData: $canvas.toDataURL() });
            $canvas.width = $canvas.height = 0;
            $canvas.remove();
          };
          dispose();
          return;
        }
        // 没有选择的话，就移除已有的 $box，且支持继续截屏，不过之后需要考虑取消截屏的状态
        removeDOM($box);
        $box = null;
      }

      const evtTypesWithHandler = [
        ["mousedown", onMousedown],
        ["mousemove", onMousemove],
        ["mouseup", onMouseup]
      ];

      const initBoxRelHandlers = () => {
        evtTypesWithHandler.forEach(([type, handler]) => {
          document.addEventListener(type, handler);
        });
      }

      const dispose = () => {
        if ($bgCanvas) {
          $bgCanvas.width = 0;
          $bgCanvas.height = 0;;
          removeDOM($bgCanvas);
          $bgCanvas = null;
        }

        if ($box) {
          removeDOM($box);
          $box = null;
        }

        evtTypesWithHandler.forEach(([type, handler]) => {
          document.removeEventListener(type, handler);
        })
      }

      html2canvas(this.rangeDOM, { useCORS: true }).then((canvas) => {
        $bgCanvas = canvas;
        this.decorateBgCanvas($bgCanvas);
        this.rangeDOM.appendChild($bgCanvas);

        initBoxRelHandlers();
      }).catch((err) => {
        this.$emit('captured', { error: err })
      });
    }
  }
};
</script>

<style>
  .capture {
    display: inline-block;
  }
</style>
