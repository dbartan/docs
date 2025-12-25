# Saha Kontrol Uygulaması - Claude Code Agent Prompt

## Proje Özeti

**Saha Kontrol Uygulaması**, inşaat projelerinde QA/QC (Kalite Güvence/Kalite Kontrol) süreçlerini dijitalleştiren, saha mühendisleri ve kalite kontrol yöneticileri için tasarlanmış kapsamlı bir web uygulamasıdır.

### Teknoloji Yığını

**Frontend:**
- Next.js 14 (App Router)
- TypeScript
- Tailwind CSS
- shadcn/ui components
- React Hook Form + Zod validation
- TanStack Query (React Query)

**Backend:**
- tRPC (Type-safe API)
- Prisma ORM
- PostgreSQL
- NextAuth.js (Authentication)
- AWS S3 (File storage)

**Deployment:**
- Vercel (Frontend & Backend)
- Vercel Postgres (Database)
- Vercel Blob (File storage)

---

## Temel Özellikler (6 Ana Modül)

### 1. Proje Yönetimi
- Proje oluşturma ve düzenleme
- Proje detayları (ad, konum, müşteri, başlangıç/bitiş tarihleri)
- Proje durumu takibi (Planlama, Devam Ediyor, Tamamlandı)
- Proje ekibi yönetimi (roller: Admin, QA/QC Manager, Site Engineer)

### 2. Doküman Yönetimi
- Doküman yükleme (PDF, DWG, Excel, Word, Resim)
- Doküman kategorileri:
  - Teknik Çizimler (Architectural, Structural, MEP)
  - Teknik Özellikler
  - Kalite Planları
  - Test Prosedürleri
  - Malzeme Onayları
- Versiyon kontrolü
- Doküman arama ve filtreleme
- QR kod ile doküman erişimi

### 3. QA/QC Kontrol Listeleri (Inspections)
- Özelleştirilebilir kontrol listesi şablonları
- Kontrol kategorileri:
  - Beton Dökümü
  - Demir Donatı Kontrolü
  - Kalıp Kontrolü
  - Zemin Hazırlığı
  - MEP Tesisatları
  - Dış Cephe
- Her madde için:
  - Uygun ✅ / Uygunsuz ❌ / Uygulanamaz ⊘
  - Fotoğraf ekleme
  - Not/Yorum
- GPS konum kaydı
- Dijital imza

### 4. Uygunsuzluk Yönetimi (Defects/NCR)
- Uygunsuzluk kaydı oluşturma
- Önem derecesi: Kritik, Yüksek, Orta, Düşük
- Durum takibi: Açık, İnceleniyor, Düzeltildi, Kapalı
- Sorumlu atama
- Hedef tamamlama tarihi
- Fotoğraflar (önce/sonra)
- Düzeltici aksiyon notları
- Kapanış onayı

### 5. Raporlama ve Analiz
- Günlük saha raporları
- QA/QC özet raporları
- Uygunsuzluk istatistikleri
- Proje ilerleme raporları
- Excel/PDF export
- Grafikler ve dashboard (Chart.js / Recharts)

### 6. Kullanıcı ve Rol Yönetimi
- Kullanıcı profilleri
- Roller:
  - Super Admin (Tüm sistem erişimi)
  - QA/QC Manager (Tüm proje erişimi)
  - Site Engineer (Atanan projeler)
  - Observer (Sadece görüntüleme)
- Proje bazlı yetkilendirme

---

## Ekran Yapısı ve Ana Bileşenler

### Ana Sayfa (Dashboard)
```
/dashboard
├── Aktif Projeler Kartları
├── Son Kontroller (Recent Inspections)
├── Açık Uygunsuzluklar (Open NCRs)
└── Hızlı İşlemler (Quick Actions)
```

### Proje Detay Sayfası
```
/projects/[id]
├── Proje Bilgileri Tab
├── Dokümanlar Tab
├── Kontrol Listeleri Tab
├── Uygunsuzluklar Tab
└── Raporlar Tab
```

### Kontrol Listesi Sayfası
```
/inspections/new
├── Kontrol Tipi Seçimi
├── Dinamik Form (Checkbox grid)
├── Fotoğraf Upload
├── GPS Konum
└── İmza Paneli
```

### Uygunsuzluk Detay Sayfası
```
/defects/[id]
├── Uygunsuzluk Bilgileri
├── Fotoğraf Galerisi
├── Aksiyon Geçmişi
├── Yorum/Notlar
└── Durum Güncelleme
```

---

## Veri Modelleri (Prisma Schema)

### User Model
```typescript
model User {
  id            String    @id @default(cuid())
  email         String    @unique
  name          String
  role          Role      @default(ENGINEER)
  image         String?
  phone         String?
  title         String?   // e.g., "QA/QC Manager", "Site Engineer"
  projects      ProjectMember[]
  inspections   Inspection[]
  defects       Defect[]
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum Role {
  SUPER_ADMIN
  QAQC_MANAGER
  SITE_ENGINEER
  OBSERVER
}
```

### Project Model
```typescript
model Project {
  id            String    @id @default(cuid())
  name          String
  code          String    @unique  // Project Code: PRJ-001
  description   String?
  client        String
  location      String
  startDate     DateTime
  endDate       DateTime?
  status        ProjectStatus @default(PLANNING)
  members       ProjectMember[]
  documents     Document[]
  inspections   Inspection[]
  defects       Defect[]
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum ProjectStatus {
  PLANNING
  IN_PROGRESS
  COMPLETED
  ON_HOLD
}

model ProjectMember {
  id          String   @id @default(cuid())
  projectId   String
  userId      String
  role        String   // Project-specific role
  project     Project  @relation(fields: [projectId], references: [id])
  user        User     @relation(fields: [userId], references: [id])
  createdAt   DateTime @default(now())

  @@unique([projectId, userId])
}
```

### Document Model
```typescript
model Document {
  id            String    @id @default(cuid())
  projectId     String
  title         String
  category      DocumentCategory
  fileUrl       String
  fileType      String    // pdf, dwg, xlsx, jpg, etc.
  fileSize      Int       // in bytes
  version       String    @default("1.0")
  uploadedById  String
  qrCode        String?   // QR code for quick access
  tags          String[]  // ["structural", "foundation", "floor-1"]
  project       Project   @relation(fields: [projectId], references: [id])
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum DocumentCategory {
  ARCHITECTURAL_DRAWINGS
  STRUCTURAL_DRAWINGS
  MEP_DRAWINGS
  SPECIFICATIONS
  QUALITY_PLANS
  TEST_PROCEDURES
  MATERIAL_APPROVALS
  CERTIFICATES
  OTHER
}
```

### Inspection Model
```typescript
model Inspection {
  id            String    @id @default(cuid())
  projectId     String
  inspectorId   String
  type          InspectionType
  location      String    // e.g., "Block A - 3rd Floor"
  gpsLat        Float?
  gpsLng        Float?
  status        InspectionStatus @default(DRAFT)
  checkItems    Json      // Array of check items with results
  photos        String[]  // Array of photo URLs
  notes         String?
  signature     String?   // Digital signature image URL
  inspectedAt   DateTime  @default(now())
  project       Project   @relation(fields: [projectId], references: [id])
  inspector     User      @relation(fields: [inspectorId], references: [id])
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum InspectionType {
  CONCRETE_POURING
  REBAR_INSPECTION
  FORMWORK_CHECK
  SOIL_PREPARATION
  MEP_INSTALLATION
  FACADE_INSPECTION
  WATERPROOFING
  FINISHING_WORKS
}

enum InspectionStatus {
  DRAFT
  SUBMITTED
  APPROVED
  REJECTED
}
```

### Defect (NCR) Model
```typescript
model Defect {
  id              String    @id @default(cuid())
  projectId       String
  reportedById    String
  assignedToId    String?
  title           String
  description     String
  location        String
  severity        Severity
  status          DefectStatus @default(OPEN)
  category        String    // e.g., "Concrete", "Rebar", "MEP"
  photosBefore    String[]  // Photo URLs
  photosAfter     String[]
  dueDate         DateTime?
  closedAt        DateTime?
  correctiveAction String?
  rootCause       String?
  project         Project   @relation(fields: [projectId], references: [id])
  reportedBy      User      @relation(fields: [reportedById], references: [id])
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
}

enum Severity {
  CRITICAL
  HIGH
  MEDIUM
  LOW
}

enum DefectStatus {
  OPEN
  IN_PROGRESS
  RESOLVED
  CLOSED
  REJECTED
}
```

### Report Model
```typescript
model Report {
  id            String    @id @default(cuid())
  projectId     String
  title         String
  type          ReportType
  period        String    // e.g., "2025-01", "Q1-2025"
  content       Json      // Report data
  generatedById String
  fileUrl       String?   // PDF export URL
  project       Project   @relation(fields: [projectId], references: [id])
  createdAt     DateTime  @default(now())
}

enum ReportType {
  DAILY_SITE_REPORT
  WEEKLY_QA_SUMMARY
  MONTHLY_PROGRESS
  NCR_SUMMARY
  INSPECTION_SUMMARY
}
```

---

## API Uç Noktaları (tRPC Routes)

### Project Routes
```typescript
// src/server/api/routers/project.ts

export const projectRouter = createTRPCRouter({
  // Get all projects (with filtering)
  getAll: protectedProcedure
    .input(z.object({
      status: z.enum(['PLANNING', 'IN_PROGRESS', 'COMPLETED', 'ON_HOLD']).optional(),
    }))
    .query(async ({ ctx, input }) => {
      return await ctx.db.project.findMany({
        where: { status: input.status },
        include: { members: true },
      });
    }),

  // Get single project
  getById: protectedProcedure
    .input(z.object({ id: z.string() }))
    .query(async ({ ctx, input }) => {
      return await ctx.db.project.findUnique({
        where: { id: input.id },
        include: {
          members: { include: { user: true } },
          documents: true,
          inspections: true,
          defects: true,
        },
      });
    }),

  // Create project
  create: protectedProcedure
    .input(z.object({
      name: z.string(),
      code: z.string(),
      client: z.string(),
      location: z.string(),
      startDate: z.date(),
      endDate: z.date().optional(),
      description: z.string().optional(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.project.create({
        data: input,
      });
    }),

  // Update project
  update: protectedProcedure
    .input(z.object({
      id: z.string(),
      name: z.string().optional(),
      status: z.enum(['PLANNING', 'IN_PROGRESS', 'COMPLETED', 'ON_HOLD']).optional(),
      // ... other fields
    }))
    .mutation(async ({ ctx, input }) => {
      const { id, ...data } = input;
      return await ctx.db.project.update({
        where: { id },
        data,
      });
    }),

  // Delete project
  delete: protectedProcedure
    .input(z.object({ id: z.string() }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.project.delete({
        where: { id: input.id },
      });
    }),

  // Add team member
  addMember: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      userId: z.string(),
      role: z.string(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.projectMember.create({
        data: input,
      });
    }),
});
```

### Document Routes
```typescript
// src/server/api/routers/document.ts

export const documentRouter = createTRPCRouter({
  // Get documents by project
  getByProject: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      category: z.enum([...DocumentCategories]).optional(),
    }))
    .query(async ({ ctx, input }) => {
      return await ctx.db.document.findMany({
        where: {
          projectId: input.projectId,
          category: input.category,
        },
        orderBy: { createdAt: 'desc' },
      });
    }),

  // Upload document
  create: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      title: z.string(),
      category: z.enum([...DocumentCategories]),
      fileUrl: z.string(),
      fileType: z.string(),
      fileSize: z.number(),
      tags: z.array(z.string()).optional(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.document.create({
        data: {
          ...input,
          uploadedById: ctx.session.user.id,
        },
      });
    }),

  // Generate QR code for document
  generateQR: protectedProcedure
    .input(z.object({ documentId: z.string() }))
    .mutation(async ({ ctx, input }) => {
      // Generate QR code logic
      const qrCode = await generateQRCode(input.documentId);
      return await ctx.db.document.update({
        where: { id: input.documentId },
        data: { qrCode },
      });
    }),

  // Search documents
  search: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      query: z.string(),
    }))
    .query(async ({ ctx, input }) => {
      return await ctx.db.document.findMany({
        where: {
          projectId: input.projectId,
          OR: [
            { title: { contains: input.query, mode: 'insensitive' } },
            { tags: { has: input.query } },
          ],
        },
      });
    }),
});
```

### Inspection Routes
```typescript
// src/server/api/routers/inspection.ts

export const inspectionRouter = createTRPCRouter({
  // Get inspections by project
  getByProject: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      type: z.enum([...InspectionTypes]).optional(),
      status: z.enum(['DRAFT', 'SUBMITTED', 'APPROVED', 'REJECTED']).optional(),
    }))
    .query(async ({ ctx, input }) => {
      return await ctx.db.inspection.findMany({
        where: {
          projectId: input.projectId,
          type: input.type,
          status: input.status,
        },
        include: { inspector: true },
        orderBy: { inspectedAt: 'desc' },
      });
    }),

  // Create inspection
  create: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      type: z.enum([...InspectionTypes]),
      location: z.string(),
      checkItems: z.array(z.object({
        id: z.string(),
        label: z.string(),
        result: z.enum(['PASS', 'FAIL', 'NA']),
        notes: z.string().optional(),
        photos: z.array(z.string()).optional(),
      })),
      gpsLat: z.number().optional(),
      gpsLng: z.number().optional(),
      photos: z.array(z.string()).optional(),
      notes: z.string().optional(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.inspection.create({
        data: {
          ...input,
          inspectorId: ctx.session.user.id,
          status: 'DRAFT',
        },
      });
    }),

  // Submit inspection (change status)
  submit: protectedProcedure
    .input(z.object({
      id: z.string(),
      signature: z.string(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.inspection.update({
        where: { id: input.id },
        data: {
          status: 'SUBMITTED',
          signature: input.signature,
        },
      });
    }),

  // Approve/Reject inspection
  review: protectedProcedure
    .input(z.object({
      id: z.string(),
      status: z.enum(['APPROVED', 'REJECTED']),
      notes: z.string().optional(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.inspection.update({
        where: { id: input.id },
        data: { status: input.status },
      });
    }),
});
```

### Defect Routes
```typescript
// src/server/api/routers/defect.ts

export const defectRouter = createTRPCRouter({
  // Get defects by project
  getByProject: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      status: z.enum(['OPEN', 'IN_PROGRESS', 'RESOLVED', 'CLOSED', 'REJECTED']).optional(),
      severity: z.enum(['CRITICAL', 'HIGH', 'MEDIUM', 'LOW']).optional(),
    }))
    .query(async ({ ctx, input }) => {
      return await ctx.db.defect.findMany({
        where: {
          projectId: input.projectId,
          status: input.status,
          severity: input.severity,
        },
        include: {
          reportedBy: true,
          assignedTo: true,
        },
        orderBy: { createdAt: 'desc' },
      });
    }),

  // Create defect/NCR
  create: protectedProcedure
    .input(z.object({
      projectId: z.string(),
      title: z.string(),
      description: z.string(),
      location: z.string(),
      severity: z.enum(['CRITICAL', 'HIGH', 'MEDIUM', 'LOW']),
      category: z.string(),
      assignedToId: z.string().optional(),
      dueDate: z.date().optional(),
      photosBefore: z.array(z.string()).optional(),
    }))
    .mutation(async ({ ctx, input }) => {
      return await ctx.db.defect.create({
        data: {
          ...input,
          reportedById: ctx.session.user.id,
          status: 'OPEN',
        },
      });
    }),

  // Update defect status
  updateStatus: protectedProcedure
    .input(z.object({
      id: z.string(),
      status: z.enum(['OPEN', 'IN_PROGRESS', 'RESOLVED', 'CLOSED', 'REJECTED']),
      correctiveAction: z.string().optional(),
      photosAfter: z.array(z.string()).optional(),
    }))
    .mutation(async ({ ctx, input }) => {
      const { id, status, ...data } = input;
      return await ctx.db.defect.update({
        where: { id },
        data: {
          status,
          ...data,
          closedAt: status === 'CLOSED' ? new Date() : undefined,
        },
      });
    }),

  // Get defect statistics
  getStats: protectedProcedure
    .input(z.object({ projectId: z.string() }))
    .query(async ({ ctx, input }) => {
      const defects = await ctx.db.defect.findMany({
        where: { projectId: input.projectId },
      });

      return {
        total: defects.length,
        open: defects.filter(d => d.status === 'OPEN').length,
        inProgress: defects.filter(d => d.status === 'IN_PROGRESS').length,
        resolved: defects.filter(d => d.status === 'RESOLVED').length,
        closed: defects.filter(d => d.status === 'CLOSED').length,
        bySeverity: {
          critical: defects.filter(d => d.severity === 'CRITICAL').length,
          high: defects.filter(d => d.severity === 'HIGH').length,
          medium: defects.filter(d => d.severity === 'MEDIUM').length,
          low: defects.filter(d => d.severity === 'LOW').length,
        },
      };
    }),
});
```

---

## Kullanıcı Akışları

### Akış 1: Yeni Kontrol Listesi Oluşturma
1. Kullanıcı dashboard'dan "Yeni Kontrol" butonuna tıklar
2. Proje seçimi yapar
3. Kontrol tipi seçer (Beton Dökümü, Demir Kontrolü, vb.)
4. Lokasyon bilgisi girer
5. GPS konumunu otomatik yakalar (opsiyonel)
6. Kontrol maddelerini işaretler (✅/❌/⊘)
7. Her madde için fotoğraf ekleyebilir
8. Not/yorum ekleyebilir
9. Dijital imza ekler
10. "Gönder" butonuna basarak kaydeder
11. Sistem otomatik PDF rapor oluşturur

### Akış 2: Uygunsuzluk Kaydı ve Takibi
1. Kullanıcı uygunsuzluk tespit eder
2. "Yeni NCR" formunu açar
3. Başlık, açıklama, lokasyon girer
4. Önem derecesi seçer (Kritik/Yüksek/Orta/Düşük)
5. Kategori seçer
6. Fotoğraf ekler
7. Sorumlu kişi atar
8. Hedef tamamlama tarihi belirler
9. Kaydı oluşturur
10. Sorumlu kişiye bildirim gider
11. Sorumlu düzeltici aksiyon alır ve fotoğraf yükler
12. QA/QC Manager onaylar ve kaydı kapatır

### Akış 3: Doküman Yönetimi
1. Kullanıcı proje sayfasında "Dokümanlar" sekmesine tıklar
2. "Yükle" butonuna tıklar
3. Dosya seçer (sürükle-bırak veya dosya seçici)
4. Doküman başlığı girer
5. Kategori seçer
6. Etiketler (tags) ekler
7. Versiyon numarası girer
8. Yükle butonuna tıklar
9. Sistem dosyayı S3'e yükler
10. QR kod otomatik oluşturulur
11. Doküman listede görünür

### Akış 4: Raporlama
1. Kullanıcı "Raporlar" sayfasına gider
2. Rapor tipi seçer (Günlük/Haftalık/Aylık/NCR Özeti)
3. Tarih aralığı seçer
4. "Rapor Oluştur" butonuna tıklar
5. Sistem verileri toplar ve işler
6. Rapor önizlemesi gösterilir
7. Kullanıcı Excel veya PDF olarak indirebilir
8. Rapor kaydedilir ve geçmiş raporlar listesine eklenir

---

## Renk Paleti ve Tasarım Sistemi

### Ana Renkler
```css
--primary: #15803D (Yeşil - QA/QC Pass)
--success: #16A34A (Açık Yeşil)
--danger: #DC2626 (Kırmızı - Fail/Critical)
--warning: #F59E0B (Turuncu - Medium)
--info: #3B82F6 (Mavi - Bilgi)
--gray: #6B7280 (Gri - Neutral)
```

### Durum Renkleri
```typescript
const statusColors = {
  // Inspection Results
  PASS: 'bg-green-100 text-green-800 border-green-300',
  FAIL: 'bg-red-100 text-red-800 border-red-300',
  NA: 'bg-gray-100 text-gray-800 border-gray-300',

  // Defect Severity
  CRITICAL: 'bg-red-500 text-white',
  HIGH: 'bg-orange-500 text-white',
  MEDIUM: 'bg-yellow-500 text-white',
  LOW: 'bg-blue-500 text-white',

  // Defect Status
  OPEN: 'bg-red-100 text-red-800',
  IN_PROGRESS: 'bg-yellow-100 text-yellow-800',
  RESOLVED: 'bg-blue-100 text-blue-800',
  CLOSED: 'bg-green-100 text-green-800',

  // Project Status
  PLANNING: 'bg-purple-100 text-purple-800',
  IN_PROGRESS: 'bg-blue-100 text-blue-800',
  COMPLETED: 'bg-green-100 text-green-800',
  ON_HOLD: 'bg-gray-100 text-gray-800',
};
```

---

## Dosya Yapısı

```
saha-kontrol/
├── prisma/
│   ├── schema.prisma
│   └── seed.ts
├── public/
│   ├── images/
│   └── icons/
├── src/
│   ├── app/
│   │   ├── (auth)/
│   │   │   ├── sign-in/page.tsx
│   │   │   └── sign-up/page.tsx
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx                    # Dashboard
│   │   │   ├── projects/
│   │   │   │   ├── page.tsx                # Projects list
│   │   │   │   ├── [id]/page.tsx           # Project detail
│   │   │   │   └── new/page.tsx            # New project
│   │   │   ├── inspections/
│   │   │   │   ├── page.tsx                # Inspections list
│   │   │   │   ├── [id]/page.tsx           # Inspection detail
│   │   │   │   └── new/page.tsx            # New inspection
│   │   │   ├── defects/
│   │   │   │   ├── page.tsx                # Defects/NCR list
│   │   │   │   ├── [id]/page.tsx           # Defect detail
│   │   │   │   └── new/page.tsx            # New defect
│   │   │   ├── documents/
│   │   │   │   ├── page.tsx                # Documents library
│   │   │   │   └── [id]/page.tsx           # Document viewer
│   │   │   ├── reports/
│   │   │   │   ├── page.tsx                # Reports
│   │   │   │   └── generate/page.tsx       # Report generator
│   │   │   └── settings/
│   │   │       ├── page.tsx                # User settings
│   │   │       └── users/page.tsx          # User management
│   │   ├── api/
│   │   │   ├── auth/[...nextauth]/route.ts
│   │   │   ├── trpc/[trpc]/route.ts
│   │   │   └── upload/route.ts             # File upload endpoint
│   │   └── layout.tsx
│   ├── components/
│   │   ├── ui/                             # shadcn/ui components
│   │   ├── layout/
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Header.tsx
│   │   │   └── MobileNav.tsx
│   │   ├── projects/
│   │   │   ├── ProjectCard.tsx
│   │   │   ├── ProjectForm.tsx
│   │   │   └── ProjectStats.tsx
│   │   ├── inspections/
│   │   │   ├── InspectionForm.tsx
│   │   │   ├── ChecklistItem.tsx
│   │   │   ├── SignaturePad.tsx
│   │   │   └── InspectionCard.tsx
│   │   ├── defects/
│   │   │   ├── DefectForm.tsx
│   │   │   ├── DefectCard.tsx
│   │   │   ├── DefectTimeline.tsx
│   │   │   └── SeverityBadge.tsx
│   │   ├── documents/
│   │   │   ├── DocumentUpload.tsx
│   │   │   ├── DocumentCard.tsx
│   │   │   ├── DocumentViewer.tsx
│   │   │   └── QRCodeGenerator.tsx
│   │   ├── reports/
│   │   │   ├── ReportChart.tsx
│   │   │   ├── ReportTable.tsx
│   │   │   └── ExportButtons.tsx
│   │   └── shared/
│   │       ├── FileUpload.tsx
│   │       ├── ImageGallery.tsx
│   │       ├── DateRangePicker.tsx
│   │       ├── LoadingSpinner.tsx
│   │       └── StatusBadge.tsx
│   ├── server/
│   │   ├── api/
│   │   │   ├── root.ts
│   │   │   ├── trpc.ts
│   │   │   └── routers/
│   │   │       ├── project.ts
│   │   │       ├── document.ts
│   │   │       ├── inspection.ts
│   │   │       ├── defect.ts
│   │   │       ├── report.ts
│   │   │       └── user.ts
│   │   ├── auth.ts                         # NextAuth config
│   │   └── db.ts                           # Prisma client
│   ├── lib/
│   │   ├── utils.ts                        # Utility functions
│   │   ├── validations.ts                  # Zod schemas
│   │   ├── constants.ts                    # App constants
│   │   └── storage.ts                      # File storage helpers
│   ├── hooks/
│   │   ├── useProjects.ts
│   │   ├── useInspections.ts
│   │   ├── useDefects.ts
│   │   └── useGeolocation.ts
│   └── types/
│       └── index.ts                        # TypeScript types
├── .env.example
├── package.json
├── tailwind.config.ts
├── tsconfig.json
└── README.md
```

---

## Geliştirme Adımları (7 Faz)

### Faz 1: Proje Kurulumu ve Temel Yapı
**Görevler:**
1. Next.js projesi oluştur (`create-t3-app`)
2. Prisma schema oluştur
3. NextAuth yapılandır
4. Tailwind CSS ve shadcn/ui kur
5. Temel layout ve navigation oluştur
6. Authentication sayfaları (sign-in, sign-up)

**Tamamlanma Kriteri:** Kullanıcı giriş yapıp dashboard görebilmeli

### Faz 2: Proje Yönetimi Modülü
**Görevler:**
1. Project CRUD operations (tRPC)
2. Proje listesi sayfası
3. Proje detay sayfası
4. Proje oluşturma formu
5. Proje ekibi yönetimi
6. Proje durumu güncelleme

**Tamamlanma Kriteri:** Kullanıcı proje oluşturup ekip üyesi ekleyebilmeli

### Faz 3: Doküman Yönetimi
**Görevler:**
1. File upload API (Vercel Blob)
2. Document CRUD operations
3. Doküman yükleme UI
4. Doküman listesi ve filtreleme
5. QR kod oluşturma
6. Doküman görüntüleyici

**Tamamlanma Kriteri:** Kullanıcı doküman yükleyip QR kod oluşturabilmeli

### Faz 4: QA/QC Kontrol Listeleri
**Görevler:**
1. Inspection templates oluştur
2. Dinamik form builder
3. Kontrol listesi oluşturma UI
4. Checkbox grid component
5. Fotoğraf yükleme
6. GPS konum entegrasyonu
7. Dijital imza pad
8. Inspection submit ve approval

**Tamamlanma Kriteri:** Kullanıcı kontrol listesi oluşturup submit edebilmeli

### Faz 5: Uygunsuzluk Yönetimi
**Görevler:**
1. Defect CRUD operations
2. NCR oluşturma formu
3. Uygunsuzluk listesi ve filtreleme
4. Defect detay sayfası
5. Durum güncelleme ve aksiyon takibi
6. Fotoğraf karşılaştırma (önce/sonra)
7. Bildirim sistemi

**Tamamlanma Kriteri:** Kullanıcı NCR oluşturup takip edebilmeli

### Faz 6: Raporlama ve Analiz
**Görevler:**
1. Report generator
2. Dashboard istatistikleri
3. Grafikler (Chart.js/Recharts)
4. Excel export (xlsx library)
5. PDF export (jsPDF/React-PDF)
6. Rapor şablonları

**Tamamlanma Kriteri:** Kullanıcı rapor oluşturup export edebilmeli

### Faz 7: İleri Seviye Özellikler ve Optimizasyon
**Görevler:**
1. Offline support (PWA)
2. Push notifications
3. Advanced search
4. Bulk operations
5. Performance optimizasyonu
6. Mobile responsive iyileştirmeler
7. E2E testing (Playwright)
8. Documentation (Mintlify)

**Tamamlanma Kriteri:** Uygulama production-ready

---

## Önemli Notlar ve Best Practices

### Güvenlik
- Tüm API routes authentication gerektirmeli
- File upload size limitleri (max 10MB per file)
- File type validation (whitelist approach)
- SQL injection prevention (Prisma ORM kullanımı)
- XSS prevention (Next.js automatic escaping)
- CSRF protection (NextAuth built-in)

### Performans
- Image optimization (next/image)
- Lazy loading for lists
- Pagination (cursor-based)
- Server-side rendering where appropriate
- React Query caching
- Debounce search inputs

### UX/UI
- Loading states her işlemde
- Error handling ve user-friendly mesajlar
- Optimistic updates
- Confirmation dialogs for destructive actions
- Toast notifications (sonner)
- Mobile-first responsive design

### Code Quality
- TypeScript strict mode
- ESLint ve Prettier
- Husky pre-commit hooks
- Conventional commits
- Component composition over inheritance
- Custom hooks for reusable logic

### Testing
- Unit tests (Vitest)
- Integration tests (Playwright)
- API tests (tRPC testing utilities)
- Minimum %70 code coverage

---

## Environment Variables

```env
# Database
DATABASE_URL="postgresql://..."

# NextAuth
NEXTAUTH_SECRET="..."
NEXTAUTH_URL="http://localhost:3000"

# File Storage (Vercel Blob)
BLOB_READ_WRITE_TOKEN="..."

# Email (Optional - for notifications)
SMTP_HOST="smtp.gmail.com"
SMTP_PORT="587"
SMTP_USER="..."
SMTP_PASS="..."

# App Config
NEXT_PUBLIC_APP_URL="http://localhost:3000"
NEXT_PUBLIC_APP_NAME="Saha Kontrol"
```

---

## Kullanım Talimatları

Bu prompt'u Claude Code Agent'e vermek için:

1. **Tam Prompt:** Bu dosyanın tamamını kopyalayın
2. **Claude'a Yapıştırın:** Claude Code Agent'e yapıştırın
3. **Görev Verin:** Örnek:
   - "Proje Yönetimi modülünü tamamen uygula (Faz 2)"
   - "QA/QC Kontrol Listeleri özelliğini oluştur (Faz 4)"
   - "Tüm tRPC routers'ları yaz"
   - "Dashboard sayfasını tasarla ve kodla"

Claude Code Agent, bu detaylı prompt sayesinde:
- Tutarlı kod yazacak
- Doğru teknolojileri kullanacak
- Tasarım sistemine uygun UI oluşturacak
- TypeScript type safety sağlayacak
- Best practices'i takip edecek

---

**Hazırlayan:** Derya Bartan - QA/QC Manager
**Versiyon:** 1.0
**Tarih:** 2025-12-25
