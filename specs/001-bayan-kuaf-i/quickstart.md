# Quickstart – Bayan Kuaförü Randevu ve Yönetim Sitesi

## Önkoşullar
- Node.js 18 LTS
- pnpm 9+
- Docker & Docker Compose

## Kurulum Adımları
1. Bağımlılıkları kurun:
   ```bash
   pnpm install
   ```
2. Ortam değişkenlerini oluşturun:
   - `.env.example` dosyasını `.env.local` olarak kopyalayın.
   - PostgreSQL, Redis, NextAuth (SECRET, NEXTAUTH_URL) ve SMTP/SMS sağlayıcı anahtarlarını doldurun.
3. Docker servislerini ayağa kaldırın:
   ```bash
   docker compose up -d postgres redis
   ```
4. Prisma migrasyonlarını uygulayın ve seed verisini yükleyin:
   ```bash
   pnpm prisma migrate deploy
   pnpm prisma db seed
   ```
5. Geliştirme sunucusunu başlatın:
   ```bash
   pnpm dev
   ```
   - Uygulama: http://localhost:3000
   - Storybook/shadcn katalogu planlanıyorsa ilgili komutu burada belirtin.

## Test ve Doğrulama
- Birim testleri:
  ```bash
  pnpm test
  ```
- Uçtan uca testler:
  ```bash
  pnpm test:e2e
  ```
- Kalite kapıları:
  ```bash
  pnpm lint
  pnpm build
  ```

## Yardımcı Komutlar
- Prisma Studio:
  ```bash
  pnpm prisma studio
  ```
- Docker servislerini durdurma:
  ```bash
  docker compose down
  ```

## Rol Tabanlı Giriş
- Yönetici/Çalışan hesapları davet kodu ile oluşturulur (`/api/invitations`).
- İlk kullanıcıyı yaratmak için `pnpm prisma db seed` içerisinde yönetici daveti üretin.
