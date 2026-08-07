# Lembar Validasi Aturan — Layer ↔ Klassifizierung

**Tujuan:** memvalidasi aturan `check-layer-classification` bersama arsitek.
Dasar aturan: [flow-task-layer-class.md](flow-task-layer-class.md).
Bukti dari data: [FAVORITEN_AUDIT_STERN.md](req/FAVORITEN_AUDIT_STERN.md) (504 favorit aktif).

**Cara pakai:** kolom **Status** menunjukkan keyakinan aturan. Baris ber-flag
❓ / ⚠️ butuh keputusan arsitek — nomor `[Q#]` menautkan ke daftar pertanyaan di §3.

Legenda status:
`✅ Terkonfirmasi` = data favorit 100% mendukung ·
`⚠️ Konflik` = data favorit melanggar aturan ·
`❓ Belum jelas` = data tidak cukup / nama layer tidak terekspor ·
`⏭️ Skip` = sengaja di luar cakupan.

---

## 1. Tabel Aturan Dasar (Base: 1 Layer → 1 Klasifikasi)

| Layer | Klasifikasi wajib | Status | Catatan / bukti favorit |
|-------|-------------------|:------:|--------------------------|
| 002 Fixpunkt Kugel | ELEMENTE | ⚠️ | 1 favorit `(ohne)` (Vermessungspunktanzeige) — salah favorit? `[Q7]` |
| 004 Bezugspunkt | Tiefbau Element | ✅ | 4/4 cocok |
| 010 Aussenwände | Wand | ✅ | 6/6 cocok |
| 011 Fassadensystem | Wand | ✅ | 8/8 cocok |
| 015 Innenwände | Wand | ✅ | 4/4 cocok |
| 016 Trennwände | Wand | ✅ | 3/3 cocok |
| 017 Wandbekleidung | Bekleidung / Belag | ✅ | 5/5 cocok |
| **020 Stützen** | **Stütze / Pilaster** | ⚠️ | Hanya 4/17 cocok. Ada Stahl, Dach, Fenster, Tür, Wand `[Q1]` |
| 021 Fenster Zubehör | Fenster | ✅ | 14/14 cocok |
| 022 Tür Zubehör | Tür | ✅ | 10/10 cocok |
| 025 Unterzüge | Balken / Unterzug | ✅ | 4/4 cocok |
| **026 Träger** | **Balken / Unterzug** | ⚠️ | Hanya 6/23 cocok. Ada Stahl, Wand, Haustechnik, Bedarfskörper, Fenster, Dach `[Q2]` |
| 029 Decken Grundriss | Decke | ❓ | Tidak ada favorit di layer ini — belum terverifikasi `[Q8]` |
| 030 Bodenaufbauten | Decke | ✅ | 4/4 cocok |
| 035 Decken | Decke | ✅ | 10/10 cocok |
| **036 Deckenbekleidung** | **Bekleidung / Belag** | ⚠️ | 13/14 cocok; 1 favorit `Wand` `[Q4]` |
| 040 Dächer | Dach | ✅ | cocok |
| 041 Dächer Grundrisslinie | Dach | ✅ | cocok |
| 042 Dächer Zubehör | Dach | ✅ | 15 Dach + 4 Dachfenster (via override) |
| 045 Dachkonstruktionen | Dach | ✅ | 3/3 cocok |
| 046 Stahlkonstruktionen | Stahl | ✅ | 4/4 cocok |
| **050 Geländer** | **Absturzsicherung** | ⚠️ | 8/13 cocok; ada Treppe, Einrichtung `[Q3]` |
| 053 Treppenuntersicht | Treppe | ✅ | 2/2 cocok |
| 054 Treppen Zubehör | Treppe | ❓ | Tidak ada favorit — belum terverifikasi `[Q8]` |
| 055 Treppen | Treppe | ✅ | 25/25 cocok |
| **060 Möbel Einbau** | **Einrichtung** | ⚠️ | 116/117 cocok; 1 favorit `Möbel` `[Q4]` |
| 080 Raum Innen | Geschossfläche | ❓ | Tidak ada favorit — belum terverifikasi `[Q8]` |
| 090 KNGW Wand | ELEMENTE | ✅ | cocok |
| 091 KNGW Decke | ELEMENTE | ✅ | 4/4 cocok |
| 300/305/310/315/320 Komponenten | Haustechnische Komp. (allg.) | ✅ | cocok |
| 331 Feuerlöschposten | Feuerlöscheinrichtung | ⚠️ | 4/5 cocok; 1 favorit `Einrichtung` `[Q4]` |
| 360/361/362 Rohr/Kanal/Trasse | Haustechnische Komp. (allg.) | ✅ | cocok |
| 400 Terrain gewachsen | Tiefbau Element | ✅ | cocok |
| 410 Umgebung | Tiefbau Element | ✅ | 7/7 cocok |
| 411 Bäume | Tiefbau Element | ✅ | 2/2 cocok |
| 412 Bestockte Fläche | Tiefbau Element | ✅ | 4/4 cocok |
| 450 Kanalisation Schacht | Haustechnische Komp. (allg.) | ✅ | 5/5 cocok |
| 700 Operatoren | Bedarfskörper | ✅ | 6/6 cocok |

---

## 2. Tabel Override (Layer + Tipe Elemen → daftar diizinkan)

| Layer | Tipe | Diizinkan | Status | Catatan |
|-------|------|-----------|:------:|---------|
| 010 Aussenwände | Window | Fenster / Nische | ✅ | Terbukti (7 Fenster + 4 Nische) |
| 015 Innenwände | Window | Fenster / Nische | ✅ | Terbukti |
| 017 Wandbekleidung | Window | Fenster | ✅ | — |
| 010 Aussenwände | Door | Tür | ✅ | — |
| **015 Innenwände** | Door | Tür / **Einrichtung** | ❓ | Einrichtung hanya sah di sini — apakah benar? `[Q6]` |
| 016 Trennwände | Door | Tür | ✅ | — |
| 042 Dächer Zubehör | Object | Dach / Dachfenster | ✅ | Terbukti (4 Dachfenster-Object) |
| 042 Dächer Zubehör | Door | Tür | ❓ | Tidak ada favorit Door di 042 — belum terverifikasi `[Q8]` |
| 060 Möbel Einbau | Door | Tür | ❓ | Tidak ada favorit Door di 060 — belum terverifikasi `[Q8]` |

---

## 3. Titik Kebingungan & Pertanyaan untuk Arsitek

> Setiap pertanyaan menunjuk ke baris `[Q#]` di tabel atas. Jawaban arsitek
> menentukan apakah aturan diubah, ditambah override, atau favorit diperbaiki.

### 🔴 Kebingungan Utama — Layer Konstruksi "Bersama"

Layer `020 Stützen`, `026 Träger`, `050 Geländer` ternyata **menampung banyak
klasifikasi berbeda** di favoriten, padahal flow mengasumsikan satu layer =
satu klasifikasi. Ini sumber konflik terbesar (35 favorit).

**[Q1] Layer `020 Stützen`** memuat kolom dengan klasifikasi: Stütze/Pilaster,
Stahl (kolom `[Sk]`), Dach (kolom dukungan atap), serta Fenster/Tür/Wand
(kolom "Aussenverkleidung"). Pertanyaan:
- Apakah **layer `020` boleh berisi lebih dari satu klasifikasi**? Jika ya,
  daftar mana yang resmi diizinkan?
- Atau seharusnya kolom baja/atap/verkleidung **pindah ke layer lain** (mis.
  046 Stahlkonstruktionen), dan favorit yang sekarang perlu dikoreksi?

**[Q2] Layer `026 Träger`** memuat balok: Balken/Unterzug, Stahl, Dach (sparren),
Bedarfskörper (operator), Fenster (Fenstereinfassung), Haustechnik, Wand
(Fachwerk). Pertanyaan sama seperti Q1:
- Daftar klasifikasi resmi yang diizinkan di `026`? Atau balok non-struktur
  harus pindah layer?

**[Q3] Layer `050 Geländer`** memuat: Absturzsicherung (benar), tetapi juga
Treppe (balok tangga "Treppe (Beam)") dan Einrichtung. Pertanyaan:
- Apakah `050` khusus **Absturzsicherung** saja, sehingga balok tangga harus
  diklasifikasi/di-layer ulang? Atau `Treppe` juga sah di `050`?

### 🟡 Konflik Tunggal — kemungkinan salah favorit

**[Q4]** Empat kasus 1-favorit menyimpang dari mayoritas. Mohon konfirmasi
apakah ini **kesalahan pada favorit** (perlu dikoreksi) atau memang boleh:
- `036 Deckenbekleidung` → 1 favorit `Wand` (harusnya Bekleidung/Belag?)
- `060 Möbel Einbau` → 1 favorit `Möbel` (harusnya Einrichtung?)
- `331 Feuerlöschposten` → 1 favorit `Einrichtung` (harusnya Feuerlöscheinrichtung?)

### 🟡 Layer yang Namanya Tidak Terbaca

**[Q5]** Di XML favorit ada 21 elemen di layer `Index 154` (14× Stahl) dan
`Index 156` (7× Fenster/Tür) — **nama layer tidak ikut terekspor**. Mohon
arsitek sebutkan layer sebenarnya untuk kedua index ini, agar bisa dipetakan
ke tabel aturan (dugaan: 046 Stahlkonstruktionen dan 021 Fenster Zubehör?).

### 🟡 Elemen Bukaan (Fenster/Tür) — mewarisi layer host

**[Q6]** Ada 3 favorit **Pintu berklasifikasi `Einrichtung`**. Aturan sekarang
hanya mengizinkan Tür-Einrichtung di `015 Innenwände`. Pertanyaan:
- Pada dinding apa saja pintu boleh berklasifikasi `Einrichtung`? Hanya
  Innenwände, atau juga Aussenwände/Trennwände? (menentukan apakah akan ada
  false-FAIL)

**[Q7] Layer `002 Fixpunkt Kugel`**: 1 favorit tanpa klasifikasi `(ohne)`.
Apakah semua elemen di `002` **wajib** ELEMENTE, atau `(ohne)` dapat diterima
untuk objek tertentu (mis. Vermessungspunktanzeige)?

### 🟢 Layer Belum Terverifikasi (tidak ada contoh di favorit)

**[Q8]** Aturan berikut belum bisa diuji karena tidak ada favorit di layer/tipe
tersebut. Mohon konfirmasi aturannya benar:
- `029 Decken Grundriss` → Decke
- `054 Treppen Zubehör` → Treppe
- `080 Raum Innen` → Geschossfläche
- Override `042 Dächer Zubehör + Door → Tür`
- Override `060 Möbel Einbau + Door → Tür`

---

## 4. Cakupan yang Sengaja Dilewati (konfirmasi saja, bukan pertanyaan)

Flow melewati (SKIP) layer di luar tabel — **bukan** dianggap gagal. Favorit di
layer berikut tidak akan diaudit. Mohon konfirmasi ini memang diinginkan:

`065 Möbel Einrichtung` (Möbel), `290 Raster/Achsen` (Raster), `006 Grenzen`,
`038 Lichtschächte`, `500 Hilfskonstruktionen`, serta semua layer anotasi 2D
(`110 Bemassungen`, `200/201 Schnittlinien`, `205 Ansichtslinien`, `250`).
