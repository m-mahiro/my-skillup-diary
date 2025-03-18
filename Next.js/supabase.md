# .env
- **`supabase_url`**：Supabaseが提供するAPI（Restful APIやGraphQL）にアクセスするためのURL。通常の開発ではこれで十分。
- **`anon_key`**：Supabaseの匿名公開キー。クライアントサイドからSupabase APIにアクセスするために必要。
- **`database_url`**：PostgreSQLデータベースに直接接続するためのURL。サーバーサイドでの高度なデータ操作や直接クエリが必要な場合に使用。

通常は、**`supabase_url`**と**`anon_key`**だけで十分ですが、**`database_url`**はサーバーサイドでの直接データベース操作が必要な場合に使用します！
認証を実装する際も`supabase_url`は必要ない

# type generate
pnpx supabase gen types typescript --project-id <supabaseのプロジェクトid>  > ./types/database-type.ts