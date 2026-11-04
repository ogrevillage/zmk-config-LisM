# LisM — Geonix Compact5移植試作（配列確認用）

> **試作・未検証。GitHub内だけの変更で、LisM本体には未書き込み。**
> 元ブランチ：`dya-studio_zmk-v0.4`。このページは`config/lism.keymap`の見取り図です。DYA Studioの本体保存設定とは自動同期されません。

## 配列の読み方

- 左右それぞれ文字キー3段×5列。下段は**各5物理キー＋1モジュール位置**。
- `ENC`＝左垂直ロータリー、`TB`＝右トラックボール。**キーではありません。**
- `短/長`＝タップ／長押し、`▽`＝下位レイヤーを継承。
- 横の並びは上から見た概略。実物のモジュール形状・位置とは多少異なります。

## L0 — BASE

```text
左手                       || 右手
 Q    W    E    R    T      || Y      U    I    O    P/DEVICE
 A    S    D    F    G      || H      J    K    L    ;
 Z/⇧  X    C    V    B      || N      M    ,    .    :/⇧
 Ctrl Win  Alt [ENC] DEL/NUM Space  || Enter/MOUSE BS/FUNC [TB] Shift  Ro   /Ctrl
```

- **A＋S＝Tabを継続**。A長押しTabは不採用。
- Z短押しZ／長押し左Shift、右下段のコロン短押し／長押し右Shift、右端/短押し／長押し右Ctrl。
- P短押しP／長押しL3 Device、Del短押し／長押しL1 Number、Backspace短押し／長押しL2 Function、Enter短押し／長押しL4 Mouse。
- 現在の右TB・左ロータリーと、スクロール用の仮想バインディングを残す。

## L1 — NUMBER / SYMBOL

```text
 1    2    3    4    5     || 6   7   8   9   /
 Tab  ▽    ▽    ▽    =     || :   4   5   6   *
 Shift▽    ▽    ▽    ¥     || ,   1   2   3   -
 Esc/Ctrl ▽ ▽ [ENC] ▽ ▽    || ▽   ▽  [TB] 0   .   +
```

- 左端最下段：Esc短押し／Ctrl長押し。Z位置は専用Shift。右下段は数字`0 . +`。
- `=`、`:`、`¥` はWindows/JIS配列で確認が必要。
- 左ロータリーはこのレイヤーで音量調整の設定を継承する案。

## L2 — FUNCTION

```text
 F1   F2   F3   F4   F5   || F6  F7  F8  F9  F10
 Tab  ▽    ▽    ▽    ▽    || ←   ↑   ↓   →   F11
 Shift▽    ▽    ▽    ▽    || ▽   ▽   ▽   ▽    ▽
 Esc/Ctrl ▽ ▽ [ENC] ▽ ▽   || ▽   ▽  [TB] ▽   ▽    ▽
```

- **H J K L＝← ↑ ↓ →**（2026-11-04指定反映。Geonix Compact5の以前の`← ↓ ↑ →`を変更）。
- 左A位置Tab、左Z位置Shift、左下端Esc/Ctrl。
- F12の位置は未確定。押しやすさは後で実機確認。

## L3 — DEVICE / BLUETOOTH

- 右Pを長押しし、左Q/W/E/R/TでBluetooth接続先1〜5を選択。
- 接続情報消去、Studio Unlock、リセット、ブートローダー用の保守キーは保持。ただし押し間違いに注意。
- Geonix固有の2.4GHzやRGB操作はLisMには移植しない。

## L4 — MOUSE

```text
 左手：E↑、S←、D↓、F→
 右手：I スクロール↑、J 左クリック、K 中クリック、L 右クリック
       , スクロール↓
```

右Enter長押しで起動。トラックボールのポインター操作は独立して維持する設計。

## コンボ（実装試作）

| 物理キー | 出力 | 対象 |
|---|---|---|
| Q＋W | Esc | L0 |
| A＋S | **Tab** | L0 |
| D＋F | 英数（LANG2） | L0 |
| J＋K | かな（LANG1） | L0 |
| F＋J | @（JISの左角括弧HID位置） | L0 |
| O＋P | -／長音（IME依存） | L0・L1 |

括弧の組`()`／`[]`を出すコンボは**今回の試作では未実装**。日本語JISでのHIDコードとカーソル戻しを検証してから追加します。コンボとLayer-Tapの同時判定も実機未検証。

## 書き込む前の確認事項

1. GitHub Actionsのビルド成功を確認する。成功しても文字出力・コンボの正しさは保証されない。
2. DYA Studioで**現行キーマップをエクスポートしてバックアップ**。
3. ファームウェア書き換え後もDYA Studioの本体保存キー設定が残る可能性がある。確認せず設定リセットしない。
4. まず右手トラックボール、左ロータリー、Bluetooth接続とDYA Studioの動作確認。
5. ブートローダー復旧、Studio Unlockキーを必ず残す。本体は**利用者が別途明示的に書き込むまで変更しない**。

## 参照

- [移植試作のキーマップ本体](../config/lism.keymap)
- [LisMメーカーのDYA Studio対応ファームウェア](https://github.com/4mplelab/zmk-config-LisM/tree/dya-studio_zmk-v0.4)
- [Geonix Compact5の設計記録](https://github.com/ogrevillage/GeonixR2-QMK/blob/main/variants/compact5/README.md)
