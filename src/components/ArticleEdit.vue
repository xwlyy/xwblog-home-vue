<template>
  <div>
    <div class="uk-width-medium-3-4">
        <div class="uk-alert uk-alert-danger" v-if="message" v-text="message"></div>
        <form v-on:submit.prevent="submit" class="uk-form uk-form-stacked">
            <div class="uk-alert uk-alert-danger uk-hidden"></div>
            <div class="uk-form-row">
                <label class="uk-form-label">标题:</label>
                <div class="uk-form-controls">
                    <input v-model="title" type="text" placeholder="标题" class="uk-width-1-1">
                </div>
            </div>
            <div class="uk-form-row">
                <label class="uk-form-label">内容:</label>
                <div class="uk-form-controls">
                    <textarea id="div1" v-model="content" placeholder="内容" class="uk-width-1-1" style="height:400px;max-height:500px;"></textarea>
                    <div v-html="preview"></div>
                </div>
            </div>
            <div class="uk-form-row">
                <button type="submit" class="uk-button uk-button-primary"><i class="uk-icon-save"></i> 保存</button>
                <router-link :to="'/article/'+article.id" class="uk-button"><i class="uk-icon-times"></i> 取消</router-link>
            </div>
        </form>
    </div>
  </div>
</template>

<script>
import marked from 'marked'
import { mapGetters, mapActions } from 'vuex'

export default {
  data () {
    return {
      title: '',
      content: '',
      message: ''
    }
  },
  computed: {
    ...mapGetters({
      user: 'userDetail',
      article: 'articleDetail'
    }),
    preview: function () {
      let _content = this.content
      marked(_content, (err, content) => {
        if (!err) {
          _content = content
        }
      })
      return _content
    }
  },
  methods: {
    ...mapActions([
      'getArticleDetail',
      'addArticle',
      'modifyArticle'
    ]),
    submit: async function () {
      if (this.title.trim() === '' || this.content.trim() === '') {
        this.message = '标题和内容不能为空'
        return
      }
      if (this.$route.params.id !== 'new') {
        await this.modifyArticle({
          id: this.$route.params.id,
          title: this.title,
          content: this.content
        })
        this.$router.go(-1)
      } else {
        let res = await this.addArticle({
          title: this.title,
          content: this.content
        })
        this.$router.push({name: 'ArticleDetail', params: { id: res.data.data.articleId }})
      }
    }
  },
  mounted: function () {
    if (this.$route.params.id !== 'new') {
      // this.getArticleDetail(this.$route.params.id)
      this.title = this.article.title
      this.content = this.article.content
    }
  }
}
</script>
