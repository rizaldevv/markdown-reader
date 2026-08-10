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

| # | Layer | Klasifikasi wajib |
|--:|-------|-------------------|
| 1 | `002 Fixpunkt Kugel` | ELEMENTE |
| 2 | `004 Bezugspunkt` | Tiefbau Element |
| 3 | `010 Aussenwände` | Wand |
| 4 | `011 Fassadensystem` | Wand |
| 5 | `015 Innenwände` | Wand |
| 6 | `016 Trennwände` | Wand |
| 7 | `017 Wandbekleidung` | Bekleidung / Belag |
| 8 | `020 Stützen` | Stütze / Pilaster |
| 9 | `021 Fenster Zubehör` | Fenster |
| 10 | `022 Tür Zubehör` | Tür |
| 11 | `025 Unterzüge` | Balken / Unterzug |
| 12 | `026 Träger` | Balken / Unterzug |
| 13 | `029 Decken Grundriss` | Decke |
| 14 | `030 Bodenaufbauten` | Decke |
| 15 | `035 Decken` | Decke |
| 16 | `036 Deckenbekleidung` | Bekleidung / Belag |
| 17 | `040 Dächer` | Dach |
| 18 | `041 Dächer Grundrisslinie` | Dach |
| 19 | `042 Dächer Zubehör` | Dach |
| 20 | `045 Dachkonstruktionen` | Dach |
| 21 | `046 Stahlkonstruktionen` | Stahl |
| 22 | `050 Geländer` | Absturzsicherung |
| 23 | `053 Treppenuntersicht` | Treppe |
| 24 | `054 Treppen Zubehör` | Treppe |
| 25 | `055 Treppen` | Treppe |
| 26 | `060 Möbel Einbau` | Einrichtung |
| 27 | `080 Raum Innen` | Geschossfläche |
| 28 | `090 KNGW Wand` | ELEMENTE |
| 29 | `091 KNGW Decke` | ELEMENTE |
| 30 | `300 Elektrokomponenten` | Haustechnische Komponente (allgemein) |
| 31 | `305 Heizungskomponenten` | Haustechnische Komponente (allgemein) |
| 32 | `310 Lüftungskomponenten` | Haustechnische Komponente (allgemein) |
| 33 | `315 Klimakomponenten` | Haustechnische Komponente (allgemein) |
| 34 | `320 Sanitärkomponenten` | Haustechnische Komponente (allgemein) |
| 35 | `331 Feuerlöschposten` | Feuerlöscheinrichtung |
| 36 | `360 Rohrleitungen` | Haustechnische Komponente (allgemein) |
| 37 | `361 Kanäle` | Haustechnische Komponente (allgemein) |
| 38 | `362 Trasse` | Haustechnische Komponente (allgemein) |
| 39 | `400 Terrain gewachsen` | Tiefbau Element |
| 40 | `410 Umgebung` | Tiefbau Element |
| 41 | `411 Bäume` | Tiefbau Element |
| 42 | `412 Bestockte Fläche` | Tiefbau Element |
| 43 | `450 Kanalisation Schacht` | Haustechnische Komponente (allgemein) |
| 44 | `700 Operatoren` | Bedarfskörper |

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

## Kesimpulan

Mesin aturan bertumpu pada **44 base rule** (satu layer → satu klasifikasi) yang
diperbaiki di sembilan titik oleh **tabel override** `(layer, element type)`,
sementara elemen host-child (Fenster/Tür/Dachfenster) sengaja tidak punya layer
sendiri sehingga dinilai lewat layer host via override yang sama — dan seluruh
struktur ini terbukti konsisten dengan data 504 favorit kecuali pada layer
konstruksi bersama `020`/`026`/`050` (yang menampung banyak maksud dalam satu
layer) serta satu titik risiko pintu `Einrichtung`, yang keduanya masih menunggu
keputusan arsitek sebelum aturan tambahan dikunci.
