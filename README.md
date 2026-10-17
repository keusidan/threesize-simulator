# 3サイズ シミュレーター

名前を入れると、ハッシュ値からバスト・ウエスト・ヒップ(各 0〜999)を算出し、いちばん近いキャラクターと判定(美・普・細・太・獣・ゴジラ…)を出すネタサイトです。

公開先: https://keusidan.github.io/threesize-simulator/

## 仕組み

- 名前を NFKC(Unicode 正規化形式 KC)で正規化し、SHA-256(Secure Hash Algorithm 256-bit)を計算して 3 サイズにします。同じ名前なら必ず同じ結果です。
- 判定は 20 代前半の平均(約 82 / 64 / 89)を基準に、全体の大きさ 5 段階 × 体型 7 種で決まります。
- 単一の `index.html` と `fonts/` だけで動き、外部サービスへの通信はありません(ビルド不要)。

## 共有ページ(`r/`)

X や LINE のプレビュー画像(サムネイル)を結果ごとに変えるため、判定(46 種類)ごとに共有ページ `r/<番号>.html` と画像 `r/<番号>.png`(番号は 0〜45)を用意しています。共有リンクはこのページを指し、開いた人は本体へ自動で移動します。

- プレビュー用のクローラは JavaScript を実行しないため、静的サイトで画像を変えるには、ページ自体を判定ごとに用意する必要があります。
- 番号は `index.html` の `judge()` が返す `id` と同じです。体型の表(`VERDICT_TABLE`)が「体型の並び順 × 段階」で 0〜34、人間サイズの判定(`HUMAN_VERDICTS`)がその続きの 35〜45 です。
- 画像は判定ごとに 1 枚です。名前や 3 サイズの数値は載りません。
- `r/` 以下は生成物です。判定を追加・変更したら再生成が必要で、番号がずれるときは共有ページも作り直します。

## セキュリティ

- 外部のスクリプトやサービスは使わず、入力した名前は `textContent` / `encodeURIComponent` だけで扱います(`innerHTML` に渡すのは固定文字列のみ)。
- GitHub Pages は HTTP ヘッダーを設定できないため、CSP(Content Security Policy)は `<meta http-equiv>` で指定しています。`index.html` は `script` と `style` をハッシュで許可し、通信(`connect-src`)は禁止、フォントは同じ origin のみです。`frame-ancestors` は meta では効かないので指定できません。
- **`index.html` の `<script>` か `<style>` の中身を変えたら、CSP のハッシュを更新してください。** 更新しないと、スクリプトかスタイルがブラウザに止められてページが動かなくなります。更新は、リポジトリのルートで次を実行します。

  ```sh
  node -e 'const fs=require("fs"),c=require("crypto");let s=fs.readFileSync("index.html","utf8");const h=t=>"sha256-"+c.createHash("sha256").update(t).digest("base64");const sc=s.match(/<script>([\s\S]*?)<\/script>/)[1],st=s.match(/<style>([\s\S]*?)<\/style>/)[1];s=s.replace(/script-src \x27sha256-[^\x27]*\x27/,`script-src \x27${h(sc)}\x27`).replace(/style-src \x27sha256-[^\x27]*\x27/,`style-src \x27${h(st)}\x27`);fs.writeFileSync("index.html",s)'
  ```

- `r/` の共有ページにも CSP を入れています(リダイレクト用の 1 行のスクリプトだけを、ハッシュで許可)。
- 名前の最大文字数(1000)は、入力欄と `analyze()` の両方で検証しています。

## フォント

書体は [Google Fonts](https://fonts.google.com/) から取得し、使う文字だけに subset して `fonts/` に同梱しています。すべて SIL Open Font License(OFL) 1.1 です。ライセンス全文は `fonts/FONT-LICENSES_*.txt` にあります。

## ライセンス

コードは [MIT License](LICENSE)。フォントは各書体のライセンスに従います。
