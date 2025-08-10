# 前提
- アナリティクスアカウントに対し、プロパティが紐付き、計測IDが付与される
- イベントに対しそれに紐ずくプロパティに具体的な値がセットされる


# 初期設定
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

2. GTMでカスタムイベントを設定
