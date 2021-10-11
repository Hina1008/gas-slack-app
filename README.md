# Slack Appを使って、Google Calenderの予定をリマインドする

## 背景

incoming webhook等のカスタムインテグレーションは非推奨のため、Slack Appを使用してみる。その練習として今回は下のツールを作成を目的とする。

毎朝6時に、Google Calendarに入っている今日と明日予定を教えてくれるSlack Botを作成する。

## 目次

1. [開発環境](#開発環境)
   1. [初期環境](#初期環境)
   2. [必要な環境](#必要な環境)
   3. [開発をする際にインストールするツール](#開発をする際にインストールするツール)
2. GASをローカルで管理するための準備
   1. nodebrewのインストール
   2. Node.jsのインストール
   3. nodeが使えるように環境PATHを設定する
   4. claspのインストール
   5. GASプロジェクトの作成
   6. doPost関数の作成
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

### 開発をする際にインストールするツール

- nodebrew: v16.11.0
- npm@8.0.0
- clasp: 2.4.1

## GASをローカルで管理するための準備

### nodebrewをインストール

```zsh
brew install nodebrew
```

### Node.jsをインストール

インストールできるバージョンを確認

```zsh:
nodebrew ls-remote
```

出力結果

```zsh
v0.0.1    v0.0.2    v0.0.3    v0.0.4    v0.0.5    v0.0.6  

...

v16.0.0   v16.1.0   v16.2.0   v16.3.0   v16.4.0   v16.4.1   v16.4.2   v16.5.0
v16.6.0   v16.6.1   v16.6.2   v16.7.0   v16.8.0   v16.9.0   v16.9.1   v16.10.0
v16.11.0  

io@v1.0.0 io@v1.0.1 io@v1.0.2 io@v1.0.3 io@v1.0.4 io@v1.1.0 io@v1.2.0 io@v1.3.0
io@v1.4.1 io@v1.4.2 io@v1.4.3 io@v1.5.0 io@v1.5.1 io@v1.6.0 io@v1.6.1 io@v1.6.2
io@v1.6.3 io@v1.6.4 io@v1.7.1 io@v1.8.1 io@v1.8.2 io@v1.8.3 io@v1.8.4 

io@v2.0.0 io@v2.0.1 io@v2.0.2 io@v2.1.0 io@v2.2.0 io@v2.2.1 io@v2.3.0 io@v2.3.1
io@v2.3.2 io@v2.3.3 io@v2.3.4 io@v2.4.0 io@v2.5.0 

io@v3.0.0 io@v3.1.0 io@v3.2.0 io@v3.3.0 io@v3.3.1 
```

#### バージョンを指定してインストールする方法

1. 上記バージョンから指定してインストール

    ```zsh
    nodebrew install-binary {version}
    ```

   - 例

       ```zsh
       nodebrew install-binary v16.11.0
       ```

2. 最新バージョンをインストールする場合

    ```zsh
    nodebrew install-binary latest
    ```

3. 安定バージョンをインストールする場合

    ```zsh
    nodebrew install-binary stable
    ```

上記の際に `<homedir>/.nodebrew/src` が無いよとエラーが出力された場合はそれぞれのディレクトリを作成して、もう一度[Node.jsのインストール](#nodejsをインストール)を行う

```zsh
mkdir ~/.nodebrew
mkdir ~/.nodebrew/src
```

インストール完了後、インストールしたバージョンを有効にする

```zsh
nodebrew use {version}
```

- 例

    ```zsh
    nodebrew use v16.11.0
    ```

- バージョンの確認は、以下のコマンドを入力する

    ```zsh
    nodebrew list
    ```

### nodeが使えるように環境PATHを設定する

```zsh
echo 'export PATH=$HOME/.nodebrew/current/bin:$PATH' >> ~/.zprofile
```

設定後、ターミナルを再起動、もしくは以下のコマンドを実行

```zsh
source ~/.zprofile
```

### claspのインストール

```zsh
npm i @google/clasp -g
```