# AGENTS.md

## 作業開始前の必須手順（最優先・例外なし）

1. エージェントは、調査、計画、コマンド実行、スキル利用、ファイル編集、コミット、プッシュを始める前に、必ずリポジトリ直下の `.\COMMON-AGENTS.md` を開き、先頭から末尾まで全文を読む。
2. `COMMON-AGENTS.md` はGit管理外のシンボリックリンクである。`git`や既定のignore設定が有効な`rg --files`の検索結果だけで、ファイルが存在しないと判断してはならない。PowerShellでは最初に次を実行する。

```powershell
Get-Content -Raw -LiteralPath .\COMMON-AGENTS.md
```

3. 読み取りに失敗した場合、出力が省略された場合、または末尾まで読めたことを確認できない場合は、一切の作業を開始せず、パスとシンボリックリンク先を確認して全文を再取得する。必要なら分割して末尾まで読む。
4. 全文を読了するまで、ローカル `AGENTS.md` だけを根拠に作業を続けてはならない。読了後は `COMMON-AGENTS.md` を最優先の指針とし、読了直後の最初の進捗報告で全文を読了したことを明示する。
このファイルでは `network-adaptor-switcher` 固有の補足だけを記載する。

## プロジェクト固有情報

- 作業前にこのリポジトリの `README.md`、設定ファイル、CI 定義を確認する。
- 追加のプロジェクト固有ルールが必要になった場合は、このファイルに追記する。

## 作業で判明した重要事項

- Dependabotのメジャー更新PR（#1〜#6・#9）は自動取り込み対象外のため、`gh pr checks` で全件passを確認してから `gh pr merge --squash` で順次マージする。`ci.yml`・`release.yml`・`dependency-review.yml` に重なる差分でも、squashマージは競合なく連続適用できた（2026-09-23確認）。
- mypy 1.13.0→2.3.1のメジャー更新後も、ソース変更なしで `mypy src/`（strict）が合格する。検証時は `requirements.txt` の更新版を先にインストールすること。
- `pip-audit` はプロジェクト依存ではなく環境全体を検査対象にするため、結果にagent環境由来のパッケージが混ざる。判定は `requirements.txt` 由来（mypy、ruff、black、pytest系、python-dotenv）に絞って行う。
