# LisM × KobitoKey_DYA キーマップ移植（試作）

> **LisM本体には未適用。** 公式の `dya-studio_zmk-v0.4` から切った隔離ブランチにのみ保存しています。コードの静的な配置確認は済みましたが、ビルド・実機動作は未検証です。

## 移植方針

- **移植元：** [ogrevillage/KobitoKey_DYA](https://github.com/ogrevillage/KobitoKey_DYA/blob/main/config/KobitoKey.keymap)。Geonix Compact5のQMKコードを直接移植したものではありません。
- **LisM固有部分：** 左の縦型ロータリー、右トラックボール、センサー処理、Bluetooth、DYA Studio連携、メンテナンスキーを残す。
- KobitoKey独自の `layout_shift.dtsi` と `&kp` ドライバー置換はコピーしない。異なるハードウェア間で安全に移植できるキー割当・コンボのみ採用。
- **差分：** L2のHJKLを `← ↑ ↓ →` に変更。L0右下の専用Shiftを削除。L5右下の0を削除して、L5のN位置にある `,` を `0` に変更。
- **L3はAUTO MOUSE。** 移動で自動起動し、クリックキーを提供。手動のMouse Layerへの切替は不要。

## 物理配列の見取り図

```text
          LisM 左手                  LisM 右手
           0  1  2  3  4              5  6  7  8  9
           10 11 12 13 14            15 16 17 18 19
           20 21 22 23 24            25 26 27 28 29

   30   31   32  [ENC] 34   35   36   37  [TB] 39   40   41
```

`[ENC]`＝左縦型ロータリー、`[TB]`＝右トラックボール。どちらも通常の物理キーとしては扱いません。LisM設定ファイルにはモジュール用のバインディングと仮想スクロール枠が残っています。

## L0 BASE

```text
  Q       W       E       R       T       || Y      U       I      O      P/BT
  A       S       D       F       G       || H      J       K      L      ;
  Z/Shift X       C       V       B       || N      M       ,      .      :/Shift
  Ctrl/Esc  Win    Alt   [ENC] Del/NUM Space || Enter  BS/FUNC [TB] (無効) Ro  /Ctrl
```

- A＋S → Tab（**A長押しTabにはしない**）。
- Q＋W → Esc、D＋F → 英数、J＋K → かな、F＋J → @、O＋P → マイナス／長音。
- L＋; → `()`、.＋: → `[]` は小人キー由来のマクロを移植。日本語JISの出力とカーソル移動は未検証。

## L1 NUMBER（小人キー由来）

```text
  @      7    8    9    ▽           || -    ▽    ↑    ▽    @
  ¥      4    5    6    =           || +    ←    ↓    →    ▽
  0      1    2    3    .           || *    /    ▽    ▽    ▽
  ▽      ▽    ▽  [ENC] ▽    ▽        || ▽    ▽   [TB] ▽    ▽    ▽
```

`▽`は透明（基本レイヤーなどを継承）。キーコード `KP_*` はテンキー入力として扱います。

## L2 FUNCTION

```text
 F1   F2   F3   F4   F5        || F6   F7   F8   F9  F10
 Tab  ▽    ▽    ▽    ▽         || ←    ↑    ↓    →   F11
 Shift▽    ▽    ▽    ▽         || ▽    ▽    ▽    ▽    ▽
 ▽    ▽    ▽  [ENC] ▽    ▽     || ▽    ▽   [TB] ▽    ▽    ▽
```

- **H J K L＝← ↑ ↓ →** に修正（旧GeonixのJ/Kは逆）。
- F12は未指定。

## L3 AUTO MOUSE（今回の指定）

```text
  ▽    ▽    ▽    ▽    ▽        || ▽    左    中    右    ▽
  ▽    ▽    ▽    ▽    ▽        || ▽    ▽    ▽    ▽    ▽
  ▽    ▽    ▽    ▽    ▽        || ▽    ▽    ▽    ▽    ▽
  ▽    ▽    ▽  [ENC] ▽   L0へ || ▽    ▽   [TB] ▽  L0へ ▽
```

- 左クリック：U／中クリック：I／右クリック：O（小人キーL3の配置を継承）。
- **右トラックボールの移動**で自動起動。起動判定100ms、無操作解除700ms。
- 実装場所：[`snippets/lism-input-processors.dtsi`](../snippets/lism-input-processors.dtsi) のcentral processor。
- DYA Studioで以前保存したランタイム設定がある場合、ファームウェアの初期値より優先される可能性があります。
- 誤操作を避けるため、起動閾値・解除時間は実機で後から調整。

## L4 BT/DEVICE

- P長押しでL4へ。左上Q/W/E/R/TでBluetooth 1〜5を選択。
- **小人キーと同様、L4の下2段は原則透過（`&trans`）**。LisMの管理・復旧用として例外的に、左下端に`&bootloader`を1つ、左下段2キー目に`&bt BT_CLR_ALL`を1つ、右下段に`&studio_unlock`を1つ残す。
- **`&sys_reset`は2か所とも削除**。再起動は基板の物理RESETボタンを利用。右側の重複`&bootloader`も削除。
- Bluetooth前後切替は小人キーの中段位置を維持し、接続先の全消去は通常操作中に誤発動しないようL4に限定。
- Geonixの2.4GHzやRGBは対象外。

## L5 NUMBER2（今回の指定）

```text
  1    2    3    4    5         || 6    7    8    9     /
  Tab  ▽    ▽    ▽    =         || :    4    5    6     *
  Shift▽    ▽    ▽    ¥         || 0    1    2    3     -
  ▽    ▽    ▽  [ENC] ▽     ▽   || ▽    ▽   [TB] (無効) .  +
```

- **以前のN位置にある`,`を`0`へ変更。**
- **右下にあった`0`は削除。** 空きの右キー位置は`&none`として明示。
- Del長押しでL5。

## GitHub・DYA Studioに関する注意点

1. [キーマップ定義](../config/lism.keymap) とこの図はGitHubの**試作ブランチ**のもの。DYA Studioで現在表示されているキーボードの設定が自動更新されるわけではありません。
2. DYA Studioのインポート／エクスポートで現行キーマップをバックアップすること。
3. 現在の本体のファームウェアと保存済みDYA設定がGitHubの内容と食い違う場合があります。**動作確認前に書き込まない**。
4. LisMはトラックボール・ロータリー・コネクション管理の維持が重要。ファームウェア書き込み後の入力が途絶えた場合に備え、リセット・ブートローダーの復旧手順を把握した上で試す。
5. 日本語JISマクロ・キー名・ZMKのビルド結果は未確認。ビルドが通っても誤コンボや実機挙動の検証が必要。

## 参照元

- [KobitoKey_DYA](https://github.com/ogrevillage/KobitoKey_DYA/blob/main/config/KobitoKey.keymap)
- [LisM公式DYA対応ブランチ](https://github.com/4mplelab/zmk-config-LisM/tree/dya-studio_zmk-v0.4)
- [入力プロセッサの公式説明](https://github.com/cormoran/zmk-module-runtime-input-processor)
