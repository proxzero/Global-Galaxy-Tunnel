# Galaxy-Tunnel

> Cloudflare Worker ပေါ်မှာ run မယ့် **VLESS + Trojan** proxy tunnel။
> IPv4 / IPv6 နှစ်ခုလုံး support လုပ်ထားပြီး **Race Dial** နဲ့ **Auto Fallback** ပါတယ်။

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| **Dual Protocol** | VLESS + Trojan တစ်ခုတည်းမှာ run တယ် |
| **IPv4 + IPv6** | Proxy pool မှာ IPv4 / IPv6 domain/IP နှစ်ခုလုံးထည့်ထားတယ် |
| **Race Dial** | Direct + Proxy candidate တွေကို တစ်ချိန်တည်း connect လုပ်ပြီး အမြန်ဆုံး winner ကို ယူသည် |
| **Auto Health Check** | 1 မိနစ်တိုင်း proxy pool ကို health check လုပ်ပြီး dead proxy တွေ auto skip |
| **DoH (DNS over HTTPS)** | 9 ခု provider (Cloudflare, Google, Quad9, AliDNS, ...) |
| **External Proxy List** | GitHub raw URL ကနေ proxy list dynamically load လုပ်နိုင်တယ် (optional) |
| **SNI Spoofing** | Visa, Government, CDN domain တွေနဲ့ clean SNI |

---

## 🚀 Deploy လုပ်ပုံ (3 Steps)

### 1. GitHub Repo တင်ပါ

```
galaxy-tunnel/
├── index.js          ← Main Worker Code
├── wrangler.toml     ← Config
└── README.md         ← This file
```

Repo ကို **Public** (သို့) **Private** လုပ်ပြီး push လိုက်ပါ။

---

### 2. Cloudflare Dashboard မှာ Worker ဆောက်ပါ

1. [dash.cloudflare.com](https://dash.cloudflare.com) → **Workers & Pages** → **Create**
2. **"Create from GitHub"** ကို ရွေးပါ
3. Repo ကို link လုပ်ပြီး **"Begin setup"**
4. **Build settings** မလိုပါဘူး — **"Deploy"** နှိပ်လိုက်ပါ

(သို့မဟုတ် **"Create a Service"** → Code editor မှာ `index.js` ကို copy-paste လုပ်လည်းရတယ်)

---

### 3. Environment Variables ထည့်ပါ

Dashboard → Worker → **Settings** → **Variables and Secrets**

| Name | Type | Value | Required |
|------|------|-------|----------|
| `UUID` | Secret | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | ✅ VLESS အတွက် |
| `TROJAN_PASS` | Secret | `your-strong-password` | ✅ Trojan အတွက် |
| `PROXY_LIST_URL` | Text | `https://raw.githubusercontent.com/...` | ❌ Optional |

> **Note:** `UUID` နဲ့ `TROJAN_PASS` နှစ်ခုစလုံး ထားရင် နှစ်ခုလုံး အလုပ်လုပ်မယ်။ တစ်ခုပဲ ထားရင် တစ်ခုပဲ အလုပ်လုပ်မယ်။

---

## 🔧 Proxy Pool ကိုပြင်ချင်ရန်

`index.js` ထဲက `PROXY_POOL` array မှာ တိုက်ရိုက် ထည့်လို့ရတယ်။

```javascript
const PROXY_POOL = [
    // IPv4
    "104.16.85.20",
    "172.64.155.1",

    // IPv6 (brackets နဲ့ ထည့်)
    "[2606:4700:4700::1111]",
    "[2606:4700:4700::1001]",

    // Domain
    "cdn.xn--b6gac.eu.org",
    "www.visa.com",
];
```

---

## 📡 External Proxy List (Optional)

GitHub repo မှာ `PROXYIP.txt` ဆောက်ပြီး raw URL ကို `PROXY_LIST_URL` မှာ ထည့်လို့ရတယ်။

**PROXYIP.txt format:**
```
# Comment
cdn.xn--b6gac.eu.org
104.16.85.20
[2606:4700::6810:8514]
www.visa.com
```

**ကောင်းကျိုးဆိုးကျိုး:**
- ✅ Update မလိုဘဲ GitHub file edit လုပ်ရုံနဲ့ proxy list update လို့ရတယ်
- ✅ Worker redeploy မလိုဘူး
- ⚠️ GitHub down ရင် fallback လုပ်ပြီး hardcoded pool ကို သုံးတယ်

---

## 🛡️ Security Notes

- `UUID` ကို **Secret** အနေနဲ့ ထားပါ — Dashboard မှာ **Encrypt** လုပ်ထားလို့ပိုကောင်းတယ်
- `TROJAN_PASS` လည်း **Secret** အနေနဲ့ ထားပါ
- `PROXY_LIST_URL` ကို public GitHub raw URL အနေနဲ့ ထားလို့ရတယ် (sensitive မဟုတ်လို့)

---

## 📄 License

MIT — ကိုယ့်အလိုလိုသုံးပါ။
