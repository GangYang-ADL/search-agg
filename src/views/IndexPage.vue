<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import UserList from "@/components/UserList.vue";
import PictureList from "@/components/PictureList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";

const postList = ref([]);
const userList = ref([]);
myAxios.post("/post/list/page/vo", {}).then((res: any) => {
  postList.value = res.records;
});

myAxios.post("/user/list/page/vo", {}).then((res: any) => {
  console.log(res.records);
  userList.value = res.records;
});

const router = useRouter();
const route = useRoute();

const selectedTab = route.params.category;
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

const onSearch = () => {
  router.push({
    query: searchParams.value,
  });
};

const onTabChange = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
</script>

<template>
  <div class="index-input">
    <a-input-search
      v-model:value="searchParams.text"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />
    <MyDivider />
    <a-tabs v-model:activeKey="selectedTab" @change="onTabChange">
      <a-tab-pane key="post" tab="Tab 1">
        <PostList :post-List="postList" />
      </a-tab-pane>
      <a-tab-pane key="user" tab="Tab 2" force-render>
        <UserList :user-list="userList" />
      </a-tab-pane>
      <a-tab-pane key="picture" tab="Tab 3">
        <PictureList />
      </a-tab-pane>
    </a-tabs>
  </div>
</template>

<style></style>
