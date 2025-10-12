# Feature Specification: Bayan Kuaförü Randevu ve Yönetim Sitesi

**Feature Branch**: `001-bayan-kuaf-i`  
**Created**: 2025-10-12  
**Status**: Draft  
**Input**: User description: "bayan kuaföü için bir web sitesi tasarla. Yönetici, çalışan ve müşterilerin kullanabileceği bir web sitesi olsun. Tema modern ve şık olacak. modüler bir yapı olacak. Yönetici kendi sayfasında salonla ilgili çalışma saatleri özel günler hizmetler gibi salon yönetimi ile ilgili herşeyi yapabilecek salonun raporlamasıı görebilecek ve kullanıı rol atamasıı yada diğer düzenlemeleri yönetici yapacak. çalışan yada müşteriler rol hariç büün kendine ait herşeyi düzenleyebilecek. yönetici/çalışan telefon ile aranan müşterilere randevu açabilecek ad soyad ve telefon numarası ile. kayıtlı müşteriler/kayıtsız müşteriler web sitesi üzerinden randevu oluşturabilecek randevu oluştururken çatışma kontrolü yapılmayacak kullanıı sadece tarih saat ve alacağı hizmetleri belirleyecek ve sisteme bekleyen olarak kaydedilip müşteriye randevu takip numarası verecek. takip numarası benzersiz olacak. kayıtlı müşteri bilgileri otomotik gelecek eğer giriş yaptıysa kayıtsız müşteriler ad soyad ve telefon numarası girecek. web sitesine kayıtlar yönetici davet yoluyla olacak ve davet süresi 1 hafta olacak ve davet sadece bir kişi için kullanılacak. web sitesinde galeride olmasıı istiyorum galeri video ve resim olarak ayrılacak. müşteriler alınan hizmete yönelik yorum ve beğeni bırakacak. yorumlar otomotik onaylı olacak. müşteriler kendi yorumlarıı düzenleyip silebilecek. büün yorumları sadece admin düzenleyip silebilir. web sitesi ana sayfasışık ve çok iyi tasarlanmalı. sosyal medya linkleri olacak (facebook, instegram ve tiktok) ayrıca telefon ile arama ve whatsapp üzerinden iletişime geçme linkleri olacak. randevu ile illgili herşeyde randevuyu oluşturan kullanıı ile yöneticiye sms, websocket ve mail ile bildirim gidecek. kayıtsız müşteriler randevu takip numarası ile randevularıı takip edebilecek kayıtlı müşteriler kendi sayfasında randevu geçmişinden takip edebilecek. api istemiyorum. web sayfası responsiv ve mobil uyumlu olacak"
**Dokümantasyon Kaynağı**: Context7 MCP referansı `/vercel/next.js` (alım tarihi: 2025-10-12)

## User Scenarios & Testing *(mandatory)*

<!--
  IMPORTANT: User stories should be PRIORITIZED as user journeys ordered by importance.
  Each user story/journey must be INDEPENDENTLY TESTABLE - meaning if you implement just ONE of them,
  you should still have a viable MVP (Minimum Viable Product) that delivers value.
  
  Assign priorities (P1, P2, P3, etc.) to each story, where P1 is the most critical.
  Think of each story as a standalone slice of functionality that can be:
  - Developed independently
  - Tested independently
  - Deployed independently
  - Demonstrated to users independently
  - Tasarımı sadece shadcn bileşenleriyle uygulanmış olacak şekilde belgelenmiş
-->

### User Story 1 - Müşterinin Web Üzerinden Randevu Oluşturması (Priority: P1)

Kayıtlı veya kayıtsız müşteri, modern ana sayfa üzerinden salon hizmetlerini inceleyip tarih, saat ve hizmet seçerek randevu talebi bırakır; sistem talebi çakışma kontrolü yapmadan beklemeye alır.

**Why this priority**: Randevu talebi salonun ana gelir kaynağıdır ve hem kayıtlı hem de yeni müşterilerin kolayca işlem yapması gerekir.

**Independent Test**: "Konuk veya giriş yapmış müşteri, web formunu doldurup benzersiz takip numarası aldığı bir randevuyu oluşturabilir mi?"

**Acceptance Scenarios**:

1. **Given** müşteri hizmet listesini görür, **When** tarih, saat ve hizmetleri seçip formu gönderir, **Then** sistem randevuyu bekleyen olarak kaydeder ve benzersiz takip numarasını gösterir.
2. **Given** müşteri giriş yapmıştır, **When** randevu formunu açar, **Then** ad, soyad ve telefon bilgileri otomatik doldurulur.
3. **Given** müşteri kayıtlı değildir, **When** randevu formunu gönderir, **Then** ad, soyad ve telefon zorunlu alanlar olarak kaydedilir ve takip numarası e-posta/SMS ile paylaşılır.
4. **Given** bekleyen randevu kaydedilmiştir, **When** yönetici/çalışan talep bildirimi alır, **Then** randevu onay sırasına eklendiğini görür.
5. **Given** müşteri çalışma saatleri dışındaki bir tarih veya saat seçer, **When** formu göndermeye çalışır, **Then** sistem seçimi engeller ve telefon/WhatsApp iletişim bağlantılarıyla yönlendirme mesajı gösterir.

---

### User Story 2 - Yönetici/Çalışanın Randevu Oluşturması ve Onay Süreci (Priority: P1)

Salon ekibi telefonla arayan müşteriler için randevu açar; sistem tarih-saat çakışmasını hemen kontrol eder. Ayrıca web üzerinden gelen bekleyen talepleri inceleyip çakışma uyarılarıyla onaylar veya revize eder.

**Why this priority**: Telefonla rezervasyon salon operasyonlarında kritik olduğu için çevrimdışı müşterilere hizmet sunmayı sürdürür; bekleyen web taleplerinin onayı iş sürekliliğini garanti eder.

**Independent Test**: "Çalışan, telefonla gelen randevularda çakışma uyarılarını görüp onaylayabiliyor ve web üzerinden gelen bekleyen talepleri aynı süreçle finalize edebiliyor mu?"

**Acceptance Scenarios**:

1. **Given** çalışan randevu panosuna erişir, **When** arayan müşterinin ad, soyad ve telefonunu girip hizmet/tarih/saat seçer, **Then** sistem çakışma kontrolü yapar, sonuç ekranında uyarı durumunu gösterir.
2. **Given** çakışma uyarısı gösterilmiştir, **When** yönetici aynı slotu buna rağmen onaylar, **Then** randevu onaylı statüsüne geçer ve uyarı kaydedilir; müşteriye onay bildirimi gider.
3. **Given** çakışma uyarısı gösterilmiştir, **When** çalışan yeni bir tarih veya saat seçer ve onaylar, **Then** randevu revize edilmiş bilgilerle onaylanır ve müşteriye revizyon bildirimi gönderilir.
4. **Given** web üzerinden bekleyen bir randevu vardır, **When** çalışan onay işlemini başlatır, **Then** sistem çakışma kontrolü yapar ve yukarıdaki karar akışını uygular.

---

### User Story 3 - Yönetici Salon Takvimi, Özel Günler ve Raporları Yönetir (Priority: P1)

Yönetici paneli üzerinden tek bir salon takvimini yönetir, çalışma saatleri ve özel günleri (tekrar edenler dahil) tanımlar, kullanıcı davetleri ve raporları kontrol eder.

**Why this priority**: Tek takvimli yapı, operasyonel planlamanın standartlaşmasını ve müşteri deneyiminin tutarlı olmasını sağlar.

**Independent Test**: "Yönetici paneli, salon takvimini tek kaynaktan yönetip özel gün tekrarlarını ayarlayabiliyor ve raporları görüntüleyebiliyor mu?"

**Acceptance Scenarios**:

1. **Given** yönetici paneline giriş yapılmıştır, **When** çalışma saatlerini ve özel günleri düzenler, **Then** tüm salon takvimi bu ayarlara göre güncellenir.
2. **Given** yönetici özel gün ekler, **When** tekrar ayarını (ör. her yıl) işaretler, **Then** ilgili tarih geldiğinde sistem otomatik olarak özel çalışma saatini uygular.
3. **Given** yönetici raporları açar, **When** tarih aralığı seçer, **Then** gerçekleşen, bekleyen, onaylanmış, iptal ve indirimli işlemleri içeren metrikler listelenir.
4. **Given** yönetici yeni personel veya müşteri kaydı başlatmak ister, **When** davet oluşturur ve gönderir, **Then** davet 1 hafta geçerli benzersiz kod içerir ve tek kullanımlıdır.

---

### User Story 4 - Hizmet Tamamlama ve Ödeme Kaydı (Priority: P1)

Hizmet tamamlandığında çalışan veya yönetici, alınan hizmetleri gözden geçirir, gerekirse günceller, indirimleri uygular ve ödeme yöntemini seçerek işlemi kesinleştirir; aynı seansta personel notu eklenir.

**Why this priority**: Salon gelirlerinin doğru kaydedilmesi ve müşteri geçmişinin zenginleştirilmesi hem finansal raporlama hem de kişisel hizmet kalitesi için kritiktir.

**Independent Test**: "Bir randevu tamamlandığında hizmet listesi, indirimler ve ödeme yöntemi tek ekranda güncellenip kaydedilebiliyor mu?"

**Acceptance Scenarios**:

1. **Given** randevu tamamlanma aşamasındadır, **When** çalışan ödeme ekranını açar, **Then** müşterinin seçtiği hizmetler standart fiyatlarıyla otomatik olarak listelenir.
2. **Given** müşteri ek hizmet almıştır, **When** çalışan hizmeti ekler veya çıkarır, **Then** toplam tutar anında yeniden hesaplanır.
3. **Given** yönetici önceden indirim tanımlamıştır, **When** randevu tamamlanırken indirim koşulu sağlanır, **Then** indirim otomatik uygulanır ve çalışan gerekirse ek manuel indirim girer.
4. **Given** ödeme tutarı netleşmiştir, **When** çalışan fiziksel kredi kartı, havale/EFT veya nakit seçeneklerinden birini seçip kaydeder, **Then** ödeme kaydı hizmet ve indirim kırılımlarıyla veri tabanına işlenir.
5. **Given** çalışan hizmet sonrası not bırakmak ister, **When** kişisel bakım notunu kaydeder, **Then** not yalnızca yönetici ve ilgili çalışanlar tarafından görüntülenir.

---

### User Story 5 - Müşteri Deneyimi ve Etkileşim (Priority: P2)

Müşteri aldığı hizmetlere yorum ve beğeni bırakır, gerektiğinde kendi yorumunu günceller veya siler; galeri içerikleri ile salona dair güven kazanır.

**Why this priority**: Sosyal kanıt ve müşteri geri bildirimi yeni müşterilerin karar alma sürecini hızlandırır.

**Independent Test**: "Bir müşteri, aldığı hizmet için yorum bırakıp düzenleyebiliyor ve galeri içeriğine erişebiliyor mu?"

**Acceptance Scenarios**:

1. **Given** müşteri hizmetini tamamlamıştır, **When** yorum formunu doldurup gönderir, **Then** yorum otomatik onaylanır ve ilgili hizmet altında görünür.
2. **Given** müşteri önceki yorumunu düzenlemek ister, **When** içerik güncellemesi yapar veya yorumu siler, **Then** listeleme anında güncellenir.
3. **Given** yönetici tutarsızlık görür, **When** müşteri yorumunu düzenler veya siler, **Then** değişiklik kayıt altına alınır.

---

### User Story 6 - Modern Ana Sayfa, Çalışma Saatleri ve İletişim (Priority: P3)

Ziyaretçiler salonu tanıtan modern, şık ana sayfayı görüntüler, çalışma saatlerini inceler, sosyal medya ve doğrudan iletişim bağlantılarıyla etkileşime geçer; responsiv tasarım sayesinde tüm cihazlarda içerik erişilebilir olur.

**Why this priority**: Çalışma saatlerine hızlı erişim ve mobil dostu iletişim seçenekleri yeni müşteri kazanımını belirgin şekilde etkiler.

**Independent Test**: "Farklı ekran boyutlarında ana sayfa yerleşimi bozulmadan çalışma saatleri, sosyal medya ve iletişim bağlantılarına erişiliyor mu?"

**Acceptance Scenarios**:

1. **Given** kullanıcı ana sayfaya gelir, **When** salon çalışma takvimini görüntüler, **Then** mevcut hafta için çalışma gün ve saatlerini (özel günler dahil) kolayca görür.
2. **Given** kullanıcı özel güne denk gelen bir tarihi inceler, **When** takvimde özel gün bilgisi bulunur, **Then** özel çalışma saatleri standart saatlerin yerine gösterilir.
3. **Given** kullanıcı mobil cihazdadır, **When** telefon veya WhatsApp bağlantısına tıklar, **Then** cihazın arama veya mesaj uygulaması açılır.
4. **Given** kullanıcı sosyal medya bağlantılarına bakar, **When** Facebook, Instagram veya TikTok ikonuna tıklar, **Then** ilgili sosyal medya sayfasına yönlendirilir.
5. **Given** kullanıcı galeri bölümünü açar, **When** video veya fotoğraf sekmesini seçer, **Then** içerik türüne göre filtrelenmiş modern bir sunum görür.

### Edge Cases

- Davet kodu süresi dolmuşsa davetli kullanıcı kayıt tamamlayamaz ve yöneticiye otomatik bildirim gider.
- Aynı telefon numarasıyla birden fazla bekleyen randevu oluşturulduğunda her biri ayrı takip numarasıyla saklanır.
- Müşteri çalışma saatleri dışındaki bir slotu seçmeye çalıştığında form gönderimi engellenir ve alternatif iletişim bağlantıları gösterilir.
- Tekrarlayan özel gün tanımları standart çalışma saatleriyle çakışırsa özel gün ayarı önceliklidir ve yöneticiden onay ister.
- Otomatik ve manuel indirimlerin toplamı tutarı sıfırın altına düşürürse sistem kullanıcıdan doğrulama ister ve minimum tutarı 0 TL olarak kaydeder.
- Hizmet tamamlanırken personel notu kaydedilemezse işlem kaydedilmez ve tekrar denenmesi istenir.
- Bildirim kanallarından biri geçici olarak başarısız olursa işlem asenkron olarak yeniden denenir ve yönetici panelinde durum gösterilir.
- Galeriye eklenen video veya görsel dosyası desteklenmeyen formatta ise yükleme reddedilir ve yöneticiden yeniden yükleme istenir.
- Çakışma uyarısı verilip onaylanan randevular raporlarda “override” etiketiyle işaretlenir; hatalı kararlar için retrospektif yapılır.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Sistem, kayıtlı müşteriler için randevu formunu kullanıcı profilinden otomatik doldurmalıdır.
- **FR-002**: Sistem, kayıtsız müşterilerin randevu oluştururken ad, soyad ve telefon bilgisini zorunlu kılmalıdır.
- **FR-003**: Sistem, her yeni randevu talebi için benzersiz takip numarası üretip müşteriye ve yöneticiye göstermelidir.
- **FR-004**: Sistem, randevu oluşturma ve durum güncelleme işlemlerinde müşteriye ve ilgili yönetici/çalışanlara SMS, e-posta ve gerçek zamanlı bildirim göndermelidir.
- **FR-005**: Sistem, çalışan veya yöneticinin telefonla randevu kaydederken tek salon takvimine göre tarih-saat çakışma kontrolü yapmalı, sonucu gösterip onay veya revizyon kararı alınmasına izin vermelidir.
- **FR-006**: Sistem, telefonla kaydedilen randevuları onaylı statüde saklamalı ve çakışma uyarısı verildiyse kararı randevu kaydına eklemelidir.
- **FR-007**: Sistem, web üzerinden oluşturulan randevuları çakışma kontrolü yapmadan bekleyen statüsünde saklamalı, müşteriye benzersiz takip numarası sunmalı ve ilgili personele bildirim göndermelidir.
- **FR-008**: Sistem, tüm randevular için tek bir salon takvimi kullanmalı ve çift kayıt yapılmasını engellemelidir.
- **FR-009**: Sistem, yöneticiye çalışma günleri ve saatlerini tek arayüz üzerinden tanımlama olanağı sunmalıdır.
- **FR-010**: Sistem, yöneticiye tekrar eden özel gün ve özel çalışma saatlerini tanımlama yetkisi vermelidir.
- **FR-011**: Sistem, özel gün tarihleri geldiğinde tanımlanan çalışma saatlerini otomatik olarak devreye almalı ve standart saatleri geçersiz kılmalıdır.
- **FR-012**: Sistem, web randevu formunda yalnızca çalışma saatleri içinde kalan zaman dilimlerini seçilebilir kılmalı; çalışma saati dışında seçim yapılırsa telefon ve WhatsApp bağlantılarıyla iletişim önerisi göstermelidir.
- **FR-013**: Sistem, salon çalışma gün ve saatlerini (özel günler dahil) web sitesinde müşterilere görünür şekilde sunmalıdır.
- **FR-014**: Sistem, yöneticiye hizmet kataloğunu (ad, süre, fiyat) oluşturma ve güncelleme olanağı sağlamalıdır.
- **FR-015**: Sistem, yöneticiye kullanıcı daveti oluşturma, davet süresini 1 haftayla sınırlama ve daveti tek kullanımla sınırlandırma yetkisi vermelidir.
- **FR-016**: Sistem, yöneticiye kullanıcı rolleri (yönetici, çalışan, müşteri) atama ve güncelleme olanağı sunmalıdır.
- **FR-017**: Sistem, yöneticiye randevu performans ve gelir raporlarını (bekleyen, onaylanan, iptal, override edilen, indirimli ve ödeme yöntemi kırılımları) görüntüleme imkânı vermelidir.
- **FR-018**: Sistem, galeri bölümünde içerikleri video ve görsel olarak ayrı sekmelerde sunmalıdır.
- **FR-019**: Sistem, müşterilerin hizmetlere yorum ve beğeni bırakmasına, yorumlarını düzenlemesine ya da silmesine izin vermelidir.
- **FR-020**: Sistem, yalnızca yöneticiye tüm yorumlar üzerinde düzenleme ve silme yetkisi tanımalıdır.
- **FR-021**: Sistem, kayıtsız müşterilerin takip numarasıyla randevu durumlarını sorgulamasına izin vermelidir.
- **FR-022**: Sistem, kayıtlı müşterilere profil alanında randevu geçmişini ve bekleyen talepleri göstermelidir.
- **FR-023**: Sistem, ana sayfada modern salon tanıtımı, sosyal medya bağlantıları, telefon ve WhatsApp iletişim linklerini sunmalıdır.
- **FR-024**: Sistem, depo standardı tasarım bileşen setiyle tutarlı bir arayüz sunmalı ve uyumsuz bileşen kullanımını engellemelidir.
- **FR-025**: Sistem geliştirmesi, proje anayasasında belirtilen Türkçe belgeleme ve güncel Context7 dokümantasyonuna uyum ilkelerine bağlı kalmalıdır.
- **FR-026**: Sistem, hizmet tamamlama ekranında randevu sırasında seçilen hizmetleri standart fiyatlarıyla otomatik olarak listelemeli ve çalışan/yöneticinin hizmet ekleme veya çıkarma işlemlerini desteklemelidir.
- **FR-027**: Sistem, hizmet tamamlama aşamasında önceden tanımlı indirimleri otomatik uygulamalı, çalışan/yöneticinin manuel indirim girmesine izin vermeli ve toplam tutarı anında yeniden hesaplamalıdır.
- **FR-028**: Sistem, ödeme sırasında yalnızca fiziksel kredi kartı, havale/EFT veya nakit seçeneklerini sunmalı ve seçilen yöntemi zorunlu kılmalıdır.
- **FR-029**: Sistem, ödeme kaydını hizmet listesi, uygulanan salon indirimleri, manuel indirimler ve nihai tutarı içerecek şekilde veri tabanına kaydetmelidir.
- **FR-030**: Sistem, hizmet tamamlandıktan sonra personelin müşteriye özel not eklemesine izin vermeli ve bu notları yalnızca yetkili personelin erişimine açık tutmalıdır.

### Key Entities *(include if feature involves data)*

- **Kullanıcı**: Roller (Yönetici, Çalışan, Müşteri), iletişim bilgileri, davet durumu, hesap erişim tarihçesi.
- **Randevu**: Takip numarası, müşteri profili veya konuk bilgileri, seçilen hizmetler, tarih-saat, oluşturma kaynağı (müşteri, çalışan, yönetici), statü geçmişi ve bildirim kayıtları.
- **Hizmet**: Hizmet adı, süresi, fiyatı, ilgili çalışan uzmanlığı, aktif/pasif durumu.
- **Davet**: Davet kodu, hedef e-posta/telefon, oluşturma ve bitiş tarihi, kullanım bilgisi, atanacak rol.
- **Yorum**: Hizmet bağlantısı, müşteri içeriği, beğeni sayısı, yönetici müdahale geçmişi, son düzenlenme zamanı.
- **Galeri Öğesi**: Tür (video/görsel), başlık, açıklama, yayın sırası, görünürlük durumu.
- **Salon Takvimi**: Standart çalışma saatleri, özel günler ve tekrar kuralları; randevu slotlarının tek kaynağı.
- **İndirim**: Yönetici tarafından tanımlanan kampanya/kupon kuralları ile personel tarafından eklenen manuel indirimler.
- **Ödeme Kaydı**: Seçilen hizmetlerin fiyat kırılımları, uygulanan indirimler, ödeme yöntemi ve işlem zamanı.
- **Müşteri Notu**: Hizmet sonrası personelin eklediği, yalnızca yetkili personelin görebildiği bakım notları.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Müşterilerin %90’ı randevu formunu 3 dakikadan kısa sürede tamamlayıp takip numarasını görüntüleyebilmelidir.
- **SC-002**: Telefonla alınan randevu kaydı işlemleri %95 oranında 2 dakikadan kısa sürede tamamlanmalıdır.
- **SC-003**: Yönetici paneli kullanıcılarının %90’ı çalışma saatleri, hizmet veya davet yönetimi görevlerini ek destek almadan tamamlayabilmelidir.
- **SC-004**: Galeri ve ana sayfa ziyaretçilerinin %80’i sosyal medya veya iletişim bağlantılarından en az birine tıklayarak etkileşime geçmelidir.
- **SC-005**: Bildirimlerin (SMS, e-posta, gerçek zamanlı) %98’i randevu oluşturma işlemini takiben 30 saniye içinde iletilmelidir.
- **SC-006**: Çakışma uyarısı verilen randevuların %95’inde yönetici/çalışan 5 dakika içinde onay veya revizyon kararı vermelidir.
- **SC-007**: Tamamlanan randevuların %100’ü ödeme kaydı, hizmet ve indirim kırılımı ile 2 dakika içinde sisteme işlenmelidir.
- **SC-008**: Ana sayfa kullanıcılerinin %90’ı çalışma saatleri bilgisini 10 saniye içinde bulup görüntüleyebilmelidir.

## Assumptions

- Mevcut müşteriler, salon tarafından iç sistemlerde saklanan hesaplarla eşleştirilecek; davetli kayıt süreci bu hesaplara bağlanacaktır.
- SMS, e-posta ve gerçek zamanlı bildirim altyapısı (örneğin mevcut servis sağlayıcılar) hâlihazırda kullanılabilir ve entegrasyon için gerekli API anahtarları sağlanacaktır.
- Salon hizmetleri ve fiyatlandırması yönetici tarafından düzenli güncellenecek; mali raporlamalar bu verilere dayanacaktır.
- Kurumun onaylı tasarım bileşen kütüphanesi bu proje kapsamında tüm arayüzler için kullanılabilir durumdadır; özel ihtiyaç durumunda bileşen kompozisyonu ile çözülecektir.
- Salon tek şubeli yapıdadır ve tüm randevular aynı takvim üzerinden yönetilecektir.
- Salonun POS cihazı, banka transfer bilgileri ve nakit süreçleri mevcut olup çevrimiçi ödeme entegrasyonu planlanmamaktadır.
- Yönetici, otomatik indirim kurallarını ve özel gün takvimini periyodik olarak gözden geçirip güncelleyecektir.

## Dependencies & Constraints

- Kullanıcı davet e-postalarının ve SMS’lerinin ulaştırılabilmesi için doğrulanmış gönderim kanallarının hazır olması gerekir.
- Tek salon takvimi, tüm çalışanlar tarafından paylaşılan tek referans olmalı; harici sistem entegrasyonu yapılmadığı için takvim çakışmalarının manuel onayı ekip sorumluluğundadır.
- Raporlama verilerinin doğruluğu için randevu durum güncellemeleri (tamamlama, iptal, override) zamanında yapılmalıdır.
- Salonun POS, havale/EFT ve nakit süreçleri işler durumda tutulmalı; çevrimiçi ödeme altyapısı olmadığından müşteri deneyimi fiziksel tahsilata bağlıdır.
- Sosyal medya ve iletişim linkleri ile çalışma saati içerikleri salon tarafından periyodik olarak doğrulanmalıdır.
- Çakışma uyarı kayıtları denetim amacıyla saklanmalı; yöneticiler düzenli olarak potansiyel kapasite sorunlarını ve sık tekrar eden özel günleri gözden geçirmelidir.
