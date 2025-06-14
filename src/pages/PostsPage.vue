<template>
	<main>
		<h1 id="heading">Posts Page</h1>

		<InputUI
			v-model.trim="searchQuery"
			v-focus
			type="search"
			class="search"
			placeholder="search"
		/>

		<PaginationPosts v-if="searchQuery.length === 0" />

		<PostsList :posts="sortedAndSearchedPosts" />

		<div
			v-if="sortedAndSearchedPosts.length"
			v-observer="postsStore.fetchPosts"
		></div>

		<div class="filter">
			<PagesLimit />
			<SelectUI v-model="selectedSort" :options="sortOptions" title="sort" />
		</div>

		<ButtonUI title="Add new post" @click="uiStore.openModal('addPost')"
			><IconsPlus class="add" />
		</ButtonUI>

		<ModalUI v-if="uiStore.modal.type === 'addPost'">
			<FormAddPost />
		</ModalUI>
	</main>
</template>

<!-- COMPOSITION API -->

<script setup lang="ts">
import FormAddPost from "../components/FormAddPost.vue";
import IconsPlus from "../components/Icons/Plus.vue";
import PagesLimit from "../components/PagesLimit.vue";
import PaginationPosts from "../components/PaginationPosts.vue";
import PostsList from "../components/PostsList.vue";

import { usePostsStore } from "../store/posts.ts";
import { useUIStore } from "../store/ui.ts";

import { useRoute } from "vue-router";

import vFocus from "../directives/VFocus.ts";
import vObserver from "../directives/VObserver.ts";

import { computed, onMounted, reactive, ref, watch } from "vue";

const postsStore = usePostsStore();
const uiStore = useUIStore();

const route = useRoute();

const searchQuery = ref("");
const selectedSort = ref("");
const sortOptions = reactive([
	{ title: "by title", value: "title" },
	{ title: "by body", value: "body" },
]);

const scrollToUp = () => {
	const heading = document.getElementById("heading");

	if (heading) {
		heading.scrollIntoView();
	}
};

const sortedAndSearchedPosts = computed(() => {
	const sortedPosts = [...postsStore.posts];

	if (selectedSort.value) {
		sortedPosts.sort((post1, post2) =>
			post1[selectedSort.value as "title" | "body"].localeCompare(
				post2[selectedSort.value as "title" | "body"],
			),
		);
	}

	return sortedPosts.filter((post) =>
		post.title.toLowerCase().includes(searchQuery.value.toLowerCase()),
	);
});

watch(selectedSort, () => {
	scrollToUp();
});

onMounted(() => {
	postsStore.fetchPosts();

	const queryLimit = Number(route.query.limit);
	const queryPage = Number(route.query.page);

	if (queryLimit !== postsStore.limit || queryPage !== postsStore.page) {
		postsStore.setLimit(queryLimit, queryPage);
		postsStore.setPage(queryPage);
	}
});
</script>

<!-- OPTIONS API -->

<!-- <script>
import FormAddPost from "../components/FormAddPost.vue";
import IconsPlus from "../components/Icons/Plus.vue";
import PagesLimit from "../components/PagesLimit.vue";
import PaginationPosts from "../components/PaginationPosts.vue";
import PostsList from "../components/PostsList.vue";

import { mapStores } from "pinia";
import { usePostsStore } from "../store/posts.ts";
import { useUIStore } from "../store/ui.ts";

import VFocus from "../directives/VFocus.ts";
import VObserver from "../directives/VObserver.ts";

export default {
	data() {
		return {
			searchQuery: "",
			selectedSort: "",
			sortOptions: [
				{ title: "by title", value: "title" },
				{ title: "by body", value: "body" },
			],
		};
	},

	methods: {
		scrollToUp() {
			document.getElementById("heading").scrollIntoView();
		},
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

	watch: {
		selectedSort() {
			this.scrollToUp();
		},
	},

	mounted() {
		this.postsStore.fetchPosts();

		const queryLimit = Number(this.$route.query.limit);
		const queryPage = Number(this.$route.query.page);

		if (
			queryLimit !== this.postsStore.limit ||
			queryPage !== this.postsStore.page
		) {
			this.postsStore.setLimit(queryLimit, queryPage);
			this.postsStore.setPage(queryPage);
		}
	},

	components: {
		PostsList,
		FormAddPost,
		PaginationPosts,
		PagesLimit,
		IconsPlus,
	},

	directives: {
		observer: VObserver,
		focus: VFocus,
	},
};
</script> -->

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
	right: 3rem;
	bottom: 3rem;

	display: flex;
	align-items: center;
	justify-content: center;

	width: 50px;
	height: 50px;

	color: #555;

	background-color: white;
	border: 1px solid #333;
	border-radius: 50%;

	transition: 1s;

	&:hover {
		transform: scale(1.1);
	}

	button {
		font-size: 20px;

		border: none;
	}
}

.filter {
	position: fixed;
	right: 0;
	bottom: 0;

	select {
		padding: 0.3rem;

		color: white;

		background-color: #555;
	}
}
</style>
