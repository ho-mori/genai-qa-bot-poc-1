# 生成 AI × 社内ナレッジ QA チャットボット PoC

社内に散在する FAQ・手順書・議事録などを **ChatGPT / Ollama** と **ベクトル検索 (FAISS)** を組み合わせて検索・要約するチャットボットの概念実証リポジトリです。  
**4 週間で導入効果を数値化** し、横展開できるテンプレートを提供します。

---

## 📌 ゴール

| KPI              | 目標値                        |
| ---------------- | ----------------------------- |
| 回答正確度       | ≥ 85 %（ヒューマンレビュー）  |
| 平均応答時間     | < 2 秒（キャッシュ時 < 1 秒） |
| FAQ チケット削減 | ≥ 30 %                        |
| ユーザー満足度   | ≥ 4.0 / 5.0                   |

---

## 🏗️ 技術スタック

| 項目           | 技術                                        |
| -------------- | ------------------------------------------- |
| 言語           | Python 3.11                                 |
| フレームワーク | FastAPI + LangChain                         |
| LLM            | OpenAI GPT-4o (API) **or** Ollama (local)   |
| ベクトル DB    | FAISS (in-memory)                           |
| Embedding      | `text-embedding-3-small`                    |
| インフラ       | Docker Compose / optional AWS Fargate       |
| CI/CD          | GitHub Actions (lint → test → Docker build) |

---

## 📂 ディレクトリ構成

```

.
├─ app/
│  ├─ api/            # FastAPI ルーター
│  ├─ core/           # 設定・共通ユーティリティ
│  ├─ services/       # LangChain ラッパ
│  └─ tests/
├─ docs/              # 取り込み用 Markdown / PDF
├─ docker-compose.yml
├─ Dockerfile
└─ README.md

```

---

## 🚀 クイックスタート

### 1. 前提ツール

- **Docker Desktop** v4.30+
- **Git** v2.40+
- （ローカル推論を使う場合のみ）**Ollama** / GPU

### 2. 環境変数

`.env.example` をコピーして `.env` を作成し、必要なキーを設定してください。

```bash
cp .env.example .env
# OPENAI_API_KEY=sk-...
# OLLAMA_MODEL=mistral:7b-chat
```

### 3. 起動

```bash
# ベクトル DB に初期埋め込み
docker compose run --rm backend python scripts/embed.py docs/

# API + UI を起動
docker compose up -d
```

- API: [http://localhost:8000/docs](http://localhost:8000/docs)
- Web UI (Streamlit): [http://localhost:8501](http://localhost:8501)

---

## 📝 ドキュメント追加フロー

1. `docs/` に Markdown または PDF を置く
2. `embed.py` を再実行
3. キャッシュをクリアして再起動（`docker compose restart backend`）

---

## 🛠️ 開発モード

```bash
# Poetry 環境でローカル実行
poetry install
poetry run uvicorn app.api.main:app --reload
```

単体テスト:

```bash
poetry run pytest
```

---

## 📈 評価方法

- `scripts/evaluate.py` に質問セット (YAML) を渡すと

  - 正答率、平均応答時間、ソース提示率を自動集計

- KPI レポートは `reports/` に Markdown + CSV で出力

---

## 🔒 セキュリティ

| リスク     | 対策                                    |
| ---------- | --------------------------------------- |
| 機密漏洩   | オフライン推論 / OpenAI Enterprise 経由 |
| 不適切回答 | Guardrails + モデレーション API         |
| 権限制御   | JWT + RBAC (FastAPI)                    |

---

## 🗺️ 今後のロードマップ

- [ ] **Slack / Teams Bot** 連携
- [ ] **画像・図面** 検索（Vision Embedding）
- [ ] **S3 / Git** 自動インジェスト CI
- [ ] マルチテナント SaaS 化

---

## 🤝 コントリビューション

1. Issue を立てる
2. `feat/xxxx` ブランチで PR 作成
3. GitHub Actions を通過後、レビュー → マージ

---

## 📄 ライセンス

本リポジトリは **MIT License** です。詳細は `LICENSE` を参照してください。

---

## 👤 作者

- **技術開発部 EN3**（森 宝松）

  - Twitter: [@your_handle](https://twitter.com/your_handle)
  - Blog: [https://kinova.jp/blog/](https://kinova.jp/blog/)

> 社内外の知識を「聞ける化」し、探す時間を価値創出に置き換えましょう。
