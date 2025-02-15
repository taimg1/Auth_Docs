**Google OAuth2** — це один із сервісів, що реалізує протокол **OAuth2**, надаючи можливість авторизації та реєстрації користувачів через Google.

---
## **Початок роботи**

Щоб розпочати працювати з **Google OAuth2**, необхідно виконати такі кроки:

1. **Перейти до Google Cloud Console** ([Google Cloud Console](https://console.developers.google.com/)).
    
2. **Створити новий проект** (або вибрати існуючий).
    
3. **Увімкнути API Google Identity Platform**.
    
4. **Налаштувати облікові дані (Credentials) для OAuth2**:
    
    - Перейти до розділу "Credentials".
        
    - Натиснути "Create Credentials" та вибрати "OAuth 2.0 Client ID".
        
    - Вказати базову інформацію, таку як назва програми, домени переадресації та дозволені URI.
        
    - Отримати **Client ID** та **Client Secret**.
        

## **Робота з Google OAuth2 на клієнті та сервері**

### **1. Клієнтська частина (Frontend)**

На фронтенді можна використовувати бібліотеки для аутентифікації через Google, наприклад:

- **Google Identity Services** (`@react-oauth/google` для React)
    
- **OAuth.js** або **Google Sign-In API**
    

#### **Приклад інтеграції Google Sign-In у React:**

```
import { GoogleOAuthProvider, GoogleLogin } from '@react-oauth/google';

const clientId = "YOUR_CLIENT_ID";

const App = () => {
  return (
    <GoogleOAuthProvider clientId={clientId}>
      <GoogleLogin
        onSuccess={(response) => console.log("Success:", response)}
        onError={() => console.log("Login Failed")}
      />
    </GoogleOAuthProvider>
  );
};

export default App;
```

### **2. Серверна частина (Backend)**

На бекенді потрібно перевіряти токени, отримані від клієнта, щоб підтвердити автентичність користувача.

#### **Приклад перевірки токена у Node.js (Express) з використанням** `**google-auth-library**`**:**

```
const { OAuth2Client } = require('google-auth-library');
const client = new OAuth2Client("YOUR_CLIENT_ID");

async function verifyToken(token) {
  const ticket = await client.verifyIdToken({
    idToken: token,
    audience: "YOUR_CLIENT_ID",
  });
  const payload = ticket.getPayload();
  return payload;
}
```

## **Обробка користувачів у базі даних**

При реєстрації через Google потрібно визначити, чи користувач вже є в системі:

- Якщо email вже використовується для стандартного входу — заблокувати OAuth2 реєстрацію.
    
- Якщо користувач входить через Google — створити запис у БД або оновити існуючий.
    

Приклад збереження користувача в базі через Sequelize:

```
const user = await User.findOrCreate({
  where: { email: payload.email },
  defaults: { name: payload.name, googleId: payload.sub }
});
```

## **Додаткові Налаштування**

- **Refresh Token:** Для довготривалої аутентифікації використовуйте refresh-токен.
    
- **Scopes:** Додаткові дозволи (наприклад, доступ до Google Drive, Gmail).
    
- **Security:** Використовуйте HTTPS та перевіряйте токени на сервері.
    

---