---
name: traefik-cert-add
description: ローカル開発環境のTraefikにプロジェクト用のmkcert証明書を追加するスキル。「〇〇の証明書を追加して」「〇〇プロジェクトをHTTPSで使えるようにして」「mkcertで証明書を作って」「〇〇.localhostの証明書を追加して」などのフレーズで積極的に使うこと。証明書の生成（mkcert）とdynamic yamlファイルの作成を自動で行う。
---

# Traefik ローカル証明書追加スキル

## 概要

ローカル開発環境でプロジェクトごとに `*.プロジェクト名.localhost` のHTTPS証明書を追加するスキル。

## 前提環境

- `~/traefik/` にTraefikの設定がある
- `~/traefik/certs/` が証明書置き場
- `~/traefik/dynamic/` が動的設定置き場
- mkcertがインストール済み（未インストールなら `brew install mkcert` を案内）

## 手順

### Step 1: 入力の確認

ユーザーの指示からプロジェクト名を特定する。

- 「my-blog-site-backend の証明書を追加して」→ プロジェクト名: `my-blog-site-backend`
- 「*.foo.localhost の証明書を追加して」→ プロジェクト名: `foo`
- 不明な場合はユーザーに確認する

### Step 2: 既存ファイルの確認

```bash
ls ~/traefik/certs/
ls ~/traefik/dynamic/
```

同名の証明書・dynamicファイルが既に存在する場合はユーザーに確認してから上書きする。

### Step 3: 証明書の生成

```bash
cd ~/traefik/certs
mkcert "*.<プロジェクト名>.localhost"
```

生成されるファイル名を確認する（mkcertはドットをアンダースコアに変換する）：
```bash
ls ~/traefik/certs/ | grep <プロジェクト名>
```

### Step 4: dynamic yamlファイルの作成

`~/traefik/dynamic/<プロジェクト名>.yaml` を作成する：

```yaml
tls:
  certificates:
    - certFile: /etc/traefik/certs/<生成された.pemのファイル名>
      keyFile: /etc/traefik/certs/<生成された-key.pemのファイル名>
```

**注意**: ファイル名はStep 3で確認した実際のファイル名を使うこと。

### Step 5: 反映確認

dynamic設定は `watch: true` により自動反映されるのでTraefikの再起動は不要。

以下を報告する：
- 生成した証明書ファイル名
- 作成したdynamic yamlのパスと内容
- アクセスできるURL例: `https://web.<プロジェクト名>.localhost`

## 注意事項

- `sudo` が必要な操作はユーザーに確認を求めること
- エラーが発生した場合は内容を報告して止まること
- 証明書の上書きは必ずユーザー確認を取ること
- mkcertが未インストールの場合は `brew install mkcert && mkcert -install` を案内してから止まること
