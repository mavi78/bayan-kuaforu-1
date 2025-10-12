# Requirements Quality Checklist – Bayan Kuaförü Randevu ve Yönetim Sitesi

**Purpose**: Unit tests for requirements completeness, clarity, consistency, measurability, and coverage.  
**Created**: 2025-10-13  
**Scope Focus**: Tüm spesifikasyon (fonksiyonel + veri modeli + NFR)  
**Depth**: Standart gözden geçirme  
**Audience**: İnceleme yapan ekip arkadaşları

## Requirement Completeness
- [x] CHK001 – Salon raporlama panosunda bekleyen/onaylı/iptal/override/indirim kırılımlarının tamamı gereksinim olarak listelendi mi? [Completeness, Spec §FR-017]
- [x] CHK002 – Davet sürecinde davetin süresi dolduğunda veya reddedildiğinde izlenecek alternatif adımlar dökümante edildi mi? [Completeness, Spec §FR-015, Spec §Edge Cases, Gap]
- [x] CHK003 – Randevu iptali veya yeniden planlama durumlarında hem müşteri hem personel için bildirim gereksinimleri tanımlandı mı? [Completeness, Spec §FR-004, Spec §User Story 2]
- [x] CHK004 – Telefonla randevu alırken mevcut müşteri kayıtlarıyla eşleştirme kriterleri açıkça yazıldı mı? [Completeness, Spec §User Story 2, Gap]

## Requirement Clarity
- [x] CHK005 – “Tek salon takvimi” ifadesi hangi veri kaynaklarını ve entegrasyon sınırlarını içerdiği açısından netleştirildi mi? [Clarity, Spec §Assumptions, Gap]
- [x] CHK006 – Çalışma saatleri dışındaki yönlendirme mesajının metni ve hangi iletişim kanallarını içerdiği gereksinim seviyesinde tanımlandı mı? [Clarity, Spec §FR-012, Gap]
- [x] CHK007 – SMS/e-posta/websocket bildirimlerinin tetiklendiği olaylar ve alıcı rolleri ölçülebilir şekilde listelendi mi? [Clarity, Spec §FR-004]
- [x] CHK008 – Randevu statüleriyle ilişkili iptal nedenleri ve override açıklama zorunlulukları sözlük halinde belirlendi mi? [Clarity, Spec §Clarifications, Spec §FR-031, Gap]

## Requirement Consistency
- [x] CHK009 – Web üzerinden alınan randevuların çakışma kontrolü yapılmaması ile telefon randevularında zorunlu kontrol ifadeleri arasında çelişki bulunuyor mu? [Consistency, Spec §FR-005, Spec §FR-007]
- [x] CHK010 – Override bayrağına ilişkin gereksinimler (statü akışı, raporlama, edge case maddesi) birbirleriyle tutarlı mı? [Consistency, Spec §FR-031, Spec §Edge Cases]
- [x] CHK011 – İndirim uygulama kuralları (otomatik + manuel) ile toplam tutarın sıfır altına düşmemesi koşulu uyumlu mu? [Consistency, Spec §FR-027, Spec §Edge Cases]

## Acceptance Criteria Quality
- [x] CHK012 – Başarı ölçütleri SC-001…SC-008 için ölçüm kaynağı ve doğrulama yöntemi (hangi araç/kayıt üzerinden) belirlendi mi? [Acceptance Criteria, Spec §Success Criteria, Gap]
- [x] CHK013 – User Story 1’deki “yönlendirme mesajı” senaryosunun neyin başarısını göstereceği açıkça ölçülebilir hale getirildi mi? [Acceptance Criteria, Spec §User Story 1.5, Gap]
- [x] CHK014 – User Story 2’deki çakışma onayı ve revizyon senaryoları için başarı koşulları (örn. hangi bildirim veya log istenir) net mi? [Acceptance Criteria, Spec §User Story 2, Gap]

## Scenario Coverage
- [x] CHK015 – Takip numarası hatalı olduğunda konuk müşterinin alacağı geri bildirim ve yönlendirme gereksinimleri yazıldı mı? [Scenario Coverage, Spec §FR-021, Gap]
- [x] CHK016 – Yönetici rapor ekranında geçersiz tarih aralığı veya boş sonuç durumları için gereksinimler mevcut mu? [Scenario Coverage, Spec §User Story 3, Gap]
- [x] CHK017 – Aynı randevu üzerinde eşzamanlı kullanıcı işlemlerinde (çift onay girişimi vb.) nasıl davranılacağı gereksinim olarak belirlendi mi? [Scenario Coverage, Spec §Assumptions, Gap]

## Edge Case Coverage
- [x] CHK018 – Bildirim kanallarında uzun süreli kesinti meydana geldiğinde (yeniden deneme sınırı aşıldığında) nasıl ilerlenmesi gerektiği tarif edildi mi? [Edge Case Coverage, Spec §Edge Cases, Gap]
- [x] CHK019 – Seçilen hizmetlerin toplam süresi çalışma saatini aştığında randevu talebi için beklenen davranış tanımlandı mı? [Edge Case Coverage, Spec §FR-012, Gap]
- [x] CHK020 – Davet tokenının kötüye kullanım girişimlerinde (ör. tahmin edilmesi) alınacak önlemler belirlenmiş mi? [Edge Case Coverage, Spec §FR-015, Gap]

## Non-Functional Requirements
- [x] CHK021 – Performans hedefleri (SC-001…SC-007) için izlenecek metrikler, dashboard veya alarm eşikleri gereksinim olarak eklendi mi? [Non-Functional, Spec §Success Criteria, Gap]
- [x] CHK022 – Erişilebilirlik (klavye navigasyonu, kontrast, ekran okuyucu) gereksinimleri modern tasarım hedefiyle birlikte listelendi mi? [Non-Functional, Spec §FR-023, Gap]
- [x] CHK023 – Güvenlik gereksinimleri (parola politikası, brute force koruması, davet token şifrelemesi) belgede yeterince yer alıyor mu? [Non-Functional, Spec §FR-015, Plan §Technical Context, Gap]

## Dependencies & Assumptions
- [x] CHK024 – SMS/e-posta/gerçek zamanlı bildirim sağlayıcılarının SLA ve hata modları varsayımları doğrulanıp belgelenmiş mi? [Dependencies, Spec §Assumptions]
- [x] CHK025 – Docker Compose ortamı ile üretim dağıtımı arasındaki uyumsuzluklara dair riskler ve mitigasyonlar yazıldı mı? [Dependencies, Plan §Quickstart, Gap]
- [x] CHK026 – Redis ve PostgreSQL hizmetlerinin ölçek ve yedeklilik varsayımları neler olduklarıyla birlikte belirlendi mi? [Dependencies, Spec §Assumptions, Plan §Technical Context, Gap]

## Ambiguities & Conflicts
- [x] CHK027 – “Modern ve şık tasarım” gibi nitel ifadeler ölçülebilir tasarım kriterlerine dönüştürüldü mü? [Ambiguity, Spec §FR-023, Gap]
- [x] CHK028 – Müşteri yorumlarının otomatik onayı ile yalnızca yöneticinin düzenleme/silme yetkisi arasında tutarsızlık var mı? [Ambiguity, Spec §FR-019, Spec §FR-020]
- [x] CHK029 – “Yerleşik bildirim altyapısı mevcut” varsayımı geçersiz çıkarsa alternatif süreç için gereksinim eklenmesi gerekiyor mu? [Ambiguity, Spec §Assumptions, Gap]
