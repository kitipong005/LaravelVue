<template>
  <div>
    <h2>Articles</h2>
    <form @submit.prevent="addArticle()">
        <div class="form-froup">
            <input type="text" class="form-control mb-2" placeholder="title...." v-model="article.title">
        </div>
        <div class="form-froup">
            <textarea type="text" class="form-control mb-2" placeholder="body...." v-model="article.body"></textarea>
        </div>
        <button type="submit" class="btn btn-success btn-block mb-2">save</button>
    </form>
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item">
          <a class="page-link" href="#" @click="getArticles(pagination.prev_page_url)">Previous</a>
        </li>
        <li class="page-item disabled"><a class="page-link text-dark" href="#">page {{pagination.current_page}} of {{pagination.last_page}}</a></li>
        <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item">
          <a class="page-link" href="#" @click="getArticles(pagination.next_page_url)">Next</a>
        </li>
      </ul>
    </nav>
    <div class="card card-body mb-2" v-for="article in articles.data" v-bind:key="article.id">
      <h3>{{article.title}}</h3>
      <p>{{article.body}}</p>
      <button class="btn btn-warning mb-2" @click="editArticle(article)">Edit</button>
      <button class="btn btn-danger" @click="deleteArticle(article.id)">Delete</button>
    </div>
  </div>
</template>
<script>
export default {
  mounted() {
    this.getArticles();
  },
  methods: {
    getArticles(page_url) {
      let vm = this;
      page_url = page_url || "api/articles";
      axios.get(page_url).then(res => {
        this.articles = res.data;
        vm.makePagination(res.data);
      });
    },
    makePagination(data) {
      let pagination = {
        current_page: data.current_page,
        last_page: data.last_page,
        next_page_url: data.next_page_url,
        prev_page_url: data.prev_page_url
      };
      this.pagination = pagination;
    },
    addArticle(){
        if(this.edit === false){
            //Add
            axios.post('api/article',{
                body: JSON.stringify(this.article),
                headers: {
                    'content-type' : 'application/json'
                }
            }).then(res=>{
                this.article.title = '',
                this.article.body = '',
                this.getArticles();
            })
        }
        else {
            //Update            
            let page = "api/articles?page="+this.pagination.current_page;
            axios.put('api/article',{
                body: JSON.stringify(this.article),
                headers: {
                    'content-type' : 'application/json'
                }
            }).then(res=>{
                this.article.title = '',
                this.article.body = '',
                this.edit = false;
                this.getArticles(page);
            })
        }
    },
    editArticle(article){
        this.edit = true;
        this.article.id = article.id;
        this.article_id = article.id;
        this.article.title = article.title;
        this.article.body = article.body;
    },
    deleteArticle(id) {
        if(confirm('are you want delete this Article !!!')){
            axios.delete('api/article/'+id).then(res=>{
                alert('article remove');
                this.getArticles();
            }).catch(err => console.log(err));
        }
    }
  },
  data() {
    return {
      articles: [],
      article: {
        id: "",
        title: "",
        body: ""
      },
      article_id: "",
      pagination: {},
      edit: false
    };
  }
};
</script>

