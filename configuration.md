# 設定仕様 (Configuration)

本システムは `config/settings.json` を通じて、外部ファイルのパスや分割ロジックの挙動を制御します

## 1. 設定項目詳細

### 共通設定 (common セクション)

| キー | 型 | 説明 |
| :--- | :--- | :--- |
| `data_root` | string | 扱うデータ格納ルートパス |
| `input_json_file_dir` | string | 新規入力ファイル格納パス |
| `master_json_file_dir` | string | マスターデータファイル格納パス |
| `output_json_file_dir` | string | 出力ファイル格納パス |
---

### 重複排除設定 (deduplicator セクション)

| キー | 型 | 説明 |
| :--- | :--- | :--- |
| `output_master_file_prefix` | string | 出力されるマスターデータファイル接頭辞 |
| `analysis_keys` | list[string] | ソート操作などで使用するキー (0番目から優先的にソートsされる) |
| `dedup_key` | string | 重複排除する制御キー名 |
| `pickup_key` | string | ファイル生成時に分割数の基準となるキー名 |
---


### 分割設定 (splitter セクション)

| キー | 型 | 説明 |
| :--- | :--- | :--- |
| `split_key` | integer | 分割数の基準となるキー名 |
| `split_num` | string | 1ファイルあたりの `split_key` 数 (下限) |
| `output_json_file_pefix` | string | 出力ファイルの接頭辞 |
---


## 2. 動的プレースホルダーの活用

`output_json_file_prefix` では、以下の特別なキーワードを使用できます。

### `{file_counts}`
生成される総ファイル数に基づき、適切な桁数で **0埋め（パディング）** された連番に置換されます。
- **例**: `split-{file_counts}` と設定し、ファイルが 150 個生成される場合、ファイル名は `split-001...`, `split-010...`, `split-150...` となります。

---

## 3. 運用のポイントとアドバイス

### `split_num` の調整
この数値は単純な「行数」ではなく、**「重複を除いたユニークな ID の数」**を指します。
- **設計意図**: 1つの ID に対して複数行のデータがある場合でも、それらが複数ファイルに別れにならないよう、ID 単位でカウントします。
- **推奨値**: 閲覧性やメモリ負荷を考慮し、まずは `10000` 程度を基準に調整してください。(仮)

### ディレクトリの管理
- `input_json_file_dir` や `output_json_file_dir` に指定したパスが実在しない場合、プログラム実行時に自動で作成されます。
- パスはプロジェクトルートからの相対パスでの記述を推奨します。

---

## 4. 設定サンプル (`config/settings.json`)

```json
{
  "common": {
    "data_root": "./data",
    "input_json_file_dir": "${common.data_root}/input",
    "master_json_file_dir": "${common.data_root}/master",
    "output_json_file_dir": "${common.data_root}/output"
  },
  "deduplicator": {
    "output_master_file_prefix": "master",
    "analysis_keys": ["id"],
    "dedup_key": "id",
    "pickup_key": "id"
  },
  "splitter": {
    "split_num": 10000,
    "split_key": "id",
    "output_json_file_pefix": "split-{file_counts}"
  }
}