# Tasks: Bayan Kuaförü Randevu ve Yönetim Sitesi

**Input**: Design documents from `/specs/001-bayan-kuaf-i/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Context7 Dokümantasyonu**: Referans `/vercel/next.js` (alım: 2025-10-12) `docs/context7/vercel-nextjs-2025-10-12.md` içinde arşivlenecek ve görevlerde kaynak olarak kullanılacaktır.

**Tests**: Araştırma notlarında Playwright ve React Testing Library tercih edildi; her kullanıcı hikâyesi için ilgili test görevleri eklenmiştir ve TDD sırasına göre önce listelenmiştir.

**Organization**: Görevler kullanıcı hikâyelerine göre gruplanmıştır; her hikâye bağımsız olarak tamamlanıp test edilebilir.

## Format: `[ID] [P?] [Story] Description`
- **[P]**: Farklı dosyalarda çalışılabildiği ve bağımlılık olmadığı sürece eşzamanlı yürütülebilir.
- **[Story]**: Görevin ait olduğu kullanıcı hikâyesi (ör. US1, US2). Hikâye dışı ortak işler için `[Setup]`, `[Foundation]`, `[Polish]`.
- Her görevde ilgili dosya yolu belirtilmiştir.

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Ortak ortam hazırlığı ve dokümantasyon.

- [ ] T001 [Setup] Context7 referansını özetleyip `docs/context7/vercel-nextjs-2025-10-12.md` dosyasına kaydet.
- [ ] T002 [Setup] `.env.example` dosyasına `DATABASE_URL`, `REDIS_URL`, `NEXTAUTH_SECRET`, `NEXTAUTH_URL`, `SMS_PROVIDER_KEY`, `SMTP_HOST` alanlarını ekleyip açıklamalarını Türkçe yaz.
- [ ] T003 [Setup] `docker-compose.yml` dosyasına PostgreSQL ve Redis servislerini (persistent volume, healthcheck, ortak network) ekle.
- [ ] T004 [P] [Setup] `package.json` içine `test`, `test:e2e`, `lint`, `build`, `db:migrate`, `db:seed` komutlarını ekle ve `pnpm` script’lerini Quickstart ile hizala.

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Tüm hikâyeler başlamadan önce gerekli altyapı.

- [ ] T005 [Foundation] `prisma/schema.prisma` içinde datasource, generator, `Role`, `AppointmentStatus`, `NotificationChannel` enum’larını ve `users`, `invitations`, `services`, `business_hours` tablolarını tanımla.
- [ ] T006 [Foundation] İlk Prisma migrasyonunu (`prisma/migrations/20251013_init/`) oluşturup kullanıcı, davet, hizmet ve çalışma saatleri tablolarını veritabanına uygula.
- [ ] T007 [Foundation] Prisma istemci yardımcı dosyasını `src/lib/prisma.ts` içinde singleton desenle ve Türkçe JSDoc ile hazırla.
- [ ] T008 [Foundation] NextAuth yapılandırmasını `src/lib/auth.ts` içerisinde Prisma adapter, bcrypt hash ve davet token doğrulamasıyla kur.
- [ ] T009 [Foundation] NextAuth parola politikası (en az 12 karakter, karmaşıklık kontrolü) ve 5 başarısız giriş sonrası 15 dakikalık kilit logic’ini `src/lib/auth.ts` dosyasında uygula.
- [ ] T010 [Foundation] Redis tabanlı IP rate-limit middleware’ini `src/middleware/rate-limit.ts` dosyasında geliştir ve API route handler’larına entegre et.
- [ ] T011 [Foundation] `src/lib/redis.ts` içinde Redis istemcisi ve pub/sub yardımcı fonksiyonlarını ekle (oturum + bildirim).
- [ ] T012 [Foundation] `src/app/layout.tsx` dosyasında `SessionProvider`, `ThemeProvider` ve global Zustand store sağlayıcılarını tanımla.
- [ ] T013 [Foundation] shadcn bileşenleri için temel kompozisyonları `src/app/(components)/ui/` altında toplayan dizin yapısını ve paylaşılan stilleri oluştur.
- [ ] T014 [Foundation] Jest + React Testing Library altyapısını `jest.config.ts`, `setupTests.ts` ve `tests/unit/.gitkeep` ile yapılandır.
- [ ] T015 [Foundation] Playwright altyapısını `playwright.config.ts` ve `tests/e2e/.gitkeep` ile kur; `.github/workflows/` varsa e2e komutunu ekle.

**Checkpoint**: Prisma + NextAuth + Redis + test altyapısı hazır; kullanıcı hikâyeleri başlayabilir.

---

## Phase 3: User Story 1 – Müşterinin Web Üzerinden Randevu Oluşturması (Priority: P1) 🎯 MVP

**Goal**: Kayıtlı/kayıtsız müşterinin hizmet seçip bekleyen randevu oluşturması ve benzersiz takip numarası alması.

**Independent Test**: "Konuk veya giriş yapmış müşteri, web formunu doldurup benzersiz takip numarası aldığı bir randevuyu oluşturabilir mi?"

### Tests (write first)
- [ ] T016 [US1] Playwright senaryosunu `tests/e2e/appointments.spec.ts` dosyasında randevu oluşturma, takip numarası gösterimi ve engellenen çalışma saatleri dışı seçim akışı için yaz.

### Implementation
- [ ] T017 [US1] `prisma/schema.prisma` dosyasına `appointments`, `appointment_services`, `notifications` tablolarını ve `override_flag`, `created_source` alanlarını ekle.
- [ ] T018 [US1] Yeni tablolar için migrasyon dosyasını `prisma/migrations/20251013_appointments/` altında oluşturup çalıştır.
- [ ] T019 [P] [US1] `prisma/seed.ts` içinde varsayılan hizmetler ve standart çalışma saatleri seed verilerini ekle.
- [ ] T020 [P] [US1] Randevu formu doğrulamalarını `src/schemas/appointment.ts` dosyasında Zod ile tanımla (müşteri/konuk varyantları dahil).
- [ ] T021 [US1] Bildirim yöneticisini `src/services/notification-dispatcher.ts` dosyasında SMS/e-posta/WebSocket kanal şablonlarıyla kurgula.
- [ ] T022 [US1] Hizmet listesini dönen GET uç noktasını `src/app/api/services/route.ts` dosyasında uygula.
- [ ] T023 [US1] Müşteri randevu talebi için `src/app/api/appointments/route.ts` POST işleyicisini yaz; benzersiz takip numarası üretimi ve bildirim tetiklemeyi ekle.
- [ ] T024 [US1] Takip numarasıyla randevu sorgulayan GET uç noktasını `src/app/api/appointments/tracking/[trackingNumber]/route.ts` dosyasında uygula; geçersiz numaralar için “Takip numarası bulunamadı” cevabı ve yeniden talep bağlantısı sağlayacak hata işleyişini ekle.
- [ ] T025 [US1] Randevu formu arayüzünü `src/app/appointments/page.tsx` dosyasında shadcn bileşenleri, React Hook Form ve date-fns entegrasyonuyla geliştir.
- [ ] T026 [P] [US1] Form durumunu yöneten Zustand store’u `src/stores/appointment-form.ts` dosyasında oluştur.
- [ ] T027 [US1] Takip numarası sorgu ekranını `src/app/appointments/track/page.tsx` dosyasında uygula; başarılı oluşturma sonrası yönlendirme yap.
- [ ] T028 [US1] Randevu formu onayı sonrası `src/services/metrics.ts` servisini çağırarak SC-001 metriklerini ve `afterHoursRedirect` olaylarını kaydeden izleme katmanını `src/app/api/appointments/route.ts` ve `src/app/appointments/page.tsx` dosyalarına entegre et.
- [ ] T029 [US1] Çalışma saatlerini anonim gösteren uç noktayı `src/app/api/public/business-hours/route.ts` dosyasında sağlayıp randevu formuna entegre et.
- [ ] T030 [US1] Kayıtlı müşterilerin randevu geçmişini dönen GET uç noktasını `src/app/api/appointments/me/route.ts` dosyasında yaz; oturum doğrulaması ve sayfalama ekle.
- [ ] T031 [US1] Müşteri profil randevu geçmişi sayfasını `src/app/account/appointments/page.tsx` dosyasında oluştur; tablo bileşeni ve takip numarası bağlantıları ekle.

**Checkpoint**: Web randevusu formu, API uçları ve bildirim tetikleme uçtan uca çalışır; Playwright testi geçer.

---

## Phase 4: User Story 2 – Yönetici/Çalışanın Randevu Oluşturması ve Onay Süreci (Priority: P1)

**Goal**: Salon ekibinin telefon randevusu açması, çakışma kontrolü ve onay/override sürecini yürütmesi.

**Independent Test**: "Çalışan, telefonla gelen randevularda çakışma uyarılarını görüp onaylayabiliyor ve web üzerinden gelen bekleyen talepleri finalize edebiliyor mu?"

### Tests (write first)
- [ ] T032 [US2] Playwright senaryosunu `tests/e2e/dashboard-appointments.spec.ts` dosyasında oluştur; manuel randevu, çakışma uyarısı, override ve bildirim akışını kapsa.

### Implementation
- [ ] T033 [US2] `prisma/schema.prisma` içine `status_history` tablosunu ve çakışma karar bilgilerini ekle.
- [ ] T034 [US2] Migrasyonu `prisma/migrations/20251013_status_history/` olarak oluşturup uygula.
- [ ] T035 [P] [US2] Çakışma kontrol yardımcılarını `src/services/appointment-conflicts.ts` dosyasında geliştir (tek salon takvimi + override kaydı) ve eşzamanlı onay denemelerinde “slot_conflict” hatası verecek kilit mekanizmasını ekle.
- [ ] T036 [US2] Telefon randevusu oluşturma uç noktasını `src/app/api/appointments/manual/route.ts` dosyasında POST olarak yaz.
- [ ] T037 [US2] Randevu durum güncelleme (APPROVED/IN_SERVICE/CANCELLED/override) uç noktasını `src/app/api/appointments/[appointmentId]/route.ts` içinde PATCH ile uygula.
- [ ] T038 [US2] Bildirim yöneticisini `src/services/notification-dispatcher.ts` içinde manuel/override olaylarını kapsayacak şekilde güncelle (SMS + WebSocket yayınları).
- [ ] T039 [US2] Gerçek zamanlı bildirim akışını `src/app/api/notifications/stream/route.ts` ve `src/stores/notification-center.ts` dosyalarıyla kur (Redis pub/sub).
- [ ] T040 [US2] Yönetici/çalışan randevu panosunu `src/app/dashboard/appointments/page.tsx` dosyasında TanStack React Table ile uygula.
- [ ] T041 [P] [US2] Çakışma/override uyarı bileşenini `src/app/dashboard/appointments/components/ConflictBanner.tsx` dosyasında ekle.
- [ ] T042 [P] [US2] Panodaki hızlı işlemler için Zustand store’u `src/stores/appointment-admin.ts` dosyasında tanımla.
- [ ] T043 [US2] Telefon randevusu oluşturma ve durum değişikliklerinde `src/services/metrics.ts` servisini kullanarak SC-002 ve SC-006 metrik kayıtlarını tetikleyen izleme entegrasyonunu `src/app/api/appointments/manual/route.ts` ve `src/app/api/appointments/[appointmentId]/route.ts` dosyalarına ekle.
- [ ] T044 [US2] Müşteri eşleştirme yardımcılarını `src/services/customer-lookup.ts` dosyasında geliştir; telefon/e-posta ile eşleşme, çoklu sonuçlarda seçim ve konuk kaydı mantığını uygula.
- [ ] T045 [P] [US2] Yönetici/çalışan randevu panosunda müşteri eşleşme sonuçlarını gösteren bileşeni `src/app/dashboard/appointments/components/CustomerMatchDialog.tsx` dosyasında oluştur ve manuel randevu akışına bağla.
- [ ] T046 [US2] Randevu iptal ve override kodlarını yöneten `src/config/appointment-status.ts` modulünü ekleyip API/raporlama katmanında bu kodların zorunlu seçimini uygula.

**Checkpoint**: Telefon randevuları, çakışma kontrolleri, onay/override akışları ve gerçek zamanlı bildirimler çalışır; US2 Playwright senaryosu geçer.

---

## Phase 5: User Story 3 – Yönetici Salon Takvimi, Özel Günler ve Raporları Yönetir (Priority: P1)

**Goal**: Yönetici tek takvimi yönetir, özel günleri tanımlar, kullanıcı davetleri oluşturur ve raporları inceler.

**Independent Test**: "Yönetici paneli, salon takvimini tek kaynaktan yönetip özel gün tekrarlarını ayarlayabiliyor ve raporları görüntüleyebiliyor mu?"

### Tests (write first)
- [ ] T047 [US3] Playwright senaryosunu `tests/e2e/admin-schedule.spec.ts` dosyasında yaz; çalışma saatleri güncelleme, özel gün ekleme, davet gönderme ve rapor görüntülemeyi içer.

### Implementation
- [ ] T048 [US3] `prisma/schema.prisma` dosyasına `special_days`, `staff_assignments` ve raporlama için gerekli index’leri ekle.
- [ ] T049 [US3] Migrasyonu `prisma/migrations/20251013_special_days/` olarak oluşturup uygula.
- [ ] T050 [P] [US3] Takvim servislerini `src/services/business-hours.ts` dosyasında (standart + özel gün birleşimi) geliştir.
- [ ] T051 [P] [US3] Raporlama servislerini `src/services/reporting.ts` içinde oluştur (bekleyen/onaylı/iptal/override/indirim).
- [ ] T052 [US3] Çalışma saatleri yönetimi için `src/app/api/schedule/business-hours/route.ts` dosyasında GET/PUT işleyicilerini uygula.
- [ ] T053 [US3] Özel gün yönetimi için `src/app/api/schedule/special-days/route.ts` dosyasında POST/DELETE işleyicilerini ekle.
- [ ] T054 [US3] Davet oluşturma uç noktasını `src/app/api/invitations/route.ts` dosyasında POST olarak yaz; JWT imzalı token üretimini ekle.
- [ ] T055 [US3] Takvim yönetim arayüzünü `src/app/dashboard/schedule/page.tsx` dosyasında oluştur.
- [ ] T056 [P] [US3] Davet yönetim arayüzünü `src/app/dashboard/invitations/page.tsx` dosyasında geliştir (rol atama + durum gösterimi).
- [ ] T057 [P] [US3] Raporlama ekranını `src/app/dashboard/reports/page.tsx` dosyasında TanStack tablo + grafik bileşenleriyle kur.
- [ ] T058 [US3] Davet e-postası şablonunu `src/services/email-templates/invitation.ts` dosyasında oluştur.
- [ ] T059 [US3] Hizmet yönetimi API’sini `src/app/api/dashboard/services/route.ts` dosyasında POST/PUT/DELETE işlemleriyle geliştir; `is_active` alanı ve rol yetkilendirmesi ekle.
- [ ] T060 [P] [US3] Hizmet kataloğu yönetim arayüzünü `src/app/dashboard/services/page.tsx` dosyasında oluştur; TanStack tablo + shadcn form modalı kullan.
- [ ] T061 [P] [US3] Hizmet CRUD form bileşenini `src/app/(components)/services/ServiceForm.tsx` dosyasında geliştir ve Zod şemasıyla entegre et.
- [ ] T062 [US3] Kullanıcı rol güncelleme API’sini `src/app/api/users/[userId]/role/route.ts` dosyasında PATCH olarak uygula; Türkçe JSDoc ile yetki kontrollerini açıkla.
- [ ] T063 [P] [US3] Rol yönetim panelini `src/app/dashboard/users/page.tsx` dosyasında oluştur; davet ve mevcut kullanıcı listelerini birleştir.
- [ ] T064 [US3] Başarı kriteri metrikleri için `src/services/metrics.ts` dosyasında Prisma/Redis tabanlı ölçüm kayıt katmanını yaz.
- [ ] T065 [P] [US3] Raporlama ekranına metrik grafiklerini eklemek için `src/app/dashboard/reports/page.tsx` dosyasını SC-001…SC-006 göstergeleriyle güncelle.
- [ ] T066 [US3] Rapor ekranında geçersiz tarih aralığı veya boş sonuçlar için `EmptyState` bileşenini ekleyip önerilen tarih filtreleriyle birlikte göster; bu davranışı e2e testinde doğrula.

**Checkpoint**: Takvim yönetimi, özel günler, davetler ve raporlar tek panelde çalışır; US3 Playwright senaryosu geçer.

---

## Phase 6: User Story 4 – Hizmet Tamamlama ve Ödeme Kaydı (Priority: P1)

**Goal**: Hizmet bitişinde hizmet listesi güncellenir, indirimler uygulanır, ödeme kaydı yapılır ve personel notu eklenir.

**Independent Test**: "Bir randevu tamamlandığında hizmet listesi, indirimler ve ödeme yöntemi tek ekranda güncellenip kaydedilebiliyor mu?"

### Tests (write first)
- [ ] T067 [US4] Playwright senaryosunu `tests/e2e/payment-flow.spec.ts` dosyasında yaz; hizmet güncelleme, indirim uygulanması, ödeme kaydı ve personel notu süreçlerini kapsa.

### Implementation
- [ ] T068 [US4] `prisma/schema.prisma` dosyasına `discounts`, `payments`, `customer_notes` tablolarını ekle ve ilişkileri tanımla.
- [ ] T069 [US4] Migrasyonu `prisma/migrations/20251013_payments/` olarak oluşturup uygula.
- [ ] T070 [P] [US4] İndirim kurallarını `src/services/discounts.ts` dosyasında uygula (otomatik + manuel).
- [ ] T071 [P] [US4] Ödeme işlemlerini `src/services/payments.ts` dosyasında kaydeden servis katmanını yaz.
- [ ] T072 [US4] Randevu tamamlama uç noktasını `src/app/api/appointments/[appointmentId]/complete/route.ts` dosyasında oluştur; hizmet güncelleme, indirim hesaplama ve ödeme kaydetmeyi tek transaksiyonda yap.
- [ ] T073 [P] [US4] Manuel indirim doğrulaması için `src/app/api/discounts/validate/route.ts` dosyasında endpoint ekle.
- [ ] T074 [US4] Tamamlama ekranını `src/app/dashboard/appointments/[appointmentId]/complete/page.tsx` dosyasında shadcn formlarıyla geliştir.
- [ ] T075 [P] [US4] Form şemalarını `src/schemas/appointment-completion.ts` dosyasında tanımla.
- [ ] T076 [US4] Personel notlarını kaydeden yardımcıyı `src/services/customer-notes.ts` dosyasında oluştur ve API’ye entegre et.
- [ ] T077 [US4] Seed verilerini `prisma/seed.ts` içinde kampanya indirimleri örnekleriyle güncelle.

**Checkpoint**: Hizmet tamamlama akışı, indirim ve ödeme kayıtları çalışır; US4 Playwright senaryosu geçer.

---

## Phase 7: User Story 5 – Müşteri Deneyimi ve Etkileşim (Priority: P2)

**Goal**: Müşteriler yorum/beğeni bırakır, düzenler veya siler; galeri içerikleri görüntülenir, yönetici moderasyonu yapılır.

**Independent Test**: "Bir müşteri, aldığı hizmet için yorum bırakıp düzenleyebiliyor ve galeri içeriğine erişebiliyor mu?"

### Tests (write first)
- [ ] T078 [US5] Playwright senaryosunu `tests/e2e/reviews-gallery.spec.ts` dosyasında yaz; yorum oluşturma/düzenleme/silme ve galeri filtreleme akışını kapsa.

### Implementation
- [ ] T079 [US5] `prisma/schema.prisma` dosyasına `reviews` ve `gallery_items` tablolarını ekle; `likes`, `is_published`, `display_order` alanlarını dahil et.
- [ ] T080 [US5] Migrasyonu `prisma/migrations/20251013_reviews_gallery/` olarak oluşturup uygula.
- [ ] T081 [P] [US5] Yorum API’sini `src/app/api/reviews/route.ts` dosyasında GET/POST olarak uygula (otomatik onay).
- [ ] T082 [US5] Yorum güncelleme uç noktasını `src/app/api/reviews/[reviewId]/route.ts` dosyasında PATCH/DELETE ile ekle.
- [ ] T083 [US5] Yönetici moderasyon uç noktasını `src/app/api/reviews/[reviewId]/moderate/route.ts` dosyasında oluştur.
- [ ] T084 [P] [US5] Galeri API’sini `src/app/api/gallery/route.ts` dosyasında GET/POST olarak yaz; video/resim ayrımını sağla.
- [ ] T085 [US5] Yorum listeleme bileşenini `src/app/(components)/reviews/ReviewList.tsx` dosyasında geliştir.
- [ ] T086 [P] [US5] Yorum form bileşenini `src/app/(components)/reviews/ReviewForm.tsx` dosyasında oluştur.
- [ ] T087 [US5] Müşteri profil yorum sayfasını `src/app/account/reviews/page.tsx` dosyasında uygula.
- [ ] T088 [P] [US5] Yönetici yorum moderasyon panelini `src/app/dashboard/reviews/page.tsx` dosyasında geliştir.
- [ ] T089 [US5] Galeri sayfasını `src/app/gallery/page.tsx` dosyasında video/foto sekmeleriyle tasarla.

**Checkpoint**: Yorum ve galeri işlevleri uçtan uca çalışır; US5 Playwright senaryosu geçer.

---

## Phase 8: User Story 6 – Modern Ana Sayfa, Çalışma Saatleri ve İletişim (Priority: P3)

**Goal**: Modern, responsiv ana sayfa; çalışma saatleri, sosyal medya ve iletişim linkleri tüm cihazlarda erişilebilir.

**Independent Test**: "Farklı ekran boyutlarında ana sayfa yerleşimi bozulmadan çalışma saatleri, sosyal medya ve iletişim bağlantılarına erişiliyor mu?"

### Tests (write first)
- [ ] T090 [US6] Playwright senaryosunu `tests/e2e/homepage.spec.ts` dosyasında oluştur; mobil/masaüstü görünümü, çalışma saati bileşeni ve iletişim linklerini kapsasın.

### Implementation
- [ ] T091 [US6] Ana sayfa düzenini `src/app/page.tsx` dosyasında shadcn grid, çağrı-to-action ve dinamik veri çekimleriyle uygula; hero, hizmet öne çıkarma ve galeri önizleme bölümlerini FR-023 renk paletiyle oluştur.
- [ ] T092 [P] [US6] Hero/CTA bileşenlerini `src/app/(components)/home/HeroSection.tsx` ve `src/app/(components)/home/ServiceHighlights.tsx` dosyalarında oluştur; klavye erişilebilirliği ve görünür focus stillerini ekle.
- [ ] T093 [P] [US6] Çalışma saatleri kartını `src/app/(components)/home/HoursCard.tsx` dosyasında geliştir; `GET /api/public/business-hours` verisini kullan.
- [ ] T094 [US6] Sosyal & iletişim bağlantı konfigürasyonunu `src/config/contact.ts` dosyasında tanımlayıp ana sayfaya entegre et.
- [ ] T095 [P] [US6] Galeri önizleme bileşenini `src/app/(components)/home/GalleryPreview.tsx` dosyasında oluştur ve US5 verilerini kullan.
- [ ] T096 [US6] Responsiv stilleri `src/app/globals.css` dosyasında güncelleyerek mobil kırılım gereksinimlerini karşıla; WCAG 2.1 AA kontrast oranlarını ve medya sorgusu tab sırasını doğrula.

**Checkpoint**: Ana sayfa modern ve responsiv; US6 Playwright senaryosu geçer.

---

## Phase 9: Polish & Cross-Cutting Concerns

**Purpose**: Tüm hikâyeleri etkileyen son düzenlemeler, dokümantasyon ve kalite kapıları.

- [ ] T097 [Polish] Quickstart adımlarını teyit edip `specs/001-bayan-kuaf-i/quickstart.md` dosyasını güncel komutlarla senkronize et.
- [ ] T098 [Polish] QA notlarını `docs/QA/randevu-senaryolari.md` dosyasında Playwright senaryolarına dayalı olarak belgeye dök.
- [ ] T099 [Polish] `pnpm lint`, `pnpm build`, `pnpm test`, `pnpm test:e2e` çıktılarının raporlandığı `docs/reports/ci-checklist.md` dosyasını oluştur.
- [ ] T100 [P] [Polish] Proje kök README’sini yeni modüler dizin yapısı ve Docker komutlarıyla güncelle.
- [ ] T101 [Polish] ESLint yapılandırmasına Türkçe JSDoc zorunluluğu için kural ekleyip `pnpm lint` içinde etkinleştir; `eslint.config.mjs` ve `package.json` güncelle.
- [ ] T102 [Polish] ESLint’e shadcn bileşen beyaz listesi ve yasaklı ham HTML kullanım kuralı ekle; `eslint.config.mjs` içinde rule set’i tanımlayıp `pnpm lint` çıktısında doğrula.
- [ ] T103 [P] [Polish] `src/services/`, `src/app/` ve `src/stores/` klasörlerindeki yeni modüllerde Türkçe JSDoc açıklamalarını ekleyip doğrulama betiğini `docs/reports/javadoc-audit.md` dosyasında raporla.
- [ ] T104 [Polish] Shadcn lint kuralı ihlallerini raporlayan `docs/reports/shadcn-audit.md` dosyasını oluştur ve mimari karar kaydına referans ekle.

---

## Dependencies & Execution Order

### Phase Dependencies
- **Setup (Phase 1)** → diğer tüm fazların ön koşulu.
- **Foundational (Phase 2)** → tüm kullanıcı hikâyelerinin başlaması için zorunlu.
- **User Story Fazları** → US1, US2, US3, US4 sırasıyla P1 önceliği; US5 (P2) ve US6 (P3) tamamlayıcıdır. Her faz, önceki fazların tamamlanmasına bağlıdır.
- **Polish (Phase 9)** → tüm gerekli kullanıcı hikâyeleri tamamlandıktan sonra yürütülür.

### User Story Dependencies
- US1 → MVP; bağımsız çalışır fakat Foundational’a bağlıdır.
- US2 → US1 altyapısını (randevu tabloları, bildirim servisi) yeniden kullanır; US1 tamamlandıktan sonra başlatılması önerilir.
- US3 → Davet ve rapor işlemleri için US1/US2’de oluşturulan randevu verilerine dayanır.
- US4 → Randevu ve hizmet modellerini genişletir; US1 + US2 tamamlanmadan doğru test edilemez.
- US5 → Randevu tamamlanma verilerini (US4) ve müşteri profillerini kullanır.
- US6 → Çalışma saatleri (US3), galeri önizlemeleri (US5) ve servis listelerini (US1) kullanır.

---

## Parallel Opportunities

### US1 Parallel Sample
- Aynı anda yürütülebilir: T019 (seed güncellemesi), T020 (Zod şeması), T026 (Zustand store), T029 (public business-hours API).
- Uygulama API’si (T022–T023) tamamlandıktan sonra, T025 ve T027 ile arayüz geliştirme eşzamanlı yürütülebilir.

### US2 Parallel Sample
- T035 (çakışma servisi) ve T039 (gerçek zamanlı akış) farklı dosyalarda olduğundan paralel geliştirilebilir.
- API güncellemeleri (T036–T043) tamamlandıktan sonra, UI bileşenleri T040–T042 eşanlı rafine edilebilir.

### US3 Parallel Sample
- T045 (takvim servisi) ve T046 (rapor servisi) paralel yürütülebilir.
- Takvim/davet/rapor ve hizmet/rol arayüzleri (T047–T058) farklı dosyalarda geliştirilip daha sonra entegre edilir.

### US4 Parallel Sample
- Servis katmanı (T064–T065) tamamlandığında, form şeması T069 ile UI T068 paralel ilerleyebilir.

### US5 Parallel Sample
- API görevleri (T073–T078) tamamlandıktan sonra, bileşen görevleri (T079–T089) farklı dosyalarda paralel geliştirilebilir.

### US6 Parallel Sample
- T091, T092 ve T095 farklı bileşen dosyalarında olduğundan eşzamanlı yürütülebilir; T094 yapılandırması tamamlandıktan sonra T096 responsiv düzen entegrasyonu yapılır.

## Implementation Strategy

1. **MVP Önceliği**: Phase 1–2’yi tamamladıktan sonra US1’i teslim ederek randevu talep formunu yayına almaya odaklanın.
2. **Artımlı Teslim**: Her kullanıcı hikâyesi tamamlandığında Playwright senaryosu ve ilgili API/testler çalıştırılarak bağımsız değer sunacak şekilde deploy edin.
3. **Çapraz Ekip Çalışması**: Foundational tamamlandıktan sonra ekip üyelerini hikâyelere paylaştırarak paralel ilerleme sağlayın (ör. bir geliştirici US2, diğeri US3).
4. **Kalite Kapıları**: Her faz sonunda `pnpm lint`, `pnpm build`, Playwright ve Jest testleri çalıştırılarak raporlar `docs/reports/ci-checklist.md` dosyasına kaydedilir.
5. **Dokümantasyon**: Quickstart ve QA notları güncel tutulur; yeni bağımlılıklar Context7 referanslarıyla ilişkilendirilir.
