# 自他融合デモ（Fusion Demo）

自分の手のライブ映像と、比較対象（他者の手の録画、または数フレーム前の自分の手）を一定周期で切り替えて表示する、ブラウザ用のデモアプリです。HTML 1枚で動き、外部ライブラリやサーバ側の処理はありません。

ポスター会場で、立ったまま体験してもらうことを想定しています。研究用の実験装置ではありません（提示タイミングの精度は保証しない）。

- 公開URL：https://kasukeinu.github.io/self-other-fusion-demo/
- アプリ本体：`index.html`
- 仕様書：`docs/SPEC.md`

## ファイル構成

```
self-other-fusion-demo/
├── index.html      アプリ本体（HTML 1枚）
├── README.md       このファイル
├── .nojekyll       GitHub Pages でJekyllの変換を使わない指定
├── .gitignore      録画した動画・CSVをリポジトリに入れない
└── docs/
    └── SPEC.md     仕様書
```

## 動作条件

- カメラを使うため、**HTTPS** か **localhost** で開く必要がある（`file://` で直接開くとカメラが拒否されることがある）
- 確認したい環境：iPhone / iPad の Safari、Mac の Safari / Chrome
- 動作確認の状況は `docs/SPEC.md` の「検証状況」を参照

## 1. Mac で動作確認する

```bash
cd fusion-demo
python3 -m http.server 8000
```

ブラウザで `http://localhost:8000/` を開き、「カメラ開始」を押す。

## 2. GitHub Pages に公開する

GitHub Pages は、リポジトリのファイルをそのままHTTPSのWebページとして配信する機能です。公開すると、iPhoneのSafariからURLを開くだけで使えます。

### 公開範囲の注意

- 私の知る限り、無料プランでGitHub Pagesを使えるのは**公開（public）リポジトリ**のみです。非公開リポジトリで使うには有料プランか組織のプランが必要です。所属組織のプランを確認してください
- Pages のURLは誰でも開けます。アプリ自体には個人情報は含まれず、録画もブラウザ内で処理されるだけでサーバには送られません
- **録画した動画やCSVをリポジトリに入れない**でください（`.gitignore` で除外しています）

### 方法A：ブラウザだけで行う

1. GitHub で新しいリポジトリを作る（例：`fusion-demo`、Public）
2. 「Add file」→「Upload files」で、zipを展開した中身（`index.html`、`README.md`、`.nojekyll`、`.gitignore`、`docs/`）をアップロードして Commit
   - `.nojekyll` などのドットで始まるファイルは、Macの Finder では隠しファイルです。`Command + Shift + .` で表示できます。アップロードできなくても動作には影響しません
3. リポジトリの「Settings」→「Pages」を開く
4. 「Build and deployment」の Source を「Deploy from a branch」、Branch を `main`、フォルダを `/ (root)` にして Save
5. 1〜2分待つと、同じ画面に公開URLが表示される：`https://<ユーザー名>.github.io/fusion-demo/`

### 方法B：コマンドで行う

```bash
cd fusion-demo
git init
git add .
git commit -m "Initial commit: fusion demo"
git branch -M main
git remote add origin git@github.com:<ユーザー名>/fusion-demo.git
git push -u origin main
```

その後、方法Aの手順3〜5で Pages を有効にする。GitHub CLI を使う場合は `gh repo create fusion-demo --public --source=. --push` でリポジトリ作成とpushをまとめてできる。

### 更新するとき

`index.html` を書き換えて commit・push すると、1〜2分で公開ページに反映される。iPhone側で古い版が表示される場合は、Safariでページを再読み込みする。

## 3. iPhone / iPad で使う

1. Safari で公開URLを開く
2. 共有ボタン →「ホーム画面に追加」（アドレスバーのない全画面で起動できる）
3. ホーム画面のアイコンから起動し、「カメラ開始」を押してカメラを許可
4. 操作方法はアプリ内の「使い方」ボタンを参照

会場のWi-Fiに頼らないよう、事前に一度開いておくこと。ただし、オフラインで再起動したときに確実に開けるかは未確認です（`docs/SPEC.md` 参照）。

## 4. 保存されるファイル

アプリが保存するファイルは、すべて端末のダウンロード先（Macはダウンロードフォルダ、iPhoneは「ファイル」アプリの「ダウンロード」）に置かれます。

| ボタン | ファイル | 内容 |
| --- | --- | --- |
| 録画を保存 | `other_hand_<日時>.mp4` または `.webm` | 比較対象用に録画した他者の手 |
| 体験を記録 → 記録停止 | `session_<日時>.mp4` または `.webm` | 体験中に画面に出ていた映像 |
| 体験を記録 → 記録停止 | `session_<日時>.csv` | 切替のログ |

保存しなかった録画は、ページを再読み込みすると消えます。

## 5. iPhone での不具合を調べる

iPhone を Mac にケーブルでつなぎ、Mac の Safari の「開発」メニューから iPhone のページを選ぶと、コンソールでエラーを確認できる。iPhone 側で「設定」→「Safari」→「詳細」→「Webインスペクタ」をオンにしておく。
