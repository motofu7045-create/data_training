# 6. データと分析の管理（配点17%）

公式 Trailmix「Salesforce Platform アドミニストレーター認定試験に向けた準備」の「Data and Analytics Management」の教材を読んでまとめたもの（2026-10-02 時点）。

- 読んだ教材: [データ管理](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_data_management)、[重複管理](https://trailhead.salesforce.com/ja/content/learn/modules/sales_admin_duplicate_management)、[データ管理ツールを使用してインポートとエクスポートを行う](https://trailhead.salesforce.com/ja/content/learn/projects/import-and-export-with-data-management-tools)、[Lightning Experience のレポートおよびダッシュボード](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_reports_dashboards)、[営業マネージャーとマーケティングマネージャー用のレポートとダッシュボードを作成する](https://trailhead.salesforce.com/ja/content/learn/projects/create-reports-and-dashboards-for-sales-and-marketing-managers)
- 読めなかった教材: Salesforce ヘルプの記事「Compare Access Levels for Report and Dashboard Folders」（作業環境から表示できなかった）、YouTube 動画「How to Update Records Using the External ID Using Data Loader」

---

## 6-1. データのインポート

### データインポートウィザード と データローダ

| | データインポートウィザード | データローダ |
| --- | --- | --- |
| 何か | ブラウザで使う、[設定] の中のツール | PCに**インストール**して使うアプリ |
| 件数 | **5万件まで** | **5万〜1億5,000万件** |
| 対象 | 取引先、取引先責任者、リード、ソリューション、個人取引先、キャンペーンメンバー、カスタムオブジェクト | ほぼすべてのオブジェクト |
| 自動化 | できない | できる（コマンドラインで定期実行、夜間インポートなど） |
| 使う場面 | 5万件未満、対象がウィザードに対応、自動化不要 | 5万件以上、ウィザード非対応のオブジェクト、定期的な読み込み |

- 1億5,000万件を超える場合は、パートナー製品（AgentExchange）を使う
- データローダは通常 **SOAP API** を使う。大量データを速く処理したいときは **Bulk API** に切り替える（並列処理で速い）
- 設定する場所: [設定] → クイック検索「データインポートウィザード」
- インポートの状況確認: [設定] → クイック検索「一括データ読み込みジョブ」。完了するとメールが届く

出典: [データのインポート](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_data_management/lex_implementation_data_import)

### インポート前の準備（試験に出やすい）

- インポートファイルを**クリーンアップ**する（重複の排除、不要な情報の削除、表記ゆれの修正）
- 必要なら事前に設定を変える（カスタム項目の作成、選択リストの値の追加、**ワークフロールールの一時的な無効化**など）
- まず**小さなテストファイル**で試す

### インポート時の動き（試験に出やすい）

| 対象 | 動き |
| --- | --- |
| 制限なし選択リスト | ファイルの値がそのまま入る |
| 制限付き選択リスト | 一致しないとデフォルト値が使われ、失敗することもある |
| 複数選択リスト | 値を**セミコロン**で区切る |
| チェックボックス | オン＝**1**、オフ＝**0** |
| 数式項目 | 参照のみなので**インポートできない** |
| 入力規則 | インポート前に実行される。**違反するレコードはインポートされない**。必要なら事前に入力規則を無効化する |
| 項目の対応付け | 対応付けていない項目は**インポートされない** |

出典: [データのインポート](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_data_management/lex_implementation_data_import)

### Dataloader.io

- MuleSoft 提供の、**ブラウザで使う**データローダ。インポート・エクスポート・更新ができる
- 無料版は 1万レコード/月、10MB まで
- SOQL でエクスポートするデータを絞り込める（例: `SELECT Id, Name FROM Account WHERE Type LIKE '%Customer%'`）

出典: [Dataloader.io でデータをエクスポートする](https://trailhead.salesforce.com/ja/content/learn/projects/import-and-export-with-data-management-tools/use-data-loader-to-export-data)

---

## 6-2. データのエクスポート（バックアップ）

| | データエクスポートサービス | データローダ |
| --- | --- | --- |
| 何か | ブラウザで使う、[設定] の中のサービス | PCにインストールして使うアプリ |
| 頻度 | 手動: **7日ごと**（ウィークリー）または **29日ごと**（マンスリー）。自動: 週次または月次のスケジュール | 自由。コマンドラインで自動化できる |
| 使う場面 | 定期的なバックアップ | 自動化、API で別システムと連携 |

- ウィークリーエクスポートは Enterprise / Performance / Unlimited Edition で使える。Professional / Developer Edition は 29日ごとのみ
- 出力は CSV を圧縮した **zip**。準備ができるとメールが届く
- zip ファイルは**メール送信から48時間後に削除**される
- 画像・ドキュメント・添付ファイルを含めるかを選べる
- 設定する場所: [設定] → クイック検索「データのエクスポート」→ [今すぐエクスポート] または [エクスポートをスケジュール]

出典: [データのエクスポート](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_data_management/lex_implementation_data_export)

---

## 6-3. 重複管理

### 一致ルール と 重複ルール

| | 一致ルール | 重複ルール |
| --- | --- | --- |
| 役割 | **何を重複とみなすか**（判定の条件） | **重複を見つけたらどうするか**（動作） |
| 標準で用意 | 法人取引先・取引先責任者・リードの3つ（個人取引先を有効にすると4つ目） | 法人取引先・取引先責任者・リード・個人取引先 |
| 設定する場所 | [設定] → 「一致ルール」 | [設定] → 「重複ルール」 |

- 一致ルールは**重複ルールとペアにしないと動かない**
- 1つの重複ルールに一致ルールは**最大3つ**。1つのオブジェクトに有効な一致ルールは**最大5つ**
- 一致の方法: **完全一致**（Margaret Chan と Margaret Chan）と**あいまい一致**（Margaret Chan と Maggie Chan）
- 重複ルールの動作: **ブロック**（作成・編集させない）または**許可してアラート**を出す
- 許可した場合、警告を無視して作られた重複をレポートで確認できる（カスタムレポートタイプで「重複レコード項目」を選ぶ）
- 対象: 法人取引先、個人取引先、取引先責任者、リード、カスタムオブジェクト

出典: [Salesforce でデータ品質を向上させる](https://trailhead.salesforce.com/ja/content/learn/modules/sales_admin_duplicate_management/sales_admin_duplicate_management_unit_1)、[重複データを防止する](https://trailhead.salesforce.com/ja/content/learn/modules/sales_admin_duplicate_management/sales_admin_duplicate_management_unit_2)

---

## 6-4. レポート

### レポートを作る前に

依頼は「売上上位の商品は？」のような**質問の形**で来る。質問をはっきりさせてから（収益か数量か、期間は、など）レポートの条件に落とし込む。

### レポートタイプ

- レポートの**テンプレート**。どのレコードと項目を使えるかが決まる
- 主オブジェクト＋関連オブジェクト（例: 商品が関連する商談）
- 標準レポートタイプで足りないときは**カスタムレポートタイプ**を作る
  - 例: 「活動が関連するリード」を作ると、**活動があるリードだけ**を表示できる（標準の「リード」だとすべてのリードが出る）
  - 「関連レコードの有無を問わない」設定にすると、関連がないレコードも含められる
- 設定する場所: [設定] → クイック検索「レポートタイプ」

### レポート形式

| 形式 | 使う場面 | ポイント |
| --- | --- | --- |
| 表形式 | リストを作る（メーリングリストなど） | 一番シンプル。ダッシュボードで使うには**行制限**が必要 |
| サマリー形式 | **行**でグループ化して集計 | 一番よく使われる。グラフも作れる |
| マトリックス形式 | **行と列**でグループ化して集計 | 一番詳細。例: 行＝完了予定月、列＝種別 |

- 「詳細行」をオフにすると集計だけを表示できる

### 検索条件

| 種類 | 何ができるか |
| --- | --- |
| 標準の検索条件 | 「表示」（私の取引先／すべての取引先）と日付の範囲 |
| 項目の検索条件 | 項目・演算子・値で絞り込む |
| 検索条件ロジック | `(1 OR 2) AND 3 AND NOT 4` のように組み合わせる。**項目の検索条件にだけ使える**（標準の検索条件には使えない） |
| クロス条件 | 子オブジェクトが「**関連する／関連しない**」で絞り込む。**例外レポート**に使う（例: 商談のない取引先、取引先のない取引先責任者） |
| 行制限 | 表形式で表示する行数の上限を決める |

- 「次の文字列と一致しない」は**レポートが遅くなる**ことがある
- 検索条件を**ロック**すると、レポートを見る人が値を変えられない
- 日付の範囲はできるだけ狭くすると速い

### その他

- **バケット項目**: レコードを「大・中・小」のように分類してグループ化する（例: 商談を金額で規模別に分ける）
- **エクスポート**: レポートの内容を CSV などで出力して、表計算ソフトで扱える

出典: [レポートとダッシュボードの概要](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_reports_dashboards/lex_implementation_reports_dashboards_overview)、[レポートの絞り込み](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_reports_dashboards/lex_implementation_reports_dashboards_filter_your_report)、[レポート形式](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_reports_dashboards/lex_implementation_reports_dashboards_report_formats)、[営業マネージャーとマーケティングマネージャー用のレポートとダッシュボードを作成する](https://trailhead.salesforce.com/ja/content/learn/projects/create-reports-and-dashboards-for-sales-and-marketing-managers)

---

## 6-5. ダッシュボード

- 1つのウィジェット＝**1つのソースレポート**
- ソースレポートに**結合レポートと履歴トレンドレポートは使えない**

### ウィジェットの種類

| 種類 | 使う場面 |
| --- | --- |
| グラフ | データをグラフで見せる |
| ゲージ | 1つの値を**目標の範囲**の中で見せる |
| メトリクス | **1つの重要な値**だけを見せる |
| テーブル | レポートのデータを列で見せる |

### 実行ユーザー（試験に出やすい）

| | 通常のダッシュボード | 動的ダッシュボード |
| --- | --- | --- |
| 誰の権限でデータを表示するか | **指定した1人のユーザー**（実行ユーザー） | **見ている本人**（ログインユーザー） |
| 注意点 | 閲覧者全員に実行ユーザーの権限でデータが見える。見せすぎに注意 | **非公開フォルダーには保存できない** |
| 使う場面 | チーム全員に同じ数字を見せたい | 人によって見える範囲を変えたい。ダッシュボードの数を減らせる（例: 45個→2個） |

- 「私のチームのダッシュボードの参照」または「すべてのデータの参照」権限を持つマネージャーは、部下として表示をプレビューできる
- 設定する場所: ダッシュボードのプロパティ → [次のユーザーとしてダッシュボードを参照] → [ダッシュボード閲覧者]

### レポートグラフ

ダッシュボードを作らず、**レポートの上にグラフを1つ**表示したいときに使う。

出典: [ダッシュボードでデータを視覚化する](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_reports_dashboards/lex_implementation_reports_dashboards_visualizing_data)

---

## 6-6. フォルダーとアクセス権

- レポートとダッシュボードは**フォルダー**に保存する。フォルダーで誰がアクセスできるかを決める
- アクセス権のレベル: **参照・編集・管理**
- 共有先: ロール、ロール & 内部下位ロール、公開グループ、テリトリー、ライセンスの種類 など
- 公開・非表示・共有を選べる
- ダッシュボードのウィジェットを見るには、**元のレポートへのアクセス権も必要**

出典: [レポートとダッシュボードの概要](https://trailhead.salesforce.com/ja/content/learn/modules/lex_implementation_reports_dashboards/lex_implementation_reports_dashboards_overview)
