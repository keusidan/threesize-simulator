# 3サイズ シミュレーター

名前を入れると、ハッシュ値からバスト・ウエスト・ヒップ(各 0〜999)を算出し、いちばん近いキャラクターと判定(美・普・細・太・獣・ゴジラ…)を出すネタサイトです。

公開先: https://keusidan.github.io/threesize-simulator/

## 仕組み

- 名前を NFKC(Unicode 正規化形式 KC)で正規化し、SHA-256(Secure Hash Algorithm 256-bit)を計算して 3 サイズにします。同じ名前なら必ず同じ結果です。
- 判定は 20 代前半の平均(約 82 / 64 / 89)を基準に、全体の大きさ 5 段階 × 体型 7 種で決まります。
- 単一の `index.html` と `fonts/` だけで動き、外部サービスへの通信はありません(ビルド不要)。

## フォント

書体は [Google Fonts](https://fonts.google.com/) から取得し、使う文字だけに subset して `fonts/` に同梱しています。すべて SIL Open Font License(OFL) 1.1 です。ライセンス全文は `fonts/FONT-LICENSES_*.txt` にあります。

## ライセンス

コードは [MIT License](LICENSE)。フォントは各書体のライセンスに従います。
