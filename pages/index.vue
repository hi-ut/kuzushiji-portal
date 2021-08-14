<template>
  <div>
    <section class="mb-5">
      <v-parallax :src="top" height="300">
        <v-layout column align-center justify-center class="white--text">
          <h1 class="white--text mb-2 display-1 text-center">
            <b>{{ siteName }}</b>
          </h1>
          <v-btn
            large
            class="mt-5"
            :to="localePath({ name: 'unicode' })"
            color="primary"
            >{{ $t('try_out') }}</v-btn
          >
        </v-layout>
      </v-parallax>
    </section>

    <v-container class="pt-10 mt-10">
      <div class="mb-5 pb-5 text-center">
        <!-- <h1>{{ $t(siteName) }}</h1> -->
        <h3>
          文字種: {{ size.toLocaleString() }}／文字数:
          {{ total.toLocaleString() }} の文字を探そう
        </h3>
      </div>

      <v-row class="mb-10">
        <v-col cols="12" md="3">
          
        </v-col>
        <v-col cols="12" md="6">
          <v-simple-table>
            <template v-slot:default>
              <thead>
                <tr>
                  <th class="text-center">データベース</th>
                  <th class="text-center">文字種</th>
                  <th class="text-center">文字数</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, key) in items" :key="key">
                  <td class="text-center">{{ item.label }}</td>
                  <td class="text-center">{{ item.size.toLocaleString() }}</td>
                  <td class="text-center">{{ item.total.toLocaleString() }}</td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-col>
        <v-col></v-col>
      </v-row>

      <div class="mb-5 pb-5 text-center">
        <!-- <h1>{{ $t(siteName) }}</h1> -->
        <p class="py-5" v-html="siteDesc"></p>
      </div>

      <div v-if="menu.length > 0 && false">
        <h3 class="mt-5 mb-10 text-center">{{ $t('menu') }}</h3>

        <v-row class="mb-10">
          <v-col v-for="(obj, key) in menu" :key="key" cols="12" :sm="3">
            <v-card flat no-body class="mb-4">
              <template v-if="obj.href">
                <a :href="obj.href" target="_blank">
                  <template v-if="obj.img">
                    <div
                      class="text-center grey lighten-2"
                      style="height: 150px"
                    >
                      <v-img contain style="height: 150px" :src="obj.img" />
                    </div>
                  </template>
                  <template v-else>
                    <div
                      class="text-center grey lighten-2 pa-10"
                      style="height: 150px"
                    >
                      <v-icon size="75">{{ obj.icon }}</v-icon>
                    </div>
                  </template>
                </a>
                <div class="pa-4">
                  <a :href="obj.href" target="_blank">
                    <h4>{{ obj.label }}</h4>
                  </a>

                  <p v-if="obj.description" class="mt-2 mb-0">
                    {{ obj.description }}
                  </p>
                </div>
              </template>
              <template v-else>
                <nuxt-link :to="localePath(obj.path)">
                  <div
                    class="text-center grey lighten-2 pa-10"
                    style="height: 150px"
                  >
                    <v-icon size="75">{{ obj.icon }}</v-icon>
                  </div>
                </nuxt-link>
                <div class="pa-4">
                  <nuxt-link :to="localePath(obj.path)">
                    <h4>{{ obj.label }}</h4>
                  </nuxt-link>

                  <p v-if="obj.description" class="mt-2 mb-0">
                    {{ obj.description }}
                  </p>
                </div>
              </template>
            </v-card>
          </v-col>
        </v-row>
      </div>

      <v-card flat class="my-5" v-if="false">
        <v-card-title>
          <h2>{{ $t('updates') }}</h2>
        </v-card-title>
        <v-card-text>
          <ul class="text--primary">
            <li>2021年XX月XX日: プロトタイプシステムを公開しました。</li>
          </ul>
        </v-card-text>
      </v-card>

      <br />

      <h3 class="mt-5 mb-10 text-center">{{ "関連機関／関連データベース・データセット" }}</h3>

      <div class="text-center mt-10">
        <p id="other_institute_link py-10">
          <a class="ma-2" href="https://www.nabunken.go.jp/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/nabunken_bana.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="http://www.hi.u-tokyo.ac.jp/index-j.html" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/hi_u_tokyo_bana.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="https://www.nijl.ac.jp/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/nijl_nama.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="https://www.ninjal.ac.jp/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/ninjal_bana.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="http://www.zinbun.kyoto-u.ac.jp/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/zinbun_kyoto-u_nama.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="http://www.hng-data.org/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/hng_bana.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="http://www2.ihp.sinica.edu.tw/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/ihp_bana.png" width="" height="60" alt=""></a>
          <a class="ma-2" href="https://ascdc.sinica.edu.tw/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/ascdc_bana.png" width="" height="60" alt=""></a>
        </p>

        <p id="other_db_link" class="my-10 py-10">
          <a class="ma-2" href="https://mokkanko.nabunken.go.jp/ja/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/mokkanko_banner.png" width="" height="67" alt="木簡庫"></a>
          <a class="ma-2" href="http://wwwap.hi.u-tokyo.ac.jp/ships/db.html" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/hi-u-tokyo_banner.png" width="" height="67" alt="東京大学史料編纂所データベース"></a>
          <a class="ma-2" href="https://lab.nijl.ac.jp/jikei/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/nijl_jikei_banner.png" width="" height="67" alt="国文研字形検索β"></a>
          <a class="ma-2" href="https://search.hng-data.org/" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/hng_tanji_banner.png" width="" height="67" alt="HNG単字検索"></a>
          <a class="ma-2" href="https://wcd-ihp.ascdc.sinica.edu.tw/woodslip" target="_blank"><img src="https://mojiportal.nabunken.go.jp/img/ihp_woodslip_banner.png" width="" height="67" alt="簡牘字典 史語所藏居延漢簡資料庫"></a>
        </p>
      </div>

    </v-container>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'
import axios from 'axios'

@Component({})
export default class about extends Vue {
  baseUrl: any = process.env.BASE_URL
  siteName: any = (this as any).$t(process.env.siteName)
  siteDesc: any = process.env.siteDesc
  top: any = process.env.top
  visualization: any = process.env.visualization

  items: any[] = []
  size: number = 0
  total: number = 0



  menu: any = [
    /*
    {
      label: 'くずし字データセットの一覧を見る（機械のための学習データ）',
      path: {
        name: 'book',
      },
      description: '',
      icon: 'mdi-view-list',
    },
    */
    {
      label: '文字種ごとのくずし字一覧を見る（人間のための学習データ）',
      path: {
        name: 'unicode',
      },
      description: '',
      icon: 'mdi-account',
    },
    /*
    {
      label: 'くずし字ジェネレータ',
      path: {
        name: 'generator',
      },
      description: '',
      icon: 'mdi-format-text',
    },
    */
    /*
    {
      label: this.$t('category'),
      path: {
        name: 'category',
      },
      description: '',
      icon: 'mdi-tag',
    },
    */
    /*
    {
      label: "現代地図",
      path: {
        name: 'map',
      },
      description: '',
      icon: 'mdi-map',
    },
    */
    /*
    {
      label: this.$t('list'),
      path: {
        name: 'collection',
      },
      description: '',
      icon: 'mdi-view-list',
    },
    {
      label: "水名一覧",
      path: {
        name: "river",
      },
      description: '',
      icon: 'mdi-water',
    },
    {
      label: this.$t('category'),
      path: {
        name: 'category',
      },
      description: '',
      icon: 'mdi-tag',
    },
    {
      label: "冊子画像",
      path: {
        name: "book",
      },
      description: '',
      icon: 'mdi-book',
    },
    */
    /*
    {
      label: "提供機能",
      href: "https://docs.google.com/document/d/1MpB4CfebygZE2C4VGUemjI0Pa7242go1Q_5tfMHxyCE/edit?usp=sharing",
      description: '',
      icon: 'mdi-cog',
    },
    */
    /*
    {
      label: this.$t('iiif_collection'),
      href: "https://www.kanzaki.com/works/2016/pub/image-annotator?u=https://static.toyobunko-lab.jp/suikeichuzu_data/iiif/collection/top.json",
      description: '',
      icon: 'mdi-image',
    },
    */
    /*
    {
      label: this.$t('NYPL可視化'),
      href: this.visualization+ '/pd/',
      description: '',
      img: this.baseUrl + "/img/pd_banner_magnified.png"
    },
    {
      label: this.$t('PixPlot'),
      href: this.visualization+ '/pp/',
      description: '',
      img: this.baseUrl + "/img/pixplot.png"
    },
    */
  ]

  async mounted() {
    const df = await axios.get(this.baseUrl + '/data/ana.json')
    const data = df.data

    const items = []

    //let allSize = 0
    //let allTotal = 0

    for (const key in data.map) {
      const item = data.map[key]

      let total = 0
      for (const e in item) {
        total += item[e]
      }
      items.push({
        label: key,
        size: Object.keys(item).length,
        total: total,
      })

      //allSize += Object.keys(item).length
      //allTotal += total
    }

    this.items = items

    this.size = data.size
    this.total = data.total
  }

  head() {
    const title = this.siteName
    return {
      titleTemplate: null,
      title,
    }
  }
}
</script>
