## GA4とGTMの違い

- **GA4（Googleアナリティクス4）**  
  Googleが提供するアクセス解析ツール本体。Webサイトやアプリのユーザー行動を計測・分析できる。  
  ページビューやイベントなどのデータを直接Googleアナリティクスに送信する。

- **GTM（Googleタグマネージャー）**  
  サイトに設置する各種タグ（GA4、広告、カスタムイベントなど）を一元管理できるツール。  
  サイトのHTMLを直接編集せず、GTMの管理画面からタグの追加・変更・発火条件の設定ができる。  
  GTM自体はデータを保存しないが、GA4タグを設定することでGA4にデータを送信できる。

---

## 1. 通常のページ（静的HTMLやサーバーサイドレンダリング）

### GA4の導入方法

1. **GA4の計測IDを取得**（例: G-XXXXXXXXXX）
2. 各ページの`<head>`内に以下のコードを貼り付ける

    ```html
    <!-- Google tag (gtag.js) -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
    <script>
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
      gtag('config', 'G-XXXXXXXXXX');
    </script>
    ```

3. **自動収集イベント**や**拡張計測機能イベント**は、特別な設定なしで自動で計測される

4. **カスタムイベント**を計測したい場合は、イベント発生時に`gtag('event', ...)`を呼び出す

    ```html
    <button onclick='gtag("event", "click_button", { label: "サンプル" })'>ボタン</button>
    ```

---

### GTMの導入方法

1. **GTMコンテナIDを取得**（例: GTM-XXXXXXX）
2. `<head>`内のなるべく上にGTMのスニペットを貼り付ける

    ```html
    <!-- Google Tag Manager -->
    <script>...GTMのコード...</script>
    ```

3. `<body>`直後にnoscriptタグを貼り付ける

    ```html
    <!-- Google Tag Manager (noscript) -->
    <noscript><iframe src="..."></iframe></noscript>
    ```

4. GTMの管理画面でGA4タグやイベントタグを設定する  
   **GTMからGA4にデータを連携するには：**
   - GTM管理画面で「タグ」→「新規」→「Googleアナリティクス: GA4 計測」または「GA4イベント」を選択
   - GA4の「測定ID（G-XXXXXXXXXX）」を入力
   - トリガー（例: 全ページ、特定イベントなど）を設定
   - 公開して反映  
   これにより、GTM経由でGA4にデータが送信される

  GTMにおけるタグとは「どの条件のときに」「どのイベントを送信するか」を含む設定
  

---

### 注意点

- **gtag.jsとGTMを両方入れると、同じイベントが二重計測される場合がある**ので、どちらか一方に統一するのが推奨です。

---

## 2. SPA（シングルページアプリケーション）

### GA4の導入方法

- 基本的な設置方法は通常ページと同じですが、**SPAではページ遷移してもHTMLが再読み込みされない**ため、  
  ページ遷移ごとに手動でイベント送信が必要です。

#### 例：VueやReactなど

```js
// ルーター遷移ごとに
router.afterEach((to) => {
  window.gtag('event', 'page_view', {
    page_path: to.fullPath,
    page_title: document.title,
  });
});
```

- カスタムイベントも同様に、イベント発生時に`gtag('event', ...)`を呼ぶ

---

### GTMの導入方法

- スニペットの設置は通常ページと同じ

- **SPAの場合のページビュー計測（公式推奨手順）**  
  SPAではページ遷移時にHTMLが再読み込みされないため、GTMで正しくGA4のページビューを計測するには、  
  ページ遷移ごとに`dataLayer.push()`でカスタムイベント（例: `event: 'page_view'`）を送信し、  
  GTM側でこのイベントをトリガーにGA4タグを発火させる設定が必要です。

  （補足）
  ページ遷移時にブラウザのhistoryを更新するルーティングの場合、以下のデフォルト設定により、その変化をGAが検知し、page viewイベントを送信する。
  GA4 > 管理 > データストリーム > web > 「ブラウザの履歴イベントに基づくページの変更」

　ただしページ遷移時の初期化処理は行われない。そのためdataLayerを利用して付加情報を送信する作りの場合、そのデータが更新されないため、個別で考慮が必要な場合がある。
　考慮して個別でイベントを送信するようにした場合、上記デフォルト設定は二重計測の観点から外しておく必要がある。
　
　なお個人的な意見として、あくまでGTMはソースにタグを埋め込むことなく計測できるようにするためのものなので、アプリソースに以下のように記述することが得策とはあまり思えない。
　
#### 例：VueやReactなど

```js
// ルーター遷移ごとに
router.afterEach((to) => {
  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: 'page_view', // 公式推奨: 'page_view'
    page_path: to.fullPath,
    page_title: document.title,
  });
});
```

#### GTM側の設定手順（公式推奨）

1. **GA4タグの作成**  
   GTM管理画面で「タグ」→「新規」→「Googleアナリティクス: GA4 イベント」を選択  
   - 計測ID（G-XXXXXXXXXX）を入力
   - イベント名に「page_view」と入力
   - イベントパラメータに「page_path」「page_title」などを追加し、`{{Page Path}}`や`{{Page Title}}`の変数を割り当てる  
     例:  
     - パラメータ名: `page_path`、値: `{{Page Path}}`
     - パラメータ名: `page_title`、値: `{{Page Title}}`

2. **トリガーの設定**  
   - 「カスタムイベント」トリガーを作成し、イベント名に「page_view」と入力
   - このトリガーをGA4イベントタグに割り当てる

3. **公開して反映**

> 詳細: [公式ドキュメント](https://developers.google.com/analytics/devguides/collection/ga4/single-page-applications?implementation=event&hl=ja#tag-manager)

---

#### 注意点（公式より）

- SPAでは、**ページ遷移ごとに`dataLayer.push({ event: 'page_view', ... })`を必ず実装すること**
- GTMの「GA4設定」タグ（自動ページビュー送信）はSPAでは正しく動作しない場合があるため、  
  上記のように「GA4イベント」タグ＋カスタムイベントトリガーで明示的に送信するのが推奨
- `page_path`や`page_title`などのパラメータは、`dataLayer.push()`で渡した値をGTMの変数として参照できるように設定する

---

## まとめ表

|                | 通常ページ                      | SPA（シングルページ）                |
|----------------|-------------------------------|--------------------------------------|
| GA4設置        | headにgtag.js                  | headにgtag.js＋ページ遷移ごとに送信  |
| GTM設置        | head/bodyにGTMスニペット       | head/bodyにGTM＋ページ遷移ごとに送信 |
| PV自動計測     | 〇                             | △（拡張計測ONなら一部自動）          |
| カスタムイベント| gtag('event', ...)             | gtag('event', ...)                   |
| 二重計測防止   | どちらか一方に統一             | どちらか一方に統一                   |

---