# GitHub Pages での認証を追加する（Firebase Authentication）

GitHub Pages は静的ホスティングのためサーバー側の処理を実行できません。そこで、クライアントサイド（ブラウザ）の JavaScript で動作する Firebase Authentication を使って認証を実装します。

## 準備
- Firebase コンソールに Google アカウントでログインする。
- GitHub Pages（例: `yourname.github.io`）で公開するリポジトリを用意する。

## 手順概要
1. Firebase プロジェクトを作成
2. ウェブアプリを登録して `firebaseConfig` を取得
3. 認証プロバイダ（メール/Google）を有効化
4. GitHub Pages ドメインを承認済みドメインに追加
5. HTML/JavaScript に Firebase を組み込む

---

## 1. Firebase プロジェクトの作成
1. Firebase コンソールにアクセスしてログイン。
2. 「プロジェクトを追加」をクリックし、任意の名前（例: `my-github-page-auth`）で新規作成する。
3. Google アナリティクスは任意（今回はオフでも可）。

## 2. ウェブアプリの登録と設定情報の取得
1. プロジェクトの概要ページで「ウェブアプリ（</>）」を選択。
2. アプリのニックネームを入力して「アプリを登録」する（Hosting のチェックは不要）。
3. 表示される `const firebaseConfig = { ... };` のコードブロックをコピーして控える。

例（実際の値はコンソールで取得してください）:
```
const firebaseConfig = {
	apiKey: "YOUR_API_KEY",
	authDomain: "your-project.firebaseapp.com",
	projectId: "your-project-id",
	storageBucket: "your-project.appspot.com",
	messagingSenderId: "...",
	appId: "..."
};
```

## 3. 認証方法の有効化
1. 左メニューの「構築」→「Authentication」を開き、「始める」をクリック。
2. 「ログイン方法」タブで必要なプロバイダを有効化する。一般的には次を有効にします:
	 - メール / パスワード: 「有効にする」をオン
	 - Google: 「有効にする」をオン（サポートメールを選択して保存）

## 4. GitHub Pages ドメインの許可
Firebase は許可されたドメインからのリクエストのみ受け付けます。
1. Authentication の「設定」タブを開く。
2. 「承認済みドメイン」セクションで `yourname.github.io`（またはカスタムドメイン）を追加する。
3. ローカル検証のために `localhost` は初めから含まれています。

## 5. HTML/JavaScript に組み込む
1. `index.html` などのファイルに Firebase SDK と初期化コードを追加します。

最小の例:
```
<!-- Firebase App (the core Firebase SDK) -->
<script src="https://www.gstatic.com/firebasejs/9.XX.X/firebase-app.js"></script>
<!-- If you use Firebase Authentication -->
<script src="https://www.gstatic.com/firebasejs/9.XX.X/firebase-auth.js"></script>

<script>
	// 手順2で控えた設定をここに貼る
	const firebaseConfig = { /* ... */ };
	firebase.initializeApp(firebaseConfig);

	// 例: メールでサインインする簡単な関数
	async function signInWithEmail(email, password) {
		try {
			const userCredential = await firebase.auth().signInWithEmailAndPassword(email, password);
			console.log('Signed in:', userCredential.user);
		} catch (err) {
			console.error(err);
		}
	}
</script>
```

注意点:
- Firebase のバージョン（スクリプト URL の `9.XX.X`）は適宜最新のものを使ってください。
- GitHub Pages 上で動作させる前にローカルで `localhost` を使って動作確認するとデバッグが楽です。

---

## 補足
- カスタムドメインを使う場合は、そのホスト名を承認済みドメインに追加してください。
- より高度なセッション管理やサーバー処理が必要な場合は、Firebase Functions や別のバックエンドを検討してください。

以上で、`index.html` に AWS 等のサーバー無しで簡単に Firebase Authentication を組み込めます。
