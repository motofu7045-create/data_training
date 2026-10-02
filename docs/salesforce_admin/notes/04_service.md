# 4. サービスとサポートのアプリケーション（配点10%）

公式 Trailmix「Salesforce Platform アドミニストレーター認定試験に向けた準備」の「Service and Support Applications」の教材を読んでまとめたもの（2026-10-02 時点）。

- 読んだ教材: [Agentforce Service の管理](https://trailhead.salesforce.com/ja/content/learn/modules/service_lex)、[サポートケースを管理するプロセスを作成する](https://trailhead.salesforce.com/ja/content/learn/projects/create-a-process-for-managing-support-cases)、[Set Up Case Escalation and Entitlements](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements)（英語のみ）、[組織をカスタマイズして新規ビジネスユニットをサポート](https://trailhead.salesforce.com/ja/content/learn/projects/customize-an-org-to-support-a-new-business-unit)（割り当てルールとエスカレーションルールの部分）

---

## 4-1. サービスコンソール

- サービス担当者の**統合ワークスペース**。顧客の情報（履歴、納入商品、過去のやり取り）を1つの画面にまとめる
- 普通のアプリと違い、**複数のレコードをタブで同時に開いて作業できる**

| 部分 | 内容 |
| --- | --- |
| ナビゲーションバー | オブジェクトメニューでケース・取引先・取引先責任者などを切り替える |
| **分割ビュー** | 画面左にリストを表示し、リストとレコードを行き来せずに作業できる |
| レコードページ | 強調表示パネル（優先度、状況、発生源など）、フィード（メール・電話・メモのやり取り）、関連レコード |
| ナレッジ | ケースの件名から**ヘルプ記事を提案**し、ケースに添付したりメールで送ったりできる |
| **ユーティリティバー** | 画面下部の、どのタブでも使えるツール（**履歴**: 直近10件のレコードに戻る、**マクロ**: 繰り返し作業をワンクリックで実行） |

- コンソールの作成・カスタマイズ: [設定] → 「アプリケーションマネージャー」

出典: [サービスコンソールを理解する](https://trailhead.salesforce.com/ja/content/learn/modules/service_lex/service_lex_cloud)

---

## 4-2. サービスの実装（Salesforce Go）

**Salesforce Go**: 重要な設定タスクや機能の有効化を1か所にまとめた、システム管理者向けのガイド付きツール。

### 実装の4つのフェーズ

| フェーズ | 主な機能 |
| --- | --- |
| 1. 基盤 | プロファイルとアクセス権、ケースの項目・ページレイアウト・状況、**キュー**、**割り当てルール・自動レスポンスルール・エスカレーションルール**、サービスコンソール、Slack、**メール-to-ケース／Web-to-ケース** |
| 2. 生産性と効率性 | **ナレッジ**、**マクロ・クイックテキスト・履歴**、**エンタイトルメントとマイルストーン**（SLA）、**オムニチャネル**（作業を担当者に自動で転送）、モバイルアプリ |
| 3. チャネルと拡張 | Experience Cloud サイト（セルフサービス）、チャット・メッセージング・Voice、ガイド付きワークフロー、Field Service |
| 4. インテリジェンス | AI（作業の要約、返信のドラフト）、AI エージェント、サービス向けコマンドセンター |

出典: [Salesforce Go でサービスを設定する](https://trailhead.salesforce.com/ja/content/learn/modules/service_lex/service_lex_connect)

---

## 4-3. ケースを自動で処理する機能（試験に出やすい）

| 機能 | 何をするか | 使う場面（チームへの質問の例） |
| --- | --- | --- |
| **キュー** | ケースを**チームで共有するリスト**に入れる | チームでワークロードを共有しているか？ |
| **割り当てルール** | ケースを**特定の担当者やキューに自動で割り当てる** | 問題の種類ごとに担当が決まっているか？ |
| **エスカレーションルール** | 一定**時間**解決されないケースを、別の人やキューに回す・通知する | 期限までに解決しないケースを誰かに回す必要があるか？ |
| **自動レスポンスルール** | ケースの内容に応じて、**顧客に自動で返信メール**を送る | 受け付けたことを顧客に知らせる必要があるか？ |

### ルールの共通点

- 1つのルールに複数の**ルールエントリ**を作る。エントリは**並び順に評価**され、**最初に一致したエントリ**が適用される（それ以降は評価しない）
- 条件には、ケース以外のレコード（取引先、取引先責任者、納入商品、ユーザーなど）の項目も使える
- 有効にできる割り当てルールは**1つ**（「有効」チェックボックス）

### エスカレーションルールの設定

- エントリ: 条件、**営業時間**（デフォルトは24時間365日）、エスカレーション時刻の基準（例: ケースの作成日時）
- **エスカレーションアクション**: 何時間後（30分単位も可）に、**誰（ユーザーまたはキュー）に再割り当てするか**、**誰に通知するか**（メールテンプレートを使う）
- エスカレーションの予定は [設定] → 「ケースのエスカレーション」で確認できる（「エスカレーション日時」列）

### その他

- **自動化ケースユーザー**: 割り当てルールなどで自動変更されたとき、ケースの履歴に表示されるユーザー名（例: 「System」に変えておく）
- ケースのページレイアウトのプロパティで、「有効な割り当てルールを使用して割り当てる」チェックボックスをデフォルトでオンにできる

出典: [ケース管理の自動化](https://trailhead.salesforce.com/ja/content/learn/modules/service_lex/service_lex_case_manage)、[エスカレーションルールの作成](https://trailhead.salesforce.com/ja/content/learn/projects/create-a-process-for-managing-support-cases/create-an-escalation-rule)、[Create Case Queues and Assignment Rules](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements/create-case-queues-assignment-rule)

---

## 4-4. ケースの受付チャネル（試験に出やすい）

| | メール-to-ケース | Web-to-ケース |
| --- | --- | --- |
| 何をするか | **メール**で届いた問い合わせをケースにする | **Web サイトのフォーム**から送られた問い合わせをケースにする |
| 設定の流れ | メールプロバイダーを選び、サポート用のメールアドレスから Salesforce の転送アドレスへ**メールを転送**する | フォームに出す項目を選び、**HTML を生成**して Web 開発者に渡し、Web サイトに置いてもらう |
| ポイント | 優先度や発生源などを自動で設定できる。アドレスを Web サイトや名刺に載せて周知する | **1日最大5,000件**。自動返信用のテンプレートを選べる。reCAPTCHA も使える |

出典: [基本的なサービスチャネルを作成する](https://trailhead.salesforce.com/ja/content/learn/modules/service_lex/service_lex_channels)

---

## 4-5. サポートプロセスとレコードタイプ

- **サポートプロセス**: ケースの**状況**（新規、対応中、保留中、クローズなど）のうち、どれを使うかを決める
- ケースの種類ごとに **サポートプロセス → レコードタイプ → ページレイアウト** を用意する
  - 例: 「製品サポート」と「問い合わせ」で、使う状況、種別の選択肢、ページレイアウトを分ける
- レコードタイプで、選択リスト（種別など）の値をレコードタイプごとに絞れる
- 選択リストの不要な値は**無効化**できる（無効な値リストに移る）

出典: [サポートプロセスの作成](https://trailhead.salesforce.com/ja/content/learn/projects/create-a-process-for-managing-support-cases/create-support-processes)、[レコードタイプの作成](https://trailhead.salesforce.com/ja/content/learn/projects/create-a-process-for-managing-support-cases/support-cases-record-types)、[Create Support Processes](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements/create-support-processes-cases)

---

## 4-6. エンタイトルメントとサービス契約（SLA）

| 用語 | 意味 |
| --- | --- |
| **エンタイトルメント** | 顧客が受けられる**サポートのレベル**（例: プラチナの電話サポート）。残りケース数なども管理できる |
| **サービス契約** | 顧客とのサポート契約（SLA）。期間を持ち、エンタイトルメントを追加する |
| **エンタイトルメントプロセス** | ケースを解決するまでの**タイムライン** |
| **マイルストーン** | タイムラインの中の**期限付きのステップ**（例: 初回応答、解決時間） |

- 設定の流れ: [設定] → 「エンタイトルメント設定」で**エンタイトルメント管理を有効化** → タブをアプリに追加 → ページレイアウトに関連リストや項目（エンタイトルメント名、SLA の開始・終了時刻、ケースマイルストーン）を追加 → マイルストーンを作る → エンタイトルメントプロセスを作る → サービス契約にエンタイトルメントを追加
- マイルストーンには**警告アクション**（期限の前に通知メールを送るなど）を設定できる
- ケースのページに**マイルストーンコンポーネント**を置くと、残り時間のカウントダウンが表示される

出典: [Enable Entitlements and Set Up Service Contracts](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements/enable-entitlements-set-up-service-contracts)、[Create an Entitlement Process](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements/create-entitlement-process)、[Create Service Contracts with Entitlements](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements/create-service-contracts-with-entitlements)

---

## 4-7. フローによるケースの通知（例）

大口の取引先で新しいケースが作られたら担当者に通知する。

1. **メールテンプレート**を作る
2. **メールアラート**を作る（テンプレートと受信者を指定）
3. **レコードトリガーフロー**（ケースの作成時）を作り、**決定**要素で大口かどうかを判定し、Yes のときにメールアラートを実行する

出典: [Setup Automated Case Notifications with Flow Builder](https://trailhead.salesforce.com/ja/content/learn/projects/set-up-case-escalation-entitlements/create-flow-with-process-builder)
