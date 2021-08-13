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

      <h3>データセットの統計情報</h3>

      <p>
        データセットに含まれる文字種を、頻度順文字種リストまたはコード順文字種リストで一覧できます。くずし字の一つ一つの文字の形の違いだけでなく、くずし字の元となる字母の違いによる異体字のバリエーションなど、実際の字形を画像で確認しながら、くずし字の学習などにご利用ください。文字をクリックすると字形一覧ページに移動します。文字の検索にはブラウザの検索機能を使って下さい。
      </p>

      <p>
        なおデータセット全体の統計情報については、くずし字データセット
        文字種（くずし字）一覧、また検索にはくずし字データベース検索（ひらがな（変体仮名）・カタカナ・漢字）をご利用下さい。
      </p>

      <p>
        文字種：{{ items.length.toLocaleString() }} ／ 文字数：{{
          total.toLocaleString()
        }}
      </p>

      <h4 class="mt-10">文字種リスト</h4>

      <v-text-field
        class="my-10"
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        filled
        rounded
        background-color="grey lighten-2"
        clear-icon="mdi-close-circle"
        :clearable="true"
      ></v-text-field>

      <v-data-table
        :headers="headers"
        :items="items"
        :items-per-page="50"
        :search="search"
      >
        <template v-slot:item.label="{ item }">
          <nuxt-link
            :to="
              localePath({
                name: 'unicode-id',
                params: {
                  id: item.label,
                },
              })
            "
          >
            {{ item.label }}
          </nuxt-link>
        </template>
      </v-data-table>

      <!-- <h3>くずし字データセットの書名一覧</h3> -->
    </v-container>
  </div>
</template>
<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'
import axios from 'axios'

@Component({})
export default class about extends Vue {
  title: string = 'くずし字データセット 文字種（くずし字）一覧'

  search: string = ''

  head() {
    const title = this.title
    return {
      title,
    }
  }

  items: any = []

  headers: any[] = [
    {
      text: '文字種',
      value: 'label',
    },
    {
      text: '出現回数',
      value: 'size',
    },
    {
      text: 'コード',
      value: 'unicode',
    },
  ]

  bh: any[] = [
    {
      text: this.$t('top'),
      disabled: false,
      to: this.localePath({ name: 'index' }),
      exact: true,
    },
    {
      text: '文字種一覧',
    },
  ]

  baseUrl: any = process.env.BASE_URL

  total: number = 0

  async mounted() {
    const res = await axios.get(this.baseUrl + '/data/001_list.json')
    const data = res.data
    console.log(data)

    data.sort(function (a: any, b: any) {
      if (a.size < b.size) return 1
      if (a.size > b.size) return -1
      return 0
    })

    let total = 0
    for (const item of data) {
      total += item.size
    }

    this.total = total

    this.items = data
  }
}
</script>
