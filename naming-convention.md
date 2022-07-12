# 目次

- [フォルダ及びファイル名](#folder-and-files)
- [Vue.js コード内](#vuejs-in-code)

---

# 命名規則

## フォルダ及びファイル名

<a name="folder-and-files"></a>

- フォルダ名: `/kebab-case`
- .vue 以外のファイル名: `kebab-case.js` , `kebab-case.img`
- .vue ファイル名(コンポーネント名): `PascalCase.vue`

  - 必ず複数単語にする(HTML タグとの衝突防止)
  - 親子関係の場合、親の名前を prefix する
    または フォルダを作る

  ```
  // 親のコンポーネント名をPrefixする
  components/
  |- SearchButtonClear.vue
  |- SearchButtonRun.vue
  |- SearchInputQuery.vue
  |- SearchInputExcludeGlob.vue

  // または フォルダを作る
  components/
  |- search/
  |  |- ButtonClear.vue
  |  |- ButtonRun.vue
  |  |- InputQuery.vue
  |  |- InputExcludeGlob.vue
  ```

  **[理由]** インテリセンスが利用しやすい かつ 可読性向上のため

## Vue.js コード内

<a name="vuejs-in-code"></a>

> [参考サイト(Vue.js 公式)] https://jp.vuejs.org/v2/style-guide/index.html

- Props 名:

  - 渡す側: `kebab-case`
  - 受け取り側: `camelCase`

- イベント名: `kebab-case`

  ```html
  <template>
    <button @click="this.$emit('create-button-click')"></button>
  </template>
  ```

- コンポーネントを HTML で使用: `<自己終了形式 />`

  ```html
  <UserList />
  ```

- CSS のセレクタ名: BEM(Block Element Modifier) を採用する

  > [参考サイト] https://qiita.com/mrd-takahashi/items/07dc3b4bad027daa2884

  - Block と Element の区切り: `__`
  - Block or Element と Modifier の区切り: `--`
    > [※] `_` を使うパターンもあるが、今回は `--` を使う
  - Modifier の Key と Value の区切り: `_`
  - 2 つ以上の単語の区切り: `-`

  ```html
  <!-- HTML -->
  <section class="item">
    <nav class="item-nav">
      <!-- modifierのスタイルを適用したいときは、元のスタイル + modifierスタイルの2つを当てること -->
      <!-- block__element--key_value であらわしている。modifierは _ で繋ぐパターンもある -->
      <a class="item-nav__item item-nav__item--state_active" href="#">タブA</a>
      <a class="item-nav__item" href="#">タブB</a>
      <a class="item-nav__item" href="#">タブC</a>
    </nav>
    <div class="item__body item-box__body--state_active">…</div>
    <div class="item__body">…</div>
  </section>
  ```

  ```scss
  // CSS
  .item {…}

  .item-nav {…}
   .item-nav__item {…}
     .item-nav__item--state_active {…}

  .item__body {…}
   .item__body--state_active {
      …
     }
  ```
