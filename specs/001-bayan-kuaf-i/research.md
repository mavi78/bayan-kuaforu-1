# Research Notes: Bayan Kuaförü Randevu ve Yönetim Sitesi

## Test Otomasyonu Stratejisi
- **Decision**: Bileşen testleri için React Testing Library + Jest, uçtan uca senaryolar için Playwright kullanılacak; `pnpm test` ve `pnpm test:e2e` skriptleri eklenerek CI öncesi manuel olarak koşturulacak.
- **Rationale**: RTL, shadcn/tailwind bileşenleriyle iyi entegre olur ve kullanıcı odaklı etkileşimleri test etmeyi kolaylaştırır; Playwright, Next.js App Router üzerinde çok adımlı randevu akışlarını gerçek tarayıcı ortamında doğrular.
- **Alternatives considered**: Cypress (daha fazla bakım yükü ve lisans maliyeti); vitest (Jest yerine hız kazancı olsa da repo Jest kurulu değil).

## Ölçek ve Eşzamanlılık Varsayımları
- **Decision**: Sistem tek salon için günlük ~150 randevu talebini ve eşzamanlı en fazla 25 oturumu destekleyecek şekilde tasarlanacak; p95 yanıt süresi <200 ms hedeflenecek.
- **Rationale**: Tek şube işletmelerin rezervasyon hacmi bu aralığa denk düşüyor; Redis destekli oturum ve önbellekleme bu yükte yeterli, gereksiz aşırı mimariyi engelliyor.
- **Alternatives considered**: Yatay çok şube ölçeklendirme (şimdilik kapsam dışı, yönetici paneli tek salon varsayıyor); tamamen stateless mimari (bildirim/oturum ihtiyaçları nedeniyle uygun değil).

## Next.js App Router Mimari İlkeleri
- **Decision**: App Router segmentleri; sunucu bileşenleri veri alma (Prisma) için, istemci bileşenleri etkileşim için kullanılacak. Route handlers `src/app/api` altında RESTful uçlar olarak tanımlanacak.
- **Rationale**: App Router, veri getirimi ile UI katmanını ayırıp stream/SSR avantajı sağlıyor; yönetici panellerinde server actions ile formları sadeleştirmek mümkün.
- **Alternatives considered**: Pages Router (deprecated özellikler ve modülerlik kaybı); edge-only dağıtım (Prisma/NextAuth ile Node runtime ihtiyacı var).

## Prisma + PostgreSQL Tasarımı
- **Decision**: Prisma şeması, kullanıcı, davet, randevu, hizmet, yorum, indirim, ödeme, müşteri notu tablolarını ilişkisel olarak modelleyecek; benzersiz randevu takip numarası için `@unique` alan, override bayrağı için boolean sütun eklenecek.
- **Rationale**: PostgreSQL ilişkisel bütünlük, Prisma migrasyonları ise tip güvenliği sağlar; tek veritabanıyla raporlama ve transaksiyonel gereksinim karşılanır.
- **Alternatives considered**: Çoklu veritabanı (gereksiz karmaşıklık); Supabase/Hasura (mevcut altyapıya bağımlılık artar).

## NextAuth ile Kimlik ve Roller
- **Decision**: NextAuth email+şifre akışı (bcrypt ile hash) ve isteğe bağlı OAuth sağlayıcıları; davet kodları JWT ile imzalanıp 1 haftalık tek kullanımlık token olarak gönderilecek, rol ataması davet üzerinde taşınacak.
- **Rationale**: NextAuth App Router uyumlu ve oturum yönetimi için yerleşik; davet tokenı JWT ile imzalamak doğrulamayı basitleştirir.
- **Alternatives considered**: Sadece magic link (çalışan/telefon ilk girişleri için parola gerekebilir); Auth0 gibi SaaS çözümleri (maliyet ve kontrol kaybı).

## Redis Kullanımı
- **Decision**: Redis, NextAuth oturum deposu, davet token kara listesi ve bildirim kuyruğu gecikme tamponu için kullanılacak; Docker Compose içinde service olarak tanımlanacak.
- **Rationale**: Redis, düşük gecikmeli oturum saklama ve rate limit/yeniden deneme senaryoları için uygun; mevcut stack ile uyumlu.
- **Alternatives considered**: PostgreSQL tabanlı session store (yüksek IO); external cache servisi (ek bağımlılık).

## Form Yönetimi ve Doğrulama
- **Decision**: React Hook Form + Zod şema adaptörleri kullanılacak; formlar shadcn bileşenlerine bağlanacak, tarih-saat doğrulaması date-fns ile yapılacak.
- **Rationale**: RHF performanslı formlar sunar, Zod runtime doğrulaması ve tip üretimi sağlar; date-fns Türkçe locale desteği verir.
- **Alternatives considered**: Formik/Yup (daha ağır ve daha az tip güvenli); Luxon/dayjs (date-fns kadar geniş helper yok).

## Yönetici Paneli Tabloları
- **Decision**: TanStack React Table, randevu ve rapor tablolarında kullanılacak; sunucu tarafından sayfalama ve sıralama uygulanacak.
- **Rationale**: TanStack esnek kolon yapılandırması ve Next.js ile uyumlu veri getirme stratejisi sunar.
- **Alternatives considered**: Material UI DataGrid (shadcn ile uyumsuz stil); basit HTML tablo (ileri filtreleme ihtiyaçlarını karşılamaz).

## Docker ve Yerel Geliştirme
- **Decision**: Docker Compose ile Next.js app, PostgreSQL ve Redis servisleri ayağa kaldırılacak; `pnpm dev` konteyner içinde çalışacak, volume ile sıcak reload sağlanacak.
- **Rationale**: Ekip üyeleri için tekrarlanabilir ortam, PostgreSQL/Redis bağımlılıklarını kolay kurulum sağlar.
- **Alternatives considered**: Yerel servis kurulumu (platform farklılıkları); monolitik tek container (güncellenebilirlik zor).
