# Swiper

标签（空格分隔）： 实践

---

## 1.swiper结合vue-awesome-swiper
### 1.安装
- swiper版本：@8.4.7
- vue-awesome-swiper版本：@4.1.1
- 命令：`npm install swiper@8.4.7 vue-awesome-swiper@4.1.1`

### 2.版本变化
- 较低版本：swiper中内置使用各模块，如Pagination, Autoplay，无需手动引入
- 当前版本：swiper实现按需加载，对需要的模块进行手动引入

### 3.优缺点分析
- 优点：相比于原生swiper无需过多手动操作
- 缺点：swiper已经推出swiper/vue用于vue项目，原有vue-awesome-swiper已不再维护

### 4.局部注册组件

```javascript
<template>
  <div>
    <!-- 使用 swiper 组件 -->
    <swiper ref="mySwiper" :options="swiperOptions">
      <swiper-slide v-for="(slide, index) in slides" :key="index">
        {{ slide }}
      </swiper-slide>
      <!-- 分页器 -->
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>

<script>
// 局部导入所需的 Swiper 模块
import { Swiper, SwiperSlide } from 'vue-awesome-swiper'

// Swiper 核心样式
import 'swiper/swiper-bundle.min.css'

// 按需导入 Swiper 功能模块
import SwiperCore, { Pagination, Autoplay } from 'swiper'

// 安装 Swiper 模块
SwiperCore.use([Pagination, Autoplay])

export default {
  components: {
    Swiper,
    SwiperSlide
  },
  data() {
    return {
      slides: ['Slide 1', 'Slide 2', 'Slide 3'],
      swiperOptions: {
        // modules: [Pagination, Autoplay], 亦可用该配置项取代SwiperCore.use
        initialSlide: 0,
        autoplay: {
          delay: 5000
        },
        pagination: {
          el: '.swiper-pagination'
        }
        // 其他 Swiper 配置...
      }
    }
  },
  computed: {
    swiper() {
      return this.$refs.mySwiper.$swiper
    }
  },
  mounted() {
    // 现在你可以访问 Swiper 实例
    console.log('Current Swiper instance:', this.swiper)
  }
}
</script>

<style scoped>
/* 可以在这里添加自定义样式 */
.swiper-container {
  height: 300px;
}
</style>
```







