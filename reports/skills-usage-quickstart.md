# Codex Skills 利用まとめ（新規メモ）

Codexの実験的機能「Skills」を使うと、ローカルに置いた `SKILL.md` を会話のコンテキストへ素早く差し込めます。過去メモを踏まえ、導入から呼び出しまでを最小ステップでまとめました。

## 前提
- 機能はデフォルト無効。破壊的変更があり得る実験版です。
- 設定・ファイル配置はすべてローカル (`$CODEX_HOME` は通常 `~/.codex`) で完結します。

## 有効化手順
1. 設定にフラグを追加して再起動する（推奨）。
   ```toml
   [features]
   skills = true
   ```
   *一度だけ試す場合は* `codex --enable skills` でも可。
2. 起動時に `~/.codex/skills/**/SKILL.md` を再帰的に探索します。隠しエントリ・シンボリックリンクは無視され、ファイル名は `SKILL.md` 固定です。

## `SKILL.md` の最低条件
- YAMLフロントマター必須。
- フィールド: `name`（非空・100文字以内）、`description`（非空・500文字以内）。改行を含めず1行で書く。
- 本文は任意（コンテキストへは自動注入されない）。
- 例:
  ```markdown
  ---
  name: my-skill
  description: when and how to use my skill (<=500 chars)
  ---

  # Optional body
  Add examples, scripts, notes, etc.
  ```

## 検証と読み込みの挙動
- 起動時にすべてのスキルを検証。無効なファイルがあると、TUIがブロッキングモーダルでエラー内容とパスを表示し、そのスキルを無視して続行します。
- 有効なスキルがある場合のみ、ランタイムの `AGENTS.md` 末尾に `## Skills` セクションが追加され、`- <name>: <description> (file: /abs/path/to/SKILL.md)` 形式で列挙されます。

## 呼び出し方
- 会話メッセージ中で `$<skill-name>` を書くと、そのスキルが参照されます。
- TUIで `/skills` を実行すると有効スキル一覧が表示され、選択してメッセージへ挿入できます。

## 最小セットアップ例
1. ディレクトリとファイルを用意:
   ```bash
   mkdir -p ~/.codex/skills/hello-skill
   cat > ~/.codex/skills/hello-skill/SKILL.md <<'SKILL'
   ---
   name: hello-skill
   description: provide a friendly hello and usage reminder
   ---

   Greet the user and explain what this skill can do.
   SKILL
   ```
2. Codexを再起動 → `/skills` で確認 → メッセージに `$hello-skill` を記載して呼び出す。

## トラブルシュートのヒント
- スキルが表示されない: `skills = true` 設定の反映とファイル名 `SKILL.md` 固定を確認。
- 検証モーダルで止まる: YAML欠落や文字数超過がないか確認。問題のスキルを修正または削除すると起動が進みます。
- 表示順: パスを名前順→パス順でソートした順に列挙されます。
