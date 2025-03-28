# pbVector Docker Example

PostgreSQL と pgVector 拡張機能を使ったベクトル検索データベース環境の Docker セットアップ例です。

## 概要

このリポジトリは、ベクトル類似性検索機能を持つ PostgreSQL コンテナをすぐに使える形で提供します。機械学習の埋め込みベクトルや類似度検索が必要なアプリケーション開発に最適です。

主な構成要素:

- PostgreSQL + pgVector 拡張（v0.7.2）
- Adminer（データベース管理ツール）
- 環境設定用の変数
- Docker ボリュームによるデータ永続化

## 前提条件

- Docker
- Docker Compose

## セットアップ

1. リポジトリをクローン:

   ```bash
   git clone <repository-url>
   cd pgvector-docker-example
   ```

2. `.env`ファイルを作成し、必要な環境変数を設定:

   ```
   POSTGRES_USER=<ユーザー名>
   POSTGRES_PASSWORD=<パスワード>
   POSTGRES_HOST=<ホスト名>
   POSTGRES_PORT=<ポート番号>
   POSTGRES_DB=<データベース名>
   ADMINER_PORT=<Adminerポート番号>
   ```

3. Docker コンテナを起動:
   ```bash
   docker-compose up -d
   ```

## pgVector の使用方法

### データベースへの接続

```bash
docker-compose exec pgvector psql -U ${POSTGRES_USER} -d ${POSTGRES_DB}
```

### pgvector 拡張の有効化

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

### サンプルの使用方法

1. ベクトルデータを格納するテーブルの作成:

   ```sql
   CREATE TABLE items (
     id bigserial PRIMARY KEY,
     embedding vector(3)
   );
   ```

2. データの挿入:

   ```sql
   INSERT INTO items (embedding) VALUES ('[1,2,3]'), ('[4,5,6]');
   ```

3. 類似度検索:
   ```sql
   -- 最も近いベクトルを検索
   SELECT * FROM items ORDER BY embedding <-> '[3,1,2]' LIMIT 5;
   ```

## Adminer の使用方法

Web ブラウザで`http://localhost:<ADMINER_PORT>`にアクセスし、以下の情報でログイン:

- システム: PostgreSQL
- サーバー: pgvector
- ユーザー名: `.env`ファイルで設定した`POSTGRES_USER`の値
- パスワード: `.env`ファイルで設定した`POSTGRES_PASSWORD`の値
- データベース: `.env`ファイルで設定した`POSTGRES_DB`の値

## コンテナ管理

- コンテナの状態確認:

  ```bash
  docker-compose ps
  ```

- コンテナのログ表示:

  ```bash
  docker-compose logs -f
  ```

- コンテナの停止:

  ```bash
  docker-compose down
  ```

- コンテナとボリュームの完全削除:
  ```bash
  docker-compose down -v
  ```

## プロジェクト構成

- `pgvector/Dockerfile`: pgvector 拡張機能付き PostgreSQL イメージのビルド定義
- `docker-compose.yaml`: サービス構成の定義
- `.env`: 環境変数設定ファイル

## データの永続化

PostgreSQL データは名前付きボリューム`pgvector_data`に保存され、コンテナを再起動してもデータが保持されます。

## 本番環境での使用

このセットアップは開発環境向けに最適化されています。本番環境で使用する場合は、以下の点を考慮してください:

- セキュアなパスワード設定
- ネットワークセキュリティの強化
- リソース使用量の制限設定
- 定期的なバックアップ戦略の実装

## 参考資料

- [pgvector 公式リポジトリ](https://github.com/pgvector/pgvector)
- [PostgreSQL 公式ドキュメント](https://www.postgresql.org/docs/)
- [Docker ボリューム管理](https://docs.docker.com/storage/volumes/)

## ライセンス

[MIT License](LICENSE)
