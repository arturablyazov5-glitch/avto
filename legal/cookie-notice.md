# Cookie и Яндекс Метрика: нужен ли баннер

Не юридическая консультация. Доступ к источникам: 08.10.2026.

## Вывод

**Баннер нужен. Метрику включать только после «Принять».** Это рекомендация, а не прямая норма, поэтому отмечено «проверить у юриста».

Почему:

1. **Отдельного закона о cookie в РФ нет.** Всё решает 152-ФЗ. В нём ПД — «любая информация, относящаяся прямо или косвенно к определённому или определяемому физлицу» (п. 1 ст. 3). Cookie, ClientID Метрики и IP вместе с данными о поведении на сайте позволяют выделить конкретного посетителя. Позиция «это ПД» распространена в практике РКН и судов, но **официального разъяснения РКН именно по cookie и Метрике я не нашёл** (rkn.gov.ru и pd.rkn.gov.ru из окружения были недоступны). Проверить у юриста.
2. **Условия Яндекс Метрики** (yandex.ru/legal/metrica_termsofuse, публикация 07.08.2026) обязывают владельца сайта соблюдать законодательство о ПД и, «если того требует применимое законодательство», получать согласия посетителей. Ответственность за это несёт владелец сайта, а не Яндекс.
3. Если cookie — это ПД, то для аналитики (а это не исполнение договора) остаётся одно основание: согласие (п. 1 ч. 1 ст. 6, ст. 9 152-ФЗ). Согласие должно быть «конкретным… однозначным» и оформленным отдельно (ч. 1 ст. 9, ред. 156-ФЗ от 24.06.2025). Плашка «Продолжая пользоваться сайтом, вы соглашаетесь…» без кнопки выбора под эти признаки, скорее всего, не подходит.
4. Цель Метрики «статистика и оценка рекламы» надо указать в Политике. Это сделано в `privacy.html`, раздел 5.

Практический компромисс для лендинга под Директ: Метрика нужна для оптимизации кампаний, и часть посетителей откажется. Это осознанный выбор владельца между потерей части статистики и правовым риском. Минимальный рискованный вариант (Метрика сразу, баннер только информирует) **не рекомендую**, но если владелец его выберет, решение надо зафиксировать в checklist (П-07).

Вебвизор (запись действий, включая ввод в поля) повышает риск: в записи могут попасть имя и телефон из формы. Если Вебвизор включён, нужно либо запретить запись полей формы (атрибуты/настройки Метрики «не записывать содержимое полей»), либо выключить Вебвизор. Проверить настройку счётчика.

## Тексты

- Баннер: «Мы используем cookie и Яндекс Метрику, чтобы считать посещения и улучшать сайт. Подробнее — в [политике обработки персональных данных](privacy.html).» Кнопки: «Принять» / «Только необходимые».
- Ссылка в подвале: «Политика обработки персональных данных».

## Готовый фрагмент (HTML + CSS + JS, без зависимостей)

Как подключить: вставить перед `</body>`. Код счётчика Метрики **убрать** из `<head>` и задать номер в `GM_METRIKA_ID`. Счётчик подключится сам после согласия. Для повторных визитов выбор хранится в `localStorage` (обёрнут в try/catch). Если хранилище недоступно, баннер просто покажется снова.

```html
<div id="gm-cookie" class="gm-cookie" role="dialog" aria-live="polite" aria-label="Уведомление о cookie" hidden>
  <p>Мы используем cookie и Яндекс Метрику, чтобы считать посещения и улучшать сайт.
     Подробнее — в <a href="privacy.html">политике обработки персональных данных</a>.</p>
  <div class="gm-cookie__btns">
    <button type="button" data-gm-cookie="all">Принять</button>
    <button type="button" data-gm-cookie="necessary" class="gm-cookie__alt">Только необходимые</button>
  </div>
</div>
<style>
  .gm-cookie{position:fixed;left:16px;right:16px;bottom:16px;z-index:9999;max-width:560px;margin:0 auto;
    background:#23262A;color:#E7E9EC;border:1px solid #33373D;border-left:3px solid #0C87A5;border-radius:10px;
    padding:14px 16px;font:14px/1.5 system-ui,-apple-system,"Segoe UI",Roboto,Arial,sans-serif;box-shadow:0 8px 24px rgba(0,0,0,.4)}
  .gm-cookie[hidden]{display:none}
  .gm-cookie p{margin:0 0 10px}
  .gm-cookie a{color:#2BB3D4}
  .gm-cookie__btns{display:flex;gap:8px;flex-wrap:wrap}
  .gm-cookie button{flex:1 1 140px;min-height:44px;border:0;border-radius:8px;background:#0C87A5;color:#fff;font:inherit;font-weight:600;cursor:pointer}
  .gm-cookie button.gm-cookie__alt{background:transparent;color:#E7E9EC;border:1px solid #4A4F57}
  .gm-cookie button:focus-visible{outline:2px solid #2BB3D4;outline-offset:2px}
</style>
<script>
(function () {
  var GM_METRIKA_ID = 0; // ← номер счётчика Яндекс Метрики (число)
  var KEY = 'gm_cookie_v1';
  var box = document.getElementById('gm-cookie');
  function get() { try { return localStorage.getItem(KEY); } catch (e) { return null; } }
  function set(v) { try { localStorage.setItem(KEY, v); } catch (e) {} }
  function loadMetrika() {
    if (!GM_METRIKA_ID || window.ym) return;
    (function(m,e,t,r,i,k,a){m[i]=m[i]||function(){(m[i].a=m[i].a||[]).push(arguments)};
      m[i].l=1*new Date();k=e.createElement(t),a=e.getElementsByTagName(t)[0];k.async=1;k.src=r;a.parentNode.insertBefore(k,a)})
      (window, document, 'script', 'https://mc.yandex.ru/metrika/tag.js', 'ym');
    window.ym(GM_METRIKA_ID, 'init', { clickmap: true, trackLinks: true, accurateTrackBounce: true, webvisor: false });
  }
  var choice = get();
  if (choice === 'all') { loadMetrika(); }
  else if (!choice && box) { box.hidden = false; }
  if (box) box.addEventListener('click', function (ev) {
    var v = ev.target && ev.target.getAttribute('data-gm-cookie');
    if (!v) return;
    set(v); box.hidden = true;
    if (v === 'all') loadMetrika();
  });
})();
</script>
```

Замечания:
- Цели Метрики (`ym(ID,'reachGoal','lead')`) вызывать через проверку `if (window.ym)`: без согласия счётчика нет.
- Директ без Метрики не видит конверсии у тех, кто отказался. Это ожидаемо.
- `webvisor:false` стоит по умолчанию. Включать только после решения П-07 и с запретом записи полей формы.
- Код загрузки `tag.js` — стандартный сниппет Метрики. Сверить с актуальным кодом из интерфейса счётчика.
