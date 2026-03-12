# Настройка сбора контактов в Google Таблицу

## Шаг 1: Создание Google Таблицы

1. Откройте [Google Sheets](https://sheets.google.com)
2. Создайте новую таблицу: **"МалышШеринг - Заявки"**
3. В первой строке создайте заголовки:
   - A1: `Дата`
   - B1: `Имя`
   - C1: `Контакт`
   - D1: `Категория`
   - E1: `Тип`

## Шаг 2: Создание Google Apps Script

1. В таблице откройте: **Расширения → Apps Script**
2. Удалите весь код в редакторе
3. Вставьте этот код:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);

    // Добавляем новую строку с данными
    sheet.appendRow([
      new Date(),
      data.name,
      data.contact,
      data.category,
      data.type
    ]);

    return ContentService.createTextOutput(JSON.stringify({
      status: 'success'
    })).setMimeType(ContentService.MimeType.JSON);

  } catch(error) {
    return ContentService.createTextOutput(JSON.stringify({
      status: 'error',
      message: error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. Нажмите **Сохранить** (иконка дискеты)
5. Дайте проекту имя: **"МалышШеринг API"**

## Шаг 3: Публикация Web App

1. Нажмите **Развернуть → Новое развертывание**
2. Нажмите на иконку шестеренки рядом с "Выберите тип"
3. Выберите **Веб-приложение**
4. Настройте параметры:
   - **Описание**: "API для сбора заявок"
   - **Выполнять как**: **Я** (ваш email)
   - **У кого есть доступ**: **Все**
5. Нажмите **Развернуть**
6. **Скопируйте URL веб-приложения** (он выглядит как `https://script.google.com/macros/s/...`)

## Шаг 4: Обновление лендинга

1. Откройте файл `index.html`
2. Найдите строку (около 817):
   ```javascript
   const GOOGLE_SCRIPT_URL = 'ВСТАВЬТЕ_СЮДА_URL_ИЗ_GOOGLE_APPS_SCRIPT';
   ```
3. Замените `'ВСТАВЬТЕ_СЮДА_URL_ИЗ_GOOGLE_APPS_SCRIPT'` на скопированный URL в кавычках
4. Сохраните файл

## Шаг 5: Тестирование

1. Откройте лендинг в браузере
2. Заполните форму и отправьте
3. Проверьте, что данные появились в Google Таблице

## Готово!

Теперь все заявки будут автоматически попадать в вашу Google Таблицу с датой и временем отправки.

### Дополнительные возможности

**Автоматические уведомления на email:**
Добавьте в конец функции `doPost`:

```javascript
MailApp.sendEmail({
  to: "ваш-email@gmail.com",
  subject: "Новая заявка на МалышШеринг",
  body: `Новая заявка от ${data.name}\nКонтакт: ${data.contact}\nКатегория: ${data.category}\nТип: ${data.type}`
});
```

**Форматирование таблицы:**
- Первую строку сделайте жирной
- Включите фильтры: Данные → Создать фильтр
- Настройте условное форматирование для типов пользователей
