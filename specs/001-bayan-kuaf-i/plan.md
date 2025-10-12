# Implementation Plan: Bayan Kuaförü Randevu ve Yönetim Sitesi

**Branch**: `001-bayan-kuaf-i` | **Date**: 2025-10-13 | **Spec**: [specs/001-bayan-kuaf-i/spec.md](specs/001-bayan-kuaf-i/spec.md)
**Input**: Feature specification from `/specs/001-bayan-kuaf-i/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Modern, modüler Next.js randevu sistemi; kayıtlı/kayıtsız müşteri randevuları, yönetici/çalışan onayı, davet tabanlı kullanıcı kayıtları ve bildirim hattı (SMS/e-posta/websocket) tek salon takvimi üzerinden yönetilecek. Teknik yaklaşım: Next.js 14 App Router, Prisma + PostgreSQL, Redis destekli oturum/önbellek katmanı, NextAuth tabanlı rol yönetimi, shadcn UI, tanstack/react-table ve Zustand ile yönetim panelleri.

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: TypeScript (Next.js 14 App Router, Node 18)  
**Primary Dependencies**: Next.js, Prisma ORM, NextAuth, shadcn/ui, Zustand, TanStack React Table, React Hook Form, Zod, date-fns  
**Storage**: PostgreSQL (primary), Redis (sessions/cache), Prisma migrations  
**Testing**: React Testing Library + Jest (bileşen), Playwright (e2e), manuel QA destekleyici  
**Target Platform**: Web (SSR/ISR), Docker tabanlı dağıtım, Vercel/Node 18 uyumlu  
**Project Type**: Tek Next.js web uygulaması (App Router)  
**Performance Goals**: Bildirimler ≤30 sn, randevu formu tamamlama ≤3 dk, yönetici iş akışları ≤2 dk (spec SC-001..SC-007)  
**Constraints**: shadcn bileşen zorunluluğu, 2 boşluk/çift tırnak kod stili, davet süresi 1 hafta, çevrimdışı ödeme, harici public API yayını yok (yalnızca iç route handler’lar), metrik toplama Next.js analytics + Prisma/Redis logları ile yapılacak, NextAuth parola politikası ve Redis tabanlı rate-limit zorunluluğu  
**Scale/Scope**: Tek salon, günlük ~150 randevu talebi, eşzamanlı ≤25 aktif oturum

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [x] Context7 MCP sunucusundan en güncel dokümantasyon getirildi ve plan notlarında referanslandı (Next.js `/vercel/next.js`, 2025-10-12).
- [x] UI bileşenleri yalnızca shadcn kütüphanesi ile kurgulanacak; ham HTML etiketleri plan kapsamına alınmadı.
- [x] `src/app` segment yapısı korunuyor ve tüm yeni yollar `page.tsx`/`layout.tsx` ile tanımlanıyor; `@/` alias kullanımı planlandı.
- [x] Tüm fonksiyonlar/sınıflar için Türkçe JSDoc ve standart isimlendirme görevleri planlandı.
- [x] Teslim öncesi `pnpm lint` ve `pnpm build` çalıştırmaları ile yapı bütünlüğü koruma adımları plana eklendi.

## Project Structure

### Documentation (this feature)

```
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)
<!--
  ACTION REQUIRED: Replace the placeholder tree below with the concrete layout
  for this feature. Delete unused options and expand the chosen structure with
  real paths (e.g., apps/admin, packages/something). The delivered plan must
  not include Option labels.
-->

```
src/
├── app/
│   ├── (components)/        # Paylaşılan shadcn tabanlı UI parçaları
│   ├── (features)/          # Randevu, davet, raporlama modülleri
│   ├── api/                 # Route handlers (Next.js App Router)
│   ├── dashboard/           # Yönetici/çalışan panelleri
│   ├── appointments/        # Müşteri randevu akışları
│   ├── auth/                # NextAuth sayfaları
│   └── layout.tsx           # Global düzen
├── lib/                     # Prisma client, cache, auth yardımcıları
├── stores/                  # Zustand durum yapıları
├── schemas/                 # Zod doğrulama şemaları
├── services/                # Domain servis katmanı (bildirim, raporlama)
├── hooks/                   # React Hook Form entegrasyonları
└── utils/                   # date-fns yardımcıları ve formatlayıcılar

prisma/
├── schema.prisma
└── migrations/

contracts/                  # OpenAPI/GraphQL taslakları

tests/
├── unit/
├── integration/
└── e2e/                    # Playwright (planlanan)
```

**Structure Decision**: Tek Next.js projesi içinde App Router segmentleriyle modülerlenen yapı; domain servisleri `src/services`, durum yönetimi `src/stores`, tip/doğrulama `src/schemas` altında tutulacak. Prisma ve migrasyonlar kök `prisma/` dizininde, planlanan testler `tests/` altında gruplanacak.

## Complexity Tracking

*Fill ONLY if Constitution Check has violations that must be justified*

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| Yok | — | — |
