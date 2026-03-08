# OpenShortcutKey

Windows でグローバルホットキーを使い、URL起動やコマンド実行を呼び出すツールです。

## 動作環境
- OS: Windows
- Python: 3.11.9

> `app/shortcut_key_listener.py` は Windows API（`RegisterHotKey`）を利用します。

## セットアップ
プロジェクトルートで以下のどちらかを実行します。

```bash
python setting.py
```

または

```bash
pip install -r requirements.txt
```

### 動作確認バージョン
- streamlit 1.54.0
- keyboard 0.13.5
- pystray 0.19.5

---

## まず最初に（重要）
このプロジェクトの各アプリは、設定ファイル `shortcut_config.json` を**相対パス**で扱います。
そのため、実行時は `app` ディレクトリに移動してください。

```bash
cd app
```

また、常駐リスナーは `shortcut_config.json` が無いと待機状態になります。
初回は以下のどちらかを先に行ってください。

- WebUI（`shortcut_key_setting_webui.py`）を起動して保存する
- `../config/shortcut_config.json` を `./shortcut_config.json` としてコピーする

---

## `app` 配下アプリの使い方

## 1. 設定 WebUI
**ファイル:** `app/shortcut_key_setting_webui.py`

ショートカット設定の追加・編集・保存を行うアプリです。

### 起動
```bash
python -m streamlit run shortcut_key_setting_webui.py
```

### できること
- ショートカットの追加 / 編集 / 削除
- `action_type` の選択
  - `open_url`: Chrome で URL を開く
  - `run_cmd`: コマンド実行
  - `open_cmd`: 新しい `cmd.exe` を開く
- `shortcut_config.json` の保存
- 監視開始 / 停止（保存済み設定で起動）

---

## 2. 常駐ホットキーリスナー
**ファイル:** `app/shortcut_key_listener.py`

グローバルホットキーを監視し、設定に応じたアクションを実行します。

### 起動
```bash
python shortcut_key_listener.py
```

### 挙動
- `shortcut_config.json` の更新を監視し、自動再読み込み
- トリガー時に `last_trigger.txt` を更新
- `Ctrl + C` で停止

---

## 3. F13〜F16 キー送信 GUI（テスト用）
**ファイル:** `app/f13_f16_key_sender_gui.py`

F13/F14/F15/F16 を疑似送信し、ホットキー動作確認を行うための GUI です。

### 起動
```bash
python f13_f16_key_sender_gui.py
```

### 用途
- 常駐リスナー起動中に GUI ボタンから F13〜F16 を送信して、設定動作を確認

---

## 動作確認の流れ
1. `cd app`
2. WebUI を起動し、例として `f13` に `open_url` + `https://chat.openai.com` を保存
3. 別ターミナルで常駐リスナーを起動
4. 実キーボードまたは F13〜F16 送信 GUI で `f13` を送信
5. Chrome が開けば成功

## トラブルシュート
- **キーが反応しない**: 管理者権限で実行が必要な場合があります。
- **Fn キーが効かない**: 機種によって `Fn` は OS に届きません。`ctrl+alt+f1` などで確認してください。
- **Chrome が開かない**: `run_cmd` に切り替え、Chrome のフルパスを指定してください。
  - 例: `"C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe" https://chat.openai.com`
