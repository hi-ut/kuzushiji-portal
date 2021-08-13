<template>
  <div>
    <v-sheet color="grey lighten-2">
      <v-container fluid class="py-4">
        <v-breadcrumbs class="py-0" :items="bh">
          <template #divider>
            <v-icon>mdi-chevron-right</v-icon>
          </template>
        </v-breadcrumbs>
      </v-container>
    </v-sheet>
    <v-container class="my-5">
      <h2 class="mb-5">{{ title }}</h2>

      <!--
      <p>
        データセット全体の統計情報については、くずし字データセット
        文字種（くずし字）一覧、また検索にはくずし字データベース検索（ひらがな（変体仮名）・カタカナ・漢字）をご利用下さい。
      </p>

      <p>また変体仮名の情報については、Unicode変体仮名一覧もご利用下さい。</p>
      -->

      <div class="text-center">
        <v-tooltip bottom>
          <template v-slot:activator="{ on }">
            <v-btn icon class="ma-1" target="_blank" :href="jsonUrl" v-on="on"
              ><img :src="baseUrl + '/img/icons/json-logo.svg'" width="24px"
            /></v-btn>
          </template>
          <span>{{ 'JSON' }}</span>
        </v-tooltip>

        <span class="mx-2"></span>

        <v-menu offset-y :close-on-content-click="false">
          <template v-slot:activator="{ on }">
            <v-btn class="ma-1" color="primary" depressed icon v-on="on">
              <v-icon>mdi-comment-quote-outline</v-icon>
            </v-btn>
          </template>
          <v-card flat width="400px">
            <div class="pt-4 px-4">
              <h4 class="mb-2">{{ $t('citation') }}</h4>
              <p>{{ myText }}</p>
            </div>
            <v-card-actions>
              <v-spacer />
              <v-btn rounded color="primary" @click="writeToClipboard()">
                {{ $t('citation_copy') }}
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-menu>

        <ResultOption
          class="ma-1"
          :item="{
            label: title,
            url: url,
          }"
        />
      </div>

      <p class="mt-10">
        DelegateがTrueの文字について、上位最大 {{ thres }} 件を表示します。
      </p>

      <div class="mt-10" v-if="filter && queryId">
        <v-chip label color="primary" close @click="init()"
          >フィルタ: {{ filter }}</v-chip
        >
      </div>

      <div v-for="(value, key) in map" :key="key" class="my-10" :id="key">
        <h3 class="mt-10 mb-5">
          <!--
          <nuxt-link
            :to="
              localePath({
                name: 'book-id',
                params: { id: key },
                hash: '#' + id,
              })
            "
            >{{ value.label }}（{{ value.list.length }}）</nuxt-link
          >
          -->
          {{ value.label }}（Delegate:
          {{ value.delegate.toLocaleString() }}／Total:
          {{ value.size.toLocaleString() }}）
        </h3>
        <!-- <p>{{ key }}</p> -->
        <v-row dense>
          <v-col cols="3" md="1" v-for="(item, key2) in value.list" :key="key2">
            <a
              :href="
                'https://universalviewer.io/examples/uv/uv.html#?manifest=' +
                item.manifest
              "
              target="_blank"
            >
              <v-img
                max-height="150"
                contain
                style="height: 150px"
                class="grey"
                :src="item.image"
              ></v-img>
            </a>
          </v-col>
        </v-row>
      </div>

      <BarChart class="pt-10 my-10" :labels="labels" :datasets="datasets">
      </BarChart>
    </v-container>
  </div>
</template>
<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'
import axios from 'axios'
import ResultOption from '~/components/display/ResultOption.vue'

@Component({
  components: {
    ResultOption,
  },
})
export default class about extends Vue {
  id: any = this.$route.params.id
  title: string = '文字種「' + this.id + '」'

  baseUrl: any = process.env.BASE_URL

  url: any = this.baseUrl + '/unicode/' + this.id

  head() {
    const title = this.title
    return {
      title,
    }
  }

  thres: number = 12

  get myText() {
    const siteName: any = process.env.siteName
    const footer: any = process.env.footer
    if (this.$i18n.locale === 'en') {
      const attribution = this.$t(siteName) + ' - ' + this.$t(footer)
      //const attribution = provided by${(this as any).item.attribution}
      return `"${this.title}" ${attribution} (${this.url})`
    } else {
      const attribution = siteName + ' - ' + footer
      //const attribution = 「${(this as any).item.attribution}」収録
      return `『${this.title}』${attribution} (${this.url})`
    }
  }

  items: any = []

  bh: any[] = [
    {
      text: this.$t('top'),
      disabled: false,
      to: this.localePath({ name: 'index' }),
      exact: true,
    },
    {
      disabled: false,
      to: this.localePath({ name: 'unicode' }),
      text: '文字種一覧',
      exact: true,
    },
    {
      text: this.id,
    },
  ]

  total: number = 0

  map: any = {}

  filter: string = ''

  queryId: any = ''

  res: any[] = []

  labels: any[] = []
  datasets: any[] = []

  jsonUrl: any = ''

  async created() {
    const jsonUrl = this.baseUrl + '/api/unicode/' + this.id + '.json'
    this.jsonUrl = jsonUrl

    const res = await axios.get(jsonUrl)
    const data = res.data
    this.res = data

    const queryId = this.$route.query.id
    this.queryId = queryId

    this.main()
  }

  init() {
    this.queryId = ''
    this.main()
  }

  main() {
    const map: any = {}

    const data = this.res

    const queryId = this.queryId

    for (const item of data) {
      const source = item.source
      const creator = item.creator.split('@')[0]

      if (!map[creator]) {
        map[creator] = {
          label: creator,
          list: [],
          size: 0,
          delegate: 0,
        }
      }

      map[creator].size += 1

      const delegate = '' + item.delegate

      if (
        delegate === '1' ||
        delegate === 'true' ||
        creator === '東京大学史料編纂所'
      ) {
        const list = map[creator].list

        map[creator].delegate += 1

        if (list.length >= this.thres) {
          continue
        }

        list.push({
          id: item.identifier,
          image: item.thumbnail_url,
          manifest: item.manifest_url,
        })
      }
    }

    const labels = []
    const datasets = []
    for (const creator in map) {
      labels.push(creator)
      datasets.push(map[creator].size)
    }

    this.labels = labels
    this.datasets = [
      {
        data: datasets,
        label: '文字', //$t("item")
      },
    ]

    this.map = map
  }
}
</script>
