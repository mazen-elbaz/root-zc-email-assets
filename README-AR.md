# root@ZC Email System

6 قوالب HTML بهوية root@ZC (أسود + أحمر)، الهيدر صورة الجامعة بلور واللوجو فوقها.

## الملفات
| الملف | الغرض |
|---|---|
| `preview/` | افتح أي ملف HTML بالمتصفح تشوف الشكل بداتا تجريبية (الصور محلية) |
| `templates/` | القوالب الأصلية (دي اللي بتعدل عليها النص) |
| `dist/` | القوالب الجاهزة للإرسال بعد `python build.py` |
| `assets/header.jpg` | بانر الهيدر (خلفية + لوجو) |
| `assets/logo.png` | لوجو الفوتر |
| `config.json` | اسمك، المسمى، إيميل النادي، لينكات السوشيال، رابط الصور |
| `build.py` | بيدمج الإعدادات في القوالب |
| `apps-script/Code.gs` + `sheet-template.csv` | الإرسال الجماعي من Google Sheet |

القوالب: 01 Interview Invite · 02 Slot Booking · 03 Welcome · 04 Thank-you/Rejection · 05 Event Invite · 06 Follow-up

---
## الخطوة 1: ارفع الصور على رابط HTTPS عام (إجباري)
الإيميل مش هيعرض الصور إلا لو متاحة على رابط عام.
1. اعمل حساب GitHub، وأنشئ repo عام اسمه `root-zc-email-assets`.
2. ارفع `header.jpg` و `logo.png` في الـ repo (Add file → Upload files).
3. Settings → Pages → Branch: main / root → Save.
4. بعد دقيقة، الصورة هتبقى على: `https://USERNAME.github.io/root-zc-email-assets/header.jpg`
5. افتح `config.json` وحط `ASSET_BASE_URL` = `https://USERNAME.github.io/root-zc-email-assets` (من غير سلاش في الآخر).
> لا تستخدم Google Drive ولا مسار من جهازك، الصور مش هتظهر عند المستلم.

## الخطوة 2: عدّل الإعدادات وابني
1. في `config.json` غيّر `SENDER_NAME` و `SENDER_TITLE` و `CLUB_EMAIL` و لينكات Instagram/LinkedIn.
2. شغّل: `python build.py` (محتاج Python بس، من غير مكتبات). الناتج في `dist/`.
3. للمعاينة المحلية: `python build.py --preview` ثم افتح ملفات `preview/`.

## تغيير الزرار (Call to Action)
افتح الملف من `templates/` بأي محرر نصوص وابحث عن `<!-- ... -->` أو عن `{{CONFIRM_URL}}` (أو `{{BOOKING_URL}}`، `{{WELCOME_URL}}`، `{{EVENTS_URL}}`، `{{RSVP_URL}}`، `{{REPLY_URL}}`). الزرار شكله:
```html
<a href="{{CONFIRM_URL}}" ...>CONFIRM ATTENDANCE</a>
```
- **تغيير الكلام:** غيّر `CONFIRM ATTENDANCE`.
- **تغيير الوجهة:** غيّر `{{CONFIRM_URL}}` إلى لينك ثابت (مثلًا `https://forms.gle/xxxx`)، أو سيبه placeholder وقيمته من الشيت لكل شخص.
- **زرار يفتح إيميل رد:** `mailto:root@zewailcity.edu.eg?subject=Interview%20Confirmation`
- **زرار Google Form:** اعمل Form، انسخ لينك المشاركة، وحطه مكان الـ URL.
- **حجز الأوقات (قالب 02):** استخدم Google Calendar Appointment Schedules (Create → Appointment schedule → Share → انسخ لينك الحجز) وحطه في `BOOKING_URL`.
بعد أي تعديل في `templates/` شغّل `python build.py` تاني.

## تغيير صورة الهيدر
- بدّل `assets/header.jpg` بصورة جديدة بنفس المقاس (1200×460 تقريبًا، أقل من 150KB)، وارفعها على GitHub بنفس الاسم.
- لو غيّرت الاسم، عدّل `header.jpg` داخل ملفات `templates/`.
- البلور واللوجو متدمجين في الصورة نفسها، فلو عايز خلفية تانية لازم تعمل الصورة كاملة من جديد.
- ملاحظة: Gmail بيخزّن الصور مؤقتًا، فلو استبدلتها بنفس الاسم ممكن القديمة تفضل تظهر شوية. الأفضل اسم جديد (`header-v2.jpg`).

## تعديل النصوص والـ placeholders
- أي حاجة بين `{{ }}` هي خانة بتتملا (زي `{{FIRST_NAME}}`). تقدر تضيف خانة جديدة باسم بحروف كبيرة، وتضيف عمود بنفس الاسم في الشيت.
- قاعدة: أي اسم ينتهي بـ `_URL` بيتحط كرابط، وغيره بيتحوّل لنص آمن.

---
## الإرسال (الطريقتين)
### أ) نسخ ولصق في Gmail (إرسال فردي)
1. افتح ملف من `dist/` في Chrome، املأ الـ placeholders بالتعديل في الملف (ابحث `{{` واستبدل).
2. حدد كل الصفحة (Ctrl+A) وانسخ (Ctrl+C)، افتح Compose والصق.
3. ابعت اختبار لنفسك أولًا وشوفه على الموبايل.
> الطريقة دي ممكن تبوّظ حاجات صغيرة (زوايا الزرار، المسافات). للكميات استخدم Apps Script.

### ب) Mail merge من Google Sheet (الأنسب)
1. اعمل Google Sheet جديد، واستورد `sheet-template.csv` (File → Import) أو انسخ الأعمدة. كل صف = شخص.
2. Extensions → Apps Script. الصق محتوى `Code.gs`.
3. من القايمة اليسار + → HTML. سمّي الملف بنفس اسم القالب من غير `.html` (مثل `01-interview-invite`) والصق محتوى الملف من `dist/`. كرر لكل قالب هتستخدمه.
4. احفظ، ارجع للشيت وحدّث الصفحة، هتلاقي قايمة **root@ZC Mailer**. أول مرة هيطلب صلاحيات.
5. اختار صف → **Send test of selected row to me** وراجع الإيميل.
6. بعد المراجعة → **Send all pending rows**. السكريبت بيكتب `SENT` في عمود Status ويتخطى اللي اتبعت.

الأعمدة المطلوبة لكل قالب بتتطبع لما تشغل `python build.py` (سطر "fill per recipient"). لو عمود ناقص أو فاضي السكريبت بيوقف الصف ده ويكتب ERROR بدل ما يبعت إيميل ناقص.

حدود الإرسال: حساب Gmail مجاني ~100 مستلم/يوم، Google Workspace ~1500/يوم. الإيميل بيطلع من الحساب اللي مشغّل السكريبت، فشغّله من حساب root@ZC الرسمي.

## قبل ما تبعت لأي حد
- راجع الصف: التاريخ، الوقت، المكان، والإيميل لكل شخص. ما تبعتش نفس الإيميل بتاع الانترفيو لكل الناس وتغيّر اسم اللجنة بس.
- ابعت لنفسك أولًا وافتحه على Gmail موبايل.
- الأرقام والوظائف (مثل الـ Vice President) في `config.json` لازم تتأكد منها.
