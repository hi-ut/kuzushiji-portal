<template>
  <div>
    <h3>データセットの統計情報</h3>

    <p>
      データセットに含まれる文字種を、頻度順文字種リストまたはコード順文字種リストで一覧できます。くずし字の一つ一つの文字の形の違いだけでなく、くずし字の元となる字母の違いによる異体字のバリエーションなど、実際の字形を画像で確認しながら、くずし字の学習などにご利用ください。文字をクリックすると字形一覧ページに移動します。文字の検索にはブラウザの検索機能を使って下さい。
    </p>

    <p>
      なおデータセット全体の統計情報については、くずし字データセット
      文字種（くずし字）一覧、また検索にはくずし字データベース検索（ひらがな（変体仮名）・カタカナ・漢字）をご利用下さい。
    </p>

    <p>
      文字種：{{ size.toLocaleString() }} ／ 文字数：{{
        total.toLocaleString()
      }}
    </p>
  </div>
</template>

<script lang="ts">
import { Prop, Component, Vue } from 'nuxt-property-decorator'

@Component
export default class ShareButtons extends Vue {
  @Prop({ required: true })
  size!: number

  @Prop({ required: true })
  total!: number
}
</script>
