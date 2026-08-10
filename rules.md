# Referensi Aturan — Check Layer Classification

Rincian aturan yang aktif di mesin `check-layer-classification`, dipisah per
tabel. Sumber kode:
`Addons/SN_Architect/Src/Features/MultiTaskFeature/CheckLayerClassification/`
— `LayerClassificationRules.cpp` (base) dan `CheckLayerClassificationTask.cpp`
(override). Data pembanding: [FAVORITEN_AUDIT_STERN.md](FAVORITEN_AUDIT_STERN.md).

---

## Tabel 1 — 44 Layer Base Rule

Satu layer → satu klasifikasi. Nama layer dicocokkan **byte-exact** (diakritik
German penting). Elemen 3D-visible pada layer ini wajib membawa klasifikasi yang
tertera. Layer di luar tabel ini → **SKIP** (ditangani audit lain).

Kolom **Fav** = jumlah favorit pada layer itu yang klasifikasinya **cocok** base
rule (bukti validasi). **Status**: ✅ tervalidasi data · ⚠ tervalidasi sebagian
(ada konflik/outlier, lihat CMP §3) · ⚪ belum teruji (tak ada favorit).

| # | Layer | Klasifikasi wajib | Fav | Status |
|--:|-------|-------------------|----:|:------:|
| 1 | `002 Fixpunkt Kugel` | ELEMENTE | 1 | ⚠ |
| 2 | `004 Bezugspunkt` | Tiefbau Element | 4 | ✅ |
| 3 | `010 Aussenwände` | Wand | 6 | ✅ |
| 4 | `011 Fassadensystem` | Wand | 8 | ✅ |
| 5 | `015 Innenwände` | Wand | 4 | ✅ |
| 6 | `016 Trennwände` | Wand | 3 | ✅ |
| 7 | `017 Wandbekleidung` | Bekleidung / Belag | 5 | ✅ |
| 8 | `020 Stützen` | Stütze / Pilaster | 4 | ⚠ |
| 9 | `021 Fenster Zubehör` | Fenster | 14 | ✅ |
| 10 | `022 Tür Zubehör` | Tür | 10 | ✅ |
| 11 | `025 Unterzüge` | Balken / Unterzug | 4 | ✅ |
| 12 | `026 Träger` | Balken / Unterzug | 6 | ⚠ |
| 13 | `029 Decken Grundriss` | Decke | 0 | ⚪ |
| 14 | `030 Bodenaufbauten` | Decke | 4 | ✅ |
| 15 | `035 Decken` | Decke | 10 | ✅ |
| 16 | `036 Deckenbekleidung` | Bekleidung / Belag | 13 | ⚠ |
| 17 | `040 Dächer` | Dach | 3 | ✅ |
| 18 | `041 Dächer Grundrisslinie` | Dach | 1 | ✅ |
| 19 | `042 Dächer Zubehör` | Dach | 15 | ✅ |
| 20 | `045 Dachkonstruktionen` | Dach | 3 | ✅ |
| 21 | `046 Stahlkonstruktionen` | Stahl | 4 | ✅ |
| 22 | `050 Geländer` | Absturzsicherung | 8 | ⚠ |
| 23 | `053 Treppenuntersicht` | Treppe | 2 | ✅ |
| 24 | `054 Treppen Zubehör` | Treppe | 0 | ⚪ |
| 25 | `055 Treppen` | Treppe | 25 | ✅ |
| 26 | `060 Möbel Einbau` | Einrichtung | 116 | ⚠ |
| 27 | `080 Raum Innen` | Geschossfläche | 0 | ⚪ |
| 28 | `090 KNGW Wand` | ELEMENTE | 1 | ✅ |
| 29 | `091 KNGW Decke` | ELEMENTE | 4 | ✅ |
| 30 | `300 Elektrokomponenten` | Haustechnische Komponente (allgemein) | 9 | ✅ |
| 31 | `305 Heizungskomponenten` | Haustechnische Komponente (allgemein) | 9 | ✅ |
| 32 | `310 Lüftungskomponenten` | Haustechnische Komponente (allgemein) | 2 | ✅ |
| 33 | `315 Klimakomponenten` | Haustechnische Komponente (allgemein) | 4 | ✅ |
| 34 | `320 Sanitärkomponenten` | Haustechnische Komponente (allgemein) | 2 | ✅ |
| 35 | `331 Feuerlöschposten` | Feuerlöscheinrichtung | 4 | ⚠ |
| 36 | `360 Rohrleitungen` | Haustechnische Komponente (allgemein) | 3 | ✅ |
| 37 | `361 Kanäle` | Haustechnische Komponente (allgemein) | 5 | ✅ |
| 38 | `362 Trasse` | Haustechnische Komponente (allgemein) | 3 | ✅ |
| 39 | `400 Terrain gewachsen` | Tiefbau Element | 1 | ✅ |
| 40 | `410 Umgebung` | Tiefbau Element | 7 | ✅ |
| 41 | `411 Bäume` | Tiefbau Element | 2 | ✅ |
| 42 | `412 Bestockte Fläche` | Tiefbau Element | 4 | ✅ |
| 43 | `450 Kanalisation Schacht` | Haustechnische Komponente (allgemein) | 5 | ✅ |
| 44 | `700 Operatoren` | Bedarfskörper | 6 | ✅ |

**Rekap:** ✅ tervalidasi bersih **35** · ⚠ ada konflik/outlier **6**
(`002`, `020`, `026`, `036`, `050`, `060`) · ⚪ belum teruji **3**
(`029 Decken Grundriss`, `054 Treppen Zubehör`, `080 Raum Innen`).

> Catatan: `046 Stahlkonstruktionen` menerima tambahan **21** favorit lewat
> resolusi `Index 154` (lihat CMP §4) — tidak dihitung di kolom Fav karena bukan
> favorit yang menamai layer ini secara langsung.

---

## Tabel 2 — Favoriten tanpa layer (host-child / openings)

Favorit Fenster / Tür / Dachfenster **tidak punya layer sendiri** — mereka
mewarisi layer elemen host (dinding/atap). Layer efektif saat audit = layer
host, jadi mereka **tidak pernah** dievaluasi lewat base rule layer sendiri,
melainkan lewat tabel override host-type (Tabel 3).

| Werkzeug | Klasifikasi (favorit) | n | Layer efektif | Ditangani |
|----------|-----------------------|--:|---------------|-----------|
| Fenster (Window) | Fenster | 7 | host wall (010/015) | override → Fenster ✓ |
| Fenster (Window) | Nische | 4 | host wall (010/015) | override → Fenster / Nische ✓ |
| Tür (Door) | Tür | 16 | host wall (010/015/016) | override → Tür ✓ |
| Tür (Door) | Einrichtung | 3 | host wall | ⚠ hanya sah bila host = `015 Innenwände` |
| Dachfenster | Dachfenster | 2 | host roof (042) | override → Dach / Dachfenster ✓ |

> ⚠ **Titik risiko:** 3 pintu berklasifikasi `Einrichtung` hanya lolos pada host
> `015 Innenwände`. Bila terpasang di `010`/`016` → false-FAIL. Perlu keputusan
> apakah override `Einrichtung` diperluas ke host lain.

---

## Tabel 3 — Override `(Layer, Element Type)` → Klasifikasi

Bila pasangan `(layer, typeID)` cocok di sini, daftar `allowed` **menang** atas
base rule layer (Tabel 1). Dikonfirmasi bersama Pak Rizal 2026-06-19. Semua
sudah **tervalidasi** oleh data favoriten.

| # | Layer | Element Type | Klasifikasi diizinkan |
|--:|-------|--------------|-----------------------|
| 1 | `010 Aussenwände` | Window | Fenster, Nische |
| 2 | `015 Innenwände` | Window | Fenster, Nische |
| 3 | `017 Wandbekleidung` | Window | Fenster |
| 4 | `010 Aussenwände` | Door | Tür |
| 5 | `015 Innenwände` | Door | Tür, Einrichtung |
| 6 | `016 Trennwände` | Door | Tür |
| 7 | `042 Dächer Zubehör` | Object | Dach, Dachfenster |
| 8 | `042 Dächer Zubehör` | Door | Tür |
| 9 | `060 Möbel Einbau` | Door | Tür |

---

## Tabel 4 — Override name-token `(Layer, Element Type, Nama library-part)`

Dimensi ketiga: override yang hanya berlaku bila **nama library-part**
(`docu_UName`, yang tampil di Info Box) mengandung token tertentu. Ini membuat
satu tool melayani dua maksud di layer yang sama — pintu lemari built-in yang
dimodel dengan tool Door dikenali dari nama `Einbauschrank`. Dikonfirmasi
2026-08-10. Cocok memakai **substring** (mis. "Einbauschrank 25" tetap kena).

| # | Layer | Element Type | Token nama | Klasifikasi diizinkan |
|--:|-------|--------------|-----------|-----------------------|
| 1 | `010 Aussenwände` | Door | `Einbauschrank` | Einrichtung |
| 2 | `015 Innenwände` | Door | `Einbauschrank` | Einrichtung |
| 3 | `016 Trennwände` | Door | `Einbauschrank` | Einrichtung |
| 4 | `060 Möbel Einbau` | Door | `Einbauschrank` | Einrichtung |

Urutan resolusi: **token cocok → menang**; kalau tak ada token yang cocok →
jatuh ke override tokenless (Tabel 3); lalu base rule (Tabel 1). Jadi pintu
biasa (tanpa "Einbauschrank" di namanya) tetap **Tür**, tak terpengaruh. Nama
library-part dibaca malas — hanya saat ada token override untuk `(layer,
typeID)` — jadi mayoritas elemen tak kena biaya baca ekstra.

---

## Autofix — "Standarisasi Klasifikasi"

Tombol di palette Multi-Task Audit (`LayerClassificationFixer::FixAll`) menyetel
klasifikasi tiap elemen ke nilai *Expected*-nya, terbungkus satu undo:

- Hanya layer dengan **satu** klasifikasi wajib yang diubah (mis. `700` →
  Bedarfskörper, `021` → Fenster, pintu biasa → Tür).
- Layer bernilai ganda (Window→{Fenster,Nische}, Door 015→{Tür,Einrichtung})
  **dilewati** karena ambigu; layer di luar tabel dilewati.
- Resolusi memakai jalur yang sama dengan audit — termasuk name-token, jadi
  pintu `Einbauschrank` distandarkan ke Einrichtung, bukan Tür.

---

## Kesimpulan

Mesin aturan bertumpu pada **44 base rule** (satu layer → satu klasifikasi) yang
diperbaiki oleh **tabel override** `(layer, element type)` dan kini juga oleh
override **name-token** `(layer, element type, nama library-part)` — sehingga
pintu lemari `Einbauschrank` dikenali sebagai Einrichtung sementara pintu biasa
tetap Tür. Elemen host-child (Fenster/Tür/Dachfenster) sengaja tak punya layer
sendiri sehingga dinilai lewat layer host via override yang sama, dan tombol
**Standarisasi Klasifikasi** dapat menulis balik nilai *Expected* untuk layer
bernilai-tunggal. Seluruh struktur ini konsisten dengan data 504 favorit kecuali
pada layer konstruksi bersama `020`/`026`/`050` (yang menampung banyak maksud
dalam satu layer) serta satu titik risiko pintu `Einrichtung` di host selain
`015`, yang keduanya masih menunggu keputusan arsitek sebelum aturan tambahan
dikunci.
