# 📖 دليل استخدام البويلر بليت في مشاريع جديدة

> هذا الملف يشرح **كيف تستخدم هذا الـ boilerplate** كنقطة انطلاق لأي مشروع جديد.
> اقرأه كامل قبل ما تبدأ أي مشروع.

---

## 🎯 الهدف من هذا الـ Repository

هذا **template أساسي (Master Template)** — يتفضل نظيف ومستقل عن أي مشروع محدد.
كل ما تبي تبدأ مشروع جديد، استخدم GitHub "Use this template" وأنشئ repo جديد.

### ✅ قاعدة ذهبية: ما تعدّل على هذا الـ repo أبداً
كل تعديلاتك تكون في **repo المشروع الجديد**. هذا الـ repo يبقى كـ "نسخة نظيفة" للأبد.

---

## 🚀 خطوات بدء مشروع جديد (5 دقائق)

### 1️⃣ أنشئ repo جديد من الـ template

1. اذهب لـ: https://github.com/zagweb101/next-boiler-plate
2. اضغط زر **"Use this template"** الأخضر
3. اختر **"Create a new repository"**
4. اكتب اسم المشروع (مثلاً `edu-platform`, `e-commerce-app`, `clinic-system`)
5. اختر Public أو Private حسب الحاجة
6. اضغط **"Create repository"**

GitHub هينشئ repo جديد بكل الكود، **بدون git history** — نظيف تماماً.

### 2️⃣ استنسخه محلياً

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_PROJECT.git
cd YOUR_PROJECT
bun install
```

### 3️⃣ اضبط متغيرات البيئة

```bash
cp .env.example .env
```

عدّل في `.env`:

| المتغير | القيمة الافتراضية | عدّلها لـ |
|---------|------------------|----------|
| `DATABASE_URL` | `file:./db/custom.db` | اتركها للـ dev، PostgreSQL للـ prod |
| `AUTH_SECRET` | placeholder | شغّل `openssl rand -base64 32` |
| `NEXT_PUBLIC_APP_NAME` | `Next Boilerplate` | اسم مشروعك (مثلاً `منصة تعلّم`) |
| `NEXT_PUBLIC_APP_URL` | `http://localhost:3000` | رابط الإنتاج لاحقاً |

### 4️⃣ اضبط قاعدة البيانات

```bash
bun run db:generate   # يولّد Prisma Client
bun run db:push       # ينشئ الجداول في SQLite
bun run db:seed       # يضيف بيانات تجريبية + 3 مستخدمين
```

### 5️⃣ شغّل المشروع

```bash
bun run dev
```

افتح: http://localhost:3000

### 6️⃣ سجّل دخول بحساب اختبار

| النوع | الإيميل | كلمة المرور |
|------|---------|------------|
| Admin | admin@boilerplate.dev | admin12345 |
| Manager | manager@boilerplate.dev | manager12345 |
| User | user@boilerplate.dev | user12345 |

---

## 🎨 قائمة التخصيص الإجبارية (30 دقيقة)

هذي الأشياء لازم تتغير في كل مشروع جديد:

### 1. اسم التطبيق والـ branding (5 دقائق)

```bash
# .env
NEXT_PUBLIC_APP_NAME="اسم مشروعك"
NEXT_PUBLIC_APP_URL="https://yourdomain.com"
```

```tsx
// src/app/[locale]/page.tsx — غيّر:
// - العناوين (h1, h2)
// - الوصف (description)
// - الـ features اللي تعرض
// - الـ tech stack badges
```

### 2. الشعار (5 دقائق)

```bash
# استبدل:
public/logo.svg       ← شعارك الجديد
public/favicon.ico    ← أيقونة المتصفح
public/og-image.png   ← صورة المشاركة على السوشيال (1200x630)
```

### 3. الألوان والثيم (10 دقائق)

```css
/* src/app/globals.css — عدّل CSS variables */
:root {
  --primary: 220 90% 56%;   /* بدّل للون مشروعك */
  --accent: 25 95% 53%;
}
```

> ملاحظة: البويلر بليت ما يستخدم indigo أو blue (قاعدة UX). لو حبيت تعدّل، اختر ألوان تناسب هوية المشروع.

### 4. النصوص والترجمات (10 دقائق)

```json
// messages/ar.json — كل النصوص العربية
// messages/en.json — كل النصوص الإنجليزية
```

غيّر:
- `app.name`
- `app.description`
- `dashboard.title` (مثلاً "لوحة التحكم" → "لوحة الطالب")
- `auth.welcome`

### 5. إعدادات NextAuth (5 دقائق)

```typescript
// src/lib/auth.ts — عدّل:
// - صفحات الـ login/register URLs
// - مدة الـ session
// - providers (احذف Google/GitHub لو ما تحتاجهم)
```

---

## 🗃️ إضافة موديلات Prisma خاصة بالمشروع

هذا أهم جزء — كل مشروع له موديلات خاصة فيه.

### قاعدة: ما تعدّل على الـ User model الأساسي

أضف حقول جديدة لو لزم، لكن ما تحذف الحقول الموجودة (role, email, name, etc.).

### مثال: منصة تعليمية

```prisma
// prisma/schema.prisma — أضف تحت الـ User model

model Course {
  id          String   @id @default(cuid())
  title       String
  description String   @db.Text
  teacherId   String
  teacher     User     @relation(fields: [teacherId], references: [id])
  price       Decimal  @db.Decimal(10, 2) @default(0)
  level       String   @default("BEGINNER")
  status      String   @default("DRAFT")
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@index([teacherId])
}

model Enrollment {
  id          String   @id @default(cuid())
  courseId    String
  course      Course   @relation(fields: [courseId], references: [id])
  studentId   String
  student     User     @relation(fields: [studentId], references: [id])
  progress    Int      @default(0)
  createdAt   DateTime @default(now())

  @@unique([courseId, studentId])
}
```

ثم:
```bash
bun run db:push
```

### أمثلة لموديلات حسب نوع المشروع

| نوع المشروع | موديلات مقترحة |
|-------------|---------------|
| 🛒 E-commerce | Product, Category, Order, OrderItem, Cart, Review, Coupon |
| 🎓 Education | Course, Module, Lesson, Enrollment, LessonProgress, Quiz, Certificate |
| 🏥 Healthcare | Patient, Appointment, Prescription, MedicalRecord, Doctor, Clinic |
| 📈 SaaS Analytics | Project, DataSource, Dashboard, Widget, Report, ApiKey |
| 🍽️ Food Delivery | Restaurant, MenuItem, Order, Driver, Review, Coupon |
| 💼 Job Board | Company, Job, Application, Resume, Category, Skill |
| 🏠 Real Estate | Property, Agent, Listing, Inquiry, Favorite, Appointment |

---

## 🔐 تخصيص الـ RBAC

البويلر بليت فيه 3 أدوار جاهزة: `ADMIN`, `MANAGER`, `USER`.

### لتغيير الأدوار حسب مشروعك

```typescript
// src/lib/rbac.ts
export type Role = 'ADMIN' | 'TEACHER' | 'STUDENT';  // مثال للمنصة التعليمية

export const PERMISSIONS = {
  'course.create': ['TEACHER', 'ADMIN'],
  'course.publish': ['ADMIN'],
  'enroll.free': ['STUDENT', 'TEACHER', 'ADMIN'],
  'certificate.issue': ['ADMIN'],
};
```

```prisma
// prisma/schema.prisma — عدّل default role
model User {
  // ...
  role String @default("STUDENT")  // بدل "USER"
}
```

```bash
bun run db:push   # حدّث الـ schema
```

ثم حدّث الـ seed:
```typescript
// scripts/seed.ts — غيّر الأدوار
await db.user.create({ data: { email: '...', role: 'STUDENT' } });
```

---

## 🛣️ إضافة صفحات Dashboard جديدة

### هيكل المجلدات

```
src/app/[locale]/dashboard/
├── page.tsx              # Overview (موجود)
├── analytics/            # Analytics (موجود)
├── users/                # User management (موجود)
├── payments/             # Payments (موجود)
├── notifications/        # Notifications (موجود)
├── audit/                # Audit log (موجود)
├── settings/             # Settings (موجود)
└── courses/              # ← أضف صفحاتك الجديدة هنا
    ├── page.tsx          # قائمة الكورسات
    ├── [id]/
    │   └── page.tsx      # تفاصيل كورس
    └── new/
        └── page.tsx      # إنشاء كورس جديد
```

### إضافة item جديد للـ sidebar

```tsx
// src/components/dashboard/sidebar.tsx
const NAV = [
  { href: '/dashboard', label: t('dashboard.overview'), icon: LayoutDashboard },
  { href: '/dashboard/courses', label: 'الكورسات', icon: GraduationCap },  // ← أضف هذا
  { href: '/dashboard/users', label: t('dashboard.users'), icon: Users },
  // ...
];
```

### حماية الصفحة بالـ RBAC

```tsx
// src/app/[locale]/dashboard/courses/new/page.tsx
import { requireRole } from '@/lib/rbac';
import { auth } from '@/lib/auth';
import { redirect } from 'next/navigation';

export default async function NewCoursePage() {
  const session = await auth();
  if (!session) redirect('/auth/login');

  // بس TEACHER و ADMIN يقدرون
  if (!['TEACHER', 'ADMIN'].includes(session.user.role)) {
    redirect('/forbidden');
  }

  return <NewCourseForm />;
}
```

---

## 🌐 إضافة API routes

### هيكل المجلدات

```
src/app/api/
├── auth/                 # NextAuth (موجود — ما تعدّل)
├── notifications/        # (موجود)
├── users/                # (موجود)
├── payments/             # (موجود)
├── audit/                # (موجود)
├── health/               # (موجود)
└── courses/              # ← أضف APIs جديدة هنا
    ├── route.ts          # GET (list), POST (create)
    └── [id]/
        ├── route.ts      # GET, PATCH, DELETE
        └── enroll/
            └── route.ts  # POST - تسجيل في كورس
```

### نموذج API route

```typescript
// src/app/api/courses/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { db } from '@/lib/db';
import { auth } from '@/lib/auth';

// GET /api/courses — قائمة الكورسات
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url);
  const level = searchParams.get('level');

  const courses = await db.course.findMany({
    where: { status: 'PUBLISHED', ...(level && { level }) },
    include: { teacher: { select: { id: true, name: true } } },
  });

  return NextResponse.json({ items: courses });
}

// POST /api/courses — إنشاء كورس (TEACHER/ADMIN فقط)
export async function POST(req: NextRequest) {
  const session = await auth();
  if (!session) {
    return NextResponse.json({ error: 'UNAUTHORIZED' }, { status: 401 });
  }

  if (!['TEACHER', 'ADMIN'].includes(session.user.role)) {
    return NextResponse.json({ error: 'FORBIDDEN' }, { status: 403 });
  }

  const body = await req.json();
  const course = await db.course.create({
    data: { ...body, teacherId: session.user.id },
  });

  return NextResponse.json(course, { status: 201 });
}
```

---

## 💳 ربط الدفع بمنتجاتك

البويلر بليت فيه Moyasar جاهز. لربطه بمنتجاتك:

```typescript
// src/lib/payments/moyasar.ts — موجود، استخدمه كده:
import { createMoyasarPayment } from '@/lib/payments/moyasar';

async function enrollInCourse(courseId: string, studentId: string) {
  const course = await db.course.findUnique({ where: { id: courseId } });

  if (course.price === 0) {
    // كورس مجاني
    return await db.enrollment.create({
      data: { courseId, studentId },
    });
  }

  // كورس مدفوع
  const payment = await createMoyasarPayment({
    amount: Number(course.price) * 100,  // by halalas
    currency: 'SAR',
    description: `كورس: ${course.title}`,
    callbackUrl: `${process.env.NEXT_PUBLIC_APP_URL}/dashboard/courses/${courseId}`,
    metadata: { courseId, studentId },
  });

  return payment;  // فيه checkout URL للـ Moyasar
}
```

---

## 🔔 استخدام نظام الإشعارات

```typescript
import { sendNotification } from '@/lib/notifications';

// إشعار داخل التطبيق
await sendNotification({
  userId: student.id,
  channel: 'IN_APP',
  title: 'تم نشر درس جديد',
  body: `درس "${lesson.title}" متاح الآن في ${course.title}`,
});

// إيميل
await sendNotification({
  userId: student.id,
  channel: 'EMAIL',
  title: 'تذكير: اختبار غداً',
  body: 'لا تنسَ الاختبار في كورس X',
});

// Push notification
await sendNotification({
  userId: student.id,
  channel: 'PUSH',
  title: 'خصم 50%!',
  body: 'لفترة محدودة على الكورس Y',
});
```

---

## 🌍 إضافة لغة جديدة

البويلر بليت فيه عربي + إنجليزي. لإضافة لغة ثالثة:

### 1. أضف ملف ترجمات

```bash
cp messages/en.json messages/fr.json   # مثلاً فرنسي
# ترجم المحتوى
```

### 2. حدّث إعدادات i18n

```typescript
// src/i18n/routing.ts
export const routing = defineRouting({
  locales: ['ar', 'en', 'fr'],  // ← أضف 'fr'
  defaultLocale: 'ar',
});
```

### 3. أضف اللغة للـ switcher

```tsx
// src/components/dashboard/topbar.tsx
function toggleLocale() {
  const locales = ['ar', 'en', 'fr'];
  const idx = locales.indexOf(locale);
  const newLocale = locales[(idx + 1) % locales.length];
  // ...
}
```

---

## 🚀 النشر على Railway

### قبل النشر، تأكد من:

- [ ] عدّلت `NEXT_PUBLIC_APP_NAME` في `.env`
- [ ] غيّرت الـ logo في `public/logo.svg`
- [ ] حدّثت `messages/ar.json` و `messages/en.json`
- [ ] أضفت موديلات Prisma الخاصة بمشروعك
- [ ] شغّلت `bun run db:push` و `bun run db:seed`
- [ ] اختبرت كل الـ flows (login, register, dashboard)
- [ ] شغّلت `bun run lint` و `bun run test`

### خطوات النشر

1. **ارفع المشروع على GitHub** (لو ما رفعته)
2. **اذهب لـ** https://railway.app/new
3. **اختر** "Deploy from GitHub repo" → اختر repo مشروعك
4. **أضف** PostgreSQL + Redis plugins
5. **اضبط متغيرات البيئة** (في Railway Variables tab):

   | المتغير | القيمة |
   |---------|--------|
   | `AUTH_SECRET` | شغّل `openssl rand -base64 32` |
   | `AUTH_URL` | `https://YOUR-APP.up.railway.app` |
   | `NEXT_PUBLIC_APP_NAME` | اسم مشروعك |
   | `NEXT_PUBLIC_APP_URL` | `https://YOUR-APP.up.railway.app` |
   | `NEXT_PUBLIC_DEFAULT_LOCALE` | `ar` |

6. **انتظر الـ build** (3-5 دقائق)
7. **شغّل الـ migration** من Railway Shell:
   ```bash
   bun run db:push && bun run db:seed
   ```

---

## 🆘 مشاكل شائعة وحلولها

### "Cannot find module '@/lib/db'"
- تأكد إنك شغّلت `bun install`
- أعد تشغيل الـ dev server

### "Prisma Client not generated"
```bash
bun run db:generate
```

### "AUTH_SECRET is required"
```bash
echo "AUTH_SECRET=$(openssl rand -base64 32)" >> .env
```

### "Login not working on production"
- تأكد إن `AUTH_URL` = رابط الإنتاج (وليس localhost)
- في Google/GitHub OAuth، حدّث callback URLs

### "Moyasar webhook not received"
- أضف webhook URL في Moyasar Dashboard: `https://your-app.com/api/payments/webhook`
- تأكد إن `MOYASAR_WEBHOOK_SECRET` مظبوط

### "Database connection failed"
- في dev: تأكد إن `db/custom.db` موجود ومش للقراءة فقط
- في prod: تأكد إن `DATABASE_URL` من Railway PostgreSQL

### "Build fails on Railway"
- شغّل `bun run build` محلياً وتأكد إنه ينجح
- راجع logs في Railway
- تأكد إن `output: 'standalone'` موجود في `next.config.ts`

---

## 📋 Checklist قبل تسليم المشروع

### Branding
- [ ] اسم التطبيق في `.env` و `messages/*.json`
- [ ] الشعار في `public/logo.svg`
- [ ] أيقونة المتصفح في `public/favicon.ico`
- [ ] صورة المشاركة `public/og-image.png`
- [ ] الألوان في `src/app/globals.css`

### المحتوى
- [ ] النصوص في `messages/ar.json` و `messages/en.json`
- [ ] صفحة الـ landing `src/app/[locale]/page.tsx`
- [ ] عناصر الـ sidebar `src/components/dashboard/sidebar.tsx`

### الكود
- [ ] موديلات Prisma الخاصة بالمشروع
- [ ] API routes الجديدة
- [ ] صفحات الـ dashboard الجديدة
- [ ] حماية الـ routes بالـ RBAC

### الاختبار
- [ ] `bun run lint` ينجح
- [ ] `bun run test` ينجح
- [ ] `bun run build` ينجح
- [ ] كل الـ flows شغّالة (login, dashboard, payments)

### النشر
- [ ] رفع على GitHub
- [ ] ربط مع Railway
- [ ] ضبط متغيرات البيئة
- [ ] تشغيل `db:push` و `db:seed`
- [ ] اختبار على رابط الإنتاج

---

## 💡 نصائح مهمة

### 1. احتفظ بنسخة نظيفة من الـ template
لا تدمج تعديلات المشروع في الـ template الأصلي (`zagweb101/next-boiler-plate`).
كل مشروع له repo مستقل.

### 2. حدّث الـ template فقط بالميزات العامة
لو لقيت bug أو أضفت ميزة تنفع كل المشاريع، حدّث الـ template نفسه:
```bash
git clone https://github.com/zagweb101/next-boiler-plate.git
# عدّل
git push origin main
```

### 3. استخدم branches في المشاريع الكبيرة
```bash
git checkout -b feature/courses
# ... تطوير ...
git push -u origin feature/courses
# اعمل PR و merge لـ main
```

### 4. سجّل dependencies الجديدة
كل ما تضف package جديد، حدّث `package.json`:
```bash
bun add @react-pdf/renderer  # مثلاً لتوليد الشهادات
```

### 5. خذ backups دورية من قاعدة البيانات
في Railway:
- اذهب لـ PostgreSQL service
- تبويب "Data" → "Backup"
- نزّل الـ backup أسبوعياً

---

## 🎓 أمثلة عملية لاستخدام الـ template

### مثال 1: منصة تعليمية (edu-platform)
- **موديلات:** Course, Module, Lesson, Enrollment, Quiz, Certificate
- **أدوار:** ADMIN, TEACHER, STUDENT
- **صفحات:** /dashboard/courses, /dashboard/teach, /dashboard/certificates
- **دفع:** Moyasar لرسوم الكورسات

### مثال 2: متجر إلكتروني (e-commerce)
- **موديلات:** Product, Category, Order, OrderItem, Cart, Review
- **أدوار:** ADMIN, SELLER, CUSTOMER
- **صفحات:** /dashboard/products, /dashboard/orders, /dashboard/reports
- **دفع:** Moyasar لكل الطلبات

### مثال 3: نظام حجوزات عيادة (clinic-system)
- **موديلات:** Patient, Doctor, Appointment, Prescription, MedicalRecord
- **أدوار:** ADMIN, DOCTOR, RECEPTIONIST, PATIENT
- **صفحات:** /dashboard/appointments, /dashboard/patients, /dashboard/prescriptions
- **إشعارات:** تذكير بالمواعيد عبر SMS + email

### مثال 4: SaaS تحليلات (analytics-saas)
- **موديلات:** Project, DataSource, Dashboard, Widget, Report
- **أدوار:** ADMIN, OWNER, MEMBER, VIEWER
- **صفحات:** /dashboard/projects, /dashboard/dashboards, /dashboard/integrations
- **اشتراكات:** شهري/سنوي عبر Moyasar

---

## 📞 لما تحتاج مساعدة

- **مرجع البويلر بليت:** https://github.com/zagweb101/next-boiler-plate
- **الوثائق:** `docs/SETUP.md`, `docs/DEPLOYMENT.md`, `docs/ARCHITECTURE.md`
- **متغيرات البيئة:** `.env.example` (كل متغير موثّق)
- **APIs:** راجع `src/app/api/` للأمثلة
- **اختبارات:** `tests/unit/` و `tests/e2e/`

---

## ✨ خلاصة

1. هذا الـ template = نقطة انطلاق نظيفة
2. كل مشروع = repo مستقل من "Use this template"
3. عدّل branding + أضف موديلات + ابني صفحاتك
4. اختبار → نشر على Railway → استلام
5. **ما تلمس الـ template الأصلي** أبداً (إلا للتحديثات العامة)

**موفق في مشاريعك! 🚀**
