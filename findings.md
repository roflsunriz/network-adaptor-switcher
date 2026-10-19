# 調査知見

- `pip-audit` は現在のPython環境全体を検査するため、エージェント環境由来のpackageが混ざる。判定は `requirements.txt` 由来の依存経路を確認して行う。
- 品質検査のPython matrixとWindows EXE生成は別runner・別成果物で完結する。EXEをmatrix完了待ちへ戻さず並列化し、品質matrixのfail-fastを維持する。
- mypyのmajor更新は更新後requirementsを先に導入してからstrict検査する。古い環境のmypy結果で互換性を判断しない。
