<template>
  <v-layout row wrap>
    <v-flex xs12>
      <v-card flat>
        <v-toolbar dark color="primary">
          <v-toolbar-title class="white--text">Git Repo Search Filter</v-toolbar-title>
        </v-toolbar>
        <v-layout row wrap class="" style="min-height: 100px; border: 1px solid #1976d2; align-items: center; padding: 10px 20px">
          <v-flex xs12 md3>
            <input placeholder="Search Repo" class="input-box" v-model="search_query">
          </v-flex>
          <v-flex xs12 md3>
            <v-btn color="primary" @click="fireQuery">
              Search
            </v-btn>
          </v-flex>
        </v-layout>
      </v-card>
    </v-flex>
    <v-flex xs12 class="mt-3">
      <v-card flat>
        <v-toolbar dark color="primary">
          <v-toolbar-title class="white--text">Repo Search Result</v-toolbar-title>
        </v-toolbar>
        <v-layout row wrap v-if="loading" class="loading-row">
          <v-progress-circular
            indeterminate
            :size="50"
            color="primary"
          ></v-progress-circular>
        </v-layout>
        <v-layout row wrap class="content-layout" v-else>
          <v-flex xs12 md4 v-for="(i,index) in paginate" :key="index" style="padding: 10px">
            <v-card class="content-card">
              <!--{{i}}-->
              <v-card-text>
                <v-layout>
                  <v-flex xs12 style="text-align: center">
                    <v-avatar
                      size="150"
                      color="grey lighten-4"
                    >
                      <img :src="i.owner.avatar_url" alt="avatar">
                    </v-avatar>
                  </v-flex>
                </v-layout>
                <v-layout>
                  <v-flex xs12 class="title mt-4" style="text-align: center">
                    {{i.name}}
                  </v-flex>
                </v-layout>
                <v-layout style="text-align: center">
                  <v-flex xs12 class="mt-4">
                    <span class="custom-chip"><i class="material-icons" style="width: 20px;font-size: 10px;">star_rate</i>{{i.stargazers_count}}</span>
                    <span class="custom-chip"><i class="material-icons" style="width: 20px;font-size: 10px;">call_split</i>{{i.forks_count}}</span>
                    <span class="custom-chip"><i class="material-icons" style="width: 20px;font-size: 10px;">info</i><span class="issue-text">Open Issues</span> {{i.open_issues_count}}</span>
                  </v-flex>
                </v-layout>
                <v-layout>
                  <v-flex xs12 class="description">
                    {{i.description}}
                  </v-flex>
                </v-layout>
              </v-card-text>
              <v-layout>
                <v-flex xs12>
                  <v-btn block color="primary" style="margin: 0; padding: 0; height: 50px" :href="i.html_url">View Project</v-btn>
                </v-flex>
              </v-layout>
            </v-card>
          </v-flex>
        </v-layout>
      </v-card>
    </v-flex>
    <v-flex xs12 style="text-align: center" class="mt-4">
      <v-pagination
        v-model="currentPage"
        :length="page_size"
      ></v-pagination>
    </v-flex>
  </v-layout>
</template>

<script>
  import axios from 'axios';
  export default {
    data:()=>({
      search_query:'',
      page_size:1,
      repo_items:[],
      currentPage: 1,
      itemsPerPage:3,
      loading:false,
    }),
    computed:{
      paginate: function() {
        if (!this.repo_items || this.repo_items.length != this.repo_items.length) {
          return
        }
        this.resultCount = this.repo_items.length
        if (this.currentPage >= this.page_size) {
          this.currentPage = this.page_size
        }
        var index = this.currentPage * this.itemsPerPage - this.itemsPerPage
        return this.repo_items.slice(index, index + this.itemsPerPage)
      },
      // filteredList(){
      //   return this.repo_items.filter(list => {
      //     this.resultCount = this.repo_items.length;
      //     if (this.currentPage >= this.page_size) {
      //       this.currentPage = Math.max(0, this.page_size - 1);
      //     }
      //     var index = this.currentPage * this.itemsPerPage;
      //     return this.repo_items.slice(index, index + this.itemsPerPage);
      //   })
      // }
    },
    methods:{
      fireQuery:function () {
        this.loading=true;
        axios.get(`https://api.github.com/search/repositories?q=${this.search_query}`).then(res=>{
          this.page_size = res.data.items.length/this.itemsPerPage;
          this.repo_items = res.data.items;
          this.loading=false;
        })
      }
    },
    components: {

    },
    filters: {
      paginate: function(list) {

      }
    }
  }
</script>
<style>
  .input-box{
    box-shadow: 3px 2px 11px 3px #efefef;
    width: 100%;
    background-color: white;
    height: 38px;
    padding-left: 15px;
  }
  .input-box::placeholder{
    padding-left: 10px;
  }
  .description{
    font-size: 12px;
    margin-top: 10px;
    color: #585858;
  }

  .custom-chip{
    border: 1px solid grey;
    border-radius: 10px;
    padding: 3px 12px;
    font-size: 11px;
  }

  @media screen and (max-width: 699px){
    .issue-text{
      display: none;
    }
  }

  @media screen and (min-width: 700px) {
    .content-layout{
      min-height: 100px; border: 1px solid #1976d2; align-items: center; padding: 10px 20px
    }
    .custom-chip{
      margin: 5px;
    }
    .v-card__text{
      height: 340px;
    }
    .content-card{
      height: 390px;
    }
  }
  .loading-row{
     text-align: center;
     justify-content: center;
     height: 120px;
     align-items: center;
   }
</style>
