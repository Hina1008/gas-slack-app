# Slack Appを使って、Google Calenderの予定をリマインドする

## 背景

incoming webhook等のカスタムインテグレーションは非推奨のため、Slack Appの練習として今回は下のツールを作成を目的とする。

毎朝6時に、Google Calendarに入っている今日と明日予定を教えてくれるSlack Botを作成する。

## 目次

1. 開発環境
   1. 初期環境
   2. 必要な環境
   3. 開発をする際にインストールするツール
2. GASをローカルで管理するための準備
   1. nodebrewのインストール
   2. npmのインストール
   3. claspのインストール
   4. GASプロジェクトの作成
   5. doPost関数の作成
3. GCPプロジェクトとGASを紐付ける
   1. 同意画面を作成
   2. OAuth クライアント ID の作成
      1. デスクトップアプリを作成
      2. 認証情報の入ったjsonファイルをダウンロード
   3. ライブラリの有効化
4. Slack Appの作成
   1. Event Subscriptionsを設定
   2. OAuth & Permissionsを設定
   3. BotのDisplay Nameを設定
   4. workspaceに作成したアプリをインストール
   5. トークンを取得

## 開発環境

2021/10/11現在

### 初期環境

- MacOS Big Sur ver:11.5.2
- Homebrew 3.2.13

### 必要な環境

- Google アカウント
- Google Driveの使用権限
- Google Apps Scriptの使用権限
  - Webアプリとしてデプロイする際に、アクセス権限を **【全員】** にできる環境
- Google Calendarの使用権限

#### ローカルでGASを実行したい場合 (上記+)

- Google Cloud Platformのアカウント

### 開発をする際に入れたツール等

- nodebrew: v16.11.0
- npm@8.0.0
- clasp: 2.4.1

