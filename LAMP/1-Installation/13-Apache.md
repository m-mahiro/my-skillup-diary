ちょっと時間ない。
ざっくり書く。
裏取りしてないから、内容少し不安。
けど多分概ねあってる。



# Apacheのインストール

`cd /etc`
をしてから
`ls`
をして、その中にapache2の文字列がないことを確認してください。
この中に入っているのはUbuntuにインストールしているソフトウェアなどです。
`ls | grep apache2`
をして、何も帰ってこなければ正解。 

`sudo apt install -y apache2`
これでインストール完了

再び
`cd /etc`
をしてから
`ls`
もしくは
`ls | grep apache2`
apache2がちゃんとインストールされていることがわかります。

次に、Webサイトをブラウザから確認してみます。
`ip a`でもう一度IPアドレスを確認して。

Windowsでブラウザを開いて、`http://192.168.xx.xx`と入力してEnter
できた！！！

次に自作のhtmlファイルを作成してみましょう。
`pwd`コマンド（単に`pwd`です。present working directoryの略です。）で、ワークディレクトリが先ほど作成したディレクトリになっていることを確認して
```bash
touch test.html
```
を実行して、`ls`でファイルが作成されたことを確認してください。
ここでvimと呼ばれるものを使ってファイルを編集していきます。
Ubuntuインストール時に"Minimized"を選んだ人はインストールされていません。
```bash
sudo apt update
sudo apt install vim
```
インストールしたら、vimを使ってきます。
`vim test.html`
でvimを開きます。
ちなみに、vimは昔にあったvi(VIsual Editor)を模倣されて(iMitation)作られたものです。LinuxとUnixの関係に似てますね。viよりも良いエディタを作ることを目指いしていたので、途中からは"Vi iMprove"とされています。

vimの使い方は、キーボードしか受け付けないCUIの特性上少しややこしいものになっています。
`vim test.html`でvimを開いたら、まずInsertキーを押します。
すると、左に`---INSERT---`が出ます。もう一度押すと`---REPLACE---`と出ます。これらの意味はWordと同じです。入力モードの話ですね。
適当に文字を打ってください。
```html
<h1>Hello, world</h1>
<p>Welcome to my first Web Page!</p>
```
とでもしましょうか。
次に、保存します。
まずEscを押して入力モードからコマンドモードに移ります。
`:w`を押してEnterです。(write)
次に、vimから出ましょう。
`:q`です。(quite)
出れましたね。
これらは一度に`:wq`とすることもできます。
ブラウザでこのファイルにアクセスしてください。
できましたね。

気づきましたか？
先程表示させたDefaultPageはURLでファイル名を書いていませんでしたが、ちゃんと表示されました。
URLでファイルが指定されなかった場合は自動的にindex.htmlが表示されるようになっているのですね。

では、index.htmlの名前を変更してみましょう。
```bash
mv index.html default_page.html
```
mvはmoveです。mvコマンドはファイルの移動だけでなく、同じディレクトリ内で使えばファイル名の変更もできます。

これで、`https://192.168.xx.xxx`にアクセスしてください。
面白いでしょ？