<template>
    <div class="col-md-12">
      <div class="container py-3" v-for="(value) in myArticle" :key="value._id">
        <div class="card">
            <div class="card-footer text-muted">
                <h4 class="card-title">{{value.title}}</h4>
            </div>
          <div class="row ">
            <div class="col-md-4 col-12">
                <img class="list-img" :src="value.img" width="200" height="200">
            </div>
            <div class="col-md-8 col-12 px-6">
              <div class="list-content">
                <div class="card-block px-3">
                  <p class="card-text">Posted on {{value.createAt}}</p>
                  <p class="card-text">by {{value.username}}</p>
                  <button class="btn btn-primary"  @click="detailArticle(value._id)">Read More</button>
                </div>
                <div class="py-3" v-if="username == value.username && username != ''">
                  <button type="button" class="btn btn-secondary my-2 my-sm-0" @click="test(value._id)" data-toggle="modal" data-target="#modalEdit">Edit</button>
                  <button class="btn btn-secondary" @click="deleteArticle(value._id)">Delete</button>
                </div>
              </div>
            </div>
          </div>
        </div>
    </div>
    <modalEdit :id="idArticle" :getmylist="getMyList"></modalEdit>
  </div>
</template>

<script>
import config from '@/config.js'
import modalEdit from '@/components/modalEdit.vue'
export default {
  components :{
    modalEdit
  },
  props : ['article', 'category', 'updatecomponent'],
  data() {
    return {
      myArticle : '',
      username : '',
      idArticle : ''
    }
  },
  watch: {
    category() {
      this.username = localStorage.getItem('username')
      if(this.category === 'all'){
        this.getMyList()
      }
      else {
        this.getListByCategory()
      }
    },
  },
  created : function(){
    this.username  = localStorage.getItem('username')
    if(this.username){
      if(this.category === 'all'){
        this.getMyList()
      }
      else{
        this.getListByCategory()
      }
    }
    else{
      this.$router.push({
        name : 'home'
      })
    }
  },
  methods: {
    test(value){
      this.idArticle = value
    },
    deleteArticle : function(value){
      axios({
        method : 'DELETE',
        url : `${config.port}/articles/delete/${value}`,
        headers : {
          token : localStorage.getItem('token')
        }
      })
      .then(response => {
        this.getMyList()
        this.updatecomponent()
      })
      .catch(err => {
        console.log(err)
      })
    },
    detailArticle(articleId) {
      this.$router.push({
        name : 'detailArticle',
        params : {
            id : articleId
        }
      })      
    },
    getMyList(){
      axios({
        method : 'GET',
        url : `${config.port}/articles/myarticle`,
        headers : {
          token : localStorage.getItem('token')
        }
      })
      .then(response => {
        this.myArticle = response.data
      })
      .catch(err => {
        console.log(err)
      })
    },
    getListByCategory(){
      axios({
        method : 'GET',
        url : `${config.port}/articles/show`,
      })
      .then(response => {
        let byCategory = response.data.filter(datum => {
          return this.category === datum.category
        })
        this.myArticle = byCategory
      })
      .catch(err => {
        console.log(err)
      })
    }      
  },
}
</script>

<style>
.card{
  width: 95%!important;
}
.col-md-4{
  width: 100%!important
}
</style>
