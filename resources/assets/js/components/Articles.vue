<template>
	<div>
		<h1>Articles </h1>
		<form @submit.prevent="addArticle" class="mb-3">
			<div class="form-group">
				<input type="text" class="form-control" placeholder="Title" v-model="article.title">
			</div>
			<div class="form-group">
				<textarea class="form-control" placeholder="Body" v-model="article.body"></textarea>
			</div>
			<button type="submit" class="btn btn-light btn-block">Save</button>
		</form>
		<div class="card card-body mb-2" v-for="article in articles" :key="article.id">
			<h3>{{article.title}}</h3>
			<p>{{article.body}}</p>
			<button class="btn btn-warning" @click="editArticle(article)">Edit</button>
			<button class="btn btn-danger" @click="deleteArticle(article.id)">Delete</button>
		</div>
	</div>
</template>

<script>
	export default{
		data(){
			return {
				articles: [],
				article: {
					id:'',
					title: '',
					body: ''
				},
				article_id: '',
				pagination: {},
				edit:false
			}
		},
		created(){
			this.fetchArticles();
		},
		methods:{
			fetchArticles(page_url){
				let vm = this;
				page_url = page_url || 'api/articles'
				fetch(page_url)
				.then(res => res.json())
				.then(res => {
					this.articles = res.data;
					vm.makePagination(res.meta, res.links);			
				})
				.catch(err => console.log(err))
			},
			makePagination(meta, links){
				let pagination = {
					current_page : meta.current_page,
					last_page: meta.last_page,
					next_page_url: links.next,
					previous_page_url: links.prev
				}
				this.pagination = pagination
			},
			deleteArticle(id){
				if(confirm('Are You Sure?')){
					fetch(`api/article/${id}`, {
						method: 'delete'
					})
					.then(res => res.json())
					.then(res => {
						alert('Article Deleted')
						this.fetchArticles()
					})
					.catch(err => console.log(err))

				}
			},
			addArticle(){
				if(this.edit === false){
					//Add
					fetch('api/article', {
						method: 'post',
						body: JSON.stringify(this.article),
						headers: {
							'content-type' : 'application/json'
						} 
					})
					.then(res => res.json())
					.then(data => {
						this.article.title = ''
						this.article.body = ''
						alert('Article Added')
						this.fetchArticles()
					})
					.catch(err => console.log(err))
				}
				else{
					// Put
					fetch('api/article', {
						method: 'put',
						body: JSON.stringify(this.article),
						headers: {
							'content-type' : 'application/json'
						} 
					})
					.then(res => res.json())
					.then(data => {
						this.article.title = ''
						this.article.body = ''
						alert('Article Updated')
						this.fetchArticles()
					})
					.catch()
				}
			},
			editArticle(article){
				this.edit = true;
				this.article.id = article.id;
				this.article.article_id = article.id;
				this.article.title = article.title;
				this.article.body = article.body
			}
		}
	}
</script>

<style>
	
</style>