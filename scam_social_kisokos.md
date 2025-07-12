
# 🔐 Scam és Social Engineering Kisokos

Ez a dokumentum a csalások (scam-ek) és a social engineering támadások felismerésében és kivédésében segít.  
Különösen hasznos lehet IT-szakembereknek, önálló tanulóknak, HomeLab-építőknek és bárkinek, aki tudatosan szeretné védeni magát az interneten.

---

## I. 🧠 Alapfogalmak

### 🎭 Scam
Online csalás, amelynek célja az áldozat megtévesztése személyes adatok, pénz vagy hozzáférés megszerzése érdekében.

### 🕵️‍♂️ Social Engineering
Olyan pszichológiai manipuláció, amely során a támadó emberek viselkedésére, bizalmára építve jut hozzá információkhoz vagy jogosultságokhoz.

### 📩 Phishing
Egyfajta scam, amely e-mailben, hamis weboldalakon keresztül próbálja megszerezni a belépési adatokat vagy más érzékeny információt.

---

## II. 📬 Leggyakoribb támadási csatornák

- **Email** – Hamis toborzók, banki értesítések, linkek, csatolmányok.
- **LinkedIn** – Kamu toborzók, generikus ajánlatok, Gmail/Outlook címek.
- **Messenger/WhatsApp/Telegram** – Bizalomépítés, majd link vagy kérés.
- **Telefon/SMS** – Kamu call center, visszahívást kérő üzenetek.
- **Hamis weboldalak** – Hitelesnek tűnő URL-ek (pl. g00gle.com).

---

## III. 🎯 Miért éppen téged?

- Nyilvános e-mail címed van.
- Virálissá vált egy posztod, így megnőtt a láthatóságod.
- Technikai profil → feltételezik, hogy rendszerekhez férsz hozzá.
- Kevés ismerős → kisebb az ellenőrzési lehetőség.
- Nincs 2FA vagy jelszókezelő → sebezhetőbb vagy.

---

## IV. 🚨 Tipikus figyelmeztető jelek

| Jelzés | Miért gyanús? |
|-------|---------------|
| Gmail/Outlook cím | Nem hivatalos e-mail |
| Generikus szöveg | Nem rád szabott |
| Sürgetés | Nyomásgyakorlás |
| Átirányít másra | Tipikus social engineering |
| Hiányzó cégnév vagy pozíció | Gyanús, általános megfogalmazás |

---

## V. 🧯 Mit tegyél?

- Ne kattints, ne válaszolj, ne tölts le semmit.
- Ha megtörtént: **jelszócsere, vírusellenőrzés, kijelentkezés minden eszközről**.
- Kapcsold be a **kétlépcsős hitelesítést (2FA)**.
- Használj jelszókezelőt (Bitwarden).
- **Ne szégyelld – tanulj belőle.**

---

## VI. 🧰 Védekezési alapok

- Bitwarden → egyedi, erős jelszavak
- 2FA mindenhol (Gmail, LinkedIn, GitHub, stb.)
- LinkedIn email cím elrejtése
- Külön e-mail címek különböző célokra
- Elavult fiókok törlése
- GitHub tokenek és `.env` fájlok tisztítása

---

## VII. 🧪 Való életből vett eset (anonimizálva)

- Virális komment alatt kamu toborzók bukkantak fel
- CrowdStrike-re hivatkozó, Gmail-es scammer
- Átirányított egy másik „tanácsadóra”
- Kétes profil, elfogadva majd törölve
- E-mail láthatóság kikapcsolva, jelszavak ellenőrizve (Have I Been Pwned)
- Bitwarden aktív, 2FA beállítása folyamatban
- **Nem történt adatvesztés** → időben észlelve

---

## VIII. ✅ Gyorsellenőrző lista

### Scam felismeréshez:
- [ ] Gmail vagy Outlook e-mail cím?
- [ ] Generikus szöveg?
- [ ] Hiányzik a cég vagy pozíció neve?
- [ ] Sürget, hogy válaszolj vagy kattints?
- [ ] Átirányít valaki másra?

**→ 3+ igen = ne válaszolj, ne kattints!**

### Védelem státusz:
- [x] Bitwarden használatban
- [x] E-mail cím rejtve
- [~] 2FA bevezetés alatt
- [~] Watchtower (szivárgásfigyelés) aktiválása folyamatban

---

## IX. 🧾 Összefoglalás

Ez a kisokos valós tapasztalatokon alapuló gyakorlati útmutató.  
Segít felismerni, elkerülni és kezelni az internetes csalásokat, adathalász támadásokat és social engineering manipulációkat.

Tudás = Védelem.  
Tudatosság = Előny.  
Kitartás = Te.
