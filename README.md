# Googleフォーム監視システム 利用マニュアル
 **概要**
このシステムは、Googleフォームを利用したオンラインテスト時に、受験者が
他のタブやアプリへ移動した回数と時間を記録し、Googleスプレッドシートへ自動保存  
するための監視システムです。

構成は以下の2ページです
管理者用ページ（create.html）
受験者用ページ（test.html）

## 1. 管理者用ページ（create.html）
🎯 目的
GoogleフォームのURLから、受験者へ配布する専用URLを自動生成します。

### 使い方
① 管理者ページを開く
例：
https://〇〇.github.io/〇〇/create.html

② GoogleフォームURLを入力
例：
https://docs.google.com/forms/d/e/xxxxxxxxxxxxxxxxxxxxxxxx/viewform

③ 「配布URL作成」を押す
フォームIDを抽出し、以下の形式で受験者用URLを生成します：

コード
https://〇〇.github.io/〇〇/test.html?id=xxxxxxxxxxxxxxxxxxxxxxxx
④ 「コピー」を押す
生成されたURLをコピーします。

⑤ 生徒へ配布
コピーしたURLを生徒へ送信します。

## 2. 受験者用ページ（test.html）
### 目的
Googleフォームを表示しながら、
離脱回数・離脱秒数をリアルタイム監視し、スプレッドシートへ自動記録 します。

### 画面構成
■ テスト画面
Googleフォームが iframe で表示されます。

■ 監視カウンター（右上）
初期状態：

離脱回数：0

離脱秒数：0 秒

氏名・学籍番号（開始後に表示）

### 離脱検知
以下の行動が「離脱」として検知されます。

他のタブへ移動

他のアプリを開く

ブラウザを最小化

ホーム画面へ戻る

他のウィンドウへ移動

## 3 離脱時の動作
受験者が画面から離れると、

離脱開始時刻を記録

### 復帰時の動作
受験者が画面へ戻ると、以下を実行します。

① 離脱回数を加算
例：
離脱回数：3

② 離脱秒数を加算
例：
離脱秒数：45 秒

③ 警告表示
コード
【警告】

テスト画面から離れました。

今回の離脱時間：10 秒
累計離脱時間：45 秒
離脱回数：3 回
④ カウンター背景を赤色に変更
離脱回数が 1 回以上 → 赤色表示

## 検知方式
visibilitychange（メイン機能）
以下を検知します。

タブ移動
アプリ切り替え
ホーム画面移動
ブラウザ最小化
離脱回数・離脱時間の計算を担当します。

## 対応環境

### 対応OS
Windows
macOS
iPadOS
iOS
Android

### 対応ブラウザ
Google Chrome
Microsoft Edge
Mozilla Firefox
Safari

### 注意事項
ブラウザの制限上、以下は検知できません。
別端末での検索
別PCの利用
デュアルモニターの別画面利用
一部の画面分割操作

本システムは 完全なカンニング防止ではなく、離脱状況を記録する補助システム として利用してください。


## 3. Googleスプレッドシートへの自動記録（Apps Script連携）
離脱が発生するたびに、以下の情報が自動で記録されます。

氏名
学籍番号
離脱回数
離脱秒数
OS
ブラウザ
テスト開始時刻
テスト終了時刻（離脱時 or ページ閉じる時）

## 4. Apps Script の設定方法
① スプレッドシートを作成
1行目を以下にします：

日時  氏名  学籍番号    離脱回数    離脱秒数    OS  ブラウザ    開始時刻    終了時刻

② Apps Script を作成
スプレッドシート → 拡張機能 → Apps Script

以下を貼り付け：

js
function doPost(e) {

  const sheet =
    SpreadsheetApp.getActiveSpreadsheet()
      .getSheetByName("シート1");

  const data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    new Date(),
    data.name,
    data.studentId,
    data.leaveCount,
    data.leaveSeconds,
    data.os,
    data.browser,
    data.startTime,
    data.endTime
  ]);

  return ContentService
    .createTextOutput("OK");
}

③ 公開設定
デプロイ → 新しいデプロイ

種類：ウェブアプリ
次のユーザーとして実行：自分
アクセス権：全員

URL をコピー

④ test.html に URL を貼る
js
const APPS_SCRIPT_URL =
    "https://script.google.com/macros/s/あなたのURL/exec";

