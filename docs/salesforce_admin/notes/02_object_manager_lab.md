# 2. オブジェクトマネージャーと Lightning アプリケーションビルダー（配点15%）

公式 Trailmix「Salesforce Platform アドミニストレーター認定試験に向けた準備」の「Object Manager and Lightning App Builder」の教材を読んでまとめたもの（2026-10-02 時点）。

- 読んだ教材: [データモデリング](https://trailhead.salesforce.com/ja/content/learn/modules/data_modeling)、[Lightning Experience のカスタマイズ](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization)、[Salesforce オブジェクトをカスタマイズする](https://trailhead.salesforce.com/ja/content/learn/projects/customize-a-salesforce-object)、[Lightning アプリケーションビルダー](https://trailhead.salesforce.com/ja/content/learn/modules/lightning_app_builder)、[数式と入力規則](https://trailhead.salesforce.com/ja/content/learn/modules/point_click_business_logic)、[組織をカスタマイズして新規ビジネスユニットをサポート](https://trailhead.salesforce.com/ja/content/learn/projects/customize-an-org-to-support-a-new-business-unit)（レコードタイプの部分）
- 読めなかった教材: Salesforce ヘルプの記事「Object Relationships Overview」「Notes on Changing Custom Field Types」（作業環境から表示できなかった）

---

## 2-1. データモデルの基本

| データベースの言葉 | Salesforce の言葉 |
| --- | --- |
| テーブル | **オブジェクト** |
| 列 | **項目** |
| 行 | **レコード** |

- オブジェクトの種類: 標準オブジェクト（取引先、商談など）、カスタムオブジェクト、外部オブジェクト、プラットフォームイベント、BigObjects
- カスタムオブジェクトを作ると、ページレイアウトなども自動で作られる
- カスタムの項目・オブジェクトの API 参照名は末尾が **`__c`**（例: `Price__c`）

### すべてのオブジェクトにある項目

| 項目 | 内容 |
| --- | --- |
| ID | 自動で付く **18文字**（大文字小文字を区別しない）。**15文字版**（区別する）もある。レコードの URL に含まれる |
| システム項目 | 作成日、最終更新者、最終更新日など（参照のみ） |
| 名前 | テキスト、または**自動採番**（例: CA-1024） |

### カスタマイズのコツ

- わかりやすい一意の名前を付ける（「物件2」のような名前は避ける）
- **説明**と**ヘルプテキスト**を付ける（ヘルプテキストはユーザーが項目の横のアイコンにカーソルを置くと表示される）
- 重要な項目は**必須**にする
- 標準項目の表示ラベルを変えたいとき: [設定] → 「**タブと表示ラベルの名称変更**」

出典: [オブジェクトの概要](https://trailhead.salesforce.com/ja/content/learn/modules/data_modeling/objects_intro)

---

## 2-2. オブジェクトの関係（リレーション）（試験に出やすい）

| | 参照関係 | 主従関係 | 階層関係 |
| --- | --- | --- | --- |
| つながり | 緩い | 強い | 参照関係の特別版 |
| 使う場面 | 関連する場合もしない場合もある（単独でも存在できる） | 子が**親なしでは存在しない** | ユーザーどうし（上司・部下など） |
| 親を削除すると | 子は残る | **子も削除される** | － |
| 子の共有設定 | 子が独自に持つ | **親の設定を引き継ぐ**（「親レコードに連動」） | － |
| 積み上げ集計項目 | 使えない | **親に作れる** | － |
| 使えるオブジェクト | どれでも | どれでも | **ユーザーオブジェクトだけ** |

- 参照関係は一対一・一対多ができる（例: 取引先と取引先責任者は一対多の参照関係）
- 主従関係の項目は**従（子）オブジェクトに作る**
- **スキーマビルダー**: データモデルを図で見る・編集するツール。オブジェクトや項目もここで作れる

出典: [オブジェクトリレーション](https://trailhead.salesforce.com/ja/content/learn/modules/data_modeling/object_relationships)、[スキーマビルダー](https://trailhead.salesforce.com/ja/content/learn/modules/data_modeling/schema_builder)

---

## 2-3. 項目を使ってデータを正しく入れてもらう機能

| 機能 | 何ができるか | ポイント |
| --- | --- | --- |
| 選択リスト | 決まった選択肢から選ばせる | 入力ミスが減り、データがきれいに保てる |
| **グローバル選択リスト値セット** | 同じ選択肢を**複数のオブジェクトで共有**する | 例: リードと取引先で同じ「地域」の選択肢を使う。[設定] → 「選択リスト値セット」 |
| **連動選択リスト**（項目の連動関係） | ある項目（**制御項目**）の値に応じて、別の選択リスト（**連動項目**）の選択肢を絞る | 制御項目にできるのは**選択リスト**（値が1〜300個）か**チェックボックス** |
| **ルックアップ検索条件** | ルックアップ（参照関係の項目）で**選べるレコードを絞る** | 条件に使えるもの: 同じレコードの他の項目、参照先レコードの項目、ユーザー・プロファイル・ロールの項目など。例: ケースには同じ取引先の取引先責任者だけを選ばせる |
| **入力規則** | 条件に合わない値で**保存させない** | 数式が **True を返すとエラー**（無効なデータ）になる |
| **項目履歴管理** | 項目の変更（日時・変更者・前後の値）を追跡する | 1つのオブジェクトで**最大20項目**。レコードの「履歴」関連リストや履歴レポートで見る |
| フィード追跡 | オブジェクトのレコードの変更を Chatter フィードで見られるようにする | [設定] → 「フィード追跡」 |

### 入力規則の例

| やりたいこと | 数式 |
| --- | --- |
| 取引先番号は数字だけ | `AND(NOT(ISBLANK(AccountNumber)), NOT(ISNUMBER(AccountNumber)))` |
| 今年の日付だけ | `YEAR(My_Date__c) <> YEAR(TODAY())` |
| 給与の幅は2万ドル以内 | `(Salary_Max__c - Salary_Min__c) > 20000` |

- 入力規則はオブジェクト、項目、キャンペーンメンバー、ケースマイルストーンに作れる
- 例: 「サポートプランあり」がオンなら有効期限を必須にする、商談が不成立なら理由を必須にする

出典: [選択リストと項目の連動関係](https://trailhead.salesforce.com/ja/content/learn/projects/customize-a-salesforce-object/picklists-field-dependencies)、[ルックアップ検索条件](https://trailhead.salesforce.com/ja/content/learn/projects/customize-a-salesforce-object/create-lookup-filters)、[項目履歴管理](https://trailhead.salesforce.com/ja/content/learn/projects/customize-a-salesforce-object/account-field-history-tracking)、[入力規則](https://trailhead.salesforce.com/ja/content/learn/modules/point_click_business_logic/validation_rules)、[組織をカスタマイズして新規ビジネスユニットをサポート](https://trailhead.salesforce.com/ja/content/learn/projects/customize-an-org-to-support-a-new-business-unit/modify-your-data-model)

---

## 2-4. 数式項目 と 積み上げ集計項目（試験に出やすい）

| | 数式項目 | 積み上げ集計項目 |
| --- | --- | --- |
| 何を計算するか | **1つのレコード**（と、そこから参照できる親）の項目 | **関連する子レコードの集まり** |
| 作れる場所 | どのオブジェクトでも | **主従関係の主（親）側だけ** |
| 計算の種類 | 自由（四則演算、関数） | **COUNT（件数）、SUM（合計）、MIN（最小）、MAX（最大）** |
| 値の入力 | できない（参照のみ。インポートもできない） | できない |

### 数式項目

- **クロスオブジェクト数式**: 親オブジェクトの項目を表示できる（例: 取引先責任者に取引先番号を表示）
- 戻り値のデータ型（通貨、数値、日付、テキスト、チェックボックスなど）を選んで作る
- よく使う関数: `TODAY()`（今日の日付）、`HYPERLINK()`（リンク）、`ROUND()`（四捨五入）、`AND()`、`OR()`、`NOT()`、`ISBLANK()`、`ISNUMBER()`、`LEN()`、`CONTAINS()`
- 例: 完了予定日までの日数 = `CloseDate - TODAY()`
- 「構文を確認」ボタンでエラーを確かめる。よくあるエラー: 括弧やカンマの不足、データ型の不一致、項目名の間違い

### 積み上げ集計項目

- SUM で使える項目: 数値、通貨、パーセント
- MIN / MAX で使える項目: 数値、通貨、パーセント、日付、日付/時間
- 例: 取引先に関連する商談の最も早い作成日（MIN）、商談に関連する商品の合計金額（SUM）

出典: [数式項目](https://trailhead.salesforce.com/ja/content/learn/modules/point_click_business_logic/formula_fields)、[積み上げ集計項目](https://trailhead.salesforce.com/ja/content/learn/modules/point_click_business_logic/roll_up_summary_fields)

---

## 2-5. レコードタイプとビジネスプロセス

- **レコードタイプ**: レコードを作るときに使う **ビジネスプロセス・選択リストの値・ページレイアウト** を決める
- **ビジネスプロセス**: 選択リストの段階をレコードタイプごとに変える
  - 商談 → **セールスプロセス**（フェーズ）
  - ケース → **サポートプロセス**（状況）
- 例: ケースに「商品サポート」と「請求」のレコードタイプを作り、それぞれ別のサポートプロセスと選択リストの値を使う
- レコードタイプは**プロファイル**に割り当てる。プロファイルではデフォルトのレコードタイプも決める

出典: [組織をカスタマイズして新規ビジネスユニットをサポート](https://trailhead.salesforce.com/ja/content/learn/projects/customize-an-org-to-support-a-new-business-unit/modify-your-data-model)、[レコードタイプの作成](https://trailhead.salesforce.com/ja/content/learn/projects/customize-a-salesforce-object/create-record-types)

---

## 2-6. 画面の見た目を決める機能（試験に出やすい）

### どの機能で何を決めるか

| 機能 | 決めるもの |
| --- | --- |
| **Lightning アプリケーションビルダー** | Lightning ページの**構成**（どのコンポーネントをどこに置くか）。動的フォームを使えば項目も |
| **ページレイアウト**（ページレイアウトエディター） | **項目**（動的フォームを使わない場合）、**関連リスト**とその列、**ボタン**、**カスタムリンク**、**クイックアクション** |
| **コンパクトレイアウト** | レコード上部の**強調表示パネル**に出す項目。カーソルを当てたときのカード、**モバイルアプリ**での表示にも使われる |
| 検索レイアウト | 検索結果やルックアップの結果に表示する項目 |

- 関連リストの追加や列の調整は、**ページレイアウトエディター**でしかできない
- カスタムオブジェクトを作ると、コンパクトレイアウトは「システムデフォルト」（名前の項目だけ）になる

### Lightning ページの種類

| 種類 | 使う場面 | 使える場所 |
| --- | --- | --- |
| アプリケーションページ | アプリのホームとして、よく使う情報をまとめる | Lightning Experience とモバイルアプリ。**追加できるアクションはグローバルアクションだけ** |
| ホームページ | ログイン後に最初に見るページ | **Lightning Experience だけ** |
| レコードページ | オブジェクトのレコードの画面 | Lightning Experience とモバイルアプリ |

### Lightning ページの有効化（割り当て方）

- 組織全体のデフォルトにする
- 特定の Lightning アプリのデフォルトにする
- **アプリ × レコードタイプ × プロファイル** の組み合わせに割り当てる（ホームページは**アプリ × プロファイル**）
- **フォーム要素**（デスクトップ／携帯電話）に割り当てる

保存しただけではユーザーに表示されない。**有効化**が必要。

### 動的フォーム

- レコードの詳細を、**項目やセクションごとのコンポーネント**に分けて自由に配置できる
- **表示ルール**で、条件に合うときだけ項目やセクションを表示する
- 表示ルールの条件に使えるもの: 項目の値、ユーザーのプロファイルや権限、デバイス（フォーム要素）
- メリット: ページレイアウトやレコードタイプの数を減らせる。項目が少ないとページが速くなる
- 項目の表示ルールは編集中にも**その場で**評価される。項目セクションの表示ルールは編集中には変わらない
- **クロスオブジェクト項目**（関連オブジェクトの項目）も配置できる
- **動的強調表示パネル**: 最大12項目。アクションもアプリケーションビルダーで設定できる
- 注意: 必須項目をページから外すと、ユーザーがレコードを保存できなくなる
- 対応していないオブジェクトもある（コンポーネントパネルに「項目」タブが出なければ非対応）

### Lightning コンポーネント

- 標準コンポーネント（Salesforce 製）、カスタムコンポーネント（自作。Lightning Web コンポーネントで作る）、AgentExchange のコンポーネント
- カスタムコンポーネントは、アプリケーションビルダーで使えるように設定されている必要がある

出典: [ページレイアウトと Lightning レコードページ](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_page_layouts)、[コンパクトレイアウト](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_compact_layouts)、[Lightning アプリケーションビルダーの紹介](https://trailhead.salesforce.com/ja/content/learn/modules/lightning_app_builder/lightning_app_builder_intro)、[動的フォーム](https://trailhead.salesforce.com/ja/content/learn/modules/lightning_app_builder/get-started-with-dynamic-forms-lab)、[表示ルール](https://trailhead.salesforce.com/ja/content/learn/modules/lightning_app_builder/add-visibility-rules-for-dynamic-pages-lab)、[アプリケーションページ](https://trailhead.salesforce.com/ja/content/learn/modules/lightning_app_builder/lightning_app_builder_apphome)

---

## 2-7. ボタン・リンク・アクション

### カスタムボタンとカスタムリンク

| 種類 | 表示される場所 |
| --- | --- |
| リストボタン | 関連リスト |
| 詳細ページリンク | レコードの詳細のリンクセクション（**動的フォームを使っていないページだけ**） |
| 詳細ページボタン | 強調表示パネルのアクションメニュー |

- 外部 URL や社内システムにつなげる。URL に項目の値を入れられる（例: `https://www.google.com/search?q={!Account.Name}`）
- 作った後、**ページレイアウトに追加しないと表示されない**

### クイックアクション（試験に出やすい）

| | オブジェクト固有のアクション | グローバルアクション |
| --- | --- | --- |
| 置ける場所 | そのオブジェクトのページ | **どこでも**（ヘッダーのグローバルアクションメニューなど） |
| 関連付け | 作成したレコードは**元のレコードに自動で関連付けられる** | 関連付けなし |
| 例 | 取引先から、その取引先に関連するエネルギー監査を作る | どこからでもキャンペーンを作る |
| 設定する場所 | オブジェクトマネージャーのオブジェクト → ボタン、リンク、およびアクション | [設定] → 「グローバルアクション」。表示は**グローバルパブリッシャーレイアウト** |

- アクションで使う項目は**アクションレイアウトエディター**で決める。必須項目を外すとアクションが完了できない
- ページレイアウトの「Salesforce モバイルおよび Lightning Experience のアクション」セクションで表示するアクションと順番を決める
- オブジェクトのページレイアウトでアクションをカスタマイズしていない場合は、グローバルパブリッシャーレイアウトのアクションが使われる

出典: [カスタムボタンとカスタムリンク](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_buttons_links)、[アクション](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_actions)

---

## 2-8. Lightning アプリとリストビュー

### Lightning アプリ

- ナビゲーションバーに表示するオブジェクトやタブをまとめたもの。色やロゴでブランド設定できる
- **ユーティリティバー**（画面下部のツール）も設定できる
- 割り当て先は**ユーザープロファイル**
- 作成・管理する場所: [設定] → 「アプリケーションマネージャー」
- カスタムオブジェクトをアプリに追加するには**カスタムタブ**が必要

### リストビュー

- ユーザーが自分で作れる（システム管理者に頼まなくてよい）
- 条件で絞り込み、列の表示を変え、その場でレコードを編集できる（鉛筆アイコン）
- **リストビューグラフ**: リストの内容をグラフ表示する（集計の種別: 合計・件数・平均）。「最近参照したデータ」リストでは使えない

出典: [Lightning アプリケーション](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_apps)、[リストビュー](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_list)、[カスタムオブジェクト](https://trailhead.salesforce.com/ja/content/learn/modules/lex_customization/lex_customization_custom_objects)
