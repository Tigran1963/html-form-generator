# Генератор форм из JSON схемы

## Описание
#### Форма поддерживает основные типы полей:
- текстовое поле (text, email, password)
- выпадающий список (select)
- чекбокс (checkbox)

## Запуск проекта
#### Установка
```sh
npm install
```
#### Запуск в режиме разработки
```sh
npm run dev
```
#### Билд
```sh
npm run build
```

## Пример JSON-схемы:
```json
{
  "fields": [
     { "type": "text", "label": "Имя", "model": "name", "required": true },
     { "type": "email", "label": "Email", "model": "email", "required": true },
     { "type": "password", "label": "Пароль", "model": "password", "required": true, "minLength": 6 },
     { "type": "select", "label": "Роль", "model": "role", "options": ["Админ", "Пользователь"], "required": true },
     { "type": "checkbox", "label": "Согласен с условиями", "model": "terms", "required": true }
  ]
}