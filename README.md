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

ראשית, ניגשתי לאפליקציה הראשית בכתובת: http://<TARGET_IP>:5000/


<img width="943" height="835" alt="image" src="https://github.com/user-attachments/assets/73452736-12f7-49fe-9321-2df2cdf559ad" />

לאחר מכן הגדרתי מיפוי שמות סטטי (Host Mapping) כדי לאפשר גישה למארחים הווירטואליים של היעד, ערכתי את הקובץ המקומי /etc/hosts והוספתי את הרשומה הבאה:
<TARGET_IP> firewall.thm jobs.thm social.thm



השתמשתי בפקודה הבאה לעריכת קובץ ה-hosts:

Bash
echo "10.48.129.160 firewall.thm jobs.thm social.thm" | sudo tee -a /etc/hosts
מטרה: לאפשר לדפדפן ולכלי שורת הפקודה לתקשר עם אפליקציות ייעודיות המאוחסנות על אותו שרת באמצעות ניתוב מארחים וירטואליים (Virtual Host / VHost).

על ידי הגדרת פענוח שמות מקומי:

firewall.thm מפנה לאפליקציית חומת האש.

jobs.thm מפנה לפורטל העובדים.

social.thm מפנה לפלטפורמה החברתית.
<img width="927" height="128" alt="image" src="https://github.com/user-attachments/assets/a37e03ee-45c5-4485-89f3-02072f083674" />

שלב 1 (LEVEL 1)
מרקו פרס חומת אש בכתובת firewall.thm:5001 אך השאיר את פרטי ברירת המחדל.

האתגר הראשון כולל ממשק ניהול חומת אש הרץ על firewall.thm:5001. תיאור החדר רומז שמרקו פרס את חומת האש אך שכח לשנות את פרטי ברירת המחדל.

לאחר פתיחת דף ההתחברות בדפדפן, בדקתי את בקשת ההזדהות באמצעות כלי הפיתוח (Developer Tools). בלשונית ה-Network, ביצעתי ניסיון התחברות שגוי וניתחתי את הבקשה והתגובה.

מתוך הבקשה שנלכדה, זיהיתי:

שיטת הבקשה (Request Method): POST

נתיב ההתחברות (Login Endpoint): /login

פרמטרים (Parameters): username, password

הודעת שגיאה (Failure Message): Invalid credentials.

מידע זה קריטי מכיוון שכלי ה-Hydra דורש את נתיב ההתחברות, פרמטרי ה-POST ומחרוזת תנאי הכישלון.
<img width="932" height="812" alt="image" src="https://github.com/user-attachments/assets/9cc27764-f859-4112-9c2d-94a5a72c316a" />

מכיוון שהאתגר מציין במפורש פרטי ברירת מחדל, השתמשתי במילון סיסמאות ברירת מחדל מתוך SecLists.

פקודה:

Bash
hydra -l admin \
  -P seclists/Passwords/Default-Credentials/default-passwords.txt \
  -f -V -t4 \
  -s 5001 \
  firewall.thm http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials."
-l admin: שם המשתמש לבדיקה.

-P: נתיב למילון הסיסמאות.

-f: עצירה מיד עם מציאת פרטי התחברות תקפים.

-V: פלט מפורט (Verbose).

-t4: שימוש ב-4 תהליכים מקבילים.

-s 5001: פורט היעד.

http-post-form: מודול Hydra המיועד להזדהות בטפסי POST.

ארגומנטים עבור מודול HTTP-POST-Form ב-Hydra:
"/login:username=^USER^&password=^PASS^:Invalid credentials."

נתיב ההתחברות (/login): הדף האחראי על ביצוע ההזדהות.

מבנה הבקשה (username=^USER^&password=^PASS^): Hydra מחליף דינמית את ^USER^ ואת ^PASS^ בערכים מתוך המילון במהלך המתקפה.

תנאי כשלון (Invalid credentials.): Hydra בודק כל תגובה לקיומה של מחרוזת זו. אם המחרוזת נעלמת, Hydra מזהה שההתחברות הצליחה.

לאחר מספר ניסיונות, Hydra זיהה בהצלחה את סיסמת הניהול התקפה של ממשק חומת האש. באמצעות פרטי הזדהות אלו, השגתי גישה לאפליקציית חומת האש הפנימית.
<img width="1906" height="820" alt="image" src="https://github.com/user-attachments/assets/7315da29-e027-44ba-b52e-7b67c52798db" />

מה הוא עושה בשלבים הבאים (צעדי ההמשך של החדר)
אימות שלב 1 במערכת הראשית: העתקת הסיסמה שנמצאה (ברירת המחדל של חומת האש) והדבקתה בתיבת ה-Level 1 בממשק הראשי (http://<TARGET_IP>:5000/) לצורך מעבר ל-Level 2.

איסוף מודיעין (Reconnaissance & OSINT): כניסה לשירותים הבאים שהוגדרו בקובץ ה-hosts (פורטל העובדים ב-jobs.thm והרשת החברתית ב-social.thm) לצורך איסוף פרטים אישיים על מרקו (תאריכי לידה, שמות, תחביבים ומילות מפתח של החברה).

בניית מילון מותאם אישית (Targeted Wordlist): הרצת כלים כמו CeWL (חילוץ מילים מאתרי היעד) או CUPP (יצירת פרופיל סיסמאות מבוסס פרטים אישיים) כדי ליצור רשימת סיסמאות ייעודית למרקו.

פריצה לפורטלים הפנימיים (Web Brute-Force): הרצת מתקפת מילון ממוקדת על טופסי ההתחברות של jobs.thm ו-social.thm בעזרת המילון המותאם שנבנה.

פיצוח גיבובים (Hash Cracking): חילוץ קובצי גיבוב של סיסמאות (Hashes) מתוך המערכות שנפרצו, ושימוש ב-Hashcat או ב-John the Ripper לפיצוחן.

זיהוי תבניות וחדירה ל-SSH (Pattern Analysis & Initial Foothold): ניתוח האופן שבו מרקו מרכיב את הסיסמאות שלו (למשל: שימוש בשם, שנה ותו מיוחד), יצירת מילון מתאים בעזרת Crunch, וביצוע מתקפת Brute-Force מול שירות ה-SSH לקבלת גישת טרמינל מלאה לשרת.
  <img width="1087" height="452" alt="image" src="https://github.com/user-attachments/assets/499d766b-3028-4d3c-b7c1-33a906456c5e" />

