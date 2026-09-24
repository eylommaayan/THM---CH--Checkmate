# Operation Checkmate — מעבדת בדיקות חדירה וחקירת רשת
<img width="1913" height="502" alt="image" src="[https://github.com/user-attachments/assets/06ce3214-b813-4ec1-8589-d4b41995618f](https://github.com/user-attachments/assets/06ce3214-b813-4ec1-8589-d4b41995618f)" />

<img width="1906" height="545" alt="image" src="https://github.com/user-attachments/assets/bfcabbb9-45a7-4deb-860d-d6cf2cf0cd36" />

סקירת האתגר (Challenge Overview)
חדר ה-Checkmate מתמקד בהערכת נהלי סיסמאות חלשות בתוך סביבה ארגונית מדומה. מרקו ביאנקי (Marco Bianchi), מנהל מערכות, פרס לאחרונה מספר שירותים פנימיים, כולל ממשק ניהול חומת אש, פורטל עובדים, פלטפורמה חברתית וגישת SSH לתשתיות קריטיות. עקב הרגלי אבטחת סיסמאות לקויים ולחץ תפעולי, מרקו השתמש מחדש בסיסמאות חלשות, צפויות ומבוססות תבנית על פני מערכות שונות.

האתגר מדגים כיצד תוקפים יכולים להשתלט בהדרגה על שירותים באמצעות ניצול פרטי ברירת מחדל, מודיעין ממקורות גלויים (OSINT), מילונים מותאמים אישית (Custom Wordlists), פרופילינג של סיסמאות, פיצוח גיבובים (Hash Cracking) ותבניות יצירת סיסמאות צפויות. במקום להסתמך על פגיעויות תוכנה או אקספלויטים מורכבים, החדר מדגיש מתקפות סיסמה מציאותיות הנפוצות במהלך מבדקי חדירה פנימיים ומבצעי Red-Team.

לאורך המבדק, אנו משתמשים במספר כלי התקפה כגון Hydra, CeWL, CUPP, Crunch ו-Hashcat כדי לאתר חולשות במדיניות ההזדהות של מרקו ולהשיג גישה לשירותים רגישים יותר ויותר.

מטרות (Objectives)
ניצול פרטי הזדהות חלשים וברירת מחדל במספר שירותים.

יצירת מילוני סיסמאות מותאמים אישית בעזרת OSINT ומילות מפתח של החברה.

ביצוע מתקפות כוח גס (Brute-force) מול פורטלי התחברות בווב ושירותי SSH.

ניתוח תבניות צפויות ליצירת סיסמאות.

מהלך העבודה (Walkthrough)
כדי להתחיל באתגר, הפעלתי תחילה את מופע מכונת היעד מלוח הבקרה של חדר ה-TryHackMe. לאחר מספר רגעים, הפלטפורמה הקצתה כתובת IP ייעודית שמארחת את כל השירותים הפגיעים בחדר.



שלב 1: הבנת המטרה וזיהוי השירות
היעד: ממשק ניהול של חומת אש (Firewall Management Console).

כתובת ופורט: [http://firewall.thm:5001](http://firewall.thm:5001).

הרמז מהתרחיש: מרקו (מנהל המערכת) התקין את חומת האש אך שכח לשנות את פרטי ברירת המחדל של המערכת (Default Credentials).

שם המשתמש המשוער: admin (ברירת מחדל סטנדרטית למערכות כאלה).

<img width="943" height="835" alt="image" src="https://github.com/user-attachments/assets/73452736-12f7-49fe-9321-2df2cdf559ad" />


<img width="927" height="128" alt="image" src="https://github.com/user-attachments/assets/a37e03ee-45c5-4485-89f3-02072f083674" />
מידע זה קריטי מכיוון שכלי ה-Hydra דורש את נתיב ההתחברות, פרמטרי ה-POST ומחרוזת תנאי הכישלון.
<img width="932" height="812" alt="image" src="https://github.com/user-attachments/assets/9cc27764-f859-4112-9c2d-94a5a72c316a" />




---

שלב 1: הבנת המטרה וזיהוי השירות
היעד: ממשק ניהול של חומת אש (Firewall Management Console).

כתובת ופורט: [http://firewall.thm:5001](http://firewall.thm:5001).

הרמז מהתרחיש: מרקו (מנהל המערכת) התקין את חומת האש אך שכח לשנות את פרטי ברירת המחדל של המערכת (Default Credentials).

שם המשתמש המשוער: admin (ברירת מחדל סטנדרטית למערכות כאלה).

<img width="1052" height="772" alt="image" src="https://github.com/user-attachments/assets/3775eac5-379a-4f62-b6ee-ad739211389e" />
להוריד ישירות את הקובץ הספציפי (הכי מהיר ובטוח)
במקום להתקין את כל מאגר SecLists (ששוקל מאות מגה-בייטים), אפשר להוריד ישירות את הקובץ הספציפי שבו השתמש המדריך מתוך ה-GitHub הרשמי של SecLists.

לחץ Ctrl + C כדי לעצור את ה-find, והרץ את הפקודות הבאות:

Bash
mkdir -p seclists/Passwords/Default-Credentials/
wget -O seclists/Passwords/Default-Credentials/default-passwords.txt https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/Default-Credentials/default-passwords.txt
הפקודה תחזיר לך שורה (נתיב). ב-Kali / AttackBox זה לרוב יהיה:
/usr/share/wordlists/seclists/Passwords/Default-Credentials/default-passwords.txt



מאחר שהאתגר רמז על שימוש בפרטי ברירת מחדל, הרצנו מתקפת Brute-Force מול ממשק חומת האש באמצעות מילון ייעודי מתוך **SecLists**:

```bash
hydra -l admin \
  -P /usr/share/seclists/Passwords/Default-Credentials/default-passwords.txt \
  -f -V -t4 -s 5001 firewall.thm http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials."

```

#### פירוט הדגלים והפרמטרים:

* **`-l admin`**: שם המשתמש לבדיקה.
* **`-P <path>`**: נתיב למילון סיסמאות ברירת המחדל.
* **`-s 5001`**: פורט היעד.
* **`-f`**: עצירה מידית בעת מציאת הסיסמה הנכונה.
* **`-t4`**: הרצה ב-4 תהליכים מקבילים.
* **`-V`**: הצגת פלט מפורט בזמן אמת.
* **`http-post-form`**: מודול להזדהות בטופסי POST.
* **מבנה הבקשה (`"/login:..."`)**:
* `/login` – נתיב טופס ההתחברות.
* `username=^USER^&password=^PASS^` – שדות הטופס שבהם מוזרקים הערכים.
* `Invalid credentials.` – הודעת השגיאה; העדרה מעיד על הצלחת ההתחברות.



**תוצאה:** Hydra פיצח את הסיסמה והושגה גישה לממשק הניהול של חומת האש.


<img width="1906" height="820" alt="image" src="https://github.com/user-attachments/assets/7315da29-e027-44ba-b52e-7b67c52798db" />

<img width="1907" height="817" alt="image" src="https://github.com/user-attachments/assets/c04e75fb-33f9-4a9a-9f16-2228f5104ba0" />


