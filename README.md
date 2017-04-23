* Изначально, файл `service-worker.js` находится в папке `/assets`. Поэтому, service worker обрабатывает только запросы, которые начинаются с `/assets`. **Для того, чтобы service worker обрабатывал все запросы его нужно переместить в корень.**

* Далее в `service-worker.js` нужно поменять путь `importScripts('../vendor/kv-keeper.js-1.0.4/kv-keeper.js');` на `importScripts('vendor/kv-keeper.js-1.0.4/kv-keeper.js');`.

* В `blocks.js` нужно поменять строку 469 с `.register('./assets/service-worker.js')` на `.register('../service-worker.js')`.

* Также нужно добавить `cacheKey.includes('gifs.html')||` в `function needStoreForOffline`.

# Ответы на вопросы

## строка 14: Вопрос №1: зачем нужен этот вызов?

`self.skipWaiting()` активизирует service worker как только новая версия service worker заканчивает установку. Таким образом, не нужно перезагружать браузер, чтобы новая версия была активирована.

## строка 24: Вопрос №2: зачем нужен этот вызов?

Без строки `self.clients.claim();` service worker активизируется только после перезагрузки страницы. Данный вызов позволяет активизировать service worker сразу после установки первой версии.

## строка 41: Вопрос №3: для всех ли случаев подойдёт такое построение ключа?

Видимо, нет, так как объект url может содержать другие properties, помимо origin и pathname (https://developer.mozilla.org/en/docs/Web/API/URL).

## строка 115: Вопрос №4: зачем нужна эта цепочка вызовов?

Комментарии к коду:
```
return Promise.all(
                names.filter(name => name !== CACHE_VERSION) /*Если имя кэша не равно текущей весии, то включить в массив для удаления*/
                    .map(name => {
                        console.log('[ServiceWorker] Deleting obsolete cache:', name);
                        return caches.delete(name); /*Для каждого имени кэша в отфильтрованном массиве создать Promise который отвечает за удаления данного кэша*/
                    }) /*Возвращает массив промисов как аргумент в функцию Promise.all*/
            );

```

## строка 139: Вопрос №5: для чего нужно клонирование?

Клонирование нужно так как response - это [Stream](https://streams.spec.whatwg.org/), и его можно прочитать только один раз.

