# Data Model – Bayan Kuaförü Randevu ve Yönetim Sitesi

## Genel Notlar
- Tüm tablolar `uuid` birincil anahtar kullanır.
- Zaman alanları ISO-8601 `timestamptz`, işlemler Prisma ile yapılır.
- `created_at` ve `updated_at` alanları otomatik yönetilir; yumuşak silme gerekmiyor.
- Çok adımlı işlemler (randevu oluşturma + bildirim) tek transaksiyon içinde sıraya alınır.

## Kullanıcı (`users`)
- **Alanlar**
  - `id`: UUID (PK)
  - `email`: text, benzersiz, zorunlu
  - `hashed_password`: text, bcrypt ile oluşturulmuş (nullable – sadece davet sonrası set edilir)
  - `role`: enum (`ADMIN`, `STAFF`, `CUSTOMER`)
  - `first_name`, `last_name`: text, zorunlu
  - `phone`: text, opsiyonel, `+90` format önerisi
  - `invitation_id`: UUID, `invitations` tablosuna FK (nullable)
  - `last_login_at`: timestamptz, null olabilir
  - `status`: enum (`ACTIVE`, `SUSPENDED`)
- **İlişkiler**
  - 1:N `appointments` (müşteri veya oluşturucu)
  - 1:N `customer_notes` (personel tarafından eklenen notlar)
- **Doğrulama / İş Kuralları**
  - E-posta benzersiz, davet tamamlanana kadar doğrulama beklenir.
  - Role göre NextAuth oturum politikası; `CUSTOMER` dışındaki roller 2FA için açık genişletilebilir alan.

## Davet (`invitations`)
- **Alanlar**
  - `id`: UUID (PK)
  - `token`: text, JWT imzası ile üretilmiş, benzersiz
  - `email`: text, benzersiz davetler için
  - `role`: enum (`ADMIN`, `STAFF`, `CUSTOMER`)
  - `expires_at`: timestamptz
  - `used_at`: timestamptz, nullable
  - `created_by`: UUID, `users.id` FK
- **Doğrulama**
  - Davet sadece bir kez kullanılabilir (`used_at IS NULL` koşulu).
  - API, `expires_at` geçmişse daveti geçersiz kılar ve yöneticiyi bilgilendirir.

## Randevu (`appointments`)
- **Alanlar**
  - `id`: UUID (PK)
  - `tracking_number`: text, benzersiz
  - `customer_id`: UUID, `users.id` FK (nullable – kayıtsız müşteri)
  - `guest_first_name`, `guest_last_name`, `guest_phone`: text, konuk için zorunlu
  - `created_by_id`: UUID, `users.id` FK (müşteri, çalışan veya yönetici)
  - `status`: enum (`PENDING`, `APPROVED`, `IN_SERVICE`, `COMPLETED`, `CANCELLED`)
  - `override_flag`: boolean, varsayılan false
  - `scheduled_start`: timestamptz
  - `scheduled_end`: timestamptz
  - `notes`: text, opsiyonel
  - `created_source`: enum (`WEB_CUSTOMER`, `WEB_GUEST`, `PHONE_STAFF`, `PHONE_ADMIN`)
  - `cancellation_reason`: enum (`customer_request`, `salon_unavailable`, `no_show`, `duplicate_request`), opsiyonel
- **İlişkiler**
  - N:N `services` (ara tablo `appointment_services`)
  - 1:N `notifications` kayıtları
  - 1:N `payments`
- **Doğrulama / Kısıtlar**
  - `scheduled_end` = `scheduled_start` + hizmet toplam süresi (date-fns ile hesaplanacak).
  - `override_flag` yalnızca `APPROVED` statüsüne geçerken set edilebilir.
  - Çalışma saatleri dışındaki slotlar API düzeyinde reddedilir (müşteri için) veya override gerekir (personel).
- **Durum Geçişleri**
  - `PENDING` → `APPROVED` (personel onayı)
  - `APPROVED` → `IN_SERVICE` (hizmet başlatma)
  - `IN_SERVICE` → `COMPLETED` (hizmet bitişi + ödeme kaydı)
  - `PENDING|APPROVED|IN_SERVICE` → `CANCELLED` (iptal nedeni ile)
  - Override flag herhangi bir onaylama sırasında işaretlenir, statü değişmez.

## Hizmet (`services`)
- **Alanlar**
  - `id`: UUID (PK)
  - `name`: text (benzersiz)
  - `description`: text
  - `duration_minutes`: integer (>0)
  - `price`: numeric(10,2)
  - `is_active`: boolean
- **İlişkiler**
  - N:N `appointments` (`appointment_services`)
  - N:N `staff_assignments` (hangi personel bu hizmeti verebilir)
- **Doğrulama**
  - `duration_minutes` > 0
  - `price` ≥ 0

## Appointment Services (`appointment_services`)
- **Alanlar**
  - `appointment_id`: UUID, FK
  - `service_id`: UUID, FK
  - `position`: integer (servis sırası)
- **Doğrulama**
  - Bir randevuda aynı hizmet birden fazla kez olabilir, `position` ile sıralanır.

## Personel-Hizmet Yetkinliği (`staff_assignments`)
- **Alanlar**
  - `id`: UUID
  - `staff_id`: UUID (`users.id` – yalnızca `STAFF`/`ADMIN`)
  - `service_id`: UUID (`services.id`)
- **Kısıt**
  - Aynı kombinasyon benzersiz (`UNIQUE(staff_id, service_id)`).

## Bildirim (`notifications`)
- **Alanlar**
  - `id`: UUID
  - `appointment_id`: UUID FK
  - `channel`: enum (`SMS`, `EMAIL`, `WEBSOCKET`)
  - `status`: enum (`QUEUED`, `SENT`, `FAILED`, `RETRYING`)
  - `payload`: jsonb
  - `sent_at`: timestamptz, nullable
- **Kısıt**
  - Başarısız denemeler yeniden deneme kuyruğuna alınır (Redis).

## Yorum (`reviews`)
- **Alanlar**
  - `id`: UUID
  - `appointment_id`: UUID FK
  - `author_id`: UUID FK (`users` – müşteri)
  - `rating`: smallint (1-5)
  - `content`: text
  - `likes`: integer, varsayılan 0
  - `is_published`: boolean (otomatik true)
  - `edited_at`: timestamptz, nullable
- **Kurallar**
  - Yalnızca yönetici yorum üzerinde sonradan moderasyon yapabilir.
  - Müşteri yalnızca kendi yorumunu düzenler/siler.

## Galeri Öğesi (`gallery_items`)
- **Alanlar**
  - `id`: UUID
  - `type`: enum (`IMAGE`, `VIDEO`)
  - `title`: text
  - `description`: text
  - `url`: text
  - `display_order`: integer
  - `is_visible`: boolean

## İndirim (`discounts`)
- **Alanlar**
  - `id`: UUID
  - `name`: text
  - `type`: enum (`PERCENT`, `FIXED`)
  - `value`: numeric
  - `is_manual`: boolean (personel tarafından mı girildi)
  - `starts_at`, `ends_at`: timestamptz, nullable
  - `created_by`: UUID FK (`users`)
- **Kurallar**
  - `value` > 0, `PERCENT` için ≤100.
  - Manuel indirimler randevu tamamlama ekranında kaydedilir.

## Ödeme (`payments`)
- **Alanlar**
  - `id`: UUID
  - `appointment_id`: UUID FK
  - `method`: enum (`CASH`, `CARD`, `TRANSFER`)
  - `amount`: numeric(10,2)
  - `currency`: char(3) (varsayılan TRY)
  - `discount_total`: numeric(10,2)
  - `final_total`: numeric(10,2)
  - `paid_at`: timestamptz
- **Kurallar**
  - `final_total` ≥ 0; `amount` ≥ `final_total`.

## Müşteri Notu (`customer_notes`)
- **Alanlar**
  - `id`: UUID
  - `appointment_id`: UUID FK
  - `author_id`: UUID FK (`users`, yalnızca `STAFF`/`ADMIN`)
  - `content`: text
  - `visibility`: enum (`INTERNAL`)
  - `created_at`: timestamptz
- **Kurallar**
  - Sadece yetkili personel görebilir; müşteriye açık değildir.

## Çalışma Takvimi (`business_hours`)
- **Alanlar**
  - `id`: UUID
  - `weekday`: smallint (0-6)
  - `open_time`, `close_time`: time
  - `break_windows`: jsonb (opsiyonel)
- **Kısıt**
  - `open_time` < `close_time`.

## Özel Gün (`special_days`)
- **Alanlar**
  - `id`: UUID
  - `date`: date
  - `open_time`, `close_time`: time
  - `repeat_rule`: enum (`NONE`, `ANNUAL`)
  - `description`: text
  - `requires_confirmation`: boolean
- **Kurallar**
  - Tarih çakışması durumunda özel gün kayıtları standart saatleri geçersiz kılar.

## Audit / Log Tabloları
- `status_history` (randevu statü değişimleri, override kararı ve kullanıcı referansı)
- `login_audit` (NextAuth oturum kayıtları, IP, user-agent)

## İndeks ve Performans
- `appointments(tracking_number)` benzersiz indeks.
- `appointments(scheduled_start)` + `appointments(status)` birleşik indeks (raporlar için).
- `reviews(appointment_id)` + `reviews(author_id)` bileşik indeks.
- Redis üzerinden `rate_limit:{channel}:{phone}` anahtarları ile bildirim sınırı.
