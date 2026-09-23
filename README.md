# Operation Checkmate — מעבדת בדיקות חדירה וחקירת רשת
<img width="1913" height="502" alt="image" src="[https://github.com/user-attachments/assets/06ce3214-b813-4ec1-8589-d4b41995618f](https://github.com/user-attachments/assets/06ce3214-b813-4ec1-8589-d4b41995618f)" />

<img width="1906" height="545" alt="image" src="https://github.com/user-attachments/assets/bfcabbb9-45a7-4deb-860d-d6cf2cf0cd36" />


## סקירה כללית
מעבדה זו מדמה סביבה ארגונית מותקפת ומעריכה את שרשרת התקיפה (Unified Kill Chain). התהליך מתחיל בבדיקת אבטחה של ממשק ניהול חיצוני של חומת אש (`firewall.thm:5001`), התקדמות להשגת גישה ראשונית (Initial Foothold), ובהמשך תנועה רוחבית (Lateral Movement) והתחמקות ממנגנוני זיהוי.

---

## שלב 1: אבטחת גישה ובדיקת הזדהות (בתהליך עבודה)

### 1. מיפוי ראשוני וניתוח טופס ההתחברות (Reconnaissance)
* בוצע ניתוח של ממשק הניהול בדפדפן הרץ על פורט `5001`.
* נבדקו נתוני בקשת ה-HTTP מסוג `POST` בעזרת כלי הפיתוח (Developer Tools):
  * **נתיב (Endpoint):** `/login`
  * **מבנה הבקשה (Payload):** `username=admin&password=^PASS^`
  * **תנאי כשלון:** במקרה של סיסמה שגויה, השרת מחזיר הודעת שגיאה המכילה את המחרוזת `Invalid credentials.`.

### 2. מתקפת כוח גס ובדיקת מילונים (Hydra)
הופעלה בדיקת הזדהות אוטומטית באמצעות הכלי `hydra` מול טופס ההתחברות תוך שימוש במילון הבסיסי של המערכת:



#### תוצאות הבדיקה והמשך תהליך
* **ניסיון ראשון נכשל:** הסריקה הסתיימה ללא מציאת סיסמה תואמת (`0 valid password found`), מכיוון שהסיסמה שהוגדרה במערכת לא הייתה קיימת במילון הבסיסי `fasttrack.txt`.
* **הורדת חבילה ייעודית:** כדי לנסות סיסמאות ברירת מחדל ממוקדות של מוצרי רשת וחומות אש, הורדתי את החבילה `SecLists`:
  ```bash
  sudo apt update && sudo apt install -y seclists
hydra -l admin \
  -P /usr/share/wordlists/fasttrack.txt \
  -f -V -t4 \
  -s 5001 \
  firewall.thm http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials."

  <img width="1087" height="452" alt="image" src="https://github.com/user-attachments/assets/499d766b-3028-4d3c-b7c1-33a906456c5e" />

