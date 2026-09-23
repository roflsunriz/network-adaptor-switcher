# 検証手順

## Dependabot 自動処理（2026-09-23）

`.github/workflows/dependabot-automation.yml` を actionlint で検査し、PR 用 workflow 名（CI、Dependency Review）と一致することを確認する。Dependabot の patch／minor かつ全 PR チェック成功の場合だけ取り込み、major・古い SHA・再失敗は残す。

実際の Dependabot PR がまだない場合、動作経路は未検証として扱う。実 PR 発生後に自動化ジョブ、CI の再試行、マージ結果を確認する。

大量の Dependabot PR により CI 完了より分類が遅れる場合でも、分類後の `workflow_dispatch` が現在の PR 番号と head SHA を照合して再評価する。別の作成者、古い SHA、未完了の CI はマージしない。

## Dependabot メジャー更新の手動処理（2026-09-23）

- #1〜#6・#9（いずれもメジャー更新）は自動取り込み対象外のため、`gh pr checks` で全チェック成功を確認してから `gh pr merge --squash` で順次マージした。ワークフローファイルが重なるPRでも競合は発生しなかった。
- マージ後に `requirements.txt` の更新版をインストールし、ローカルで `mypy src/`、`ruff check src/`、`black --check src/`、`pytest tests/` を実行して全合格を確認した（pytest 33件合格）。
