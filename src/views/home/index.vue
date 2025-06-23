<template>
  <div class="block text-center">
    <el-carousel height="700px" motion-blur>
      <el-carousel-item v-for="(item, index) in images" :key="index">
        <img
          :src="item"
          alt="轮播图"
          style="width: 100%; height: 100%; object-fit: cover"
        />
      </el-carousel-item>
    </el-carousel>
  </div>
</template>

<script setup>
const images = [
  require("@/assets/images/banner1.png"),
  require("@/assets/images/banner2.png"),
  require("@/assets/images/banner3.png"),
  require("@/assets/images/banner4.png")
];
</script>

<style scoped lang="scss">
.block {
  margin: 0 auto;
  max-width: 1200px; /* 根据需要调整轮播图宽度 */
}

.el-carousel {
  border-radius: 8px; /* 添加圆角效果 */
  overflow: hidden; /* 确保图片不超出边界 */
}
</style>
