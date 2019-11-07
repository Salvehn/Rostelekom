# Сценарии использования

##Отправка вложений в канал или чат (фото/видео, фото/видео с камеры, файл, геометку). Предпросмотр вложений. Выгрузка вложений

**Акторы:** пользователь.

**Цель:** пользователь отправляет вложения в канал или чат (фото/видео, фото/видео с камеры, файл, геометку), с возможностью предпросмотра и выгрузки вложений

**Предусловия:** пользователь зашёл в приолжение и авторизовался, у него открыт чат или канал.

**Сценарий:**

1. Пользователь наводит курсор на иконку "скрепка" рядом со строкой ввода текста сообщения.

2. Приложение показывает меню выбора возможных вложений (фото/видео, фото/видео с камеры, файл, геометку) для прикпрепления к сообщению.

3. Пользователь нажимает на кнопку в появившемся меню, которая соотвествует желаемому пользователем типу прикрепляемого вложения.   

4. Приложение открывает диалоговое окно для выбора вложений на машине пользователя.

5. Пользователь выбирает прикрепление медиа файла

   Если пользователь выбрал геометку, начинается сценарий [Отправка геометки].

6. После выбора вложений пользователем, приложение запрашивает выбранные относительные пути к файлам на машине пользователя, записывая их в массив `FileList[]` в скрытом виде, подменяя их на `C:\fakepath\`.

7. Приложение создает url адрес на эти файлы методом `readAsDatasURL()`.

8. Приложение показывает список с предпросмотром загруженных файлов по этим url адресам.

9. Пользователь отправляет сообщение с прикрепленными вложениями, нажав на кнопку "отправить".

10. Приложение загружает прикрепленные вложения на бекэнд и записывает информацию о вложениях в базу `(id, тип файла, имя файла, путь до файла, публичная ссылка)`

{Успешная загрузка вложения}

1. Бекэнд присылает ответ об успешной загрузке вложений в структуре `response`:

   ```json
   {response:{statusCode:1,msg:"upload successfull"}}
   ```

2. Приложение отправляет в бекенд socket/http запрос, после которого бекенд с помощью ответа на socket запрос / технологии http2 server push обновляет список сообщений у всех пользователей в чате / канале.

**Результат:**

- В списке сообщений чата / канала появляется новое сообщение.
- Вложения из нового сообщения видны всем участникам чата / канала.
- В базу данных добавлены записи о вложениях.

**Альтернативные или исключения:**

2а. [Отправка геометки], если в меню выбора вложений была выбрана геометка. 2a1. Приложение запрашивает у пользователя его геопозицию и отправляет сообщение с вложением с json файлом, содержащим координаты. 2а2. После отправки сообщения приложение рендерит embed вставку с картой по заданным данным геолокации из json файла вложений сообщения. 2а3 Возврат на шаг {Успешная загрузка вложения}.  3а. Ошибка загрузки вложения, если у произошла потеря соединения.

```json
{response:{statusCode:0,msg:"upload failed"}}
```

 8а. Бекенд возвращает 500. 8б. Превышен лимит отправляемых вложений.
