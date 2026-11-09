# BlueLink Hub

Bluetooth機器の **探索・解析・通信・遠隔操作** をまとめた、GitHub Pages向けの静的Webアプリです。

- HTML / CSS / JavaScriptのみ
- GAS・Googleスプレッドシート・独自バックエンドなし
- PWA対応（Service Worker / Web App Manifest）
- BLE GATTのサービス列挙、特性の読み取り・通知・書き込み
- 受信データのHEX/UTF-8表示、先頭1バイトの簡易グラフ、CSV/JSONログ出力
- WebRTC DataChannelによるブラウザ間テキスト通信
- 遠隔BLE書き込みリクエスト（受信側で確認・承認してから実行）

## GitHub Pagesで公開する

1. リポジトリの **Settings → Pages** を開きます。
2. Build and deployment の Source を **GitHub Actions** にします。
3. `.github/workflows/pages.yml` が実行されると、GitHub Pagesに公開されます。
4. 公開されたHTTPS URLを対応ブラウザで開きます。

## BLEの使い方

1. Web Bluetooth対応ブラウザをHTTPSで開きます。
2. 「機器探索」→「Bluetooth機器を選ぶ」を押します。
3. OSの選択画面で接続対象を選びます。
4. 公開されたGATTサービス・特性を確認し、read / notify / writeを実行します。
5. 独自サービスを使う機器では、接続前にサービスUUIDを「追加サービスUUID」に入力して選択し直してください。

サービス・特性の書き込みは機器ごとに形式が異なります。仕様が分からない値をむやみに書き込まないでください。

## 遠隔通信の使い方

サーバーを持たないため、手動でSDPコードを交換します。

- 接続を開始する端末：「接続コードを作成」→ Offerを相手へ送る。
- 接続を受ける端末：Offerを貼り付けて「Offerを受信してAnswer作成」→ Answerを返す。
- 開始側：Answerを貼り付けて「Answerを適用して接続」。
- 接続後はテキスト通信とBLE書き込みリクエストを利用できます。

STUNサーバーを使って直接接続経路を探しますが、TURN中継は含まれません。ネットワークやNATの条件により接続できない場合があります。Offer/Answerコードは信頼できる相手とだけ共有してください。

## 重要な制約

- Web Bluetoothは主にBLE GATT向けです。従来型Bluetooth Classicの汎用チャットではありません。
- ブラウザのセキュリティ上、周辺の全Bluetooth機器を無断でスキャンすることはできません。ユーザーが選択した機器だけに接続します。
- MACアドレス、正確な距離、RSSIの連続スキャンは取得できません。
- サービスUUIDは許可リストが必要です。独自機器ではUUIDの事前指定が必要になる場合があります。
- iOS / iPadOSのSafariはWeb Bluetoothに対応していないため、iPhone/iPadから直接BLE機器へ接続することはできません。画面・ログ・WebRTC機能は利用可能な範囲で使えます。
- 遠隔操作のリクエストは自動実行されず、BLE機器に接続している側が確認・承認する必要があります。
- ブラウザを閉じている間の常時接続やバックグラウンド監視は行いません。
- 履歴は利用中のブラウザのlocalStorageに保存され、クラウド同期されません。

## セキュリティ

BLE書き込みは機器に予期しない動作を起こす場合があります。機器仕様を理解したうえで操作してください。遠隔の書き込み要求も、対象UUIDと内容を確認してから承認してください。WebRTC通信は暗号化されますが、手動で渡した接続コードだけでは相手の本人確認を保証しません。
