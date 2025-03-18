パスワードの追加・管理に関するコマンドをご説明します：

### 新しいユーザーを追加する場合
```bash
sudo htpasswd /etc/apache2/.htpasswd 新しいユーザー名
```

### よく使うオプション
- `-c`: 新規ファイル作成（**注意**: 既存のファイルは上書きされます）
- `-b`: コマンドラインでパスワードを指定
- `-D`: ユーザーの削除

### 使用例
```bash
# ユーザーの追加（対話式でパスワード入力）
sudo htpasswd /etc/apache2/.htpasswd username

# パスワードを直接指定してユーザー追加
sudo htpasswd -b /etc/apache2/.htpasswd username password

# ユーザーの削除
sudo htpasswd -D /etc/apache2/.htpasswd username
```

### パスワードファイルの内容確認
```bash
cat /etc/apache2/.htpasswd
```

注意：初回作成時以外は `-c` オプションを使用しないようご注意ください。使用すると既存のユーザー情報が全て消えてしまいます。

---

