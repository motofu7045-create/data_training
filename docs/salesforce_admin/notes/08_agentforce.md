# 8. Agentforce AI（配点8%）

公式 Trailmix「Salesforce Platform アドミニストレーター認定試験に向けた準備」の「Agentforce AI」の教材を読んでまとめたもの（2026-10-02 時点）。

- 読んだ教材: [Agentforce の基本](https://trailhead.salesforce.com/ja/content/learn/modules/einstein-copilot-basics)、[Agentforce Builder の概要](https://trailhead.salesforce.com/ja/content/learn/modules/introduction-to-agent-builder)
- Agentforce は名前や機能の変化が速い分野。教材では以前「トピック」と呼ばれていたものが「**サブエージェント**」と呼ばれている。試験の問題文で古い名前が出ることもあるので注意

---

## 8-1. Agentforce とは

- **Agentforce**: 既存のワークフロー・データ・インテグレーションを使って **AI エージェント**を作り、リリースするためのプラットフォーム
- **AI エージェント**: タスクやビジネス上のやり取りを実行する、**目標指向**の AI アプリ。データを基に自分で判断して動く（自律的）
- **コードを書かなくても**作れる。Agentforce を有効にしてエージェントを作るだけ

### すぐ使える標準アクションの例

| アクション | 内容 |
| --- | --- |
| レコードを照会（Query Records） | 条件に合うレコードを探す（例: 今四半期に完了予定の進行中の商談） |
| レコードを要約（Summarize Record） | 1件のレコードを要約する |
| メールのドラフト作成・修正 | メールの下書きを作る |
| ナレッジを使用して質問に回答 | ナレッジ記事を基に答える（**ナレッジライセンスが必要**） |

- 一部のアクションは重要なシステムアクションで、**削除できない**
- 既存のフロー（例: 商品のおすすめ）をエージェントに追加して機能を広げられる

出典: [Agentforce の概要](https://trailhead.salesforce.com/ja/content/learn/modules/einstein-copilot-basics/get-started-with-einstein-copilot)

---

## 8-2. 信頼と正確さ

| 仕組み | 内容 |
| --- | --- |
| **標準のアクセス制御を守る** | エージェントも Salesforce の共有設定や権限に従う |
| **Einstein Trust Layer** | プロンプトを信頼できる会社のデータで**グラウンディング**する。**データが外部の LLM 提供者に保持されない**。AI とのやり取りは**ログに記録**される |
| **ガードレール** | エージェントの動きを導き、ビジネスポリシーや規制に従わせる |
| **Data 360** | データを**コピーせずに**、CRM、Slack、ナレッジ記事、外部データレイクなどにアクセスさせる |
| **メタデータ** | 項目や表示ラベル、自動化に付いたメタデータから、エージェントがビジネスの文脈を理解する |
| **エージェントスクリプト** | エージェントを作るための言語。自然言語の柔軟さと、プログラムの**予測可能さ**を組み合わせる（決まったパスをたどらせられる） |

出典: [Agentforce の特徴](https://trailhead.salesforce.com/ja/content/learn/modules/einstein-copilot-basics/get-started-with-einstein-copilot)

---

## 8-3. Agentforce の4つの部品（試験に出やすい）

| 部品 | 役割 |
| --- | --- |
| **エージェント** | 従業員やお客様を支援する AI。例: 従業員エージェント、営業コーチ、サービス（問い合わせを自律的に解決）、リードの処理 |
| **サブエージェント** | エージェントが実行できる**ジョブ**のまとまり。**指示**（判断の仕方、すべきこと・すべきでないこと）と**アクション**を含む。例: 「注文管理」 |
| **アクション** | 実際に実行する処理。例: 注文 ID で注文を取得、返送ラベルを作成。**名前・説明・入力・出力**（指示）で、いつどう実行するかが決まる |
| **推論エンジン** | どのサブエージェント・アクションを使うかを**調整する**（オーケストラの指揮者）。Agentforce では **Atlas 推論エンジン** |

### 標準とカスタム

| | 標準 | カスタム |
| --- | --- | --- |
| サブエージェント | よくある用途向けにあらかじめ用意（例: General CRM、マーケティングキャンペーン）。クラウドやライセンスが必要なものもある | 自社のプロセスに合わせて作る |
| アクション | よくある処理を用意 | **既存の機能を土台に作る**: 呼び出し可能な Apex クラス、REST Apex クラス、**自動起動フロー**、**プロンプトテンプレート**、外部サービス |

### 推論の流れ

1. ユーザーが質問・依頼を入力する
2. エージェントが開始サブエージェントに移り、適切なサブエージェントを選ぶ
3. サブエージェントの指示を順に解決する（ここは**決定論的**）
4. 指示・会話履歴・使えるアクションを含むプロンプトを作り、**LLM** に送る
5. LLM が、ユーザーに答えるか、アクションを実行するかを決める
6. ユーザーが続けて質問すると、また最初から

出典: [Agentforce のしくみ](https://trailhead.salesforce.com/ja/content/learn/modules/einstein-copilot-basics/explore-einstein-copilot)、[推論エンジンの概要](https://trailhead.salesforce.com/ja/content/learn/modules/einstein-copilot-basics/enable-customize-copilot)

---

## 8-4. エージェントを作る・育てる

| ツール | 内容 |
| --- | --- |
| **Agentforce スタジオ** | エージェントの作成・カスタマイズ・テスト・監視をする**中央ハブ**。アプリケーションランチャーから開く |
| **Agentforce Builder** | エージェントを作る画面 |

### Agentforce Builder

- [新規エージェント] から、**テンプレートを選ぶ**か、**やりたいことを自然言語で説明**して作る（**テンプレートの利用が推奨**）。テンプレートや説明に応じてサブエージェントとアクションが自動で付く
- 主な部分: エクスプローラー（エージェントの部品の一覧）、エディター、**会話のプレビュー**（推論の内容も見られる）、**キャンバス**ビュー（自然言語）と**スクリプト**ビュー（コード）の切り替え、AI アシスタント
- **ドラフトを保存** → **バージョンをコミット**するとエージェントを有効化できる。**コミット済みのバージョンは変更できない**（新しいドラフトを作って変更する）
- サブエージェントやアクションは、アセットライブラリから追加するか、新しく作る

### Agentforce Observability（監視と改善）

| ツール | 内容 |
| --- | --- |
| Agentforce 分析 | セッション内のすべてのやり取りをイベントとして記録する |
| Agentforce 最適化 | 未解決のやり取りや知識の不足を見つける（インテント、品質スコア、セッション分析など） |

- 一部のツールは **Data 360 ライセンス**が必要

出典: [Agentforce Builder の概要](https://trailhead.salesforce.com/ja/content/learn/modules/introduction-to-agent-builder/get-to-know-agent-builder)、[推論エンジンの概要](https://trailhead.salesforce.com/ja/content/learn/modules/einstein-copilot-basics/enable-customize-copilot)
