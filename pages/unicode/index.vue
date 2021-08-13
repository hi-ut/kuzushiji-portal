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
        文字種：{{ items.length.toLocaleString() }} ／ 文字数：{{
          total.toLocaleString()
        }}
      </p>

      <h4 class="mt-10">文字種リスト</h4>

      <!--
      <v-simple-table class="mt-5">
        <template v-slot:default>
          <tbody>
            <tr v-for="(item, key) in items2" :key="key">
              <td class="py-2 px-4" v-for="(item2, key2) in item" :key="key2">
                <div>
                  <small>{{item2.code}}</small>
                  <h3>
                    <nuxt-link
                      :to="
                        localePath({
                          name: 'unicode-id',
                          params: {
                            id: item2.label,
                          },
                        })
                      "
                    >{{ item2.label }}
                    </nuxt-link>
                  </h3>
                  <div class="text-right">
                    <h4>{{item2.size.toLocaleString()}}</h4>
                    <small>{{item2.index}}位</small>
                  </div>
                </div>
               </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
      -->
      <div class="mt-5">
        <v-row v-for="(item, key) in items2" :key="key" dense>
          <v-col v-for="(item2, key2) in item" :key="key2" cols="4" sm="1">
            <v-card outlined>
              <div class="pa-2">
                <small>{{item2.code}}</small>
                <h3>
                  <nuxt-link
                    :to="
                      localePath({
                        name: 'unicode-id',
                        params: {
                          id: item2.label,
                        },
                      })
                    "
                  >{{ item2.label }}
                  </nuxt-link>
                </h3>
                <div class="text-right">
                  <h4>{{item2.size.toLocaleString()}} <small>件</small></h4>
                  <small>{{item2.index}}位</small>
                </div>
              </div>
            </v-card>
          </v-col>
        </v-row>
      </div>

      <!--

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

      -->

      <!-- <h3>くずし字データセットの書名一覧</h3> -->
    </v-container>
  </div>
</template>
<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'
import axios from 'axios'

@Component({})
export default class about extends Vue {
  title: string = '文字種一覧'

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

  items2: any[] = []

  async mounted() {
    const df = await axios.get(this.baseUrl + '/data/ana.json')
    const ana = df.data
    this.total = ana.total

    const res = await axios.get(this.baseUrl + '/data/list.json')
    const data = res.data

    data.sort(function (a: any, b: any) {
      if (a.size < b.size) return 1
      if (a.size > b.size) return -1
      return 0
    })

    let total = 0
    for (const item of data) {
      total += item.size
    }

    //this.total = total

    this.items = data

    let arr: any[] = []
    const items2: any[] = []
    for(let i = 0; i < data.length; i++){
      if (i % 12 === 0){
        arr = []
        items2.push(arr)
      }
      data[i].index = i+1
      arr.push(data[i])
    }

    this.items2 = items2
  }
}
</script>
