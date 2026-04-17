# 入出力・ユーティリティ

## 1. JSON ハンドラ (`json_handler.py`)
ファイルシステム上の JSON データに対するアクセスをカプセル化します。

- **`load_json` / `save_json`**: 
    - 文字コード `utf-8` を強制。
    - 保存時は `indent=4` を適用し、人間が読みやすい形式を維持。

## ファイル名生成 (`file_namer.py`)
分割保存時のファイル名を決定する重要なロジックです。

- **`generate_filename`**:
    - リスト内の `split_key` の最小値と最大値を自動抽出。
    - `{prefix}_{min}-{max}.json` の形式で出力。
      - 新規データ: `{prefix}` => `split-{file_count}_{key}` 
      - マスターデータ: `{prefix}` => `master-`
    - これにより、ファイルを開かずに中身の ID 範囲を把握可能にする。