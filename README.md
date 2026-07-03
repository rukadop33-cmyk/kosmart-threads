# Сборка поста для Threads — деплой на Firebase

Это готовый многопользовательский инструмент: Firestore (`onSnapshot`, без опроса) +
Firebase Storage для фото. Комнаты через `?room=код`.

## Что нужно перед стартом
- Google-аккаунт
- Node.js установлен на компьютере (для Firebase CLI)
- 10–15 минут

## Шаг 1. Создать проект Firebase
1. Открой https://console.firebase.google.com
2. «Add project» → введи название (например `threads-post-builder`) → создать
   (план Spark / бесплатный — этого достаточно)

## Шаг 2. Включить Firestore
1. В левом меню → **Build → Firestore Database**
2. «Create database» → режим **Production** → выбери регион (ближайший к вам)

## Шаг 3. Включить Storage
1. В левом меню → **Build → Storage**
2. «Get started» → Production mode → тот же регион

## Шаг 4. Получить конфиг веб-приложения
1. Значок шестерёнки рядом с «Project Overview» → **Project settings**
2. Внизу «Your apps» → иконка `</>` (Web) → название любое → «Register app»
3. Скопируется объект `firebaseConfig` вида:
```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "threads-post-builder.firebaseapp.com",
  projectId: "threads-post-builder",
  storageBucket: "threads-post-builder.appspot.com",
  messagingSenderId: "...",
  appId: "..."
};
```
4. Открой файл **`index.html`** из этого набора, найди в нём `firebaseConfig` (в самом
   начале `<script type="module">`) и замени значения `"REPLACE_ME"` на свои.

## Шаг 5. Установить Firebase CLI
```bash
npm install -g firebase-tools
firebase login
```
(откроется браузер — войди тем же Google-аккаунтом)

## Шаг 6. Подготовить папку проекта
Сложи в одну папку файлы из этого набора:
```
project/
  index.html
  firebase.json
  firestore.rules
  storage.rules
```
В этой папке создай файл `.firebaserc`:
```json
{
  "projects": {
    "default": "ВАШ_PROJECT_ID"
  }
}
```
(`ВАШ_PROJECT_ID` — это `projectId` из firebaseConfig, например `threads-post-builder`)

## Шаг 7. Задеплоить
Из папки проекта:
```bash
firebase deploy
```
В конце появится строка вида:
```
Hosting URL: https://threads-post-builder.web.app
```
Это и есть постоянная ссылка на приложение.

## Как пользоваться комнатами
- Открой `https://ваш-проект.web.app/?room=call1` — это отдельная комната
- Все, кто откроет **ту же ссылку с тем же `room=`**, окажутся в одной комнате
  и увидят одни и те же посты/голоса/фото
- Другой созвон — просто смени код: `?room=call2`
- Если открыть без `?room=`, попадёте в комнату `default`
- Кнопка «скопировать ссылку» в приложении копирует текущий адрес с нужным `room=`

## Про приватность личных фото
Правила Firestore/Storage в этом наборе открыты (без Firebase Auth — по условиям
задачи вход должен быть без логина). Личные фото участника технически лежат по
адресу, который знает только его браузер, и ни один экран приложения не запрашивает
чужие личные фото — поэтому на практике они не попадают к другим людям. Но это
изоляция на уровне логики приложения, а не криптографическая гарантия на уровне
базы данных. Если понадобится более строгая приватность — следующий шаг: добавить
Firebase Anonymous Auth и переписать правила на `request.auth.uid == participantId`.

## Обновление после правок
Если код `index.html` меняется — просто снова:
```bash
firebase deploy
```
Ссылка (`.web.app`) остаётся той же, обновлять её не нужно.

## Стоимость
Бесплатный тариф Firebase (Spark) даёт:
- 50 000 чтений / 20 000 записей Firestore в день
- 5 ГБ хранилища Storage, 1 ГБ трафика в день

Для одного созвона на 5–15 человек этого более чем достаточно, платить не придётся.

## Кнопка «Начать всё заново»
Удаляет посты, оценки и участников этой комнаты (Firestore `deleteDoc`). Фото в
общем банке и личные фото участников не трогает — они остаются на будущее.
