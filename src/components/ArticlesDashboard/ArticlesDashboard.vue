<template>
    <div class="content">
        <div class="masthead">
            <h2 class="font-weight-bold">You're in good company</h2>
            <div class="filters">
                <span class="text-uppercase">Filter by:</span>
                <select v-model="appliedFilters.industry" @change="applyFilters()">
                    <option v-if="!appliedFilters.industry" selected="selected" :value="undefined">Industries</option>
                    <option v-if="appliedFilters.industry" :value="undefined" class="link-filter">Clear</option>
                    <option v-for="(key, value) in filters.industries" :value="value">{{value}} ({{key}})</option>
                </select>
                <select v-model="appliedFilters.location" @change="applyFilters()">
                    <option v-if="!appliedFilters.location" selected="selected" :value="undefined">Locations</option>
                    <option v-if="appliedFilters.location" :value="undefined" class="link-filter">Clear</option>
                    <option v-for="(key, value) in filters.locations" :value="value">{{value}} ({{key}})</option>
                </select>
                <select v-model="appliedFilters.company_size" @change="applyFilters()">
                    <option v-if="!appliedFilters.company_size" selected="selected" :value="undefined">Company Size</option>
                    <option v-if="appliedFilters.company_size" :value="undefined" class="link-filter">Clear</option>
                    <option v-for="(key, value) in filters.companySizes" :value="value">{{value}} ({{key}})</option>
                </select>
                <select v-model="appliedFilters.use_case" @change="applyFilters()">
                    <option v-if="!appliedFilters.use_case" selected="selected" :value="undefined">Use Case</option>
                    <option v-if="appliedFilters.use_case" :value="undefined" class="link-filter">Clear</option>
                    <option v-for="(key, value) in filters.useCases" :value="value">{{value}} ({{key}})</option>
                </select>
            </div>
        </div>
        <div class="articles">
            <div class="article" v-for="data in displayedArticles">
                <div class="article__hero">
                    <img :src="data.image_url" class="article__img">
                </div>
                <div class="article__details">
                    <p>{{data.excerpt}}</p>
                </div>
                <div>
                    <p class="article__info">Read More <img src="@/assets/arrow.png"/></p>
                    <p class="article__time"><img src="@/assets/timer.png"/> {{getReadTime(data.word_count)}}</p>
                </div>
            </div>
        </div>
        <div class="clearfix btn-group col-md-2 mt-4" v-if="pages.length > 1">
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
                perPage: 9,
                pages: [],
                filters: {
                    industries: {},
                    locations: {},
                    companySizes: {},
                    useCases: {}
                },
                appliedFilters: {
                    industry: undefined,
                    location: undefined,
                    company_size: undefined,
                    use_case: undefined
                }
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
                this.computeFilters(this.articles);
            }
        },
        methods: {
            // to calculate read time we take 200 as average reading words per minute.
            // divide the total words in the article by the average words per minute.
            getReadTime (words) {
                let wordsPerMinute = 200;
                let result = 'Less than a min read';
                if(words > 0){
                    let value = Math.ceil(words / wordsPerMinute);
                    result = value +  'min read';
                }
                return result;
            },
            // Method to get articles.
            // to dynamically get data implement http.get call and await for results and set it on this.articles
            getArticles () {
                this.articles = JSON;
                this.originalArticles = this.articles;
            },
            setPages () {
                this.pages = [];
                let numberOfPages = Math.ceil(this.articles.length / this.perPage);
                for (let index = 1; index <= numberOfPages; index++) {
                    this.pages.push(index);
                }
            },
            paginate (articles) {
                // here it is assumed that on a page a max of 9 articles should be displayed.
                let page = this.page;
                let perPage = this.perPage;
                let from = (page * perPage) - perPage;
                let to = (page * perPage);
                return articles.slice(from, to);
            },
            computeFilters (articles) {
                this.filters = {
                    industries: {},
                    locations: {},
                    companySizes: {},
                    useCases: {}
                };
                for (const article of articles) {
                    this.filters.industries[article.industry] = (this.filters.industries[article.industry] || 0) + 1;
                    this.filters.locations[article.location] = (this.filters.locations[article.location] || 0) + 1;
                    this.filters.companySizes[article.company_size] = (this.filters.companySizes[article.company_size] || 0) + 1;
                    for (const useCase of article.use_case) {
                        this.filters.useCases[useCase] = (this.filters.useCases[useCase] || 0) + 1;
                    }
                }
            },
            applyFilters() {
                this.articles = this.originalArticles;
                for (const type in this.appliedFilters) {
                    const value = this.appliedFilters[type];
                    if (!value) {
                        continue;
                    }
                    this.applyFilter(type, value);
                }
            },
            applyFilter (type, value){
                const articles = [];
                const displayedArticles = this.articles;
                for (const article of displayedArticles) {
                    if (article[type] instanceof Array) {
                        for (const sub of article[type]) {
                            if (sub === value) {
                                articles.push(article);
                                break;
                            }
                        }
                    } else if (article[type] === value) {
                        articles.push(article);
                    }
                }
                this.articles = articles;
                this.page = 1;
            }
        },
        mounted () {
            this.getArticles();
            // fetch unique filter categories
            this.computeFilters(this.articles);
        }
    }
</script>

<style scoped>

</style>
