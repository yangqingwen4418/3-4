<template>
  <Layout>
    <section>
      <div style="min-height: 600px" v-loading="loading">
        <el-card shadow="never" style="min-height: 400px" v-if="dynamics.id">
          <div slot="header">
            <span>{{dynamics.title}}</span>
          </div>
          <div style="font-size: 0.9rem;line-height: 1.5;color: #606c71;">
            发布 {{ getDate(dynamics.created_at) }}
            <br />
            更新 {{ getDate(dynamics.updated_at) }}
          </div>
          <div
            style="font-size: 1.1rem;line-height: 1.5;color: #303133;border-bottom: 1px solid #E4E7ED;padding: 5px 0px 5px 0px"
          >
            <pre style="font-family: '微软雅黑'">{{dynamics.description}}</pre>
          </div>
          <div v-html="mdToHtml(dynamics.content)" class="markdown-body" style="padding-top: 20px"></div>
        </el-card>
        <el-card
          shadow="never"
          class="empty-style"
          v-else
        >
          <font style="font-size: 30px;color:#dddddd ">
            <b>没有更新 ╮(๑•́ ₃•̀๑)╭</b>
          </font>
        </el-card>
      </div>
    </section>
  </Layout>
</template>

<page-query>
query {
  allStrapiDynamic {
    edges {
      node {
        id
        title
        content
        description
        created_at
        updated_at
      }
    }
  }
}
</page-query>

<script>
  import MarkdownIt from 'markdown-it'
  const md = new MarkdownIt()
  export default {
    name: "HomePage",
    metaInfo: {
      title: "最新动态",
    },
    data() {
      return {
        query: {
          page: 1,
          pageSize: 1,
        },
        loading: false,
      };
    },
    computed: {
      dynamics () { // 获取最新动态信息
        return this.$page.allStrapiDynamic.edges[0].node
      }
    },
    methods: {
      getDate (time) { // 转换时间
        return new Date(time).toLocaleString()
      },
      mdToHtml (markdown) { // 将 md 格式转换成 HTML 格式
        return md.render(markdown)
      }
    }
  };
</script>

<style>
.home-links a {
  margin-right: 1rem;
}
</style>
