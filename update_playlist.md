## 🎶 Import Your Spotify Playlist into Terraform State

To make Terraform manage your **own Spotify playlist** (instead of creating a new one every time), follow these steps carefully:

---

### 🧱 1. Initialize Terraform

Open your terminal in the project root and run:

```bash
terraform init
```

---

### 🛠️ 2. Clone and Build Patched Spotify Auth Proxy

Clone the patched Terraform Spotify provider and build the `spotify_auth_proxy` binary:

```bash
git clone -b fix/base-url-127 https://github.com/gauravslnk/terraform-provider-spotify
cd terraform-provider-spotify/spotify_auth_proxy
go build -o spotify_auth_proxy.exe
```

> ✅ This builds the proxy binary required for authorization and token generation.

---

### 🔐 3. Set Required Environment Variables

Replace placeholders with your actual Spotify app credentials:

```powershell
$env:SPOTIFY_CLIENT_ID = "<your_client_id>"
$env:SPOTIFY_CLIENT_SECRET = "<your_client_secret>"
$env:SPOTIFY_REDIRECT_URI = "<http://127.0.0.1:8080/spotify_callback>"  # add your redirect uri which you set in spotify developer app
$env:SPOTIFY_PROXY_BASE_URI = "http://127.0.0.1:8080"
```

---

### 🚪 4. Start the Spotify Auth Proxy

```powershell
.\spotify_auth_proxy --port 8080
```

It will output something like this:

```
APIKey: gS4pTULOzrmczFBC3d4olxxtWBss-j4zt-gvQIaiPYaymedCSggxigu7Ksjgq4gS
Auth URL: http://127.0.0.1:8080/authorize?token=...
Listening on: 127.0.0.1:8080
```

> ⚠️ **Do not close this terminal** — keep it running.

---

### ✅ 5. Authorize Access

Open the **Auth URL** in your browser, log in, and authorize the app.

You should see:

```
Authorization successful
Token Retrieved
```

---

### ⛓️ 6. Import Your Playlist into Terraform State

1. Open a **new terminal window**.
2. In the new terminal, run:

```bash
terraform import spotify_playlist.playlist <your_playlist_id>
```

Example:

```bash
terraform import spotify_playlist.playlist 5GkQIP5IetMlF4E7IoUayV
```

3. When prompted for `SPOTIFY_API_KEY`, **paste the API key** shown in the other terminal.
4. Wait for a success message like:

```
spotify_playlist.playlist: Import prepared!
```

---

### 🧾 Result

Your playlist is now fully tracked by Terraform! ✅

* It will **no longer create new playlists** on each run.
* Terraform will **apply updates** only to this existing playlist.
