# MY SAVINGS — 貯金管理 PWA

iPhone・Android・Windowsで使える静的Webアプリ。支出・貯金・月間スコア・XP・カテゴリ・JSONバックアップに対応しています。

## 使い始める（端末保存モード）

`firebase-config.js` を変更せずGitHub Pagesへ公開すると、端末内の `localStorage` に保存するモードで動きます。端末間同期はありません。Safariで「共有 → ホーム画面に追加」でアプリ風に利用できます。プライベートブラウズやブラウザデータ削除で消える可能性があるためJSONバックアップを取ってください。

## Firebase Googleログイン・クラウド同期を有効にする

1. [Firebase Console](https://console.firebase.google.com/) でプロジェクトを作成。
2. 「Authentication → Sign-in method → Google」を有効にする。
3. 「Firestore Database」を作成し、`firestore.rules` の内容をルール画面に貼り付けて公開。
4. プロジェクトの設定でウェブアプリを登録し、表示された設定値を `firebase-config.js` に転記する。
5. Authentication → Settings → Authorized domains に GitHub Pages のドメイン `ユーザー名.github.io` を追加する。
6. GitHubで新規リポジトリを作り、このフォルダのファイルをリポジトリ直下へアップロードする。
7. GitHubの Settings → Pages → Build and deployment で `Deploy from a branch` / `main` / `/(root)` を選び保存する。
8. 公開されたURLを開き、設定タブの「Googleでログイン」を押す。

**注意:** Firebase Spark無料枠には利用量制限があります。Firebaseの認証ポップアップはiPhoneのブラウザ設定によってブロックされる場合があります。Firebaseの設定値は公開情報ですが、Firestoreルールは必ず適用してください。

## データとスコア

- データはFirestoreの `users/{uid}/data/main` にユーザー別保存。ログイン前は端末保存。初回ログイン時、クラウドに既存データがあればクラウド側を優先します。
- スコアは貯金、予算遵守、前月比、好成績維持、記録日数で計算。負点・上限なし。前月支出ゼロなら改善スコア0。
- 翌月以降の起動時に未確定月のスコアとXPを固定。確定後の過去データ編集でも付与XPは変わりません。
- 月途中に予算・貯金目標を変更した場合、翌月からスコア計算基準に反映。月初の基準値は履歴として保存。
- 残高訂正は貯金スコア対象外。記録がなくなった日の記録スコアは取り消し。
- 日付表示・入力は `yyyy/mm/dd`。

## 既知の制限（本番運用前に確認）

- **複数端末から同時編集した場合、最後に保存した端末の内容で上書き**されます。Firestoreのトランザクション・競合解決は未実装。
- Firebaseログインはポップアップ方式。iOSのPWA単独起動では環境によりログインが失敗する場合があります。その場合はSafariで開いてログインしてください。
- JSON復元前のバックアップはブラウザのダウンロード機能を利用します。保存先を確認してください。
- 固定費は月額一括設定。固定費の明細入力は未実装。
- 月途中のスコア基準変更は翌月反映ですが、利用開始月の初期基準は初回設定時点で固定されます。
- 将来日付の支出・貯金登録は不可。過去月の再計算値と確定スコアは別に表示。

## ファイル構成

- `index.html` 画面
- `style.css` デザイン
- `app.js` データ管理・スコア計算・画面・Firebase同期
- `firebase-config.js` Firebase接続設定
- `firestore.rules` ユーザー別アクセス制限
- `manifest.webmanifest` / `icon.svg` PWA
- `sw.js` オフラインキャッシュ
