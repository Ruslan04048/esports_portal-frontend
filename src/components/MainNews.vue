<template>
  <div class="MainNews_container">
    <h1>Главные новости</h1>
    <div v-for="post in topCommentedPosts" :key="post.id" class="MainNews_items">
      <router-link :to="`/${post.category}/${post.id}`" class="news-link">
        <img
          class="news_icon"
          :src="post.category === 'dota' ? dotaIcon : csIcon"
          alt="icon"
        />
        <div class="news_content">
          <p class="news_text">{{ post.title }}</p>
          <div class="news_meta">
            <time class="news_date" :datetime="post.created_at">
              {{ new Date(post.created_at).toLocaleDateString() }}
            </time>
            <div class="comments_block">
              <p class="comment_count">{{ post.comments_count }}</p>
              <img src="../assets/images/comment.svg" alt="comment" />
            </div>
          </div>
        </div>
      </router-link>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import api from "@/services/axios";
import dotaIcon from "@/assets/images/dota_icon.svg";
import csIcon from "@/assets/images/cs_icon.svg";

const allPosts = ref([]);

onMounted(async () => {
  try {
    const res = await api.get("/posts");
    if (res.data.status === 1) {
      allPosts.value = res.data.data.data;
    }
  } catch (e) {
    console.error("Ошибка при загрузке всех постов:", e);
  }
});

// сортировка главных новостей по комменту
const topCommentedPosts = computed(() => {
  return [...allPosts.value]
    .map((p) => ({
      ...p,
      comments_count:
        p.comments_count != null
          ? p.comments_count
          : Array.isArray(p.comments)
          ? p.comments.length
          : 0,
    }))
    .sort((a, b) => b.comments_count - a.comments_count)
    .slice(0, 4);
});
</script>

<style scoped>
.MainNews_container {
  box-sizing: border-box;
  width: 100%;
  margin-top: 20px;
}

.MainNews_items {
  display: flex;
  align-items: center;
  gap: 20px;
  width: 100%;
  max-width: 1200px;
  margin-top: 20px;
}

.news_icon {
  width: 40px;
  height: auto;
  flex-shrink: 0;
}

.news_content {
  display: flex;
  align-items: center;
  gap: 10px;
  flex: 1;
}

.news_text {
  font-size: 2rem;
  margin: 0 0 10px 0;
  color: white;
}

.news-link {
  text-decoration: none;
  display: flex;
  align-items: center;
  gap: 20px;
}

.news_meta {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  align-items: center;
  font-size: 1rem;
  color: #d0d0d0;
}

.comments_block {
  display: flex;
  gap: 5px;
  align-items: center;
  color: white;
}

.comment_count {
  font-weight: bold;
  font-size: 20px;
}

.comments_block img {
  width: 24px;
}

@media (max-width: 600px) {
  .news_icon {
    width: 40px;
    margin-bottom: 10px;
  }

  .news_text {
    font-size: 16px;
  }

  .news_meta {
    gap: 8px;
  }
}
</style>
