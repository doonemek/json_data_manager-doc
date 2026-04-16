# データ処理モジュール

# 1. 重複排除 (Deduplicator)
## 定義
`processors/deduplicator.py` にて定義

## 概要と仕様
複数のデータから読み込んだ全データから識別キー (`dedup_key`) が重複排除したリストを作成
- 入力データ
  - input フォルダ:  新規データ候補リスト
  - master フォルダ: マスターデータ (全重複排除済みデータ)
- 出力データ
  - output フォルダ: 分割した重複排除した新規データ 
  - master フォルダ: 重複排除した新規データを追記したマスターデータ
- 整合性
  - 最初に読み込まれたデータを優先保持

## アルゴリズム
Python の `set` 型を利用した高速アルゴリズムを採用
1. `unique_data` (`list`) と `seen_ids` (`set`) を用意
2. データを走査し、`seen_ids` にないデータを `unique_data` に追加し、`seen_ids` に登録
3. 存在する場合は無視する

上記により、データ件数が肥大化しても、ある程度無視できる高速化処理の実現している

# 2. データ分割 (Splitter)
## 定義
`processors/splitter.py` にて定義

## 概要と仕様
重複排除したデータリストを特定キー (`split_key`) のグループが、別ファイルに分かれないように処理しつつ、設定された件数 (`split_num`) を目安に分割する
- 入力データ
  - ソート済み重複排除したデータリスト
- 制約
  - 同じ特定キー (`split_key`) を持つデータ行は、同じファイルに格納されるようにグループ化してから分割する

## ファイル生成ロジック
分割は 2 ステップで実施される
1. 特定キーのグループ化: 隣接するデータと特定キー (`split_key`) を比較し、同値の場合同じグループとしてまとめる
2. チャンク化: 作成されたグループを設定された件数 (`split_num`) を超える数にまとめ、最終的な出力ファイル単位リストを作成する

上記ロジックにより、ファイルサイズを抑えつつ「関連データが複数ファイルに分散しない」ように、処理している


# 3. ファイル連携のフロー
データ入力から加工処理、そして分割保存までの全体の流れを示す

```mermaid
graph TD
    subgraph Input
        A[Existing Master Data]
        B[New Input JSONs]
    end

    A --> C[Combine to List]
    B --> C

    subgraph Processing
        C --> D[Deduplicate]
        D --> E[Sort by split_key]
        E --> F[Split by Groups]
    end

    subgraph Output
        D --> J[New Master Data]
        F --> G[split-01_id_min-max.json]
        F --> H[split-02_id_min-max.json]
        F --> I[...]
    end
