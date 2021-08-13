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
        入力された文字列に対応するくずし字をランダムに返却します。
      </p>

      <v-text-field
        class="my-10"
        v-model="q"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        filled
        rounded
        background-color="grey lighten-2"
        clear-icon="mdi-close-circle"
        :clearable="true"
        @click:append="search"
        @keydown.enter="trigger"
      ></v-text-field>

      <template v-if="items.length > 0">

        <div class="text-center my-10">
          <template v-for="(item, key) in items">
            <img v-if="item.thumbnail_url" height="150px" :src="item.thumbnail_url"  :key="key"/>
          </template>
        </div>

        <v-simple-table>
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-center">
                </th>
                <th class="text-center">
                  ID
                </th>
                <th class="text-center">
                  Call Number
                </th>
                <th class="text-center">
                  Document
                </th>
                <th class="text-center">
                  Value
                </th>
                <th class="text-center">画像
                </th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item, key) in items" :key="key"
              >
                <td class="pa-2 text-center">{{ item.title.split("@")[0] }}</td>
                <td class="pa-2 text-center">{{ item.id }}</td>
                <td class="pa-2 text-center">{{ item.source.call_number.split("@")[0] }}</td>
                <td class="pa-2 text-center">{{ item.source.document.split("@")[0] }}</td>
                <td class="pa-2 text-center">{{ item.source.value.split("@")[0] }}</td>
                <td class="pa-2 text-center">
                  <a v-if="item.manifest_url" :href="'https://clioapi.hi.u-tokyo.ac.jp/mirador/?manifest=' +
                  item.manifest_url">
                  <img :src="baseUrl + '/img/icons/mirador3.svg'" width="30px"/>
                  </a>
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>

      </template>

    </v-container>
  </div>
</template>
<script lang="ts">
import { Vue, Component, Watch } from 'nuxt-property-decorator'
import axios from 'axios'

const randRange = (min: number, max: number) => Math.floor(Math.random() * (max - min + 1) + min);

@Component({})
export default class about extends Vue {
  title: string = 'くずし字ジェネレータ'

  baseUrl: any = process.env.BASE_URL

  q: any = ""

  items: any[] = []

  head() {
    const title = this.title
    return {
      title,
    }
  }

  trigger(event: any) {
    // 日本語入力中のEnterキー操作は無効にする
    if (event.keyCode !== 13) return
    this.search()
  }

  search(){

    const q = this.q

    this.$router.push(
      this.localePath({
        name: 'generator',
        query : {
          q
        },
      }),
      () => {},
      () => {}
    )
  }

  mounted(){
    this.q = this.$route.query.q
    this.init()
  }

  @Watch('$route')
  async init(){
    const q = this.q

    if(!q){
      return
    }

    const qs = q.split("")

    const items = []

    for(const e of qs){
      try {
        const res = await axios.get(this.baseUrl + '/data/list/'+e+'.json')
        const data = res.data


        const index = randRange(0, data.length - 1)

        items.push(data[index])
      } catch (error){
        items.push({
          title: e,
          source: {
            call_number: "",
            document: "",
            value: ""
          }
        })
      }
    }

    console.log(items)
 
    this.items = items
  }

  bh: any[] = [
    {
      text: this.$t('top'),
      disabled: false,
      to: this.localePath({ name: 'index' }),
      exact: true,
    },
    {
      text: this.title,
    },
  ]
}
</script>
