# פרומפט לפרויקט: אתר "דוח סיום התקנה"

> איך משתמשים: פותחים ב-VS Code את התיקייה `doch-hatkana`, פותחים את הצ'אט של העוזר (Copilot Chat במצב Agent, או כל עוזר AI אחר), ומדביקים את כל הקובץ הזה כהודעה ראשונה.

---

## מי אני ומה אני צריך

אני בעל עסק להתקנות (סיב, דירות, וילות), ואני לא מתכנת. אני בונה אתר פשוט למתקינים שלי ורוצה להבין כל שלב.
תסביר לי בעברית פשוטה, צעד אחרי צעד. אחרי כל צעד תגיד לי מה לבדוק, ותחכה לאישור שלי לפני שממשיכים.

## מטרת האתר

אתר עם לינק קבוע, ובו טופס "דוח סיום התקנה":
1. המתקין ממלא בטלפון את פרטי העבודה.
2. הוא לוחץ "הורדת PDF" ומקבל קובץ PDF מסודר.
3. הוא שולח את הקובץ בעצמו לקבוצת וואטסאפ של העבודה.

## כללים שאסור לשבור

- **אתר סטטי בלבד:** קבצי HTML, CSS ו-JavaScript. בלי שרת, בלי מסד נתונים, בלי התחברות ובלי שירותים בתשלום.
- **לא שומרים ולא שולחים נתונים:** שום מידע מהטופס לא יוצא מהטלפון של המתקין. בלי Google Analytics, בלי פיקסלים ובלי מעקב.
- **עברית מימין לשמאל**, מותאם קודם כל לטלפון, עם כפתורים גדולים שנוח ללחוץ עליהם באצבע.
- **אחסון ב-GitHub Pages**, ובהמשך עם דומיין משלי. האתר נמצא ב-https://doch-hatkana.github.io (המאגר `Doch-Hatkana/doch-hatkana.github.io`, בארגון Doch-Hatkana).
- לפני כל שינוי גדול במבנה, תשאל אותי.

## מבנה הפרויקט

```
doch-hatkana/
├── index.html                  ← כל האתר: מבנה, עיצוב והתנהגות
├── lib/
│   ├── html2pdf.bundle.min.js  ← ספריית יצירת ה-PDF (שמורה אצלנו, לא תלויה באתר חיצוני)
│   └── html2pdf.LICENSE
└── PROMPT.md                   ← הקובץ הזה
```

אם הקובץ `lib/html2pdf.bundle.min.js` חסר, הורד את גרסה 0.10.1 מכאן ושמור אותו בתיקייה `lib`:
https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js

## מה הטופס כולל (כבר בנוי בקוד שלמטה)

1. **פרטי העבודה (הכל חובה):** שם המתקין (שם מלא), תאריך, שם הלקוח, מספר לקוח, עיר, רחוב ומספר בית.
2. **סוג העבודה (חובה):** וילה / דירה (בדירה גם קומה, חובה, ומספר דירה), ואחר כך התקנה / קריאת שירות. כל התשובות בשאלות חובה.
   - **התקנה:** האם היה קיים סיב {בדירה/בווילה}? אם "לא", נשאל גם: האם בוצע טיפול חשמלאי (תיאור חובה כשעונים כן). אחר כך תמיד: התקנת כבל רשת (כמות בכן + תיאור רשות), התקנת מגדיל טווח (כמות בכן), מכירת מגדילי טווח במעמד ההתקנה, ובסוף הזזת שקע סיב (בחיוב לקוח), עם תיאור חובה כשעונים כן.
   - **קריאת שירות:** קודם "אנא סמן את סוג התקלה שבוצעה בפועל": רשימה של 13 סוגים, בוחרים אחד בלבד, חובה. בבחירת "אחר" נפתח שדה "פירוט" שחובה למלא, וב-PDF וב-CSV מופיע "אחר: <הפירוט>". אחריה ארבע שאלות עם כפתור "כן" בלבד, לא חובה: התקנת כבל רשת (כמות בכן), התקנת מגדיל טווח (כמות בכן), מכירת מגדיל טווח במעמד הקריאה, הזזת שקע סיב (תיאור חובה בכן). שאלה שלא סומן בה "כן" לא מופיעה ב-PDF, ובקובץ ה-CSV התא שלה ריק.
   - שאלה שמופיעה בשתי הרשימות עם אותו `title` חולקת עמודה אחת ב-CSV. השאלה "מה בוצע" (טקסט חופשי) הוחלפה ברשימת סוגי התקלה, ולכן העמודה "מה בוצע בקריאת השירות" הוחלפה בעמודה "סוג התקלה".
   - השאלות מוגדרות ברשימה `QUESTIONS` בראש הסקריפט. הצגה והסתרה של שדות לפי תשובות קודמות נעשות דרך `data-when` ו-`syncVisibility`.
3. **סיכום:** הערות (רשות). סטטוס העבודה הוסר.
4. **כפתורים:** "הורדה ושיתוף" יוצר שני קבצים, PDF וקובץ נתונים CSV למשרד, ופותח מיד את תפריט השיתוף של הטלפון עם שניהם. עותק של ה-PDF נשמר בהורדות. אם הטלפון חוסם את השיתוף האוטומטי, מופיע כפתור "שליחה בוואטסאפ". יש גם "ניקוי" לדוח חדש.
   - **שני הקבצים בשיתוף אחד רק באייפון** (`SHARE_CSV`). באנדרואיד ובמחשב משתפים רק את ה-PDF, קובץ ה-CSV נשמר בהורדות, ומופיעה הוראה לצרף אותו מתוך וואטסאפ (📎 ← מסמך). נוסח ההוראה שונה לטלפון ולמחשב (`csvSteps`). הסיבה, לפי בדיקות בטלפון אמיתי: וואטסאפ באנדרואיד דוחה CSV שמגיע מתפריט השיתוף יחד עם PDF ("לא ניתן לשלוח הודעה ללא תוכן"), ובמחשב ה-CSV נשלח דרך חלון השיתוף של ווינדוס אבל לא נפתח. צירוף מתוך וואטסאפ עצמו עובד בכל מכשיר. ניסיונות שלא עבדו: הוספת טקסט להודעה; CSV עם תווית `application/pdf` (נשלח, אבל לא נפתח במחשב); XLSX חסום לשיתוף מאתר ב-Chrome; שני כפתורי שיתוף נפרדים באנדרואיד (נבנו ובוטלו לבקשת בעל האתר).
5. **קובץ ה-CSV:** שורת כותרות ושורה אחת לדוח, עם אותן עמודות לכל הדוחות (התקנה וקריאת שירות, וילה ודירה), כדי שהמשרד יוכל לאחד את כל הקבצים לגיליון אחד באקסל ("קבלת נתונים ← מתיקייה"). למה CSV ולא xlsx: Chrome באנדרואיד לא מאפשר לשתף קבצי xlsx מאתר. הקובץ מתחיל ב-BOM כדי שאקסל יציג עברית תקינה.
   (תמונות, חתימה וציוד שהותקן הוסרו לבקשת בעל העסק.)

ה-PDF כולל רק שדות שמולאו, ורק את המסלול שנבחר.

## בעיות ידועות שכבר נפתרו בקוד (אל תשבור אותן)

- **רווחים בין מילים בעברית נעלמים ב-PDF:** זה באג של html2canvas. הפתרון בקוד הוא `letter-spacing:.2px` על `.doc`.
- **ה-PDF יוצא חתוך או מוזז בדף מימין לשמאל:** בזמן יצירת ה-PDF הקוד מחליף זמנית את כיוון הדף ל-LTR, ומכסה את המסך בהודעת "מכין את קובץ ה-PDF".
- **ה-PDF יוצא קטוע וזז הצידה במסך רחב (מחשב):** הפתרון הוא `x: 0, y: 0` בהגדרות html2canvas.
- **סוגריים יוצאים הפוכים ב-PDF:** לא משתמשים בסוגריים בכיתוב שנכנס ל-PDF.
- **עמוד ראשון ריק ב-PDF בטלפון:** html2pdf חישבה שבירת עמודים לפי המיקום על המסך, שבטלפון יכול להיות מוזז (למשל כשהמקלדת פתוחה). הפתרון: `addPageBreaks` מחשבת את שבירת העמודים בתוך המסמך עצמו, ו-`pagebreak: { mode: [] }` מכבה את החישוב של html2pdf. בנוסף המקלדת נסגרת לפני יצירת ה-PDF.

## המשימות שלך, לפי הסדר

1. **בדיקה מקומית:** ודא שכל הקבצים במקום. תדריך אותי להתקין את התוסף **Live Server**, לפתוח את האתר ולבדוק אותו במחשב ובטלפון (באותה רשת Wi-Fi).
2. **התאמה אישית:** שאל אותי מה שם החברה (המשתנה `COMPANY` בראש הסקריפט), ומה השאלות האמיתיות לכל מסלול. עדכן את הטופס בהתאם.
3. **Git:** תסביר לי בקצרה מה זה Git. תדריך אותי להתקין אותו, לפתוח מאגר (`git init`), ולעשות commit ראשון עם הודעה ברורה.
4. **GitHub:** תדריך אותי לפתוח חשבון ב-GitHub, ליצור מאגר חדש ולהעלות אליו את הקוד דרך VS Code (Publish to GitHub).
   - חשוב לדעת: בחשבון חינמי, GitHub Pages עובד רק עם מאגר **ציבורי**, כלומר כל אחד יכול לראות את הקוד. זה בסדר, כי אין בקוד שום מידע על לקוחות.
5. **GitHub Pages:** תדריך אותי להפעיל אותו (Settings ← Pages ← Deploy from a branch ← `main` / root). אחר כך נבדוק שהלינק עובד מהטלפון, מחוץ לרשת הבית.
6. **דומיין משלי (בהמשך):** תסביר מה זה DNS, ואיך מחברים דומיין ל-GitHub Pages (קובץ `CNAME` והגדרות אצל הרשם). תזכיר לי להפעיל חידוש אוטומטי לדומיין ואת האפשרות Enforce HTTPS.
7. **רשימת בדיקות לפני שמפיצים:** אייפון ואנדרואיד, כל שלושת המסלולים, הודעות שגיאה לשדות חובה, עברית תקינה ב-PDF, ושליחה לקבוצת בדיקה בוואטסאפ.

## הקוד המלא של index.html

אם הקובץ כבר קיים בתיקייה, אל תכתוב אותו מחדש. השתמש בקוד הזה רק כדי ליצור את הקובץ אם הוא חסר:

````html
<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>דוח סיום התקנה</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans+Hebrew:wght@400;500;700&display=swap" rel="stylesheet">
<script src="lib/html2pdf.bundle.min.js"></script>
<style>
  :root{
    --ink:#15233B;      /* navy ink */
    --steel:#5A6A7E;    /* secondary text */
    --line:#CFD8E2;     /* borders */
    --ground:#E9EEF3;   /* page background */
    --paper:#FFFFFF;
    --hivis:#FFC629;    /* safety yellow: the one loud colour */
    --error:#B3261E;
    --ok:#1F7A4D;
    --radius-field:8px;
  }
  *{box-sizing:border-box}
  html,body{margin:0}
  body{
    font-family:"IBM Plex Sans Hebrew",system-ui,-apple-system,"Segoe UI",Arial,sans-serif;
    background:var(--ground);
    color:var(--ink);
    font-size:17px;
    line-height:1.5;
    -webkit-text-size-adjust:100%;
    padding-bottom:110px;
  }
  .wrap{max-width:640px;margin:0 auto;padding:0 16px}

  /* Header: a work-order slip */
  header.slip{
    background:var(--ink);color:#fff;
    padding:22px 0 26px;
    border-bottom:6px solid var(--hivis);
  }
  .slip .company{font-size:15px;color:#B9C4D2;margin:0 0 4px}
  .slip h1{font-size:30px;line-height:1.15;margin:0 0 14px;font-weight:700}
  .slip .meta{display:flex;flex-wrap:wrap;gap:8px 20px;font-size:15px;color:#DCE3EC}
  .slip .meta b{color:#fff;font-weight:500;font-variant-numeric:tabular-nums}

  main{padding-top:18px}
  fieldset{
    border:1px solid var(--line);
    background:var(--paper);
    border-radius:12px;
    margin:0 0 14px;
    padding:18px 16px 6px;
    min-width:0;
  }
  legend{
    float:right;width:100%;
    font-weight:700;font-size:19px;
    margin:0 0 12px;padding:0;
    display:flex;align-items:center;gap:10px;
  }
  legend .n{
    display:inline-grid;place-items:center;
    width:28px;height:28px;border-radius:50%;
    background:var(--ink);color:#fff;font-size:15px;flex:none;
  }
  legend + *{clear:both}

  .field{margin:0 0 14px}
  .row{display:grid;grid-template-columns:1fr 1fr;gap:0 12px}
  label.lbl{display:block;font-weight:500;font-size:15px;margin:0 0 5px}
  .req::after{content:" *";color:var(--error)}
  .hint{font-size:14px;color:var(--steel);margin:4px 0 0}
  input[type=text],input[type=tel],input[type=date],input[type=number],select,textarea{
    width:100%;
    font:inherit;font-size:17px;color:var(--ink);
    background:#fff;
    border:1.5px solid var(--line);
    border-radius:var(--radius-field);
    padding:11px 12px;
    min-height:48px;
  }
  textarea{min-height:92px;resize:vertical}
  input:focus,select:focus,textarea:focus{outline:none;border-color:var(--ink);box-shadow:0 0 0 3px rgba(21,35,59,.15)}
  .invalid input,.invalid select,.invalid textarea,.invalid .chips{border-color:var(--error)}
  .err{display:none;color:var(--error);font-size:14px;margin:5px 0 0}
  .invalid .err{display:block}

  /* Chip choices (radio) */
  .chips{display:flex;flex-wrap:wrap;gap:8px;border:1.5px solid transparent;border-radius:10px}
  .chips input{position:absolute;opacity:0;pointer-events:none}
  .chips label{
    flex:1 1 0;min-width:84px;text-align:center;
    padding:11px 6px;min-height:48px;white-space:nowrap;
    border:1.5px solid var(--line);border-radius:var(--radius-field);
    background:#fff;cursor:pointer;font-weight:500;
    display:flex;align-items:center;justify-content:center;
  }
  .chips input:checked + label{background:var(--ink);border-color:var(--ink);color:#fff}
  .chips input:focus-visible + label{outline:3px solid var(--hivis);outline-offset:2px}

  /* The installation type picker is the loud element */
  .type-pick .chips label{font-size:19px;font-weight:700;min-height:64px}
  .type-pick .chips input:checked + label{background:var(--hivis);border-color:var(--ink);color:var(--ink);box-shadow:inset 0 0 0 1.5px var(--ink)}

  .branch{display:none}
  .branch.on{display:block}

  /* Photos */
  .photos{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-top:10px}
  .photos figure{margin:0;position:relative;aspect-ratio:1;border-radius:8px;overflow:hidden;border:1px solid var(--line)}
  .photos img{width:100%;height:100%;object-fit:cover;display:block}
  .photos button{
    position:absolute;top:4px;left:4px;width:30px;height:30px;border-radius:50%;
    border:0;background:rgba(21,35,59,.85);color:#fff;font-size:18px;line-height:1;cursor:pointer;
  }
  .filebtn{
    display:flex;align-items:center;justify-content:center;gap:8px;
    border:1.5px dashed var(--steel);border-radius:var(--radius-field);
    min-height:52px;padding:10px;cursor:pointer;font-weight:500;background:#F7F9FB;
  }
  .filebtn:focus-within{outline:3px solid var(--hivis);outline-offset:2px}
  .filebtn input{position:absolute;opacity:0;width:1px;height:1px}

  /* Signature */
  .sigbox{position:relative;border:1.5px solid var(--line);border-radius:var(--radius-field);background:#fff;touch-action:none}
  .sigbox canvas{display:block;width:100%;height:170px;cursor:crosshair}
  .sigbox .baseline{position:absolute;right:16px;left:16px;bottom:38px;border-top:1px dashed var(--line);pointer-events:none}
  .sigbox .ph{position:absolute;right:16px;bottom:12px;font-size:14px;color:var(--steel);pointer-events:none}
  .linkbtn{background:none;border:0;color:var(--ink);text-decoration:underline;font:inherit;font-size:15px;padding:8px 0;cursor:pointer}

  .privacy{font-size:14px;color:var(--steel);margin:4px 2px 18px}

  /* Sticky action bar */
  .bar{
    position:fixed;inset:auto 0 0 0;
    background:var(--paper);border-top:1px solid var(--line);
    padding:12px 0 calc(12px + env(safe-area-inset-bottom));
    z-index:5;
  }
  .bar .wrap{display:flex;gap:10px}
  .btn{
    font:inherit;font-weight:700;font-size:18px;
    min-height:54px;border-radius:10px;cursor:pointer;
    padding:0 18px;border:2px solid var(--ink);
  }
  .btn.primary{flex:1;background:var(--ink);color:#fff}
  .btn.secondary{background:#fff;color:var(--ink)}
  .btn:focus-visible{outline:3px solid var(--hivis);outline-offset:2px}
  .btn[disabled]{opacity:.6;cursor:progress}

  /* Status / result panel */
  .notice{
    display:none;border-radius:12px;padding:14px 16px;margin:0 0 14px;
    background:#fff;border:1.5px solid var(--ink);
  }
  .notice.on{display:block}
  .notice h2{font-size:18px;margin:0 0 6px}
  .notice p{margin:0 0 6px}
  .notice ol{margin:6px 0 0;padding-inline-start:22px}
  .notice.bad{border-color:var(--error)}
  .notice .actions{display:flex;gap:10px;margin-top:10px;flex-wrap:wrap}

  /* Dialog for clearing */
  .dlg{position:fixed;inset:0;background:rgba(21,35,59,.5);display:none;align-items:center;justify-content:center;z-index:10;padding:16px}
  .dlg.on{display:flex}
  .dlg .box{background:#fff;border-radius:14px;padding:20px;max-width:380px;width:100%}
  .dlg h2{margin:0 0 8px;font-size:20px}
  .dlg .actions{display:flex;gap:10px;margin-top:16px}
  .dlg .actions .btn{flex:1}

  .busy{position:fixed;inset:0;z-index:20;background:var(--ground);display:none;align-items:center;justify-content:center;font-weight:700;font-size:20px}
  .busy.on{display:flex}

  /* PDF render target (A4 @ 96dpi = 794px wide) — kept off-screen */
  #pdfStage{position:fixed;top:0;left:-10000px;width:794px;pointer-events:none}
  .doc{letter-spacing:.2px;width:794px;padding:44px 48px;background:#fff;color:#15233B;font-family:"IBM Plex Sans Hebrew",Arial,sans-serif;font-size:14px;line-height:1.45;direction:rtl}
  .doc .top{display:flex;justify-content:space-between;align-items:flex-end;border-bottom:4px solid #FFC629;padding-bottom:14px;margin-bottom:18px}
  .doc .top .co{font-size:14px;color:#5A6A7E}
  .doc .top h1{font-size:26px;margin:2px 0 0}
  .doc .top .no{text-align:left;font-size:13px;color:#5A6A7E}
  .doc .top .no b{display:block;color:#15233B;font-size:15px}
  .doc h2{font-size:15px;margin:16px 0 6px;padding:5px 10px;background:#15233B;color:#fff;border-radius:4px}
  .doc table{width:100%;border-collapse:collapse}
  .doc td{border-bottom:1px solid #DDE3EA;padding:6px 10px;vertical-align:top}
  .doc td.k{width:34%;color:#5A6A7E}
  .doc td.v{font-weight:500;white-space:pre-wrap}
  .doc .pics{display:grid;grid-template-columns:repeat(2,1fr);gap:10px;margin-top:6px}
  .doc .pics img{width:100%;height:230px;object-fit:cover;border:1px solid #DDE3EA;border-radius:4px;display:block}
  .doc .sign{display:flex;gap:24px;align-items:flex-end;margin-top:6px}
  .doc .sign img{height:90px;border-bottom:1px solid #15233B}
  .doc .foot{margin-top:22px;padding-top:10px;border-top:1px solid #DDE3EA;font-size:11px;color:#5A6A7E}
  .doc .avoid{page-break-inside:avoid;break-inside:avoid}

  @media (max-width:420px){
    .row{grid-template-columns:1fr}
    .photos{grid-template-columns:repeat(3,1fr)}
    .slip h1{font-size:26px}
  }
  @media (prefers-reduced-motion:reduce){*{transition:none!important;animation:none!important}}
</style>
</head>
<body>

<header class="slip">
  <div class="wrap">
    <p class="company" id="companyName">שם החברה</p>
    <h1>דוח סיום התקנה</h1>
    <div class="meta">
      <span>מספר דוח <b id="reportNo">—</b></span>
      <span>תאריך <b id="todayLbl">—</b></span>
    </div>
  </div>
</header>

<main class="wrap">
  <div class="notice" id="notice" role="status" aria-live="polite"></div>

  <form id="form" novalidate>

    <fieldset>
      <legend><span class="n">1</span>פרטי העבודה</legend>
      <div class="row">
        <div class="field" data-req>
          <label class="lbl req" for="installer">שם המתקין</label>
          <input type="text" id="installer" name="installer" autocomplete="name">
          <p class="err">יש למלא את שם המתקין</p>
        </div>
        <div class="field">
          <label class="lbl" for="date">תאריך ההתקנה</label>
          <input type="date" id="date" name="date">
        </div>
      </div>
      <div class="row">
        <div class="field" data-req>
          <label class="lbl req" for="customer">שם הלקוח</label>
          <input type="text" id="customer" name="customer">
          <p class="err">יש למלא את שם הלקוח</p>
        </div>
        <div class="field">
          <label class="lbl" for="phone">טלפון הלקוח</label>
          <input type="tel" id="phone" name="phone" inputmode="tel" dir="ltr" style="text-align:right">
        </div>
      </div>
      <div class="field" data-req>
        <label class="lbl req" for="address">כתובת ההתקנה</label>
        <input type="text" id="address" name="address" placeholder="רחוב, מספר, עיר">
        <p class="err">יש למלא את כתובת ההתקנה</p>
      </div>
    </fieldset>

    <fieldset class="type-pick">
      <legend><span class="n">2</span>סוג ההתקנה</legend>
      <div class="field" data-req data-radio="type">
        <div class="chips" role="radiogroup" aria-label="סוג ההתקנה">
          <input type="radio" name="type" id="t-fiber" value="סיב"><label for="t-fiber">סיב</label>
          <input type="radio" name="type" id="t-apt" value="דירה"><label for="t-apt">דירה</label>
          <input type="radio" name="type" id="t-villa" value="וילה"><label for="t-villa">וילה</label>
        </div>
        <p class="err">יש לבחור סוג התקנה</p>
        <p class="hint">השאלות בשלב הבא משתנות לפי הבחירה.</p>
      </div>
    </fieldset>

    <!-- Branch: fiber -->
    <fieldset class="branch" data-branch="סיב">
      <legend><span class="n">3</span>פרטי התקנת סיב</legend>
      <div class="field">
        <label class="lbl" for="f_line">מספר מנוי / מזהה קו</label>
        <input type="text" id="f_line" name="f_line" data-label="מספר מנוי / מזהה קו" dir="ltr" style="text-align:right">
      </div>
      <div class="field">
        <label class="lbl" for="f_entry">נקודת כניסה</label>
        <select id="f_entry" name="f_entry" data-label="נקודת כניסה">
          <option value="">בחירה</option>
          <option>עמוד</option>
          <option>תת-קרקעי</option>
          <option>ארון תקשורת בבניין</option>
        </select>
      </div>
      <div class="field">
        <label class="lbl" for="f_cable">אורך כבל שנפרס (מטר)</label>
        <input type="number" id="f_cable" name="f_cable" data-label="אורך כבל שנפרס (מטר)" inputmode="numeric" min="0">
      </div>
      <div class="field">
        <span class="lbl">ציוד קצה שהותקן</span>
        <div class="chips">
          <input type="radio" name="f_cpe" id="f_cpe1" value="ONT" data-label="ציוד קצה שהותקן"><label for="f_cpe1">ONT</label>
          <input type="radio" name="f_cpe" id="f_cpe2" value="ראוטר" data-label="ציוד קצה שהותקן"><label for="f_cpe2">ראוטר</label>
          <input type="radio" name="f_cpe" id="f_cpe3" value="ONT + ראוטר" data-label="ציוד קצה שהותקן"><label for="f_cpe3">שניהם</label>
        </div>
      </div>
      <div class="row">
        <div class="field">
          <label class="lbl" for="f_down">מהירות הורדה (Mbps)</label>
          <input type="number" id="f_down" name="f_down" data-label="מהירות הורדה (Mbps)" inputmode="decimal" min="0">
        </div>
        <div class="field">
          <label class="lbl" for="f_up">מהירות העלאה (Mbps)</label>
          <input type="number" id="f_up" name="f_up" data-label="מהירות העלאה (Mbps)" inputmode="decimal" min="0">
        </div>
      </div>
    </fieldset>

    <!-- Branch: apartment -->
    <fieldset class="branch" data-branch="דירה">
      <legend><span class="n">3</span>פרטי התקנה בדירה</legend>
      <div class="row">
        <div class="field">
          <label class="lbl" for="a_floor">קומה</label>
          <input type="number" id="a_floor" name="a_floor" data-label="קומה" inputmode="numeric">
        </div>
        <div class="field">
          <label class="lbl" for="a_unit">מספר דירה</label>
          <input type="text" id="a_unit" name="a_unit" data-label="מספר דירה">
        </div>
      </div>
      <div class="field">
        <label class="lbl" for="a_cabinet">מיקום ארון התקשורת</label>
        <input type="text" id="a_cabinet" name="a_cabinet" data-label="מיקום ארון התקשורת" placeholder="למשל: מסדרון, ליד הכניסה">
      </div>
      <div class="field">
        <label class="lbl" for="a_points">מספר נקודות רשת שהותקנו</label>
        <input type="number" id="a_points" name="a_points" data-label="מספר נקודות רשת שהותקנו" inputmode="numeric" min="0">
      </div>
      <div class="field">
        <label class="lbl" for="a_route">דרך החיווט</label>
        <select id="a_route" name="a_route" data-label="דרך החיווט">
          <option value="">בחירה</option>
          <option>צנרת קיימת</option>
          <option>תעלה חיצונית</option>
          <option>שילוב של שתיהן</option>
        </select>
      </div>
    </fieldset>

    <!-- Branch: villa -->
    <fieldset class="branch" data-branch="וילה">
      <legend><span class="n">3</span>פרטי התקנה בווילה</legend>
      <div class="field">
        <label class="lbl" for="v_floors">מספר קומות בבית</label>
        <input type="number" id="v_floors" name="v_floors" data-label="מספר קומות בבית" inputmode="numeric" min="1">
      </div>
      <div class="row">
        <div class="field">
          <label class="lbl" for="v_in">נקודות פנים</label>
          <input type="number" id="v_in" name="v_in" data-label="נקודות פנים" inputmode="numeric" min="0">
        </div>
        <div class="field">
          <label class="lbl" for="v_out">נקודות חוץ</label>
          <input type="number" id="v_out" name="v_out" data-label="נקודות חוץ" inputmode="numeric" min="0">
        </div>
      </div>
      <div class="field">
        <span class="lbl">הותקנה רשת Mesh</span>
        <div class="chips">
          <input type="radio" name="v_mesh" id="v_mesh1" value="כן" data-label="הותקנה רשת Mesh"><label for="v_mesh1">כן</label>
          <input type="radio" name="v_mesh" id="v_mesh2" value="לא" data-label="הותקנה רשת Mesh"><label for="v_mesh2">לא</label>
        </div>
      </div>
      <div class="field">
        <span class="lbl">בוצעה חפירה או קידוח</span>
        <div class="chips">
          <input type="radio" name="v_dig" id="v_dig1" value="כן" data-label="בוצעה חפירה או קידוח"><label for="v_dig1">כן</label>
          <input type="radio" name="v_dig" id="v_dig2" value="לא" data-label="בוצעה חפירה או קידוח"><label for="v_dig2">לא</label>
        </div>
      </div>
    </fieldset>

    <fieldset>
      <legend><span class="n" id="n4">3</span>סיכום העבודה</legend>
      <div class="field">
        <label class="lbl" for="equipment">ציוד שהותקן ומספרים סידוריים</label>
        <textarea id="equipment" name="equipment" placeholder="פריט אחד בכל שורה"></textarea>
      </div>
      <div class="field" data-req data-radio="status">
        <span class="lbl req">סטטוס העבודה</span>
        <div class="chips">
          <input type="radio" name="status" id="s1" value="הושלמה"><label for="s1">הושלמה</label>
          <input type="radio" name="status" id="s2" value="הושלמה חלקית"><label for="s2">חלקית</label>
          <input type="radio" name="status" id="s3" value="לא הושלמה"><label for="s3">לא הושלמה</label>
        </div>
        <p class="err">יש לבחור סטטוס</p>
      </div>
      <div class="field">
        <label class="lbl" for="notes">הערות</label>
        <textarea id="notes" name="notes" placeholder="מה נשאר לעשות, תקלות, בקשות של הלקוח"></textarea>
      </div>
    </fieldset>

    <fieldset>
      <legend><span class="n" id="n5">4</span>תמונות מהשטח</legend>
      <label class="filebtn">
        <input type="file" id="photoInput" accept="image/*" multiple>
        <span>הוספת תמונות</span>
      </label>
      <p class="hint">עד 4 תמונות. הן ייכנסו לקובץ ה-PDF.</p>
      <div class="photos" id="photos"></div>
      <div style="height:12px"></div>
    </fieldset>

    <fieldset>
      <legend><span class="n" id="n6">5</span>אישור הלקוח</legend>
      <div class="field">
        <label class="lbl" for="signer">שם החותם</label>
        <input type="text" id="signer" name="signer">
      </div>
      <div class="field">
        <span class="lbl">חתימה</span>
        <div class="sigbox">
          <canvas id="sig" aria-label="אזור חתימה"></canvas>
          <div class="baseline"></div>
          <span class="ph" id="sigPh">חתמו כאן באצבע</span>
        </div>
        <button type="button" class="linkbtn" id="sigClear">ניקוי החתימה</button>
      </div>
    </fieldset>

    <p class="privacy">הפרטים בטופס משמשים לתיעוד ההתקנה ולמתן שירות ללקוח בלבד. הטופס לא נשמר באתר: קובץ ה-PDF נוצר במכשיר שלכם.</p>
  </form>
</main>

<div class="bar">
  <div class="wrap">
    <button type="button" class="btn secondary" id="clearBtn">ניקוי</button>
    <button type="button" class="btn primary" id="pdfBtn">הורדת PDF</button>
  </div>
</div>

<div class="dlg" id="clearDlg" role="dialog" aria-modal="true" aria-labelledby="clearTitle">
  <div class="box">
    <h2 id="clearTitle">לנקות את הטופס?</h2>
    <p style="margin:0">כל השדות, התמונות והחתימה יימחקו כדי להתחיל דוח חדש.</p>
    <div class="actions">
      <button type="button" class="btn secondary" id="clearNo">ביטול</button>
      <button type="button" class="btn primary" id="clearYes">ניקוי הטופס</button>
    </div>
  </div>
</div>

<div id="pdfStage" aria-hidden="true"></div>
<div class="busy" id="busy" role="status"><p>מכין את קובץ ה-PDF…</p></div>

<script>
/* ---------- Settings you may want to change ---------- */
const COMPANY = "שם החברה";      // shown at the top of the page and the PDF
const MAX_PHOTOS = 4;

/* ---------- Basics ---------- */
const $ = (s, r = document) => r.querySelector(s);
const $$ = (s, r = document) => Array.from(r.querySelectorAll(s));
const form = $("#form");
const pad = n => String(n).padStart(2, "0");

$("#companyName").textContent = COMPANY;
document.title = "דוח סיום התקנה · " + COMPANY;

let reportNo = "";
function newReportNo() {
  const d = new Date();
  reportNo = `${d.getFullYear()}${pad(d.getMonth() + 1)}${pad(d.getDate())}-${pad(d.getHours())}${pad(d.getMinutes())}`;
  $("#reportNo").textContent = reportNo;
  $("#todayLbl").textContent = `${pad(d.getDate())}/${pad(d.getMonth() + 1)}/${d.getFullYear()}`;
  $("#date").value = `${d.getFullYear()}-${pad(d.getMonth() + 1)}-${pad(d.getDate())}`;
}
newReportNo();

function fmtDate(v) {
  if (!v) return "";
  const [y, m, d] = v.split("-");
  return `${d}/${m}/${y}`;
}

/* ---------- Branching ---------- */
function currentType() {
  const r = $('input[name="type"]:checked');
  return r ? r.value : "";
}
function updateBranch() {
  const t = currentType();
  $$(".branch").forEach(b => b.classList.toggle("on", b.dataset.branch === t));
  const offset = t ? 1 : 0;
  $("#n4").textContent = 3 + offset;
  $("#n5").textContent = 4 + offset;
  $("#n6").textContent = 5 + offset;
}
$$('input[name="type"]').forEach(r => r.addEventListener("change", updateBranch));

/* ---------- Validation ---------- */
function validate() {
  let first = null;
  $$("[data-req]").forEach(f => {
    let ok;
    if (f.dataset.radio) ok = !!$(`input[name="${f.dataset.radio}"]:checked`);
    else ok = $("input,select,textarea", f).value.trim() !== "";
    f.classList.toggle("invalid", !ok);
    if (!ok && !first) first = f;
  });
  if (first) {
    first.scrollIntoView({ behavior: "smooth", block: "center" });
    const el = $("input,select,textarea", first);
    if (el) setTimeout(() => el.focus({ preventScroll: true }), 350);
  }
  return !first;
}
form.addEventListener("input", e => {
  const f = e.target.closest("[data-req]");
  if (f && f.classList.contains("invalid")) {
    const ok = f.dataset.radio ? !!$(`input[name="${f.dataset.radio}"]:checked`) : e.target.value.trim() !== "";
    if (ok) f.classList.remove("invalid");
  }
});
form.addEventListener("change", e => {
  const f = e.target.closest("[data-radio]");
  if (f) f.classList.remove("invalid");
});
form.addEventListener("submit", e => e.preventDefault());

/* ---------- Photos ---------- */
let photos = []; // data URLs (downscaled JPEG)
function downscale(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onerror = reject;
    reader.onload = () => {
      const img = new Image();
      img.onerror = reject;
      img.onload = () => {
        const max = 1400;
        const k = Math.min(1, max / Math.max(img.width, img.height));
        const c = document.createElement("canvas");
        c.width = Math.round(img.width * k);
        c.height = Math.round(img.height * k);
        c.getContext("2d").drawImage(img, 0, 0, c.width, c.height);
        resolve(c.toDataURL("image/jpeg", 0.82));
      };
      img.src = reader.result;
    };
    reader.readAsDataURL(file);
  });
}
function renderPhotos() {
  const box = $("#photos");
  box.innerHTML = "";
  photos.forEach((src, i) => {
    const fig = document.createElement("figure");
    const img = document.createElement("img");
    img.src = src; img.alt = `תמונה ${i + 1}`;
    const del = document.createElement("button");
    del.type = "button"; del.textContent = "×";
    del.setAttribute("aria-label", `הסרת תמונה ${i + 1}`);
    del.onclick = () => { photos.splice(i, 1); renderPhotos(); };
    fig.append(img, del);
    box.append(fig);
  });
}
$("#photoInput").addEventListener("change", async e => {
  const files = Array.from(e.target.files || []);
  const room = MAX_PHOTOS - photos.length;
  if (files.length > room) showNotice("bad", "יותר מדי תמונות", `אפשר לצרף עד ${MAX_PHOTOS} תמונות. נוספו ${Math.max(room, 0)} הראשונות.`);
  for (const f of files.slice(0, Math.max(room, 0))) {
    try { photos.push(await downscale(f)); } catch (_) { /* skip unreadable file */ }
  }
  e.target.value = "";
  renderPhotos();
});

/* ---------- Signature pad ---------- */
const sig = $("#sig");
const sctx = sig.getContext("2d");
// Strokes are kept as points (0..1 of the pad's size) so the signature survives
// phone resizes (keyboard opening, address bar hiding) and redraws instantly.
let drawing = false, signed = false, strokes = [], cur = null;
function paint() {
  const w = sig.clientWidth, h = sig.clientHeight;
  sctx.save(); sctx.setTransform(1, 0, 0, 1, 0, 0); sctx.clearRect(0, 0, sig.width, sig.height); sctx.restore();
  sctx.lineWidth = 2.4; sctx.lineCap = "round"; sctx.lineJoin = "round";
  sctx.strokeStyle = "#15233B"; sctx.fillStyle = "#15233B";
  for (const s of strokes) {
    if (s.length === 1) { sctx.beginPath(); sctx.arc(s[0][0] * w, s[0][1] * h, 1.2, 0, Math.PI * 2); sctx.fill(); continue; }
    sctx.beginPath(); sctx.moveTo(s[0][0] * w, s[0][1] * h);
    for (let i = 1; i < s.length; i++) sctx.lineTo(s[i][0] * w, s[i][1] * h);
    sctx.stroke();
  }
}
function sizeSig() {
  const dpr = window.devicePixelRatio || 1;
  const w = Math.round(sig.clientWidth * dpr), h = Math.round(sig.clientHeight * dpr);
  if (!w || !h) return;
  if (sig.width !== w || sig.height !== h) { sig.width = w; sig.height = h; }
  sctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  paint();
}
function pt(e) { const r = sig.getBoundingClientRect(); return [(e.clientX - r.left) / r.width, (e.clientY - r.top) / r.height]; }
sig.addEventListener("pointerdown", e => {
  drawing = true; cur = [pt(e)]; strokes.push(cur);
  try { sig.setPointerCapture(e.pointerId); } catch (_) {}
  if (!signed) { signed = true; $("#sigPh").style.display = "none"; }
  paint();
});
sig.addEventListener("pointermove", e => {
  if (!drawing) return;
  cur.push(pt(e)); paint();
});
["pointerup", "pointercancel"].forEach(ev => sig.addEventListener(ev, () => { drawing = false; }));
function clearSig() {
  signed = false; strokes = []; paint();
  $("#sigPh").style.display = "";
}
$("#sigClear").addEventListener("click", clearSig);
window.addEventListener("resize", sizeSig);
sizeSig();

/* ---------- Notice panel ---------- */
const notice = $("#notice");
function showNotice(kind, title, html) {
  notice.className = "notice on" + (kind === "bad" ? " bad" : "");
  notice.innerHTML = `<h2></h2><div class="body"></div>`;
  $("h2", notice).textContent = title;
  $(".body", notice).innerHTML = html;
  notice.scrollIntoView({ behavior: "smooth", block: "start" });
}
function hideNotice() { notice.className = "notice"; notice.innerHTML = ""; }

/* ---------- Build the PDF document ---------- */
function esc(s) { return String(s).replace(/[&<>"]/g, c => ({ "&": "&amp;", "<": "&lt;", ">": "&gt;", '"': "&quot;" }[c])); }
function rows(pairs) {
  const filled = pairs.filter(([, v]) => v !== undefined && String(v).trim() !== "");
  if (!filled.length) return "";
  return "<table>" + filled.map(([k, v]) => `<tr><td class="k">${esc(k)}</td><td class="v">${esc(v)}</td></tr>`).join("") + "</table>";
}
function val(id) { const el = document.getElementById(id); return el ? el.value.trim() : ""; }
function branchPairs(t) {
  const fs = $(`.branch[data-branch="${t}"]`);
  if (!fs) return [];
  const pairs = [];
  const seen = new Set();
  $$("[data-label]", fs).forEach(el => {
    if (el.type === "radio") {
      if (seen.has(el.name)) return;
      seen.add(el.name);
      const c = $(`input[name="${el.name}"]:checked`, fs);
      pairs.push([el.dataset.label, c ? c.value : ""]);
    } else {
      pairs.push([el.dataset.label, el.value.trim()]);
    }
  });
  return pairs;
}
function buildDoc() {
  const t = currentType();
  const status = ($('input[name="status"]:checked') || {}).value || "";
  const parts = [];
  parts.push(`<div class="top">
      <div><div class="co">${esc(COMPANY)}</div><h1>דוח סיום התקנה</h1></div>
      <div class="no">מספר דוח<b>${esc(reportNo)}</b></div>
    </div>`);
  parts.push(`<div class="avoid"><h2>פרטי העבודה</h2>${rows([
    ["תאריך ההתקנה", fmtDate(val("date"))],
    ["שם המתקין", val("installer")],
    ["שם הלקוח", val("customer")],
    ["טלפון הלקוח", val("phone")],
    ["כתובת ההתקנה", val("address")],
    ["סוג ההתקנה", t],
  ])}</div>`);
  const b = rows(branchPairs(t));
  if (b) parts.push(`<div class="avoid"><h2>פרטי התקנה: ${esc(t)}</h2>${b}</div>`);
  parts.push(`<div class="avoid"><h2>סיכום העבודה</h2>${rows([
    ["סטטוס העבודה", status],
    ["ציוד ומספרים סידוריים", val("equipment")],
    ["הערות", val("notes")],
  ])}</div>`);
  if (photos.length) {
    parts.push(`<div class="avoid"><h2>תמונות מהשטח</h2><div class="pics">${photos.map(p => `<img src="${p}">`).join("")}</div></div>`);
  }
  if (signed || val("signer")) {
    parts.push(`<div class="avoid"><h2>אישור הלקוח</h2><div class="sign">
      ${signed ? `<img src="${sig.toDataURL("image/png")}">` : ""}
      <div>${val("signer") ? "שם החותם: <b>" + esc(val("signer")) + "</b>" : ""}</div>
    </div></div>`);
  }
  parts.push(`<div class="foot">הפרטים בדוח נאספו לצורך תיעוד ההתקנה ומתן שירות ללקוח. נוצר בתאריך ${esc($("#todayLbl").textContent)}.</div>`);
  const doc = document.createElement("div");
  doc.className = "doc";
  doc.innerHTML = parts.join("");
  return doc;
}
function fileName() {
  const c = val("customer").replace(/[\\/:*?"<>|]+/g, "").replace(/\s+/g, "-") || "לקוח";
  return `דוח-התקנה-${c}-${reportNo}.pdf`;
}
async function makePdfBlob() {
  if (typeof html2pdf === "undefined") throw new Error("lib");
  const stage = $("#pdfStage");
  stage.innerHTML = "";
  const doc = buildDoc();
  stage.append(doc);
  // wait for embedded images to decode
  await Promise.all($$("img", doc).map(im => im.decode ? im.decode().catch(() => {}) : Promise.resolve()));
  try { if (document.fonts && document.fonts.ready) await document.fonts.ready; } catch (_) {}
  const opt = {
    margin: [8, 0, 10, 0],
    image: { type: "jpeg", quality: 0.92 },
    html2canvas: { scale: 2, useCORS: true, backgroundColor: "#ffffff", windowWidth: 794, scrollX: 0, scrollY: 0 },
    jsPDF: { unit: "mm", format: "a4", orientation: "portrait" },
    pagebreak: { mode: ["css", "legacy"], avoid: ".avoid" },
  };
  // html2canvas mis-positions content in right-to-left pages, so render with an LTR root
  // (the document itself stays right-to-left through its own CSS).
  const root = document.documentElement, prevDir = root.dir, y = window.scrollY;
  const busy = $("#busy"); busy.classList.add("on");
  root.dir = "ltr"; document.body.dir = "ltr";
  try {
    return await html2pdf().set(opt).from(doc).outputPdf("blob");
  } finally {
    root.dir = prevDir; document.body.removeAttribute("dir"); busy.classList.remove("on");
    stage.innerHTML = "";
    window.scrollTo(0, y);
  }
}

/* ---------- Deliver the file ---------- */
function directDownload(blob, name) {
  const url = URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url; a.download = name;
  document.body.append(a); a.click(); a.remove();
  setTimeout(() => URL.revokeObjectURL(url), 60000);
}

const sendSteps = `<ol>
  <li>פותחים את וואטסאפ ונכנסים לקבוצה.</li>
  <li>לוחצים על סמל הצירוף (📎) ובוחרים <b>מסמך</b>.</li>
  <li>בוחרים את הקובץ מתיקיית ההורדות ושולחים.</li>
</ol>`;

let lastBlob = null, lastName = "";
function canShareFile(blob, name) {
  try {
    const f = new File([blob], name, { type: "application/pdf" });
    return !!(navigator.canShare && navigator.canShare({ files: [f] }));
  } catch (_) { return false; }
}
async function shareFile() {
  try {
    const f = new File([lastBlob], lastName, { type: "application/pdf" });
    await navigator.share({ files: [f], title: lastName });
  } catch (e) {
    if (e && e.name === "AbortError") return; // user closed the share sheet
    showNotice("bad", "השיתוף לא נפתח", `<p>אפשר לשלוח את הקובץ שהורד בדרך הרגילה:</p>${sendSteps}`);
  }
}

$("#pdfBtn").addEventListener("click", async () => {
  hideNotice();
  if (!validate()) return;
  const btn = $("#pdfBtn");
  btn.disabled = true; btn.textContent = "מכין את הקובץ…";
  try {
    lastBlob = await makePdfBlob();
    lastName = fileName();
    directDownload(lastBlob, lastName);
    const share = canShareFile(lastBlob, lastName);
    showNotice("ok", "הקובץ מוכן",
      "<p>קובץ ה-PDF נשמר בהורדות של המכשיר.</p>" +
      (share ? `<p>הכי מהיר: שליחה ישירה לקבוצה.</p><div class="actions"><button type="button" class="btn primary" id="shareBtn">שליחה בוואטסאפ</button></div>`
             : `<p>שליחה לקבוצת הוואטסאפ:</p>${sendSteps}`));
    const sb = $("#shareBtn");
    if (sb) sb.addEventListener("click", shareFile);
  } catch (e) {
    showNotice("bad", "לא הצלחנו ליצור את הקובץ",
      e && e.message === "lib"
        ? "<p>רכיב יצירת ה-PDF לא נטען. בדקו את החיבור לאינטרנט ורעננו את הדף. הנתונים שמילאתם יימחקו ברענון.</p>"
        : "<p>נסו שוב. אם זה חוזר, הסירו תמונות ונסו שוב.</p>");
  } finally {
    btn.disabled = false; btn.textContent = "הורדת PDF";
  }
});

/* ---------- Clear form ---------- */
const dlg = $("#clearDlg");
$("#clearBtn").addEventListener("click", () => { dlg.classList.add("on"); $("#clearNo").focus(); });
$("#clearNo").addEventListener("click", () => dlg.classList.remove("on"));
dlg.addEventListener("click", e => { if (e.target === dlg) dlg.classList.remove("on"); });
document.addEventListener("keydown", e => { if (e.key === "Escape") dlg.classList.remove("on"); });
$("#clearYes").addEventListener("click", () => {
  form.reset();
  photos = []; renderPhotos();
  clearSig();
  $$(".invalid").forEach(f => f.classList.remove("invalid"));
  newReportNo(); updateBranch(); hideNotice();
  dlg.classList.remove("on");
  window.scrollTo({ top: 0, behavior: "smooth" });
});
</script>
</body>
</html>

````
