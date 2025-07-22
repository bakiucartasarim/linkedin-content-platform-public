# LinkedIn İçerik Otomasyon Platformu

Kurumsal firmalara sunulmak üzere geliştirilen LinkedIn içerik paylaşım platformu. Kullanıcılar, LinkedIn içeriklerini üretip doğrudan paylaşabilir veya zamanlayabilirler. Tüm içerik oluşturma ve paylaşım süreçleri n8n otomasyonları üzerinden çalışır.

## 🚀 Özellikler

- **4 Farklı İçerik Üretim Senaryosu:**
  - Otomatik başlatılan süreç (AI tarafından görsel ve metin önerisi)
  - Kullanıcı görsel yüklediyse (metni AI önersin)
  - Kullanıcı metin girdiyse (görseli AI önersin)
  - Sadece metin varsa (doğrudan paylaşım)

- **n8n Entegrasyonu:**
  - Tüm içerik önerileri n8n tarafından üretilir
  - LinkedIn API entegrasyonu n8n üzerinden
  - Webhook tabanlı iletişim

- **Kullanıcı Dostu Arayüz:**
  - Modern ve responsive tasarım
  - Kolay kullanımlı içerik editörü
  - Önizleme ve onay sistemi

- **Paylaşım Yönetimi:**
  - Anında yayınlama
  - Zamanlanmış paylaşım
  - Paylaşım geçmişi takibi

## 🛠️ Teknolojiler

- **Frontend:** Next.js 14 (App Router, Server Actions)
- **Backend:** Next.js API Routes + n8n webhook entegrasyonları
- **Veritabanı:** PostgreSQL + Prisma ORM
- **Kimlik Doğrulama:** NextAuth.js
- **Styling:** Tailwind CSS
- **UI Components:** Lucide React icons
- **Form Handling:** React Hook Form
- **File Upload:** React Dropzone

## 📦 Kurulum

1. **Projeyi klonlayın:**
   ```bash
   git clone <repository-url>
   cd linkedin-content-platform
   ```

2. **Bağımlılıkları yükleyin:**
   ```bash
   npm install
   ```

3. **Çevre değişkenlerini ayarlayın:**
   ```bash
   cp .env.example .env
   ```
   
   `.env` dosyasını düzenleyin:
   ```env
   DATABASE_URL=your_postgresql_connection_string
   NEXTAUTH_SECRET=your_nextauth_secret
   NEXTAUTH_URL=http://localhost:3000
   NODE_ENV=development
   ```

4. **Veritabanını hazırlayın:**
   ```bash
   npx prisma generate
   npx prisma db push
   ```

5. **Geliştirme sunucusunu başlatın:**
   ```bash
   npm run dev
   ```

## 🔧 n8n Kurulumu

1. **n8n Workflow Oluşturun:**
   - Yeni bir workflow oluşturun
   - Webhook trigger node ekleyin
   - LinkedIn API entegrasyonu için gerekli node'ları ekleyin
   - AI/GPT node'ları ile içerik üretimi yapın

2. **Webhook Konfigürasyonu:**
   - Webhook URL'sini kopyalayın
   - Auth token oluşturun
   - Platform ayarlar sayfasından webhook bilgilerini girin

3. **Gerekli API Entegrasyonları:**
   - LinkedIn API credentials
   - OpenAI/GPT API (içerik üretimi için)
   - Görsel üretimi API'leri (DALL-E, Midjourney vs.)

## 📊 Veritabanı Şeması

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  password  String
  // İlişkiler...
}

model N8nConfig {
  id         String   @id @default(cuid())
  userId     String   @unique
  webhookUrl String
  authToken  String
  // İlişkiler...
}

model ContentSession {
  id                String               @id @default(cuid())
  userId            String
  type              String // 'auto', 'image-first', 'text-first', 'text-only'
  status            ContentSessionStatus
  userInput         String?
  suggestions       String? // JSON
  finalContent      String? // JSON
  // İlişkiler...
}

model Post {
  id             String     @id @default(cuid())
  userId         String
  content        String
  imageUrl       String?
  status         PostStatus
  linkedinPostId String?
  publishedAt    DateTime?
  scheduledAt    DateTime?
  // İlişkiler...
}
```

## 🔄 İçerik Üretim Akışı

### 1. Otomatik Başlatılan Süreç
```
Platform → n8n webhook → Görsel önerisi
├── Uygun değil → Tekrar öner
└── Uygun → Metin önerisi
    ├── Uygun değil → Tekrar öner
    └── Uygun → Yayınla/zamanla
```

### 2. Kullanıcı Görsel Yüklediyse
```
Kullanıcı görsel yükler → n8n metin önerisi
├── Uygun değil → Tekrar öner
└── Uygun → Yayınla/zamanla
```

### 3. Kullanıcı Metin Girdiyse
```
Kullanıcı metin girer → n8n görsel önerisi
├── Uygun değil → Tekrar öner
└── Uygun → Yayınla/zamanla
```

### 4. Sadece Metin Varsa
```
Kullanıcı metin → Direkt yayınla/zamanla
```

## 📱 Sayfalar

- **`/auth/signin`** - Giriş sayfası
- **`/auth/signup`** - Kayıt sayfası
- **`/dashboard`** - Ana dashboard (içerik oluşturma)
- **`/settings`** - n8n webhook ayarları
- **`/history`** - Paylaşım geçmişi

## 🔌 API Endpoints

- **`POST /api/register`** - Kullanıcı kaydı
- **`POST /api/n8n`** - n8n ayarları kaydetme
- **`GET /api/n8n`** - n8n ayarları getirme
- **`POST /api/content`** - İçerik üretimi başlatma
- **`GET /api/content`** - İçerik durumu sorgulama
- **`POST /api/content/approve`** - Öneri onaylama/reddetme
- **`POST /api/content/publish`** - İçerik yayınlama/zamanlama
- **`GET /api/posts`** - Paylaşım geçmişi

## 🔒 Güvenlik

- NextAuth.js ile güvenli kimlik doğrulama
- Bcrypt ile şifre hashleme
- JWT tabanlı session yönetimi
- n8n webhook'ları için auth token koruması
- HTTPS zorunluluğu (production)

## 🚀 Production Deployment

1. **Coolify ile Deploy:**
   ```bash
   # GitHub repository'sini Coolify'a bağlayın
   # Environment variables'ları ayarlayın
   # Auto-deployment aktif edin
   ```

2. **Environment Variables (Production):**
   ```env
   DATABASE_URL=your_production_postgresql_url
   NEXTAUTH_SECRET=your_strong_secret
   NEXTAUTH_URL=https://yourdomain.com
   NODE_ENV=production
   ```

3. **Domain Setup:**
   - DNS ayarları yapın
   - SSL sertifikası aktif edin
   - Coolify üzerinden domain bağlayın

## 📝 Geliştirme Notları

- Her kullanıcı tek bir LinkedIn hesabı ile entegre olur
- LinkedIn paylaşımı doğrudan n8n üzerinden yapılır
- Her kullanıcı farklı bir n8n otomasyonu kullanabilir
- Tüm öneri süreçleri n8n otomasyonları ile yapılır
- Tüm içerik adımları onaylanmadan paylaşım yapılmaz

## 🤝 Katkıda Bulunma

1. Fork yapın
2. Feature branch oluşturun (`git checkout -b feature/amazing-feature`)
3. Commit yapın (`git commit -m 'Add amazing feature'`)
4. Branch'i push yapın (`git push origin feature/amazing-feature`)
5. Pull Request oluşturun

## 📄 Lisans

Bu proje MIT lisansı altında lisanslanmıştır.

## 📞 İletişim

Proje hakkında sorularınız için:
- Email: support@socialhub.com
- Website: https://socialhub.atalga.com

---

Made with ❤️ for corporate LinkedIn automation
