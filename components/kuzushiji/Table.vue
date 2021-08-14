<template>
  <div>
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
    -->

    <!-- 
      :search="search" -->
    <v-data-table
      :headers="headers"
      :items="items"
      :items-per-page="50"
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
  </div>
</template>

<script lang="ts">
import { Prop, Component, Vue } from 'nuxt-property-decorator'

@Component
export default class ShareButtons extends Vue {
  baseUrl: any = process.env.BASE_URL

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
      value: 'code',
    },
  ]

  @Prop({ required: true })
  items!: any[]

  //search: string = ''
}
</script>
