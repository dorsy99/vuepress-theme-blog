<template>
  <div id="base-list-layout">
    <div class="ui-posts">
      <div v-for="page in pages" :key="page.key" class="ui-post">
        <div class="ui-post-title">
          <NavLink :link="page.path">{{ page.title }}</NavLink>
        </div>

        <p class="ui-post-summary">
          {{ page.frontmatter.summary || page.summary }}
          <!-- <Content :page-key="page.key" slot-key="intro"/>-->
        </p>

        <div v-if="page.frontmatter.author" class="ui-post-meta ui-post-author">
          <NavigationIcon />
          <span
            >{{ page.frontmatter.author }} in
            {{ page.frontmatter.location }}</span
          >
        </div>

        <div v-if="page.frontmatter.date" class="ui-post-meta ui-post-date">
          <ClockIcon />
          <span>{{ resolvePostDate(page.frontmatter.date) }}</span>
        </div>

        <div v-if="page.frontmatter.tags" class="ui-post-meta ui-post-tag">
          <TagIcon />
          <router-link
            v-for="tag in resolvePostTags(page.frontmatter.tags)"
            :key="tag"
            :to="'/tag/' + tag"
          >
            {{ tag }}
          </router-link>
        </div>
      </div>
    </div>

    <component
      :is="paginationComponent"
      v-if="$pagination.length > 1 && paginationComponent"
    ></component>
  </div>
</template>

<script>
/* global THEME_BLOG_PAGINATION_COMPONENT */

import Vue from 'vue'
import dayjs from 'dayjs'
import { NavigationIcon, ClockIcon, TagIcon } from 'vue-feather-icons'
import {
  Pagination,
  SimplePagination,
} from '@vuepress/plugin-blog/lib/client/components'

export default {
  components: { NavigationIcon, ClockIcon, TagIcon },

  data() {
    return {
      paginationComponent: null,
    }
  },

  computed: {
    pages() {
      return this.$pagination.pages
    },
  },

  created() {
    this.paginationComponent = this.getPaginationComponent()
  },

  methods: {
    getPaginationComponent() {
      const n = THEME_BLOG_PAGINATION_COMPONENT
      if (n === 'Pagination') {
        return Pagination
      }

      if (n === 'SimplePagination') {
        return SimplePagination
      }

      return Vue.component(n) || Pagination
    },

    resolvePostDate(date) {
      return dayjs(date).format(
        this.$themeConfig.dateFormat || 'ddd MMM DD YYYY'
      )
    },

    resolvePostTags(tags) {
      if (!tags || Array.isArray(tags)) return tags
      return [tags]
    },
  },
}
</script>

<style lang="stylus">
.common-layout
  .content-wrapper
    padding-bottom 80px

.ui-post
  padding-bottom 25px
  margin-bottom 25px
  border-bottom 1px solid $borderColor

  &:last-child
    border-bottom 0px
    margin-bottom 0px

.ui-post-title
  font-family PT Serif, Serif
  font-size 28px
  border-bottom 0

  a
    cursor pointer
    color $darkTextColor
    transition all 0.2s
    text-decoration none

    &:hover
      text-decoration underline

.ui-post-summary
  font-size 14px
  color rgba($darkTextColor, 0.54)
  font-weight 200

.ui-post-meta
  display inline-flex
  align-items center
  font-size 12px
  line-height 12px

  &:not(:last-child)
    margin-bottom 3px
    margin-right 20px

  svg
    margin-right 5px
    width 14px
    height 14px

  @media (max-width: $MQMobile)
    display flex

    &:not(:last-child)
      margin-bottom 10px

.ui-post-author
  color rgba($darkTextColor, 0.84)
  font-weight 400

.ui-post-date
  color rgba($darkTextColor, 0.54)
  font-weight 200

.ui-post-tag
  color rgba($darkTextColor, 0.54)
  font-weight 200

  a
    color inherit
    font-weight 200
    text-decoration none
    margin-right 5px

    &:hover
      color $accentColor
</style>
