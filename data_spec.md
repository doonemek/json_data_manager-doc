# データ構造

## 保存命名規則
- 出力フォーマット: {module}-{split_file_count}_{split_key}_{min_id}-{max_id}.json
  - ファイル分割数は0埋め (動的桁数ロジックは [data_processing.md](module/data_processing.md) 参照) 
  - 例) split-001_id_1_10000.json

- ログファイルフォーマット: process-{timestamp}.log
  - ログファイルには DEBUG レベル以上が出力され、確認時に読み込んだファイル名などを確認することができる
  - 例) process-20260416-202056.log

## ID 同一ファイルルール
- 1ファイルへのデータ数が split_num で制御している
- ただし、同一 split_key を持つデータは split_num を超えたとしても同一ファイルに保存される

## データ格納場所
```
data/
├── input  # 入力データ
├── output # 出力データ
├── master # マスターデータ
└── logs   # ログファイル
```