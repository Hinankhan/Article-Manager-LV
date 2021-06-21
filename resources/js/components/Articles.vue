
<template>
  <div class="container mt-2">
    <form @submit.prevent="addArticle()" action="">
      <div class="form">
        <h1>Title</h1>
        <ui-textbox
          floating-label
          icon="title"
          label="Title"
          placeholder="Enter your name"
          v-model="article.title"
        ></ui-textbox>
        <h1>Body text</h1>
        <ui-textbox
          enforce-maxlength
          floating-label
          icon="article"
          help="Maximum 256 characters"
          label="Short bio"
          placeholder="Introduce yourself in a few words"
          :multi-line="true"
          :maxlength="256"
          v-model="article.body"
        ></ui-textbox>
        <div class="button text-center">
             <ui-button color="accent" size="normal">Save</ui-button
        >
        </div>
      </div>
    </form>

    <div class="card-deck mt-5">
      <div class="card" v-for="article in articles" v-bind:key="article.id">
        <div class="card-body">
          <h5 class="card-title">{{ article.title }}</h5>
          <p class="card-text">
            {{ article.body }}
          </p>
        </div>
        <div class="card-footer">
          <ui-icon-button  @click="editArticle(article)" color="accent" icon="edit"></ui-icon-button>
          &nbsp;
          <ui-icon-button
            @click="deleteArticle(article.id)"
            color="red"
            icon="delete"
          ></ui-icon-button>
        </div>
      </div>
    </div>
    <nav aria-label="Page navigation example">
      <ul class="pagination justify-content-center mt-4">
        <li
          :class="[{ disabled: !pagination.prev_page_url }]"
          class="page-item"
        >
          <a
            class="page-link"
            href="#"
            @click="getArticles(pagination.prev_page_url)"
            >Previous</a
          >
        </li>
        <li class="page-item disabled">
          <a class="page-link text-dark" href="#"
            >Page {{ pagination.current_page }} of {{ pagination.last_page }}</a
          >
        </li>
        <li
          :class="[{ disabled: !pagination.next_page_url }]"
          class="page-item"
        >
          <a
            class="page-link"
            href="#"
            @click="getArticles(pagination.next_page_url)"
            >Next</a
          >
        </li>
      </ul>
    </nav>
  </div>
</template>
<script>
export default {
  data() {
    return {
      selectedId: null,
      articles: [],
      article: {
        id: "",
        title: "",
        body: "",
      },
      article_id: "",
      pagination: {},
      edit: false,
    };
  },
  created() {
    this.getArticles();
  },
  methods: {
    getArticles(page_url) {
      let vm = this;
      page_url = page_url || "api/articles";
      fetch(page_url)
        .then((res) => res.json())
        .then((res) => {
          this.articles = res.data;
          vm.makePagination(res.meta, res.links);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    makePagination(meta, links) {
      let pagination = {
        current_page: meta.current_page,
        last_page: meta.last_page,
        next_page_url: links.next,
        prev_page_url: links.prev,
      };
      this.pagination = pagination;
    },
    deleteArticle(id) {
      if (confirm("Are you sure?")) {
        fetch(`api/article/${id}`, {
          method: "delete",
        })
          .then((res) => res.json())
          .then((data) => {
            alert("Article Removed");
            this.getArticles();
          })
          .catch((err) => console.log(err));
      }
    },
    addArticle(){
        if(this.edit === false){
            //Add aticle
            fetch('/api/article',{
                method:'post',
                body:JSON.stringify(this.article),
                headers:{
                    'content-type': 'application/json'
                }
            })
            .then(res => res.json)
            .then(data => {
                this.article.title="";
                this.article.body="";
                alert('Article Added')
                this.getArticles();
            })
            .catch(err => console.log(err))
        } else {
            //update article
             fetch('/api/article',{
                method:'put',
                body:JSON.stringify(this.article),
                headers:{
                    'content-type': 'application/json'
                }
            })
            .then(res => res.json)
            .then(data => {
                this.article.title="";
                this.article.body="";
                alert('Article updated')
                this.getArticles();
            })
            .catch(err => console.log(err))
        }
    },
    editArticle(article){
        this.edit = true;
        this.article.id = article.id;
        this.article.article_id = article.id;
        this.article.title = article.title;
        this.article.body = article.body;
    }
  },
};
</script>
<style scoped>
.form {
  width: 67%;
  height: 329px;
  padding: 26px;
  background: rgba(255, 255, 255, 0.2);
  -webkit-backdrop-filter: blur(16px);
  backdrop-filter: blur(16px);
  border-radius: 13px;
  position: relative;
  z-index: 1;
  margin: 0 auto;
}
.card-deck .card {
    background: rgba(255, 255, 255, 0.5);
    -webkit-backdrop-filter: blur(16px);
    backdrop-filter: blur(12px);
}
.page-link {
    background: rgba(255, 255, 255, 0.5);
    -webkit-backdrop-filter: blur(4px);
    backdrop-filter: blur(4px);
    color: black;
}
h1 {
  font-size: 25px;
}
.card-deck {
  position: relative;
  z-index: 1;
}
.pagination {
  position: relative;
  z-index: 1;
}
.ui-button--type-primary.ui-button--color-accent {
  width: 32%;
  border-radius: 18px;
  background: linear-gradient(#e91e63, #ffc107);
  cursor: pointer;
}
.ui-button--type-primary.ui-button--color-accent:hover {
    width:60% ;
     transition: 0.3s;
}
</style>
