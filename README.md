# Zero-OOM-Streaming-CSV-Uploader-for-React-Next.js
Zero-OOM streaming CSV uploader for React &amp; Next.js to bypass Vercel/Lambda limits

# Zero-OOM Streaming CSV Uploader for React & Next.js

> High-performance, memory-safe client-side CSV streaming uploader designed to bypass serverless payload limits (Vercel/AWS Lambda) and prevent browser Out-Of-Memory (OOM) crashes on multi-megabyte files.

---

## 📌 Архитектурная проблема

При попытке загрузить крупные CSV-файлы (сотни мегабайт или гигабайты) через стандартные веб-формы разработчики сталкиваются с двумя критическими барьерами:
1. **Out-Of-Memory (OOM) на клиенте:** Попытка прочитать файл целиком в память браузера через `FileReader.readAsText()` или `JSON.stringify()` приводит к мгновенному падению вкладки.
2. **Лимиты серверless-платформ (Vercel / AWS Lambda):** Ограничения на размер входящего HTTP-запроса (Payload Size Limits) и таймауты выполнения функций (Timeouts).

## 💡 Решение

Данный проект реализует **потоковую обработку (Streaming Architecture)** прямо в браузере с использованием современных веб-стандартов:
* **`File.slice()`**: Чтение файла локально фиксированными чанками (по умолчанию по 2 МБ) без загрузки всего файла в оперативной память.
* **`TextDecoder` (stream mode)**: Корректная сборка многобайтовых UTF-8 символов на стыках чанков.
* **Backpressure Control (`DELAY_MS`)**: Управление интенсивностью сетевого потока во избежание DDoS собственной бэкенд-инфраструктуры.
* **`AbortController`**: Возможность мгновенной отмены всех активных сетевых запросов пользователем.
* **Защита от Prototype Pollution**: Фильтрация опасных ключей (`__proto__`, `constructor`, и др.) на этапе парсинга заголовков.

---

## 🛠️ Основные компоненты

### 1. `StreamingCSVParser`
Потоковый стейт-машинный парсер, обрабатывающий текст построчно по мере поступления новых чанков. Игнорирует запятые внутри закавыченных полей и корректно обрабатывает экранированные кавычки (`""`).

### 2. Защита данных
* Передача авторизационного токена через кастомный заголовок `X-Upload-Token`.
* Строгая санитизация ключей через функцию `sanitizeKey`.

---

## ⚠️ Дисклеймер безопасности и технологии двойного назначения (Dual-Use)

Программное обеспечение и алгоритмы, представленные в этом репозитории, относятся к категории **технологий двойного назначения (Dual-Use Technology)**.

### Архитектурный контекст
Техники фрагментации данных (`drip-feeding`), потокового чтения байтовых диапазонов и контроля сетевого давления (`backpressure`) являются стандартными инженерными паттернами для оптимизации высоконагруженных веб-приложений. 

### Юридические ограничения и комплаенс
1. **Легитимное применение:** Инструмент предназначен исключительно для административных задач, импорта прайс-листов, аналитики и работы с корпоративными базами данных (например, Neon DB) в рамках предоставленных прав доступа.
2. **Риски нецелевого использования:** Модификация или переиспользование алгоритмов потоковой передачи для скрытой эксфильтрации данных, обхода систем предотвращения утечек (DLP) или осуществления низкоскоростных атак на инфраструктуру («Low-and-Slow») нарушает законодательство в сфере информационной безопасности.
3. **Уголовная ответственность:** Несанкционированный доступ к компьютерной информации, создание, использование и распространение вредоносных компьютерных программ, а также нарушение правил эксплуатации систем обработки данных преследуется по закону в соответствии с уголовными кодексами юрисдикций (включая статьи о несанкционированном доступе и киберпреступлениях).
4. **Отказ от ответственности:** Авторы и мейнтейнеры репозитория не несут никакой ответственности за прямой или косвенный ущерб, причиненный в результате неправомерного использования данного кода. Вся ответственность за соблюдение законов и регламентов возлагается на конечного пользователя.

---

## 📦 Быстрый старт

1. Импортируйте компонент `AdminUpload.tsx` в ваше React-приложение:
   ```tsx
   import AdminUpload from './pages/AdminUpload';

   export default function App() {
     return <AdminUpload/>;
   }

 * Настройте переменную окружения UPLOAD_SECRET_TOKEN на сервере.
 * Запустите проект:
   npm install
npm run dev

📄 Лицензия
Распространяется по лицензии MIT. Подробнее см. в файле LICENSE.


# Zero-OOM Streaming CSV Uploader for React & Next.js

> High-performance, memory-safe client-side CSV streaming uploader designed to bypass serverless payload limits (Vercel/AWS Lambda) and prevent browser Out-Of-Memory (OOM) crashes on multi-megabyte files.

---

## 📌 Architectural Problem

When attempting to upload large CSV files (hundreds of megabytes or gigabytes) via standard web forms, developers face two critical barriers:
1. **Client-side Out-Of-Memory (OOM):** Trying to read the entire file into browser memory via `FileReader.readAsText()` or `JSON.stringify()` causes the tab to crash instantly.
2. **Serverless Platform Limits (Vercel / AWS Lambda):** Payload size limits on incoming HTTP requests and function execution timeouts.

## 💡 Solution

This project implements a **Streaming Architecture** directly in the browser using modern web standards:
* **`File.slice()`**: Reading the file locally in fixed chunks (default 2 MB) without loading the entire file into RAM.
* **`TextDecoder` (stream mode)**: Correct assembly of multi-byte UTF-8 characters across chunk boundaries.
* **Backpressure Control (`DELAY_MS`)**: Managing network stream intensity to avoid DDoS-ing your own backend infrastructure.
* **`AbortController`**: Instant cancellation of all active network requests by the user.
* **Prototype Pollution Protection**: Filtering dangerous keys (`__proto__`, `constructor`, etc.) during header parsing.

---

## 🛠️ Core Components

### 1. `StreamingCSVParser`
A streaming state-machine parser that processes text line by line as new chunks arrive. It ignores commas inside quoted fields and correctly handles escaped quotes (`""`).

### 2. Data Security
* Authorization token transmission via a custom `X-Upload-Token` header.
* Strict key sanitization via the `sanitizeKey` function.

---

## ⚠️ Security Disclaimer & Dual-Use Technologies

The software and algorithms presented in this repository fall under the category of **Dual-Use Technologies**.

### Architectural Context
Data fragmentation techniques (`drip-feeding`), streaming byte-range reading, and network pressure control (`backpressure`) are standard engineering patterns for optimizing high-load web applications.

### Legal Restrictions and Compliance
1. **Legitimate Use:** The tool is intended solely for administrative tasks, price list imports, analytics, and working with corporate databases (e.g., Neon DB) within granted access rights.
2. **Misuse Risks:** Modifying or reusing streaming algorithms for covert data exfiltration, bypassing Data Loss Prevention (DLP) systems, or conducting low-and-slow infrastructure attacks violates information security regulations.
3. **Criminal Liability:** Unauthorized access to computer information, the creation, use, and distribution of malicious computer programs, and violation of data processing system rules are punishable by law under the criminal codes of respective jurisdictions (including cybercrime and unauthorized access statutes).
4. **Disclaimer of Liability:** The authors and maintainers of this repository bear no responsibility for direct or indirect damage caused by the misuse of this code. All responsibility for compliance with laws and regulations rests entirely with the end user.

---

## 📦 Quick Start

1. Import the `AdminUpload.tsx` component into your React application:
   ```tsx
   import AdminUpload from './pages/AdminUpload';

   export default function App() {
     return <AdminUpload/>;
   }

 * Configure the UPLOAD_SECRET_TOKEN environment variable on your server.
 * Run the project:
   npm install
npm run dev

📄 License
Distributed under the MIT License. See LICENSE for more information.




