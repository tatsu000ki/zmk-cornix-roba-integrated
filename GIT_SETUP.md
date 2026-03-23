# Git セットアップとGitHubへのアップロード手順

## 1. Gitのインストール

### ダウンロード
https://git-scm.com/download/win からGit for Windowsをダウンロード

### インストール
1. ダウンロードしたインストーラーを実行
2. 基本的にデフォルト設定でOK
3. インストール完了後、VSCodeを再起動

## 2. Gitの初期設定

VSCodeのターミナルで以下を実行:

```bash
git config --global user.name "あなたの名前"
git config --global user.email "あなたのメールアドレス"
```

## 3. GitHubリポジトリの作成

1. https://github.com にアクセス
2. ログイン（アカウントがない場合は作成）
3. 右上の「+」→「New repository」をクリック
4. リポジトリ名: `zmk-cornix-roba-integrated`
5. Public/Privateを選択
6. 「Create repository」をクリック

## 4. ローカルリポジトリの初期化とプッシュ

VSCodeのターミナルで以下を実行:

```bash
# zmk-cornix-roba-integratedディレクトリに移動
cd zmk-cornix-roba-integrated

# Gitリポジトリを初期化
git init

# すべてのファイルをステージング
git add .

# 最初のコミット
git commit -m "Initial commit: Cornix + roBa integrated firmware"

# GitHubリポジトリをリモートとして追加（URLは自分のリポジトリに変更）
git remote add origin https://github.com/あなたのユーザー名/zmk-cornix-roba-integrated.git

# メインブランチに変更
git branch -M main

# GitHubにプッシュ
git push -u origin main
```

## 5. GitHub Actionsでビルド

1. GitHubのリポジトリページを開く
2. 「Actions」タブをクリック
3. 自動的にビルドが開始される
4. ビルド完了後、「Artifacts」からファームウェアをダウンロード

## 6. ファームウェアの書き込み

### 左手 (Cornix)
1. Cornixをブートローダーモードにする
2. `cornix_roba_L.uf2`をドラッグ&ドロップ

### 右手 (roBa / XIAO BLE)
1. RSTボタンを2回素早く押してブートローダーモードにする
2. `cornix_roba_R.uf2`をドラッグ&ドロップ

## トラブルシューティング

### Gitのインストールが認識されない
- VSCodeを再起動
- PCを再起動

### プッシュ時に認証エラー
- Personal Access Token (PAT)を使用
- GitHub Settings → Developer settings → Personal access tokens → Generate new token
- `repo`スコープを選択
- 生成されたトークンをパスワードとして使用

### ビルドエラー
- `.github/workflows/build.yml`を確認
- ZMKのブランチが正しいか確認
- Issuesで報告

## 次のステップ

1. ✅ Gitをインストール
2. ✅ GitHubリポジトリを作成
3. ✅ コードをプッシュ
4. ✅ GitHub Actionsでビルド
5. ✅ ファームウェアをダウンロード
6. ✅ キーボードに書き込み
7. ✅ 動作確認
8. 🎉 完成！

## 参考リンク

- Git公式: https://git-scm.com/
- GitHub Docs: https://docs.github.com/
- ZMK Docs: https://zmk.dev/
