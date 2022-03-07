<template>
  <div class="scale-wrap" :style="scaleWrapStyle">
    <slot></slot>
  </div>
</template>
<script>
//这块宽高比例根据效果图来设置
const DesignWidth = 375
const DesignHeight = 667
const DesignRatio = DesignWidth / DesignHeight

function getScale() {
  let width = document.documentElement.clientWidth,
    height = document.documentElement.clientHeight
  if (
    window.orientation == null ||
    window.orientation === 180 ||
    window.orientation === 0
  ) {
    //竖屏状态
  } else {
    [width, height] = [height, width]
  }
  let ratio = width / height
  let scale
  //通过计算实际手机宽高比和效果图的宽高比得出最终的所让比例
  if (ratio > DesignRatio) {
    scale = height / (width / DesignRatio)
  } else if (ratio < DesignRatio) {
    scale = width / (height * DesignRatio)
  } else {
    scale = 1
  }
  return scale
}

export default {
  data() {
    return {}
  },
  computed: {
    scaleWrapStyle() {
      let scale = getScale()
      let scaleStr = `scale(${scale})`
      return {
        transform: scaleStr,
        '-webkit-transform': scaleStr,
      }
    },
  },
}
</script> 
<style lang="less" scoped>
.scale-wrap {
  width: 100%;
  height: 100%;
  display: flex;
  transform-origin: left center;
}
</style> 