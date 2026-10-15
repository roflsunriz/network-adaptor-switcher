# network-adaptor-switcher の実現手段と調査知見

作業着手前にこの文書を全文読む。共通方針と目的・行動義務・禁止事項は [AGENTS.md](AGENTS.md) を参照する。

## 共通方針に対する固有の実現手段

- 対象の共通ルール: 設計と実装方針の型付き言語・形式検証、検証。
- 理由: Windowsのネットワーク管理GUIをPythonで実装しているため。
- 適用範囲: Python製本体とWindows配布。
- 代替手段・検証: mypy・ruff・black・pytestとPyInstallerビルドを既存CIで確認する。
- 文書構成: 実現手段・環境・過去の調査知見はこの文書を正本とする。利用者向け導入・使用法はREADME、更新の入口はhow-to-update、実測結果はverification、変更意図はCHANGELOGに残す。共通方針の品質・秘密情報保護・ユーザー指示を免除しない。
- 移行基準: 2026-10-16の既定ブランチ `d949b70738ee3f0fc5536ea56a9fc08c91a51d71`。以下は出典の本文を移動したもので、過去の確認日時・適用条件・未検証事項を維持する。


<a id="agents-3"></a>

移動元: [AGENTS.md](AGENTS.md)。

## 作業で判明した重要事項

- Dependabotのメジャー更新PR（#1〜#6・#9）は自動取り込み対象外のため、`gh pr checks` で全件passを確認してから `gh pr merge --squash` で順次マージする。`ci.yml`・`release.yml`・`dependency-review.yml` に重なる差分でも、squashマージは競合なく連続適用できた（2026-09-23確認）。
- mypy 1.13.0→2.3.1のメジャー更新後も、ソース変更なしで `mypy src/`（strict）が合格する。検証時は `requirements.txt` の更新版を先にインストールすること。
- `pip-audit` はプロジェクト依存ではなく環境全体を検査対象にするため、結果にagent環境由来のパッケージが混ざる。判定は `requirements.txt` 由来（mypy、ruff、black、pytest系、python-dotenv）に絞って行う。


<a id="readme-5"></a>

移動元: [README.md](README.md)。

## 実行ファイル（EXE）のビルド

### クイックビルド

次のPowerShellスクリプトでビルドします。

```powershell
.\build.ps1
```

ビルドが成功すると、`dist\NetworkAdapterSwitcher.exe` が生成されます（約12MB）。

生成されたEXEファイルを右クリック → 「管理者として実行」で起動してください。

詳細なビルド手順とオプションについては、[BUILD.md](BUILD.md)を参照してください。


<a id="readme-6"></a>

移動元: [README.md](README.md)。

## 開発者向け

### コード品質チェック

#### 型チェック

```powershell
mypy src/
```

#### リンターチェック

```powershell
ruff check src/
```

#### フォーマットチェック

```powershell
black --check src/
```

#### 自動フォーマット

```powershell
black src/
ruff check --fix src/
```

### テスト実行

```powershell
pytest tests/
```

カバレッジレポート付き：

```powershell
pytest --cov=src --cov-report=html
```

### プロジェクト構造

```
network-adaptor-switcher/
├── src/
│   ├── __init__.py
│   ├── main.py              # エントリーポイント
│   ├── network_manager.py   # ネットワークアダプター管理
│   ├── gui.py               # GUI実装
│   └── models/
│       ├── __init__.py
│       └── models.py        # 型定義
├── tests/                   # テストコード
├── requirements.txt         # 依存パッケージ
├── pyproject.toml          # プロジェクト設定
├── .gitignore              # Git除外設定
├── .env.example            # 環境変数例
└── README.md               # このファイル
```


<a id="readme-10"></a>

移動元: [README.md](README.md)。

## CI/CD

このプロジェクトはGitHub Actionsを使用して自動テストとリリースを行っています。

### 自動テスト

- **型チェック**: mypy
- **リンター**: ruff
- **フォーマット**: black
- **単体テスト**: pytest
- **カバレッジ**: codecov

すべてのプルリクエストは自動的にテストされます。

### 自動リリース

タグ（`v*.*.*`）をプッシュすると、次の処理が自動で実行されます。

1. コード品質チェック
2. EXEファイルのビルド
3. GitHubリリースの作成
4. 成果物のアップロード


<a id="readme-13"></a>

移動元: [README.md](README.md)。

## 依存更新の自動処理

Dependabot は対象の依存関係を毎週確認します。patch／minor 更新は PR のチェック（CI、Dependency Review）が成功した後に自動で squash merge されます。CI の失敗ジョブは 1 回だけ再実行します。再失敗した PR は残して手動で修正します。major 更新は手動で確認します。


<a id="update-1"></a>

移動元: [how-to-update.md](how-to-update.md)。

## Dependabot PR の更新

前提は `.github/dependabot.yml` と PR 用 CI（CI、Dependency Review）です。更新 PR の head SHA と `gh pr checks <PR番号>` の結果を確認してください。patch／minor は全チェック成功後に自動取り込みされます。初回 CI 失敗は failed jobs のみを 1 回再実行し、再失敗した PR は残して手動で修正します。

設定を変えたときは `actionlint .github/workflows/dependabot-automation.yml` と実際の PR の Actions 結果を確認します。問題があれば呼び出し先の共通 workflow SHA を直前の検証済み値へ戻すコミットを push します。取り込まれた依存更新に問題があれば通常の revert コミットで復旧します。

CI 完了より Dependabot の分類が遅れる場合は、`callback_workflow_file` が指す呼び出し側 workflow を `workflow_dispatch` し、同じ PR 番号・head SHA・全チェックを再確認する。呼び出し側のファイル名を変える際はこの入力も一緒に更新する。
