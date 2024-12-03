このページも時間内からざっくり書きます。
今は、コピペ元程度に考えといてください。

# MySQL
RDBMSの一つでしたね！

`ls /etc`でmysqlが入っていないことを確認してみましょう。

`sudo apt install -y mysql-server`
これもサーバなのですね！

`ls /etc`で、sqlの表示が増えていることを体験してください。

`sudo mysql -u root -p`で対話モードに入ります。
`-u ユーザ名`はで、`-p`でパスワードを要求できます。
まだSQLにログインするようのユーザを作っていないので、rootでログインしましょう。
SQL(文)を入力してみましょう。


データベースを作成します。
```sql
CREATE DATABASE my_database;
```
データベースの一覧を表示します。

```sql
SHOW DATABASES;
```
データベースを選択します。
```sql
USE mysql;
```
大文字小文字を区別しません。
文末は必ず`;`です。
次に、選択されたデータベースの中にあるテーブルの一覧を見ます。
```sql
SHOW TABLES;
```
何も表示されません。
まだ作っていませんからね。
```sql
USE mysql;
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
これでテーブルが作成されました。確認してみて下さい。
次にレコード(行)を挿入します。
```sql
INSERT INTO users (username, email) VALUES ('john_doe', 'john@example.com');
```
取得します。
```sql
SELECT * FROM users;
```
一度に複数挿入できます。
```sql
INSERT INTO users (username, email) 
VALUES 
    ('Bob', 'bob@example.com'),
    ('Charlie', 'charlie@example.com');
```
条件で絞れます。
```sql
SELECT username, email FROM users WHERE id = 1;
```
基本構文はこんなもんです。
ユーザを作成しましょう。
```sql
CREATE USER 'ユーザー名'@'ホスト' IDENTIFIED BY 'パスワード';
```
ホストとありますが、ここでどこからアクセスしてくるのかをあらかじめ決めて置きます。
このUbuntuサーバ自身からしかアクセスできないようにしたいので'localhost'としておきましょう。
その他にもIPアドレスで指定することもできます。
ユーザ名はUbuntuにログインするときのユーザ名と同一にすると便利です。

finish;か、quit;か忘れたけど、この辺のやつで対話モードから出れる。

もういちど、対話モードに入ってみましょう。
先程は`sudo mysql -u root -p`でしたが。
SQLログイン用のユーザを作成したので、
`sudo mysql -u ユーザ名 -p`
これで入れます。
SQL用のユーザ名とUbuntuログイン用のユーザ名を同じにした人は`-u ユーザ名`要りません。
今度は自分で作成したユーザでログインしました。
もう一度、上の操作をしてみてください。
きっとどこかでエラーが起きると思います。
これは今、SQLを打ち込んでいるユーザに表示などの権限がないことを意味しているかと思います。もしくは構文エラー。

権限の付与はこのように行います。

（以下ChatGPTコピペ）
# MySQLの権限管理について

## 1. 権限の基本構造
MySQLでは権限が階層構造で管理されます。大きい単位から順に次のように権限が付与されます。

### 全体（グローバル権限）
- サーバー全体に対する権限を付与します。
- 例: `ALL PRIVILEGES`（すべての権限を許可）。
- **保存場所**: `mysql.user` テーブル。

### データベース単位の権限
- 特定のデータベースに対する権限を付与します。
- 例: `SELECT`, `INSERT` などの操作を特定のデータベースに限定。
- **保存場所**: `mysql.db` テーブル。

### テーブル単位の権限
- 特定のテーブルに対する権限を付与します。
- **保存場所**: `mysql.tables_priv` テーブル。

### カラム（列）単位の権限
- 特定の列に対してのみ操作を許可します。
- **保存場所**: `mysql.columns_priv` テーブル。

### ルーチン単位の権限
- ストアドプロシージャやストアドファンクションに対する権限を付与します。
- **保存場所**: `mysql.proc_priv` テーブル。

---

## 2. 権限の種類
MySQLにはさまざまな権限があり、主なものを以下に示します。

| 権限名            | 説明                                     |
|--------------------|------------------------------------------|
| ALL PRIVILEGES     | すべての権限（特定の範囲で指定可）。     |
| SELECT             | データを読み取る（`SELECT`クエリを実行する）。 |
| INSERT             | データを挿入する（`INSERT`クエリを実行する）。 |
| UPDATE             | データを更新する（`UPDATE`クエリを実行する）。 |
| DELETE             | データを削除する（`DELETE`クエリを実行する）。 |
| CREATE             | データベースやテーブルを作成する。        |
| DROP               | データベースやテーブルを削除する。        |
| GRANT OPTION       | 他のユーザーに権限を付与する（再付与可能）。 |
| FILE               | サーバーファイルへのアクセスを許可（データのインポート・エクスポート）。 |
| EXECUTE            | ストアドプロシージャやファンクションを実行する。 |

---

## 3. 権限の付与
権限を付与するには`GRANT`文を使用します。

### 基本構文
```sql
GRANT 権限 ON 対象 TO 'ユーザー名'@'ホスト' [IDENTIFIED BY 'パスワード'];
```

### 使用例
#### 特定データベースに全権限を付与
```sql
GRANT ALL PRIVILEGES ON sample_db.* TO 'user1'@'localhost' IDENTIFIED BY 'password';
```
- `sample_db.*`: `sample_db`内のすべてのテーブル。

#### 特定テーブルに特定の権限を付与
```sql
GRANT SELECT, INSERT ON sample_db.users TO 'user2'@'%';
```
- `user2`はどこからでも（`%`）接続可能。

#### カラム単位の権限を付与
```sql
GRANT SELECT (name, email) ON sample_db.users TO 'user3'@'localhost';
```
- `name`と`email`列に対してのみ読み取り権限を付与。

---

## 4. 権限の確認
権限を確認するには、以下を使用します。

### 現在のユーザー権限を確認
```sql
SHOW GRANTS FOR 'ユーザー名'@'ホスト';
```

### 現在ログイン中のユーザー権限を確認
```sql
SHOW GRANTS;
```

---

## 5. 権限の取り消し
権限を取り消すには`REVOKE`文を使用します。

### 基本構文
```sql
REVOKE 権限 ON 対象 FROM 'ユーザー名'@'ホスト';
```

### 使用例
#### 特定の権限を取り消す
```sql
REVOKE SELECT ON sample_db.users FROM 'user1'@'localhost';
```

#### すべての権限を取り消す
```sql
REVOKE ALL PRIVILEGES ON *.* FROM 'user1'@'localhost';
```
