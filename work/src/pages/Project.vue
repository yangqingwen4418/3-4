<template>
  <Layout>
    <section>
      <div style="min-height: 600px" v-loading="loading">
        <el-card shadow="never" style="margin-bottom: 20px">
          <el-input placeholder="请输入关键字" v-model="searchKey" clearable style="width: 300px"></el-input>
          <el-button @click="search" icon="el-icon-search" style="margin-left: 10px" circle plain></el-button>
          <el-button icon="el-icon-share" type="warning" style="margin-left: 10px" plain circle></el-button>
        </el-card>

        <div v-if="projects.length">
          <div class="el-card is-hover-shadow" style="margin-bottom: 20px;" v-for="project of projects" :key="project.id">
            <div class="el-card__header">
              <div>
                <div class="el-row">
                  <div class="el-col el-col-16">
                    <span>
                      <a :href="project.githubUrl" target="_blank" style="text-decoration: none; cursor: pointer;">
                        <i class="el-icon-service"></i>&nbsp;&nbsp; 
                        {{ project.name }}
                      </a>
                    </span>
                  </div> 
                  <div class="el-col el-col-8">
                    <div style="text-align: right;">
                      <!-- <button type="button" class="el-button el-button--text" style="padding: 3px 0px;">
                        <i class="el-icon-back"></i>
                        <span>前往GitHub</span>
                      </button>  -->
                      <a :href="project.githubUrl" target="_blank" class="el-button el-button--text" style="padding: 3px 0px;text-decoration: none; cursor: pointer;"><!---->
                        <i class="el-icon-back"></i>
                        <span>前往GitHub</span>
                      </a> 
                      <button type="button" class="el-button el-button--text" style="padding: 3px 0px;"><!---->
                        <i class="el-icon-share"></i><!---->
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="el-card__body"> 
              <div style="font-size: 0.9rem; line-height: 1.5; color: rgb(96, 108, 113);">
                最近更新 {{ getDate(project.updated_at) }}
              </div> 
              <div style="font-size: 1.1rem; line-height: 1.5; color: rgb(48, 49, 51); padding: 10px 0px 0px;">
                {{ project.desc }}
              </div> 
              <div style="font-size: 1.1rem; color: rgb(48, 49, 51); padding: 10px 0px 0px;">
                <div class="el-row">
                  <div class="el-col el-col-16" style="padding-top: 5px;">
                    <i class="el-icon-star-off el-tooltip" aria-describedby="el-tooltip-906" tabindex="0" style="margin: 0px 5px 0px 0px;"></i>
                    {{ project.collect }}
                    <i class="el-icon-view el-tooltip" aria-describedby="el-tooltip-4283" tabindex="0" style="margin: 0px 5px 0px 15px;"></i>
                    {{ project.view }}
                    <i class="el-icon-bell el-tooltip" aria-describedby="el-tooltip-1952" tabindex="0" style="margin: 0px 5px 0px 15px;"></i>
                    {{ project.attention }}
                  </div> 
                  <div class="el-col el-col-8" style="text-align: right;">
                    <span 
                      class="el-tag el-tag--small" 
                      v-for="tag of project.tags" 
                      :key="tag.id"
                      :style="{
                        color: tag.color,
                        backgroundColor: getColor(tag.color),
                        border: `1px solid ${tag.color}`
                      }"
                    >{{ tag.title }}</span> 
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div style="text-align: center;">
            <el-pagination
              background
              layout="prev, pager, next"
              @current-change="changePage"
              :page-count="$page.projects.pageInfo.totalPages"
              :current-page="$page.projects.pageInfo.currentPage">
            </el-pagination>
          </div>
        </div>
        <el-card shadow="never" class="empty-style" v-else>
          <font style="font-size: 30px;color:#dddddd ">
            <b>还没有开源项目 (╯°Д°)╯︵ ┻━┻</b>
          </font>
        </el-card>
      </div>
    </section>
  </Layout>
</template>

<page-query>
query ($page: Int) {
  projects: allStrapiProject (perPage: 3, page: $page) @paginate {
    pageInfo {
      totalPages
      currentPage
    }
    edges {
      node {
        id
        name
        desc
        githubUrl
        created_at
        updated_at
        collect
        view
        attention
        tags{
          id
          title
          color
        }
      }
    }
  }
}
</page-query>

<script>
  export default {
    name: "ProjectPage",
    metaInfo: {
      title: "我的项目",
    },
    data() {
      return {
        query: {
          page: 1,
          pageSize: 5,
          pageNumber: 1,
        },
        loading: false,
        searchKey: "",
        // projects: [],
      };
    },
    methods: {
      search() {},
      getDate (time) { // 转换时间
        return new Date(time).toLocaleString()
      },
      getColor (col) { // 将十六进制颜色转换成十进制颜色
        return `rgba(${parseInt(col.substring(1,3), 16)},${parseInt(col.substring(3,5), 16)},${parseInt(col.substring(5,7), 16)},0.1)`
      },
      changePage (page) { // 页码发生变化时触发
        if (page === 1) {
          return this.$router.push(`/project`)
        }
        this.$router.push(`/project/${page}`)
      }
    },
    computed: {
      projects () {
        return this.$page.projects.edges.map(i => i.node)
      }
    }
  };
</script>