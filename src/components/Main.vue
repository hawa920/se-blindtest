<template lang="pug">
    v-app
        v-toolbar(dark :color='!mode ? "pink lighten-1" : "green lighten-1"' fixed app)
            v-toolbar-side-icon(@click.stop='drawer = !drawer'): v-icon mdi-instagram

            v-toolbar-title se-BlindTest
            v-spacer
            v-btn(icon)
                v-icon mdi-cake
            v-btn(style='margin-right: 25px!important' icon)
                v-icon mdi-bell
        v-content
             v-container(fill-height)
                v-layout(wrap)
                    v-flex(xs12)
                        v-flex(xs12 md6 offset-md3)
                            h2.amaze-title(@mouseenter='flag=false' @mouseleave='flag=true') Compare Results from Different Search Engines&nbsp;
                                span(v-show='flag') 🙌
                                span(v-show='!flag') 👐
                            v-text-field.mt-5.mx-5(autofocus label='Hit The Road' v-model='keyword' @keyup.enter='search')
                            v-btn(dark large color='orange darken-1' @click='search' :loading='loading')
                                v-icon mdi-magnify
                                | &nbsp;搜尋&nbsp;&nbsp;&nbsp;
                            v-btn(dark large color='red lighten-1' @click='changeMode')
                                v-icon mdi-cat
                                | &nbsp;好手氣
                    v-flex(xs4)
                        div(v-html='googles' style="text-align: left")
                    v-flex(xs4)
                        div(v-html='baidus' style="text-align: left")
                    v-flex(xs4)
                        div(v-html='bings' style="text-align: left")
                    // v-flex.mt-3(md6 offset-md3 lg4 offset-lg4 d-flex v-for='item in content' :key='item._id')
                        v-card.elevation-3
                            v-container.pa-0
                                v-layout(wrap)
                                    v-flex(xs12)
                                        v-card-media.white--text(height=400 :src='item._source.imgURL')
                                    v-flex(xs12)
                                        v-card-title {{ item._source.description }}
                                        v-card-actions
                                            v-spacer
                                            v-btn(flat color='orange' :href='item._source.postURL' target='_blank' rel='noopener') Detail
                                            v-spacer
                                            //v-btn(flat color='red')
        v-fab-transition
            v-btn(fixed aria-label='fab' bottom right dark fab color='red' @click='toTop' style='margin: 12px 10px;')
                v-icon mdi-chevron-up

</template>

<script>
import axios from 'axios'
import $ from 'jquery'

window.google = {
    aft: function (tt) {
        return true
    }
}

export default {
    name: 'HelloWorld',
    data: () => ({
        flag: true,
        loading: false,
        keyword: '',
        content: [],
        mode: true,
        header: 'se-blindTest',
        googles: '',
        baidus: '',
        bings: ''
    }),
    methods: {
        toTop () {
            window.scroll({ top: 0, behavior: 'smooth' })
        },
        async search () {
            this.keyword = this.keyword.replace(' ', '+')
            this.loading = true
            console.log(this.keyword.split(' '))
            // let result = await axios.get(`http://localhost:9200/bpintaiwan/posts/_search?q=${this.keyword}`)
            let result = await axios.get(`https://www.google.com.tw/search?q=${this.keyword || 'taiwan'}`)

            let parsers = $.parseHTML(result.data)
            this.googles = $(parsers).find('#search')[0].innerHTML || '<h2>查無結果</h2>'
            console.log(this.googles)

            result = await axios.get(`https://www.baidu.com/s?wd=${this.keyword || 'taiwan'}`)
            parsers = $.parseHTML(result.data)
            this.baidus = $(parsers).find('#content_left')
            this.baidus = this.baidus.length === 0 ? '<h2 style="color:red">查無結果</h2>' : this.baidus[0].innerHTML
            // this.baidus = $(parsers).find('#content_left')[0].innerHTML || '<h2>查無結果</h2>'

            result = await axios.get(`https://www.bing.com/search?q=${this.keyword || 'taiwan'}`)
            parsers = $.parseHTML(result.data)
            this.bings = $(parsers).find('#b_results')[0].innerHTML || '<h2>查無結果</h2>'
            this.loading = false
            // this.content = result.data.hits.hits
            // setTimeout(() => { this.loading = false }, 1000)
            // this.toTop()
            // console.log(result.data)
        },

        changeMode () {
            this.mode = !this.mode
            this.content = []
            this.keyword = ''
        }
    }
}
</script>

<style lang="stylus">
    @import '../assets/style'
</style>
