# 問1

あなたの会社では現在、オンライン万歩計サービスを開発中です。
これは、スマートフォンなどのデバイスで計測された歩行アクティビティーを Web の画面で確認できるというサービスです。

構成要素は次の3つです。

- デバイス: アクティビティーの計測から10分ごとの集計までを行い、その集計結果を API 経由でサーバーに送信する
- サーバー: データの永続化とデバイスで行われない集計処理を行う。データの読み取り・書き込みはともに API 経由で行われる
- Web 画面: API 経由でサーバーから取得したデータを描画する（集計など一切の計算を行わない）

```text
            POST                    GET
            /activities             /activities
┌──────────┐            ┌──────────┐            ┌──────────┐
│  Device  │───────────▷│  Server  │◁───────────│  Web UI  │
└──────────┘            └──────────┘            └──────────┘
```

`POST /activities` API には、デバイスから下記のような JSON が10分ごとに送られてきます。

```json
{
  "interval": "PT10M",
  "activities": [
    {
      "timestamp": "2020-01-25T01:40:00Z",
      "value": 143
    }
  ]
}
```

```json
{
  "interval": "PT10M",
  "activities": [
    {
      "timestamp": "2020-01-25T01:50:00Z",
      "value": 182
    }
  ]
}
```

あなたは、この API の開発を前任者から引き継ぐことになりました。
API の仕様が [`api-spec.md`](api-spec.md) に、前任者の既存実装が [`api.py`](api.py) にあります。

既存実装は、デバイスからのリクエストが10分ごとに来るという理想的な状況であれば仕様通り動作します。

しかし、現実の環境は不確かです。
たとえばネットワーク障害などに起因するリトライやリプレイ攻撃によって、リクエストが重複したり順序が入れ替わったりします。
またデバイスがオフラインの間に計測されたデータは、いったんデバイス内にバッファーされてオンラインになった段階でまとめて送られてきたりします。

## 問題

上記に述べられているような「現実的な状況」の下でも正しく動作する API を実装してください。

なお、既存実装をそのまま修正しても、あるいはスクラッチから書きなおしても構いません。

### 前提

簡単のため、下記の前提のもと実装して構いません。

- 仕様に従わないデータは来ない
    - 不正なデータ形式に対するエラーハンドリング等はなくても構わない
- デバイスは1台しかなく、認証等も考えない
- サーバーは1プロセスだけ起動している状況のみを考えればよく、プロセスのシャットダウン・再起動を考慮する必要はない
    - データを外部に永続化する必要はない
- すべてのリクエストが直列に来ると考えて良い
    - レスポンスを返すまで新しいリクエストは来ない
- 総リクエスト数は最大1000件、1リクエストあたりの activities の数は最大20件
- リクエストは localhost から HTTP で来る
    - HTTPS ハンドリングを考える必要はない

一方で、採点の実行時には下記の制限があります。

| 項目 | 制限事項 |
|:----|:----|
| CPU | 2 vCPU |
| メモリ | 1GB |
| ストレージ | 1GB |
| ネットワーク | インターネット接続なし |
| タイムアウト | 個別のリクエストごとに1秒、全体で30秒 |

## 提出方法

[`run.sh`](run.sh) を引数無しで実行したときに、ポート 8080 でサーバーが起動するように実装し、関連するファイルをすべて GitHub にプッシュしてください。

なお `run.sh` は採点の中で複数回実行されることがあります。
実行ファイルのコンパイルなど、採点ごとに1回だけ実行したい処理がある場合は [`build.sh`](build.sh) に実装してください。

利用できる言語・ライブラリや CPU などの制限はリポジトリルートの [`README.md`](../../README.md) を参照してください。

## フィードバック

採点時に以下のフィードバック文のいずれかが返されます。

- `採点が正常に完了しました`
    - 実装されたプログラムが正常に稼働し採点が完了したことを表します
    - **実装内容が正しいとは限らない** ので注意してください（実装内容の正誤はフィードバックには含まれません）
- `ビルドに失敗しました`
    - プログラムのビルドに失敗したことを表します
    - `build.sh` の内容が正しいことを確認してください
- `メモリ使用量が制限を超過しました`
    - メモリ使用量が制限を超えたことを表します
- `採点が時間内に完了しませんでした`
    - 全体の処理時間が制限を超えたことを表します
- `API サーバーへの接続がタイムアウトしました`
    - 個別のリクエストが connection timeout で制限を超えたことを表します
- `API サーバーからの読み込みがタイムアウトしました`
    - 個別のリクエストが read timeout で制限を超えたことを表します
- `API サーバーへの接続に失敗しました（<exception_name>）`
    - プログラムコードおよび `run.sh` の内容が正しいことを確認してください
- `API サーバーが <http_status> HTTP エラーを返しました`
    - API が HTTP エラーを返したことを表します（採点システムは、仕様上に反したリクエストは行いません）
    - プログラムコードの内容が正しいことを確認してください
- `API サーバーからのレスポンスの形式が不正です`
    - 実装された API からのレスポンスの I/F 形式が仕様と異なることを表します
    - プログラムコードおよび `run.sh` の内容が正しいことを確認してください
- `サンプルのままの提出です`
    - 用意されたサンプルコードや解答例がそのまま提出されたことを表します
    - なおこのフィードバックは Q-NetworkKnowledge-Basic 全体で共通であり、no1 か no2 のいずれか片方でも変更を加えていればこのフィードバックは返されません
- `解答ファイルが提出されていません`
    - `run.sh`が提出されていないことを表します

また、下記の補足が付記されることがあります。

- `採点の途中で run.sh がステータス <status> で終了してしまいました`
    - `run.sh` が、採点システムによるシャットダウンよりも先に終了してしまったことを表します
    - プログラムコードおよび `run.sh` の内容が正しいことを確認してください

その他、採点システム上の問題が生じた場合は下記のフィードバック文が返されます。お問い合わせ先にご連絡ください。

- `採点システムにエラーが発生しました`
  - システムのエラーです。お手数ですが本スキルチェックの案内メールに記載のお問い合わせ先にご連絡ください。

