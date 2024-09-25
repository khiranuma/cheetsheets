# sed(ストリームエディタ)関連コマンド

# p コマンド

- ファイル全体を表示する

  ```bash
  sed -n 'p' <filename>
  ```

- ファイルの指定行を表示する

  ```bash
  sed -n '3p' <filename>
  ```

- ファイルの 2 行目から 4 行目を表示する

  ```bash
  sed -n '2,4p' <filename>
  ```

- ファイルの２行目から最後まで表示する

  ```bash
  sed -n '2,$p' <filename>
  ```

- First で始まる行を表示する

  ```bash
  sed -n '/^First/p' <filename>
  ```

## s コマンド(置換)

- ファイル内の'second'を'Second'に置換する(置換した行のみ表示)
  ```bash
  sed -n 's/second/Second/p' <filename>
  ```
- ファイル内の'second'を'Second'に置換する(ファイル全体を表示)

  ```bash
  sed -e 's/second/Second/' <filename>
  ```

- １行に複数の置換対象文字列があった場合すべて置換

  ```bash
  sed -e 's/second/Second/g' <filename>
  sed -n 's/second/Second/gp' <filename>
  ```

- 行を指定して置換

  ```bash
  sed -e '2,3s/second/Second/g' <filename>
  ```

- 正規表現(A で始まる)にマッチする行だけ置換

  ```bash
  sed -e '/^A/ s/second/Second/g' <filename>
  ```

- 置換文字列に`/`が含まれる場合(`@`以外の文字を使うことも可能)

  ```bash
  sed -e '/^root/ s@/bin/bash@/bin/sh@g' <filename>
  ```

## d コマンド(削除)

- 行数を指定して削除(２行目を削除)
  ```bash
  sed -e '2d' <filename>
  ```
- 2 行目から４行目まで削除

  ```bash
  sed -e '2,4d' <filename>
  ```

- 2 行目から最後まで削除

  ```bash
  sed -e '2,$d' <filename>
  ```

- 正規表現(A から始まる)にマッチした行を削除
  ```bash
  sed -e '/^A/ d' <filename>
  ```

## i コマンド(Insert)

- 指定した行(3 行目)の前に行を挿入
  ```bash
  sed -e '3i\This line was Inserted.' <filename>
  ```
- 正規表現にマッチした行の前に行を挿入（複数マッチしたら各行の前に挿入される）
  ```bash
  sed -e '/^A/ i\This line was Inserted.' <filename>
  ```

## a コマンド(Append)

- 指定した行(3 行目)の後に行を追加

  ```bash
  sed -e '3a\This line was Appended.' <filename>
  ```

- 正規表現にマッチした行のあとに行を追加（複数マッチしたら各行の後に追加される）

  ```bash
  sed -e '/^A/ a\This line was Appended.' <filename>
  ```

## y コマンド(文字置換)

## -i オプション

```

```

## -f オプション
