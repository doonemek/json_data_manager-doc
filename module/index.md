# モジュール設計 概要

本システムは、責務ごとに以下の2つの大きなモジュール群に分かれています。

## 1. [データ処理 (Data Processing)](data_processing.md)
データの論理的な加工を担当します。
- 重複排除 (`deduplicator.py`)
- データ分割・グループ化 (`splitter.py`)

## 2. [入出力・ユーティリティ (Data Read and Save)](data_read_and_save.md)
ファイルシステムとの対話と、物理的なファイル管理を担当します。
- JSONファイルの読み込み・保存 (`json_handler.py`)
- 命名規則に基づくファイル名生成 (`deduplicator.py`) (将来的に分離予定)