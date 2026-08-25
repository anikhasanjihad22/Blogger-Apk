# NexFlix APK — GitHub theke direct build kora

Eta "TWA" (Trusted Web Activity) style APK — mane tomar PWA-i ekta real Android app hishebe wrap hoye jabe, r GitHub Actions (GitHub-er nijer "terminal"/server) e build hobe. Tomar phone/PC te kichu install lagbe na.

## Step 1 — Repo banao
GitHub e ekta notun repo banao (public ba private, dutai chalbe), tar moddhe eই file gulo push koro:
```
twa-manifest.json
.github/workflows/build-apk.yml
```
(Ei message-er shathe attach kora ache — package_apk-setup.zip)

## Step 2 — Signing keystore banao (nijer PC te, ekbar)
Terminal e (Windows hole Git Bash/CMD e Java thakte hobe):
```bash
keytool -genkeypair -v -storetype PKCS12 \
  -keystore android.keystore \
  -alias nexflix \
  -keyalg RSA -keysize 2048 -validity 10000
```
Password chaibe — mone rekho, GitHub Secret e lagbe. Eta banano hoye gele ei file **kaokeo dio na, GitHub e push korba na** — GitHub Secret e শুধু encode kore rakhbo.

## Step 3 — GitHub Secrets add koro
Repo → **Settings → Secrets and variables → Actions → New repository secret**, ei 3 ta banao:

| Secret name | Value |
|---|---|
| `ANDROID_KEYSTORE_BASE64` | নিচের command er output |
| `ANDROID_KEYSTORE_PASSWORD` | keystore banabar somoy je password disho |
| `ANDROID_KEY_PASSWORD` | shadharonoto same password |

Base64 বানাতে:
```bash
base64 -w 0 android.keystore
```
(Mac hole: `base64 -i android.keystore`) — output ta copy kore `ANDROID_KEYSTORE_BASE64` e paste koro.

## Step 4 — Workflow run koro
Repo → **Actions** tab → "Build NexFlix APK" → **Run workflow** button e click.
2-3 minute e build shesh hobe. Neeche **Artifacts** e `nexflix-apk` r `nexflix-aab` pabe — click kore download.

## Step 5 — assetlinks.json (important, na dile browser bar dekhabe app e)
Build shesh howar por, tomar keystore-er SHA256 fingerprint বার korte hobe:
```bash
keytool -list -v -keystore android.keystore -alias nexflix
```
"SHA256:" line ta copy koro. Tarpor ei file ta banao:

`https://www.rnexflix.top/.well-known/assetlinks.json`
```json
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "top.rnexflix.twa",
    "sha256_cert_fingerprints": ["TOMAR_SHA256_FINGERPRINT_EITA_TE_BOSHAO"]
  }
}]
```
Eta site-er root e upload kore dile app fullscreen chalbe (address bar chara).

## Notes
- `twa-manifest.json` e `packageId` = `top.rnexflix.twa` dewa ache — Play Store e publish korle eta unique hote hobe, change korte parbo dorkar hole.
- Play Store e dite chaile `nexflix-aab` file ta lagbe (APK na, AAB).
- Shudhu test/sideload korte chaile `nexflix-apk` file phone e direct install kora jabe.
