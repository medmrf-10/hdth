# خدمة الحديث — API ثابت

خدمة استعلام فورية للحديث وشروحه. أرسل طلب HTTP واحد واستقبل كل ما تحتاج — بلا تحميل ولا مفاتيح.

القاعدة: `https://medmrf-10.github.io/hdth/api/`

## الاستعلام الرئيسي: الحديث بكل شروحه في رد واحد

```
GET api/hadith/<قسم>/<رقم>.json.gz
```

الأقسام: `1`=جامع الأصول التسعة · `2`=معالم السنة · `3`=الواجيز · `4`=الكلية

مثال:

```bash
curl -s https://medmrf-10.github.io/hdth/api/hadith/1/6000.json.gz | gunzip
```

الرد — نص الحديث + مرجعه + بابه + حكمه + مواضعه في الكتب التسعة + **كل مقاطع شروحه الموثقة من 72 كتاباً**:

```json
{"id":6000,"cat":1,"no":1,"text":")ق( عن ابن عمر...","reference":"[خ8، م16]",
 "bab":"1 - باب: أركان الإسلام والإيمان","hukm":"",
 "locs":{"صحيح البخاري":{...},"صحيح مسلم":{...}},
 "n_sharh":42,
 "sharh":[{"sid":1478,"book":"أضواء البيان","author":"محمد الأمين الشنقيطي","j":"3","s":111,"ok":true,"text":"<مقطع الشرح كاملاً>"}, ...]}
```

`ok:true` = ربط موثّق (الصفحة تحوي المتن نفسه). `ok:false` = مرشّح احتمالي.

قوائم أرقام كل قسم: `GET api/hadith/<قسم>/_list.json`

## بقية نقاط الاستعلام

| الطلب | يرجع |
|---|---|
| `GET api/index.json` | الكتالوج الكامل وعدد الملفات |
| `GET api/matn/<كتاب>.json.gz` | متن كتاب (bukhari, muslim, abudawud, tirmidhi, nasai, ibnmajah, muwatta, ahmad, riyad, bulugh, mishkat, umdat, arbain, shafii, abi_hanifa) |
| `GET api/hadiths/<قسم>.json.gz` | كل أحاديث القسم بترميزها |
| `GET api/links/shami_<قسم>.json` | جدول الربط حديث↔شرح كاملاً |
| `GET api/sharh_index.json` | كتالوج كتب الشرح الـ72 (sid/عنوان/مؤلف) |
| `GET api/sharh/<sid>/toc.json` | فهرس أجزاء كتاب شرح |
| `GET api/sharh/<sid>/parts/<NNNN>.json.gz` | جزء نص شرح (تقسيم عند حدود الأبواب — بلا تداخل) |
| `GET api/segs/<sid>_<قسم>.json.gz` | مقاطع الشرح المستخرجة لكتاب |

## قاعدة البيانات الكاملة

`releases/` — `hadith.db.gz` (SQLite، 484MB): كل الجداول + بحث كامل النص FTS5 موحّد (hadith|matn|sharh) + مرافق `query.py`.

## ملاحظة الضغط

كل الملفات `.json.gz` — فك الضغط: `curl -s <url> | gunzip` أو `gzip.open` في بايثون.
## بحث نصي

`GET api/idx/<أول-حرفين-مطبّعين>.json.gz` ← `{كلمة:[[قسم,رقم]…]}` — تقاطع القوائم عندك للعبارات. التطبيع: بلا تشكيل ولا تطويل، أإآٱ→ا، ى→ي، ة→ه؛ السوابق لا تُقلع (مرّر الصيغ بنفسك). السياسة كاملة: `api/idx/_meta.json`.
