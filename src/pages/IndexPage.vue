<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchParams.text"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />
    <MyDivider />
    <a-tabs v-model:activeKey="activeKey" @change="onTabChange">
      <a-tab-pane key="post" tab="文章">
        <PostList :postList="postList" />
      </a-tab-pane>
      <a-tab-pane key="picture" tab="图片" force-render>
        <PictureList />
      </a-tab-pane>
      <a-tab-pane key="user" tab="用户">
        <UserList :userList="userList" />
      </a-tab-pane>
    </a-tabs>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import PictureList from "@/components/PictureList.vue";
import UserList from "@/components/UserList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";

const postList = ref([]);

myAxios.post("/post/list/page/vo", {}).then((res) => {
  postList.value = res?.records;
});

const userList = ref([]);

myAxios.post("/user/list/page/vo", {}).then((res) => {
  userList.value = res?.records;
});

const route = useRoute();
const router = useRouter();
const activeKey = ref((route.params.category as string) || "post");

watch(
  () => route.params.category,
  (category) => {
    if (category) {
      activeKey.value = category as string;
    }
  }
);

const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};

const searchParams = ref(initSearchParams);

watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text as string,
  } as any;
});

const onSearch = (value: string) => {
  router.push({
    query: searchParams.value,
  });
};

const onTabChange = (key: string) => {
  router.push({
    params: {
      category: key,
    },
    query: searchParams.value,
  });
};
</script>
