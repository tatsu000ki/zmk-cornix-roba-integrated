# 現在の状況

## 最新の変更（2026-03-23）

### 実施した修正

1. **ZMK v0.3-branchに変更**
   - ZMK v0.3（タグ）からv0.3-branch（ブランチ）に変更
   - PMW3610ドライバーとの互換性を確保

2. **temp-layer-thresholdモジュールを削除**
   - roBa_Rシールドで使用していないため削除
   - コンパイルエラーを回避

3. **外部zmk-keyboard-cornixモジュールを使用**
   - ARCH=jzfエラーを解決
   - v2.6.8を使用

### 現在の設定

```yaml
# config/west.yml
manifest:
  projects:
    - name: zmk
      revision: v0.3-branch
    - name: zmk-keyboard-cornix
      revision: v2.6.8
    - name: zmk-pmw3610-driver
      revision: main
    - name: zmk-helpers
      revision: main
```

### ビルド構成

```yaml
# build.yaml
include:
  - board: cornix_left          # Cornix左手（ペリフェラル）
  - board: seeeduino_xiao_ble   # roBa右手（セントラル）
    shield: roBa_R
```

## 解決した問題

### ✅ ARCH=jzfエラー
- **問題**: Cornixのボード定義構造（`boards/jzf/cornix/`）がZephyrのアーキテクチャ命名規則と衝突
- **解決**: 外部zmk-keyboard-cornixモジュール（v2.6.8）を使用してローカルボード定義を削除

### ✅ PMW3610ドライバー互換性
- **問題**: ZMK v0.3（タグ）とPMW3610ドライバーの互換性問題
- **解決**: ZMK v0.3-branchに変更

### ✅ temp-layer-thresholdコンパイルエラー
- **問題**: デバイスツリーに定義がないモジュールを参照
- **解決**: 使用していないため削除

## 現在のビルド状況

GitHub Actionsでビルドが実行中です。

**確認方法**:
```
https://github.com/tatsu000ki/zmk-cornix-roba-integrated/actions
```

## 期待される動作

### 左手（Cornix）
- **ボード**: cornix_left
- **キー数**: 50キー
- **役割**: ペリフェラル（子機）
- **機能**: 
  - 通常のキー入力
  - レイヤー切り替え
  - 右手とのレイヤー同期

### 右手（roBa）
- **ボード**: seeeduino_xiao_ble
- **シールド**: roBa_R
- **キー数**: 41キー
- **トラックボール**: PMW3610センサー
- **役割**: セントラル（親機）
- **機能**:
  - 通常のキー入力
  - マウス移動
  - マウスクリック（左/右/中）
  - スクロール機能（レイヤー5）
  - レイヤー切り替え
  - 左手とのレイヤー同期

## 技術的な詳細

### PMW3610トラックボール設定

roBa_Rシールドの設定（[`config/boards/shields/roBa/roBa_R.overlay`](config/boards/shields/roBa/roBa_R.overlay)）:

```dts
&pro_micro_spi {
    status = "okay";
    cs-gpios = <&pro_micro 16 GPIO_ACTIVE_LOW>;
    
    trackball: trackball@0 {
        compatible = "pixart,pmw3610";
        reg = <0>;
        spi-max-frequency = <2000000>;
        irq-gpios = <&pro_micro 6 (GPIO_ACTIVE_LOW | GPIO_PULL_UP)>;
        
        // スクロールレイヤー設定
        scroll-layers = <5>;
    };
};
```

### レイヤー構成

両方のキーボードで同じレイヤー番号を使用:
- レイヤー0: デフォルトレイヤー
- レイヤー1-4: カスタムレイヤー
- レイヤー5: スクロールレイヤー（roBaのトラックボールがスクロールモードになる）

### Bluetooth接続

- **セントラル（親機）**: roBa右手
  - PCとBluetoothで接続
  - Cornix左手と内部Bluetoothで接続
  
- **ペリフェラル（子機）**: Cornix左手
  - roBa右手と内部Bluetoothで接続
  - PCとは直接接続しない

## 次のステップ

### 1. ビルド成功を確認

GitHub Actionsページで緑のチェックマークを確認

### 2. ファームウェアをダウンロード

Artifactsセクションから`firmware.zip`をダウンロード

### 3. ファームウェアを書き込む

- `cornix_left-zmk.uf2` → Cornix左手
- `seeeduino_xiao_ble-roBa_R-zmk.uf2` → roBa右手

### 4. ペアリングと動作確認

1. roBa右手を起動
2. Cornix左手を起動
3. PCとBluetoothペアリング
4. 動作確認

詳細は [`BUILD_INSTRUCTIONS.md`](BUILD_INSTRUCTIONS.md) を参照してください。

## トラブルシューティング

### ビルドが失敗した場合

1. GitHub Actionsのエラーログを確認
2. エラーメッセージをコピー
3. 以下の情報を報告:
   - エラーメッセージ
   - 失敗したステップ
   - ビルドログの該当部分

### よくある問題

- **PMW3610ドライバーのコンパイルエラー**: ZMK v0.3-branchを使用しているか確認
- **ARCH=jzfエラー**: 外部zmk-keyboard-cornixモジュールを使用しているか確認
- **レイヤー同期しない**: 単一プロジェクトでビルドしているか確認

## 参考情報

- **GitHubリポジトリ**: https://github.com/tatsu000ki/zmk-cornix-roba-integrated
- **元のroBaプロジェクト**: https://github.com/kumamuk-git/zmk-config-roBa
- **元のCornixプロジェクト**: https://github.com/hitsmaxft/zmk-keyboard-cornix
- **参考プロジェクト**: https://github.com/t-ogura/zmk-config-cornix-tb
- **ZMK公式ドキュメント**: https://zmk.dev/docs

## コミット履歴

```
7c525b7 Remove temp-layer-threshold module (not used in our configuration)
106ed01 Add comprehensive build instructions and troubleshooting guide
4a6d75d Use ZMK v0.3-branch and add temp-layer-threshold module for PMW3610 compatibility
a6e6ba1 Use external zmk-keyboard-cornix module and ZMK v0.3
2dd38e8 Use ZMK build workflow v0.2 for v0.3-branch compatibility
```
