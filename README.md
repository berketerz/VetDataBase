# VetDataBase

**Proje No 25 — Veteriner Kliniği Yönetim Sistemi** 

---

## Ekip Üyeleri

| Öğrenci No | Ad Soyad |
| :--- | :--- |
| **032490018** | Mehmet Berke Terzi |
| **032490036** | Oğuzhan Malkoç |
| **032490052** | Yağızhan Burak Yakar |
| **032490067** | Mehmet Oğuzhan Tanrıverdi |

---

## Proje Senaryosu

> Evcil hayvan sahiplerinin benzersiz bir kimlik numarası, adı, soyadı, adresi ve birden fazla telefon numarası vardır. Her hayvan sahibinin birden fazla evcil hayvanı olabilir. Evcil hayvanların benzersiz bir numarası, adı, türü, ırkı, cinsiyeti ve doğum tarihi bulunur. Veteriner hekimlerin benzersiz bir kimlik numarası, adı, soyadı, uzmanlık alanı ve telefon numarası vardır. Veteriner hekimler evcil hayvanları muayene eder. Her muayene için muayene tarihi, tanı ve notlar kaydedilir. Veteriner hekimler evcil hayvanlara ilaç reçete edebilir. Her ilacın benzersiz bir numarası, adı, türü ve birim fiyatı vardır. Reçete edilen her ilaç için reçete tarihi, doz ve miktar bilgisi kaydedilir. Evcil hayvanlara ayrıca aşı uygulanabilir. Her aşının benzersiz bir numarası, adı ve geçerlilik süresi vardır. Bir evcil hayvana uygulanan her aşı için aşılama tarihi kaydedilir.

---

## 1. Varlık-İlişki (E-R) Diyagramı

![Veteriner Kliniği E-R Diyagramı](docs/er-diagram.svg)

### Diyagram Özeti
* **Güçlü Varlıklar (5 Adet):** `sahip`, `hayvan`, `vet hekim`, `ilaç`, `aşı`
* **Çok Değerli Nitelik:** `sahip` varlığına ait `tel no` (Bir hayvan sahibinin birden fazla telefon numarası olabilir)
* **İlişkiler ve Kardinaliteler:**
  * `sahip olmak` (`sahip` 1 — N `hayvan`)
  * `muayene etmek` (`vet hekim` M — N `hayvan`):(`muayene tarihi`, `tanı`, `notlar`)
  * `aşı uygulamak` (`hayvan` N — M `aşı`):(`aşılama tarihi`)
  * `reçete yazmak` (`vet hekim` M — N `hayvan` — P `ilaç`):(`reçete tarihi`, `doz`, `miktar`)

---

## 2. İlişkisel Veritabanı Şeması

### 7 Adımlı Dönüşüm Algoritması
* **Adım 1:** Güçlü Varlık Tiplerinin Dönüştürülmesi
* **Adım 2:** Zayıf Varlık Tiplerinin Dönüştürülmesi
* **Adım 3:** İkili (1:1) İlişki Tiplerinin Dönüştürülmesi
* **Adım 4:** İkili (1:N) İlişki Tiplerinin Dönüştürülmesi
* **Adım 5:** İkili (M:N) İlişki Tiplerinin Dönüştürülmesi
* **Adım 6:** Çok Değerli Niteliklerin Dönüştürülmesi
* **Adım 7:** Üçlü (n-ary) İlişki Tiplerinin Dönüştürülmesi

### Oluşan İlişkisel Tablolar

1. **Sahip** (<ins>sahip\_ID</ins>, ad, soyad, adres) &nbsp;&nbsp;&nbsp;— *[Adım 1]*
2. **Sahip_Telefon** (<ins>sahip\_ID (FK), tel\_no</ins>) &nbsp;&nbsp;&nbsp;— *[Adım 6]*
3. **Hayvan** (<ins>hayvan\_ID</ins>, ad, tur, irk, cinsiyet, dogum\_tarihi, sahip\_ID (FK)) &nbsp;&nbsp;&nbsp;— *[Adım 1, Adım 4]*
4. **Veteriner_Hekim** (<ins>vet\_ID</ins>, ad, soyad, uzmanlik\_alani, tel\_no) &nbsp;&nbsp;&nbsp;— *[Adım 1]*
5. **Ilac** (<ins>ilac\_ID</ins>, ad, tur, fiyat) &nbsp;&nbsp;&nbsp;— *[Adım 1]*
6. **Asi** (<ins>asi\_ID</ins>, ad, gecerlilik\_suresi) &nbsp;&nbsp;&nbsp;— *[Adım 1]*
7. **Muayene** (<ins>hayvan\_ID (FK), vet\_ID (FK), muayene\_tarihi</ins>, tani, notlar) &nbsp;&nbsp;&nbsp;— *[Adım 5]*
8. **Asi_Uygulama** (<ins>hayvan\_ID (FK), asi\_ID (FK), asilama\_tarihi</ins>) &nbsp;&nbsp;&nbsp;— *[Adım 5]*
9. **Recete** (<ins>hayvan\_ID (FK), vet\_ID (FK), ilac\_ID (FK)</ins>, recete\_tarihi, doz, miktar) &nbsp;&nbsp;&nbsp;— *[Adım 7]*

---

## 3. Normalizasyon (1NF → 2NF → 3NF)

### 1NF (Birinci Normal Form)
* **Koşul:** Her hücre tek (atomik) değer içermeli, çok değerli nitelik bulunmamalıdır.
* **Geçiş (0NF → 1NF):** `Sahip` tablosundaki çok değerli `tel_no` niteliği ayrı bir tabloya ayrıştırılmıştır:
  * **Sahip** (<ins>sahip\_ID</ins>, ad, soyad, adres)
  * **Sahip_Telefon** (<ins>sahip\_ID (FK), tel\_no</ins>)

### 2NF (İkinci Normal Form)
* **Koşul:** 1NF sağlanmalı ve anahtar olmayan nitelikler bileşik anahtarın tamamına tam bağımlı olmalıdır *(Kısmi bağımlılık olmamalıdır)*.
* **Geçiş (1NF → 2NF):** Tekil anahtarlı tablolar (`Sahip`, `Hayvan`, `Veteriner_Hekim`, `Ilac`, `Asi`) doğrudan 2NF'dir. Bileşik anahtarlı `Muayene`, `Asi_Uygulama` ve `Recete` tablolarında ise ilaç adı/fiyatı gibi tek bir anahtar parçasına bağlı nitelikler ayrı tablolarda (`Ilac`, `Asi`) tutularak kısmi bağımlılık önlenmiştir:
  * **Ilac** (<ins>ilac\_ID</ins>, ad, tur, fiyat)
  * **Recete** (<ins>hayvan\_ID (FK), vet\_ID (FK), ilac\_ID (FK)</ins>, recete\_tarihi, doz, miktar)

### 3NF (Üçüncü Normal Form)
* **Koşul:** 2NF sağlanmalı ve anahtar olmayan hiçbir nitelik başka bir anahtar olmayan niteliğe bağımlı olmamalıdır *(Geçişli bağımlılık olmamalıdır)*.
* **Geçiş (2NF → 3NF):** `Hayvan` tablosunda sahibinin adı ve adresi gibi `sahip_ID` üzerinden geçişli bağımlılık (`hayvan_ID → sahip_ID → adres`) yaratacak alanlar tutulmamış, yalnızca `sahip_ID (FK)` referansı bırakılarak tüm tablolar 3NF'ye ulaştırılmıştır:
  * **Sahip** (<ins>sahip\_ID</ins>, ad, soyad, adres)
  * **Hayvan** (<ins>hayvan\_ID</ins>, ad, tur, irk, cinsiyet, dogum\_tarihi, sahip\_ID (FK))


