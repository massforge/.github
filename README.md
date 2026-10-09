# EGEMEN KAYNAK KOD VE CI/CD ALTYAPISI MANİFESTOSU

## Görev Kritik ve Hayat Kritik Sistemler İçin Ulusal Güvence Belgesi

### GİRİŞ VE VİZYON

Bizler, dijital dünyanın en kritik cephelerinde; kamu kurumlarında, askeri harekât ağlarında, hastanelerin yaşam destek ünitelerinde ve ulusal güvenliğin kalbinde atan yazılımların, yabancı egemenliğindeki merkezi bulut yapılarına veya kırılgan sistemlere emanet edilemeyeceğini ilan ediyoruz. Bugün, küresel teknoloji tekellerinin sunduğu GitHub ve GitLab gibi platformlara, aynı zamanda mevcut yetersiz ve dağınık Forgejo örneklerine askeri standartlarda meydan okuyan bir dijital kale inşa ediyoruz. Amacımız, **7/24/365 kesintisiz**, durmaksızın çalışacak, siber savaş senaryolarında dahi ayakta kalabilen, yüksek girdi/çıktı (I/O) kapasitesine sahip yerli ve egemen bir kod barındırma ve otomasyon ekosistemini dünyaya sunmaktır.

Bu vizyonun arkasındaki sarsılmaz ilkeler, operasyonel gereksinimler ve teknik şartlar şunlardır:

#### 1. Sıfır Hata ve Hayat Kritik Dayanıklılık (Zero-Fault Tolerance)

* **İnsan Hayatı Temel Kriterdir:** Geliştirdiğimiz platformda yaşanacak bir saniyelik bir kesinti, bir hastanın ameliyat masasında, bir uçağın havada veya bir savunma sisteminin radarında zafiyet yaratabilir; bu nedenle sistem mimarimiz sıfır hata payı ile tasarlanmıştır.
* **Donanımsal Sınırları Zorlayan I/O Gücü:** Binlerce eşzamanlı derleme (build) ve mikro saniyeler seviyesinde Git işlemi için altyapımız, kurumsal NVMe RAID-10 depolama mimarisiyle donatılmıştır.
* **Tier 4 Altyapı Standartları:** Sistem, her bileşenin (enerji, iklimlendirme, ağ) 2N+1 oranında yedekli olduğu, kesintisiz güç kaynakları ve jeneratörlerle korunan veri merkezlerinde konuşlandırılır.

#### 2. Mutlak Egemenlik ve Kapalı Ağ (Air-Gapped) Uyumluluğu

* **Küresel Ağlardan Bağımsızlık:** Altyapımız, dış dünyaya ve açık internete hiçbir bağımlılığı olmadan, tamamen izole (air-gapped) askeri ve kamu ağlarında yerel aynalar (mirror) üzerinden kusursuzca çalışır.
* **Açık Kaynak Kod Güvencesi:** Platformun kalbi, arka kapılardan (backdoor) arındırılmış, topluluk tarafından denetlenen ve üzerinde tam kontrol sahibi olduğumuz Forgejo ve Woodpecker CI/CD mimarisine emanettir.
* **Veri Sınır Namustur:** Kamuya ve stratejik kurumlara ait tek bir satır kaynak kod, tek bir konfigürasyon dosyası veya meta-veri asla ulusal sınırların ya da kurum içi güvenli ağların dışına çıkamaz.

#### 3. Aktif-Aktif Geo-Yedeklilik ve Kesintisiz Ağ Mimarisi

* **Coğrafi Dağıtık Canlı Mimari:** Türkiye'nin ve dünyanın stratejik farklı noktalarında konuşlanmış veri merkezleri, aktif-aktif (active-active) modda milisaniyeler içinde senkronize olur.
* **Anycast ve Akıllı Yük Dengeleme:** Bir veri merkezi fiziksel veya siber bir saldırıyla tamamen yok olsa dahi, Anycast IP mimarisi trafiği milisaniyeler içinde ayaktaki diğer kaleye yönlendirir; kullanıcı kesintiyi hissetmez.
* **Woodpecker ile Dağıtık İş Yükleri:** CI/CD süreçleri, merkezi sunucudan bağımsız çalışan, izole ve konteyner tabanlı (Podman/Docker) Woodpecker ajanları tarafından yürütülerek ana gövdeyi yükten arındırır.

#### 4. Kusursuz Güvenlik ve Siber Tehdit Avcılığı (DevSecOps)

* **Sıfır Güven (Zero Trust) Yaklaşımı:** Ağ içindeki hiçbir aktöre veya sisteme güvenilmez; her istek, her kod gönderimi (push) ve her CI/CD tetiklenmesi katı kriptografik doğrulamalardan geçer.
* **Donanımsal Kriptografi (HSM & YubiKey):** Kod imzaları, SSL sertifikaları ve kullanıcı kimlikleri, fiziksel Donanımsal Güvenlik Modülleri (HSM) ve iki aşamalı donanım anahtarlarıyla korunur.
* **Anlık Telemetri ve Hazır Kıta SOC:** Prometheus, Grafana ve ELK altyapısıyla mikrosaniyeler seviyesinde izlenen sistemde, en ufak bir anomali 7/24 görev yapan Siber Operasyon Merkezi (SOC) ve DevSecOps ekipleri tarafından anında imha edilir.

---

### BÖLÜM 1: KAMU REGÜLASYONLARI VE UYUMLULUK MATRİSİ

Platformun tüm mimarisi; Türkiye Cumhuriyeti kanunlarına, ulusal siber güvenlik stratejilerine ve uluslararası bilgi güvenliği standartlarına %100 uyumlu olacak şekilde yapılandırılmıştır.

#### 1. KVKK (6698 Sayılı Kişisel Verilerin Korunması Kanunu) Uyumluluğu

* **Veri Sorumlusu ve Veri İşleyen Rollerinin Ayrımı:** Platform üzerinde barındırılan geliştirici bilgileri, log kayıtları ve kullanıcı profilleri KVKK'ya uygun olarak tasnif edilir. Veriler, açık rıza ve kanuni istisnalar çerçevesinde işlenir.
* **Veri Yerelliği Sınırı:** Tüm kaynak kodlar, meta-veriler, kullanıcı bilgileri ve CI/CD işlem geçmişleri Türkiye Cumhuriyeti sınırları içerisindeki yerli veri merkezlerinde tutulur; yurt dışına veri aktarımı teknik olarak engellenir.
* **Kriptografik Koruma ve Maskeleme:** Veri tabanındaki kişisel veriler AES-256 algoritması ile şifrelenir. Test ve CI/CD süreçlerinde gerçek verilerin kullanılmaması için otomatik veri maskeleme (data masking) mekanizmaları devreye alınır.
* **Unutulma Hakkı ve Anonimleştirme:** Ayrılan personelin veya silinen hesapların verileri, mevzuatın öngördüğü yasal saklama süreleri (5651 sayılı kanun vb.) bittikten sonra geri döndürülemeyecek şekilde silinir veya anonimleştirilir.

#### 2. ISO/IEC 27001:2022 Bilgi Güvenliği Yönetim Sistemi Uyum Maddeleri

* **A.8.20 Ağ Güvenliği & A.8.24 Kaynak Kod Güvenliği:** Geliştirilen ve barındırılan tüm kodlar, statik (SAST) ve dinamik (DAST) güvenlik analizlerinden geçirilmeden ana dallara (master/main) kabul edilmez.
* **A.5.15 Varlıkların Sınıflandırılması:** Kaynak kod depoları; "Gizli", "Hizmete Özel" ve "Kritik" gibi etiketlerle sınıflandırılır. Erişim yetkileri "En Düşük Yetki İlkesi" (Principle of Least Privilege) uyarınca dağıtılır.
* **A.8.22 Günlük Kaydedilmesi (Logging):** Sistemdeki her `git push`, `fetch`, kullanıcı girişi ve CI/CD tetiklenmesi, değiştirilemez ve silinemez şekilde (WORM - Write Once Read Many) loglanır. Loglar merkezi SIEM sistemine kriptografik zaman damgasıyla aktarılır.

#### 3. T.C. Cumhurbaşkanlığı Dijital Dönüşüm Ofisi (DDO) Bilgi ve İletişim Güvenliği Rehberi Uyumluluğu

* **Varlık Grubu Güvenliği (Kritik Altyapılar):** Sistem, rehberde yer alan "Müşterek Hatlar ve Kritik Kamu Altyapıları" yönergelerine göre en üst seviye güvenlik önlemleriyle (Seviye 3) yapılandırılır.
* **Sıkılaştırma (Hardening) Prosedürleri:** İşletim sistemi (Linux Kernel), konteyner motorları (Podman/Docker) ve Forgejo/Woodpecker servisleri CIS (Center for Internet Security) Benchmark standartlarına göre sıkılaştırılır. Gereksiz tüm port ve servisler kapatılır.
* **Yerli Kriptografi Kullanımı:** Kamu ağları içerisindeki veri transferlerinde, rehberin yönlendirmelerine uygun olarak onaylı yerli şifreleme algoritmaları ve donanımsal güvenlik modülleri (HSM) desteklenir.

---

### BÖLÜM 2: FORGEJO VE WOODPECKER HIGH AVAILABILITY (HA) TEKNİK MİMARİSİ

Sistemin kalbini oluşturan Forgejo ve Woodpecker CI/CD altyapısı, tek bir noktanın çökmesiyle (Single Point of Failure - SPoF) tüm sistemin durmasını engelleyecek şekilde **Aktif-Aktif (Active-Active)** kümeleme (clustering) mimarisinde tasarlanmıştır.

```
                  [ Geliştirici / İstemci Trafiği ]
                                  │
      ┌───────────────────────────┴───────────────────────────┐
      ▼                                                       ▼
[ Anycast DNS / BGP Edge Router ]            [ Anycast DNS / BGP Edge Router ]
      │                                                       │
      ├───────────────────────────┬───────────────────────────┤
      ▼                           ▼                           ▼
[ Donanımsal Yük Dengeleyici ] [ Donanımsal Yük Dengeleyici ] [ Donanımsal Yük Dengeleyici ]
  (F5 / HAProxy - Veri Merkezi 1)    (Global Trafik Yönetimi)     (F5 / HAProxy - Veri Merkezi 2)
      │                                                       │
      ├───────────────────────────┐                           ├───────────────────────────┐
      ▼                           ▼                           ▼                           ▼
[ Forgejo App Instance 1 ]  [ Forgejo App Instance 2 ]  [ Forgejo App Instance 3 ]  [ Forgejo App Instance 4 ]
  (Stateless - K8s / Podman)  (Stateless - K8s / Podman)  (Stateless - K8s / Podman)  (Stateless - K8s / Podman)
      │                           │                           │                           │
      └─────────────┬─────────────┘                           └─────────────┬─────────────┘
                    │                                                       │
                    ▼                                                       ▼
     [ GlusterFS / Ceph Cluster ] <════════Geo-Replication════════> [ GlusterFS / Ceph Cluster ]
      (Paylaşımlı Git Depoları - NVMe)                             (Paylaşımlı Git Depoları - NVMe)
                    │                                                       │
                    ▼                                                       ▼
     [ PostgreSQL Cluster (Patroni) ] <═══Senkron Replikasypn═══>  [ PostgreSQL Cluster (Patroni) ]
      (Aktif / Okuma-Yazma)                                         (Pasif / Salt Okunur - HA)
                    │                                                       │
                    └───────────────────────────┬───────────────────────────┘
                                                ▼
                                   [ Redis / Valkey Cluster ]
                                    (Oturum ve Önbellek Yönetimi)
                                                │
      ┌─────────────────────────────────────────┴─────────────────────────────────────────┐
      ▼                                                                                   ▼
[ Woodpecker CI/CD Server (HA) ]                                    [ Woodpecker CI/CD Server (HA) ]
      │                                                                                   │
      ├───────────────────────────┬───────────────────────────┐                           │
      ▼                           ▼                           ▼                           ▼
[ Woodpecker Agent 01 ]     [ Woodpecker Agent 02 ]     [ Woodpecker Agent 03 ]     [ Woodpecker Agent 04 ]
 (İzole Havuz - DMZ)         (İzole Havuz - DMZ)         (İzole Havuz - DMZ)         (İzole Havuz - DMZ)
```

#### Bileşen Detayları ve Kesintisiz Akış

1. **Giriş Katmanı (Yük Dengeleme):** Gelen trafik BGP Anycast ve donanımsal yük dengeleyiciler katmanında karşılanır. Bir veri merkezi tamamen devre dışı kalsa bile trafik milisaniyeler içinde diğer merkezdeki yük dengeleyicilere akar.
2. **Uygulama Katmanı (Durumsuz / Stateless Forgejo):** Forgejo sunucuları konteyner mimarisinde (Kubernetes veya Podman Pods) tamamen durumsuz (stateless) çalışır. Sunucu sayısı yüke göre dinamik olarak ölçeklenir. Bir Forgejo podu çökerse, yük dengeleyici trafiği anında diğer podlara yönlendirir.
3. **Depolama Katmanı (Dağıtık Dosya Sistemi):** Git depoları (`.git` klasörleri), NVMe diskler üzerinde çalışan **Ceph** veya **GlusterFS** gibi yüksek performanslı dağıtık dosya sistemlerinde barındırılır. Veri merkezleri arasında anlık coğrafi replikasyon (geo-replication) yapılır.
4. **Veri Tabanı Katmanı (PostgreSQL + Patroni):** Uygulama verileri, **Patroni** ve **Etcd** ile yönetilen yüksek erişilebilir PostgreSQL kümesinde tutulur. Ana veri merkezindeki "Master" veri tabanı çöktüğü an, milisaniyeler içinde diğer veri merkezindeki "Standby" sunucu otomatik olarak "Master" rolüne yükseltilir (Automatic Failover).
5. **CI/CD Katmanı (Woodpecker Sunucu ve Ajanları):** Woodpecker Server mimarisi yedekli çalışır ve durum bilgisini ortak PostgreSQL/Redis kümesinden okur. Woodpecker Ajanları (Agents) ise ayrı donanım havuzlarında dağıtık olarak konumlandırılır. Bir ajanın üzerinde koşan derleme (build) işlemi donanımsal arıza nedeniyle kesilirse, Woodpecker Server işi otomatik olarak başka bir sağlıklı ajana devreder ve kaldığı yerden başlatır.

---

### BÖLÜM 3: SİSTEM İÇİN SLA (HİZMET SEVİYESİ TAAHHÜDÜ) KRİTERLERİ

Görev ve hayat kritik sistemlerin doğası gereği, platformun kesinti toleransı neredeyse sıfırdır. Bu doğrultuda taahhüt edilen yasal ve teknik **SLA (Service Level Agreement)** parametreleri aşağıda belirlenmiştir:

| Kriter Raporlama Alanı | Hedef Taahhüt Seviyesi | Maksimum Tolere Edilebilir Kesinti Süresi |
| :--- | :--- | :--- |
| **Yıllık Kullanılabilirlik (Uptime)** | **%99.999 (Five Nines)** | Yılda en fazla **5 dakika 15 saniye** kesinti |
| **Aylık Kullanılabilirlik (Uptime)** | **%99.999** | Ayda en fazla **26 saniye** kesinti |
| **RPO (Recovery Point Objective)** | **≤ 1 Saniye** | Olası bir felaket anında maksimum veri kaybı sınırı |
| **RTO (Recovery Time Objective)** | **≤ 30 Saniye** | Felaket anında yedek merkezin tam kapasite devreye girme süresi |

#### SLA Cezai ve Operasyonel Şartları

* **Planlı Bakım İstisnaları:** Sistem üzerinde yapılacak planlı bakım ve güncelleme çalışmaları, aktif-aktif mimari sayesinde kullanıcı trafiği kesilmeden (Zero-Downtime Deployment / Blue-Green Deployment) gerçekleştirilir. Bu nedenle planlı bakımlar dahi %99.999 taahhüdünü ihlal edemez.
* **I/O ve Performans Taahhüdü (SLA Garantisi):** Git klonlama (`git clone`) ve gönderme (`git push`) işlemlerindeki disk gecikmesi (latency), eşzamanlı 10.000 işlem altında dahi **15 milisaniyenin** üzerine çıkamaz. Aksi durum sistem zafiyeti (Degraded Performance) sayılır ve SLA ihlali sürecini başlatır.
* **7/24 Kesintisiz Destek (Siren Response):** Hayat kritik bir sistem arızasında (Örn: Hastane entegrasyon kodunun derlenememesi), resmi olarak atanmış hazır kıta DevSecOps mühendislerinin olaya müdahale süresi (Response Time) en fazla **5 dakika**, sorunu kalıcı veya geçici geçiş yoluyla çözme süresi (Resolution Time) en fazla **15 dakikadır**.

---

### BÖLÜM 4: CEPH / GLUSTERFS DEPOLAMA KÜMESİ METRİKLERİ VE HESAPLAMALARI

Yüksek girdi/çıktı (I/O) kapasitesini ve 7/24/365 ağ sürekliliğini garanti altına almak için tasarlanan depolama ve ağ mimarisi gereksinim hesaplamaları şu şekildedir:

#### 1. Disk IOPS Hesaplamaları

* **Hedef ve Varsayımlar:** Eşzamanlı aktif 10.000 geliştirici/servis girdisi, anlık yoğun Git operasyonları ve Woodpecker CI/CD ardışık yazma yükleri simüle edilmiştir. Ortalama blok boyutu 4KB ila 64KB arasında değişkenlik göstermektedir. Git işlemleri doğası gereği yoğun küçük dosya okuma/yazma (Random I/O) karakteristiğindedir.
* **Gerekli IOPS Kapasitesi:** Kusursuz çalışma için sistem genelinde en az **150.000 Random Write IOPS** ve **350.000 Random Read IOPS** (Toplam: **500.000 IOPS**) alt limiti belirlenmiştir.
* **Donanım Seçimi ve Mimari Dağılım:** Ceph OSD (Object Storage Daemon) düğümlerinde standart kurumsal NVMe diskler (U.2/U.3 PCIe Gen4, minimum 3 DWPD dayanıklılık) kullanılacaktır. Tek bir kurumsal NVMe disk ortalama 800.000 Read / 150.000 Write IOPS üretebilmektedir. Ancak Ceph üzerindeki ağ gecikmesi, replikasyon faktörü (3x) ve günlükleme (journaling) yükü nedeniyle %60 performans amortismanı hesaplanmıştır.
* **Düğüm (Node) Dağılımı:** Her veri merkezinde en az **5 adet Depolama Düğümü** bulunacaktır. Her düğümde 4'er adet U.3 NVMe disk yer alacaktır. Toplamda 20 NVMe disk, ağ replikasyon katmanına rağmen ham donanım gücüyle SLA'de belirtilen 15ms sınırını kararlılıkla koruyacaktır.

#### 2. Ağ Bant Genişliği (Bandwidth) Gereksinimleri

* **İç Ağ (Cluster Network - Doğu-Batı Trafiği):** Ceph replikasyon trafiği, veri tabanı senkronizasyonu ve Woodpecker ajanlarının ham dosya transferleri için her depolama ve uygulama sunucusu **çift yedekli (LACP Bonded) 2x 25 Gbps (Toplam 50 Gbps) RoCEv2 (RDMA over Converged Ethernet)** arabirimleri ile bağlanacaktır. Doğu-Batı yönündeki cluster omurgası toplamda **100 Gbps non-blocking** anahtarlarla (Switches) donatılacaktır.
* **Dış Ağ / Coğrafi Replikasyon (Dış Dünya - Kuzey-Güney Trafiği):** Veri merkezleri arasındaki aktif-aktif senkronizasyon ve kullanıcı/servis trafiğinin karşılanması için coğrafi lokasyonlar arasında en az **2x 10 Gbps tamamen yedekli, kiralık karanlık fiber (Dark Fiber) veya DWDM hatları** konumlandırılacaktır. Bu hatlar üzerindeki gecikme (RTT - Round Trip Time) **< 5 milisaniye** olmak zorundadır.

---

### BÖLÜM 5: SİBER SALDIRILARA KARŞI DONANIMSAL GÜVENLİK MODÜLÜ (HSM) VE API GATEWAY MİMARİSİ

Sistem mimarisi, siber savaş ve devlet düzeyinde gelişmiş kalıcı tehdit (APT) senaryolarına karşı iki aşamalı bir zırh ile donatılmıştır:

```
[ Dış İstekler / Servis Trafiği ] ──► [ API Gateway Katmanı (Trafik ve Politika Denetimi) ]
                                             │ (Kriptografik İmza / Token Doğrulama)
                                             ▼
                                     [ HSM Katmanı ] (Donanımsal Kriptografi)
                                             │ (Anahtar Yönetimi ve Şifre Çözme)
                                             ▼
                                     [ Forgejo Core & Woodpecker ]
```

#### 1. API Gateway Katmanı (Politika ve Sınır Koruyucu)

* **Tek Giriş Noktası ve Soyutlama:** Tüm Git (HTTPS/SSH) ve REST API trafiği, mikro servis omurgasının önünde yer alan yüksek performanslı, dağıtık bir **API Gateway** kümesi tarafından karşılanır. Çekirdek servisler (Forgejo ve Woodpecker API'leri) dış ağa doğrudan asla açık değildir.
* **DDoS ve Hız Sınırlama (Rate Limiting):** API Gateway, IP, Token ve Kurum ID bazlı gelişmiş hız sınırlama politikaları uygular. Saniyede gelen istek sayısı anomali seviyesine ulaştığında (örneğin kaba kuvvet saldırıları veya botnet Git klonlama girişimleri), ilgili trafik API Gateway seviyesinde milisaniyeler içinde bloklanır.
* **WAF ve Tehdit Engelleme:** HTTP/REST istekleri, OWASP Top 10 tehditlerine karşı satır satır incelenir; SQL Enjeksiyonu, Komut Çalıştırma (RCE) ve Cross-Site Scripting (XSS) barındıran paketler çekirdek sisteme ulaşmadan imha edilir.

#### 2. Donanımsal Güvenlik Modülü (HSM) Katmanı (Kriptografik Çekirdek)

* **FIPS 140-2 Level 3 / EAL4+ Güvencesi:** Platforma entegre edilen fiziksel veya sanallaştırılmış ağ tipi HSM cihazları, en üst düzey güvenlik standartlarına sahiptir. Kriptografik anahtarlar hiçbir şekilde yazılımsal belleğe (RAM) çıkmaz; tüm şifreleme ve deşifre işlemleri donanım içinde izole gerçekleştirilir.
* **Kod İmzalama (Git Commit Signing & Attestation):** Geliştiricilerin sisteme gönderdiği kodların orijinalliği ve Woodpecker CI/CD hattından çıkan derlenmiş paketlerin (artifacts) bütünlüğü, HSM içerisindeki özel anahtarlar ile otomatik olarak imzalanır. Tedarik zinciri saldırıları (Supply Chain Attacks) bu sayede tamamen engellenir.
* **SSL/TLS Sonlandırma ve Oturum Güvenliği:** API Gateway'in kullanıcılarla kurduğu güvenli bağlantıların (HTTPS) SSL anahtarları ve kullanıcıların çift aşamalı kimlik doğrulama (MFA/YubiKey) kök sertifikaları HSM içinde saklanır. Kurumsal sunucu ele geçirilse dahi anahtarlar çalınamaz.

---

### BÖLÜM 6: KAMU KURUMLARI İÇİN TEKNİK ŞARTNAME (RFP) TASLAK MADDELERİ

Kamu ihaleleri, doğrudan alımlar ve teknik şartname süreçlerinde kullanılmak üzere hazırlanmış yasal bağlayıcılığı yüksek madde taslakları:

* **Madde 1 (Platform Mimarisi):** Yüklenici tarafından kurulacak kaynak kod barındırma sistemi, tamamen açık kaynak kodlu ve bağımsız topluluklarca desteklenen Forgejo altyapısını; CI/CD otomasyon sistemi ise hafif ve konteyner tabanlı Woodpecker mimarisini temel almalıdır. Sistem, mülkiyet kısıtlaması içeren veya yurt dışı lisans bağımlılığı bulunan (GitHub Enterprise, GitLab Enterprise vb.) ürünleri içermemelidir.
* **Madde 2 (Donanımsal ve Coğrafi Yedeklilik):** Sistem, en az iki farklı coğrafi lokasyonda Aktif-Aktif (Active-Active) modda çalışabilir mimaride kurulacaktır. Lokasyonlar arası veri senkronizasyonu milisaniyeler seviyesinde gerçekleşmeli, ana veri merkezinin tamamen erişilemez olması durumunda sistem kesintisiz olarak (RTO ≤ 30 saniye, RPO ≤ 1 saniye) yedek merkezden hizmet vermeye devam etmelidir.
* **Madde 3 (Depolama ve Performans):** Depolama katmanı, dağıtık ve yatayda ölçeklenebilir bir mimaride (Ceph veya GlusterFS) kurulmalıdır. Sistemin toplam Girdi/Çıktı kapasitesi en az 500.000 IOPS değerini sağlamalı ve eşzamanlı 10.000 Git operasyonu altında disk erişim gecikmesi 15 milisaniyeyi aşmamalıdır.
* **Madde 4 (Regülasyon ve Veri Güvenliği):** Platform; 6698 Sayılı KVKK, ISO/IEC 27001:2022 standartları ve T.C. Cumhurbaşkanlığı Dijital Dönüşüm Ofisi Bilgi ve İletişim Güvenliği Rehberi (Seviye 3) gereksinimlerine tam uyumlu olmalıdır. Tüm kaynak kodlar, meta-veriler ve loglar ulusal sınırlar içerisinde kalmalı, kapalı ağ (Air-Gapped) modunda hiçbir internet bağlantısına ihtiyaç duymadan çalışabilmelidir.
* **Madde 5 (Kriptografik Sertifikasyon):** Sistem; kullanıcı kimlik doğrulamaları, oturum şifrelemeleri ve kod imzalama işlemleri için fiziksel veya ağ tabanlı, FIPS 140-2 Level 3 sertifikasına sahip Donanımsal Güvenlik Modülü (HSM) cihazları ile entegre çalışacaktır. Şifreleme anahtarları sunucu belleklerinde açıkta saklanamaz.
* **Madde 6 (SLA ve Sürdürülebilirlik):** Yüklenici, sistemin yıllık erişilebilirlik oranını %99.999 (Five Nines) olarak taahhüt edecektir. Görev ve hayat kritik durumlarda oluşabilecek P1 (Kritik) seviye arızalara müdahale süresi maksimum 5 dakika, çözüm süresi maksimum 15 dakika olacaktır.
