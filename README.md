# Deney Sonu Teslimatı

Sistem Programlama ve Veri Yapıları bakış açısıyla veri tabanlarındaki performansı öne çıkaran hususlar nelerdir?

Aşağıda kutucuk (checkbox) ile gösterilen maddelerden en az birini seçtiğiniz açık kaynak kodlu bir VT kaynak kodları üzerinde göstererek açıklayınız. Açıklama bölümüne kısaca metninizi yazıp, kod üzerinde gösterim videonuzun linkini en altta belirtilen kutucuğa yerleştiriniz.

- [X]  Seçtiğiniz konu/konuları bu şekilde işaretleyiniz. **!**
    
---

# 1. Sistem Perspektifi (Operating System, Disk, Input/Output)

### Disk Erişimi

- [ ]  **Blok bazlı disk erişimi** → block_id + offset
- [ ]  Rastgele erişim

### VT için Page (Sayfa) Anlamı

- [X]  VT hangisini kullanır? **Satır/ Sayfa** okuması

---

### Buffer Pool

- [X]  Veritabanları, Sık kullanılan sayfaları bellekte (RAM) kopyalar mı (caching) ?

- [ ]  LRU / CLOCK gibi algoritmaları
- [ ]  Diske yapılan I/O nasıl minimize ederler?

# 2. Veri Yapıları Perspektifi

- [X]  B+ Tree Veri Yapıları VT' lerde nasıl kullanılır?
- [ ]  VT' lerde hangi veri yapıları hangi amaçlarla kullanılır?
- [ ]  Clustered vs Non-Clustered Index Kavramı
- [ ]  InnoDB satırı diskte nasıl durur?
- [ ]  LSM-tree (LevelDB, RocksDB) farkı
- [ ]  PostgreSQL heap + index ayrımı

DB diske yazarken:

- [ ]  WAL (Write Ahead Log) İlkesi
- [ ]  Log disk (fsync vs write) sistem çağrıları farkı

---

# Özet Tablo

| Kavram      | Bellek          | Disk / DB      |
| ----------- | --------------- | -------------- |
| Adresleme   | Pointer         | Page + Offset  |
| Hız         | O(1)            | Page IO        |
| PK          | Yok             | Index anahtarı |
| Veri yapısı | Array / Pointer | B+Tree         |
| Cache       | CPU cache       | Buffer Pool    |

---

# Video [Linki](https://www.youtube.com/watch?v=Nw1OvCtKPII&t=2635s) 
Ekran kaydı. 2-3 dk. açık kaynak V.T. kodu üzerinde konunun gösterimi. Video kendini tanıtma ile başlamalıdır (Numara, İsim, Soyisim, Teknik İlgi Alanları). 

---

# Açıklama (Ort. 600 kelime)

Bu çalışma kapsamında, PostgreSQL veritabanı yönetim sisteminin mimarisi, Sistem Programlama ve Veri Yapıları dersi perspektifiyle incelenmiş; özellikle disk erişimi, bellek yönetimi ve indeksleme mekanizmaları doğrudan C kaynak kodları üzerinden analiz edilmiştir. Çalışmanın temel amacı, teorik olarak bilinen sayfa yapısı, önbellekleme (caching) ve ağaç tabanlı arama kavramlarının kaynak kod seviyesindeki pratik karşılıklarını tespit etmektir.
İlk olarak, sistemin disk üzerindeki veriye erişim yöntemi ele alındığında, veri okuma işlemlerinin satır (row) tabanlı değil, blok (page) tabanlı gerçekleştirildiği görülmüştür. Kaynak kod içerisinde yer alan pg_config_manual.h dosyasında BLCKSZ makrosunun 8192 byte (8KB) olarak tanımlandığı ve sistemin en küçük okuma birimi olarak bu boyutu kullandığı tespit edilmiştir. Bu mimari tercihin işleyişi, veri okuma süreçlerini başlatan ReadBuffer_common fonksiyonunun incelenmesiyle somutlaştırılmıştır. İlgili fonksiyonun parametre yapısı analiz edildiğinde, veriye erişim için mantıksal bir RowID yerine, fiziksel bir BlockNumber değerinin talep edildiği belirlenmiştir. Bu durum, veritabanının diskten veri çekerken tekil satırları değil, o satırları içeren bütünleşik sayfaları belleğe taşıdığını kanıtlamaktadır.
Analizin ikinci aşamasında, veritabanı performansının en kritik bileşeni olan tampon bellek (Buffer Pool) mekanizmaları src/backend/storage/buffer/bufmgr.c dosyası üzerinden izlenmiştir. Disk I/O maliyetini minimize etmek amacıyla kurgulanan bu yapının, ReadBuffer_common fonksiyonu içerisinde başlayan bir karar mekanizmasıyla yönetildiği gözlemlenmiştir. Kod akışı takip edildiğinde, bu fonksiyonun StartReadBuffersImpl isimli alt fonksiyonu çağırdığı ve PinBufferForBlock fonksiyonu aracılığıyla talep edilen bloğun o an RAM üzerindeki "Shared Buffers" alanında yüklü olup olmadığını sorguladığı anlaşılmıştır. Elde edilen en belirleyici teknik bulgu, StartReadBuffersImpl bloğundaki if (found) koşul ifadesidir. Eğer found değişkeni true dönerse, sistemin maliyetli WaitReadBuffers çağrısını ve disk okuma işlemlerini tamamen atlayarak, veriyi doğrudan RAM üzerinden sunduğu ve pgBufferUsage.shared_blks_hit sayacını artırdığı gözlemlenmiştir.
Bu süreçlerin tamamlayıcısı olarak, hedeflenen verinin hangi fiziksel blokta yer aldığını tespit eden B+ Tree indeks yapısı, src/backend/access/nbtree/nbtsearch.c dosyası üzerinden analiz edilmiştir. Arama algoritmasının merkezi olan bt_search fonksiyonu incelendiğinde, işlemin bt_getroot çağrısı ile kök düğümden başladığı ve bir sonsuz döngü (for(;;)) içerisinde katman katman aşağı inildiği görülmüştür. Bu iniş sırasında sistem, elindeki arama anahtarını (key) kullanarak yön tayini yapmakta ve P_ISLEAF makrosu aracılığıyla ulaşılan sayfanın bir yaprak (leaf) düğüm olup olmadığını denetlemektedir. Yaprak düğüme ulaşılmasıyla birlikte döngü sonlanmakta ve aranan verinin fiziksel adresini barındıran hedef blok (buffer) elde edilmektedir.

## VT Üzerinde Gösterilen Kaynak Kodları

Açıklama [Linki](https://...) \
Açıklama [Linki](https://...) \
Açıklama [Linki](https://...) \
... \
...
