# Codex Skills機能のメモ

## 導入バージョン
- `docs/skills.md`が追加されたコミット `feat: experimental support for skills.md (#7412)` は、タグ `rust-v0.65.0` 以降に含まれており、技能（Skills）機能はv0.65.0で初めて導入された。対応タグには`rust-v0.65.0`以降の正式版・アルファ版が含まれる。

## 使い方の要点
- 状態: "Skills"は実験的・非安定機能であり、破壊的変更の可能性がある。

### 有効化
- デフォルトでは無効。設定ファイルに以下を追加して再起動するのが推奨。`$CODEX_HOME`は通常`~/.codex`。
  ```toml
  [features]
  skills = true
  ```
- 一度だけ使う場合は`codex --enable skills`で起動する。

### 配置場所・読み込み
- v1では`~/.codex/skills/**/SKILL.md`配下を再帰的に走査する。隠しエントリ・シンボリックリンクは除外され、ファイル名は`SKILL.md`固定。名前順→パス順で並ぶ。
- 起動時に一度だけロードし、有効なスキルがあればランタイムの`AGENTS.md`末尾に`## Skills`セクションを追加し`- <name>: <description> (file: /absolute/path/to/SKILL.md)`形式で列挙する。有効なスキルが無い場合はセクションごと省略される。

### SKILL.mdの書式
- YAMLフロントマター＋本文。必須フィールドは`name`（非空・100文字以内・1行化）と`description`（非空・500文字以内・1行化）。追加キーは無視され、本文はコンテキストへは注入されない。
- 無効なスキル（YAML欠落、空欄、文字数超過など）があるとTUI起動時にブロッキングモーダルでパスとエラーが表示され、無効スキルは無視される。モーダルを閉じるか終了するまで先へ進まない。

### 呼び出し方法
- メッセージ中で`$<skill-name>`と記述して参照する。TUIでは`/skills`コマンドから一覧表示・挿入が可能。

### 作成手順例
1. `mkdir -p ~/.codex/skills/<skill-name>/`
2. `SKILL.md`を置く:
   ```
   ---
   name: your-skill-name
   description: what it does and when to use it (<=500 chars)
   ---

   # Optional body
   Add instructions, references, examples, or scripts (kept on disk).
   ```
3. `name`/`description`の文字数と改行無しを守る。
4. Codexを再起動するとスキルが読み込まれる。
