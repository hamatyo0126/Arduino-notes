# Understanding the Difference Between `""` and `< >` in Arduino Includes

When including header files in Arduino (and C/C++), the two forms of `#include` behave differently and should be used carefully.

## `#include "xxx.h"`
This form tells the compiler to:

- Search the **sketch folder first**
- Then search the global library paths

Use this when the header file is part of your project — for example, a small custom module placed next to your `.ino` file.

## `#include <xxx.h>`
This form:

- Skips the sketch folder
- Searches **only the installed library directories**

Use this for official or third‑party libraries installed under `Arduino/libraries`.

## Key Points
- Use "" for **local project headers**, such as custom header files placed in the same folder as your sketch.
- Use `< >` for **installed libraries**.
- A header placed inside the sketch folder will **never** be found with `<xxx.h>` — it must be included with `"xxx.h"`.

This small distinction often causes confusion, especially when working with custom modules or reorganizing project files.




---

## Arduino における `""` と `< >` の違い

Arduino（および C/C++）でヘッダファイルをインクルードする際、`#include` の 2 つの書き方は異なる動作をし、正しく使い分ける必要があります。

## `#include "xxx.h"`
この形式では、コンパイラは次の順番でファイルを検索します。

- **スケッチフォルダを最優先で検索**
- その後、グローバルなライブラリパスを検索

`.ino` ファイルと同じフォルダに置いた小さな自作モジュールなど、**プロジェクト内のヘッダファイル**に使用します。

## `#include <xxx.h>`
この形式では次のように動作します。

- スケッチフォルダは検索しない
- **インストール済みライブラリのディレクトリのみ**を検索

`Arduino/libraries` にインストールされた公式またはサードパーティ製ライブラリに使用します。

## 注意点
- スケッチと同じフォルダに置いた **自作ヘッダなどのローカルヘッダ**には `""` を使う  
- **インストール済みライブラリ**には `< >` を使う  
- スケッチフォルダ内のヘッダは、`<xxx.h>` では **見つからない**  
  → `"xxx.h"` を使う必要があります

特に自作モジュールを追加したり、プロジェクト構造を整理したりするときに混乱を招きやすいポイントです。

