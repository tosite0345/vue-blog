<template>
  <div class="mt-3" id="post-body">
    <v-slide-x-transition>
      <v-card v-show="!loading">
        <v-img :src="getDefaultImage(post)" :height="clientSize()">
          <v-layout row wrap align-end justify-end fill-height>
            <v-flex xs12 class="title-bg pb-1 pl-3 pt-3">
              <h1 class="white--text headline pb-1">{{ post.title }}</h1>
              <p class="text-xs-right subheading font-weight-medium white--text pr-2">公開日：{{ formatDate(post.published, "YYYY-MM-DD hh:mm") }}</p>
            </v-flex>
          </v-layout>
        </v-img>
        <v-card-text primary-title id="post-body" class="pl-4 pr-4">
          <span v-html="post.body"></span>
        </v-card-text>
      </v-card>
    </v-slide-x-transition>

    <v-snackbar v-model="snackbar" bottom right multi-line>
      {{ message }}
      <v-btn color="pink" flat @click="snackbar = false">Close</v-btn>
    </v-snackbar>

    <p style="display: none;" id="twitter-title">{{ post.title }}</p>

  </div>
</template>

<style>
.title-bg { background: rgba(0, 0, 0, 0.7) }
#post-body img { max-width: 100% !important; margin-top: 8px; margin-bottom: 10px; }
#post-body * { letter-spacing: 4px; line-height: 1.8em; }
#post-body p { font-size: 120%; margin: 8px 0; }
#post-body h1 { margin-bottom: 8px; position: relative; background: #B3E5FC; box-shadow: 0px 0px 0px 5px #B3E5FC; border: dashed 2px white; padding: 0.2em 0.5em; color: #454545; margin: 24px 0; }
#post-body h1:after{ position: absolute; content: ''; left: -7px; top: -7px; border-width: 0 0 15px 15px; border-style: solid; border-color: #fff #fff #9FA8DA; box-shadow: 1px 1px 1px rgba(0, 0, 0, 0.15); }
#post-body h2 { margin-bottom: 8px; padding: 8px 0; position: relative; }
#post-body h2:after { content: ""; position: absolute; left: 0; bottom: 0; width: 100%; height: 7px; background: -webkit-repeating-linear-gradient(-45deg, #6ad1c8, #6ad1c8 2px, #fff 2px, #fff 4px); background: repeating-linear-gradient(-45deg, #6ad1c8, #6ad1c8 2px, #fff 2px, #fff 4px); }
#post-body h3 { margin: 12px 0; position: relative; padding-left: 1.5em; line-height: 2.0; }
#post-body h3:before{ font-family: "Font Awesome 5 Free"; content: "＊"; position: absolute; font-size: 1.4em; left: 0; top: -0.2em; color: #FFB300; }
#post-body blockquote * { font-size: 95%; }
#post-body blockquote { position: relative; padding: 5px 12px 5px 56px; box-sizing: border-box; font-style: italic; color: #464646; background: #fff4db; }
#post-body blockquote:before{ display: inline-block; position: absolute; top: 50%; left: 10px; width: 36px; height: 36px; -ms-transform: translateY(-50%); -webkit-transform: translateY(-50%); transform: translateY(-50%); vertical-align: middle; text-align: center; content: '“'; color: #FFF; font-size: 20px; line-height: 36px; background: #ffd596; border-radius: 50%; font-weight: 900; }
</style>
<script>
var butter = require('buttercms')(process.env.BUTTER_API_KEY)
export default {
  props : {
    author : [Object, Array],
    // blogid : Number,
    // posts  : [Object, Array],
  },
  data () {
    return {
      slug     : null,
      post     : [],
      loading  : true,
      message  : null,
      snackbar : false,
    }
  },
  methods: {
    getPost () {
      this.slug = this.$route.params.slug
      butter.post.retrieve(this.slug)
      .then(res => {
        // console.log(res.data)
        this.post = res.data.data
        setTimeout(() => {
          this.loading = false
          this.keepHeight()
        }, 400)
      }).catch(err => {
        this.$router.push('/404')
        // console.log(err)
      });
    },
    keepHeight () {
      var div = document.getElementById('post-body')
      if (!div) {
        return
      }
      var h = div.clientHeight
      if (h < 10) {
        return
      }
      // console.log(h)
      div.style.height = h + 'px'
    },
  },
  watch: {
    $route(to, from) {
      this.loading = true
      this.keepHeight()
      this.getPost()
    },
    // post () {
    // },
  },
  created () {
    this.loading = true
    this.getPost()
  }
}
</script>