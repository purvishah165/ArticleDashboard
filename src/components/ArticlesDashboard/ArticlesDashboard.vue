<template>
    <div class="container">
        <h2>You're in good company</h2>
        <div class="articles">
        <div class="article"  v-for="data in displayedArticles">
            <div class="article__hero">
                <div class="article__actions"></div>
                <img :src="data.image_url" class="article__img">
            </div>
            <div class="article__details">
                <p>{{data.excerpt}}</p>
            </div>
            <div class="article__details">
                <p>Read More</p>
                <p>{{getReadTime(data.word_count)}}</p>
            </div>
        </div>
    </div>
        <div class="clearfix btn-group col-md-2 mt-4">
            <button type="button" class="btn btn-sm btn-outline-secondary" v-if="page != 1" @click="page--"> << </button>
            <button type="button" class="btn btn-sm btn-outline-secondary" v-for="pageNumber in pages.slice(page-1, page+5)" @click="page = pageNumber"> {{pageNumber}} </button>
            <button type="button" @click="page++" v-if="page < pages.length" class="btn btn-sm btn-outline-secondary"> >> </button>
        </div>
    </div>
</template>

<script>
    import JSON from '@/helpers/sample.json'
    export default {
        name: 'articles-dashboard',
        data () {
            return {
                articles: [],
                page: 1,
                perPage: 6,
                pages: [],
            }
        },
        computed: {
            displayedArticles () {
                return this.paginate(this.articles);
            }
        },
        watch: {
            articles () {
                this.setPages();
            }
        },
        methods: {
            getReadTime (words) {
                let wordsPerMinute = 200;//average reading speed
                let result = 0 + 'min read';
                if(words > 0){
                    let value = Math.ceil(words / wordsPerMinute);
                    result = value +  'min read';
                }
                return result;
            },
            getArticles () {
                this.articles = JSON;
            },
            setPages () {
                let numberOfPages = Math.ceil(this.articles.length / this.perPage);
                for (let index = 1; index <= numberOfPages; index++) {
                    this.pages.push(index);
                }
            },
            paginate (articles) {
                let page = this.page;
                let perPage = this.perPage;
                let from = (page * perPage) - perPage;
                let to = (page * perPage);
                return  articles.slice(from, to);
            }
        },
        mounted () {
            this.getArticles();
        }
    }
</script>

<style scoped>

</style>
