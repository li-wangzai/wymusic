<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div>
    <van-search
      v-model="value"
      placeholder="请输入搜索关键词"
      @input="inputFn"
    />
    <div class="search_wrap" v-if="searchList.length == 0">
      <p class="hot_title">热门搜索</p>
      <div class="hot_name_wrap">
        <span
          class="hot_item"
          v-for="(obj, index) in hotList"
          :key="index"
          @click="fn(obj.first)"
          >{{ obj.first }}</span
        >
      </div>
    </div>
    <div class="search_wrap" v-else>
      <!-- 标题 -->
      <p class="hot_title">最佳匹配</p>

      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <SongItem
          v-for="obj in searchList"
          :key="obj.id"
          :name="obj.name"
          :author="obj.ar[0].name"
          :id="obj.id"
        ></SongItem>
      </van-list>
    </div>
  </div>
</template>

<script>
import { hotSearchAPI, searchResultAPI } from "@/api";
import SongItem from "@/components/SongItem.vue";
export default {
  components: {
    SongItem,
  },
  data() {
    return {
      value: "",
      hotList: [],
      searchList: [],
      loading: false,
      finished: false,
      page: 1,
      timer:""
    };
  },
  async created() {
    const res = await hotSearchAPI({});
    console.log(res);
    this.hotList = res.data.result.hots;
  },
  methods: {
    async fn(val) {
      this.page = 1;
      this.finished = false;
      this.value = val;
      const res = await this.getListFn();
      console.log(res);
      this.searchList = res.data.result.songs;
      this.loading=false;
    },
    async getListFn() {
      return await searchResultAPI({
        keywords: this.value,
        limit: 20,
      });
    },
    async inputFn() {
      if(this.timer) clearTimeout(this.timer)
      this.timer = setTimeout(async() =>{
        this.page=1;
        this.finished=false
      if (this.value.length === 0) {
        this.searchList = [];
        return;
      }
      const res = await this.getListFn();
      console.log(res);
      if(res.data.result.songs === undefined) {
        this.searchList = [];
        return
      }
      this.searchList = res.data.result.songs;
      this.loading = false
      },900)
      
    },
    async onLoad(){
      this.page++;
      const res = await this.getListFn();
      if(res.data.result.songs === undefined){
        this.finished = true;
        this.loading = false;
        return
      }
      this.searchList = [...this.searchList,...res.data.result.songs];
      this.loading = false
    }
  },
};
</script>

<style>
/* 搜索容器的样式 */
.search_wrap {
  padding: 0.266667rem;
}

/*热门搜索文字标题样式 */
.hot_title {
  font-size: 0.32rem;
  color: #666;
}

/* 热搜词_容器 */
.hot_name_wrap {
  margin: 0.266667rem 0;
}

/* 热搜词_样式 */
.hot_item {
  display: inline-block;
  height: 1.853333rem;
  margin-right: 0.513333rem;
  margin-bottom: 0.513333rem;
  padding: 0 0.573333rem;
  font-size: 1rem;
  line-height: 1.853333rem;
  color: #333;
  border-color: #d3d4da;
  border-radius: 0.853333rem;
  border: 1px solid #d3d4da;
}
</style>
