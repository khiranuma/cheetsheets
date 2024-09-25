# GitHub Release 関連

## Release Note を手動作成する

- リリースノートを手動作成する

  ```bash
  gh release create v0.1.0 --title "v0.1.0" --notes "# Release Note
  ## Super Useful features
  | col1 | col2 |
  | --- | --- |
  | a1 | a2 |
  "
  ```

  - `v0.1.0`というタグが作成される
  - `--title`はリリースノートのタイトル
  - `--notes`はリリースノートの本文を Markdown で記載できる
  - アセットには自動的にソースコードが載る

- リリースノートにアセットを追加する

  ```bash
  gh release upload <release tag> example.txt
  ```

- リリースノートを Pull Request から自動生成する
  - Pull Request に基づき自動作成できる.
  - .github/release.yml が存在する前提
  ```bash
  gh release create v0.1.0 --title "v0.1.0" --generate-notes
  ```
