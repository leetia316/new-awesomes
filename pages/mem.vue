<template lang="pug">
  div.mem-area
    div.mem-banner
      div.container

      nuxt-link(to="/mem" class="tx-link") 
        img.tx(:src="cdn(mem.avatar, 'mem')")
      h4 {{mem.nc}}

      div.info
        span(title="坐标")
          icon(name="location") {{mem.mem_info.location || '坐标'}}

        span(title="公司")
          icon(name="company") {{mem.mem_info.company || '公司'}}

        a(:href="mem.mem_info.blog" target="_blank" v-if="isExsit(mem.mem_info.blog)" )
          icon(name="home") 主页

        a(:href="'http://weibo.com/' + mem.mem_info.weibo_url" target="_blank" v-if="isExsit(mem.mem_info.weibo_url)")
          icon(name="weibo")

        a(:href="'https://twitter.com/' + mem.mem_info.twitter" target="_blank"  v-if="isExsit(mem.mem_info.twitter)")
          icon(name="twitter")

        a(:href="'https://github.com/' + mem.mem_info.github" target="_blank"  v-if="isExsit(mem.mem_info.github)")
          icon(name="github")
        

    div.container.conarea
      div.mem-menus
        div.left
          div.dropdown-outer
            span.title {{getPageName()}}
            icon(name="arrow-up" rotate="90")
            div.dropdown
              nuxt-link(:to="'/mem/' + mem.id") {{who}}在用
              nuxt-link(:to="'/mem/' + mem.id + '/marks/repos'") {{who}}收藏的
              nuxt-link(:to="'/mem/' + mem.id + '/pubs/comments'") {{who}}发布的
              // nuxt-link(:to="'/mem/' + mem.id + '/ups'") {{who}}点赞的
          div.seconds
            nuxt-link(:to="'/mem/' + mem.id" v-if="routeKey === ''") 前端库
            nuxt-link(:to="'/mem/' + mem.id + '/marks/repos'" v-if="routeKey === 'marks'") 前端库
            
            template(v-if="routeKey === 'pubs'")
              nuxt-link(:to="'/mem/' + mem.id + '/pubs/comments'") 评论
              // nuxt-link(:to="'/mem/' + mem.id + '/pubs/dianps'") 经验
        div.right
      div.mem-body  
        nuxt-child  
</template>

<script>
  import axios from '~plugins/axios'
  export default {
    async asyncData ({ req, params, query }) {
      let res = await axios().get(`mem/${params.id}`)
      return {
        mem: res.data
      }
    },
    watch: {
      '$router.params.id': function (val) {
        axios().get(`mem/${val}`).then(res => {
          this.mem = res.data
        })
      }
    },
    computed: {
      session () {
        return this.$store.state.session || {}
      },
      who () {
        return this.session.id === parseInt(this.$route.params.id) ? '我' : 'TA'
      },
      routeKey () {
        return this.$route.name.split('-')[2] || ''
      }
    },
    methods: {
      isExsit: (str) => {
        return str && str.trim() !== ''
      },
      getPageName: function () {
        return {
          '': `${this.who}在用`,
          'marks': `${this.who}收藏的`,
          'pubs': `${this.who}发布的`
        }[this.routeKey]
      }
    },
    created () {
    }
  }
</script>


<style lang="scss" scoped>
  .mem-menus {
    font-size: 14px;
    font-weight: bold;
    display: flex;
    margin-bottom: 10px;
    background-color: rgb(232, 236, 230);

    .left {
      flex-grow: 1
    }

    .right {
      font-size: 13px;
      a {
        margin-right: 0;
        margin-left: 20px;
      }
    }

    a {
      padding: 15px 10px;
      display: inline-block;
    }
    .seconds {
      display: inline-block;
      a {
        &.nuxt-link-active {
          color: #da552f
        }
      }
    }



    .dropdown {
      position: absolute;
      z-index: 10;
      background-color: #FFF;
      border: #EEE 1px solid;
      border-top: #f7f8fa  1px solid;
      text-align: center;
      border-bottom: 0;
      top: 51px;
      left: 10px;
      display: none;
      a {
        display: block;
        border-bottom: #EEE 1px solid;
        padding: 13px 30px;
        width: 100%;
        &.router-link-exact-active {
          color: #da552f
        }
      }
    }

    .dropdown-outer {
      display: inline-block;
      &:hover {
        .dropdown {
          display: block;
        }
      }
      .title {
        padding:  15px;
        display: inline-block;
      }
    }
  }
  .mem-banner {
    text-align: center;
    padding: 50px;
    background: #f7f8fa;
    margin-bottom: 20px;
    padding-bottom: 150px;

    .tx-link {
      display: inline-block;
      border-radius: 100%;
      background-color: rgba(255, 255, 255, 0.25);
      padding: 10px;
      height: 120px;
    }
    .tx {
      width: 100px;
      height: 100px;
      border-radius: 100%;
      margin-bottom: 10px;
      padding: 10px;
      background: rgba(255, 255, 255, 0.39);
    }

    .info {
      line-height: 20px;
      margin-top: 20px;
      & > a, & > span {
        display: inline-block;
        padding: 0 10px;
        color: #9e9e9e
      }
      svg {
        float: left;
        margin-right: 3px;
      }
    }
  }

  .mem-area {
    background-color: rgba(247, 248, 250, 0.4);
    padding-bottom: 100px;
  }

  .conarea {
    max-width: 800px;
    background-color: #FFF;
    margin-top: -72px;
    padding: 0;
    padding-bottom: 50px;

    .mem-body {
      padding: 20px;
    }
  }

  .mem-navs {
    text-align: right;
    display: block;
    // border: #DDD 1px solid;
    border-left: 0;
    border-right: 0;
    padding: 5px;

    a {
      padding: 0 20px;
      font-weight: bold
    }
  }
</style>
