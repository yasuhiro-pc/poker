# レンジ道場 — iPhoneアプリ化の手順

## 中身

| ファイル | 役割 |
|---|---|
| `index.html` | アプリ本体(単一ファイル。Nash解・エクイティ表を内蔵) |
| `manifest.webmanifest` | アプリ名・アイコン・全画面表示の設定 |
| `sw.js` | Service Worker。オフラインでも起動できる |
| `icon-*.png` / `apple-touch-icon.png` | ホーム画面アイコン |

---

## 手順1: 公開する(5分)

PWAは **HTTPS上でないとService Workerが動きません**。以下のどれかに置いてください。

### Cloudflare Pages / Netlify(最も簡単)
1. [Cloudflare Pages](https://pages.cloudflare.com/) または [Netlify Drop](https://app.netlify.com/drop) を開く
2. このフォルダごとドラッグ&ドロップ
3. 発行されたURL(例: `https://range-dojo.pages.dev`)を共有

### GitHub Pages
```bash
git init && git add . && git commit -m "range dojo"
git branch -M main
git remote add origin git@github.com:<user>/range-dojo.git
git push -u origin main
```
リポジトリ → Settings → Pages → Source を `main` / `root` に設定。

---

## 手順2: iPhoneのホーム画面に追加

1. **Safariで**URLを開く(Chromeなど他ブラウザだと「ホーム画面に追加」が出ません)
2. 共有ボタン → 「ホーム画面に追加」
3. アイコンから起動するとアドレスバーのない全画面アプリになります

共有相手にはURLを送り、同じ手順を案内するだけです。インストール数の制限はありません。

---

## 手順3(任意): App Storeで配信する

PWAで足りない場合のみ。以下が必要です。

- Apple Developer Program(年間 US$99)
- Mac + Xcode

```bash
npm init -y
npm i @capacitor/core @capacitor/cli @capacitor/ios
npx cap init "レンジ道場" com.example.rangedojo --web-dir=.
npx cap add ios
npx cap sync
npx cap open ios      # Xcodeが開く
```

Xcodeで署名設定 → Archive → App Store Connect へアップロード。

- **TestFlight**: 審査が軽く、最大10,000人に配布可能。知人への共有ならこれで十分です
- **App Store公開**: 通常の審査あり。ギャンブル関連は審査が厳しめなので、実際の賭博機能がない学習ツールである点を説明文で明確にしてください

---

## データの保存について

ホスティング後は `localStorage` が使えるため、設定・解答履歴・マイハンドが端末に自動保存されます。
ただし端末ローカルなので、機種変更時は「マイハンド」タブの **JSON書き出し** でバックアップしてください。

複数端末で同期したい場合は、保存先を Supabase や Firebase に差し替える改修が必要です。
