<template>
  <div>
    <div class="mt-5">
      <!--
      <v-row v-for="(item, key) in items2" :key="key" dense>
        <v-col v-for="(item2, key2) in item" :key="key2" cols="4" sm="1">
          <v-card outlined>
            <div class="pa-2">
              <small>{{ item2.code }}</small>
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
                <h4>{{ item2.size.toLocaleString() }} <small>件</small></h4>
                <small>{{ item2.index }}位</small>
              </div>
            </div>
          </v-card>
        </v-col>
      </v-row>
      -->
      <v-row dense>
        <v-col v-for="(item2, key2) in items.slice(perPage * (page - 1), perPage * page)" :key="key2" cols="4" sm="1">
          <v-card outlined>
            <div class="pa-2">
              <small>{{ item2.code }}</small>
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
                <h4>{{ item2.size.toLocaleString() }} <small>件</small></h4>
                <small>{{ item2.index }}位</small>
              </div>
            </div>
          </v-card>
        </v-col>
      </v-row>

      <div class="text-center mt-10">
        <v-pagination
          v-model="page"
          :length="len"
          :total-visible="7"
        ></v-pagination>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Prop, Component, Vue, Watch } from 'nuxt-property-decorator'

@Component
export default class ShareButtons extends Vue {
  baseUrl: any = process.env.BASE_URL

  @Prop({ required: true })
  items!: any[]

  /*
  set items2(data) {
    return
  }
  */

  page: number = 1
  perPage: number = 300

  get len(): number {
    return Math.ceil(this.items.length / this.perPage)
  }

  @Watch('items')
  initPage() {
    this.page = 1
  }

  get items2(): any[] {
    const data = this.items

    let arr: any[] = []
    const items2: any[] = []
    for (let i = 0; i < data.length; i++) {
      if (i % 12 === 0) {
        arr = []
        items2.push(arr)
      }
      data[i].index = i + 1
      arr.push(data[i])
    }

    //this.items2 = items2
    return items2
  }
}
</script>
