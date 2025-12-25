# Saha Kontrol Uygulaması - Dokümantasyon Paketi

## 📦 Paket İçeriği

Bu paket, **Saha Kontrol Uygulaması**nın tam dokümantasyonunu ve Claude Code Agent için hazır prompt'unu içerir.

### Dosya Yapısı

```
saha-kontrol-docs/
├── CLAUDE_CODE_PROMPT.md          # Ana prompt dosyası (Claude Code Agent için)
├── README.md                       # Bu dosya
├── mint.json                       # Mintlify konfigürasyonu
├── introduction.mdx                # Giriş sayfası
├── quick-start.mdx                 # Hızlı başlangıç rehberi
├── architecture-overview.mdx       # Teknik mimari
└── concepts/
    ├── projects.mdx                # Proje yönetimi
    ├── documents.mdx               # Doküman yönetimi
    ├── inspections.mdx             # QA/QC kontrol listeleri
    ├── defects.mdx                 # Uygunsuzluk yönetimi
    └── reports.mdx                 # Raporlama ve analiz
```

---

## 🚀 Hızlı Başlangıç

### Claude Code Agent ile Kullanım

1. **Ana Prompt'u Kopyalayın**
   ```bash
   cat CLAUDE_CODE_PROMPT.md
   ```

2. **Claude Code Agent'e Yapıştırın**
   - Tüm içeriği kopyalayın
   - Claude Code Agent'e yapıştırın

3. **Görev Verin**
   ```
   Örnek görevler:
   - "Proje Yönetimi modülünü uygula (Faz 2)"
   - "QA/QC Kontrol Listeleri özelliğini oluştur (Faz 4)"
   - "Dashboard sayfasını tasarla ve kodla"
   - "tRPC routers'ları yaz"
   ```

### Mintlify Dokümantasyon ile Kullanım

1. **Mintlify CLI'yi Kurun**
   ```bash
   npm install -g mintlify
   ```

2. **Dokümantasyonu Başlatın**
   ```bash
   cd saha-kontrol-docs
   mintlify dev
   ```

3. **Tarayıcıda Açın**
   ```
   http://localhost:3000
   ```

---

## 📋 Prompt Özellikleri

### CLAUDE_CODE_PROMPT.md İçeriği

✅ **Proje Özeti**
- Teknoloji yığını (Next.js, tRPC, Prisma, PostgreSQL)
- Deployment stratejisi (Vercel)

✅ **6 Ana Modül**
1. Proje Yönetimi
2. Doküman Yönetimi
3. QA/QC Kontrol Listeleri
4. Uygunsuzluk Yönetimi (NCR)
5. Raporlama ve Analiz
6. Kullanıcı ve Rol Yönetimi

✅ **Ekran Yapısı**
- Dashboard
- Proje detay sayfaları
- Kontrol listesi formu
- Uygunsuzluk detay
- Raporlama arayüzü

✅ **Veri Modelleri**
- TypeScript interfaces (User, Project, Document, Inspection, Defect, Report)
- Prisma schema
- Enum tanımları

✅ **API Uç Noktaları**
- tRPC routers (Project, Document, Inspection, Defect, Report)
- Zod validasyon şemaları
- Authorization middleware

✅ **Kullanıcı Akışları**
1. Yeni kontrol listesi oluşturma
2. Uygunsuzluk kaydı ve takibi
3. Doküman yönetimi
4. Raporlama

✅ **Renk Paleti ve Tasarım Sistemi**
- Tailwind CSS renkleri
- Durum renkleri (PASS/FAIL, Severity)
- shadcn/ui komponentleri

✅ **Dosya Yapısı**
- Detaylı klasör organizasyonu
- Component hierarchy
- Server/client structure

✅ **Geliştirme Adımları (7 Faz)**
- Faz 1: Proje kurulumu
- Faz 2: Proje yönetimi
- Faz 3: Doküman yönetimi
- Faz 4: QA/QC kontrol listeleri
- Faz 5: Uygunsuzluk yönetimi
- Faz 6: Raporlama
- Faz 7: İleri özellikler

✅ **Best Practices**
- Güvenlik önlemleri
- Performans optimizasyonu
- UX/UI prensipleri
- Code quality standards
- Testing stratejisi

---

## 📚 Mintlify Dokümantasyon İçeriği

### introduction.mdx
- Uygulamaya genel bakış
- Temel özellikler
- Kimler için tasarlandı
- Neden Saha Kontrol
- Teknoloji yığını
- Hızlı başlangıç linkleri

### quick-start.mdx
- Ön gereksinimler
- Adım adım kurulum (7 adım)
- Environment variables
- Prisma schema setup
- İlk kullanıcı oluşturma
- İlk proje oluşturma
- Sorun giderme

### architecture-overview.mdx
- Sistem mimarisi
- Teknoloji yığını detayları
- Katmanlı mimari
- Veri akışı diagramları
- Authentication flow
- File upload mimarisi
- Caching stratejisi
- Performance optimizasyonları
- Security measures
- Deployment architecture

### concepts/projects.mdx
- Proje anatomisi
- Proje durumları
- Proje oluşturma
- Ekip yönetimi
- Roller ve yetkiler
- Proje dashboard
- Filtreleme ve arama
- Best practices

### concepts/documents.mdx
- Doküman sistemi
- Desteklenen dosya formatları
- Doküman kategorileri
- Versiyon kontrolü
- Etiketleme (tagging)
- QR kod sistemi
- Doküman görüntüleme
- Arama ve filtreleme
- Best practices

### concepts/inspections.mdx
- Kontrol listesi nedir
- 8 kontrol türü
- Veri yapısı
- Kontrol listesi oluşturma (6 adım)
- Kontrol şablonları
- Fotoğraf yönetimi
- GPS lokasyon
- Dijital imza
- Onay süreci
- Best practices

### concepts/defects.mdx
- NCR nedir
- Önem dereceleri (Severity)
- Durum akışı
- NCR oluşturma
- Düzeltici aksiyon süreci
- Kök neden analizi
- Fotoğraf karşılaştırma
- Bildirimler
- İstatistikler
- Best practices

### concepts/reports.mdx
- Rapor sistemi
- 6 rapor türü
- Rapor oluşturma
- Otomatik raporlama
- Dashboard ve grafikler
- PDF export
- Excel export
- Özel raporlar
- Best practices

---

## 🎯 Kullanım Senaryoları

### Senaryo 1: Tam Uygulama Geliştirme

```
Prompt: "CLAUDE_CODE_PROMPT.md dosyasını oku ve şu adımları sırasıyla yap:

1. Faz 1 - Proje Kurulumu
2. Faz 2 - Proje Yönetimi Modülü
3. Faz 3 - Doküman Yönetimi
4. Faz 4 - QA/QC Kontrol Listeleri
5. Faz 5 - Uygunsuzluk Yönetimi
6. Faz 6 - Raporlama
7. Faz 7 - İleri Özellikler

Her fazı tamamladıktan sonra test et ve sonraki faza geç."
```

### Senaryo 2: Belirli Modül Geliştirme

```
Prompt: "CLAUDE_CODE_PROMPT.md'deki Faz 4'ü uygula:
- QA/QC Kontrol Listeleri özelliğini oluştur
- Tüm kontrol tiplerini destekle
- Fotoğraf yükleme ekle
- GPS entegrasyonu yap
- Dijital imza ekle"
```

### Senaryo 3: API Geliştirme

```
Prompt: "CLAUDE_CODE_PROMPT.md'deki tüm tRPC routers'ları yaz:
- Project router
- Document router
- Inspection router
- Defect router
- Report router

Her router için:
- CRUD operations
- Validasyon (Zod)
- Authorization checks"
```

### Senaryo 4: UI/UX Geliştirme

```
Prompt: "CLAUDE_CODE_PROMPT.md'deki tasarım sistemine göre:
- Dashboard sayfasını tasarla
- Proje detay sayfasını oluştur
- Kontrol listesi formunu yap
- shadcn/ui komponentlerini kullan
- Responsive design uygula"
```

---

## 🔧 Teknik Gereksinimler

### Minimum Gereksinimler
- Node.js 18+
- npm/pnpm/yarn
- PostgreSQL 14+
- 4GB RAM
- 10GB disk alanı

### Önerilen Gereksinimler
- Node.js 20+
- pnpm 8+
- PostgreSQL 16+
- 8GB RAM
- 20GB disk alanı

### Cloud Servisler
- **Vercel** hesabı (deployment)
- **Vercel Postgres** veya **Neon** (database)
- **Vercel Blob** veya **AWS S3** (file storage)
- **Uploadthing** hesabı (opsiyonel)

---

## 📊 Proje Metrikleri (Tahmini)

### Kod Metrikleri
- **Toplam satır**: ~15,000-20,000 LOC
- **Komponent sayısı**: ~80-100
- **API endpoint**: ~50-60
- **Veritabanı tablo**: 10

### Geliştirme Süresi (Tahmini)
- Faz 1-2: 1 hafta
- Faz 3-4: 2 hafta
- Faz 5-6: 2 hafta
- Faz 7: 1 hafta
- **Toplam**: ~6 hafta (1.5 ay)

### Performans Hedefleri
- **Initial load**: < 2s
- **API response**: < 500ms
- **PDF generation**: < 5s
- **Lighthouse score**: > 90

---

## 🎨 Tasarım Sistemi

### Renkler
```css
Primary Green: #16A34A
Success: #16A34A
Danger: #DC2626
Warning: #F59E0B
Info: #3B82F6
```

### Durum Renkleri
```typescript
PASS: green-600
FAIL: red-600
NA: gray-400
CRITICAL: red-500
HIGH: orange-500
MEDIUM: yellow-500
LOW: blue-500
```

### Tipografi
- Font: Inter (sans-serif)
- Heading: 24px, 20px, 16px, 14px
- Body: 14px
- Small: 12px

---

## 🔐 Güvenlik Önlemleri

- ✅ Input validation (Zod)
- ✅ SQL injection prevention (Prisma)
- ✅ XSS prevention (React auto-escape)
- ✅ CSRF protection (NextAuth)
- ✅ Authentication (NextAuth.js)
- ✅ Authorization (role-based)
- ✅ File upload validation
- ✅ Rate limiting

---

## 📝 Lisans

Bu proje MIT lisansı altındadır.

---

## 🤝 Katkıda Bulunma

1. Fork edin
2. Feature branch oluşturun (`git checkout -b feature/amazing-feature`)
3. Commit edin (`git commit -m 'Add amazing feature'`)
4. Push edin (`git push origin feature/amazing-feature`)
5. Pull Request açın

---

## 📞 İletişim

**Proje Sahibi:** Derya Bartan
**Email:** dbartan@gmail.com
**LinkedIn:** [linkedin.com/in/derya-bartan](https://linkedin.com/in/derya-bartan)

---

## 🙏 Teşekkürler

Bu dokümantasyon paketi, Claude Code Agent ile verimli geliştirme yapabilmeniz için hazırlanmıştır.

**Happy Coding! 🚀**
