## [SQLite3]DBファイル/ディレクトリの権限設定
```bash
# データベースファイルの所有者とグループを設定
sudo chown www-data:www-data database.sqlite
# 600は所有者のみ読み書き可能
sudo chmod 600 database.sqlite
# データベースファイルが置かれているディレクトリの権限も設定
sudo chmod 755 /path/to/database/directory
```

---
SQLite3の基本的なコマンドについて説明します。SQLite3はコマンドラインツールを使用してデータベースを操作できるので、SQLクエリに加えて、SQLite3専用のコマンドも用意されています。

以下はSQLite3でよく使われる基本的なコマンドです。

---

### **1. SQLite3の起動**
SQLite3のコマンドラインツールを使ってデータベースに接続します。データベースが存在しない場合、SQLite3は新しくデータベースを作成します。

```bash
sqlite3 database.db
```
`database.db` は接続するデータベースファイルの名前です。このコマンドでSQLite3のインタラクティブシェルが起動します。

---

### **2. テーブルの作成**
SQLite3でテーブルを作成するには、`CREATE TABLE`コマンドを使用します。

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL
);
```
- `INTEGER PRIMARY KEY AUTOINCREMENT`: `id` 列は自動的に増加するユニークな値を持つ
- `TEXT`: 文字列型
- `UNIQUE`: `email` 列は重複しない値を持つ

---

### **3. テーブルの確認**
作成したテーブルを確認するには、`.tables` コマンドを使用します。

```bash
.tables
```
このコマンドは、現在のデータベースに存在するすべてのテーブル名を表示します。

---

### **4. テーブルの構造を表示**
特定のテーブルの構造を確認するには、`.schema` コマンドを使用します。

```bash
.schema users
```
これで `users` テーブルのスキーマ（構造）が表示されます。

---

### **5. データの挿入**
データを挿入するには、`INSERT INTO` コマンドを使用します。

```sql
INSERT INTO users (name, email) VALUES ('Taro', 'taro@example.com');
```

---

### **6. データの取得**
データを取得するには、`SELECT` コマンドを使用します。

```sql
SELECT * FROM users;
```
このコマンドは `users` テーブルのすべての列と行を取得します。

特定の列だけを取得することもできます。

```sql
SELECT name, email FROM users;
```

---

### **7. データの更新**
データを更新するには、`UPDATE` コマンドを使用します。

```sql
UPDATE users SET name = 'Jiro' WHERE id = 1;
```
この例では、`id` が1のユーザーの名前を `Jiro` に変更しています。

---

### **8. データの削除**
データを削除するには、`DELETE FROM` コマンドを使用します。

```sql
DELETE FROM users WHERE id = 1;
```
このコマンドは `id` が1のユーザーを削除します。

---

### **9. インデックスの作成**
インデックスを作成することで、検索性能を向上させることができます。

```sql
CREATE INDEX idx_email ON users (email);
```
このコマンドは `email` 列にインデックスを作成します。

---

### **10. データベースのバックアップ**
SQLite3データベースをバックアップするには、`.backup` コマンドを使用します。

```bash
.backup backup.db
```
このコマンドは、現在のデータベースを `backup.db` というファイル名でバックアップします。

---

### **11. データベースの閉じる**
SQLite3のインタラクティブシェルを終了するには、`.exit` コマンドを使用します。

```bash
.exit
```

これでSQLite3シェルを終了できます。

---

### **12. ヘルプを表示**
SQLite3のコマンドに関するヘルプを表示するには、`.help` コマンドを使用します。

```bash
.help
```
これで、SQLite3のコマンドラインツールで使用できるすべてのコマンドが表示されます。

---

### **13. SQLite3の設定コマンド**
SQLite3では、設定を確認したり変更したりするためのコマンドもあります。これらは主に `PRAGMA` コマンドを使います。

例えば、データベースのエラーログを確認するには以下のように使用します。

```sql
PRAGMA foreign_keys;
```

---

### **14. データベースファイルの確認**
SQLite3データベースファイルが正しく作成されているかを確認するには、`ls`（UNIX系）や`dir`（Windows）を使ってファイルの存在を確認します。

```bash
ls
```

SQLite3の基本コマンドはこれでほとんどの操作をカバーできます。日常的に使うコマンドとして、データの挿入、更新、削除、選択、バックアップなどが主に利用されます。
