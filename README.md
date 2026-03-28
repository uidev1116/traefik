# ローカル開発用 Traefik

このリポジトリは、ローカル開発環境向けの [Traefik](https://traefik.io/) リバースプロキシ設定をまとめたものです。Docker と mkcert を使い、`*.localhost` ベースの HTTPS を扱いやすくします。

## 前提条件

次のツールがインストール済みであることを想定しています。

- **Docker**（Docker Compose を含む）
- **Homebrew**（macOS で mkcert を入れるために使用）

## セットアップ手順

1. **共有ネットワークの作成**

   ```bash
   docker network create traefik
   ```

2. **mkcert のインストール**

   ```bash
   brew install mkcert nss && mkcert -install
   ```

3. **ワイルドカード証明書の生成**

   ```bash
   mkdir certs && cd certs && mkcert "*.localhost"
   ```

4. **プロジェクトごとの証明書追加（例）**

   プロジェクト用のサブドメイン向けに、ワイルドカード証明書を追加で発行します。

   ```bash
   mkcert "*.my-blog-site-backend.localhost"
   ```

5. **動的設定（`dynamic/`）の追加**

   `dynamic/` 配下に、プロジェクト名に対応した YAML ファイル（例: `my-project.yaml`）を作成します。記述形式は **`dynamic/tls.yaml` を参考に**、マウントした証明書の `certFile` / `keyFile` パスを指定してください。

6. **Traefik の起動**

   リポジトリのルートで次を実行します。

   ```bash
   docker compose up -d
   ```

## アクセス確認

- **Traefik ダッシュボード**: [http://localhost:8080](http://localhost:8080)

ブラウザで開き、プロキシとルーティングの状態を確認できます。
