<template>
  <el-breadcrumb class="app-breadcrumb" aeparator="/">
    <transition-group name="breadcrumb" style="line-height: 50px;">
      <el-breadcrumb-item v-for="(item) in levelList" :key="item.path">
        \{{ item.meta.title }}
      </el-breadcrumb-item>
    </transition-group>
  </el-breadcrumb>
</template>

<script>
import pathToRegexp from 'path-to-regexp'

export default {
  data() {
    return {
      levelList: null
    }
  },
  watch: {
    $route() {
      this.getBreadcrumb()
    }
  },
  created() {
    this.getBreadcrumb()
  },
  methods: {
    // 获取面包屑标题
    // generateTitle,
    // generateTitle(title) {
    //   const hasKey = this.$te('route.' + title)
    //   if (hasKey) {
    //     // $t :this method from vue-i18n, inject in @/lang/index.js
    //     const translatedTitle = this.$t('route.' + title)
    //     return translatedTitle
    //   }
    //   return title
    // },
    getBreadcrumb() {
      let matched = this.$route.matched.filter(item => item.name)
      const first = matched[0]
      if (first && first.name.trim().toLocaleLowerCase() !== 'Dashboard'.toLocaleLowerCase()) {
        matched = [{
          path: '/dashboard',
          meta: { title: '首页' }
        }].concat(matched)
      }

      this.levelList = matched.filter(item => item.meta && item.meta.title && item.meta.breadcrumb !== false)
    },
    pathCompile(path) {
      // To solve this problem https://github.com/PanJiaChen/vue-element-admin/issues/561
      const { params } = this.$route
      var toPath = pathToRegexp.compile(path)
      return toPath(params)
    },
    handleLink(item) {
      const { redirect, path } = item
      if (redirect) {
        this.$router.push(redirect)
        return
      }
      this.$router.push(this.pathCompile(path))
    }
  }
}
</script>

<style lang="scss" scoped>
.app-breadcrumb.el-breadcrumb {
  display: inline-block;
  font-size: 14px;
  line-height: 50px;
  margin-left: 8px;

  .no-redirect {
    color: #97a8be;
    cursor: text;
  }
}
