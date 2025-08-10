# 前提
- アナリティクスアカウントに対し、プロパティが紐付き、計測IDが付与される
- イベントに対しそれに紐ずくプロパティに具体的な値がセットされる


# GA4初期設定
各ファイル内のheadタグ内に以下のようなコードを埋め込む
```
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-GP1PDRLYYN"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-GP1PDRLYYN');
</script>
```

自動収集イベント：設定不要で自動で収集(計測)されるイベント
例：セッションスタート・ユーザーエンゲージメント
拡張計測機能イベント：「測定機能の強化」をONにすることで収集されるイベント
例：ページビュー数・スクロール数・離脱クリック

このほか、カスタムイベントを設定することでボタン押下時のクリックイベントなどを収集できる
1. GA4でカスタムイベントを設定
```
<button class="bottun-1" onclick='gtag("event", "click_button", { link_text: "お申し込みはこちら", link_url: "https://hogehoge.jp/"})'>ボタン1</button>
```
このように各イベント発火時の処理にgtagの処理を組み込めば、GA4にそのイベントは送信される
ただ探索レポートで確認するためには、GA4管理画面で、そのカスタムイベントを定義する必要ある


GA4を設定すると、GTAGも自動で紐づく？ただあくまでGA4向けの最低限の設定？

# GTAG  初期設定
1. このコードは、次のようにページの <head> 内のなるべく上のほうに貼り付けてください。
```
<!-- Google Tag Manager -->
<script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-5GQXKLGD');</script>
<!-- End Google Tag Manager -->
```
2. 開始タグ <body> の直後にこのコードを次のように貼り付けてください。
```
<!-- Google Tag Manager (noscript) -->
<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-5GQXKLGD"
height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
<!-- End Google Tag Manager (noscript) -->
```

このとき、GA4へ連携するとき、同じイベントが複数計測される？
