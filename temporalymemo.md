 
 **「0x21 を受け取ったら funcA を呼ぶ」構造は、  
**“キー（ID）→ 関数” の対応表を作るだけで実現できる。**

---

# ✦ 一番シンプルな実装例

```cpp
void funcA() {
    // 0x21 の処理
}

void funcB() {
    // 0x1F2 の処理
}

void dispatch(uint32_t id) {
    switch (id) {
        case 0x21:
            funcA();   // ← これが「直接呼び出し」
            break;

        case 0x1F2:
            funcB();
            break;
    }
}
```

これで **0x21 → funcA が直接呼ばれる**。

---

# ✦ もっと柔軟にしたいなら「ディスパッチテーブル方式」

祐亮のスタイル（抽象 API の裏側を観測して補う）に合うのはこっち。

```cpp
typedef void (*Handler)();

void funcA();
void funcB();

struct Entry {
    uint32_t id;
    Handler handler;
};

Entry table[] = {
    {0x21,  funcA},
    {0x1F2, funcB},
};

void dispatch(uint32_t id) {
    for (auto& e : table) {
        if (e.id == id) {
            e.handler();   // ← これが「直接呼び出し」
            return;
        }
    }
}
```

これなら ID が増えても表に追加するだけ。

---

# ✦ 祐亮が気にしていた「直接呼び出せない？」の正体

Arduino_CAN の API が薄すぎて、

- `onReceive()` のようなコールバックが無い  
- 割り込みにユーザー関数を登録できない  

だから「直接呼べないの？」と感じたんだと思う。

でも **“受信後の処理” は完全に自由**だから、  
上のディスパッチ構造を作れば **実質的に直接呼び出し** と同じ。

---

# ✦ 次どうする？

- このディスパッチ構造を **引数付き**にしたい？  
- **非同期（割り込み）でのディスパッチ**を作りたい？  
- **ID の範囲マッチ**や **マスクマッチ**に対応させたい？  
- それとも **Portenta C33 の CAN1 の話に戻す**？

