<template>
  <h1>Posts Page</h1>

  <input-ui
    type="search"
    class="search"
    v-model.trim="searchQuery"
    v-focus
    placeholder="search"
  />

  <PaginationPosts />

  <PostsList :posts="sortedAndSearchedPosts" />

  <div class="scroll" v-observer="postsStore.fetchPosts"></div>

  <div class="sort">
    <PagesLimit />
    <select-ui :options="sortOptions" v-model="selectedSort" title="sort" />
  </div>

  <div class="add">
    <button-ui @click="uiStore.openModal('addPost')" title="Add new post"
      >+
    </button-ui>
  </div>

  <keep-alive>
    <modal-ui v-if="uiStore.modal.type === 'addPost'">
      <FormAddPost />
    </modal-ui>
  </keep-alive>
</template>

<script>
import PostsList from '../components/PostsList.vue';
import FormAddPost from '../components/FormAddPost.vue';
import PaginationPosts from '../components/PaginationPosts.vue';
import PagesLimit from '../components/PagesLimit.vue';

import { mapStores } from 'pinia';
import { usePostsStore } from '../store/PostsStore.js';
import { useUIStore } from '../store/UIStore.js';

import VIntersection from '../directives/VIntersection.js';
import VFocus from '../directives/VFocus.js';

export default {
  data() {
    return {
      searchQuery: '',
      selectedSort: '',
      sortOptions: [
        { title: 'by title', value: 'title' },
        { title: 'by body', value: 'body' },
      ],
    };
  },

  directives: {
    observer: VIntersection,
    focus: VFocus,
  },

  mounted() {
    this.postsStore.fetchPosts();
  },

  computed: {
    ...mapStores(usePostsStore, useUIStore),

    sortedPosts() {
      return [...this.postsStore.posts].sort((post1, post2) =>
        post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]),
      );
    },

    sortedAndSearchedPosts() {
      return this.sortedPosts.filter((post) =>
        post.title.toLowerCase().includes(this.searchQuery.toLowerCase()),
      );
    },
  },

  components: {
    PostsList,
    FormAddPost,
    PaginationPosts,
    PagesLimit,
  },
};
</script>

<style scoped lang="scss">
.search {
  padding: 1rem;
  font-size: 20px;
  border-radius: 1rem;

  &::placeholder {
    font-style: italic;
  }

  &:focus {
    outline: 2px solid #333;
  }
}

.add {
  position: fixed;
  bottom: 3rem;
  right: 3rem;

  display: flex;
  justify-content: center;
  align-items: center;

  width: 50px;
  height: 50px;

  background-color: bisque;
  border-radius: 50%;
  border: 1px solid #333;

  transition: 1s;

  &:hover {
    transform: scale(1.1);
  }

  button {
    border: none;
    font-size: 20px;
  }
}

.sort {
  position: fixed;
  bottom: 0;
  right: 0;

  select {
    padding: 0.3rem;
  }
}

.scroll {
  margin-top: 10rem;
}
</style>
