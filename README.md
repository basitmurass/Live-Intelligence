# Live Event Intelligence

Event/ijtima attendance app — CNIC scan (front/back), vehicle entry, live dashboard, aur **Google Sheet me live sync**.

## Files

| File | Kaam |
|---|---|
| `index.html` | Poori app (single file — as-is deploy hoti hai) |
| `live-event-worker.js` | Cloudflare Worker — Google Sheet me live likhta hai |
| `README.md` | Ye file |

## Features

- **Attendee entry** — CNIC front scan (name, father name, CNIC, DOB auto-fill), back scan (address auto-fill), ya manual entry
- **Vehicle entry** — Car, Motorcycle, Rickshaw, Qingqi, Van/Hiace, Bus, Truck + number plate
- **Live dashboard** — total, men/women/children, top cities, vehicle breakdown
- **Entry logs** — search/filter, CSV import, Excel/CSV export, PDF report, WhatsApp share
- **Bulk import** — register ki photo se multiple entries
- **Trash** — delete hui entries recover ho sakti hain
- **Access control (license)** — client ko plan + **duration (days)** ke saath access grant karo; expiry par app khud lock
- **Google Sheet live sync** — har entry `Entries` tab me, har license `Access` tab me, foran. Internet na ho to queue me rehta hai, wapas aate hi sync.
- **Payment flow** — JazzCash/EasyPaisa → WhatsApp confirm → organizer verify karke Access Control me license deta hai
- Mobile responsive + dark mode

---

## Setup — Step by Step

### STEP 1: Google Sheet banao (5 min)

1. [sheets.google.com](https://sheets.google.com) par nayi spreadsheet banao, naam rakho `Live Event Data`
2. Pehle tab ka naam rakho **`Entries`**, pehli row me ye headers likho:

```
Timestamp | Name | Father Name | CNIC | Phone | WhatsApp 2 | WhatsApp 3 | DOB | Address | Country | City | Men | Women | Children | Total | Vehicle Type | Vehicle Number | Occupation | Entry Mode | Added By
```

3. `+` se doosra tab banao, naam **`Access`**, headers:

```
Granted At | Client Name | Email | Phone | Plan | Days | Start Date | End Date | Amount | Method | Status | Notes
```

4. Sheet ki URL se **Sheet ID** copy karo:
   `https://docs.google.com/spreadsheets/d/`**`YEH_WALA_ID`**`/edit`

### STEP 2: Service Account banao (10 min)

1. [console.cloud.google.com](https://console.cloud.google.com) → naya project banao (naam: `live-event-app`)
2. **APIs & Services → Library** → `Google Sheets API` search karke **Enable** karo
3. **APIs & Services → Credentials → Create Credentials → Service Account**
   - Naam: `sheets-writer` → Create → Continue → Done
4. Service account par click → **Keys → Add Key → Create new key → JSON** → file download ho jayegi
5. JSON file kholo, **`client_email`** copy karo (jaise `sheets-writer@live-event-app.iam.gserviceaccount.com`)
6. Wapas Sheet par jao → **Share** → client_email paste karo → role **Editor** → Send

### STEP 3: Cloudflare Worker lagao (5 min)

1. [dash.cloudflare.com](https://dash.cloudflare.com) → **Workers & Pages → Create → Create Worker** → naam rakho `live-event`
2. `live-event-worker.js` ka poora code copy karke worker me paste karo → **Deploy**
3. Worker → **Settings → Variables → Add Variable** (type: **Secret**):
   - `SHEET_ID` = Step 1 wala Sheet ID
   - `SA_JSON` = Step 2 wali JSON file ka **poora text**
   - `API_KEY` = khud koi lamba random password banao (jaise `Le9x$...`)
   - (optional) `ENTRIES_TAB` = `Entries`, `ACCESS_TAB` = `Access`
4. **Save** → worker ka URL copy karo (jaise `https://live-event.xxx.workers.dev`)

### STEP 4: App me settings lagao (2 min)

1. App kholo → login → **Sync Settings**
2. **Worker URL** aur **API Key** paste karo → Save
3. **Test connection** dabao — `Connected ✅` aana chahiye
4. **Admin password** badal lo (default: `admin123`)

### STEP 5: GitHub par upload (2 min)

```bash
git init
git add index.html live-event-worker.js README.md
git commit -m "Live Event Intelligence v2 - sheet sync"
git branch -M main
git remote add origin https://github.com/TUMHARA-USERNAME/live-event-app.git
git push -u origin main
```

### STEP 6: Vercel par deploy (2 min)

1. [vercel.com](https://vercel.com) → **Add New → Project** → GitHub repo select karo
2. Framework: **Other** (kuch change nahi karna) → **Deploy**
3. Milne wali URL (jaise `live-event-app.vercel.app`) clients ko de do

> Note: Worker ka code Vercel par **nahi** jayega — wo Cloudflare par alag rehta hai. App me sirf uska URL lagta hai.

---

## Roz ka istemal

**Access dena (payment ke baad):**
1. Client JazzCash/EasyPaisa bhej kar WhatsApp par confirm karta hai
2. Tum: **Access Control** → admin password → **Grant access** form bharo (naam, **login wali email**, plan, **kitne din**)
3. **Grant access & push to sheet** — record foran Google Sheet ke `Access` tab me
4. Client ka trial/expired access foran active ho jata hai

**Entry karna:**
1. **Attendee Entry** → **Scan CNIC front** → naam/CNIC/DOB khud bhar jayenge (check zaroor karo)
2. **Scan CNIC back** → address khud bhar jayega
3. Vehicle ho to **Arrived by** me type + number likho
4. **Save & record entry** — dashboard update + Sheet me live row

**Access khatam:** muddat poori hone par client ka dashboard/entry band — sirf renew screen nazar aayegi.

## Troubleshooting

| Masla | Hal |
|---|---|
| `Sheet not connected` | Sync Settings me Worker URL + API Key check karo, Test dabao |
| Sheet me row nahi a rahi | Worker → Settings → Variables me `SA_JSON`/`SHEET_ID` check; sheet service account se **shared** hai? |
| `Failed: ... 401` | API key app aur worker me same honi chahiye |
| Camera nahi khul raha | Browser ko camera permission do; localhost ki bajaye https URL use karo |
| OCR ghalat parh raha hai | Roshni behtar karo, card frame me seedha rakho; raw text dekh kar manual theek karo |

## Zaroori note

- `Payment` screen me JazzCash number `+92 3039982013` likha hai — **yeh number verify kar lena** (ek digit kam lag raha hai). `index.html` me `PAYMENT_NUMBER` search karke badal do.
- CNIC data sirf counting/entry ke liye hai — koi NADRA verification nahi hoti (app me disclaimer mojood hai).
- Photos browser me rehti hain, Google Sheet me nahi jatin (sirf text data jata hai).
