# プロジェクトメモリ

ローカル開発用の **Traefik v3** リポジトリです。Docker Compose で起動し、`*.localhost` 向けの HTTPS（mkcert）と動的 TLS 設定を扱います。

## ディレクトリ構成

```
.
├── compose.yaml          # Docker Compose（Traefik の起動・ボリューム）
├── traefik.yaml          # 静的設定（プロバイダ・エントリポイントなど）
├── README.md
├── AGENTS.md
├── certs/                # mkcert 証明書（多くの場合 Git 対象外）
├── dynamic/              # Traefik 動的設定（TLS など、YAML）
└── .agents/skills/       # エージェント用スキル（証明書追加手順など）
```

## 前提

- 外部 Docker ネットワーク `traefik` が必要（`docker network create traefik`）。
- 証明書は `mkcert` で生成し、`dynamic/` で `certFile` / `keyFile` を指す。

## 新しいサブドメイン用の証明書を足すとき

`.agents/skills/traefik-cert-add/SKILL.md` に手順があります。mkcert の発行と `dynamic/` への TLS 定義追加がセットになっています。

## 起動・確認

```bash
docker compose up -d
```

- ダッシュボード: `http://localhost:8080`
