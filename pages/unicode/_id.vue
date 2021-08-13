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

      <p>
        データセット全体の統計情報については、くずし字データセット
        文字種（くずし字）一覧、また検索にはくずし字データベース検索（ひらがな（変体仮名）・カタカナ・漢字）をご利用下さい。
      </p>

      <p>また変体仮名の情報については、Unicode変体仮名一覧もご利用下さい。</p>

      <div class="mt-10" v-if="filter && queryId">
        <v-chip label color="primary" close @click="init()">フィルタ: {{filter}}</v-chip>
      </div>

      <div v-for="(value, key) in map" :key="key" class="my-10" :id="key">
        <h3>
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
        </h3>
        <p>{{ key }}</p>
        <v-row dense>
          <v-col cols="3" md="1" v-for="(item, key2) in value.list" :key="key2">
            <a
              :href="
                'https://clioapi.hi.u-tokyo.ac.jp/mirador/?manifest=' +
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
    </v-container>
  </div>
</template>
<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'
import axios from 'axios'

@Component({})
export default class about extends Vue {
  id: any = this.$route.params.id
  title: string = '「' + this.id + '」くずし字データセット'

  head() {
    const title = this.title
    return {
      title,
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

  baseUrl: any = process.env.BASE_URL

  total: number = 0

  map: any = {}

  filter: string = ""

  queryId: any = ""

  res: any[] = []

  async created() {
    const res = await axios.get(
      this.baseUrl + '/data/list/' + this.id + '.json'
    )
    const data = res.data
    this.res = data

   

    const queryId = this.$route.query.id
    this.queryId = queryId

    this.main()
  }

  init(){
    this.queryId = ''
    this.main()
  }

  main(){
    const map: any = {}

    const data = this.res

    const queryId = this.queryId

    for (const item of data) {
      const source = item.source
      const doc = source.value.split('@')[0]
      const cn = source.call_number.split('@')[0]

      if (queryId && cn != queryId) {
        continue
      }

      if(queryId){
        this.filter = doc
      }

      if (!map[cn]) {
        map[cn] = {
          label: doc,
          list: [],
        }
      }

      map[cn].list.push({
        id: item.identifier,
        image: item.thumbnail_url,
        manifest: item.manifest_url,
      })
    }

    this.map = map
  }
}
</script>
