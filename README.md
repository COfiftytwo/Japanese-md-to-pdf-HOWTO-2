# mdファイル→PDF変換を自動化する方法２（丁寧解説）
Part1でmdファイルからPDFファイルに変換できたところまで行きました。<br>
このPart2では、Github上でmdファイルを更新するとPDFファイルを更新作成してくれるように自動化を行っていきます。<br>
<br> GitBashでPart1で作成した下のフォルダをCommit,pushコマンドでリモートに反映させる。
```
├── .git
├── node_modules
├── package-lock.json
├── package.json
├── README.pdf
└── README.md
```
最初に下のコマンドで変更のあるファイルすべてをadd。そしてコミット。
```
$ git add -A
$ git commit -m "1st commit"
```
次に下のコマンドでpushを行う。（※メインブランチ名はmainやmasterで人それぞれなので自分の使っているのを記述する。）
```
$ git push origin [自分の使ってるメインブランチ名]
```
この時GitBash画面がすごい勢いでpushしていくので最初はビビりますが、気にしなくても大丈夫です。
<br>
<br>
Githubの画面に移り、リモートリポジトリに下の画面のようにフォルダやファイルがあることを確認。
<br>※「.github/workflows」はなくて大丈夫です。後程解説します。
<img src="https://github.com/COfiftytwo/md-to-pdf-Automation-How2-inJapanese-Part2/blob/main/img/md-to-pdf-Automation-p2-img1.png" width="750px" height="284px"> 
<br>次に上の画面の右上ボタン「Add file」→「create new file」→「Name your file..」に「.github/workflows/AutoMtP.yaml」と打ちます。（yamlファイル名は自分の決めたファイル名でもいいです。）次にyamlファイルに下のコードをコピペしてください。この際、自分の使っているメインブランチが記述されているか確認。問題なければ「Commit changes」をクリック。
```
name: Convert Markdown to PDF

on:
  push:
    branches: [main] # 自分のメインブランチ名を記述

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      # 変換するmdファイルに日本語が含まれる場合は日本語フォントをインストール
      - name: Install Japanese font
        run: sudo apt install fonts-ipafont fonts-ipaexfont

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - name: npm install, build
        run: |
          npm ci
          npm run build --if-present          

      # カレントディレクトリ配下の.mdファイルを.pdfファイルへ変換
      - name: Convert markdown to pdf
        run: npx md-to-pdf ./*.md

      # 新規作成または更新されたPDFファイルを自動コミットする
      - name: Commit output pdf files
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: Update output pdf files
```
これで自動化の準備は完了です！
<br>最後に試しにGithub上でmdファイルを編集してコミットしてください。
<br>PDFの生成には少し時間がかかりますが、Actionsを確認すれば動いていることが分かります！
<img src="https://github.com/COfiftytwo/md-to-pdf-Automation-How2-inJapanese-Part2/blob/main/img/md-to-pdf-Automation-p2-img2.png" width="750px" height="135px"> 
<br>解説は以上になります。お疲れ様でした。<img src="https://github.com/COfiftytwo/md-to-pdf-Automation-How2-inJapanese-Part2/blob/main/img/giphy.gif" width="36px" height="36px"> 

<!--
コマンド一覧
```
$ git status //コミット可能な新規または変更のあるファイルを一覧で表示する。
$ git add -A //変更のあったファイルすべてがaddされる。
$ git commit -m "コミットの内容" //コミット
$ git push origin [メインブランチ]//プッシュ
```
-->
