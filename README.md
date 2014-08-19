[![Build Status](https://travis-ci.org/hflabs/suggestions-jquery.png?branch=master)](https://travis-ci.org/hflabs/suggestions-jquery)

DaData Suggestions
==================

jQuery-плагин для сервиса Подсказок DaData.ru.

Основан на jQuery и плагине DevBridge Autocomplete (https://github.com/devbridge/jQuery-Autocomplete).

##API

* `$(selector).suggestions(options);`
    * Инициализирует плагин для выбранных input-полей.
    * `options`: Объект, определяющий начальные параметры работы плагина.
        * `serviceUrl`: URL сервиса.
        * `type`: Строка `"NAME"|"ADDRESS"|"PARTY"`. Указывает тип искомого содержимого. Параметр обязателен.
        * `onSelect`: `function (suggestion) {}` Функция вызывается, когда пользователь выбирает вариант из списка. `this` внутри нее указывает на input элемент.
        * `onSelectNothing`: `function (query) {}` Функция вызывается, когда должна быть выбрана подсказка (например, пользователь нажал ENTER или перешел к другому элементу формы), а подходящей подсказки нет. `this` указывает на input элемент.
        * `minChars`: Минимальная длина поисковой строки, необходимая для начала поиска. По умолчанию: `1`.
        * `maxHeight`: Максимальная высота выпадающего списка. По умолчанию: `300`.
        * `deferRequestBy`: Период ожидания (в миллисекундах) перед отправкой ajax запроса. По умолчанию: `100`.
        * `width`: Ширина выпадающего списка, в пикселах. По умолчанию: `auto`, соответствует ширине поля ввода.
        * `params`: Дополнительные параметры, которые требуется включить в ajax-запрос. Может быть как объектом, так и функцией `function (query) {}`.
        * `formatResult`: `function (value, currentValue, suggestion) {}` функция, производящая дополнительное форматирование подсказки. Возвращает html.
        * `delimiter`: Строка или RegExp, разделяющая поисковую строку на фрагменты. Если задана, в ajax-запрос будет включаться последний из них.
          Полезен в случаях, когда нужно в input-поле получить несколько значений, например, перечисленных через запятую.
        * `zIndex`: 'z-index' для выпадающего списка. По умолчанию: `9999`.
        * `noCache`: Флаг, препятствующий кэшированию ответов сервера. По умолчанию: `false`.
        * `onSearchStart`: `function (query) {}` вызывается перед началом ajax-запроса. `this` привязано к input-элементу. Здесь можно модифицировать объект `query`.
        * `onSearchComplete`: `function (query, suggestions) {}` вызывается по завершении ajax-запроса. `this` привязано к input элементу. В `suggestions` передается массив полученных подсказок.
        * `onSearchError`: `function (query, jqXHR, textStatus, errorThrown) {}` вызывается при неудачном ajax-запросе. `this` привязано к input элементу.
        * `onInvalidateSelection`: `function (suggestion) {}` вызывается при изменении значение input-элемента, после выбора варианта из списка. `this` привязано к input-элементу.
        * `triggerSelectOnSpace`: Флаг, предписывающий вызывать `onSelect` при нажатии пробела, если выбран какой-либо вариант или посковая фраза совпадает с каким-либо вариантом. По умолчанию: `true`.
        * `preventBadQueries`: Флаг, предписывающий предотвращать запросы к серверу, если предыдущие запросы для поисковой фразы, являющейся началом текущей фразы возвращали нулевое количество подсказок. Например, если запрос для фразы `Jam` не вернул ни одной подсказки, то для всех фраз, начинащихся на `Jam` запросы отправлены не будут. По умолчанию `false`. 
        * `beforeRender`: `function (container) {}` вызывается перед показом выпадающего списка.
        * `tabDisabled`: По умолчанию `false`. Если установлен в `true`, то при нажатии `Tab` курсор будет оставаться в поле ввода.
        * `paramName`: По умолчанию `query`. Имя параметра в ajax-запросе, содержащего поисковую строку.
        * `autoSelectFirst`: если установлен в `true`, первая подсказка будет автоматически выделена при появлении списка. По умолчанию: `false`.
        * `token`: Строка, передаваемая в заголовке запроса для авторизации.
        * `usePreloader`: Флаг, указывающий на необходимость отображать прелоадер во время ожидания ответа сервера. По умолчанию: `true`.
        * `hint`: Поясняющий текст, выводимый в выпадающем списке над подсказками. По умолчанию: "Выберите вариант ниже или продолжите ввод".
            Чтобы задать этот текст один раз для всех полей, можно переопределить $.Suggestions.defaultOptions.hint = 'Select a suggestion below'
        * `useDadata`: Флаг, предписывающий использовть сервис dadata.ru для обогащения подсказок. По умолчанию: `true`, но требует наличия опций `token` и `type`. Обращение производится в случае:
            * выбора подсказки из списка (если определен обработчик `onSelect`). В `onSelect` будет передан агрумент `suggestion`, дополненный данными от dadata.ru
            * отсутствия подсказок по введенному запросу. Ответ от dadata.ru будет использован как наиболее вероятная подсказка.
        * `count`: Желаемое количество получаемых подсказок. По умолчанию: 10.
        * `$helpers`: HTML-элемент либо jQuery-объект либо селектор. Элементы, переданные в этой опции считаются вспомогательными элементами виджета, и нажатия кнопки мыши по ним не ведет к перемещению фокуса.
        * `constraints`: Ограничения, сужающие область поиска. Может быть как объектом со структурой `constraint` ([см. ниже](#Ограничения-области-поиска)), так и массивом таких объектов.
        * `geoLocation`: По умолчанию `true`. Заставляет сервер при ранжировании подсказок адресов учитывать информацию о местонахождении клиента.  

Инициализированный плагин имеет следующие методы:

* `setOptions(options)`: обновляет опции.
* `clear`: очищает кэш запросов и текущий список подсказок.
* `clearCache`: очищает кэш запросов.
* `disable`: временно деактивирует функционал плагина.
* `enable`: включает плагин, если он был деактивирован.
* `hide`: скрывает список с подсказками.
* `fixPosition`: принудительно позиционирует вспомогательные элементы.
* `dispose`: уничтожает экземпляр плагина.
* `setSuggestion(suggestion)`: запоминает переданную подсказку как выбранную.
* `update`: запрашивает или берет из кэша и затем выводит список подсказок.
* `getGeoLocation`: возвращает $.Deferred объект, содержащий информацию о местонахождении клиента. См [Геолокация](#Геолокация).

Есть два способа обращения к этим методам. Первый состоит в передаче названия метода как строкового аргумента, за которым могут следовать иные необходимые аргументы:

    $('#suggestions').suggestions('disable');
    $('#suggestions').suggestions('setOptions', options);

Или можно получить экземпляр плагина, и затем вызывать его методы:

    $('#suggestions').suggestions().disable();
    $('#suggestions').suggestions().setOptions(options);

##Пример использования

Html:

    <input type="text" name="fio" id="suggestions"/>

Получение подсказок через ajax:

    $('#suggestions').suggestions({
        serviceUrl: '/api/fio',
        onSelect: function (suggestion) {
            alert('You selected: ' + suggestion.value + ', ' + suggestion.data);
        }
    });

##Стили

При инициализации плагин создает дополнительные HTML-элементы. Пример приведен ниже.

    <input type="text" name="fio" id="suggestions" class="suggestions-input"/>
    <div class="suggestions-wrapper">
        <i class="suggestions-preloader"></i>
        <div class="suggestions-suggestions">
            <div class="suggestions-suggestion suggestions-selected">...</div>
            <div class="suggestions-suggestion">...</div>
            <div class="suggestions-suggestion">...</div>
        </div>
    </div>

Пример стилей:

    .suggestions-suggestions { border: 1px solid #999; background: #FFF; overflow: auto; }
    .suggestions-suggestion { padding: 2px 5px; white-space: nowrap; overflow: hidden; }
    .suggestions-selected { background: #F0F0F0; }
    .suggestions-suggestions strong { font-weight: normal; color: #3399FF; }
    .suggestions-hint { color: #777; padding: 2px 5px; }

Следует иметь в виду, что предустановленный стиль `suggestions-input` задает ширину input элемента равную "100%". Чтобы она перекрывалась нужным значением, размещайте ссылку на `suggestions.css` до ссылок на другие css-файлы, либо используйте более специфичные селекторы.
    
##Формат ответа

Сервер должен передавать следующий объект в формате JSON:

    {
        suggestions: [
            { value: 'United Arab Emirates', data: 'AE' },
            { value: 'United Kingdom',       data: 'UK' },
            { value: 'United States',        data: 'US' }
        ]
    }

Атрибут `data` может быть любого типа. Его значение будет передаваться в функции formatResults и onSelect. Если этот атрибут не используется, то response.suggestions может быть массивом строк:

    {
        suggestions: ["United Arab Emirates", "United Kingdom", "United States"]
    }

##Ограничения области поиска

Существует возможность органичить поиск адресов определенным регионом или городом. Для этого необходимо передать плагину опцию `constraints`, состоящую из одного или нескольких `constraint`-объектов. Структура этих объектов следующая:

    {
        label: 'Бердск (НСО)',      // текст метки, который будет выведен пользователю
        locations: {                // параметры, которые будут переданы на сервер
            'region': 'новосибирская',
            'city': 'бердск'
        },
        deletable: true             // флаг, позволяющий пользователю удалить данное ограничение
    }
    
Значение `label` можно не указывать, в этом случае отображаемый текст будет составлен из значений `locations`.
Поле `locations` задает непосредственно условия поиска. Может содержать: `kladr_id`, `okato`, `postal_code`, `region`, `area`, `city` и `settlement`. Может также быть массивом таких объектов.

Примеры:

    // ограничивает область поиска Москвой
    $('#suggestions').suggestions().setOptions({
        constraints: {
            locations: {
                region: 'Москва'
            }
        }
    });
    
    // ограничивает область поиска двумя регионами - Сахалинской областью и Камчатским краем.
    // каждое из органичений может быть отменено пользователем
    $('#suggestions').suggestions().setOptions({
        constraints: [
            {
                label: 'Сахалин',
                locations: {
                    kladr_id: '6500000000000'
                },
                deletable: true
            },
            {
                label: 'Камчатка',
                locations: {
                    region: 'камчатский'
                },
                deletable: true
            }
        ]
    });
    
    // ограничивает поиск несколькими условиями сразу
    $('#suggestions').suggestions().setOptions({
        constraints: {
            label: 'ЮФО',
            locations: [
                {'region': 'адыгея'},
                {'region': 'астраханская'},
                {'region': 'волгоградская'},
                {'region': 'калмыкия'},
                {'region': 'краснодарский'},
                {'region': 'ростовская'}
            ]
        }
    });

## Геолокация

Для подсказок адресов есть возможность использовать сервис геолокации. При инициализации первого экземпляра плагина на странице, отправляется запрос на сервер, получающий в ответ объект c автоматически определенным адресом вида:

    {
        country: 'Россия',
        region: 'Санкт-Петербугр',
        region_type: 'г',
        region_type_full: 'город',
        kladr_id: '7800000000000',
        ...
    }
    
В последующих запросах некоторые поля этого объект будет передаваться на сервер, и сервер будет учитывать их при ранжировании найденных подсказок.
По умолчанию эта функция включена.
Для ее отключения нужно задать:
 
    $('#address').suggestions('setOptions', {
        geoLocation: false
    });

Также, есть возможность задать местоположение вручную, указав код КЛАДР (учитываются максимум 11 знаков, что соответствующие уровню населенного пункта):
 
     $('#address').suggestions('setOptions', {
         geoLocation: { kladr_id: '63' }        // соответствует самарской области
     });
     
     $('#address').suggestions('setOptions', {
         geoLocation: { kladr_id: '63000001' }  // соответствует городу Самаре
     });

     
Можно задать местоположение для всех экземпляров плагина:
 
    $.Suggestions.defaultOptions.geoLocation = { kladr_id: ... };
    
Объект с определенным местоположением клиента можно использовать вне плагина:
 
    $('#address').suggestions('getGeoLocation')
    или 
    $('#address').suggestions().getGeoLocation()
    
Метод вернет `$.Deferred` объект (поскольку информация о местоположении доступна не сразу после инициализации).
Пример:

    $('#address).suggestions('getGeoLocation')
        .done(function(locationData){
            // Функция будет вызвана, когда местоположение определено.
        })
        .fail(function(){
            // Функция будет вызвана, если местоположение определить не удалось.
        })
        
### Сброс данных о местоположении

Запрос на определение местоположения отправляется к серверу только раз и затем кэшируется. Вызов `$.Suggestions.resetLocation()` удаляет закешированные данные. Запрос будет отправлен после, при последующей инициализации экземпляра с подсказками адресов, либо при вызове метода `setOptions` на одном из них.  
