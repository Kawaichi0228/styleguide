# 目次

- [ディレクトリ構成 & アーキテクチャ](#directory-and-architecture)
- [Vue.js - パスの指定方法](#vue-path)
- [HTML - v-for](#html-v-for)
- [JavaScript - 宣言](#js-declaret)
- [JavaScript - export/import](#js-export-import)
- [JavaScript - 関数](#js-function)
- [JavaScript - オブジェクト](#js-object)
- [JavaScript - axios](#js-axios)
- [JavaScript - Vuex](#js-vuex)
- [JavaScript - モジュールファイルの定義方法](#js-module)
- [JavaScript(コンポーネントオプション) - props](#jsvue-props)
- [JavaScript(コンポーネントオプション) - methods](#jsvue-methods)
- [JavaScript(コンポーネントオプション) - data](#jsvue-data)
- [CSS - 基本的な考え方](#css-policy)
- [CSS - margin](#css-margin)
- [CSS - !important](#css-important)
- [CSS - SCSS の記法](#css-scss)
- [CSS - インデント](#css-indent)
- [CSS - 単位](#css-unit)
- [Gin - ハンドラー関数](#gin-handler-function)
- [JavaScript & Gin - ルート](#js-gin-root)
- [Gin - 使用例: ミドルウェア](#gin-middle-ware)

---

# コーディング規約 - Vue.js

## ディレクトリ構成 & アーキテクチャ

<a name="directory-and-architecture"></a>

- Parts & Pages & Globals パターンを採用する

  ※ただし、`pages` は `views` に名前を変更する

  > [参考] https://qiita.com/tockn/items/2ce68b99e0839df52200)

  - **layouts**
    
    src/router/index.tsにimportされるレイアウト

  - **views**

    src/router/index.tsにimportされるコンポーネント

  - **components**

    再利用可能な部品

## Vue.js - パスの指定方法

<a name="vue-path"></a>

- パス指定には、ルートディレクトリ(初期設定は src ディレクトリ) `@` を使う

  **[理由]** ファイルの場所変更時に修正しなくていいようにするため

```javascript
// good
import FooComponent from "@/components/FooComponent.vue";

// bad
import FooComponent from "./components/FooComponent.vue";
```

## Vue.js - 複数レイアウト

<a name="vue-layouts"></a>

- 複数レイアウトの実装には、高階コンポーネントを使用する。

  > [参考] https://www.m3tech.blog/entry/vue-hoc-layout

  ※公式の [「ネストされたルート」](https://v3.router.vuejs.org/ja/guide/essentials/nested-routes.html)は使用しない。

## HTML - v-for

<a name="html-v-for"></a>

- 区切り文字は `in` を使う。( `of` は使わない)

```html
<!-- good -->
<div v-for="item in list"></div>

<!-- bad -->
<div v-for="item of list"></div>
```

## JavaScript - 宣言

<a name="js-declare"></a>

- 宣言は `let` または `const` で行い、`var` は禁止とする。

  **[理由]** 変数の再定義を防止するため

```javascript
// good
let foo = "value";
const bar = "value";

// bad
var foo = "value"; // このfooは再定義ができてしまう
// var foo = "value2"; // 再定義してもエラーが出ない。
```

## JavaScript - export/import

<a name="js-export-import"></a>

- JavaScript ファイルを外部化(モジュールファイルとして)する場合は、`named export/import` を使う( `default export/import` は使わない)

```javascript
// good
export const fooExport = {
  foo: "bar",
};
...
import { fooExport } from "@/utils/foo-export";

// bad
export default {
  foo: "bar",
};
...
import fooExport from "@/utils/foo-export";
// import { default as fooExport } from "@/utils/foo-export"; // 上記の省略しない正式形

```

- `export` は変数・関数定義と同時に行う。ただし、ライブラリ(Vuex 等)の仕様上、定義後の `export` が必要な場合には許可する。
- また、`named export/import` におけるエイリアスは禁止とする。
  > [参考] https://engineering.linecorp.com/ja/blog/you-dont-need-default-export/

```javascript
// good
export const publicValue = "foo"; // 定義と同時にexport
const privateValue = "foo";

// bad
const publicValue = "foo";
export { publicValue as p }; // 定義後のexport & エイリアスは禁止
...
import { p as val } from "@/utils/foo-export"; // エイリアスは禁止
```

## JavaScript - 関数

<a name="js-function"></a>

- 定義には、アロー関数式を原則使用する。また、省略形は積極的に使う。

```javascript
// ***処理が1行の場合***
// good - アロー関数式 (function, return, {} を省略できる)
const getNumber = (num1, num2) => num1 * num2;

// bad - 純粋な関数リテラル (無名関数=関数名を省略できる)
const getNumber = function (num1, num2) {
  return num1 * num2;
};

// bad - functionを別定義
function getNumber(num1, num2) {
  return num1 * num2;
}
console.log(getNumber(2, 10));

// ***処理が2行以上の場合***
// good - アロー関数式
const getNumber = (num1, num2) => {
  const foo = num1 * num2;
  console.log(foo);
  return foo;
};

// ***引数が1つの場合***
// good
const getNumber = (num1) => num1 * 10;

// ***引数がない場合***
// good
const getNumber = () => 2 * 10;
```

## JavaScript - オブジェクト

<a name="js-object"></a>

- オブジェクトの Key と変数名が一致していれば省略できるが、原則は省略せずに記述すること。

```javascript
// good
const baseURL = `${baseDomain}/api/v1`;
export default axios.create({
  baseURL: baseURL,
});

// bad
const baseURL = `${baseDomain}/api/v1`;
export default axios.create({
  baseURL,
});
```

## JavaScript - モジュールファイルの定義方法

<a name="js-module"></a>

- モジュール化する際、`index.ts` <sup>(※1)</sup> を使用したほうが良い場合は原則利用すること。

  (※1) `from` でディレクトリを指定すると、そのフォルダ内に存在する `index.ts` が自動で読み込まれる機能

```javascript
// good
./src
└── router
    └── index.ts

// bad
./src
└── router
    └── router.ts
```

## JavaScript - axios

<a name="js-axios"></a>

### 通信処理のデザインパターン

- **Repository Factory パターン** を採用する

> [参考] https://qiita.com/07JP27/items/0923cbe3b6435c19d761

### 通信処理、例外処理、レスポンスデータの受取

> [参考] https://www.sukerou.com/2019/05/axios.html

- 通信処理は、以下の記法を採用する。

```javascript
// good
const res = await axios.post("/foo", { key: value }); // axios.<httpMethod>(<URI>, <json or EncodedUrl>)

// bad
const res = await axios({
  method: "post",
  url: "/foo",
  data: { key: value },
});
```

- 例外処理は、`await-catch` パターンを採用する。

```javascript
// good - await-catchパターン
const res = await axios.get("/foo").catch((err) => {
  return err.response;
});
if (res.status != 200) {
  // 例外発生時の処理
}

// bad - try-catchパターン
let res;
try {
  res = await axios.get("/foo");
} catch (err) {
  res = err.response;
}
if (res.status != 200) {
  // 例外発生時の処理
}
```

- レスポンスデータの受取は、`await` パターンを採用する。
- また、分割代入は行わないこと。

```javascript
// good - awaitパターン
const res = await axios.get("/foo")
console.log(res.data)

// bad - thenパターン
axios.get('/users').then(res => {
  ...
})

// bad - awaitパターン + 分割代入
const { data } = await axios.get('/users')
console.log(data)
```

## JavaScript - Vuex

<a name="js-vuex"></a>

### 通信処理

- 通信処理の記述は pages globals のみとする(parts は行わない)

  > [参考] https://qiita.com/tockn/items/2ce68b99e0839df52200)

### getters

- `state` の値の取得には、必ず `getter` を経由すること。

  **[理由]** `this.$store.state` を使うと、`mutation` を介さずに直接 `state` を変更できてしまうため。

  > [参考] https://uncle-javascript.com/vuex-getters

```javascript
// example - Vuex Module
const getters = {
  foo: (state: any) => state.foo,
};

// good
isFoo() {
  return this.$store.getters.foo == true;
},

// bad
isFoo() {
  return this.$store.state.foo == true;
},
```

### mutations

- `state` の値の更新には、必ず `mutations` を使って行うこと。**`mutations` 以外で値を更新することを禁止する。**

```javascript
// example
const mutations = {
  SET_TOKEN: (state, token) => {
    state.token = token;
  },
};
```

### actions

- 非同期処理、外部 API との通信処理を記述し、最終的に`mutations` を呼び出すために用いること。

```javascript
const actions = {
  setToken: ({ commit }: { commit: Commit }, token: boolean) =>
    commit("SET_TOKEN", token),
};
```

## JavaScript(コンポーネントオプション) - props

<a name="jsvue-props"></a>

- `type` 及び `required` は必ず指定すること。

```javascript
export default {
  // good - 型とrequiredの両方を指定している
  props: {
    foo: {
      type: String,
      required: true,
      default: "", // (任意)
    },
  },

  // bad - 型のみ指定
  props: {
    name: String,
    user: Object,
  },

  // bad - 一切指定していない
  props: ["name", "user"],
};
```

## JavaScript(コンポーネントオプション) - methods

<a name="jsvue-methods"></a>

- ES2015(ES6)で導入された**短縮形**を用いる

```javascript
export default {
  methods: {
    // good
    fooMethod(arg) {
      ...
    },

    // bad
    fooMethod: function(arg) {
      ...
    },
  },
};
```

## JavaScript(コンポーネントオプション) - data

<a name="jsvue-data"></a>

- ES2015(ES6)で導入された**短縮形**を用いる

```javascript
export default {
  // good
  data() {
    return {
      name: "太郎",
      age: 10,
    };
  },

  // bad
  data: function () {
    return {
      name: "太郎",
      age: 10,
    };
  },

  // bad
  data: {
    name: "太郎",
    age: 10,
  },
};
```

## CSS - 基本的な考え方

<a name="css-policy"></a>

- 原則的には scoped CSS + BEM 設計 を用いる。

  > Scoped CSS: コンポーネント内のみに CSS 有効範囲を"スコープ"(=適用させる)

  ```scss
  // 仕組みのsampleコード
  //※カスタムデータ属性を付与している
  <div data-v-5e5284cd class="title" > </div > div[data-v-5e5284cd] {
    color: red;
  }
  ```

- 命名規則には BEM の考え方を利用
- 上の親要素であるほど適用するスタイルは必要最小限にとどめる

## CSS - margin

<a name="css-margin"></a>

- 上下の要素間の隙間を空ける時は、 `margin-bottom` を適用する
- 左右の要素間の隙間を空ける時は、 `margin-right` を適用する
- 上下の要素を重ねる時は、 `margin-top` のネガティブマージン(マイナスのマージン)を用いる
- 左右の要素を重ねる時は、 `margin-left` でネガティブマージン(マイナスのマージン)を用いる

## CSS - !important

<a name="css-important"></a>

- [原則] 使用禁止
- [例外] ページ固有の CSS が、外部 CSS ライブラリ(bootstrap や正規化 CSS 等) を上書きする場合のみ使用許可

## CSS - SCSS の記法

<a name="css-scss"></a>

- ブロック内は 擬似要素 及び メディアクエリ のみ定義を許可する

- セレクタ Prefix のアンパサンド `&` (親セレクタ名参照) は擬似要素以外での使用を禁止する

  **[理由]** 可読性のため(ネスト多重化防止)

```scss
// Good
.foo-block {
  ...
  &:hover {
    ...
  }
  @include tab {
    ...
  }
}

.foo-block__bar-element {
  ...
}

// Bad
.foo-block {
  ...
  &__bar-element { //「.foo-block__bar-element」と出力される
    ...
  }
}

.foo-block:hover {
  ...
}
```

## CSS - インデント

<a name="css-indent"></a>

- Block 名ごとに 1 行空ける

```scss
.top {
  ...
}
.top__logo {
  ...
}
.top__title {
  ...
}
// 1行空ける //
.drawer {
  ...
}
.drawer__body {
  ...
}
```

## CSS - 単位

<a name="css-unit"></a>

- 単位は以下のとおり使い分けるものとする。

  > [参考] https://pengi-n.co.jp/blog/unit/

  ### **[絶対値]**

  #### px

  - boder などの可変させたくない要素

  ### **[相対値]**

  #### rem

  - フォントサイズ
  - height, width
  - margin, padding

  #### em

  - letter-spacing
  - 擬似要素との間隔

  #### %

  - 親要素にあたる包含的な container 要素
  - 画面幅いっぱいに表示させたい要素

  #### vw, vh

  (※vw: Viewport Width, vh: Viewport Height)

  - ヒーローイメージやモーダルウィンドウ など画面を占有して表示させる要素

## Gin - ハンドラー関数

<a name="gin-handler-function"></a>

- ハンドラー関数は戻り値を利用しない形式で作成する。**ただし、ミドルウェアの場合を除く。**

  **[理由]** 可読性のため(ネスト多重化防止)

```go
    // good - 関数の戻り値を利用しない
    func HelloWorld(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{
            "message": "hello world",
        })
    }
    func main() {
        // ~~~ 省略 ~~~
        r.GET("/hello", HelloWorld)
    }


    // bad - 関数の戻り値を利用する ※ミドルウェアの場合を除き使用しない
    func HelloWorld() gin.HandlerFunc {
        return func(c *gin.Context) {
            c.JSON(http.StatusOK, gin.H{
                "message": "hello world"
            })
        }
    }
    func main() {
        // ~~~ 省略 ~~~
        r.GET("/hello", HelloWorld())
    }
```

- ハンドラー関数はできる限り、外部関数化する。(関数の内容が短い場合は使用も可)

```go
    // 推奨
    r.GET("/hello", HelloWorld)

    // 非推奨
    r.GET("/hello", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{
            "message": "hello world",
        })
    })

    // 短い場合は可
    r.GET("/", func(c *gin.Context) {
        c.HTML(http.StatusOK, "web", nil)
    })
```

## JavaScript & Gin - ルート

<a name="js-gin-root"></a>

- 先頭のルートは省略しない(現在ディレクトリを示す `./` を含む)

  **[理由]** 見た瞬間にルートだと認識できる & 検索がしやすいため

### Golang(Gin)

```go
    // good
    r.StaticFile("/static/foo.png", "./static/foo.png")
    sr := r.Group("/hello")
    {
        sr.GET("/world", HelloWorld)
    }

    // bad
    r.StaticFile("static/foo.png", "static/foo.png")
    sr := r.Group("hello")
    {
        sr.GET("world", HelloWorld)
    }
```

### JavaScript(axios)

```javascript
// good
const res = await axios.get("/foo");

// bad
const res = await axios.get("foo");
```

## Gin - 使用例: ミドルウェア

<a name="gin-middle-ware"></a>

```go
	// -------------------------------------------------------------------------
	// ミドルウェアをリクエストメソッドに適用 ※必ず、適用させたいメソッドの"前"に記述すること
	// -------------------------------------------------------------------------
	// 全てのルーティングのリクエストメソッドに対して前後処理(ミドルウェア)を適用
	r.Use(FooCustomMiddleWare)

	// 特定のルーティングのリクエストメソッドに対して前後処理(ミドルウェア)を適用
	// - 特定のルーティングに適用したい場合
	r.GET("/middle-test1", FooCustomMiddleWare, FooHandlerFunc)

	// - 特定のルーティンググループにまとめて適用したい場合
	middletest := r.Group("/middle-test2")
	middletest.Use(FooCustomMiddleWare)
	{
		middletest.GET("/", FooHandlerFunc)
	}

    // -------------------------------------------------------------------------
    // カスタムミドルウェア
    // - ユーザが定義した、ルーティング処理(=ハンドラー関数)の前後に実行したい共通処理のこと
    // - エラー時でも必ず実行される！(URLが存在しない404エラー時などでも実行される)
    // -------------------------------------------------------------------------
    func FooCustomMiddleWare(c *gin.Context) {
        // c.String: ログ出力メソッド
        MSG_PREFIX := "[共通]"
        c.String(http.StatusOK, MSG_PREFIX+"Initialize-ミドルウェア処理 ハンドラー関数実行前の処理")
        c.String(http.StatusOK, "\n")
        c.Next() // ←このメソッドでハンドラー関数が実行される
        c.String(http.StatusOK, "\n")
        c.String(http.StatusOK, MSG_PREFIX+"Finalize-ミドルウェア処理 ハンドラー関数実行後の処理")
    }
```
