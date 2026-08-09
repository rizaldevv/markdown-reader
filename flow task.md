# Flow Task — Cek Layer ↔ Classification

Dokumen ini merangkum **cara kerja**, **aturan (rule base)**, dan **parameter**
dari task audit `check-layer-classification` (judul UI: **"Cek Layer ↔ Classification"**).

Sumber kode:
- [CheckLayerClassificationTask.cpp](../../Addons/SN_Architect/Src/Features/MultiTaskFeature/CheckLayerClassification/CheckLayerClassificationTask.cpp)
- [CheckLayerClassificationTask.hpp](../../Addons/SN_Architect/Src/Features/MultiTaskFeature/CheckLayerClassification/CheckLayerClassificationTask.hpp)
- [ThreeDTypePopulation.cpp](../../Addons/SN_Architect/Src/Features/MultiTaskFeature/Audit/ThreeDTypePopulation.cpp)

---

## 1. Ringkasan Tujuan

Task ini memastikan **setiap elemen 3D yang berada di layer "standar"** memiliki
nilai **"Archicad Klassifizierung"** yang **sesuai** dengan klasifikasi yang
diwajibkan oleh layer tersebut.

> Prinsip inti: **Layer menentukan klasifikasi yang seharusnya.** Jika elemen
> ada di layer `010 Aussenwände`, maka klasifikasinya harus `Wand`.
> Ada pengecualian untuk elemen tertentu (mis. Window/Door di dalam dinding).

Kebijakan cakupan (dikonfirmasi dgn Pak Rizal, 2026-06-19):
**Layer di luar tabel = DILEWATI (skip)**, bukan Warn/Fail. Layer non-standar
adalah domain audit lain (`CheckNonStandardLayers`, `CheckLayerCombinations`).

---

## 2. Alur Kerja (Flow)

```text
1. Run(context)
   └─ Cari classification system "Archicad Klassifizierung"
        ├─ Tidak ada  → FailNoNav (1 baris) → selesai
        └─ Ada        → lanjut
2. Ambil populasi "3D Types" (context.GetThreeDTypeElements())
3. Untuk setiap elemen (yield ke UI tiap 50 elemen):
   a. Baca header (ACAPI_Element_GetHeader) → gagal? skip elemen
   b. Resolve nama layer (via ProjectNameCache, dicache per index)
   c. Cari klasifikasi yang diizinkan: AcceptableClassificationsFor(layer, typeID)
        ├─ kosong  → SKIP  (layer di luar tabel)
        └─ ada     → lanjut evaluasi
   d. Baca nilai klasifikasi aktual elemen (ReadClassification)
   e. Bandingkan: aktual == salah satu yang diizinkan (exact string match)?
        ├─ ya  → Pass  (successCount++)
        └─ tdk → FailNoNav (failedCount++)
   f. Bangun baris hasil + detail (collapsible)
4. Log total/success/failed/skipped → return AuditTaskResult
```

---

## 3. Rule Base

### 3.1 Aturan Dasar (Base Layer Rule)

Pemetaan **1 layer → 1 klasifikasi wajib**. Layer yang **tidak** ada di tabel
ini akan dilewati.

| # | Layer | Klasifikasi Wajib |
|---|-------|-------------------|
| 1 | 002 Fixpunkt Kugel | ELEMENTE |
| 2 | 004 Bezugspunkt | Tiefbau Element |
| 3 | 010 Aussenwände | Wand |
| 4 | 011 Fassadensystem | Wand |
| 5 | 015 Innenwände | Wand |
| 6 | 016 Trennwände | Wand |
| 7 | 017 Wandbekleidung | Bekleidung / Belag |
| 8 | 020 Stützen | Stütze / Pilaster |
| 9 | 021 Fenster Zubehör | Fenster |
| 10 | 022 Tür Zubehör | Tür |
| 11 | 025 Unterzüge | Balken / Unterzug |
| 12 | 026 Träger | Balken / Unterzug |
| 13 | 029 Decken Grundriss | Decke |
| 14 | 030 Bodenaufbauten | Decke |
| 15 | 035 Decken | Decke |
| 16 | 036 Deckenbekleidung | Bekleidung / Belag |
| 17 | 040 Dächer | Dach |
| 18 | 041 Dächer Grundrisslinie | Dach |
| 19 | 042 Dächer Zubehör | Dach |
| 20 | 045 Dachkonstruktionen | Dach |
| 21 | 046 Stahlkonstruktionen | Stahl |
| 22 | 050 Geländer | Absturzsicherung |
| 23 | 053 Treppenuntersicht | Treppe |
| 24 | 054 Treppen Zubehör | Treppe |
| 25 | 055 Treppen | Treppe |
| 26 | 060 Möbel Einbau | Einrichtung |
| 27 | 080 Raum Innen | Geschossfläche |
| 28 | 090 KNGW Wand | ELEMENTE |
| 29 | 091 KNGW Decke | ELEMENTE |
| 30 | 300 Elektrokomponenten | Haustechnische Komponente (allgemein) |
| 31 | 305 Heizungskomponenten | Haustechnische Komponente (allgemein) |
| 32 | 310 Lüftungskomponenten | Haustechnische Komponente (allgemein) |
| 33 | 315 Klimakomponenten | Haustechnische Komponente (allgemein) |
| 34 | 320 Sanitärkomponenten | Haustechnische Komponente (allgemein) |
| 35 | 331 Feuerlöschposten | Feuerlöscheinrichtung |
| 36 | 360 Rohrleitungen | Haustechnische Komponente (allgemein) |
| 37 | 361 Kanäle | Haustechnische Komponente (allgemein) |
| 38 | 362 Trasse | Haustechnische Komponente (allgemein) |
| 39 | 400 Terrain gewachsen | Tiefbau Element |
| 40 | 410 Umgebung | Tiefbau Element |
| 41 | 411 Bäume | Tiefbau Element |
| 42 | 412 Bestockte Fläche | Tiefbau Element |
| 43 | 450 Kanalisation Schacht | Haustechnische Komponente (allgemein) |
| 44 | 700 Operatoren | Bedarfskörper |

> Nama klasifikasi bergaya `A / B` (mis. "Stütze / Pilaster") adalah **satu
> nilai klasifikasi tunggal** persis seperti di Archicad — bukan dua pilihan.

### 3.2 Aturan Override per (Layer + Tipe Elemen)

Override **menang** atas aturan dasar. Alasannya: sebuah Window di dalam
`010 Aussenwände` diklasifikasi oleh **jendela itu sendiri** (`Fenster`), bukan
oleh dinding tempatnya (`Wand`). Sama untuk Door. Bila (layer, tipe) cocok di
tabel ini, daftar `allowed` di bawah dipakai dan aturan dasar **tidak** dilihat.

| Layer | Tipe Elemen | Klasifikasi yang Diizinkan |
|-------|-------------|-----------------------------|
| 010 Aussenwände | Window | Fenster **atau** Nische |
| 015 Innenwände | Window | Fenster **atau** Nische |
| 017 Wandbekleidung | Window | Fenster |
| 010 Aussenwände | Door | Tür |
| 015 Innenwände | Door | Tür **atau** Einrichtung |
| 016 Trennwände | Door | Tür |
| 042 Dächer Zubehör | Object | Dach **atau** Dachfenster |
| 042 Dächer Zubehör | Door | Tür |
| 060 Möbel Einbau | Door | Tür |
| 040 Dächer | Skylight | Dachfenster |
| 041 Dächer Grundrisslinie | Skylight | Dachfenster |
| 045 Dachkonstruktionen | Skylight | Dachfenster |

> Skylight (alat *Dachfenster*) *host-inherited* dari atap → memakai layer atap
> (040/041/045), tetapi diklasifikasi oleh skylight-nya sendiri (`Dachfenster`),
> bukan aturan dasar atap (`Dach`).

> Override boleh menghasilkan **beberapa** nilai diizinkan; elemen dianggap
> Pass bila aktualnya **cocok dengan salah satu**.

### 3.3 Urutan Resolusi (`AcceptableClassificationsFor`)

```text
1. Cek tabel Override → jika (layer, typeID) cocok → pakai daftar allowed override
2. Jika tidak → cek tabel Base → jika layer cocok → pakai 1 klasifikasi wajib
3. Jika tidak ada → kembalikan kosong → elemen DISKIP
```

---

## 4. Logika Verdict (Pass / Fail / Skip)

| Verdict | Kondisi |
|---------|---------|
| **SKIP** | Layer tidak ada di tabel dasar maupun override (`allowed` kosong). Tidak menghasilkan baris. |
| **PASS** | Klasifikasi terbaca (`hasValue == true`) **dan** nilai aktualnya **sama persis** dengan salah satu nilai `allowed`. |
| **FAIL** | Semua kasus lain: elemen tak berklasifikasi `(Unclassified)`, atau nilai aktual tidak cocok dengan `allowed`. |

Catatan pencocokan:
- **Exact string match** — perbandingan string penuh, bukan hierarki.
  Pencocokan turunan (descendant node) belum diterapkan dan bisa ditambah nanti.
- Elemen tanpa klasifikasi ditandai label `(Unclassified)` dan otomatis Fail.

Kegagalan level task (bukan per elemen): jika sistem klasifikasi
**"Archicad Klassifizierung"** tidak ditemukan di project, task mengembalikan
satu baris `FailNoNav` dan berhenti.

---

## 5. Parameter & Konstanta

| Parameter | Nilai / Sumber | Keterangan |
|-----------|----------------|------------|
| `Id()` | `check-layer-classification` | ID task internal |
| `Title()` | `Cek Layer ↔ Classification` | Judul di palette |
| `TargetSystemPrefix` | `L"Archicad Klassifizierung"` | Match nama sistem via `BeginsWith` (toleran terhadap sufiks `- v2.0`) |
| `UnclassifiedLabel` | `(Unclassified)` | Label elemen tanpa klasifikasi |
| `UiYieldInterval` | `50` | Yield ke UI tiap 50 elemen (jaga responsivitas) |
| `RequiresElementContext()` | `false` | Tidak butuh prefetch daftar penuh project |
| `RequiresThreeDTypePopulation()` | `true` | Pakai populasi "3D Types" bersama (tidak re-scan model) |

### 5.1 Populasi Elemen yang Diaudit ("3D Types")

Task hanya memindai elemen dari `context.GetThreeDTypeElements()` — set
"3D Types" yang sama dengan **Find & Select** di Archicad, lalu difilter
**"terlihat di view 3D saat ini"**.

Tipe yang termasuk (`k3DTypes`):
Wall, Column, Beam, Window, Door, Skylight, Object, Lamp, Slab, Roof, Mesh,
Shell, CurtainWall, Zone, Morph, Stair, Railing (parent), ExternalElem
(MEP / Structural Analytical).

Filter visibilitas (`kVisibilityFilters`):

| Flag | Arti |
|------|------|
| `APIFilt_OnVisLayer` | Layer dalam keadaan ON |
| `APIFilt_IsVisibleByRenovation` | Lolos renovation filter |
| `APIFilt_IsInStructureDisplay` | Lolos partial structure display |

> Design Option visibility (AC29) **sengaja diabaikan** agar build AC26–28
> tetap bersih. Karena filter ini dinamis, populasi **ikut berubah** mengikuti
> state view 3D saat scan dijalankan.

---

## 6. Output per Elemen (Baris Hasil)

Setiap baris (Pass/Fail) membawa:

- **navTarget** = GUID elemen; **preferredView** = `ThreeD`.
  Klik baris membuka dialog detail collapsible (bukan auto-zoom); tombol
  "Move to Element" di dialog yang memakai navTarget.
- **detailResultLine**: `Seharusnya <expected> | Aktual <actual>`
- **detailBuildingMaterial**: nama layer
- **detailRows**: Classification (expected vs actual), Element Type, Story, Layer
- **Detail teks**: Element Type / Layer / Expected / Actual / Story

Di akhir, task menulis log:
`total=… success=… failed=… skipped=…`

---

## 7. Ringkasan Singkat

| Aspek | Nilai |
|-------|-------|
| Yang dicek | Nilai "Archicad Klassifizierung" elemen 3D |
| Basis aturan | Nama layer (44 aturan dasar + 9 override) |
| Cakupan elemen | 18 tipe "3D Types" yang terlihat di view 3D |
| Layer di luar tabel | Diskip (bukan gagal) |
| Metode cocok | Exact string match terhadap daftar allowed |
| Gagal task | Sistem klasifikasi tidak ditemukan |
