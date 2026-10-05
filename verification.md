# 検証手順

## Dependabot 自動処理（2026-09-23）

`.github/workflows/dependabot-automation.yml` を actionlint で検査し、PR 用 workflow 名（CI、Dependency Review）と一致することを確認する。Dependabot の patch／minor かつ全 PR チェック成功の場合だけ取り込み、major・古い SHA・再失敗は残す。

実際の Dependabot PR がまだない場合、動作経路は未検証として扱う。実 PR 発生後に自動化ジョブ、CI の再試行、マージ結果を確認する。

大量の Dependabot PR により CI 完了より分類が遅れる場合でも、分類後の `workflow_dispatch` が現在の PR 番号と head SHA を照合して再評価する。別の作成者、古い SHA、未完了の CI はマージしない。

## Dependabot メジャー更新の手動処理（2026-09-23）

- #1〜#6・#9（いずれもメジャー更新）は自動取り込み対象外のため、`gh pr checks` で全チェック成功を確認してから `gh pr merge --squash` で順次マージした。ワークフローファイルが重なるPRでも競合は発生しなかった。
- マージ後に `requirements.txt` の更新版をインストールし、ローカルで `mypy src/`、`ruff check src/`、`black --check src/`、`pytest tests/` を実行して全合格を確認した（pytest 33件合格）。

## 2026-10-05: GitHub受付・READMEの整備（公開前）

- 比較元: `0d3d31c00bc0e6703e8ee1cc0268dd5e8f03fe99`（`main`）。
- 受付フォーム 2 件のYAML構造、重複キー・ID、入力型、選択肢、予約ファイル名を一括検査し、エラー0件。
- 既存の固有質問・入力例・必須条件を原文と照合。READMEのリンク・画像・コマンド・条件を確認し、裏付けがある誤記だけを訂正した。
- 既存のCI、Dependabot、labeler、ライセンスのファイル内容は比較元から変更していない。
- 製品のビルド・インストール・実機操作、GitHub上のフォーム表示、公開後CIは今回の静的検証に含めない。公開後に実際の受付表示と必要ラベルの適用を確認する。
