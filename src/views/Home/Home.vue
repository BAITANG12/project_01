<template>
  <div class="home-container">
    <!-- 头部区域 -->
    <van-nav-bar title="头条" fixed />
    <!-- <p>{{ artList.length }}</p> -->
    <van-pull-refresh
      v-model="isLoading"
      :disabled="finished"
      @refresh="onRefresh"
    >
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <ArticleInfo
          v-for="item in artList"
          :key="item.id"
          :title="item.title"
          :author="item.aut_name"
          :cmt-count="item.comm_count"
          :time="item.pubdate"
          :cover="item.cover"
        ></ArticleInfo>
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
import { getArticleListAPI } from "@/api/articleAPI.js";
import ArticleInfo from "@/components/Article/ArticleInfo.vue";

export default {
  name: "Home",
  data() {
    return {
      page: 1,
      limit: 10,
      artList: [],
      loading: true,
      finished: false,
      isLoading: false,
    };
  },
  created() {
    this.initArticleList();
  },
  methods: {
    async initArticleList(isRefresh) {
      const { data: res } = await getArticleListAPI(this.page, this.limit);
      // console.log(res);
      if (isRefresh) {
        this.artList = [...res, ...this.artList];
        this.isLoading = false;
      } else {
        this.artList = [...this.artList, ...res];
        this.loading = false;
      }
      if (res.length === 0) {
        this.finished = true;
      }
    },
    onLoad() {
      console.log("出发了onlode时间");
      this.page++;
      this.initArticleList();
    },
    onRefresh() {
      this.page++;
      this.initArticleList(true);
    },
  },
  components: {
    ArticleInfo,
  },
};
</script>

<style lang="less" scoped>
.home-container {
  padding: 46px 0 50px 0;
  // .van-nav-bar {
  //   // background-color: #007bff;
  // }
  /deep/ .van-nav-bar__title {
    color: white;
  }
}
</style>