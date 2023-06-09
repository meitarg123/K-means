
תקציר:

מימשנו את אלגוריתם k-means וdp-means ולאחר מכן ניתחנו את השינויים בתוצאות של האלגרויתמים על תמונה לפי שינוי ערכי k ו λ .

אלגוריתם k-means

סעיף 1: תיאור האלגוריתם ומימושו

תחילה, מימשנו את אלגוריתם k-means כך שאתחול k הנקודות (אשר יהוו בסיום ההרצה מרכזי האשכולות השונים) התבצע באופן רנדומלי, בתוך טווח הערכים התחום על ידי ערכי ה-x וה-y המינימליים והמקסימליים מבין הנקודות הנתונות ב- Data set (אותו יש לציין, ייצרנו באמצעות ספריית sklearn ומתודת make\_blobs).

לאחר אתחול k הנקודות (ייקראו מעתה "נקודות מרכזיות"), ביצענו עדכונים חוזרים ונשנים של נקודות אלו לפי האלגוריתם הבא:

1. חישוב המרחק האוקלידי (ייקרא מעתה "מרחק") של כל נקודה בדאטה מכל נקודה מרכזית.
2. הנקודות השונות מסווגות לקבוצות לפי הנקודה המרכזית הקרובה אליהן ביותר.
3. הנקודות המרכזיות מחושבות שוב לפי ממוצע הנקודות ששייכות לכל נקודה מרכזית.

סיום הלולאה מתרחש כשקבוצת הנקודות המרכזיות לא השתנתה בין שתי איטרציות של הלולאה.

על מנת להימנע מלולאה אין-סופית, הוספנו תנאי עצירה נוסף ללולאה המגביל את מספר האיטרציות.

המימוש המלא מצורף כקובץ פייתון נפרד.

סעיף 2: בחינת ביצועי המימוש

על אף הצלחה יחסית, שמנו לב שבכמות ניכרת של איטרציות נצפו ביצועים פחות טובים של האלגוריתם. מתוך הבנה שאתחול הנקודות המרכזיות מתבצע באקראי, הצלחנו לתחקר ולזהות 2 בעיות עיקריות שהביאו לפגיעה בביצועי המימוש:

בגרפים הבאים צבענו בצבע שונה את הנקודות שייצרנו באמצעות make\_blobs, וכל נקודה מיוצגת על ידי **צורה שונה** בהתאם לסיווג k-means.

1. בעיה ראשונה: אם נקודה מרכזית מאותחלת רחוק מהקבוצות שיוצרו על ידי make\_blobs, היא נוטה לא לשנות את מיקומה, ולהתקבע כטעות.
2. 
<img width="271" alt="Picture1" src="https://github.com/meitarg123/K-means/assets/100788290/91b6196e-c443-4b12-b6a6-0f698157500f">

סימן הפלוס הימני תחתון הינו נקודה מרכזית בסיום ההרצה, ערכה x=1.292, y=-1.725

מתחקור ההרצה ראינו כי ערכה באתחול היה כמעט זהה: x=1.289, y=-1,724

1. אם שתי נקודות מרכזיות מאותחלות קרוב מדי אחת לשנייה, כנראה שהן לא יתרחקו אחת מהשנייה.

![Picture2](https://github.com/meitarg123/K-means/assets/100788290/bee14e82-131d-4e62-8828-c0371b0538d7)

סימני ה+ האדומים זה הערכים ההתחלתיים של הנקודות המרכזיות, ואילו ה+ השחורים זה הערכים הסופיים של הנקודות המרכזיות, ואכן ניתן לראות שהנקודות בצד שמאל למטה של התמונה לא התרחקו, וקבוצה אחת (הירוקה) חולקה לשני clusters שונים.

מבדיקת השפעת ערכו של פרמטר k על ביצועי האלגוריתם, הבחנו כי הביצועים של k-means פחות טובים ככל שה-k גדל. זאת, להשערתנו, בשל סיכוי גבוה יותר לאתחול בעייתי של הנקודות המרכזיות, שיוביל לאחת משתי הבעיות שתוארו מעלה. בתמונה המובאת מטה ניתן לראות דוגמא להתקבצות של 3 נקודות מרכזיות שונות שאותחלו בערכים קרובים.

נדגיש - כי אלגוריתם k-means מתאים לבעיות סיווג בהם ידועה כמות הקבוצות השונות מראש, ועל כן - ביצירת הדאטה-סט באמצעות sklearn.datasets.make\_blobs ייצרנו כמות קבוצות בדאטה הזהות לפרמטר k. כנדרש במשימה - פיזרנו 1000 נקודות במרחב דו-מימדי.

![Picture3](https://github.com/meitarg123/K-means/assets/100788290/c0642350-6817-44ca-9e52-0b1fccb6e7c8)


על מנת להתמודד עם בעיות אלה, החלטנו לאתחל את k הנקודות בצורה אחרת:

1. נאתחל את ערכי הנקודה המרכזית הראשונה להיות זהים לאחת מהנקודות הקיימות ב- Data set, נקודה זו נבחרת באקראי.
2. כעת, נאתחל בלולאה את k-1 הנקודות המרכזיות האחרות:
  1. עבור כל נקודה ב Data set, נחשב את סכום המרחקים שלה לבין כל הנקודות המרכזיות שחושבו עד כה. בנוסף, ננרמל מרחק זה למול סכום כל המרחקים של כל הנקודות מכל המרכזים.
  2. כדי לאתחל את ערכי הנקודה המרכזית הבאה, נגריל נקודה אקראית בData set - כשההסתברות להגריל נקודה מסוימת שווה ערך לסכום המרחקים המנורמל שלה.

כעת, ניתן היה לראות שהביצועים טובים יותר. זאת, משום שערכי הנקודה המרכזית ההתחלתיים בהכרח שווים לנקודה כלשהי מה- Data set, ומשום שבהסתברות נמוכה יותר נאתחל שתי נקודות מרכזיות קרוב מדי זו לזו.

אלגוריתם dp-means

סעיף 1: תיאור האלגוריתם ומימושו:

בשונה מה-kmeans, באלגוריתם זה מספר הclusters להם מחולק הדאטה לא ידוע לנו מראש - ולכן הוא מתאים לסוג בעיות נוספות. האלגוריתם מוצא את מספר ה-clusters במהלך הריצה, תוך התחשבות בפרמטר λ, באופן הבא:

מאתחלים נקודה מרכזית ראשונה יחידה על ידי הממוצע של כל הנקודות בדאטה סט. ומתחילים להריץ בלולאה:

בכל איטרציה בלולאה, עוברים על כל הנקודות בדאטה-סט: עבור כל נקודה בדאטה-סט, מחשבים את מרחקה מכל נקודה מרכזית ומוצאים את המרחק המינימלי עבורה. כעת, נבדוק האם מרחק מינימלי זה גדול מ- λ. במידה שכן - ניצור נקודה מרכזית חדשה, שערכיה שווים לנקודה אותה בדקנו. נמשיך לעבור על שאר הנקודות כשבסיום איטרציה, בדומה לk-means, כל נקודה "משויכת" לנקודה מרכזית מסוימת.

בדומה לk-means, בכל איטרציה, לאחר מעבר על כל הנקודות בדאטה-סט באיטרציה, מתבצע מיטוב של הנקודות המרכזיות לפי ממוצע הנקודות ה"שייכות" אליהן בדאטה-סט.

אם לא היה שינוי בערך הנקודות המרכזיות או בכמותן בין שתי איטרציות, האלגוריתם יחזור לבצע איטרציה נוספת של הלולאה.

סעיף 2: בחינת ביצועי המימוש

הערך λ מייצג את המרחק האוקלידי המקסימלי של נקודה בדאטה-סט מהנקודה המרכזית הקרובה ביותר אליה. על כן, ערך λ קטן מדי (למשל, קטן ממחצית המרחק בין כל 2 נקודות) יביא לכך (במימוש שלנו, בהעדר הגבלה על כמות הנקודות המרכזיות) שכל נקודה תהפוך נקודה מרכזית.

בתמונה: ערך λ הוא 0.001


<img width="179" alt="Picture4" src="https://github.com/meitarg123/K-means/assets/100788290/2c7e8623-067e-4092-91a6-147bac68ed24">


מצד שני, ערך λ גדול מדי יביא לכך שלא יווצרו נקודות מרכזיות חדשות כלל, וכל הנקודות יסווגו לנקודה מרכזית אחת.

בתמונה: ערך λ הוא 2.4


<img width="187" alt="Picture5" src="https://github.com/meitarg123/K-means/assets/100788290/dbca836d-3803-45aa-83a2-36bda50812ba">


על כן, ישנו אופטימום כלשהו בין 2 ערכי קיצון אלה. מבחינת ערכי λ הבחנו כי הערך האופטימלי של λ תלוי בכמה פרמטרים:

1. מספר הקבוצות בדאטה-סט: הערך של λ משפיע על כמות הנקודות המרכזיות שנוצרות, ערך λ גדול יביא ליצירת פחות נקודות מרכזיות, כשערך λ קטן יביא לתוצאה הפוכה. על כן, בהינתן מספר קטן של קבוצות בדאטה-סט - ערכי λ גדולים הביאו תוצאות טובות יותר.

בתמונה המצורפת ערכו של λ הוא 1, ו- 1000 הנקודות מפוזרות כ-2 קבוצות. ה "+" מייצגים את הנקודות המרכזיות בסיום ריצת האלגוריתם.

<img width="195" alt="Picture6" src="https://github.com/meitarg123/K-means/assets/100788290/c382d8d3-1b78-4431-88c9-be5ca4c1852a">


ובתמונה זו ערכו של λ הוא 2: כאמור, ניתן לראות סיווג נכון יותר של הדאטה-סט clusters

<img width="198" alt="Picture7" src="https://github.com/meitarg123/K-means/assets/100788290/6362fba9-c0af-4e48-90de-427120c150da">

1. פיזור הנקודות במרחב: בקלאסטרים "גדולים" (בעלי רדיוס גדול) הגדלת ערך λ הביאה למיטוב נקודה מרכזית למרכז הקלאסטר, בעוד שהקטנת ערך λ הביאה ליצירת נקודות מרכזיות נוספות לאותו קלאסטר.

בתמונה המובאת מטה ערכו של λ הינו 1.2, כשפיזרנו 4 קבוצות של נקודות במרחב (לפי צבעים), האלגוריתם טעה בכך שסיווג חלק מהקבוצות ל-2 קלאסטרים שונים.

<img width="194" alt="Picture8" src="https://github.com/meitarg123/K-means/assets/100788290/a4afa174-d141-441d-b569-286994ae8eb1">

זאת, לעומת ערך גדול יותר של λ (1.7), שהביאה לביצועים טובים יותר:

<img width="219" alt="Picture9" src="https://github.com/meitarg123/K-means/assets/100788290/df3c0b60-2676-46a0-8e62-95873e56a4d1">


באופן כללי , הקטנת **** λ **** גורמת ל  העדפת  יצירת נקודות מרכזיות חדשות ,  והגדלתו להתאמת הנקודות המרכזיות הקיימות.
סעיף 3:

Kmeans: מבחינת אלגוריתם Kmeans על תמונת קוף מדריל (512\*512), ניתן לראות (בתמונה המובאת מטה) שהגדלת ערכו של K הביאה לביצועים טובים יותר. 

<img width="452" alt="Picture10" src="https://github.com/meitarg123/K-means/assets/100788290/7214f5d5-358d-4f3e-8efd-f83f34aea3c1">

עבור ערכי k גבוהים (30 ואילך), ראינו שאין שינוי משמעותי בביצועי האלגוריתם.
<img width="452" alt="Picture11" src="https://github.com/meitarg123/K-means/assets/100788290/409712da-8f4a-4877-89e5-7debdee24a95">

נציין כי על אף שבסעיף 1 טענו שהגדלת ערכי k מביאים לירידה בביצועי האלגוריתם, שתי הטעויות המרכזיות שאפיינו לאלגוריתם בסעיף 1:

- התקבצות נקודות מרכזיות באותה קבוצת נקודות קרובות בדאטה-סט וחלוקה (מיותרת) שלהן לקלאסטרים שונים.
- התמקמות נקודה מרכזית רחוק מכל נקודה בדאטה-סט.

לא משפיעות על ביצועי האלגוריתם במשימת צביעת קוף המנדריל. זאת, משום שקרבה בין שתי נקודות מרכזיות תביא לצביעה זהה (או דומה מספיק) של תמונת הפלט. כמו כן, נקודה מרכזית שמוקמה רחוק מכל נקודה בדאטה-סט, לא תביא לצביעה שגויה משמעותית (אם בכלל) של פיקסלים בתמונת הפלט.

זמן הריצה: ערכי K גבוהים הביאו כצפוי לזמני ריצה ארוכים יותר. עבור 4 התמונות הראשונות זמן הריצה היה 9.4 שניות. בעבור 4 התמונות האחרונות זמן הריצה היה 27.35 שניות.

DPMeans:מבחינת אלגוריתם DPMeans ראינו כי הורדת ערכה של λ הביאה לביצועים טובים יותר של האלגוריתם.

זאת, משום שהורדת ערכה של λ, מאפשר לאלגוריתם ליצור יותר נקודות מרכזיות ובכך להצליח לסווג יותר נקודות בדאטה-סט ליותר קלאסטרים בהתאם לצורך.

ערך λ גבוה מדי יביא ליצירת מעט נקודות מרכזיות, ועל כן לסיווג הנקודות בדאטה-סט למספר מועט של clusters - מה שיביא לתמונת פלט פחות מגוונת (כפי שניתן לראות בתמונה המובאת מטה)

<img width="452" alt="Picture12" src="https://github.com/meitarg123/K-means/assets/100788290/2c68e525-f64e-43a5-a295-cd5e6f56c934">

הורדת ערכו של λ מביאה ליותר איטרציות (יצירה של יותר נקודות מרכזיות, ומיטובן) ועל כן כצפוי הורדת ערכו של λ הביאה לעלייה בזמן הריצה. אולם - זמן הריצה לא עלה באופן משמעותי.

ערך λ נמוך מספיק מביא ליצירת נקודה מרכזית עבור כל נקודה בדאטה-סט (כפי שתיארנו בסעיפים 1-2), כך שאין שוני מהתמונה המקורית).

<img width="452" alt="Picture13" src="https://github.com/meitarg123/K-means/assets/100788290/551302ae-1d40-4d61-a38b-cfd9f5202897">

