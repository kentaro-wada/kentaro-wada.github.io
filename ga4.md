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

ボタン押下時
