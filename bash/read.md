# read コマンド

## 文字列を出力して入力を促す

- 結果は`name`変数に格納される

  ```bash
  read -p "May I ask your name? " name
  echo ${name}
  ```

- 入力可能文字数を指定(1 文字を入力すると次のコマンドに進む)

  ```bash
  read -n1 -p "Press any key to exit."
  ```

- 入力された文字列を標準出力に出さない

  ```bash
  read -s -p "Password: " pass
  ```

## ファイルから 1 行ずつ読み取る

- ファイルから読み取る

  ```bash
  while read line
  do
    echo $line
  done < file.txt
  ```

- 標準入力から読み取りとファイルからの読み取りを同時に行う
  ```bash
  TTY=$(tty)
  while read line
  do
    read -p "Question: $line " ans < $TTY
    echo "Answer: $ans"
  done < questions.txt
  ```

## read で区切り文字を指定して配列に読み込む

- 実行結果は`1 2 3`が改行されて出力される
- `${version:-0.0.0}`の`:-`により`version`変数に値がない場合は`0.0.0`が初期値として代入される

  ```bash
  version=1.2.3
  IFS='.' read -ra token <<< ${version:-0.0.0}
  echo ${token[0]}
  echo ${token[1]}
  echo ${token[2]}
  ```
