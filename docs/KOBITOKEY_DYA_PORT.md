# LisM × KobitoKey_DYA キーマップ移植（試作）

> **現在のLisMには以前の試作版を書き込み済み。** この文書のIME S+D/K+L変更は作業ブランチ上の新しい試用設定であり、まだ実機適用・動作検証はしていません。JKLクリックの修正は実機で動作確認済みです。旧実機キーマップのバックアップは2026-11-04にKeyboard Abyssへ `LisM_Original_20261104` として作成済み。

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
- Q＋W → Esc、**S＋D → 英数、K＋L → かな**（左右ミラー／L0・L1・L3・L5で共通、起動後L0へ戻る。L2 FUNC・L4 BT・予約レイヤーには設定しない）。F＋J → @、O＋P → マイナス／長音。
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
  ▽    ▽    ▽    ▽    ▽        || ▽    ▽    ▽    ▽    ▽
  ▽    ▽    ▽    ▽    ▽        || ▽    左    中    右    ▽
  ▽    ▽    ▽    ▽    ▽        || ▽    ▽    ▽    ▽    ▽
  ▽    ▽    ▽  [ENC] ▽   L0へ || ▽    ▽   [TB] ▽  L0へ ▽
```

- **L3クリックはJ＝左、K＝中、L＝右。** U/I/Oのクリック割当は解除。JKにIMEコンボが重ならず、マウス操作中でもK+Lでかなへ切替できる。
- **右トラックボールの移動**で自動起動。起動判定100ms、無操作解除700ms。
- 実装場所：[`snippets/lism-input-processors.dtsi`](../snippets/lism-input-processors.dtsi) のcentral processor。
- **IMEコンボ（L0・L1・L3・L5共通）：S＋D＝英数、K＋L＝かな。** それぞれキー送信後に`&to 0`でL0へ切り替えるマクロを実装。E＋R・U＋Iの旧IMEコンボは削除（その前のJ＋K・D＋Fも既に削除済み）。
- IMEコンボのタイムアウトは50ms。E＋Rは「れ」（R→E）の入力時に実機で誤発動したため変更。S＋D／K＋Lはローマ字入力との競合が少ない見込みだが実機テスト要。L3ではK（中クリック）とL（右クリック）がコンボ判定対象になるため、特に右クリック遅延・右ドラッグを確認。
- DYA Studioで以前保存したランタイム設定がある場合、ファームウェアの初期値より優先される可能性があります。
- 誤操作を避けるため、起動閾値・解除時間は実機で後から調整。

## L4 BT/DEVICE

- P長押しでL4へ。左上Q/W/E/R/TでBluetooth 1〜5を選択。
- L4ではIMEコンボを無効化し、Bluetoothプロファイル選択操作を妨げない。
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

## 左ロータリーのスクロール方向・量（試用設定）

- スクロール量は従来200から**100（50%）**に減量。
- 実機で時計回りのスクロールが上方向へ動いたという操作希望に合わせ、L0の`rsr_scrl`の`cw-binding`と`ccw-binding`を入れ替える試験版を追加。
- **コード上はcw＝`SCRL_UP`、ccw＝`SCRL_DOWN`となる**。これはロータリー実装の物理的な検出方向が反転している場合に「実際の時計回り＝下」を実現する補正で、実機で必ず確認すること。
- DYA Studioに保存済みのランタイムセンサー回転設定がある場合、コードの既定値より優先される。反映されなければDYA Studioの回転方向設定で変更する。
- L1の`encoder_kp`による音量アップ／ダウンには手を加えない。

## GitHub・DYA Studioに関する注意点

1. [キーマップ定義](../config/lism.keymap) とこの図はGitHubの**試作ブランチ**のもの。DYA Studioで現在表示されているキーボードの設定が自動更新されるわけではありません。
2. 旧実機設定を `LisM_Original_20261104` としてKeyboard Abyssに非公開保存済み。更新後は新たに別名で保存すること。
3. 現在の本体のファームウェアと保存済みDYA設定がGitHubの内容と食い違う場合があります。**ビルド完了・UF2の内容を確認してから書き込む**。
4. LisMはトラックボール・ロータリー・コネクション管理の維持が重要。ファームウェア書き込み後の入力が途絶えた場合に備え、リセット・ブートローダーの復旧手順を把握した上で試す。
5. 日本語JISマクロ・キー名・ZMKのビルド結果は未確認。ビルドが通っても誤コンボや実機挙動の検証が必要。

## 参照元

- [KobitoKey_DYA](https://github.com/ogrevillage/KobitoKey_DYA/blob/main/config/KobitoKey.keymap)
- [LisM公式DYA対応ブランチ](https://github.com/4mplelab/zmk-config-LisM/tree/dya-studio_zmk-v0.4)
- [入力プロセッサの公式説明](https://github.com/cormoran/zmk-module-runtime-input-processor)
