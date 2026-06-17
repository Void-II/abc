# Repository Structure and Contents

## Directory Tree

```
.
├── .github
│   └── workflows
│       ├── build.yml
│       └── dump.yml
├── .gitignore
├── LICENSE
├── README.md
├── _worker.js
├── build.js
├── dist
│   └── worker.js
├── index.html
├── index.js
├── package.json
└── wrangler.toml

```

## File Contents

### build.js

```js
import { readFileSync, writeFileSync, mkdirSync } from 'fs';
import { join, dirname } from 'path';
import { fileURLToPath } from 'url';
import { build } from 'esbuild';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

async function runBuild() {
  console.log("🐾 Starting build process...");

  const result = await build({
    entryPoints: [join(__dirname, './index.js')],
    bundle: true,
    format: 'esm',
    target: 'esnext',
    external: ['cloudflare:sockets'],
    minify: true,
    write: false
  });

  mkdirSync(join(__dirname, 'dist'), { recursive: true });
  const outputPath = join(__dirname, 'dist/worker.js');
  
  const finalCode = `// Time is: ${new Date().toISOString()}\n${result.outputFiles[0].text}`;
  
  writeFileSync(outputPath, finalCode, 'utf8');
  console.log("🧸 Build successful! Final worker file is located in the root directory with the name _worker.js");
}

runBuild().catch(() => process.exit(1));

```

### README.md

```md
> [!NOTE]
>
> <br/>
>
> **Serverless Runtime**
> 
> **Running VLESS-WS-TLS/TCP inside Cloudflare Edge/Serverless runtime**  
>  
> Base on [zizifn] and [harmony]
>
> [![zread](https://img.shields.io/badge/Ask_Zread-_.svg?style=plastic&color=00b0aa&labelColor=000000&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB3aWR0aD0iMTYiIGhlaWdodD0iMTYiIHZpZXdCb3g9IjAgMCAxNiAxNiIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTQuOTYxNTYgMS42MDAxSDIuMjQxNTZDMS44ODgxIDEuNjAwMSAxLjYwMTU2IDEuODg2NjQgMS42MDE1NiAyLjI0MDFWNC45NjAxQzEuNjAxNTYgNS4zMTM1NiAxLjg4ODEgNS42MDAxIDIuMjQxNTYgNS42MDAxSDQuOTYxNTZDNS4zMTUwMiA1LjYwMDEgNS42MDE1NiA1LjMxMzU2IDUuNjAxNTYgNC45NjAxVjIuMjQwMUM1LjYwMTU2IDEuODg2NjQgNS4zMTUwMiAxLjYwMDEgNC45NjE1NiAxLjYwMDFaIiBmaWxsPSIjZmZmIi8%2BCjxwYXRoIGQ9Ik00Ljk2MTU2IDEwLjM5OTlIMi4yNDE1NkMxLjg4ODEgMTAuMzk5OSAxLjYwMTU2IDEwLjY4NjQgMS42MDE1NiAxMS4wMzk5VjEzLjc1OTlDMS42MDE1NiAxNC4xMTM0IDEuODg4MSAxNC4zOTk5IDIuMjQxNTYgMTQuMzk5OUg0Ljk2MTU2QzUuMzE1MDIgMTQuMzk5OSA1LjYwMTU2IDE0LjExMzQgNS42MDE1NiAxMy43NTk5VjExLjAzOTlDNS42MDE1NiAxMC42ODY0IDUuMzE1MDIgMTAuMzk5OSA0Ljk2MTU2IDEwLjM5OTlaIiBmaWxsPSIjZmZmIi8%2BCjxwYXRoIGQ9Ik0xMy43NTg0IDEuNjAwMUgxMS4wMzg0QzEwLjY4NSAxLjYwMDEgMTAuMzk4NCAxLjg4NjY0IDEwLjM5ODQgMi4yNDAxVjQuOTYwMUMxMC4zOTg0IDUuMzEzNTYgMTAuNjg1IDUuNjAwMSAxMS4wMzg0IDUuNjAwMUgxMy43NTg0QzE0LjExMTkgNS42MDAxIDE0LjM5ODQgNS4zMTM1NiAxNC4zOTg0IDQuOTYwMVYyLjI0MDFDMTQuMzk4NCAxLjg4NjY0IDE0LjExMTkgMS42MDAxIDEzLjc1ODQgMS42MDAxWiIgZmlsbD0iI2ZmZiIvPgo8cGF0aCBkPSJNNCAxMkwxMiA0TDQgMTJaIiBmaWxsPSIjZmZmIi8%2BCjxwYXRoIGQ9Ik00IDEyTDEyIDQiIHN0cm9rZT0iI2ZmZiIgc3Ryb2tlLXdpZHRoPSIxLjUiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIvPgo8L3N2Zz4K&logoColor=ffffff)](https://zread.ai/NiREvil/zizifn)
> 
> <br/>
> 

> [!CAUTION]
> 
> ## 🧸 Cloudflare 1101 Error  
> **Resolved**, without Obfuscation or junk code injection!
> 
> در آپدیت اخیر، مشکل رایج Cloudflare Worker Error 1101 که هنگام دیپلوی نسخه‌ی آخر رخ می‌داد، برطرف شد، بدون نیاز به مبهم‌سازی/obfuscation یا تغییر در منطق اصلی VLESS over WS
>
> - مثل سابق، برای دیپلوی از فایل worker.js استفاده کنید.
> 
> ### تغییرات معماری
> 
> <details>
> <summary> خواندن ادامه توضیحات </summary> <br/>
> 
> مهم‌ترین تغییر این نسخه، ماژولارسازی ساختار پنل و جداسازی لایه‌ی فرانت‌اند از منطق Worker بود،
> 
> #### فایل index.html که شامل:
> 
> - پنل کاربری
> - استایل‌ها (CSS)
> - اسکریپت‌های رابط کاربری (Client-side JS)   
هست، دیگه داخل وورکر embed نمی‌شه!
> 
> این فایل حالا داخل رپو مستقیم توسط GitHub Pages دپلوی شده و از همون‌جا توسط Worker فراخوانی fetch می‌شه.
> 
> #### در مقابل، woekers/pages کلادفلر صرفا میزبان فایل index.js یا همون worker.js هستن که شامل:
> 
>  - منطق VLESS over WebSocket
>  - ساخت لینک‌های Subscription
>  - پردازش ترافیک TCP/UDP
>  - مدیریت DNS over HTTPS
>  ‏- Scamalytics Lookup Endpoint
> 
> می‌باشن.
> 
> <hr/><br/>  
> 
> ### مزایای این تغییر
> من دلیل این‌که چرا مثل بقیه کد رو نمی‌برم زیر مبهم‌سازی و رمزنگاری و اضافه کردن کلی کد مرده رو اینجا داخل [issues] نوشتم روز گذشته.
> 
> 
> **این جداسازی باعث شده:**
> 
> - حجم اسکریپت اجرایی Worker به‌طور قابل توجهی کاهش پیدا کنه, (یک سوم قبل).
> - بار پردازشی روی Cloudflare Isolate CPU Budget کمتر بشه.
> - از Freeze شدن Event Loop در Cold Start جلوگیری بشه.
> - وابستگی به Render رشته‌های HTML سنگین در مسیر Upgrade حذف بشه.
> - احتمال بروز خطاهای Runtime مثل:
> 
>   - 1101: Worker threw exception
>   - 1102: Worker exceeded CPU time limit
> 
> به حداقل مقدار ممکن برسه.
> 
> <hr/><br/>  
> 
> یه سری تغییرات دیگه در جهت پایداری /Stability Hardening کد و هم‌چنین برای جلوگیری از بروز خطاهای آینده در شرایط Load بالا یا شبکه‌ی ناپایدار انجام شده، مثل:  
> 
> - تمامی درخواست‌های خارجی (مثل GitHub Raw و DNS DoH) حالا با Timeout و AbortController انجام میشن.
> - لیست IPهای تمیز کلادفلر از طریق caches.default در Edge Cache ذخیره شده و از هر دقیقه Fetch شدن و هدر رفت منابع وورکر جلوگیری میشه.
> - اعتبارسنجی UUID توی Stream Pipeline شکل non-throw انجام میشه تا از Crash شدن WritableStream اجتناب کنیم.
> - مقدار SNI  داخل کانفیگ‌های TLS Links ثابت شده و دبگه با حروف کوچیک‌تر بزرگتر رندوم نمیاد تا شاید تونسته باشیم با این‌کار از Session Cache Miss توی لایه‌ی Edge جلوگیری کرده باشیم.
> - پردازش آدرس SOCKS5 از مسیر Hot (WS Upgrade) خارج شده و داخل سطح Isolate Cache قرار دادیم.
> - طی همین‌ هفته بخش نمایش ریسک آی‌پی رو کلا بازنویسی می‌کنم که دیگه نیازی به api از Scamalytics با کلی محدودیت و ادا نداشته باشیم. 
> 
> </details>
> <br/>	

<br><br/> 

> [!TIP]
>
> <br/>
> 
> این پروژه به ما اجازه می‌ده تا بدون نیاز به سرور شخصی، تنها با استفاده از زیرساخت Cloudflare edge/Serverless یک کانفیگ پروکسی امن و پایدار برای خود و یا اطرافیان خودمون داشته باشیم.
>
> تمام پردازش‌ها در شبکه کلادفلر بر روی worker & pages انجام می‌شه و ما از سرعت و امنیت بالای این زیرساخت فوق‌العاده به‌طور رایگان بهره‌مند می‌شویم.  
> 
> <br/>
>
> **برخی از ویژگی‌ها**
> 
> 🧩 **راه‌اندازی سریع و آسان:** در کمتر از ۵ دقیقه فقط با چند کلیک، پروکسی vless ما آماده است.
>
> ⚙️ **کد سمت‌سرور قدرتمند:** کد‌ بک‌اند این پروژه بدون نیاز به مبهم‌سازی‌‌های سنگین و پیچیده بدون دریافت خطای 1101 به شکل عادی به کار خود ادامه می‌دهد. خودتان امتحان کنید.  
>
> 📈 **پنل اطلاعات:** این پروژه دارای یک رابط کاربری زیبا و حرفه‌ای برای کپی و افزودن کانفیگ‌ در کلاینت‌های مختلف، نمایش اطلاعات شبکه، پروکسی‌ آی‌پی و کانکشن شما می‌باشد.
>   
> 🧠 **دریافت خودکار آی‌پی:** آی‌پی‌های تمیز به صورت خودکار از مخازن معتبر گیت‌هاب دریافت و در کانفیگ‌های داخل لینک ساب‌اسکریپشن شما قرار می‌گیرند.
>   
> 🔄 **بروزرسانی لینک ساب:** با هر بار آپدیت لینک اشتراک در داخل کلاینت، آی‌پی‌های جدید جایگزین می‌شوند.
>   
> 💻 **چهار روش پیکربندی مختلف:** مناسب برای کاربران مبتدی تا حرفه‌ای (فورک، کپی-پست، آپلود فایل وورکر، wrangler).
> 
> 🖱️ **اتصال با یک کلیک:** دکمه‌های آماده برای وارد کردن لینک اشتراک به محبوب‌ترین کلاینت‌ها و اتصال سریع و آسان.

<br/> 

> [!WARNING]
>
> <br/> 
> 
> **نکته مهم:**  
> چند هفته‌ای هست که دامنه‌ی `pages.dev` داخل ایران فیلتر شده و دیگه نمی‌شه از کانفیگ‌های ساخته‌شده با این روش استفاده کرد، عملا بدرد نمی‌خوره.
>
> ولی دامنه‌ی `worker.dev` هم‌چنان عالیه، پس پیشنهاد می‌کنم روش اول و دوم رو برای ساخت وورکر پیش برید.
>
> <br/>

<br><br/>
   
# نحوه راه‌اندازی

شما می‌تونید به یکی از چهار روش زیر پروژه رو روی اکانت کلادفلر خود مستقر کنید.

## روش اول: وورکر از طریق wrangler (پیشنهادی)

 ‏1. کافیه که کلیک کنید روی این دکمه زیر تا پروسه فورک کردن مخزن و رفتن تو داشبورد کلادفلر و ساخت وورکر جدید رو خودش طی کنه

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/NiREvil/zizifn)

 ‏2. حالا تو صفحه‌ی باز شده اگر اکانت کلادفلر به گیت‌هاب متصل بود که هیچ، اگر نبود اول از گزینه Git account و سپس New git connection اکانت خودتون رو بهش وصل کنید.

> [!NOTE]
>
> <br/>
> 
> <details>
> <summary> مشاهده اسکرین‌شات </summary> <br/>
> 
> <p align="center">
>  <img src="https://github.com/user-attachments/assets/a6bce206-f57d-49dd-bbde-9ee2ccee6b13" alt="Deployments" width="768px" />
> </p>
> 
> </details>
>
> <br/>

 ‏3. گزینه‌هایی که می‌تونید تغییر بدید:

- نام پروژه - Project Name
- افزون متغیر - Variables
  - [جدول متغیرها](#تنظیمات-و-متغیرها)

 ‏4. بعد از این‌که:  
- به اکانت گیت‌هاب خودتون وصل شدید
- یک اسم برای وورکر تعیین کردید
- یک [UUID][uuid] از سایت گرفته و در داخل  متغییرها قرار دادید  

وقتش رسیده که کلیک کنید روی گزینه Create and Deploy و چند ثانیه صبر کنید تا پیغام تموم شدن پروسه ظاهر شه.

<br><br/>

## روش دوم: از طریق workers

**این روش برای تست سریع (بدون نیاز به ساخت اکانت گیت‌هاب) مناسب است.**

 ‏1. **ورود به داشبورد کلادفلر:** وارد داشبورد Cloudflare شوید.   
  - [ورود به کلادفلر][login]
  - [ساخت اکانت کلادفلر][signup]
    - [ایجاد ایمیل موقت][mail]
   
   > **نکته:** چند وقتی میشه که کلادفلر اجازه نمیده با ایمیل فیک اکانت ساخته بشه، ساخته میشه ولی وریفای نمی‌شه، ده‌بار، سی‌بار، پنجاه بار هم برید از طریق از ایمیلی که براتون ارسال کرده عمل وریفای رو انجام بدید بعد از چند ثانیه دوباره و دوباره و دوباره میگه وریفای کنبد، پس پیشنهاد می‌کنم از G-Mail, Outlook, hotmail, protonail و امثال اینا استفاده بکنید برای ساخت اکانت کلادفلر.

 ‏2. **شروع ساخت ورکر:** در نوار ابزار بالای سایت روی گزینه `Add` یا در موبایل روی آیکون `+` کلیک کرده سپس "workers" را انتخاب کنید.
  
 ‏3. **انتخاب وورکر:** روی دکمه Get Start مقابل "Start with Hello World!" کلیک کرده و سپس یک نام دلخواه برای ورکر خود انتخاب کنید. سپس Deploy را بزنید.

 ‏4. **کپی کد:** پس از اتمام مرحله ساخت وورکر روی گزینه Edit code کلیک کرده و سپس محتویات یکی از فایل‌های زیر را کپی و یا خود فایل رو دانلود کنید. (هیچ فرقی ندارن باهم)
 
- [کد نرمال وورکر](./index.js)
- [کد فشرده‌ی وورکر](./_worker.js)

قبل از هرکاری؛ کد پیش‌فرض `hello world` داخل وورکر رو کامل حذف کنید، سپس برای انجام عمل جایگذاری کد در pc می‌تونید از کلید‌های ترکیبی کبورد `ctrl+v` استفاده کنید، و در موبایل باید حتما فایل وورکر رو دانلود کرده سپس در محیط ویرایش‌گر کلادفلر آپلود کنید. کافیه اول روی آیکون Explorer سمت چپ کلیک کنید تا منو باز بشه سپس توی یک فضای خالی چند لحظه دستتون رو نگه‌دارید تا گزینه‌ها بالا بیان تا بتونید آپلود رو انتخاب کنید.

> [!NOTE]
>
> <br/>
> 
> <details>
> <summary> مشاهده اسکرین‌شات </summary> <br/>
> 
> <p align="center">
>  <img src="https://github.com/user-attachments/assets/b921df78-6471-4ce5-8f2a-d3b2058542de" alt="upload-1" width="768px" />
> </p>
> 
> <p align="center">
>  <img src="https://github.com/user-attachments/assets/ad97fd13-c24c-427d-a42a-45a2395188a5" alt="upload-2" width="768px" />
> </p>
> 
> </details>
>
> <br/>

 ‏5. **اعمال تغییرات** بعد از جایگذاری کد در داخل وورکر؛ به منظور اعمال تغییرات از گوشه سمت راست روی گزینه آبی رنگ `Deploy` کلیک کنید.

<br><br/>

## روش سوم: از طریق آپلود در Pages

> [!CAUTION]
> 
> روش سوم و چهارم موقتا توصیه نمی‌شه به دلیل فیلتر بودن دامنه‌های `pages.dev` در مملکت لعنتی‌ِ ما،
> 
> ترجیحا از روش اول و دوم یعنی وورکر پیش برید تا وقتی که محدودیت رفع بشه.

به‌ هر حال  
این روش به شما اجازه میده از کد پروژه برای دپلوی در Cloudflare Pages (بدون اتصال به اکانت گیت‌هاب) استفاده کنید.

 ‏1. **دانلود فایل:** فایل [worker.js_](./_worker.js) رو از مخزن دانلود کنید.
 
 ‏2. **شروع ساخت pages:** در نوار ابزار بالای سایت روی گزینه `Add` یا در موبایل روی آیکون `+` کلیک کرده سپس `pages` را انتخاب کنید.

 ‏3. **آپلود فایل:** در تب Pages روی دکمه Get Start مقابل Drag and drop your files کلیک کنید.

 ‏4. **دپلوی پروژه:** برای ساب‌دامنه خود یک نام تعیین کرده و سپس فایل "_worker.js" را از سیستم‌ خود آپلود کنید تا پروسه دپلوی آغاز گردد.

 ‏5. **نکته مهم:** موقع ساخت pages دقت کنید حتما حتما نام فایل باید: `worker.js_` باشه، هر اسم دیگه‌ای به‌جز این برای کلادفلر پیجز قابل قبول نیست. 

<br><br/>

## روش چهارم: فورک زدن مخزن
 
این روش ساده‌ترین روش برای مدیریت و بروزرسانی‌های آینده است. از طریق فورک زدن مخزن zizifn و اتصال اون به اکانت کلادفلر پیش میریم.

 ‏1. **فورک/کلون کردن پروژه:** ابتدا این مخزن ([Repository][N-zizifn]) رو با اکانت گیت‌هاب خود Fork کنید.
 
> [!NOTE]
>
> <br/>
> 
> <details>
> <summary> مشاهده اسکرین‌شات </summary> 
> 
> <p align="center">
>  <img src="https://github.com/user-attachments/assets/316345d0-b27b-442e-b48d-69d4fd0d1da2" alt="Fork" width="768px" />
> </p>
> 
> </details>
>
> <br/>
>

 ‏2. **ورود به کلادفلر:** وارد داشبورد کلادفلر خود شده و در نوار ابزار بالای سایت روی `Add` یا در موبایل روی آیکون `+` کلیک کرده سپس `Pages` را انتخاب کنید. همچنین می‌تونید از منوی کشویی سمت چپ از بخش Build و سپس سپس Compute & Ai به بخش workers & Pages راه پیدا کنید.

 ‏3. **اتصال اکانت به گیت‌هاب:** در صفحه جدید گزینه "Import an existing Git repository" را انتخاب کنید.  
در قسمت اول اگر به اکانت خود متصل نبودید "Connect to Git" را بزنید.  
**انتخاب مخزن:** مخزن فورک شده خود را انتخاب کنید و Begin setup را بزنید.

<br/>
  
 ‏4. **تنظیمات دپلوی:**

 ‏- ‏**Project name:** یک نام دلخواه برای پروژه‌تان انتخاب کنید.
 
 ‏- **Production branch:** شاخه main را انتخاب کنید.
  
 ‏- **Framework preset:** گزینه None را انتخاب کنید.

 ‏- **ذخیره و دپلوی:** روی Save and Deploy کلیک کنید. پروژه شما در عرض چند ثانیه آماده خواهد شد!
 
<br><br/>

## تنظیمات و متغیرها

بعد از راه‌اندازی، باید متغیرهای محیطی `Environment Variables` را برای شخصی‌سازی کانفیگ‌های خود تنظیم کنید. این متغیرها را در داشبورد پروژه خود در کلادفلر، در مسیر زیر اضافه کنید:

> worker & Pages > Settings > Variables and Secrets > Add variable

<br/> 

| **نام متغیر** | **توضیحات** | **الزامی/اختیاری** | **مقدار پیش‌فرض** |
| :----- | :----------------- | :-----: | --------------- |
| UUID | آی‌دی منحصر به فرد شما. این متغیر برای امنیت ضروری است. | الزامی | برای ساخت، به [UUID Generator][uuid] مراجعه کنید |
| PROXYIP | یک IP یا دامنه برای fronting. این آدرس به عنوان آی‌پی جایگزین موقع بازدید از وب‌سایت‌های پشت کلادفلر مانند speedtest و whoer استفاده می‌شود. از [مخزن پروکسی آی‌پی][proxyip] ما پیشنهادی یک مورد را انتخاب کنید. | اختیاری | مقدار پیش‌فرض: `nima.nscl.ir` ده‌ها پروکسی‌آی‌پی آمریکا از بهترین سرویس‌دهنده‌ها <br/> می‌تونید از پروکسی‌آی‌پی‌های ترکیه در پشت این دامنه هم استفاده کنید: `turk.radicalization.ir` |
| SCAMALYTICS_USERNAME | نام کاربری سرویس Scamalytics برای تحلیل IP. | اختیاری | برای مصرف شخصی نیاز نیست. در صورت استفاده عمومی و فورک‌های زیاد، از سایت [scamalytics] درخواست API شخصی بدهید. در عرض ۲۴ ساعت اطلاعات سرویس ایمیل می‌شود. |
| SCAMALYTICS_API_KEY | کلید API سرویس Scamalytics. | اختیاری | همراه با نام کاربری از سایت [scamalytics] دریافت می‌شود. |
| SCAMALYTICS_BASEURL | اندپوینت سرویس Scamalytics. | اختیاری | همراه با نام کاربری و api برای شما ایمیل میشود.  |

<br><br/> 

## نحوه استفاده

<br/>

### دسترسی به پنل مدیریت
پس از دپلوی، کافیست UUID خود را به انتهای آدرس ورکر یا پیج خود اضافه کنید:

`https://<Your-Worker-URL>/<Your-UUID>`

برای مثال:

`https://my-proxy.pages.dev/d342d11e-d424-4583-b36e-524ab1f0afa4`

<hr/><br/> 

### دریافت لینک اشتراک (Subscription)
لینک اشتراک شما شامل ده‌ها کانفیگ حاوی آی‌پی‌های تمیز کلادفلر می‌باشد. برای دریافت آن به صورت خودکار از کلید‌های داخل پنل استفاده کنید.

و یا در صورت نیار به آدرس اشتراک‌ها به صورت دستی و استفاده از آن‌ها در کلاینت‌های دیگر عبارت `xray` یا `sb` را بین آدرس ورکر و UUID خود قرار دهید:

`https://<Your-Worker-URL>/xray/<Your-UUID>`

`https://<Your-Worker-URL>/sb/<Your-UUID>`

برای مثال:  

`https://my-proxy.pages.dev/xray/d342d11e-d424-4583-b36e-524ab1f0afa4`

<br/> 

> [!NOTE]
>
> **عبارت xray:**
>   
> برای کلاینت‌هایی که از هسته‌ی Xray استفاده می‌کنند، مانند:  
> v2rayNG, MahsaNG, Hiddify, Nekoray, v2rayN, Streisand, Napsternet, Happ, and etc.
>
> <br/>
> 
> **عبارت sb:**
> 
> برای کلاینت‌هایی که از هسته‌ی SingBox استفاده می‌کنند، مانند:  
> Nekobox, Exclave, Singbox, Husi, Karing, and etc.
>
> <br/>
> 
> آی‌پی‌های موجود لینک اشتراک‌ها از مخزن آی‌پی تمیز [NiREvil/vless][cleanip] تامین میشه، سیکل بروزرسانی آی‌پی‌ها: هر ۴ ساعت.
>
> 
> <br/>

<br/><br/>

## لینک دانلود کلاینت‌ها
برای راحتی، می‌تونید از دکمه‌های موجود در پنل مدیریت خود جهت افزودن لینک‌های اشتراک به کلاینت‌ها استفاده کنید، در زیر لینک دانلود دیگر کلاینت‌ها قرار داده شده است:

- **Xray Core Clients:**
  - [Hiddify]
  - [v2RayNG] ⭐ (Recommended)
  - [MahsaNG]
  - [Streisand]
  - [ZedSecure]  
  - [Nekoray]
  - [Throne]
  - [v2rayN]
  - [v2rayN-Pro]
  - [v2rayTun]  
  - [Happ]  

- **Singbox Core Clients:**
  - [Nekobox]
  - [Exclave] ⭐ (Recommended)
  - [Singbox]
  - [Sagernet]
  - [Karing]
  - [Husi]

- **Clash Core Clients:**
  - [Clash-Meta] ⭐ (Recommended)  
  - [FIClash]  
  - [ClashMi]

<hr/><br/> 

## Credits
Many thanks to [NiREvil] and [zizifn]

<br/>	 

> [!WARNING]
>
> <br/>
> 
> <details>
> <summary>⁠■■■UI </summary> <br/>
> 
> <p align="center">
> <img src="https://github.com/user-attachments/assets/5a07e7e8-ee8a-43d0-9dc3-3afa09792075" alt="ZiZifn-UI" width="768px" />
> </p>
> 
> </details>
>
> <br/>
>


[zizifn]: https://github.com/zizifn/edgetunnel
[N-zizifn]: https://github.com/NiREvil/zizifn
[harmony]: https://github.com/NiREvil/Harmony
[NiREvil]: https://github.com/NiREvil
[login]: https://dash.cloudflare.com/login
[signup]: https://dash.cloudflare.com/sign-up
[mail]: https://mail.tm/en
[uuid]: https://www.uuidgenerator.net
[proxyip]: https://github.com/NiREvil/vless/blob/main/sub/ProxyIP.md
[scamalytics]: https://scamalytics.com/ip/api/enquiry?monthly_api_calls=5000
[cleanip]: https://github.com/NiREvil/vless/blob/main/Cloudflare-IPs.json
[v2RayNG]: https://github.com/2dust/v2rayng/releases
[Singbox]: https://github.com/SagerNet/sing-box/releases
[Hiddify]: https://github.com/hiddify/hiddify-app/releases
[Exclave]: https://github.com/dyhkwong/Exclave/releases
[Nekobox]: https://github.com/MatsuriDayo/NekoBoxForAndroid/releases
[Sagernet]: https://github.com/dyhkwong/SagerNet/releases
[MahsaNG]: https://github.com/mahsanet/MahsaaNG/releases
[NikaNG]: https://github.com/mahsanet/NikaNG/releases
[Karing]: https://github.com/KaringX/karing/releases
[Streisand]: https://apps.apple.com/app/id6450534064
[Husi]: https://github.com/xchacha20-poly1305/husi/releases
[Nekoray]: https://github.com/MRT-project/Neko-ray/releases
[v2rayTun]: https://play.google.com/store/apps/details?id=com.v2raytun.android
[Happ]: https://play.google.com/store/apps/details?id=com.happproxy
[Clash-Meta]: https://github.com/MetaCubeX/ClashMetaForAndroid/releases
[FIClash]: https://github.com/chen08209/FlClash/releases
[ClashMi]: https://github.com/KaringX/clashmi/releases
[v2rayN]: https://github.com/2dust/v2rayN/releases
[v2rayN-Pro]: https://github.com/lowercase78/V2RayN-PRO/releases/
[Throne]: https://github.com/throneproj/Throne/releases
[ZedSecure]: https://github.com/CluvexStudio/ZedSecure
[issues]: https://github.com/NiREvil/zizifn/issues/9#issuecomment-3936974414

```

### LICENSE

```
                    GNU GENERAL PUBLIC LICENSE
                       Version 2, June 1991

 Copyright (C) 1989, 1991 Free Software Foundation, Inc.,
 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA
 Everyone is permitted to copy and distribute verbatim copies
 of this license document, but changing it is not allowed.

                            Preamble

  The licenses for most software are designed to take away your
freedom to share and change it.  By contrast, the GNU General Public
License is intended to guarantee your freedom to share and change free
software--to make sure the software is free for all its users.  This
General Public License applies to most of the Free Software
Foundation's software and to any other program whose authors commit to
using it.  (Some other Free Software Foundation software is covered by
the GNU Lesser General Public License instead.)  You can apply it to
your programs, too.

  When we speak of free software, we are referring to freedom, not
price.  Our General Public Licenses are designed to make sure that you
have the freedom to distribute copies of free software (and charge for
this service if you wish), that you receive source code or can get it
if you want it, that you can change the software or use pieces of it
in new free programs; and that you know you can do these things.

  To protect your rights, we need to make restrictions that forbid
anyone to deny you these rights or to ask you to surrender the rights.
These restrictions translate to certain responsibilities for you if you
distribute copies of the software, or if you modify it.

  For example, if you distribute copies of such a program, whether
gratis or for a fee, you must give the recipients all the rights that
you have.  You must make sure that they, too, receive or can get the
source code.  And you must show them these terms so they know their
rights.

  We protect your rights with two steps: (1) copyright the software, and
(2) offer you this license which gives you legal permission to copy,
distribute and/or modify the software.

  Also, for each author's protection and ours, we want to make certain
that everyone understands that there is no warranty for this free
software.  If the software is modified by someone else and passed on, we
want its recipients to know that what they have is not the original, so
that any problems introduced by others will not reflect on the original
authors' reputations.

  Finally, any free program is threatened constantly by software
patents.  We wish to avoid the danger that redistributors of a free
program will individually obtain patent licenses, in effect making the
program proprietary.  To prevent this, we have made it clear that any
patent must be licensed for everyone's free use or not licensed at all.

  The precise terms and conditions for copying, distribution and
modification follow.

                    GNU GENERAL PUBLIC LICENSE
   TERMS AND CONDITIONS FOR COPYING, DISTRIBUTION AND MODIFICATION

  0. This License applies to any program or other work which contains
a notice placed by the copyright holder saying it may be distributed
under the terms of this General Public License.  The "Program", below,
refers to any such program or work, and a "work based on the Program"
means either the Program or any derivative work under copyright law:
that is to say, a work containing the Program or a portion of it,
either verbatim or with modifications and/or translated into another
language.  (Hereinafter, translation is included without limitation in
the term "modification".)  Each licensee is addressed as "you".

Activities other than copying, distribution and modification are not
covered by this License; they are outside its scope.  The act of
running the Program is not restricted, and the output from the Program
is covered only if its contents constitute a work based on the
Program (independent of having been made by running the Program).
Whether that is true depends on what the Program does.

  1. You may copy and distribute verbatim copies of the Program's
source code as you receive it, in any medium, provided that you
conspicuously and appropriately publish on each copy an appropriate
copyright notice and disclaimer of warranty; keep intact all the
notices that refer to this License and to the absence of any warranty;
and give any other recipients of the Program a copy of this License
along with the Program.

You may charge a fee for the physical act of transferring a copy, and
you may at your option offer warranty protection in exchange for a fee.

  2. You may modify your copy or copies of the Program or any portion
of it, thus forming a work based on the Program, and copy and
distribute such modifications or work under the terms of Section 1
above, provided that you also meet all of these conditions:

    a) You must cause the modified files to carry prominent notices
    stating that you changed the files and the date of any change.

    b) You must cause any work that you distribute or publish, that in
    whole or in part contains or is derived from the Program or any
    part thereof, to be licensed as a whole at no charge to all third
    parties under the terms of this License.

    c) If the modified program normally reads commands interactively
    when run, you must cause it, when started running for such
    interactive use in the most ordinary way, to print or display an
    announcement including an appropriate copyright notice and a
    notice that there is no warranty (or else, saying that you provide
    a warranty) and that users may redistribute the program under
    these conditions, and telling the user how to view a copy of this
    License.  (Exception: if the Program itself is interactive but
    does not normally print such an announcement, your work based on
    the Program is not required to print an announcement.)

These requirements apply to the modified work as a whole.  If
identifiable sections of that work are not derived from the Program,
and can be reasonably considered independent and separate works in
themselves, then this License, and its terms, do not apply to those
sections when you distribute them as separate works.  But when you
distribute the same sections as part of a whole which is a work based
on the Program, the distribution of the whole must be on the terms of
this License, whose permissions for other licensees extend to the
entire whole, and thus to each and every part regardless of who wrote it.

Thus, it is not the intent of this section to claim rights or contest
your rights to work written entirely by you; rather, the intent is to
exercise the right to control the distribution of derivative or
collective works based on the Program.

In addition, mere aggregation of another work not based on the Program
with the Program (or with a work based on the Program) on a volume of
a storage or distribution medium does not bring the other work under
the scope of this License.

  3. You may copy and distribute the Program (or a work based on it,
under Section 2) in object code or executable form under the terms of
Sections 1 and 2 above provided that you also do one of the following:

    a) Accompany it with the complete corresponding machine-readable
    source code, which must be distributed under the terms of Sections
    1 and 2 above on a medium customarily used for software interchange; or,

    b) Accompany it with a written offer, valid for at least three
    years, to give any third party, for a charge no more than your
    cost of physically performing source distribution, a complete
    machine-readable copy of the corresponding source code, to be
    distributed under the terms of Sections 1 and 2 above on a medium
    customarily used for software interchange; or,

    c) Accompany it with the information you received as to the offer
    to distribute corresponding source code.  (This alternative is
    allowed only for noncommercial distribution and only if you
    received the program in object code or executable form with such
    an offer, in accord with Subsection b above.)

The source code for a work means the preferred form of the work for
making modifications to it.  For an executable work, complete source
code means all the source code for all modules it contains, plus any
associated interface definition files, plus the scripts used to
control compilation and installation of the executable.  However, as a
special exception, the source code distributed need not include
anything that is normally distributed (in either source or binary
form) with the major components (compiler, kernel, and so on) of the
operating system on which the executable runs, unless that component
itself accompanies the executable.

If distribution of executable or object code is made by offering
access to copy from a designated place, then offering equivalent
access to copy the source code from the same place counts as
distribution of the source code, even though third parties are not
compelled to copy the source along with the object code.

  4. You may not copy, modify, sublicense, or distribute the Program
except as expressly provided under this License.  Any attempt
otherwise to copy, modify, sublicense or distribute the Program is
void, and will automatically terminate your rights under this License.
However, parties who have received copies, or rights, from you under
this License will not have their licenses terminated so long as such
parties remain in full compliance.

  5. You are not required to accept this License, since you have not
signed it.  However, nothing else grants you permission to modify or
distribute the Program or its derivative works.  These actions are
prohibited by law if you do not accept this License.  Therefore, by
modifying or distributing the Program (or any work based on the
Program), you indicate your acceptance of this License to do so, and
all its terms and conditions for copying, distributing or modifying
the Program or works based on it.

  6. Each time you redistribute the Program (or any work based on the
Program), the recipient automatically receives a license from the
original licensor to copy, distribute or modify the Program subject to
these terms and conditions.  You may not impose any further
restrictions on the recipients' exercise of the rights granted herein.
You are not responsible for enforcing compliance by third parties to
this License.

  7. If, as a consequence of a court judgment or allegation of patent
infringement or for any other reason (not limited to patent issues),
conditions are imposed on you (whether by court order, agreement or
otherwise) that contradict the conditions of this License, they do not
excuse you from the conditions of this License.  If you cannot
distribute so as to satisfy simultaneously your obligations under this
License and any other pertinent obligations, then as a consequence you
may not distribute the Program at all.  For example, if a patent
license would not permit royalty-free redistribution of the Program by
all those who receive copies directly or indirectly through you, then
the only way you could satisfy both it and this License would be to
refrain entirely from distribution of the Program.

If any portion of this section is held invalid or unenforceable under
any particular circumstance, the balance of the section is intended to
apply and the section as a whole is intended to apply in other
circumstances.

It is not the purpose of this section to induce you to infringe any
patents or other property right claims or to contest validity of any
such claims; this section has the sole purpose of protecting the
integrity of the free software distribution system, which is
implemented by public license practices.  Many people have made
generous contributions to the wide range of software distributed
through that system in reliance on consistent application of that
system; it is up to the author/donor to decide if he or she is willing
to distribute software through any other system and a licensee cannot
impose that choice.

This section is intended to make thoroughly clear what is believed to
be a consequence of the rest of this License.

  8. If the distribution and/or use of the Program is restricted in
certain countries either by patents or by copyrighted interfaces, the
original copyright holder who places the Program under this License
may add an explicit geographical distribution limitation excluding
those countries, so that distribution is permitted only in or among
countries not thus excluded.  In such case, this License incorporates
the limitation as if written in the body of this License.

  9. The Free Software Foundation may publish revised and/or new versions
of the General Public License from time to time.  Such new versions will
be similar in spirit to the present version, but may differ in detail to
address new problems or concerns.

Each version is given a distinguishing version number.  If the Program
specifies a version number of this License which applies to it and "any
later version", you have the option of following the terms and conditions
either of that version or of any later version published by the Free
Software Foundation.  If the Program does not specify a version number of
this License, you may choose any version ever published by the Free Software
Foundation.

  10. If you wish to incorporate parts of the Program into other free
programs whose distribution conditions are different, write to the author
to ask for permission.  For software which is copyrighted by the Free
Software Foundation, write to the Free Software Foundation; we sometimes
make exceptions for this.  Our decision will be guided by the two goals
of preserving the free status of all derivatives of our free software and
of promoting the sharing and reuse of software generally.

                            NO WARRANTY

  11. BECAUSE THE PROGRAM IS LICENSED FREE OF CHARGE, THERE IS NO WARRANTY
FOR THE PROGRAM, TO THE EXTENT PERMITTED BY APPLICABLE LAW.  EXCEPT WHEN
OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS AND/OR OTHER PARTIES
PROVIDE THE PROGRAM "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED
OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  THE ENTIRE RISK AS
TO THE QUALITY AND PERFORMANCE OF THE PROGRAM IS WITH YOU.  SHOULD THE
PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF ALL NECESSARY SERVICING,
REPAIR OR CORRECTION.

  12. IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING
WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MAY MODIFY AND/OR
REDISTRIBUTE THE PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES,
INCLUDING ANY GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES ARISING
OUT OF THE USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT NOT LIMITED
TO LOSS OF DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY
YOU OR THIRD PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER
PROGRAMS), EVEN IF SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE
POSSIBILITY OF SUCH DAMAGES.

                     END OF TERMS AND CONDITIONS

            How to Apply These Terms to Your New Programs

  If you develop a new program, and you want it to be of the greatest
possible use to the public, the best way to achieve this is to make it
free software which everyone can redistribute and change under these terms.

  To do so, attach the following notices to the program.  It is safest
to attach them to the start of each source file to most effectively
convey the exclusion of warranty; and each file should have at least
the "copyright" line and a pointer to where the full notice is found.

    <one line to give the program's name and a brief idea of what it does.>
    Copyright (C) <year>  <name of author>

    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License along
    with this program; if not, write to the Free Software Foundation, Inc.,
    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

Also add information on how to contact you by electronic and paper mail.

If the program is interactive, make it output a short notice like this
when it starts in an interactive mode:

    Gnomovision version 69, Copyright (C) year name of author
    Gnomovision comes with ABSOLUTELY NO WARRANTY; for details type `show w'.
    This is free software, and you are welcome to redistribute it
    under certain conditions; type `show c' for details.

The hypothetical commands `show w' and `show c' should show the appropriate
parts of the General Public License.  Of course, the commands you use may
be called something other than `show w' and `show c'; they could even be
mouse-clicks or menu items--whatever suits your program.

You should also get your employer (if you work as a programmer) or your
school, if any, to sign a "copyright disclaimer" for the program, if
necessary.  Here is a sample; alter the names:

  Yoyodyne, Inc., hereby disclaims all copyright interest in the program
  `Gnomovision' (which makes passes at compilers) written by James Hacker.

  <signature of Ty Coon>, 1 April 1989
  Ty Coon, President of Vice

This General Public License does not permit incorporating your program into
proprietary programs.  If your program is a subroutine library, you may
consider it more useful to permit linking proprietary applications with the
library.  If this is what you want to do, use the GNU Lesser General
Public License instead of this License.

```

### index.js

```js
import { connect } from "cloudflare:sockets";

/**
 * LAST UPDATE
 *  - Sat, 21 February 2026, 04:20 UTC.
 *    https://github.com/NiREvil/zizifn
 *
 * UUID
 *  - Generate your own uuid: https://www.uuidgenerator.net
 *  - Add multiple: comma-separated (uuid1, uuid2) - Line 30..
 *
 * PROXY IP LAND
 *  - An array of proxy addresses. You can add multiple proxies to the list.
 *    Example: ['proxy1.ir:8443', '1.1.1.1:443', 'proxy2.com:2053'], - Line 31.
 *  - Daily, tested proxy list:
 *    https://github.com/NiREvil/vless/blob/main/sub/ProxyIP.md
 *
 * SCAMALYTICS API
 *  - Default key is public, Line 33, 34, 45.
 *  - If you fork or expect heavy use, get your own free key:
 *    https://scamalytics.com/ip/api/enquiry?monthly_api_calls=5000
 *
 */
 
/** @type {ReturnType<typeof socks5AddressParser> | null} */
let parsedSocksCache = null;

const decodeSecure = (encoded) => atob(encoded);

const HTML_URL = "https://nirevil.github.io/zizifn/";

const Config = {
  userID: "be0ff9df-1468-41a0-8865-796d1c6800db",
  proxyIPs: ["nima.nscl.ir:443"],
  scamalytics: {
    username: "nimasecure999",
    apiKey: "ce75d58f98849753077a270e6013a036d6f4a6c562fd74c960960ae7a7087b40",
    baseUrl: "https://api12.scamalytics.com/v3/",
  },
  socks5: {
    enabled: false,
    relayMode: false,
    address: "",
  },

  /**
   * @param {{ PROXYIP: string; UUID: any; SCAMALYTICS_USERNAME: any; SCAMALYTICS_API_KEY: any; SCAMALYTICS_BASEURL: any; SOCKS5: any; SOCKS5_RELAY: string; }} env
   */
  fromEnv(env) {
    const selectedProxyIP =
      env.PROXYIP || this.proxyIPs[Math.floor(Math.random() * this.proxyIPs.length)];
    const [proxyHost, proxyPort = "443"] = selectedProxyIP.split(":");

    return {
      userID: env.UUID || this.userID,
      proxyIP: proxyHost,
      proxyPort: proxyPort,
      proxyAddress: selectedProxyIP,
      scamalytics: {
        username: env.SCAMALYTICS_USERNAME || this.scamalytics.username,
        apiKey: env.SCAMALYTICS_API_KEY || this.scamalytics.apiKey,
        baseUrl: env.SCAMALYTICS_BASEURL || this.scamalytics.baseUrl,
      },
      socks5: {
        enabled: !!env.SOCKS5,
        relayMode: env.SOCKS5_RELAY === "true" || this.socks5.relayMode,
        address: env.SOCKS5 || this.socks5.address,
      },
    };
  },
};

/**
 * @param {URL | RequestInfo<unknown, CfProperties<unknown>>} url
 */
async function safeFetch(url, options = {}, timeout = 4000) {
  const controller = new AbortController();
  const id = setTimeout(() => controller.abort(), timeout);
  try {
    const response = await fetch(url, {
      ...options,
      signal: controller.signal,
    });
    return response;
  } finally {
    clearTimeout(id);
  }
}

const CONST = {
  ED_PARAMS: { ed: 2560, eh: "Sec-WebSocket-Protocol" },
  AT_SYMBOL: "@",
  VLESS_PROTOCOL: decodeSecure("dmxlc3M="), // "vless"
  WS_READY_STATE_OPEN: 1,
  WS_READY_STATE_CLOSING: 2,
};

/**
 * Generates a random path string for WebSocket connection.
 * @param {number} length - Length of the random path part.
 * @param {string} [query] - Optional query string to append (e.g., 'ed=2048').
 * @returns {string} The generated path.
 */
function generateRandomPath(length = 28, query = "") {
  const chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
  let result = "";
  for (let i = 0; i < length; i++) {
    result += chars.charAt(Math.floor(Math.random() * chars.length));
  }
  return `/${result}${query ? `?${query}` : ""}`;
}

const CORE_PRESETS = {
  // Xray cores – Dream
  xray: {
    tls: {
      path: () => generateRandomPath(12, "ed=2048"),
      security: "tls",
      fp: "chrome",
      alpn: "http/1.1",
      extra: {},
    },
    tcp: {
      path: () => generateRandomPath(12, "ed=2560"),
      security: "none",
      fp: "chrome",
      extra: {},
    },
  },
  // Singbox cores – Freedom
  sb: {
    tls: {
      path: () => generateRandomPath(18),
      security: "tls",
      fp: "chrome",
      alpn: "http/1.1",
      extra: CONST.ED_PARAMS,
    },
    tcp: {
      path: () => generateRandomPath(18),
      security: "none",
      fp: "chrome",
      extra: CONST.ED_PARAMS,
    },
  },
};

/**
 * @param {any} tag
 * @param {string} proto
 */
function makeName(tag, proto) {
  return `${tag}-${proto.toUpperCase()}`;
}

function createVlessLink({
  userID,
  address,
  port,
  host,
  path,
  security,
  sni,
  fp,
  alpn,
  extra = {},
  name,
}) {
  const params = new URLSearchParams({
    type: decodeSecure("d3M="), // "ws"
    host,
    path,
  });

  if (security) {
    params.set("security", security);
    if (security === "tls") params.set("allowInsecure", "1");
  }

  if (sni) params.set("sni", sni);
  if (fp) params.set("fp", fp);
  if (alpn) params.set("alpn", alpn);

  for (const [k, v] of Object.entries(extra)) params.set(k, v);

  return `${CONST.VLESS_PROTOCOL}://${userID}@${address}:${port}?${params.toString()}#${encodeURIComponent(name)}`;
}

function buildLink({ core, proto, userID, hostName, address, port, tag }) {
  const p = CORE_PRESETS[core][proto];
  return createVlessLink({
    userID,
    address,
    port,
    host: hostName,
    path: p.path(),
    security: p.security,
    sni: p.security === "tls" ? hostName : undefined,
    fp: p.fp,
    alpn: p.alpn,
    extra: p.extra,
    name: makeName(tag, proto),
  });
}

const pick = (/** @type {string | any[]} */ arr) => arr[Math.floor(Math.random() * arr.length)];

/**
 * @param {Request} request
 * @param {string} core
 * @param {any} userID
 * @param {string} hostName
 * @param {{ waitUntil: (arg0: Promise<void>) => void; }} ctx
 */
async function handleIpSubscription(request, core, userID, hostName, ctx) {
  const url = new URL(request.url);
  const subName = url.searchParams.get("name");

  /**
   * Cake Subscription usage details
   * - These values create fake usage statistics for subscription clients
   * - Customize these values to display desired traffic and expiry information
   */
  const CAKE_INFO = {
    total_TB: 380, // Total traffic quota in Terabytes
    base_GB: 42000, // Base usage that's always shown (in Gigabytes)
    daily_growth_GB: 250, // Daily traffic growth (in Gigabytes) - simulates gradual usage
    expire_date: "2028-4-20", // Subscription expiry date (YYYY-MM-DD)
  };

  // Domains behind Cloudflare, fixed in the subscription links, you can add as many as you want..
  const mainDomains = [
    hostName,
    "creativecommons.org",
    "2027.victoriacross.ir",
    "www.speedtest.net",
    "sky.rethinkdns.com",
    "chat.openai.com",
    "cfip.xxxxxxxx.tk",
    "go.inmobi.com",
    "singapore.com",
    "www.visa.com",
    "www.wto.org",
    "chatgpt.com",
    "yakamoz.nscl.ir",
    "nodejs.org",
    "zzula.ir",
    "csgo.com",
    "fbi.gov",
  ];

  const httpsPorts = [443, 8443, 2053, 2083, 2087, 2096]; // Standard cloudflare TLS/HTTPS ports.
  const httpPorts = [80, 8080, 8880, 2052, 2082, 2086, 2095]; // Standard cloudflare TCP/HTTP ports.
  let links = [];
  const isPagesDeployment = hostName.endsWith(".pages.dev");

  mainDomains.forEach((domain, i) => {
    links.push(
      buildLink({
        core,
        proto: "tls",
        userID,
        hostName,
        address: domain,
        port: pick(httpsPorts),
        tag: `Domain${i + 1}`,
      }),
    );
    if (!isPagesDeployment) {
      links.push(
        buildLink({
          core,
          proto: "tcp",
          userID,
          hostName,
          address: domain,
          port: pick(httpPorts),
          tag: `Domain${i + 1}`,
        }),
      );
    }
  });

  try {
    const cache = caches.default;
    const cacheKey = new Request("https://cf-ip-cache.local");

    let response = await cache.match(cacheKey);

    if (!response) {
      const r = await safeFetch(
        "https://raw.githubusercontent.com/NiREvil/vless/refs/heads/main/Cloudflare-IPs.json",
        {},
        4000,
      );
      if (r.ok) {
        response = new Response(await r.text(), {
          headers: {
            "Cache-Control": "public, max-age=86400",
          },
        });
        ctx.waitUntil(cache.put(cacheKey, response.clone()));
      }
    }

    if (response) {
      const json = await response.json();
      const ips = [...(json.ipv4 || []), ...(json.ipv6 || [])].slice(0, 20).map((x) => x.ip);

      ips.forEach((ip, i) => {
        const formattedAddress = ip.includes(":") ? `[${ip}]` : ip;
        links.push(
          buildLink({
            core,
            proto: "tls",
            userID,
            hostName,
            address: formattedAddress,
            port: pick(httpsPorts),
            tag: `IP${i + 1}`,
          }),
        );
        if (!isPagesDeployment) {
          links.push(
            buildLink({
              core,
              proto: "tcp",
              userID,
              hostName,
              address: formattedAddress,
              port: pick(httpPorts),
              tag: `IP${i + 1}`,
            }),
          );
        }
      });
    }
  } catch (e) {
    console.error("Cached IP fetch failed", e);
  }

  // Creating cake information headers
  const GB_in_bytes = 1024 * 1024 * 1024;
  const TB_in_bytes = 1024 * GB_in_bytes;
  const total_bytes = CAKE_INFO.total_TB * TB_in_bytes;
  const base_bytes = CAKE_INFO.base_GB * GB_in_bytes;
  // Calculating "dynamic" consumption based on hours per day
  const now = new Date();
  const hours_passed = now.getHours() + now.getMinutes() / 60;
  const daily_growth_bytes = (hours_passed / 24) * (CAKE_INFO.daily_growth_GB * GB_in_bytes);
  // Splitting usage between upload and download
  const cake_download = base_bytes + daily_growth_bytes / 2;
  const cake_upload = base_bytes + daily_growth_bytes / 2;
  // Convert expiration date to Unix Timestamp
  const expire_timestamp = Math.floor(new Date(CAKE_INFO.expire_date).getTime() / 1000);
  const subInfo = `upload=${Math.round(cake_upload)}; download=${Math.round(cake_download)}; total=${total_bytes}; expire=${expire_timestamp}`;

  const headers = {
    "Content-Type": "text/plain;charset=utf-8",
    "Profile-Update-Interval": "6",
    "Subscription-Userinfo": subInfo,
  };

  if (subName) headers["Profile-Title"] = subName;
  return new Response(btoa(links.join("\n")), { headers });
}

/**
 * Core vless protocol logic
 * Handles VLESS protocol over WebSocket.
 * @param {Request} request
 * @param {object} config
 * @returns {Promise<Response>}
 */
async function ProtocolOverWSHandler(request, config) {
  const webSocketPair = new WebSocketPair();
  const [client, webSocket] = Object.values(webSocketPair);
  webSocket.accept();
  let address = "";
  let portWithRandomLog = "";
  let udpStreamWriter = null;
  const log = (info, event) => {
    console.log(`[${address}:${portWithRandomLog}] ${info}`, event || "");
  };
  const earlyDataHeader = request.headers.get("Sec-WebSocket-Protocol") || "";
  const readableWebSocketStream = MakeReadableWebSocketStream(webSocket, earlyDataHeader, log);
  let remoteSocketWapper = { value: null };

  readableWebSocketStream
    .pipeTo(
      new WritableStream({
        async write(chunk, controller) {
          if (udpStreamWriter) return udpStreamWriter.write(chunk);
          if (remoteSocketWapper.value) {
            const writer = remoteSocketWapper.value.writable.getWriter();
            await writer.write(chunk);
            writer.releaseLock();
            return;
          }

          const {
            hasError,
            message,
            addressType,
            portRemote = 443,
            addressRemote = "",
            rawDataIndex,
            ProtocolVersion = new Uint8Array([0, 0]),
            isUDP,
          } = ProcessProtocolHeader(chunk, config.userID);
          address = addressRemote;
          portWithRandomLog = `${portRemote}--${Math.random()} ${isUDP ? "udp" : "tcp"} `;

          if (hasError) throw new Error(message);

          const vlessResponseHeader = new Uint8Array([ProtocolVersion[0], 0]);
          const rawClientData = chunk.slice(rawDataIndex);

          if (isUDP) {
            if (portRemote === 53) {
              const dnsPipeline = await createDnsPipeline(webSocket, vlessResponseHeader, log);
              udpStreamWriter = dnsPipeline.write;
              udpStreamWriter(rawClientData);
            } else {
              throw new Error("UDP proxy is only enabled for DNS (port 53)");
            }
            return;
          }

          HandleTCPOutBound(
            remoteSocketWapper,
            addressType,
            addressRemote,
            portRemote,
            rawClientData,
            webSocket,
            vlessResponseHeader,
            log,
            config,
          );
        },
        close() {
          log(`readableWebSocketStream closed`);
        },
        abort(err) {
          log(`readableWebSocketStream aborted`, err);
        },
      }),
    )
    .catch((err) => {
      console.error("Pipeline failed:", err.stack || err);
    });

  return new Response(null, { status: 101, webSocket: client });
}

/**
 * @param {string} uuid
 */
function isValidUUID(uuid) {
  const uuidRegex = /^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i;
  return uuidRegex.test(uuid);
}

/**
 * Helper function to randomize uppercase and lowercase letters in a string
 * @param {string} str Input string (like SNI)
 * @returns {string} String with random characters
 */
function randomizeCase(str) {
  let result = "";
  // 50% chance of making a big deal out of it.
  for (let i = 0; i < str.length; i++)
    result += Math.random() < 0.5 ? str[i].toUpperCase() : str[i].toLowerCase();
  return result;
}

/**
 * Handles TCP outbound logic for VLESS.
 * @param {{ value: any; }} remoteSocket
 * @param {number} addressType
 * @param {string} addressRemote
 * @param {number} portRemote
 * @param {any} rawClientData
 * @param {WebSocket} webSocket
 * @param {Uint8Array} protocolResponseHeader
 * @param {{ (info: any, event: any): void; (arg0: string): void; }} log
 * @param {{ socks5Relay: any; parsedSocks5Address: any; enableSocks: any; proxyIP: any; proxyPort: any; userID?: string; socks5Address?: string; }} config
 */
async function HandleTCPOutBound(
  remoteSocket,
  addressType,
  addressRemote,
  portRemote,
  rawClientData,
  webSocket,
  protocolResponseHeader,
  log,
  config,
) {
  async function connectAndWrite(address, port, socks = false) {
    let tcpSocket;
    if (config.socks5Relay) {
      tcpSocket = await socks5Connect(addressType, address, port, log, config.parsedSocks5Address);
    } else {
      tcpSocket = socks
        ? await socks5Connect(addressType, address, port, log, config.parsedSocks5Address)
        : connect({ hostname: address, port: port });
    }
    remoteSocket.value = tcpSocket;
    log(`connected to ${address}:${port}`);
    const writer = tcpSocket.writable.getWriter();
    await writer.write(rawClientData);
    writer.releaseLock();
    return tcpSocket;
  }

  async function retry() {
    const tcpSocket = config.enableSocks
      ? await connectAndWrite(addressRemote, portRemote, true)
      : await connectAndWrite(
          config.proxyIP || addressRemote,
          config.proxyPort || portRemote,
          false,
        );

    tcpSocket.closed
      .catch((error) => console.log("retry tcpSocket closed error", error))
      .finally(() => safeCloseWebSocket(webSocket));
    RemoteSocketToWS(tcpSocket, webSocket, protocolResponseHeader, null, log);
  }

  const tcpSocket = await connectAndWrite(addressRemote, portRemote);
  RemoteSocketToWS(tcpSocket, webSocket, protocolResponseHeader, retry, log);
}

/**
 * Converts WebSocket messages to a readable stream.
 * @param {WebSocket} webSocketServer
 * @param {string} earlyDataHeader
 * @param {{ (info: any, event: any): void; (arg0: string): void; }} log
 */
function MakeReadableWebSocketStream(webSocketServer, earlyDataHeader, log) {
  return new ReadableStream({
    start(controller) {
      webSocketServer.addEventListener("message", (event) => controller.enqueue(event.data));
      webSocketServer.addEventListener("close", () => {
        safeCloseWebSocket(webSocketServer);
        controller.close();
      });
      webSocketServer.addEventListener("error", (err) => {
        log("webSocketServer has error");
        controller.error(err);
      });
      const { earlyData, error } = base64ToArrayBuffer(earlyDataHeader);
      if (error) controller.error(error);
      else if (earlyData) controller.enqueue(earlyData);
    },
    pull(_controller) {},
    cancel(reason) {
      log(`ReadableStream was canceled, due to ${reason}`);
      safeCloseWebSocket(webSocketServer);
    },
  });
}

function ProcessProtocolHeader(protocolBuffer, userID) {
  if (protocolBuffer.byteLength < 24) return { hasError: true, message: "invalid data" };
  const dataView = new DataView(protocolBuffer);
  const version = dataView.getUint8(0);
  const slicedBufferString = stringify(new Uint8Array(protocolBuffer.slice(1, 17)));
  const uuids = userID.split(",").map((id) => id.trim());
  if (!slicedBufferString) return { hasError: true, message: "invalid uuid format" };
  const isValidUser = uuids.some((uuid) => slicedBufferString === uuid);
  if (!isValidUser) return { hasError: true, message: "invalid user" };

  const optLength = dataView.getUint8(17);
  const command = dataView.getUint8(18 + optLength);
  if (command !== 1 && command !== 2)
    return { hasError: true, message: `command ${command} is not supported` };

  const portIndex = 18 + optLength + 1;
  const portRemote = dataView.getUint16(portIndex);
  const addressType = dataView.getUint8(portIndex + 2);
  let addressValue, addressLength, addressValueIndex;

  switch (addressType) {
    case 1: // IPv4
      addressLength = 4;
      addressValueIndex = portIndex + 3;
      addressValue = new Uint8Array(
        protocolBuffer.slice(addressValueIndex, addressValueIndex + addressLength),
      ).join(".");
      break;
    case 2: // Domain
      addressLength = dataView.getUint8(portIndex + 3);
      addressValueIndex = portIndex + 4;
      addressValue = new TextDecoder().decode(
        protocolBuffer.slice(addressValueIndex, addressValueIndex + addressLength),
      );
      break;
    case 3: // IPv6
      addressLength = 16;
      addressValueIndex = portIndex + 3;
      addressValue = Array.from({ length: 8 }, (_, i) =>
        dataView.getUint16(addressValueIndex + i * 2).toString(16),
      ).join(":");
      break;
    default:
      return { hasError: true, message: `invalid addressType: ${addressType}` };
  }

  if (!addressValue)
    return { hasError: true, message: `addressValue is empty, addressType is ${addressType}` };

  return {
    hasError: false,
    addressRemote: addressValue,
    addressType,
    portRemote,
    rawDataIndex: addressValueIndex + addressLength,
    ProtocolVersion: new Uint8Array([version]),
    isUDP: command === 2,
  };
}

/**
 * Pipes remote socket data to WebSocket.
 * @param {Socket} remoteSocket
 * @param {WebSocket} webSocket
 * @param {string | Uint8Array | ArrayBuffer | ArrayBufferView | Blob} protocolResponseHeader
 * @param {{ (): Promise<void>; (): any; }} retry
 * @param {{ (info: any, event: any): void; (arg0: string): void; (info: any, event: any): void; (arg0: string): void; (arg0: string): void; }} log
 */
async function RemoteSocketToWS(remoteSocket, webSocket, protocolResponseHeader, retry, log) {
  let hasIncomingData = false;
  try {
    await remoteSocket.readable.pipeTo(
      new WritableStream({
        async write(chunk) {
          if (webSocket.readyState !== CONST.WS_READY_STATE_OPEN)
            throw new Error("WebSocket is not open");
          hasIncomingData = true;
          const dataToSend = protocolResponseHeader
            ? await new Blob([protocolResponseHeader, chunk]).arrayBuffer()
            : chunk;
          webSocket.send(dataToSend);
          protocolResponseHeader = null;
        },
        close() {
          log(`Remote connection readable closed.`);
        },
        abort(reason) {
          console.error(`Remote connection readable aborted:`, reason);
        },
      }),
    );
  } catch (error) {
    console.error(`RemoteSocketToWS error:`, error.stack || error);
    safeCloseWebSocket(webSocket);
  }
  if (!hasIncomingData && retry) {
    log(`No incoming data, retrying`);
    await retry();
  }
}

/**
 * decodes base64 string to ArrayBuffer.
 * @param {string} base64Str
 */
function base64ToArrayBuffer(base64Str) {
  if (!base64Str) return { earlyData: null, error: null };
  try {
    const binaryStr = atob(base64Str.replace(/-/g, "+").replace(/_/g, "/"));
    const buffer = new ArrayBuffer(binaryStr.length);
    const view = new Uint8Array(buffer);
    for (let i = 0; i < binaryStr.length; i++) view[i] = binaryStr.charCodeAt(i);
    return { earlyData: buffer, error: null };
  } catch (error) {
    return { earlyData: null, error };
  }
}

/**
 * Safely closes a WebSocket connection.
 * @param {{ readyState: number; close: () => void; }} socket
 */
function safeCloseWebSocket(socket) {
  try {
    if (
      socket.readyState === CONST.WS_READY_STATE_OPEN ||
      socket.readyState === CONST.WS_READY_STATE_CLOSING
    )
      socket.close();
  } catch (error) {
    console.error("safeCloseWebSocket error:", error);
  }
}

const byteToHex = Array.from({ length: 256 }, (_, i) => (i + 0x100).toString(16).slice(1));

/*
 * @param {Uint8Array | (string | number)[]} arr
 */
function unsafeStringify(arr, offset = 0) {
  return (
    byteToHex[arr[offset]] +
    byteToHex[arr[offset + 1]] +
    byteToHex[arr[offset + 2]] +
    byteToHex[arr[offset + 3]] +
    "-" +
    byteToHex[arr[offset + 4]] +
    byteToHex[arr[offset + 5]] +
    "-" +
    byteToHex[arr[offset + 6]] +
    byteToHex[arr[offset + 7]] +
    "-" +
    byteToHex[arr[offset + 8]] +
    byteToHex[arr[offset + 9]] +
    "-" +
    byteToHex[arr[offset + 10]] +
    byteToHex[arr[offset + 11]] +
    byteToHex[arr[offset + 12]] +
    byteToHex[arr[offset + 13]] +
    byteToHex[arr[offset + 14]] +
    byteToHex[arr[offset + 15]]
  ).toLowerCase();
}

/*
 * @param {Uint8Array} arr
 */
function stringify(arr, offset = 0) {
  const uuid = unsafeStringify(arr, offset);
  return isValidUUID(uuid) ? uuid : "";
}

/**
 * DNS pipeline for UDP DNS requests, using DNS-over-HTTPS, (REvil Method).
 * @param {WebSocket} webSocket
 * @param {Uint8Array} vlessResponseHeader
 * @param {Function} log
 * @returns {Promise<{write: Function}>}
 */
async function createDnsPipeline(webSocket, vlessResponseHeader, log) {
  let isHeaderSent = false;
  const transformStream = new TransformStream({
    transform(chunk, controller) {
      // Parse UDP packets from VLESS framing
      for (let index = 0; index < chunk.byteLength; ) {
        const lengthBuffer = chunk.slice(index, index + 2);
        const udpPacketLength = new DataView(lengthBuffer).getUint16(0);
        const udpData = new Uint8Array(chunk.slice(index + 2, index + 2 + udpPacketLength));
        index = index + 2 + udpPacketLength;
        controller.enqueue(udpData);
      }
    },
  });

  transformStream.readable
    .pipeTo(
      new WritableStream({
        async write(chunk) {
          try {
            // Send DNS query using DoH
            const resp = await safeFetch(
              `https://1.1.1.1/dns-query`,
              {
                method: "POST",
                headers: { "content-type": "application/dns-message" },
                body: chunk,
              },
              3000,
            );
            const dnsQueryResult = await resp.arrayBuffer();
            const udpSize = dnsQueryResult.byteLength;
            const udpSizeBuffer = new Uint8Array([(udpSize >> 8) & 0xff, udpSize & 0xff]);

            if (webSocket.readyState === CONST.WS_READY_STATE_OPEN) {
              if (isHeaderSent) {
                webSocket.send(await new Blob([udpSizeBuffer, dnsQueryResult]).arrayBuffer());
              } else {
                webSocket.send(
                  await new Blob([
                    vlessResponseHeader,
                    udpSizeBuffer,
                    dnsQueryResult,
                  ]).arrayBuffer(),
                );
                isHeaderSent = true;
              }
            }
          } catch (error) {
            log("DNS query error: " + error);
          }
        },
      }),
    )
    .catch((e) => log("DNS stream error: " + e));

  const writer = transformStream.writable.getWriter();
  return {
    write: (/** @type {any} */ chunk) => writer.write(chunk),
  };
}

/**
 * SOCKS5 TCP connection logic.
 * @param {any} addressType
 * @param {string} addressRemote
 * @param {number} portRemote
 * @param {any} log
 * @param {{ username: any; password: any; hostname: any; port: any; }} parsedSocks5Addr
 */
async function socks5Connect(addressType, addressRemote, portRemote, log, parsedSocks5Addr) {
  const { username, password, hostname, port } = parsedSocks5Addr;
  const socket = connect({ hostname, port });
  const writer = socket.writable.getWriter();
  const reader = socket.readable.getReader();
  const encoder = new TextEncoder();

  await writer.write(new Uint8Array([5, 2, 0, 2]));
  let res = (await reader.read()).value;
  if (res[0] !== 0x05 || res[1] === 0xff) throw new Error("SOCKS5 server connection failed.");

  if (res[1] === 0x02) {
    if (!username || !password) throw new Error("SOCKS5 auth credentials not provided.");
    const authRequest = new Uint8Array([
      1,
      username.length,
      ...encoder.encode(username),
      password.length,
      ...encoder.encode(password),
    ]);
    await writer.write(authRequest);
    res = (await reader.read()).value;
    if (res[0] !== 0x01 || res[1] !== 0x00) throw new Error("SOCKS5 authentication failed.");
  }

  let DSTADDR;
  switch (addressType) {
    case 1:
      DSTADDR = new Uint8Array([1, ...addressRemote.split(".").map(Number)]);
      break;
    case 2:
      DSTADDR = new Uint8Array([3, addressRemote.length, ...encoder.encode(addressRemote)]);
      break;
    case 3:
      DSTADDR = new Uint8Array([
        4,
        ...addressRemote
          .split(":")
          .flatMap((x) => [parseInt(x.slice(0, 2), 16), parseInt(x.slice(2), 16)]),
      ]);
      break;
    default:
      throw new Error(`Invalid addressType for SOCKS5: ${addressType}`);
  }

  const socksRequest = new Uint8Array([5, 1, 0, ...DSTADDR, portRemote >> 8, portRemote & 0xff]);

  await writer.write(socksRequest); // SOCKS5 greeting
  res = (await reader.read()).value;
  if (res[1] !== 0x00) throw new Error("Failed to open SOCKS5 connection.");

  writer.releaseLock();
  reader.releaseLock();
  return socket;
}

/**
 * Parses SOCKS5 address string.
 * @param {string} address
 * @returns {object}
 */
function socks5AddressParser(address) {
  try {
    const [authPart, hostPart] = address.includes("@") ? address.split("@") : [null, address];
    const [hostname, portStr] = hostPart.split(":");
    const port = parseInt(portStr, 10);
    if (!hostname || isNaN(port)) throw new Error();
    let username, password;
    if (authPart) {
      [username, password] = authPart.split(":");
      if (!username) throw new Error();
    }
    return { username, password, hostname, port };
  } catch {
    throw new Error("Invalid SOCKS5 address format.");
  }
}

async function handleScamalyticsLookup(request, config) {
  const url = new URL(request.url);
  const ipToLookup = url.searchParams.get("ip");
  if (!ipToLookup)
    return new Response(JSON.stringify({ error: "Missing IP" }), {
      status: 400,
      headers: { "Content-Type": "application/json" },
    });

  const { username, apiKey, baseUrl } = config.scamalytics;
  if (!username || !apiKey)
    return new Response(JSON.stringify({ error: "Scamalytics API not configured" }), {
      status: 500,
      headers: { "Content-Type": "application/json" },
    });

  const scamalyticsUrl = `${baseUrl}${username}/?key=${apiKey}&ip=${ipToLookup}`;
  const headers = new Headers({
    "Content-Type": "application/json",
    "Access-Control-Allow-Origin": "*",
  });

  try {
    const scamalyticsResponse = await fetch(scamalyticsUrl);
    const responseBody = await scamalyticsResponse.json();
    return new Response(JSON.stringify(responseBody), { headers });
  } catch (error) {
    return new Response(JSON.stringify({ error: error.toString() }), { status: 500, headers });
  }
}

async function handleConfigPage(userID, hostName, proxyAddress) {
  const dream = buildLink({
    core: "xray",
    proto: "tls",
    userID,
    hostName,
    address: hostName,
    port: 443,
    tag: `${hostName}-Xray`,
  });
  const freedom = buildLink({
    core: "sb",
    proto: "tls",
    userID,
    hostName,
    address: hostName,
    port: 443,
    tag: `${hostName}-Singbox`,
  });

  const encodedSubName = encodeURIComponent("INDEX");
  const subXrayUrl = `https://${hostName}/xray/${userID}?name=${encodedSubName}`;
  const subSbUrl = `https://${hostName}/sb/${userID}?name=${encodedSubName}`;

  try {
    const response = await safeFetch(HTML_URL);
    if (!response.ok) throw new Error(`Failed to load HTML from GitHub Pages: ${response.status}`);

    let finalHTML = await response.text();

    finalHTML = finalHTML
      .replace(/{{PROXY_ADDRESS}}/g, proxyAddress)
      .replace(/{{CONFIG_DREAM}}/g, dream)
      .replace(/{{CONFIG_FREEDOM}}/g, freedom)
      .replace(/{{URL_HIDDIFY}}/g, `hiddify://install-config?url=${encodeURIComponent(subXrayUrl)}`)
      .replace(
        /{{URL_V2RAYNG}}/g,
        `v2rayng://install-config?url=${encodeURIComponent(subXrayUrl)}#${encodedSubName}`,
      )
      .replace(
        /{{URL_CLASH}}/g,
        `clash://install-config?url=${encodeURIComponent(`https://revil-sub.pages.dev/sub/clash-meta?url=${subSbUrl}`)}`,
      )
      .replace(
        /{{URL_EXCLAVE}}/g,
        `sn://subscription?url=${encodeURIComponent(subSbUrl)}&name=${encodedSubName}`,
      );

    return new Response(finalHTML, { headers: { "Content-Type": "text/html; charset=utf-8" } });
  } catch (error) {
    return new Response(`Error rendering panel: ${error.message}`, {
      status: 500,
      headers: { "Content-Type": "text/plain" },
    });
  }
}

export default {
  /**
   * @param {Request<any, CfProperties<any>>} request
   * @param {{ PROXYIP: string; UUID: any; SCAMALYTICS_USERNAME: any; SCAMALYTICS_API_KEY: any; SCAMALYTICS_BASEURL: any; SOCKS5: any; SOCKS5_RELAY: string; }} env
   * @param {any} ctx
   */
  async fetch(request, env, ctx) {
    try {
      const cfg = Config.fromEnv(env);
      const url = new URL(request.url);

      const upgradeHeader = request.headers.get("Upgrade");
      if (upgradeHeader && upgradeHeader.toLowerCase() === "websocket") {
        if (cfg.socks5.enabled && !parsedSocksCache) {
          parsedSocksCache = socks5AddressParser(cfg.socks5.address);
        }

        const requestConfig = {
          userID: cfg.userID,
          proxyIP: cfg.proxyIP,
          proxyPort: cfg.proxyPort,
          socks5Address: cfg.socks5.address,
          socks5Relay: cfg.socks5.relayMode,
          enableSocks: cfg.socks5.enabled,
          parsedSocks5Address: cfg.socks5.enabled ? parsedSocksCache : {},
        };
        return ProtocolOverWSHandler(request, requestConfig);
      }

      if (url.pathname === "/scamalytics-lookup") return handleScamalyticsLookup(request, cfg);
      if (url.pathname.startsWith(`/xray/${cfg.userID}`))
        return handleIpSubscription(request, "xray", cfg.userID, url.hostname, ctx);
      if (url.pathname.startsWith(`/sb/${cfg.userID}`))
        return handleIpSubscription(request, "sb", cfg.userID, url.hostname, ctx);
      if (url.pathname.startsWith(`/${cfg.userID}`))
        return handleConfigPage(cfg.userID, url.hostname, cfg.proxyAddress);

      return new Response(
        "UUID not found. Please set the UUID environment variable in the Cloudflare dashboard.",
        { status: 404 },
      );
    } catch (err) {
      return new Response(`Worker Logic Error: ${err.message}\n${err.stack}`, {
        status: 500,
        headers: { "Content-Type": "text/plain" },
      });
    }
  },
};

```

### wrangler.toml

```toml
name = "spring"
main = "index.js"
compatibility_date = "2025-11-09"
workers_dev = true
preview_urls = true
# logpush job is setup to capture from envs ending: log, pro, or one
logpush = false
# use node_compat or custom build
# node_compat = true
send_metrics = false
minify = false

[vars]
UUID = "d342d11e-d424-4583-b36e-524ab1f0afa4"
PROXYIP = "nima.nscl.ir"

# [build]
# command = "npm run deploy"

# [[rules]]
# type = "ESModule"
# globs = ["**/*.js"]

```

### index.html

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>VLESS Proxy Configuration</title>
  <link rel="icon" href="https://raw.githubusercontent.com/NiREvil/zizifn/refs/heads/Legacy/assets/raven-1.png" type="image/png">
  <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@300..700&display=swap" rel="stylesheet">
  <style>
    * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  @font-face {
    font-family: "Aldine 401 BT Web";
    src: url("https://pub-7a3b428c76aa411181a0f4dd7fa9064b.r2.dev/Aldine401_Mersedeh.woff2") format("woff2");
    font-weight: 400; font-style: normal; font-display: swap;
  }
  @font-face {
    font-family: "Styrene B LC";
    src: url("https://pub-7a3b428c76aa411181a0f4dd7fa9064b.r2.dev/StyreneBLC-Regular.woff2") format("woff2");
    font-weight: 400; font-style: normal; font-display: swap;
  }
  @font-face {
    font-family: "Styrene B LC";
    src: url("https://pub-7a3b428c76aa411181a0f4dd7fa9064b.r2.dev/StyreneBLC-Medium.woff2") format("woff2");
    font-weight: 500; font-style: normal; font-display: swap;
  }

  :root {
    --background-primary: #2a2421; --background-secondary: #35302c; --background-tertiary: #413b35;
    --border-color: #5a4f45; --border-color-hover: #766a5f; --text-primary: #e5dfd6; --text-secondary: #b3a89d;
    --text-accent: #ffffff; --accent-primary: #be9b7b; --accent-secondary: #d4b595; --accent-tertiary: #8d6e5c;
    --accent-primary-darker: #8a6f56; --button-text-primary: #2a2421; --button-text-secondary: var(--text-primary);
    --shadow-color: rgba(0, 0, 0, 0.35); --shadow-color-accent: rgba(190, 155, 123, 0.4);
    --border-radius: 8px; --transition-speed: 0.2s; --transition-speed-fast: 0.1s; --transition-speed-medium: 0.3s; --transition-speed-long: 0.6s;
    --status-success: #70b570; --status-error: #e05d44; --status-warning: #e0bc44; --status-info: #4f90c4;
    --serif: "Aldine 401 BT Web", "Times New Roman", Times, Georgia, ui-serif, serif;
    --sans-serif: "Styrene B LC", -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, "Noto Color Emoji", sans-serif;
    --mono-serif: "Fira Code", Cantarell, "Courier Prime", monospace;
  }

  body {
    font-family: var(--sans-serif); font-size: 16px; font-weight: 400; font-style: normal;
    background-color: var(--background-primary); color: var(--text-primary);
    padding: 3rem; line-height: 1.5; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale;
  }

  .container {
    max-width: 800px; margin: 20px auto; padding: 0 12px; border-radius: var(--border-radius);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2), 0 0 25px 8px var(--shadow-color-accent);
    transition: box-shadow var(--transition-speed-medium) ease;
  }

  .container:hover { box-shadow: 0 8px 20px rgba(0, 0, 0, 0.25), 0 0 35px 10px var(--shadow-color-accent); }
  .header { text-align: center; margin-bottom: 40px; padding-top: 30px; }
  .header h1 { font-family: var(--serif); font-weight: 400; font-size: 2rem; color: var(--text-accent); margin-top: 0px; margin-bottom: 2px; }
  .header p { color: var(--text-secondary); font-size: 0.8rem; font-weight: 400; }
  .config-card {
    background: var(--background-secondary); border-radius: var(--border-radius); padding: 20px; margin-bottom: 24px;
    border: 1px solid var(--border-color);
    transition: border-color var(--transition-speed) ease, box-shadow var(--transition-speed) ease;
  }

  .config-card:hover { border-color: var(--border-color-hover); box-shadow: 0 4px 8px var(--shadow-color); }
  .config-title {
    font-family: var(--serif); font-size: 1.4rem; font-weight: 400; color: var(--accent-secondary);
    margin-bottom: 16px; padding-bottom: 13px; border-bottom: 1px solid var(--border-color);
    display: flex; align-items: center; justify-content: space-between;
  }

  .config-title .refresh-btn {
    position: relative; overflow: hidden; display: flex; align-items: center; gap: 4px;
    font-family: var(--serif); font-size: 12px; padding: 6px 12px; border-radius: 6px;
    color: var(--accent-secondary); background-color: var(--background-tertiary); border: 1px solid var(--border-color);
    cursor: pointer;
    transition: background-color var(--transition-speed) ease, border-color var(--transition-speed) ease, color var(--transition-speed) ease, transform var(--transition-speed) ease, box-shadow var(--transition-speed) ease;
  }

  .config-title .refresh-btn::before {
    content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 100%;
    background: linear-gradient(120deg, transparent, rgba(255, 255, 255, 0.2), transparent);
    transform: translateX(-100%); transition: transform var(--transition-speed-long) ease; z-index: 1;
  }

  .config-title .refresh-btn:hover {
    letter-spacing: 0.5px; font-weight: 600; background-color: #4d453e; color: var(--accent-primary);
    border-color: var(--border-color-hover); transform: translateY(-2px); box-shadow: 0 4px 8px var(--shadow-color);
  }

  .config-title .refresh-btn:hover::before { transform: translateX(100%); }
  .config-title .refresh-btn:active { transform: translateY(0px) scale(0.98); box-shadow: none; }
  .refresh-icon { width: 12px; height: 12px; stroke: currentColor; }
  .config-content {
    position: relative; background: var(--background-tertiary); border-radius: var(--border-radius);
    padding: 16px; margin-bottom: 20px; border: 1px solid var(--border-color);
  }

  .config-content pre {
    overflow-x: auto; font-family: var(--mono-serif); font-size: 12px; color: var(--text-primary);
    margin: 0; white-space: pre-wrap; word-break: break-all;
  }

  .button {
    display: inline-flex; align-items: center; justify-content: center; gap: 8px;
    padding: 8px 16px; border-radius: var(--border-radius); font-size: 15px; font-weight: 500;
    cursor: pointer; border: 1px solid var(--border-color); background-color: var(--background-tertiary);
    color: var(--button-text-secondary);
    transition: background-color var(--transition-speed) ease, border-color var(--transition-speed) ease, color var(--transition-speed) ease, transform var(--transition-speed) ease, box-shadow var(--transition-speed) ease;
    -webkit-tap-highlight-color: transparent; touch-action: manipulation; text-decoration: none; overflow: hidden; z-index: 1;
  }

  .button:focus-visible { outline: 2px solid var(--accent-primary); outline-offset: 2px; }
  .button:disabled { opacity: 0.6; cursor: not-allowed; transform: none; box-shadow: none; transition: opacity var(--transition-speed) ease; }
  .copy-buttons {
    position: relative; display: flex; gap: 4px; overflow: hidden; align-self: center;
    font-family: var(--serif); font-size: 13px; padding: 6px 12px; border-radius: 6px;
    color: var(--accent-secondary); border: 1px solid var(--border-color);
    transition: background-color var(--transition-speed) ease, border-color var(--transition-speed) ease, color var(--transition-speed) ease, transform var(--transition-speed) ease, box-shadow var(--transition-speed) ease;
  }

  .copy-buttons::before, .client-btn::before {
    content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 100%;
    background: linear-gradient(120deg, transparent, rgba(255, 255, 255, 0.2), transparent);
    transform: translateX(-100%); transition: transform var(--transition-speed-long) ease; z-index: -1;
  }

  .copy-buttons:hover::before, .client-btn:hover::before { transform: translateX(100%); }
  .copy-buttons:hover {
    background-color: #4d453e; letter-spacing: 0.5px; font-weight: 600;
    border-color: var(--border-color-hover); transform: translateY(-2px); box-shadow: 0 4px 8px var(--shadow-color);
  }

  .copy-buttons:active { transform: translateY(0px) scale(0.98); box-shadow: none; }
  .copy-icon { width: 12px; height: 12px; stroke: currentColor; }
  .client-buttons { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 12px; margin-top: 16px; }
  .client-btn {
    width: 100%; background-color: var(--accent-primary); color: var(--background-tertiary);
    border-radius: 6px; border-color: var(--accent-primary-darker); position: relative; overflow: hidden;
    transition: all 0.3s cubic-bezier(0.2, 0.8, 0.2, 1); box-shadow: 0 2px 5px rgba(0, 0, 0, 0.15);
  }

  .client-btn::after {
    content: ''; position: absolute; bottom: -5px; left: 0; width: 100%; height: 5px;
    background: linear-gradient(90deg, var(--accent-tertiary), var(--accent-secondary));
    opacity: 0; transition: all 0.3s ease; z-index: 0;
  }

  .client-btn:hover {
    text-transform: uppercase; letter-spacing: 0.3px; transform: translateY(-3px);
    background-color: var(--accent-secondary); color: var(--button-text-primary);
    box-shadow: 0 5px 15px rgba(190, 155, 123, 0.5); border-color: var(--accent-secondary);
  }

  .client-btn:hover::after { opacity: 1; bottom: 0; }
  .client-btn:active { transform: translateY(0) scale(0.98); box-shadow: 0 2px 3px rgba(0, 0, 0, 0.2); background-color: var(--accent-primary-darker); }
  .client-btn .client-icon { position: relative; z-index: 2; transition: transform 0.3s ease; }
  .client-btn:hover .client-icon { transform: rotate(15deg) scale(1.1); }
  .client-btn .button-text { position: relative; z-index: 2; transition: letter-spacing 0.3s ease; }
  .client-btn:hover .button-text { letter-spacing: 0.5px; }
  .client-icon { width: 18px; height: 18px; border-radius: 6px; background-color: var(--background-secondary); display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .client-icon svg { width: 14px; height: 14px; fill: var(--accent-secondary); }
  .button.copied { background-color: var(--accent-secondary) !important; color: var(--background-tertiary) !important; }
  .button.error { background-color: #c74a3b !important; color: var(--text-accent) !important; }

  .footer { text-align: center; margin-top: 20px; padding-bottom: 40px; color: var(--text-secondary); font-size: 12px; }
  .footer p { margin-bottom: 0px; }

  ::-webkit-scrollbar { width: 8px; height: 8px; }
  ::-webkit-scrollbar-track { background: var(--background-primary); border-radius: 4px; }
  ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 4px; border: 2px solid var(--background-primary); }
  ::-webkit-scrollbar-thumb:hover { background: var(--border-color-hover); }
  * { scrollbar-width: thin; scrollbar-color: var(--border-color) var(--background-primary); }

  .ip-info-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(230px, 1fr)); gap: 24px; }
  .ip-info-section { background-color: var(--background-tertiary); border-radius: var(--border-radius); padding: 16px; border: 1px solid var(--border-color); display: flex; flex-direction: column; gap: 20px; }
  .ip-info-header { display: flex; align-items: center; gap: 10px; border-bottom: 1px solid var(--border-color); padding-bottom: 10px; }
  .ip-info-header svg { width: 20px; height: 20px; stroke: var(--accent-secondary); }
  .ip-info-header h3 { font-family: var(--serif); font-size: 18px; font-weight: 400; color: var(--accent-secondary); margin: 0; }
  .ip-info-content { display: flex; flex-direction: column; gap: 10px; }
  .ip-info-item { display: flex; flex-direction: column; gap: 2px; }
  .ip-info-item .label { font-size: 11px; color: var(--text-secondary); text-transform: uppercase; letter-spacing: 0.5px; }
  .ip-info-item .value { font-size: 14px; color: var(--text-primary); word-break: break-all; line-height: 1.4; }

  .badge { display: inline-flex; align-items: center; justify-content: center; padding: 3px 8px; border-radius: 12px; font-size: 11px; font-weight: 500; text-transform: uppercase; letter-spacing: 0.5px; }
  .badge-yes { background-color: rgba(112, 181, 112, 0.15); color: var(--status-success); border: 1px solid rgba(112, 181, 112, 0.3); }
  .badge-no { background-color: rgba(224, 93, 68, 0.15); color: var(--status-error); border: 1px solid rgba(224, 93, 68, 0.3); }
  .badge-neutral { background-color: rgba(79, 144, 196, 0.15); color: var(--status-info); border: 1px solid rgba(79, 144, 196, 0.3); }
  .badge-warning { background-color: rgba(224, 188, 68, 0.15); color: var(--status-warning); border: 1px solid rgba(224, 188, 68, 0.3); }
  .skeleton { display: block; background: linear-gradient(90deg, var(--background-tertiary) 25%, var(--background-secondary) 50%, var(--background-tertiary) 75%); background-size: 200% 100%; animation: loading 1.5s infinite; border-radius: 4px; height: 16px; }
  @keyframes loading { 0% { background-position: 200% 0; } 100% { background-position: -200% 0; } }
  .country-flag { display: inline-block; width: 18px; height: auto; max-height: 14px; margin-right: 6px; vertical-align: middle; border-radius: 2px; }

  .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.7); display: flex; align-items: center; justify-content: center; z-index: 1000; opacity: 0; visibility: hidden; transition: opacity 0.3s ease, visibility 0.3s ease; }
  .modal-overlay.visible { opacity: 1; visibility: visible; }
  .modal-overlay.visible { opacity: 1; visibility: visible; }
  .modal-content { background: var(--background-secondary); padding: 24px; border-radius: var(--border-radius); border: 1px solid var(--border-color); width: 90%; max-width: 450px; text-align: center; box-shadow: 0 8px 30px var(--shadow-color-accent); transform: scale(0.95); transition: transform 0.3s ease; }
  .modal-overlay.visible .modal-content { transform: scale(1); }
  .modal-title { font-family: var(--serif); font-size: 1.5rem; color: var(--accent-secondary); margin-bottom: 16px; }
  .modal-text { color: var(--text-primary); font-size: 14px; line-height: 1.6; margin-bottom: 20px; }
  .modal-instruction { background: var(--background-tertiary); padding: 12px; border-radius: 6px; margin-bottom: 24px; font-size: 13px; line-height: 1.8; border: 1px solid var(--border-color); }
  .modal-instruction code { background: var(--background-primary); color: var(--accent-primary); padding: 3px 6px; border-radius: 4px; font-family: var(--mono-serif); }
  #hiddify-modal-continue { width: 100%;}

  @media (max-width: 768px) {
    body { padding: 20px; } .container { padding: 0 14px; width: min(100%, 768px); }
    .ip-info-grid { grid-template-columns: repeat(auto-fit, minmax(170px, 1fr)); gap: 18px; }
    .header h1 { font-size: 1.8rem; } .header p { font-size: 0.7rem }
    .ip-info-section { padding: 14px; gap: 18px; } .ip-info-header h3 { font-size: 16px; }
    .ip-info-header { gap: 8px; } .ip-info-content { gap: 8px; }
    .ip-info-item .label { font-size: 11px; } .ip-info-item .value { font-size: 13px; }
    .config-card { padding: 16px; } .config-title { font-size: 18px; }
    .config-title .refresh-btn { font-size: 11px; } .config-content pre { font-size: 12px; }
    .client-buttons { grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); }
    .button { font-size: 12px; } .copy-buttons { font-size: 11px; }
  }

  @media (max-width: 480px) {
    body { padding: 16px; } .container { padding: 0 12px; width: min(100%, 390px); }
    .header h1 { font-size: 20px; } .header p { font-size: 8px; }
    .ip-info-section { padding: 14px; gap: 16px; }
    .ip-info-grid { grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 16px; }
    .ip-info-header h3 { font-size: 14px; } .ip-info-header { gap: 6px; }
    .ip-info-content { gap: 6px; } .ip-info-header svg { width: 18px; height: 18px; }
    .ip-info-item .label { font-size: 9px; } .ip-info-item .value { font-size: 11px; }
    .badge { padding: 2px 6px; font-size: 10px; border-radius: 10px; }
    .config-card { padding: 10px; } .config-title { font-size: 16px; }
    .config-title .refresh-btn { font-size: 10px; } .config-content { padding: 12px; }
    .config-content pre { font-size: 10px; }
    .client-buttons { grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); }
    .button { padding: 4px 8px; font-size: 11px; } .copy-buttons { font-size: 10px; }
    .footer { font-size: 10px; }
  }

  @media (max-width: 359px) {
    body { padding: 12px; font-size: 14px; } .container { max-width: 100%; padding: 8px; }
    .header h1 { font-size: 16px; } .header p { font-size: 6px; }
    .ip-info-section { padding: 12px; gap: 12px; }
    .ip-info-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 10px; }
    .ip-info-header h3 { font-size: 13px; } .ip-info-header { gap: 4px; } .ip-info-content { gap: 4px; }
    .ip-info-header svg { width: 16px; height: 16px; } .ip-info-item .label { font-size: 8px; }
    .ip-info-item .value { font-size: 10px; } .badge { padding: 1px 4px; font-size: 9px; border-radius: 8px; }
    .config-card { padding: 8px; } .config-title { font-size: 13px; } .config-title .refresh-btn { font-size: 9px; }
    .config-content { padding: 8px; } .config-content pre { font-size: 8px; }
    .client-buttons { grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); }
    .button { padding: 3px 6px; font-size: 10px; } .copy-buttons { font-size: 9px; } .footer { font-size: 8px; }
  }
    @media (min-width: 360px) { .container { max-width: 95%; } }
    @media (min-width: 480px) { .container { max-width: 90%; } }
    @media (min-width: 640px) { .container { max-width: 600px; } }
    @media (min-width: 768px) { .container { max-width: 720px; } }
    @media (min-width: 1024px) { .container { max-width: 800px; } }
  </style>
  </head>
  <body data-proxy-ip="{{PROXY_ADDRESS}}">
    <div class="container">
    <div class="header">
      <h1>VLESS Proxy Configuration</h1>
      <p>Copy the configuration or import directly into your client</p>
    </div>

    <div class="config-card">
      <div class="config-title">
        <span>Network Information</span>
        <button id="refresh-ip-info" class="refresh-btn" aria-label="Refresh IP information">
          <svg
            class="refresh-icon"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path
              d="M21.5 2v6h-6M2.5 22v-6h6M2 11.5a10 10 0 0 1 18.8-4.3M22 12.5a10 10 0 0 1-18.8 4.2"
            />
          </svg>
          Refresh
        </button>
      </div>
      <div class="ip-info-grid">
        <div class="ip-info-section">
          <div class="ip-info-header">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path
                d="M15.5 2H8.6c-.4 0-.8.2-1.1.5-.3.3-.5.7-.5 1.1v16.8c0 .4.2.8.5 1.1.3.3.7.5 1.1.5h6.9c.4 0 .8-.2 1.1-.5.3-.3.5-.7.5-1.1V3.6c0-.4-.2-.8-.5-1.1-.3-.3-.7-.5-1.1-.5z"
              />
              <circle cx="12" cy="18" r="1" />
            </svg>
            <h3>Proxy Server</h3>
          </div>
          <div class="ip-info-content">
            <div class="ip-info-item">
              <span class="label">Proxy Host</span
              ><span class="value" id="proxy-host"
                ><span class="skeleton" style="width: 150px"></span
              ></span>
            </div>
            <div class="ip-info-item">
              <span class="label">IP Address</span
              ><span class="value" id="proxy-ip"
                ><span class="skeleton" style="width: 120px"></span
              ></span>
            </div>
            <div class="ip-info-item">
              <span class="label">Location</span
              ><span class="value" id="proxy-location"
                ><span class="skeleton" style="width: 100px"></span
              ></span>
            </div>
            <div class="ip-info-item">
              <span class="label">ISP Provider</span
              ><span class="value" id="proxy-isp"
                ><span class="skeleton" style="width: 140px"></span
              ></span>
            </div>
          </div>
        </div>
        <div class="ip-info-section">
          <div class="ip-info-header">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path
                d="M20 16V7a2 2 0 0 0-2-2H6a2 2 0 0 0-2 2v9m16 0H4m16 0 1.28 2.55a1 1 0 0 1-.9 1.45H3.62a1 1 0 0 1-.9-1.45L4 16"
              />
            </svg>
            <h3>Your Connection</h3>
          </div>
          <div class="ip-info-content">
            <div class="ip-info-item">
              <span class="label">Your IP</span
              ><span class="value" id="client-ip"
                ><span class="skeleton" style="width: 110px"></span
              ></span>
            </div>
            <div class="ip-info-item">
              <span class="label">Location</span
              ><span class="value" id="client-location"
                ><span class="skeleton" style="width: 90px"></span
              ></span>
            </div>
            <div class="ip-info-item">
              <span class="label">ISP Provider</span
              ><span class="value" id="client-isp"
                ><span class="skeleton" style="width: 130px"></span
              ></span>
            </div>
            <div class="ip-info-item">
              <span class="label">Risk Score</span
              ><span class="value" id="client-proxy"
                ><span class="skeleton" style="width: 100px"></span
              ></span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="config-card">
      <div class="config-title">
        <span>Xray Core Clients</span>
        <button class="button copy-buttons" onclick="copyToClipboard(this, '{{CONFIG_DREAM}}')">
          <svg
            class="copy-icon"
            xmlns="http://www.w3.org/2000/svg"
            width="12"
            height="12"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
          Copy
        </button>
      </div>
      <div class="config-content"><pre id="xray-config">{{CONFIG_DREAM}}</pre></div>
      <div class="client-buttons">
        <a href="{{URL_HIDDIFY}}" id="hiddify-import-btn" class="button client-btn">
          <span class="client-icon"
            ><svg viewBox="0 0 24 24">
              <path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5" /></svg
          ></span>
          <span class="button-text">Import to Hiddify</span>
        </a>
        <a href="{{URL_V2RAYNG}}" class="button client-btn">
          <span class="client-icon"
            ><svg viewBox="0 0 24 24">
              <path d="M12 2L4 5v6c0 5.5 3.5 10.7 8 12.3 4.5-1.6 8-6.8 8-12.3V5l-8-3z" /></svg
          ></span>
          <span class="button-text">Import to V2rayNG</span>
        </a>
      </div>
    </div>

    <div class="config-card">
      <div class="config-title">
        <span>Sing-Box Core Clients</span>
        <button class="button copy-buttons" onclick="copyToClipboard(this, '{{CONFIG_FREEDOM}}')">
          <svg
            class="copy-icon"
            xmlns="http://www.w3.org/2000/svg"
            width="12"
            height="12"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
          Copy
        </button>
      </div>
      <div class="config-content"><pre id="singbox-config">{{CONFIG_FREEDOM}}</pre></div>
      <div class="client-buttons">
        <a href="{{URL_CLASH}}" class="button client-btn">
          <span class="client-icon"
            ><svg viewBox="0 0 24 24">
              <path
                d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 17.93c-3.95-.49-7-3.85-7-7.93 0-.62.08-1.21.21-1.79L9 15v1c0 1.1.9 2 2 2v1.93zm6.9-2.54c-.26-.81-1-1.39-1.9-1.39h-1v-3c0-.55-.45-1-1-1H8v-2h2c.55 0 1-.45 1-1V7h2c1.1 0 2-.9 2-2v-.41c2.93 1.19 5 4.06 5 7.41 0 2.08-.8 3.97-2.1 5.39z"
              /></svg
          ></span>
          <span class="button-text">Import to Clash Meta</span>
        </a>
        <a href="{{URL_EXCLAVE}}" class="button client-btn">
          <span class="client-icon"
            ><svg viewBox="0 0 24 24">
              <path
                d="M20,8h-3V6c0-1.1-0.9-2-2-2H9C7.9,4,7,4.9,7,6v2H4C2.9,8,2,8.9,2,10v9c0,1.1,0.9,2,2,2h16c1.1,0,2-0.9,2-2v-9 C22,8.9,21.1,8,20,8z M9,6h6v2H9V6z M20,19H4v-2h16V19z M20,15H4v-5h3v1c0,0.55,0.45,1,1,1h1.5c0.28,0,0.5-0.22,0.5-0.5v-0.5h4v0.5 c0,0.28,0.22,0.5,0.5,0.5H16c0.55,0,1-0.45,1-1v-1h3V15z"
              />
              <circle cx="8.5" cy="13.5" r="1" />
              <circle cx="15.5" cy="13.5" r="1" />
              <path d="M12,15.5c-0.55,0-1-0.45-1-1h2C13,15.05,12.55,15.5,12,15.5z" /></svg
          ></span>
          <span class="button-text">Import to Exclave</span>
        </a>
      </div>
    </div>

    <div class="footer">
      <p>
        © <span id="current-year"></span> REvil - All Rights Reserved
      </p>
      <p>Secure. Private. Fast.</p>
    </div>
  </div>

  <div id="hiddify-dns-modal" class="modal-overlay" style="display: none">
    <div class="modal-content">
      <h3 class="modal-title">Important Note for Hiddify Users</h3>
      <p class="modal-text">
        For the configuration to work correctly, you need to change the
        <strong>Remote DNS</strong> setting in the Hiddify app.
      </p>
      <div class="modal-instruction">
        Change from: <code>udp://1.1.1.1</code><br />
        To: <code>https://8.8.8.8/dns-query</code>
      </div>
      <button id="hiddify-modal-continue" class="button client-btn">Continue to Hiddify</button>
    </div>
  </div>

    <script>
      function copyToClipboard(button, text) {
        const originalHTML = button.innerHTML;

        navigator.clipboard
          .writeText(text)
          .then(() => {
            button.innerHTML = `
          <svg class="copy-icon" xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
          Copied!
        `;
            button.classList.add("copied");
            button.disabled = true;

            setTimeout(() => {
              button.innerHTML = originalHTML;
              button.classList.remove("copied");
              button.disabled = false;
            }, 1200);
          })
          .catch((err) => {
            console.error("Failed to copy text: ", err);
            const originalHTMLError = button.innerHTML;

            button.innerHTML = `
          <svg class="copy-icon" xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
          Error
        `;
            button.classList.add("error");
            button.disabled = true;

            setTimeout(() => {
              button.innerHTML = originalHTMLError;
              button.classList.remove("error");
              button.disabled = false;
            }, 1500);
          });
      }

      async function fetchClientPublicIP() {
        try {
          const response = await fetch("https://api.ipify.org?format=json");
          if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
          return (await response.json()).ip;
        } catch (error) {
          console.error("Error fetching client IP:", error);
          return null;
        }
      }

      async function fetchScamalyticsClientInfo(clientIp) {
        if (!clientIp) return null;
        try {
          const response = await fetch(`/scamalytics-lookup?ip=${encodeURIComponent(clientIp)}`);
          if (!response.ok) {
            const errorText = await response.text();
            throw new Error(
              `Worker request failed! status: ${response.status}, details: ${errorText}`,
            );
          }
          const data = await response.json();
          if (data.scamalytics && data.scamalytics.status === "error") {
            throw new Error(data.scamalytics.error || "Scamalytics API error via Worker");
          }
          return data;
        } catch (error) {
          console.error("Error fetching from Scamalytics via Worker:", error);
          return null;
        }
      }

      // Extract information from IP databases
      function updateScamalyticsClientDisplay(data) {
        const prefix = "client";
        if (!data || !data.scamalytics || data.scamalytics.status !== "ok") {
          showError(
            prefix,
            (data && data.scamalytics && data.scamalytics.error) ||
              "Could not load client data from Scamalytics",
          );
          return;
        }

        const sa = data.scamalytics;
        const ext = data.external_datasources || {};

        // Alternative sources for extracting information
        const ipinfo = ext.ipinfo || {};
        const maxmind = ext.maxmind_geolite2 || {};
        const dbip = ext.dbip || {};

        const elements = {
          ip: document.getElementById(`${prefix}-ip`),
          location: document.getElementById(`${prefix}-location`),
          isp: document.getElementById(`${prefix}-isp`),
          proxy: document.getElementById(`${prefix}-proxy`),
        };

        // Helper function for data validation
        const isValid = (val) =>
          val && val !== "PREMIUM FIELD - upgrade to view" && val.trim() !== "";

        // IP display (Your connection)
        if (elements.ip) elements.ip.textContent = sa.ip || "N/A";

        // Show ISP - priority is given to ipinfo
        let ispName = "N/A";
        if (isValid(ipinfo.as_name)) ispName = ipinfo.as_name;
        else if (isValid(maxmind.as_name)) ispName = maxmind.as_name;
        else if (isValid(sa.scamalytics_isp)) ispName = sa.scamalytics_isp;
        else if (isValid(dbip.isp_name)) ispName = dbip.isp_name;

        if (elements.isp) elements.isp.textContent = ispName;

        // Show location (city and country)
        if (elements.location) {
          let city = "";
          let countryName = "";
          let countryCode = "";

          // Trying to find a city
          if (isValid(maxmind.ip_city)) city = maxmind.ip_city;
          else if (isValid(dbip.ip_city)) city = dbip.ip_city;

          // Trying to find a country
          if (isValid(ipinfo.ip_country_name)) {
            countryName = ipinfo.ip_country_name;
            countryCode = ipinfo.ip_country_code;
          } else if (isValid(maxmind.ip_country_name)) {
            countryName = maxmind.ip_country_name;
            countryCode = maxmind.ip_country_code;
          } else if (isValid(dbip.ip_country_name)) {
            countryName = dbip.ip_country_name;
            countryCode = dbip.ip_country_code;
          }

          countryCode = countryCode ? countryCode.toLowerCase() : "";

          let flagElementHtml = countryCode
            ? `<img src="https://flagcdn.com/w20/${countryCode}.png" srcset="https://flagcdn.com/w40/${countryCode}.png 2x" alt="${countryCode}" class="country-flag"> `
            : "";
          let textPart = [city, countryName].filter(Boolean).join(", ");

          elements.location.innerHTML =
            flagElementHtml || textPart ? `${flagElementHtml}${textPart}`.trim() : "N/A";
        }

        // Show risk score
        if (elements.proxy) {
          const score = sa.scamalytics_score;
          const risk = sa.scamalytics_risk;
          let riskText = "Unknown";
          let badgeClass = "badge-neutral";
          if (risk && score !== undefined) {
            riskText = `${score} - ${risk.charAt(0).toUpperCase() + risk.slice(1)}`;
            switch (risk.toLowerCase()) {
              case "low":
                badgeClass = "badge-yes";
                break;
              case "medium":
                badgeClass = "badge-warning";
                break;
              case "high":
              case "very high":
                badgeClass = "badge-no";
                break;
            }
          }
          elements.proxy.innerHTML = `<span class="badge ${badgeClass}">${riskText}</span>`;
        }
      }

      function updateIpApiIoDisplay(geo, prefix, originalHost) {
        const hostElement = document.getElementById(`${prefix}-host`);
        if (hostElement) hostElement.textContent = originalHost || "N/A";
        const elements = {
          ip: document.getElementById(`${prefix}-ip`),
          location: document.getElementById(`${prefix}-location`),
          isp: document.getElementById(`${prefix}-isp`),
        };
        if (!geo || geo.error) {
          const errorMessage = geo ? geo.reason : "N/A";
          Object.values(elements).forEach((el) => {
            if (el) el.innerHTML = errorMessage;
          });
          if (elements.ip) elements.ip.innerHTML = "N/A";
          return;
        }
        if (elements.ip) elements.ip.textContent = geo.ip || "N/A";
        if (elements.location) {
          const city = geo.city || "";
          const countryName = geo.country_name || "";
          const countryCode = geo.country_code ? geo.country_code.toLowerCase() : "";
          let flagElementHtml = countryCode
            ? `<img src="https://flagcdn.com/w20/${countryCode}.png" srcset="https://flagcdn.com/w40/${countryCode}.png 2x" alt="${geo.country_code}" class="country-flag"> `
            : "";
          let textPart = [city, countryName].filter(Boolean).join(", ");
          elements.location.innerHTML =
            flagElementHtml || textPart ? `${flagElementHtml}${textPart}`.trim() : "N/A";
        }
        if (elements.isp)
          elements.isp.textContent = geo.isp || geo.organization || geo.org || "N/A";
      }

      async function fetchIpApiIoInfo(ip) {
        try {
          const response = await fetch(`https://ipapi.co/${ip}/json/`);
          if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
          return await response.json();
        } catch (error) {
          console.error("IP API Error (ipapi.co):", error);
          return null;
        }
      }

      function showError(prefix, message = "Could not load data", originalHostForProxy = null) {
        const errorMessage = "N/A";
        const elements =
          prefix === "proxy"
            ? ["host", "ip", "location", "isp"]
            : ["ip", "location", "isp", "proxy"];

        elements.forEach((key) => {
          const el = document.getElementById(`${prefix}-${key}`);
          if (!el) return;
          if (key === "host" && prefix === "proxy")
            el.textContent = originalHostForProxy || errorMessage;
          else if (key === "proxy" && prefix === "client")
            el.innerHTML = `<span class="badge badge-neutral">N/A</span>`;
          else el.innerHTML = errorMessage;
        });
        console.warn(`${prefix} data loading failed: ${message}`);
      }

      async function loadNetworkInfo() {
        try {
          const proxyIpWithPort = document.body.getAttribute("data-proxy-ip") || "N/A";
          const proxyDomainOrIp = proxyIpWithPort.split(":")[0];
          const proxyHostEl = document.getElementById("proxy-host");
          if (proxyHostEl) proxyHostEl.textContent = proxyIpWithPort;

          if (proxyDomainOrIp && proxyDomainOrIp !== "N/A") {
            let resolvedProxyIp = proxyDomainOrIp;
              if (!/^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$/.test(proxyDomainOrIp)) {
              try {
                const dnsRes = await fetch(
                  `https://dns.google/resolve?name=${encodeURIComponent(proxyDomainOrIp)}&type=A`,
                );
                if (dnsRes.ok) {
                  const dnsData = await dnsRes.json();
                  const ipAnswer = dnsData.Answer?.find((a) => a.type === 1);
                  if (ipAnswer) resolvedProxyIp = ipAnswer.data;
                }
              } catch (e) {
                console.error("DNS resolution for proxy failed:", e);
              }
            }
            const proxyGeoData = await fetchIpApiIoInfo(resolvedProxyIp);
            updateIpApiIoDisplay(proxyGeoData, "proxy", proxyIpWithPort);
          } else {
            showError("proxy", "Proxy Host not available", proxyIpWithPort);
          }

          const clientIp = await fetchClientPublicIP();
          if (clientIp) {
            const clientIpElement = document.getElementById("client-ip");
            if (clientIpElement) clientIpElement.textContent = clientIp;
            const scamalyticsData = await fetchScamalyticsClientInfo(clientIp);
            updateScamalyticsClientDisplay(scamalyticsData);
          } else {
            showError("client", "Could not determine your IP address.");
          }
        } catch (error) {
          console.error("Overall network info loading failed:", error);
          showError(
            "proxy",
            `Error: ${error.message}`,
            document.body.getAttribute("data-proxy-ip") || "N/A",
          );
          showError("client", `Error: ${error.message}`);
        }
      }

      document.getElementById("refresh-ip-info")?.addEventListener("click", function () {
        const button = this;
        const icon = button.querySelector(".refresh-icon");
        button.disabled = true;
        if (icon) icon.style.animation = "spin 1s linear infinite";

        const resetToSkeleton = (prefix) => {
          const elementsToReset = ["ip", "location", "isp"];
          if (prefix === "proxy") elementsToReset.push("host");
          if (prefix === "client") elementsToReset.push("proxy");
          elementsToReset.forEach((key) => {
            const element = document.getElementById(`${prefix}-${key}`);
            if (element) element.innerHTML = `<span class="skeleton" style="width: 120px;"></span>`;
          });
        };

        resetToSkeleton("proxy");
        resetToSkeleton("client");
        loadNetworkInfo().finally(() =>
          setTimeout(() => {
            button.disabled = false;
            if (icon) icon.style.animation = "";
          }, 1000),
        );
      });

      const style = document.createElement("style");
      style.textContent = `@keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }`;
      document.head.appendChild(style);

      document.addEventListener("DOMContentLoaded", () => {
        const yearEl = document.getElementById("current-year");
        if (yearEl) yearEl.textContent = new Date().getFullYear();
      
        loadNetworkInfo();

        const hiddifyBtn = document.getElementById("hiddify-import-btn");
        const modal = document.getElementById("hiddify-dns-modal");
        const continueBtn = document.getElementById("hiddify-modal-continue");

        if (hiddifyBtn && modal && continueBtn) {
          hiddifyBtn.addEventListener("click", function (event) {
            event.preventDefault();
            modal.style.display = "flex";
            setTimeout(() => modal.classList.add("visible"), 10);
          });

          continueBtn.addEventListener("click", function () {
            modal.classList.remove("visible");
            setTimeout(() => {
              modal.style.display = "none";
              window.location.href = hiddifyBtn.href;
            }, 300);
          });

          modal.addEventListener("click", function (event) {
            if (event.target === modal) {
              modal.classList.remove("visible");
              setTimeout(() => (modal.style.display = "none"), 300);
            }
          });
        }
      });
    </script>
  </body>
</html>

```

### _worker.js

```js
// Time is: 2026-02-22T00:01:59.404Z
import{connect as N}from"cloudflare:sockets";var W=null,H=e=>atob(e),j="https://nirevil.github.io/zizifn/",G={userID:"be0ff9df-1468-41a0-8865-796d1c6800db",proxyIPs:["nima.nscl.ir:443"],scamalytics:{username:"nimasecure999",apiKey:"ce75d58f98849753077a270e6013a036d6f4a6c562fd74c960960ae7a7087b40",baseUrl:"https://api12.scamalytics.com/v3/"},socks5:{enabled:!1,relayMode:!1,address:""},fromEnv(e){let t=e.PROXYIP||this.proxyIPs[Math.floor(Math.random()*this.proxyIPs.length)],[a,r="443"]=t.split(":");return{userID:e.UUID||this.userID,proxyIP:a,proxyPort:r,proxyAddress:t,scamalytics:{username:e.SCAMALYTICS_USERNAME||this.scamalytics.username,apiKey:e.SCAMALYTICS_API_KEY||this.scamalytics.apiKey,baseUrl:e.SCAMALYTICS_BASEURL||this.scamalytics.baseUrl},socks5:{enabled:!!e.SOCKS5,relayMode:e.SOCKS5_RELAY==="true"||this.socks5.relayMode,address:e.SOCKS5||this.socks5.address}}}};async function M(e,t={},a=4e3){let r=new AbortController,n=setTimeout(()=>r.abort(),a);try{return await fetch(e,{...t,signal:r.signal})}finally{clearTimeout(n)}}var g={ED_PARAMS:{ed:2560,eh:"Sec-WebSocket-Protocol"},AT_SYMBOL:"@",VLESS_PROTOCOL:H("dmxlc3M="),WS_READY_STATE_OPEN:1,WS_READY_STATE_CLOSING:2};function $(e=28,t=""){let a="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",r="";for(let n=0;n<e;n++)r+=a.charAt(Math.floor(Math.random()*a.length));return`/${r}${t?`?${t}`:""}`}var z={xray:{tls:{path:()=>$(12,"ed=2048"),security:"tls",fp:"chrome",alpn:"http/1.1",extra:{}},tcp:{path:()=>$(12,"ed=2560"),security:"none",fp:"chrome",extra:{}}},sb:{tls:{path:()=>$(18),security:"tls",fp:"chrome",alpn:"http/1.1",extra:g.ED_PARAMS},tcp:{path:()=>$(18),security:"none",fp:"chrome",extra:g.ED_PARAMS}}};function F(e,t){return`${e}-${t.toUpperCase()}`}function X({userID:e,address:t,port:a,host:r,path:n,security:c,sni:s,fp:o,alpn:i,extra:d={},name:h}){let u=new URLSearchParams({type:H("d3M="),host:r,path:n});c&&(u.set("security",c),c==="tls"&&u.set("allowInsecure","1")),s&&u.set("sni",s),o&&u.set("fp",o),i&&u.set("alpn",i);for(let[l,p]of Object.entries(d))u.set(l,p);return`${g.VLESS_PROTOCOL}://${e}@${t}:${a}?${u.toString()}#${encodeURIComponent(h)}`}function x({core:e,proto:t,userID:a,hostName:r,address:n,port:c,tag:s}){let o=z[e][t];return X({userID:a,address:n,port:c,host:r,path:o.path(),security:o.security,sni:o.security==="tls"?r:void 0,fp:o.fp,alpn:o.alpn,extra:o.extra,name:F(s,t)})}var I=e=>e[Math.floor(Math.random()*e.length)];async function K(e,t,a,r,n){let s=new URL(e.url).searchParams.get("name"),o={total_TB:380,base_GB:42e3,daily_growth_GB:250,expire_date:"2028-4-20"},i=[r,"creativecommons.org","2027.victoriacross.ir","www.speedtest.net","sky.rethinkdns.com","chat.openai.com","cfip.xxxxxxxx.tk","go.inmobi.com","singapore.com","www.visa.com","www.wto.org","chatgpt.com","yakamoz.nscl.ir","nodejs.org","zzula.ir","csgo.com","fbi.gov"],d=[443,8443,2053,2083,2087,2096],h=[80,8080,8880,2052,2082,2086,2095],u=[],l=r.endsWith(".pages.dev");i.forEach((S,E)=>{u.push(x({core:t,proto:"tls",userID:a,hostName:r,address:S,port:I(d),tag:`Domain${E+1}`})),l||u.push(x({core:t,proto:"tcp",userID:a,hostName:r,address:S,port:I(h),tag:`Domain${E+1}`}))});try{let S=caches.default,E=new Request("https://cf-ip-cache.local"),P=await S.match(E);if(!P){let U=await M("https://raw.githubusercontent.com/NiREvil/vless/refs/heads/main/Cloudflare-IPs.json",{},4e3);U.ok&&(P=new Response(await U.text(),{headers:{"Cache-Control":"public, max-age=86400"}}),n.waitUntil(S.put(E,P.clone())))}if(P){let U=await P.json();[...U.ipv4||[],...U.ipv6||[]].slice(0,20).map(_=>_.ip).forEach((_,v)=>{let B=_.includes(":")?`[${_}]`:_;u.push(x({core:t,proto:"tls",userID:a,hostName:r,address:B,port:I(d),tag:`IP${v+1}`})),l||u.push(x({core:t,proto:"tcp",userID:a,hostName:r,address:B,port:I(h),tag:`IP${v+1}`}))})}}catch(S){console.error("Cached IP fetch failed",S)}let p=1024*1024*1024,f=1024*p,y=o.total_TB*f,m=o.base_GB*p,b=new Date,R=(b.getHours()+b.getMinutes()/60)/24*(o.daily_growth_GB*p),L=m+R/2,C=m+R/2,k=Math.floor(new Date(o.expire_date).getTime()/1e3),A={"Content-Type":"text/plain;charset=utf-8","Profile-Update-Interval":"6","Subscription-Userinfo":`upload=${Math.round(C)}; download=${Math.round(L)}; total=${y}; expire=${k}`};return s&&(A["Profile-Title"]=s),new Response(btoa(u.join(`
`)),{headers:A})}async function J(e,t){let a=new WebSocketPair,[r,n]=Object.values(a);n.accept();let c="",s="",o=null,i=(l,p)=>{console.log(`[${c}:${s}] ${l}`,p||"")},d=e.headers.get("Sec-WebSocket-Protocol")||"",h=Z(n,d,i),u={value:null};return h.pipeTo(new WritableStream({async write(l,p){if(o)return o.write(l);if(u.value){let A=u.value.writable.getWriter();await A.write(l),A.releaseLock();return}let{hasError:f,message:y,addressType:m,portRemote:b=443,addressRemote:T="",rawDataIndex:R,ProtocolVersion:L=new Uint8Array([0,0]),isUDP:C}=ee(l,t.userID);if(c=T,s=`${b}--${Math.random()} ${C?"udp":"tcp"} `,f)throw new Error(y);let k=new Uint8Array([L[0],0]),O=l.slice(R);if(C){if(b===53)o=(await ne(n,k,i)).write,o(O);else throw new Error("UDP proxy is only enabled for DNS (port 53)");return}Q(u,m,T,b,O,n,k,i,t)},close(){i("readableWebSocketStream closed")},abort(l){i("readableWebSocketStream aborted",l)}})).catch(l=>{console.error("Pipeline failed:",l.stack||l)}),new Response(null,{status:101,webSocket:r})}function q(e){return/^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i.test(e)}async function Q(e,t,a,r,n,c,s,o,i){async function d(l,p,f=!1){let y;i.socks5Relay?y=await Y(t,l,p,o,i.parsedSocks5Address):y=f?await Y(t,l,p,o,i.parsedSocks5Address):N({hostname:l,port:p}),e.value=y,o(`connected to ${l}:${p}`);let m=y.writable.getWriter();return await m.write(n),m.releaseLock(),y}async function h(){let l=i.enableSocks?await d(a,r,!0):await d(i.proxyIP||a,i.proxyPort||r,!1);l.closed.catch(p=>console.log("retry tcpSocket closed error",p)).finally(()=>D(c)),V(l,c,s,null,o)}let u=await d(a,r);V(u,c,s,h,o)}function Z(e,t,a){return new ReadableStream({start(r){e.addEventListener("message",s=>r.enqueue(s.data)),e.addEventListener("close",()=>{D(e),r.close()}),e.addEventListener("error",s=>{a("webSocketServer has error"),r.error(s)});let{earlyData:n,error:c}=te(t);c?r.error(c):n&&r.enqueue(n)},pull(r){},cancel(r){a(`ReadableStream was canceled, due to ${r}`),D(e)}})}function ee(e,t){if(e.byteLength<24)return{hasError:!0,message:"invalid data"};let a=new DataView(e),r=a.getUint8(0),n=ae(new Uint8Array(e.slice(1,17))),c=t.split(",").map(y=>y.trim());if(!n)return{hasError:!0,message:"invalid uuid format"};if(!c.some(y=>n===y))return{hasError:!0,message:"invalid user"};let o=a.getUint8(17),i=a.getUint8(18+o);if(i!==1&&i!==2)return{hasError:!0,message:`command ${i} is not supported`};let d=18+o+1,h=a.getUint16(d),u=a.getUint8(d+2),l,p,f;switch(u){case 1:p=4,f=d+3,l=new Uint8Array(e.slice(f,f+p)).join(".");break;case 2:p=a.getUint8(d+3),f=d+4,l=new TextDecoder().decode(e.slice(f,f+p));break;case 3:p=16,f=d+3,l=Array.from({length:8},(y,m)=>a.getUint16(f+m*2).toString(16)).join(":");break;default:return{hasError:!0,message:`invalid addressType: ${u}`}}return l?{hasError:!1,addressRemote:l,addressType:u,portRemote:h,rawDataIndex:f+p,ProtocolVersion:new Uint8Array([r]),isUDP:i===2}:{hasError:!0,message:`addressValue is empty, addressType is ${u}`}}async function V(e,t,a,r,n){let c=!1;try{await e.readable.pipeTo(new WritableStream({async write(s){if(t.readyState!==g.WS_READY_STATE_OPEN)throw new Error("WebSocket is not open");c=!0;let o=a?await new Blob([a,s]).arrayBuffer():s;t.send(o),a=null},close(){n("Remote connection readable closed.")},abort(s){console.error("Remote connection readable aborted:",s)}}))}catch(s){console.error("RemoteSocketToWS error:",s.stack||s),D(t)}!c&&r&&(n("No incoming data, retrying"),await r())}function te(e){if(!e)return{earlyData:null,error:null};try{let t=atob(e.replace(/-/g,"+").replace(/_/g,"/")),a=new ArrayBuffer(t.length),r=new Uint8Array(a);for(let n=0;n<t.length;n++)r[n]=t.charCodeAt(n);return{earlyData:a,error:null}}catch(t){return{earlyData:null,error:t}}}function D(e){try{(e.readyState===g.WS_READY_STATE_OPEN||e.readyState===g.WS_READY_STATE_CLOSING)&&e.close()}catch(t){console.error("safeCloseWebSocket error:",t)}}var w=Array.from({length:256},(e,t)=>(t+256).toString(16).slice(1));function re(e,t=0){return(w[e[t]]+w[e[t+1]]+w[e[t+2]]+w[e[t+3]]+"-"+w[e[t+4]]+w[e[t+5]]+"-"+w[e[t+6]]+w[e[t+7]]+"-"+w[e[t+8]]+w[e[t+9]]+"-"+w[e[t+10]]+w[e[t+11]]+w[e[t+12]]+w[e[t+13]]+w[e[t+14]]+w[e[t+15]]).toLowerCase()}function ae(e,t=0){let a=re(e,t);return q(a)?a:""}async function ne(e,t,a){let r=!1,n=new TransformStream({transform(s,o){for(let i=0;i<s.byteLength;){let d=s.slice(i,i+2),h=new DataView(d).getUint16(0),u=new Uint8Array(s.slice(i+2,i+2+h));i=i+2+h,o.enqueue(u)}}});n.readable.pipeTo(new WritableStream({async write(s){try{let i=await(await M("https://1.1.1.1/dns-query",{method:"POST",headers:{"content-type":"application/dns-message"},body:s},3e3)).arrayBuffer(),d=i.byteLength,h=new Uint8Array([d>>8&255,d&255]);e.readyState===g.WS_READY_STATE_OPEN&&(r?e.send(await new Blob([h,i]).arrayBuffer()):(e.send(await new Blob([t,h,i]).arrayBuffer()),r=!0))}catch(o){a("DNS query error: "+o)}}})).catch(s=>a("DNS stream error: "+s));let c=n.writable.getWriter();return{write:s=>c.write(s)}}async function Y(e,t,a,r,n){let{username:c,password:s,hostname:o,port:i}=n,d=N({hostname:o,port:i}),h=d.writable.getWriter(),u=d.readable.getReader(),l=new TextEncoder;await h.write(new Uint8Array([5,2,0,2]));let p=(await u.read()).value;if(p[0]!==5||p[1]===255)throw new Error("SOCKS5 server connection failed.");if(p[1]===2){if(!c||!s)throw new Error("SOCKS5 auth credentials not provided.");let m=new Uint8Array([1,c.length,...l.encode(c),s.length,...l.encode(s)]);if(await h.write(m),p=(await u.read()).value,p[0]!==1||p[1]!==0)throw new Error("SOCKS5 authentication failed.")}let f;switch(e){case 1:f=new Uint8Array([1,...t.split(".").map(Number)]);break;case 2:f=new Uint8Array([3,t.length,...l.encode(t)]);break;case 3:f=new Uint8Array([4,...t.split(":").flatMap(m=>[parseInt(m.slice(0,2),16),parseInt(m.slice(2),16)])]);break;default:throw new Error(`Invalid addressType for SOCKS5: ${e}`)}let y=new Uint8Array([5,1,0,...f,a>>8,a&255]);if(await h.write(y),p=(await u.read()).value,p[1]!==0)throw new Error("Failed to open SOCKS5 connection.");return h.releaseLock(),u.releaseLock(),d}function se(e){try{let[t,a]=e.includes("@")?e.split("@"):[null,e],[r,n]=a.split(":"),c=parseInt(n,10);if(!r||isNaN(c))throw new Error;let s,o;if(t&&([s,o]=t.split(":"),!s))throw new Error;return{username:s,password:o,hostname:r,port:c}}catch{throw new Error("Invalid SOCKS5 address format.")}}async function oe(e,t){let r=new URL(e.url).searchParams.get("ip");if(!r)return new Response(JSON.stringify({error:"Missing IP"}),{status:400,headers:{"Content-Type":"application/json"}});let{username:n,apiKey:c,baseUrl:s}=t.scamalytics;if(!n||!c)return new Response(JSON.stringify({error:"Scamalytics API not configured"}),{status:500,headers:{"Content-Type":"application/json"}});let o=`${s}${n}/?key=${c}&ip=${r}`,i=new Headers({"Content-Type":"application/json","Access-Control-Allow-Origin":"*"});try{let h=await(await fetch(o)).json();return new Response(JSON.stringify(h),{headers:i})}catch(d){return new Response(JSON.stringify({error:d.toString()}),{status:500,headers:i})}}async function ie(e,t,a){let r=x({core:"xray",proto:"tls",userID:e,hostName:t,address:t,port:443,tag:`${t}-Xray`}),n=x({core:"sb",proto:"tls",userID:e,hostName:t,address:t,port:443,tag:`${t}-Singbox`}),c=encodeURIComponent("INDEX"),s=`https://${t}/xray/${e}?name=${c}`,o=`https://${t}/sb/${e}?name=${c}`;try{let i=await M(j);if(!i.ok)throw new Error(`Failed to load HTML from GitHub Pages: ${i.status}`);let d=await i.text();return d=d.replace(/{{PROXY_ADDRESS}}/g,a).replace(/{{CONFIG_DREAM}}/g,r).replace(/{{CONFIG_FREEDOM}}/g,n).replace(/{{URL_HIDDIFY}}/g,`hiddify://install-config?url=${encodeURIComponent(s)}`).replace(/{{URL_V2RAYNG}}/g,`v2rayng://install-config?url=${encodeURIComponent(s)}#${c}`).replace(/{{URL_CLASH}}/g,`clash://install-config?url=${encodeURIComponent(`https://revil-sub.pages.dev/sub/clash-meta?url=${o}`)}`).replace(/{{URL_EXCLAVE}}/g,`sn://subscription?url=${encodeURIComponent(o)}&name=${c}`),new Response(d,{headers:{"Content-Type":"text/html; charset=utf-8"}})}catch(i){return new Response(`Error rendering panel: ${i.message}`,{status:500,headers:{"Content-Type":"text/plain"}})}}var de={async fetch(e,t,a){try{let r=G.fromEnv(t),n=new URL(e.url),c=e.headers.get("Upgrade");if(c&&c.toLowerCase()==="websocket"){r.socks5.enabled&&!W&&(W=se(r.socks5.address));let s={userID:r.userID,proxyIP:r.proxyIP,proxyPort:r.proxyPort,socks5Address:r.socks5.address,socks5Relay:r.socks5.relayMode,enableSocks:r.socks5.enabled,parsedSocks5Address:r.socks5.enabled?W:{}};return J(e,s)}return n.pathname==="/scamalytics-lookup"?oe(e,r):n.pathname.startsWith(`/xray/${r.userID}`)?K(e,"xray",r.userID,n.hostname,a):n.pathname.startsWith(`/sb/${r.userID}`)?K(e,"sb",r.userID,n.hostname,a):n.pathname.startsWith(`/${r.userID}`)?ie(r.userID,n.hostname,r.proxyAddress):new Response("UUID not found. Please set the UUID environment variable in the Cloudflare dashboard.",{status:404})}catch(r){return new Response(`Worker Logic Error: ${r.message}
${r.stack}`,{status:500,headers:{"Content-Type":"text/plain"}})}}};export{de as default};

```

### .gitignore

```
.DS_Store
/node_modules
*-lock.*
*.lock
*.log
dist
```

### package.json

```json
{
  "name": "zizifn",
  "version": "1.0.0",
  "description": "So what?",
  "type": "module",
  "scripts": {
    "build": "node build.js"
  },
  "devDependencies": {
    "esbuild": "^0.20.0"
  }
}

```

