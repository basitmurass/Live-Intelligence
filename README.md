# Live Event Intelligence

Event/ijtima attendance app — CNIC scan (front/back), vehicle entry, live dashboard, aur **Google Sheet me live sync** (Google Forms ke zariye — koi key/server nahi chahiye).

## Files

| File | Kaam |
|---|---|
| `index.html` | Poori app (single file — as-is deploy hoti hai) |
| `README.md` | Ye file |

## Features

- **Attendee entry** — CNIC front scan (name, father name, CNIC, DOB auto-fill), back scan (address auto-fill), ya manual entry
- **Vehicle entry** — Car, Motorcycle, Rickshaw, Qingqi, Van/Hiace, Bus, Truck + number plate
- **Live dashboard** — total, men/women/children, top cities, vehicle breakdown
- **Entry logs** — search/filter, CSV import, Excel/CSV export, PDF report, WhatsApp share
- **Bulk import** — register ki photo se multiple entries
- **Trash** — delete hui entries recover ho sakti hain
- **Access control (license)** — client ko plan + **duration (days)** ke saath access grant karo; expiry par app khud lock
- **Google Sheet live sync** — har entry `Entries` form me, har license `Access` form me — seedha sheet me live. Internet na ho to queue me rehta hai, wapas aate hi sync.
- **Payment flow** — JazzCash/EasyPaisa (+92 3039982013) → WhatsApp confirm → organizer verify karke Access Control me license deta hai
- Mobile responsive + dark mode

---

## Setup — sirf 2 steps

### STEP 1: Forms ke responses sheet se joro (2 min)

Dono forms pehle se bane hue hain:

1. [LiveEvent-Entries form](https://docs.google.com/forms/d/1522l-_x2u17Yyjb_SEb4q17oQz7iwvBdJN6gEo_VrJw/edit) kholo → **Responses** tab → **⋮** (teen dots) → **Select response destination** → **Select existing spreadsheet** → **Live Event Data** select karo → naya tab ban jayega
2. [LiveEvent-Access form](https://docs.google.com/forms/d/1adx6Nk_Spsx-iqKHUXb63bY6vJfQiWnF53Rdi3oAIr4/edit) ke saath yehi karo

> Pehli test entry ("TEST ENTRY (delete me)") sheet me nazar aayegi — use delete kar dena.

### STEP 2: GitHub + Vercel (pehle ho chuka)

```bash
git add index.html README.md
git commit -m "Live Event v3 - Google Forms live sync"
git push
```

Vercel khud dobara deploy kar lega (GitHub se connected hai).

---

## Roz ka istemal

**Access dena (payment ke baad):**
1. Client JazzCash/EasyPaisa bhej kar WhatsApp par confirm karta hai
2. Tum: **Access Control** → admin password → **Grant access** form bharo (naam, **login wali email**, plan, **kitne din**)
3. **Grant access & push to sheet** — record foran Google Sheet ke Access form ke tab me
4. Client ka access foran active ho jata hai

**Entry karna:**
1. **Attendee Entry** → **Scan CNIC front** → naam/CNIC/DOB khud bhar jayenge (check zaroor karo)
2. **Scan CNIC back** → address khud bhar jayega
3. Vehicle ho to **Arrived by** me type + number likho
4. **Save & record entry** — dashboard update + sheet me live row

**Access khatam:** muddat poori hone par client ka dashboard/entry band — sirf renew screen nazar aayegi.

**Admin password** default `admin123` hai — app me **Sync Settings** me ja kar foran badal lo.

## Troubleshooting

| Masla | Hal |
|---|---|
| Sheet me row nahi a rahi | Form ka **Responses** tab kholo — wahan hai? Agar hai to response destination sheet se linked nahi (Step 1 dobara karo) |
| `● N pending…` atka hua | Internet check karo, phir **Sync Settings → Sync now** dabao |
| Camera nahi khul raha | Browser ko camera permission do; https URL use karo |
| OCR ghalat parh raha hai | Roshni behtar karo, card frame me seedha rakho; raw text dekh kar manual theek karo |

## Zaroori note

- CNIC data sirf counting/entry ke liye hai — koi NADRA verification nahi hoti.
- Photos browser me rehti hain, Google Sheet me nahi jatin (sirf text data jata hai).
