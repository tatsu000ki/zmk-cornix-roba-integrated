# 次のステップ

## 現在の状況

✅ **完了した作業**:
1. Cornix（左手）とroBa（右手）の統合プロジェクトを作成
2. 外部zmk-keyboard-cornixモジュールを使用してARCH=jzfエラーを解決
3. ZMK v0.3-branchとtemp-layer-thresholdモジュールを追加してPMW3610互換性を確保
4. GitHubリポジトリにプッシュ完了
5. GitHub Actionsワークフローが自動実行中

## すぐにやること

### 1. ビルド状況を確認

**GitHubにアクセス**:
```
https://github.com/tatsu000ki/zmk-cornix-roba-integrated/actions
```

**確認ポイント**:
- ✅ 緑のチェックマーク = ビルド成功 → 次のステップへ
- ❌ 赤のバツマーク = ビルド失敗 → エラーログを確認して報告
- 🟡 黄色の円 = ビルド実行中 → 完了まで待つ（通常5-10分）

### 2. ビルド成功時の手順

1. **ファームウェアをダウンロード**:
   - Actionsページで最新のワークフロー実行をクリック
   - 下部の「Artifacts」セクションから`firmware.zip`をダウンロード
   - ZIPファイルを解凍

2. **ファームウェアを書き込む**:
   - `cornix_left-zmk.uf2` → Cornix左手に書き込み
   - `seeeduino_xiao_ble-roBa_R-zmk.uf2` → roBa右手に書き込み

3. **ペアリング**:
   - roBa右手（親機）を先に起動
   - Cornix左手（子機）を起動
   - PCとBluetoothペアリング

詳細は [`BUILD_INSTRUCTIONS.md`](BUILD_INSTRUCTIONS.md) を参照してください。

### 3. ビルド失敗時の対応

エラーログを確認して以下の情報を報告:
- エラーメッセージ
- 失敗したステップ
- ビルドログの該当部分

## 技術的な詳細

### 現在の設定

| 項目 | 設定値 |
|------|--------|
| ZMKバージョン | v0.3-branch |
| Cornixモジュール | zmk-keyboard-cornix v2.6.8（外部） |
| PMW3610ドライバー | kumamuk-git/zmk-pmw3610-driver |
| temp-layer-threshold | yamaryu211/zmk-input-processor-temp-layer-threshold |
| ビルドワークフロー | v0.2 |

### ビルド構成

```yaml
# build.yaml
include:
  - board: cornix_left          # Cornix左手（ペリフェラル/子機）
  - board: seeeduino_xiao_ble   # roBa右手（セントラル/親機）
    shield: roBa_R
```

### 期待される機能

**左手（Cornix）**:
- 50キーレイアウト
- ペリフェラル（子機）として動作
- レイヤー同期

**右手（roBa）**:
- 41キー + PMW3610トラックボール
- セントラル（親機）として動作
- マウス移動、クリック、スクロール
- レイヤー5でスクロール機能
- レイヤー同期

## 解決した問題

### 1. ARCH=jzfエラー
**問題**: Cornixのボード定義が`boards/jzf/cornix/`構造を使用し、Zephyrが`jzf`をアーキテクチャとして解釈
**解決**: 外部zmk-keyboard-cornixモジュール（v2.6.8）を使用してローカルボード定義を削除

### 2. PMW3610ドライバー互換性
**問題**: ZMK v0.3（タグ）とPMW3610ドライバーの互換性問題
**解決**: ZMK v0.3-branchに変更し、temp-layer-thresholdモジュールを追加（roBaの元の設定と同じ）

### 3. レイヤー同期
**問題**: 左右のキーボードでレイヤーを同期する必要がある
**解決**: 単一プロジェクトでビルドすることで自動的にレイヤー同期を実現

## 参考プロジェクト

成功事例として参考にしたプロジェクト:
- **t-ogura/zmk-config-cornix-tb**: Cornix + トラックボール統合の実例
  - 外部zmk-keyboard-cornixモジュールの使用方法を参考
  - ZMK v0.3の使用を確認

## トラブルシューティング

よくある問題と解決方法は [`BUILD_INSTRUCTIONS.md`](BUILD_INSTRUCTIONS.md) の「トラブルシューティング」セクションを参照してください。

## 質問がある場合

ビルドエラーや動作に問題がある場合は、以下の情報を提供してください:
1. エラーメッセージ（全文）
2. どのステップで問題が発生したか
3. 試したこと
4. 期待される動作と実際の動作の違い
