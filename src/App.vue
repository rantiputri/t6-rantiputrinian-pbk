<template>
  <div>
    <form @submit.prevent="save" class="form-container">
      <input type="text" v-model="form.title" placeholder="Title" class="form-input" /><br />
      <textarea v-model="form.content" placeholder="Content" class="form-input"></textarea><br />
      <button type="submit" class="form-button">{{ form.id ? 'Update' : 'Save' }}</button>
    </form>
    <ul class="articles-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <div class="article">
          <h3 class="article-title">{{ article.title }}</h3>
          <p class="article-content">{{ article.content }}</p>
          <button @click="edit(article)" class="edit-button">Edit</button>
          <button @click="remove(article.id)" class="delete-button">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load" class="load-button">Load</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      form: {
        id: null,
        title: '',
        content: ''
      }
    };
  },
  methods: {
    async save() {
      if (this.form.id) {
        // Update existing article
        const index = this.articles.findIndex(article => article.id === this.form.id);
        if (index !== -1) {
          this.articles.splice(index, 1, { ...this.form });
        }
      } else {
        // Add new article
        const newArticle = { ...this.form, id: Date.now().toString() };
        this.articles.push(newArticle);
      }
      this.resetForm();
    },
    edit(article) {
      this.form = { ...article };
    },
    remove(id) {
      const index = this.articles.findIndex(article => article.id === id);
      if (index !== -1) {
        this.articles.splice(index, 1);
      }
    },
    async load() {
      try {
        const response = await fetch('/db.json');
        const data = await response.json();
        this.articles = data.articles;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    },
    resetForm() {
      this.form = {
        id: null,
        title: '',
        content: ''
      };
    }
  },
  mounted() {
    this.load(); // Load articles when component is mounted
  }
};
</script>

<style scoped>
.form-container {
  background-color: #ffe4e1;
  border: 1px solid #ffb6c1;
  padding: 20px;
  border-radius: 10px;
  max-width: 500px;
  margin: auto;
}
.form-input {
  width: calc(100% - 20px);
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ffb6c1;
  border-radius: 5px;
  background-color: #ffccd5;
  font-size: 16px;
}
.form-button {
  background-color: #ff69b4;
  border: none;
  padding: 10px 20px;
  margin: 10px 0;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}
.form-button:hover {
  background-color: #ff4da6;
}
.articles-list {
  list-style-type: none;
  padding: 0;
  margin: 20px auto;
  max-width: 600px;
}
.article-item {
  background-color: #ffe4e1;
  border: 1px solid #ffb6c1;
  padding: 15px;
  margin: 10px 0;
  border-radius: 10px;
}
.article-title {
  color: #ff1493;
  margin: 0 0 10px 0;
}
.article-content {
  color: #fa6ab7;
  margin: 0 0 10px 0;
}
.edit-button, .delete-button {
  background-color: #ff69b4;
  border: none;
  padding: 5px 10px;
  margin: 5px 5px 0 0;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}
.edit-button:hover, .delete-button:hover {
  background-color: #ff4da6;
}
.load-button {
  background-color: #ff69b4;
  border: none;
  padding: 10px 20px;
  margin: 20px auto;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  display: block;
  transition: background-color 0.3s;
}
.load-button:hover {
  background-color: #ff4da6;
}
</style>
