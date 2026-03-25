# obscure
*Оригінал: Jason Yung — [http://callmejay.com](http://callmejay.com)*  
*Форк і оновлення: Roman Skrylnyk — [github.com/r-skrylnyk](https://github.com/r-skrylnyk/)*

No nonsense obfuscation for HTML/CSS based frontend applications.  
Перейменовує всі CSS-класи та ID у нечитабельні хеш-назви і застосовує зміни до HTML/PHP файлів.

---

## Встановлення

```bash
npm install -g @etozheroma/obscure
```

---

## Використання

### Базовий приклад

```bash
obscure style.css --apply index.html
```

Бере всі **id та класи** з `style.css`, обфускує їх і застосовує зміни до `style.css` та `index.html`.

---

### Директорія виводу (`--output`)

```bash
obscure style.css --apply index.html --output ./dist
```

Обфусковані файли записуються у `./dist/`.  
Також генерується `./dist/obscure.map` — JSON з таблицею відповідностей оригінал → обфускована назва.

---

### Виключення (`--exclude`)

```bash
obscure style.css --exclude bootstrap.css --apply index.html
```

Класи та ID з `bootstrap.css` не будуть перейменовані.

---

### Фіксований сід (`--seed`)

```bash
obscure style.css --apply index.html --output ./dist --seed 42
```

Без `--seed` результат буде **різним при кожному запуску** (використовується `Date.now()`).  
З `--seed` обфускація **відтворювана** — однаковий вхід завжди дає однаковий вихід. Рекомендовано для Docker CI/CD збірок.

---

### Батч-режим

**Список через кому:**
```bash
obscure style.css,other.css --apply index.html,other.html
```

**Glob-шаблони:**
```bash
obscure *.css --apply *.html
```

**Змішано:**
```bash
obscure *.css,app/style.css --apply *.html,app/index.html
```

---

## Використання у Docker

```dockerfile
FROM node:18-bullseye-slim AS builder

RUN npm install -g @etozheroma/obscure@1.1.0

WORKDIR /app
COPY . .

RUN obscure style.css --apply index.html --output /output/dist --seed 42

FROM alpine:3.18
COPY --from=builder /output/dist /dist
```

> У версії 1.1.0 більше не потрібно робити `COPY` кастомного `index.js` поверх npm-пакету — всі зміни вже включені у пакет.

---

## Зміни у версії 1.1.0 (форк)

| Зміна | Деталі |
|-------|--------|
| `glob` v7 → v10 | `glob.sync()` → `globSync()` |
| `hashids` v1 → v2 | Імпорт через `hashids/cjs` |
| `cheerio` 0.20 → v1 | Актуальний API |
| `commander` v2 → v12 | `new Command()`, правильна передача опцій в action |
| `mkdirp` видалено | Замінено на вбудований `fs.mkdirSync({recursive: true})` |
| Баг з `lastIndex` | `DEFINITIONS_CAP.lastIndex` скидається між файлами |
| Баг `safeWrite` | Усунуто гонкову умову async/sync |
| `bin` виправлено | Поле вказує безпосередньо на `index.js` |
| Node.js | Мінімальна версія `>=16` |

---

## Ліцензія

MIT — оригінал © 2016 Jason Yung (bitstrider), зміни © 2026 Roman Skrylnyk (etozheroma).  
Дивіться [LICENSE](./LICENSE).
