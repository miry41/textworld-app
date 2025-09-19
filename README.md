# TextWorld×Gemini MVP

TextWorldゲーム環境でGeminiモデルが人間のヒントを受けながらテキストアドベンチャーを攻略するMVPアプリケーションです。研究前段階として、人間とAIの協調プレイの有効性を検証することを目的としています。

## 🎮 概要

このアプリは以下の体験を提供します：

- **3ペイン構成**：ゲームログ（左）、人間ヒント入力（中央）、Geminiの提案（右）
- **協調プレイ**：毎ターン人間が1行のヒントを入力し、Geminiが次のアクションを提案
- **モデル選択**：Gemini 1.5 Flash / 1.5 Pro をUI上で選択可能
- **セッション保存**：ブラウザのlocalStorageにプレイログを保存
- **リプレイ機能**：過去のセッションを振り返り可能（本人のみ）

## 🏗️ システム構成

```
[Next.js Frontend + BFF]  ──HTTP──>  [TextWorld Service (Python)]
         │                              └── TextWorld runtime (gym-like)
         └──HTTP──>  Gemini API (Models: 1.5 Flash / 1.5 Pro)
```

### 技術スタック

- **フロントエンド**: Next.js 15 (App Router) + React 19 + Tailwind CSS
- **状態管理**: TanStack Query
- **BFF**: Next.js API Routes
- **TextWorld Service**: Python + FastAPI
- **AI**: Google Gemini API (1.5 Flash / 1.5 Pro)
- **データ保存**: ブラウザ localStorage

## 🚀 セットアップ

### 前提条件

- Node.js 18+
- Python 3.9+
- Google Gemini API キー

### インストール

1. **リポジトリのクローン**
```bash
git clone <repository-url>
cd textworld-app
```

2. **フロントエンド依存関係のインストール**
```bash
npm install
```

3. **TextWorld Service のセットアップ**
```bash
cd textworld-service
pip install -r requirements.txt
```

4. **環境変数の設定**
```bash
cp .env.example .env
# .env ファイルを編集してGEMINI_API_KEYを設定
```

### 開発環境での起動

1. **TextWorld Service の起動**
```bash
cd textworld-service
uvicorn main:app --reload --port 8000
```

2. **Next.js アプリケーションの起動**
```bash
npm run dev
```

3. ブラウザで http://localhost:3000 にアクセス

## 🎯 使用方法

1. **ゲーム開始**: トップページで「新しいゲームを開始」をクリック
2. **モデル選択**: Gemini 1.5 Flash または 1.5 Pro を選択
3. **ヒント入力**: 中央ペインに1行でヒントを入力（例：「鍵が要るはず」）
4. **提案確認**: 右ペインでGeminiの提案アクションと根拠を確認
5. **アクション実行**: 「採用」「編集して送信」「却下」「再試行」から選択
6. **ゲーム継続**: クリアまたは制限ターン数まで継続

## 📊 機能詳細

### ゲーム体験

- **固定マップ**: 研究用に1つのマップを使用
- **ターン制**: 毎ターン人間のヒント → Gemini提案 → アクション実行
- **進捗表示**: 成功/失敗アイコンと勝利条件ゲージ
- **フィードバック**: 各アクションの結果を視覚的に表示

### AI支援機能

- **コンテキスト理解**: 観測、履歴、ユーザーヒントを統合して判断
- **出力検証**: アクション形式の検証と自動再試行
- **禁止コマンド**: 不適切なコマンドの自動フィルタリング

### データ管理

- **セッション保存**: ブラウザlocalStorageに自動保存
- **メトリクス計測**: レスポンス時間、成功率、無効出力率を記録
- **プライバシー**: APIキーはサーバーサイドのみ、個人情報は保存しない

## 🔧 API仕様

### TextWorld Service (FastAPI)

- `POST /reset` - ゲームセッションの初期化
- `GET /observe` - 現在の観測状態を取得
- `POST /step` - アクションを実行して次の状態に遷移

### BFF (Next.js API)

- `POST /api/llm/propose` - Geminiにアクション提案を要求
- `POST /api/game/step` - TextWorldサービスへのstep中継
- `POST /api/game/reset` - TextWorldサービスへのreset中継

## 📈 メトリクス

MVPでは以下のメトリクスを計測します：

- **パフォーマンス**: API応答時間（P50, P95）
- **品質**: 無効出力率、アクション成功率
- **利用状況**: セッション毎ターン数、完了率
- **モデル比較**: Flash vs Pro の性能差

## 🚀 デプロイ

### 推奨構成

- **フロントエンド**: Vercel
- **TextWorld Service**: Fly.io (東京リージョン)

### デプロイ手順

1. **TextWorld Service のデプロイ**
```bash
cd textworld-service
fly deploy
```

2. **Next.js アプリのデプロイ**
```bash
vercel deploy
```

## 🔒 セキュリティ

- APIキーはサーバーサイドの環境変数のみ
- セッションIDは推測困難なUUID v4を使用
- CORSは必要な通信のみ許可
- localStorageには機密情報を保存しない

## 🧪 テスト

```bash
# ユニットテスト
npm test

# 結合テスト（10ターンの通し試験）
npm run test:integration

# E2Eテスト
npm run test:e2e
```

## 📋 開発タスク

### Week 1: 環境API化
- [ ] FastAPI で基本API実装
- [ ] 固定マップロード機能
- [ ] セッション管理（LRU）
- [ ] Docker化とヘルスチェック

### Week 2: BFF + Gemini 接続
- [ ] Gemini API統合
- [ ] プロンプト合成とモデル選択
- [ ] 出力検証と再試行機能
- [ ] エラーハンドリング

### Week 3: フロントUI
- [ ] 3ペインレイアウト
- [ ] アクション選択UI
- [ ] localStorage統合
- [ ] リプレイ機能

## 🔮 次フェーズ計画

### 外部API不要化
- **ローカルLLM**: webGPU + 量子化モデル、またはサーバーサイドllama.cpp
- **別ゲーム対応**: TextWorld以外のゲーム環境への拡張
- **プロンプト最適化**: ヒント→意図抽出→候補選択の改善

### 研究データ収集
- 人間ヒントの言語的特徴と成功率の相関分析
- モデル間の介入必要回数の比較
- 失敗パターンの分類と改善点の特定

## 🤝 コントリビューション

1. このリポジトリをフォーク
2. フィーチャーブランチを作成 (`git checkout -b feature/amazing-feature`)
3. 変更をコミット (`git commit -m 'Add some amazing feature'`)
4. ブランチにプッシュ (`git push origin feature/amazing-feature`)
5. プルリクエストを作成

## 📄 ライセンス

このプロジェクトはMITライセンスの下で公開されています。詳細は `LICENSE` ファイルを参照してください。

## 🙋‍♂️ サポート

質問や問題がある場合は、GitHubのIssuesでお知らせください。

---

**注意**: このMVPは研究前段階として設計されており、本格的な商用利用は想定していません。コスト制限や高度なセキュリティ機能は次フェーズで実装予定です。
