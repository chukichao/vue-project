<template>
  <li>
    <div class="post-item" @click="$router.push(`/posts/${post.id}`)">
      <button-ui
        @click.stop="postsStore.removePost(post.id)"
        class="post-item-delete"
      >
        X
      </button-ui>

      <div class="post-item-content">
        <div class="post-item-content-body">
          <h3>{{ post.id < 100 ? `${post.id}.` : '' }} {{ post.title }}</h3>
          <p>{{ post.body }}</p>
        </div>
        <div class="post-item-content-edit">
          <button-ui @click.stop="uiStore.openModal('editPost', post.id)">
            Edit
          </button-ui>
        </div>
      </div>
    </div>

    <modal-ui v-if="uiStore.modal.extra === post.id">
      <FormUpdatePost :title="post.title" :body="post.body" :id="post.id" />
    </modal-ui>
  </li>
</template>

<script>
import FormUpdatePost from './FormUpdatePost.vue';

import { mapStores } from 'pinia';
import { usePostsStore } from '../store/PostsStore';
import { useUIStore } from '../store/UIStore.js';

export default {
  props: {
    post: Object,
  },

  computed: {
    ...mapStores(usePostsStore, useUIStore),
  },

  components: {
    FormUpdatePost,
  },
};
</script>

<style scoped lang="scss">
.post-item {
  display: flex;
  flex-direction: column;

  padding: 0 0 2rem 2rem;
  margin-top: 1rem;

  border: 2px solid #333;

  cursor: pointer;

  &:hover {
    box-shadow: 5px 5px 5px #333;
  }

  &-delete {
    align-self: end;
    border: none;

    &:hover {
      background-color: #333;
      color: white;
    }
  }

  &-content {
    display: flex;
    align-items: center;
    justify-content: space-between;

    margin-top: 2rem;

    &-body {
      p {
        padding: 0.5rem;
        margin: 1rem 0;

        text-indent: 0.5rem;
      }
    }

    &-edit {
      margin: 1rem;

      button:hover {
        background: bisque;
      }
    }
  }
}
</style>
