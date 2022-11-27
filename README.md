# live_info_api_client_py

## Dependencies

- Python 3.10

```
python3 -m venv venv
source venv/bin/activate

pip3 install -r requirements.txt
```

## Usage

### ニコニコ生放送 `live.nicovideo.jp`

```shell
# 番組 （ 月刊ニコニコインフォチャンネル https://live.nicovideo.jp/watch/lv339313375 ）
python3 main.py -s nicolive "lv339313375"

# コミュニティ （ ニコニコ動画プレミアムアワード https://com.nicovideo.jp/community/co5683564 ）
python3 main.py -s nicolive "co5683564"

# ユーザー （ ニコニコプレミアムDAY https://www.nicovideo.jp/user/123430062 ）
python3 main.py -s nicolive "user/123430062"

# ニコニコチャンネル （ウェザーニュースチャンネル https://ch.nicovideo.jp/weathernews ）
python3 main.py -s nicolive "ch1072"
```

#### 期待される挙動と既知の問題

放送中の番組がある場合、その番組を返します。

最後に放送した番組がある場合、その番組を返します。
番組が放送中かどうか判定するには、放送開始時間（`start_date`）と放送終了時間（`end_date`）および現在時刻が利用できます。

番組ID（`lv*`）ではなく、コミュニティID（`co*`）やユーザID（`user/*`）、ニコニコチャンネルID（`ch*`）を渡した場合、`https://live.nicovideo.jp/watch/*`に各IDを設定したときと同じ挙動をします。

既知の問題として、ニコニコ公式チャンネルの[月刊ニコニコインフォチャンネル](https://ch.nicovideo.jp/weekly-niconico-info)が放送中でないときに上記操作をしたとき、番組が存在しない（`not_found`）扱いになることが確認されています。
これはニコニコまたは当該チャンネルの仕様として、修正は考えていません。


```shell
# Return not_found (at least not-onair status)

# ニコニコチャンネル （月刊ニコニコインフォチャンネル https://ch.nicovideo.jp/weekly-niconico-info ）
python3 main.py -s nicolive "ch2646073"
```

## API研究

以下、未実装または実装予定のない内容が含まれます。

### ニコニコ生放送 `live.nicovideo.jp`

- `https://com.nicovideo.jp/api/v1/communities/{community_id}/lives/onair.json`
  - コミュニティで放送中の番組を返す（コミュニティ個別ページの放送Alert表示用）
  - `{community_id}`には、コミュニティID`co*`の数値部分（`*`）が入ります

### ニコニコチャンネルプラス `nicochannel.jp`

- `https://nfc-api.nicochannel.jp/fc/fanclub_sites/{fanclub_site_id}/live_pages?page={page}&live_type={live_type}&per_page={per_page}`
  - `https://nicochannel.jp/{channel_slug}/lives`で表示される生放送番組一覧
  - `fanclub_site_id`: `channel_slug`とは異なる数値ID
  - `live_type`
    - `1`(CURRENT): 放送中
    - `2`(SCHEDULED): 放送予定
    - `3`(FINISHED): 過去の放送（すべて）
    - `4`(ARCHIVED): 過去の放送（生放送アーカイブ）
      - 「すべて」と「生放送アーカイブ」の違い: 調査中（字面から、アーカイブが残っていない放送と残っている放送の区別？）
  - `page`
    - `1`始まり
  - `per_page`
    - 初期値 `live_type=1`: `10`
    - 初期値 `live_type=2`: `6`
    - 初期値 `live_type=3`: `8`
    - 初期値 `live_type=4`: `8`
