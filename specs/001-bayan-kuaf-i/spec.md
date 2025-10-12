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

Kayıtlı veya kayıtsız müşteri, modern ana sayfa üzerinden salon hizmetlerini inceleyip tarih, saat ve hizmet seçerek randevu talebi bırakır.

**Why this priority**: Randevu talebi salonun ana gelir kaynağıdır ve hem kayıtlı hem de yeni müşterilerin kolayca işlem yapması gerekir.

**Independent Test**: "Konuk veya giriş yapmış müşteri, web formunu doldurup benzersiz takip numarası aldığı bir randevuyu oluşturabilir mi?"

**Acceptance Scenarios**:

1. **Given** müşteri hizmet listesini görür, **When** tarih, saat ve hizmetleri seçip formu gönderir, **Then** sistem randevuyu bekleyen olarak kaydeder ve benzersiz takip numarasını gösterir.
2. **Given** müşteri giriş yapmıştır, **When** randevu formunu açar, **Then** ad, soyad ve telefon bilgileri otomatik doldurulur.
3. **Given** müşteri kayıtlı değildir, **When** randevu formunu gönderir, **Then** ad, soyad ve telefon zorunlu alanlar olarak kaydedilir ve takip numarası e-posta/SMS ile paylaşılır.

---

### User Story 2 - Yönetici/Çalışanın Randevu Oluşturması ve Onay Süreci (Priority: P1)

Salon ekibi telefonla arayan müşteriler için randevu açar, gelen tüm randevu taleplerini kontrol eder, gerekirse tarih-saat revize ederek onaylar ve müşteriyi bilgilendirir.

**Why this priority**: Telefonla rezervasyon salon operasyonlarında kritik olduğu için çevrimdışı müşterilere hizmet sunmayı sürdürür; ekibin onay akışı iş sürekliliğini garanti eder.

**Independent Test**: "Çalışan, hem telefon üzerinden randevu oluşturabiliyor hem de bekleyen talepleri çakışma uyarılarıyla değerlendirip onaylayabiliyor mu?"

**Acceptance Scenarios**:

1. **Given** çalışan randevu panosuna erişir, **When** arayan müşterinin ad, soyad ve telefonunu girip hizmet/tarih/saat seçer, **Then** sistem bekleyen statülü randevuyu oluşturur ve takip numarası üretir.
2. **Given** bekleyen bir randevu vardır, **When** çalışan onay işlemini başlatır, **Then** sistem seçilen tarih-saat için çakışma kontrolü yapar ve varsa uyarı gösterir.
3. **Given** çakışma uyarısı görüntülenmiştir, **When** yönetici onaya devam etmeyi seçer, **Then** randevu onaylı statüsüne geçer ve uyarı işletme kaydına eklenir.
4. **Given** tarih-saat revizyonu gerekmiştir, **When** çalışan yeni bir slot seçip onaylar, **Then** randevu revize edilmiş bilgilerle onaylanır ve müşteriye yeni bilgi gönderilir.

---

### User Story 3 - Yönetici Salon Yönetimini ve Raporları Yönetir (Priority: P1)

Yönetici paneli üzerinden çalışma saatleri, özel günler, hizmet katalogu, kullanıcı davetleri ve rol atamalarını yönetir, raporları görüntüler.

**Why this priority**: Salonda yetkilendirme ve hizmet bilgileri tek merkezden düzenlenerek operasyonel süreklilik sağlanır.

**Independent Test**: "Yönetici paneli, salon yapılandırması ve kullanıcı yönetimi işlemlerinin tamamlanmasını tek başına destekliyor mu?"

**Acceptance Scenarios**:

1. **Given** yönetici paneline giriş yapılmıştır, **When** çalışma saatlerini ve özel günleri düzenler, **Then** değişiklikler salon takvimine yansır.
2. **Given** yönetici yeni personel veya müşteri kaydı başlatmak ister, **When** davet oluşturur ve gönderir, **Then** davet 1 hafta geçerli benzersiz kod içerir ve tek kullanımlıdır.
3. **Given** yönetici raporları açar, **When** tarih aralığı seçer, **Then** gerçekleşen/bekleyen iptal gibi randevu metrikleri listelenir.

---

### User Story 4 - Müşteri Deneyimi ve Etkileşim (Priority: P2)

Müşteri aldığı hizmetlere yorum ve beğeni bırakır, gerektiğinde kendi yorumunu günceller veya siler; galeri içerikleri ile salona dair güven kazanır.

**Why this priority**: Sosyal kanıt ve müşteri geri bildirimi yeni müşterilerin karar alma sürecini hızlandırır.

**Independent Test**: "Bir müşteri, aldığı hizmet için yorum bırakıp düzenleyebiliyor ve galeri içeriğine erişebiliyor mu?"

**Acceptance Scenarios**:

1. **Given** müşteri hizmetini tamamlamıştır, **When** yorum formunu doldurup gönderir, **Then** yorum otomatik onaylanır ve ilgili hizmet altında görünür.
2. **Given** müşteri önceki yorumunu düzenlemek ister, **When** içerik güncellemesi yapar veya yorumu siler, **Then** listeleme anında güncellenir.
3. **Given** yönetici tutarsızlık görür, **When** müşteri yorumunu düzenler veya siler, **Then** değişiklik kayıt altına alınır.

---

### User Story 5 - Modern Ana Sayfa ve İletişim (Priority: P3)

Ziyaretçiler salonu tanıtan modern, şık ana sayfayı görüntüler, sosyal medya ve doğrudan iletişim bağlantılarıyla etkileşime geçer, responsiv tasarım sayesinde tüm cihazlarda içerik erişilebilir olur.

**Why this priority**: İlk izlenim ve mobil erişim yeni müşteri kazanımını belirgin şekilde etkiler.

**Independent Test**: "Farklı ekran boyutlarında ana sayfa yerleşimi bozulmadan sosyal medya ve iletişim bağlantılarına erişiliyor mu?"

**Acceptance Scenarios**:

1. **Given** kullanıcı ana sayfaya gelir, **When** Facebook, Instagram veya TikTok ikonuna tıklar, **Then** ilgili sosyal medya sayfasına yönlendirilir.
2. **Given** kullanıcı mobil cihazdadır, **When** telefon veya WhatsApp bağlantısına tıklar, **Then** cihazın arama veya mesaj uygulaması açılır.
3. **Given** kullanıcı galeri bölümünü açar, **When** video veya fotoğraf sekmesini seçer, **Then** içerik türüne göre filtrelenmiş modern bir sunum görür.

### Edge Cases

- Davet kodu süresi dolmuşsa davetli kullanıcı kayıt tamamlayamaz ve yöneticiye otomatik bildirim gider.
- Aynı telefon numarasıyla birden fazla bekleyen randevu oluşturulduğunda her biri ayrı takip numarasıyla saklanır.
- Bildirim kanallarından biri geçici olarak başarısız olursa işlem asenkron olarak yeniden denenir ve yönetici panelinde durum gösterilir.
- Yorum düzenleme esnasında bağlantı kesilirse orijinal yorum korunur ve kullanıcıya hata mesajı sunulur.
- Galeriye eklenen video dosyası desteklenmeyen formatta ise yükleme reddedilir ve yöneticiden yeniden yükleme istenir.
- Çakışma uyarısı verilip onaylanan randevular raporlarda “override” etiketiyle işaretlenir; hatalı kararlar için retrospektif yapılır.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Sistem, kayıtlı müşteriler için randevu formunu kullanıcı profilinden otomatik doldurmalıdır.
- **FR-002**: Sistem, kayıtsız müşterilerin randevu oluştururken ad, soyad ve telefon bilgisini zorunlu kılmalıdır.
- **FR-003**: Sistem, her yeni randevu talebi için benzersiz takip numarası üretip müşteriye ve yöneticiye göstermelidir.
- **FR-004**: Sistem, randevuyu oluştururken SMS, e-posta ve gerçek zamanlı bildirim göndermelidir.
- **FR-005**: Sistem, çalışan veya yöneticinin müşteri adına telefonla randevu kaydetmesini sağlamalıdır.
- **FR-006**: Sistem, oluşturulan randevuları başlangıçta “bekleyen” statüsünde saklamalıdır; çakışma kontrolü yapılmadan kaydedilmelidir.
- **FR-007**: Sistem, yöneticinin çalışma saatleri, özel günler ve hizmet listesi tanımlamasına izin vermelidir.
- **FR-008**: Sistem, yöneticiye kullanıcı daveti oluşturma, davet süresini 1 haftayla sınırlama ve daveti tek kullanımla sınırlandırma yetkisi vermelidir.
- **FR-009**: Sistem, yöneticiye kullanıcı rolleri (yönetici, çalışan, müşteri) atama ve güncelleme olanağı sunmalıdır.
- **FR-010**: Sistem, yöneticiye randevu performans raporlarını (bekleyen, onaylanan, iptal, gelir/profil bazlı) görüntüleme imkânı vermelidir.
- **FR-011**: Sistem, galeri bölümünde içerikleri video ve görsel olarak ayrı sekmelerde sunmalıdır.
- **FR-012**: Sistem, müşterilerin yorum ve beğeni bırakmasına, yorumlarını düzenlemesine ya da silmesine izin vermelidir.
- **FR-013**: Sistem, yalnızca yöneticiye tüm yorumlar üzerinde düzenleme ve silme yetkisi tanımalıdır.
- **FR-014**: Sistem, kayıtsız müşterilerin takip numarasıyla randevu durumlarını sorgulamasına izin vermelidir.
- **FR-015**: Sistem, kayıtlı müşterilere profil alanında randevu geçmişini ve bekleyen talepleri göstermelidir.
- **FR-016**: Sistem, ana sayfada salonun modern tanıtım içeriği, sosyal medya bağlantıları, telefon ve WhatsApp iletişim linklerini sunmalıdır.
- **FR-017**: Sistem, depo standardı tasarım bileşen setiyle tutarlı bir arayüz sunmalı ve uyumsuz bileşen kullanımını engellemelidir.
- **FR-018**: Sistem geliştirmesi, proje anayasasında belirtilen Türkçe belgeleme ve güncel Context7 dokümantasyonuna uyum ilkelerine bağlı kalmalıdır.
- **FR-019**: Sistem, randevu onay sürecinde tarih-saat çakışmasını analiz ederek yönetici/çalışana uyarı mesajı göstermelidir.
- **FR-020**: Sistem, yönetici/çalışan uyarıya rağmen onaylasa bile randevuyu onaylı statüde saklarken çakışma kararını kayıt altına almalıdır.
- **FR-021**: Sistem, randevu onayı veya revizyonu tamamlandığında müşteriye ve ilgili ekibe güncel durumu bildiren SMS, e-posta ve gerçek zamanlı bildirim göndermelidir.

### Key Entities *(include if feature involves data)*

- **Kullanıcı**: Roller (Yönetici, Çalışan, Müşteri), iletişim bilgileri, davet durumu, hesap erişim tarihçesi.
- **Randevu**: Takip numarası, müşteri profili veya konuk bilgileri, seçilen hizmetler, tarih-saat, oluşturma kaynağı (müşteri, çalışan, yönetici), statü geçmişi ve bildirim kayıtları.
- **Hizmet**: Hizmet adı, süresi, fiyatı, ilgili çalışan uzmanlığı, aktif/pasif durumu.
- **Davet**: Davet kodu, hedef e-posta/telefon, oluşturma ve bitiş tarihi, kullanım bilgisi, atanacak rol.
- **Yorum**: Hizmet bağlantısı, müşteri içeriği, beğeni sayısı, yönetici müdahale geçmişi, son düzenlenme zamanı.
- **Galeri Öğesi**: Tür (video/görsel), başlık, açıklama, yayın sırası, görünürlük durumu.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Müşterilerin %90’ı randevu formunu 3 dakikadan kısa sürede tamamlayıp takip numarasını görüntüleyebilmelidir.
- **SC-002**: Telefonla alınan randevu kaydı işlemleri %95 oranında 2 dakikadan kısa sürede tamamlanmalıdır.
- **SC-003**: Yönetici paneli kullanıcılarının %90’ı çalışma saatleri, hizmet veya davet yönetimi görevlerini ek destek almadan tamamlayabilmelidir.
- **SC-004**: Galeri ve ana sayfa ziyaretçilerinin %80’i sosyal medya veya iletişim bağlantılarından en az birine tıklayarak etkileşime geçmelidir.
- **SC-005**: Bildirimlerin (SMS, e-posta, gerçek zamanlı) %98’i randevu oluşturma işlemini takiben 30 saniye içinde iletilmelidir.
- **SC-006**: Çakışma uyarısı verilen randevuların %95’inde yönetici/çalışan 5 dakika içinde onay veya revizyon kararı vermelidir.

## Assumptions

- Mevcut müşteriler, salon tarafından iç sistemlerde saklanan hesaplarla eşleştirilecek; davetli kayıt süreci bu hesaplara bağlanacaktır.
- SMS, e-posta ve gerçek zamanlı bildirim altyapısı (örneğin mevcut servis sağlayıcılar) hâlihazırda kullanılabilir ve entegrasyon için gerekli API anahtarları sağlanacaktır.
- Salon hizmetleri ve fiyatlandırması yönetici tarafından düzenli güncellenecek; mali raporlamalar bu verilere dayanacaktır.
- Kurumun onaylı tasarım bileşen kütüphanesi bu proje kapsamında tüm arayüzler için kullanılabilir durumdadır; özel ihtiyaç durumunda bileşen kompozisyonu ile çözülecektir.

## Dependencies & Constraints

- Kullanıcı davet e-postalarının ve SMS’lerinin ulaştırılabilmesi için doğrulanmış gönderim kanallarının hazır olması gerekir.
- Raporlama verilerinin doğruluğu için randevu durum güncellemelerinin düzenli yapılması zorunludur.
- Sosyal medya bağlantılarının güncelliği salon tarafından periyodik olarak kontrol edilmelidir.
- Çakışma kontrolü yapılmadığından, operasyon ekibi bekleyen randevuları manuel değerlendirip onay sürecini yönetmekle sorumludur.
- Çakışma uyarı kayıtları denetim amacıyla saklanmalı; yöneticiler düzenli olarak potansiyel kapasite sorunlarını gözden geçirmelidir.
