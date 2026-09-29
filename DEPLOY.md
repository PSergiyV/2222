# Платные экскурсии: запуск за 7 шагов

Нужен бесплатный аккаунт на cloudflare.com и установленный Node.js.

1. Откройте терминал в папке `worker` и выполните: `npm i -g wrangler`, затем `wrangler login`.
2. Создайте хранилища:
   - `wrangler r2 bucket create audio-tours`
   - `wrangler kv namespace create CODES` — в ответе будет `id`, вставьте его в `wrangler.toml` вместо ВСТАВЬТЕ_ID_ИЗ_ШАГА_2.
3. Придумайте длинный пароль администратора: `wrangler secret put ADMIN_KEY`.
4. Выполните `wrangler deploy`. В конце появится адрес вида `https://audio-tours.ВАШ-АККАУНТ.workers.dev`. Впишите его в поле `"api"` файла `tours.json`.
5. Загрузите полный mp3 в R2 под именем `<id экскурсии>.mp3` (например, `lviv-rynok.mp3`): через сайт Cloudflare (R2, бакет audio-tours, Upload) или командой `wrangler r2 object put audio-tours/lviv-rynok.mp3 --file "путь/к/файлу.mp3"` (если файл попал в локальное хранилище, добавьте `--remote`).
6. Загрузите на GitHub Pages `index.html`, `sw.js`, `tours.json` и файл превью (`lviv-rynok-preview.mp3`). Полный mp3 в публичный репозиторий не кладите. Если он там уже лежит, удалите его; в истории коммитов он останется, поэтому для настоящих экскурсий лучше сразу создать репозиторий без полных файлов.
7. В `tours.json` в поле `"buyURL"` укажите вашу платёжную ссылку для этой экскурсии, в `"price"` цену.

## Как выдать код после оплаты

```
curl -X POST https://audio-tours.ВАШ-АККАУНТ.workers.dev/admin/codes \
  -H "Authorization: Bearer ВАШ_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{"tourId":"lviv-rynok","count":1}'
```

В ответе будет код (например, `K7M2XQ9PAB`). Отправьте его покупателю. Он вводит код в приложении, и полная экскурсия открывается. Один код работает на трёх устройствах.
Добавить платную экскурсию: положите в R2 файл `<id>.mp3`, а в `tours.json` добавьте экскурсию с полями `previewURL`, `price`, `buyURL` (без `audioURL`).
