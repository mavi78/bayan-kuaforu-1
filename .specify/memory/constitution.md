<!--
Sync Impact Report
- Sürüm değişimi: tanımsız → 1.0.0
- Güncellenen ilkeler: PRINCIPLE_1 yer tutucusu → I. Next.js Modülerliği; PRINCIPLE_2 yer tutucusu → II. Shadcn Arayüz Disiplini; PRINCIPLE_3 yer tutucusu → III. Türkçe Belgeleme ve İsimlendirme Tutarlılığı; PRINCIPLE_4 yer tutucusu → IV. Yetkili Dokümantasyon Temelli Geliştirme; PRINCIPLE_5 yer tutucusu → V. Kalite Kapıları ve Yapı Koruması
- Eklenen bölümler: Ek Kısıtlar ve Standartlar; Geliştirme Süreci ve Kontroller
- Kaldırılan bölümler: Yok
- Şablon güncellemeleri:
  - ✅ .specify/templates/plan-template.md
  - ✅ .specify/templates/spec-template.md
  - ✅ .specify/templates/tasks-template.md
  - ✅ .specify/templates/checklist-template.md
  - ⚠ .specify/templates/commands/ (dizin mevcut değil, doğrulama gerektiriyor)
- Takip TODO'ları: Yok
-->
# Bayan Kuaforu Constitution

## Core Principles

### I. Next.js Modülerliği
- `src/app` segment mimarisi muhafaza edilmelidir; her yeni rota `page.tsx` ve gerekiyorsa `layout.tsx` ile tanımlanır.
- Paylaşılan UI bölümleri `src/app/(components)` veya `src/app/(features)` altında konumlandırılır, alias olarak yalnızca `@/` kullanılır.
- Kodlama standartları 2 boşluk girintisi, çift tırnaklı import/string kullanımı ve görsel stiller için Tailwind yardımcı sınıflarıyla uygulanır.
Gerekçe: Bu disiplin Next.js yapısının tutarlı kalmasını sağlar ve modülerliği korur.

### II. Shadcn Arayüz Disiplini
- Tüm arayüzler shadcn bileşenleri ile inşa edilir; ham HTML etiketleri ek kodda yer almaz.
- Yeni bileşen ihtiyaçları shadcn üzerinden türetilip kompozisyon ile genişletilir, mantık katmanı UI'dan ayrılır.
- Tasarım değişikliklerinde erişilebilirlik öznitelikleri korunur ve güncellenen kullanım notları plan/spec belgelerine eklenir.
Gerekçe: shadcn standardı görsel tutarlılık ve bakım kolaylığı sağlayarak kullanıcı deneyimini güvence altına alır.

### III. Türkçe Belgeleme ve İsimlendirme Tutarlılığı
- Fonksiyon, sınıf, dosya, değişken ve sabitler TypeScript/React konvansiyonlarına göre (PascalCase bileşen, camelCase fonksiyon, SCREAMING_SNAKE sabit) adlandırılır.
- Dışa aktarılan tüm API yüzeyleri ve karmaşık iş kuralları Türkçe JSDoc açıklamalarıyla belgelenir.
- Depodaki iletişim, yorumlar ve commit açıklamaları Türkçe yazılır, kullanıcıya dönük metinlerde anlam bütünlüğü korunur.
Gerekçe: Dil ve isimlendirme tutarlılığı ekip içi iletişimi hızlandırır, bilgi kaybını engeller.

### IV. Yetkili Dokümantasyon Temelli Geliştirme
- Her geliştirme döngüsünden önce ilgili Context7 MCP dokümantasyonu alınır, referans kimliği plan/spec/tasks dosyalarında kaydedilir.
- Uygulama, bağlayıcı dokümantasyon hükümlerinden sapmaz; zorunlu sapmalar için önceden onay alınarak gerekçe belgelenir.
- Dokümantasyon yenileme tarihleri takip edilir, eski referans tespit edildiğinde çalışma durdurulup yeni sürüm aktarılır.
Gerekçe: Yetkili kaynağa bağlı kalmak gereksinimlerin güncel karşılanmasını ve teknik borcun kontrol altında tutulmasını sağlar.

### V. Kalite Kapıları ve Yapı Koruması
- `pnpm lint` ve `pnpm build` komutları her teslimde zorunlu kalite kapılarıdır; sonuçlar PR veya görev notlarına eklenir.
- Hata düzeltmeleri mevcut modüler yapıyı bozmayacak şekilde gerçekleştirilir; yapısal değişiklikler için ayrı özellik çalışması açılır.
- Her görev tamamlandığında mantıklı küçük dilimler halinde commit atılır, commit mesajları emir kipinde yazılır.
Gerekçe: Sürekli kalite kontrolü ve yapısal bütünlük uzun vadeli bakım maliyetini düşürür, geri dönüş süresini kısaltır.

## Ek Kısıtlar ve Standartlar
- **Gizlilik ve Konfigürasyon**: Tüm hassas bilgiler `.env.local` dosyasında tutulur, yeni anahtarlar PR açıklamalarında belgelenir.
- **Bağımlılıklar**: Paket yüklemeleri `pnpm` ile yapılır ve `pnpm-lock.yaml` tek doğruluk kaynağı olarak korunur.
- **Kod Stili**: Tailwind yardımcı sınıfları mantıklı kümeler halinde gruplanır, karmaşık bloklar için gerektiğinde kısa Türkçe yorumlar eklenir.
- **İletişim**: Kullanıcı belgeleri ve QA notları Türkçe hazırlanır, manuel test adımları açıkça listelenir.

## Geliştirme Süreci ve Kontroller
- **Planlama**: `/speckit.plan` çıktılarında Context7 referansı, shadcn kısıtları ve kalite kapıları kontrol listesi ile doğrulanır.
- **Özellik Belgelemesi**: `spec.md` dosyaları shadcn kullanımını, JSDoc kapsamını ve yetkili dokümantasyon referansını açıkça belirtir.
- **Görev Yönetimi**: `tasks.md` listeleri Context7 dokümantasyon arşivini, modüler sınırları ve lint/build koşullarını kapsayan işler içerir.
- **Doğrulama**: QA sırasında `pnpm lint`, `pnpm build` çıktıları ve manuel test sonuçları paylaşılır; sapmalar için yönetici onayı alınır.

## Governance
Bu anayasa depo içindeki tüm teknik ve süreçsel kararların nihai kaynağıdır; diğer belgelerde çelişki varsa anayasa hükümleri geçerlidir.

- **Değişiklik Prosedürü**: Öneriler PR üzerinden yapılır, Context7 referans etkisi ve uyumluluk analizi eklenir, proje sorumlularından onay alınır.
- **Sürüm Politikası**: MAJOR sürümde ilke kaldırma veya yeniden tanımlama, MINOR sürümde yeni ilke/bölüm ekleme veya kapsamlı genişletme, PATCH sürümde yalnızca açıklayıcı düzeltmeler yapılır.
- **Denetim**: Her plan/spec/tasks çıktısı ve PR incelemesi, ilkelerle uyumu checklist üzerinden doğrular; ihlal için ek çalışma açılması zorunludur.
- **Arşiv**: Ratifikasyon ve değişiklik tarihleri bu belgede tutulur; tüm geçmiş sürümler depo geçmişi ile izlenebilir.

**Version**: 1.0.0 | **Ratified**: 2025-10-12 | **Last Amended**: 2025-10-12
