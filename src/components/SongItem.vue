<template>
  <van-cell :title="name" :key="id" :label="author + '-' + name" center>
    <!-- 使用 right-icon 插槽来自定义右侧图标 -->
    <template #right-icon>
      <van-icon name="play-circle-o" size="1.5rem" @click="playFn" />
    </template>
  </van-cell>
</template>

<script>
export default {
  props: {
    name: String,
    id: Number,
    author: String,
  },
  methods: {
    playFn() {
      this.$router.push({
        path:'/play',
        query:{
          id:this.id
        }
      })
    },
  },
};
</script>

<style>
/* 给单元格设置底部边框 */
.van-cell {
  border-bottom: 1px solid lightgray;
}
</style>