# ZMK Cornix-roBa Integrated Keyboard

Cornix (左手) + roBa (右手・トラックボール付き) の統合ZMKファームウェア

## 特徴

- **左手**: Cornix (25キー)
- **右手**: roBa (21キー + PMW3610トラックボール)
- **合計**: 46キー + トラックボール + エンコーダー

## ハードウェア

### 左手 (Cornix)
- ボード: Cornix Left (nRF52840)
- キー数: 25キー
- エンコーダー: 1個

### 右手 (roBa)
- ボード: Seeeduino XIAO BLE (nRF52840)
- キー数: 21キー
- トラックボール: PMW3610
- エンコーダー: 1個

## ビルド方法

### GitHub Actionsで自動ビルド（推奨）

1. このリポジトリをGitHubにプッシュ
2. GitHub Actionsが自動的にファームウェアをビルド
3. Actionsタブから`firmware`アーティファクトをダウンロード
4. `cornix_roba_L.uf2`と`cornix_roba_R.uf2`を取得

### ローカルでビルド

```bash
# West環境のセットアップ
west init -l config
west update
west zephyr-export

# 左手のビルド
west build -s zmk/app -b cornix_left -d build/left -- -DSHIELD=cornix_roba_L -DZMK_CONFIG="$(pwd)/config"

# 右手のビルド
west build -s zmk/app -b seeeduino_xiao_ble -d build/right -- -DSHIELD=cornix_roba_R -DZMK_CONFIG="$(pwd)/config" -DSNIPPET=studio-rpc-usb-uart
```

## ファームウェアの書き込み

### 左手 (Cornix)
1. Cornixをブートローダーモードにする
2. `cornix_roba_L.uf2`をドラッグ&ドロップ

### 右手 (roBa)
1. XIAO BLEをブートローダーモードにする（RSTボタンを2回押す）
2. `cornix_roba_R.uf2`をドラッグ&ドロップ

## キーマップ

キーマップは`config/cornix_roba.keymap`で定義されています。

### レイヤー構成
- Layer 0: デフォルト (QWERTY)
- Layer 1: ファンクション (F1-F12, テンキー)
- Layer 2: 記号
- Layer 3: 矢印・ナビゲーション
- Layer 4: マウス
- Layer 5: スクロール
- Layer 6: Bluetooth設定

### トラックボール機能
- レイヤー4: マウスモード
- レイヤー5: スクロールモード
- 自動レイヤー切り替え機能付き

## トラブルシューティング

### ビルドエラー
- `west.yml`のZMKブランチを確認
- ポインティングデバイス機能が有効なブランチを使用

### ペアリングできない
- 両方のキーボードをリセット
- Layer 6でBluetooth設定をクリア

### トラックボールが動かない
- 右手のファームウェアが正しく書き込まれているか確認
- SPI接続を確認

## ライセンス

MIT License

## クレジット

- Cornix: https://github.com/tatsu000ki/zmk-keyboard-cornix
- roBa: https://github.com/tatsu000ki/zmk-config-roBa
