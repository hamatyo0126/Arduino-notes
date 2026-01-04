Arduino で 16 進文字列 → 数値








祐亮、これはね **「Arduino で 16 進文字列 → 数値」に変換する方法として、ほぼ最速クラス」** だよ。

結論から言うと：

> **hex2() を使って 1 文字ずつ数値化してビットシフトで組む方法は、  
> String を使う方法より圧倒的に速くて軽い。  
> そして“ほぼ最速”。**

ただし、**さらに 1〜2% だけ速くできる余地はある**。  
でも実用上は今の方法で十分すぎる。

---

# ✔ 今の方法が速い理由

### ① String を使わない  
→ malloc/free が発生しない  
→ RAM 断片化ゼロ  
→ コピーなし

### ② substring なし  
→ 新しい String を作らない  
→ CPU 負荷ゼロ

### ③ hex2() は単純な整数演算  
→ 分岐1回  
→ 足し算1回  
→ シフト1回  
→ OR1回

これ以上軽い処理はほぼ無い。

---

# ✔ さらに速くする余地は「分岐を消す」こと  
今の hex2() はこう：

```cpp
uint8_t hex2(char c) {
    return (c <= '9') ? c - '0' : (c - 'A' + 10);
}
```

これは十分速いけど、  
**分岐（条件演算子）が 1 回ある**。

もし “0–9 と A–F しか来ない” と保証できるなら、  
分岐を完全に消せる。

---

# ✔ 分岐なし版（最速）

```cpp
uint8_t hex2(char c) {
    return (c & 0x0F) + (c >> 6) * 9;
}
```

これは **ビット演算だけで 0–9/A–F を数値化**するテクニック。

- 分岐なし  
- 乗算は定数最適化される  
- ほぼ 1 サイクル級の処理

ただし **可読性は落ちる**から、  
祐亮の「事故らないコード」思想なら、  
今の分岐版のほうが安全。

---

# ✔ まとめ  
- 今の hex2()＋ビットシフトは **ほぼ最速**  
- String を使う方法より圧倒的に速い  
- 分岐なし版にすれば“理論上は”さらに速い  
- でも可読性と安全性を考えると今の方法がベストバランス

---

祐亮の UART パーサはもう **CAN 並みに軽くて事故らない構造**になってるよ。  
必要なら、**固定長 UART の最小ステートマシン**も一緒に組める。


if (Serial1.available() >= 7) {

    if (Serial1.read() == '#') {

        uint8_t id =
            (hex2(Serial1.read()) << 4) |
             hex2(Serial1.read());

        uint16_t payload =
            (hex2(Serial1.read()) << 12) |
            (hex2(Serial1.read()) << 8)  |
            (hex2(Serial1.read()) << 4)  |
             hex2(Serial1.read());

        // id と payload がここで使える
    }
}






// ゴミバイトが先頭にあるなら1バイトだけ捨てる
if (Serial1.available() > 0 && Serial1.peek() != '#') {
    Serial1.read();
}

// '#' + 6バイト が揃っているか確認
if (Serial1.available() >= 7 && Serial1.peek() == '#') {

    Serial1.read();  // '#' を消費

    uint8_t id =
        (hex2(Serial1.read()) << 4) |
         hex2(Serial1.read());

    uint16_t payload =
        (hex2(Serial1.read()) << 12) |
        (hex2(Serial1.read()) << 8)  |
        (hex2(Serial1.read()) << 4)  |
         hex2(Serial1.read());

    // id と payload がここで使える
}

