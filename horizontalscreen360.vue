<template>
  <div class="page-showDetail-container">
    <div class="page-showDetail-main">
      <div class="actionFace-container">
        <div class="action-mask">
          <div class="select-action-app" @click="openApp" v-if="$env != 'app'&& !musicTrigger">
            App打开
          </div>
          <!-- 选择乐器 -->

          <div class="select-action" @click="selectMusic" v-if="!musicTrigger">选择乐器</div>
          <div class="selectMusic-contaner" v-show="musicTrigger">
            <div class="selectMusic-content">
              <div class="music-back" @click="selectMusic">取消</div>
              <div class="swiper-container">
                <div class="swiper-wrapper">
                  <div
                    class="swiper-slide"
                    v-for="(item, index) in list"
                    :key="index"
                    @click="goDetail(index)"
                  >
                    <img
                      :src="
                        item.smallPic +
                        '?x-oss-process=image/resize,w_200,m_lfit'
                      "
                      class="main-img"
                    />
                    <div class="title">{{ item.mainTitle }}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- 左边 -->
          <div class="select-action-1-container" @click="slectPoup(1)">
            <img
              class="select-action-1"
              :src="
                require('@/assets/images/musicalInstrument/musicalInstrument-action1.png')
              "
            />介绍
          </div>
          <div class="select-action-2-container" @click="slectPoup(2)">
            <img
              class="select-action-2"
              :src="
                require('@/assets/images/musicalInstrument/musicalInstrument-action2.png')
              "
            />参数
          </div>
          <!-- 360旋转 -->
          <VueProduct360
            :images="
              images.length === 0 ? actionFaceData.musicList[0].images : images
            "
            :key="time"
            v-if="!sizeTrigger"
            image-class="routeImages"
            :infinite="true"
          >
            <p>Loading</p>
          </VueProduct360>
          <div class="coverImage" v-if="sizeTrigger">
            <img :src="coverIamge" />
          </div>
          <div
            class="color-select-container"
            v-show="colorTrigger"
            v-if="actionFaceData.musicList.length > 1"
          >
            <div
              v-for="(item, index) in actionFaceData.musicList"
              :class="[colorIndex === index ? 'item-active' : 'item-unactive']"
              :key="index"
              @click="selectColor(index)"
            >
              <div class="item" :style="{ backgroundColor: item.color }"></div>
            </div>
          </div>
          <div
            v-if="actionFaceData.musicList.length > 1"
            @click="colorHandle"
            class="select-action-3-container"
            :style="{
              border: colorTrigger
                ? '1px solid #BB7DFF'
                : '1px solid transparent',
              color: colorTrigger ? '#BB7DFF' : '#3E4147',
              background: colorTrigger
                ? 'rgba(187, 125, 255, .06)'
                : 'rgba(0, 0, 0, .06)',
            }"
          >
            <img
              class="select-action-3"
              :src="
                colorTrigger
                  ? require('@/assets/images/musicalInstrument/musicalInstrument-action3.5.png')
                  : require('@/assets/images/musicalInstrument/musicalInstrument-action3.png')
              "
            />外观
          </div>
          <div
            class="select-action-4-container"
            @click="sizeHandle"
            :style="{
              border: sizeTrigger
                ? '1px solid #BB7DFF'
                : '1px solid transparent',
              color: sizeTrigger ? '#BB7DFF' : '#3E4147',
              background: sizeTrigger
                ? 'rgba(187, 125, 255, .06)'
                : 'rgba(0, 0, 0, .06)',
            }"
          >
            <img
              class="select-action-4"
              :src="
                sizeTrigger
                  ? require('@/assets/images/musicalInstrument/musicalInstrument-action4.5.png')
                  : require('@/assets/images/musicalInstrument/musicalInstrument-action4.png')
              "
            />尺寸
          </div>

          <van-popup class="popup" v-model="popup" position="right">
            <div class="poup-main">
              <div class="title">{{ poupType == 1 ? '介绍' : '参数' }}</div>
              <div v-show="poupType == 1" class="content">
                {{ actionFaceData.firstDesc }}
              </div>
              <div v-show="poupType == 2" class="item-content">
                <div
                  class="item"
                  v-for="(item, index) in actionFaceData.firstParameter"
                  :key="index"
                >
                  <div class="left">{{ item.name }}</div>
                  <div class="right">{{ item.value }}</div>
                </div>
              </div>
            </div>
          </van-popup>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Swiper from './swiper'
import { Popup } from 'vant'
import VueProduct360 from './vue-product-360.vue'
const musicalInstrument= [
  {
    mainPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/d1.png',
    mainBg:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/b1.png',
    smallPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/s1.png',
    mainTitle: 'ArtPlay X1 Pro',
    mainDesc: '适用少儿音乐素养课',
    mainTag: '24键音色,7键打击乐,电吉他',
    firstDesc:
      '小熊音乐神器的宗旨是多种乐器的合成器性能集成与一个紧凑的键盘外壳中，并且加入新型的现代感设计，符合当今一代乐手的“便携式音乐创作”的需求，相比于其他合成器而言，小熊音乐神器更加简化了操作技能，同时也方便了孩子们对此音乐合成器的了解。轻便小巧、方便携带，集吉他、键盘、贝斯、打击乐、以及各种效果器一身的神器。',
    firstParameter: [
      { name: '产品型号', value: 'X1 Pro', icon: '' },
      { name: '产品尺寸', value: '675mmx127mmx45mm', icon: '' },
      { name: '产品净重', value: '770g', icon: '' },
      { name: '蓝牙名称', value: 'ArtPlay X1 Pro', icon: '' },
      { name: '蓝牙版本', value: '5.0', icon: '' },
      { name: '输入电压', value: 'DC6V', icon: '' },
      { name: '支持系统', value: 'Android/ios/windows/Mac', icon: '' },
    ],
    rotateImages: [
      {
        color: '#000',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_x1Pro/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size5.png?x-oss-process=image/resize,w_500/format,webp',
      },
    ],
  },
  {
    mainPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/d3.png',
    mainBg:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/b3.png',
    smallPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/s3.png',
    mainTitle: 'ArtPlay P1 ',
    mainDesc: '适用器乐学习-钢琴系统课',
    mainTag: '法国DREAM音源,高保真喇叭',
    firstDesc:
      'ArtPlay P1 小熊音乐钢琴弹唱课程所采用的硬件设备之一，88键重锤榔头键盘，外观采取了滑盖设计，保证琴键避免夹手，简约利落的金属踏板，仿传统大三角钢琴踏板功能，环保烤漆工艺，采用防渗油工艺，防止油漆渗漏以及甲醛污染。该款电钢琴采用了法国DREAM音源，高保真还原了真实钢琴的弹奏手感和触键力度。',
    firstParameter: [
      { name: '产品型号', value: 'ArtPlay P1', icon: '' },
      { name: '产品尺寸', value: '1380mm * 480mm * 941mm', icon: '' },
      { name: '产品净重', value: '46KG', icon: '' },
      { name: '蓝牙名称', value: 'ArtPlay P1', icon: '' },
      { name: '蓝牙版本', value: '/', icon: '' },
      { name: '输入电压', value: 'DC15V/3A', icon: '' },
      { name: '支持系统', value: 'Android/IOS', icon: '' },
    ],
    rotateImages: [
      {
        color: '#000',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_B/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size3.png?x-oss-process=image/resize,w_500/format,webp',
      },
      {
        color: '#FFF',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/Artplay_P1_W/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size4.png?x-oss-process=image/resize,w_500/format,webp',
      },
    ],
  },
  {
    mainPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/d4.png',
    mainBg:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/b4.png',
    smallPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/s4.png',
    mainTitle: 'ArtPlay S1',
    mainDesc: '适用声乐学习-小熊声乐汇',
    mainTag: '高保真咪头,悬浮震撼双喇叭',
    firstDesc:
      '小熊音乐麦克风是集专业智能麦克风和专业声卡一体的机器，锂电池超强续航能力更满足不同场景的歌唱需求！内置专业声卡降噪，更格外设置了多种k歌音效，满足孩子们对于多种曲风歌曲的歌唱需要。值得一提的是内含特制DSP芯片调制硬软件结合更能塑造美妙声音另外高保真咪头和悬浮震撼双喇叭让小熊音乐麦克风能够环绕立体发声声效，清晰灵敏拾音、不失真不破音，更能沉浸式体验唱歌！',
    firstParameter: [
      { name: '产品型号', value: 'ArtPlay S1', icon: '' },
      { name: '产品尺寸', value: '89mm * 89mm * 313mm', icon: '' },
      { name: '产品净重', value: '/', icon: '' },
      { name: '蓝牙名称', value: 'ArtPlay S1', icon: '' },
      { name: '蓝牙版本', value: '5.0', icon: '' },
      { name: '输入电压', value: 'DC5V/1A', icon: '' },
      { name: '支持系统', value: 'Android/IOS', icon: '' },
    ],
    rotateImages: [
      {
        color: '#FFF',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size7.png?x-oss-process=image/resize,w_500/format,webp',
      },
    ],
  },
  {
    mainPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/d5.png',
    mainBg:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/b5.png',
    smallPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/s5.png',
    mainTitle: 'ArtPlay S1 Pro',
    mainDesc: '适用声乐学习-小熊声乐汇',
    mainTag: '高保真咪头,悬浮震撼双喇叭',
    firstDesc:
      '小熊音乐麦克风是集专业智能麦克风和专业声卡一体的机器，锂电池超强续航能力更满足不同场景的歌唱需求！内置专业声卡降噪，更格外设置了多种k歌音效，满足孩子们对于多种曲风歌曲的歌唱需要。值得一提的是内含特制DSP芯片调制硬软件结合更能塑造美妙声音另外高保真咪头和悬浮震撼双喇叭让小熊音乐麦克风能够环绕立体发声声效，清晰灵敏拾音、不失真不破音，更能沉浸式体验唱歌！',
    firstParameter: [
      { name: '产品型号', value: 'ArtPlay S1 Pro', icon: '' },
      { name: '产品尺寸', value: '83mm * 83mm * 313mm', icon: '' },
      { name: '产品净重', value: '/', icon: '' },
      { name: '蓝牙名称', value: 'ArtPlay S1 Pro', icon: '' },
      { name: '蓝牙版本', value: '5.0', icon: '' },
      { name: '输入电压', value: 'DC5V/1A', icon: '' },
      { name: '支持系统', value: 'Android/IOS', icon: '' },
    ],
    rotateImages: [
      {
        color: '#FFF',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlay_S1Pro/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size6.png?x-oss-process=image/resize,w_500/format,webp',
      },
    ],
  },
  {
    mainPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/d6.png',
    mainBg:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/b6.png',
    smallPic:
      'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/tab/s6.png',
    mainTitle: 'ArtPlay P1 L',
    mainDesc: '适用器乐学习-钢琴系统课',
    mainTag: '法国DREAM音源,高保真喇叭',
    firstDesc:
      'ArtPlay P1 L 小熊音乐钢琴弹唱课程所采用的硬件设备之一，88键重锤榔头键盘，给孩子更接近三角钢琴的阻力手感。该款电钢琴采用了法国DREAM音源，准确还原了三角钢琴的宽广音域，让孩子以更接近三角钢琴的发声方式来训练自己的耳朵，并且高度还原了真钢琴的触键力度和听觉快感。同时拥有四种配色的外观设计，分别包括：原木色、梦幻紫色、典雅黑色、天空蓝色供大家选择。',
    firstParameter: [
      { name: '产品型号', value: 'ArtPlay P1-L', icon: '' },
      { name: '产品尺寸', value: '1270mm * 350mm * 130mm', icon: '' },
      { name: '产品净重', value: '19.5KG', icon: '' },
      { name: '蓝牙名称', value: 'ArtPlay P1', icon: '' },
      { name: '蓝牙版本', value: '5.0', icon: '' },
      { name: '输入电压', value: 'DC15V/3A', icon: '' },
      { name: '支持系统', value: 'Android/IOS', icon: '' },
    ],
    rotateImages: [
      {
        color: '#000',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Black/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size2.png?x-oss-process=image/resize,w_500/format,webp',
      },
      {
        color: '#B38BD6',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_Blue/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size1.png?x-oss-process=image/resize,w_500/format,webp',
      },
      {
        color: '#36B8E1',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_purple/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size8.png?x-oss-process=image/resize,w_500/format,webp',
      },
      {
        color: '#995430',
        images: [
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/1.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/2.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/3.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/4.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/5.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/6.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/7.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/8.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/9.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/10.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/11.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/12.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/13.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/14.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/15.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/16.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/17.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/18.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/19.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/20.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/21.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/22.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/23.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/24.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/25.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/26.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/27.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/28.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/29.png?x-oss-process=image/resize,w_500/format,webp',
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/ArtPlayP1_L_wood/30.png?x-oss-process=image/resize,w_500/format,webp',
        ],
        sizeImage:
          'http://ai-xxyy-backend-online.oss-cn-hangzhou.aliyuncs.com/h5/musicalInstrument/20220214/sizeImage/size9.png?x-oss-process=image/resize,w_500/format,webp',
      },
    ],
  },
]
export default {
  name: 'horizontalscreen360',
  components: {
    [Popup.name]: Popup,
    VueProduct360,
  },
  data() {
    return {
      id: this.$route.params.id,
      list: [],
      infoData: null,
      actionFaceData: null,
      count: 0,
      popup: false,
      poupType: 1,
      images: [],
      coverIamge: '',
      colorIndex: 0,
      colorTrigger: false,
      sizeTrigger: false,
      musicTrigger: false,
      time: 1,
    }
  },
  created() {
    this.init()
  },
  methods: {
    openApp() {
      window.location.href =
        'https://a.app.qq.com/o/simple.jsp?pkgname=com.msb.bear.music&fromcase=40003'
    },
    init() {
      musicalInstrument &&
        musicalInstrument.map((item) => {
          if (item.mainTag) {
            item.mainTagArray = item.mainTag.split(',')
          }
        })
      this.list = musicalInstrument
      let infoData = this.list[this.id]
      this.time = +new Date()
      this.actionFaceData = {
        firstDesc: infoData.firstDesc,
        firstParameter: infoData.firstParameter,
        musicList: infoData.rotateImages,
        sizeImage: infoData.sizeImage,
      }
      this.images = this.actionFaceData.musicList[0].images
      this.coverIamge = this.actionFaceData.musicList[0].sizeImage
      document.title = infoData.mainTitle
    },
    selectMusic() {
      this.musicTrigger = !this.musicTrigger
      this.colorTrigger = false
      this.sizeTrigger = false
      if (this.musicTrigger) {
        setTimeout(() => {
          this.initSwiper(true)
        }, 0)
      }
    },
    sizeHandle() {
      this.sizeTrigger = !this.sizeTrigger
      this.colorTrigger = !this.colorTrigger
    },
    colorHandle() {
      this.sizeTrigger = false
      this.colorTrigger = !this.colorTrigger
    },
    slectPoup(type) {
      this.popup = true
      this.poupType = type
    },
    selectColor(index) {
      this.colorIndex = index
      this.coverIamge = this.actionFaceData.musicList[index].sizeImage
      this.images = this.actionFaceData.musicList[index].images
    },
    initSwiper(isReverse) {
      var swiper = new Swiper('.swiper-container', {
        slidesPerView: 4,
        spaceBetween: 30,
        centeredSlides: true,
        isReverse: isReverse,
      })
    },
    goDetail(index) {
      this.init()
      this.musicTrigger = !this.musicTrigger
      this.sizeTrigger = false
      this.colorTrigger = false
      window.location.replace(`/hardware/showDetail/${index}`)
    },
  },
  mounted() {},
}
</script>
<style lang="scss">
.page-showDetail-container {
  position: fixed;
  left: 0;
  top: 0;
  bottom: 0;
  right: 0;
  width: 100%;
  height: 100%;
}
@import './landscape.scss';
@import './portrait.scss';
</style>
