# codex-cli `.rules` 設定調査レポート

## 調査対象

- codex-cli の設定ファイル（`.rules`）で指定できる項目と役割

## 参照元

- codex リポジトリの `codex-rs/execpolicy/README.md`
- codex リポジトリの `shell-tool-mcp/README.md`

## `.rules` ファイルの役割

- `.rules` はコマンド実行ポリシー（execpolicy）を定義するファイルで、コマンドのトークン先頭一致に基づいて許可・承認要求・拒否を決定する。
- ルールに一致しないコマンドは許可扱いだが、Bash サンドボックスの制約は残る。

## 設定できる項目（`prefix_rule`）

- `pattern`: 先頭から一致させるトークン列を指定する。要素に配列を入れると代替トークン（OR）になる。
- `decision`: 一致時の扱いを `allow` / `prompt` / `forbidden` で指定する。未指定時は `allow`。
- `justification`: ルールの理由を説明する任意文言。承認や拒否のメッセージに使われる可能性がある。
- `match`: このルールに一致すべき例（文字列またはトークン配列）。読み込み時に検証される。
- `not_match`: 一致してはいけない例（文字列またはトークン配列）。読み込み時に検証される。

## 判定の優先度

- 複数ルールに一致した場合は、最も厳しい決定が優先される（`forbidden` > `prompt` > `allow`）。

## 例

```starlark
prefix_rule(
    pattern = ["git", ["status", "diff"]],
    decision = "prompt",
    justification = "Git操作は事前確認を行う",
    match = ["git status"],
    not_match = ["git push"],
)
```
