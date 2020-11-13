[![YaGames Logo](cover.png)](https://github.com/indiesoftby/defold-yagames)

# YaGames - Yandex.Games for Defold (WORK IN PROGRESS)

*This is an open-source project. It is not affiliated with Yandex LLC.*

YaGames is the Yandex.Games extension for the [Defold](https://www.defold.com/) game engine. [Yandex.Games](https://yandex.com/games/) is a collection of browser games for smartphones and computers. The games are available in Yandex Browser and the Yandex app. Games from the catalog are displayed in Yandex recommendation systems, which have a total audience of more than 50 million users per month.

You can check [here](https://radar.yandex.ru/yandex) the size of Yandex.Games audience. The platform is constantly growing.

## Installation

You can use it in your own project by adding this project as a [Defold library dependency](http://www.defold.com/manuals/libraries/). Open your `game.project` file and in the dependencies field under project add:

https://github.com/indiesoftby/defold-yagames/archive/master.zip

Or point to the ZIP file of a [specific release](https://github.com/indiesoftby/defold-yagames/releases).

**English edition of the documentation is not ready yet!**

# *На русском*

*Это проект с открытым исходным кодом. Он никак не аффилирован с компанией Яндекс.*

YaGames - это реализация SDK Яндекс.Игр для движка [Defold](https://www.defold.com/). [Яндекс.Игры](https://yandex.com/games/) - коллекция браузерных игр для смартфонов и компьютеров. Игры доступны через Яндекс.Браузер и приложение Яндекс на смартфонах.

## Быстрый старт

* [Официальная документация](https://yandex.ru/dev/games/doc/dg/concepts/about.html).
* [Telegram-чат Яндекс.Игр](https://t.me/yagamedev), где присутствуют официальные представители и модераторы.
* [Telegram-чат Defold Engine](https://t.me/DefoldEngine), где можно задать вопросы об этом расширении.

### Чек-лист для релиза Defold-игры на Яндекс.Играх

1. [Зарегистрируйтесь как разработчик в Яндекс.Играх](https://yandex.ru/dev/games/).
2. Подготовьте ассеты для каталога:
    1. Иконка 512x512 пикселей.
    2. Обложка 800x470 пикселей.
    3. Скриншоты по 2 шт. отдельно для мобильной и десктоп версии игр.
3. Переведите название игры на русский язык, подготовьте описание игры на русском.
4. Добавьте [ссылку на это расширение](https://github.com/indiesoftby/defold-yagames/archive/master.zip) в Dependencies вашего проекта. **С этого момента вы уже можете публиковать игру на Яндекс.Играх, она будет соответствовать требованиям.**
5. Подключите монетизацию в игре:
    1. С помощью полноэкранной рекламы в процессе загрузки игры, между уровнями игры, после поражения игрока. *Важно:* Во время показа такой рекламы звуки в игре должны выключаться.
    2. С помощью Rewarded рекламы. *Важно:* Во время показа такой рекламы звуки в игре должны выключаться.
    3. ~~С помощью контекстной рекламы (RTB-блоки).~~ Простой способ интеграции еще не реализован.
    4. С помощью внутриигровых покупок.
6. Включите Service Worker, скопировав файл `yagames/manifests/web/sw.js` в корневую директорию релизного билда игры:
    1. *Важно:* Укажите относительный путь к файлу `sw.js` в `game.project` настройках.
    2. *Важно:* В файле `sw.js` отредактируйте список файлов вашей игры. Сам `sw.js` включать в него не нужно.
    3. *Важно:* При каждом обновлении файлов игры на Яндексе вам нужно инкрементировать версию в файле `sw.js`.
7. Опубликуйте игру [в каталоге Яндекс.Игр](https://yandex.ru/dev/games/).

### Лучшие практики

1. Яндекс.Игры доступны только для HTML5, поэтому на остальных платформах это расширение имитирует работу SDK и предупреждает об этом на старте. Поэтому можно быстро внедрить и проверить работу SDK в основной среде разработки (Windows, macOS, Linux), затем загрузить полностью готовый к работе HTML5 билд вашей игры в каталог Яндекс.Игр.
2. Код из `yagames/manifests/web/engine_template.html` всегда внедряется в ваш HTML5 билд, соответственно SDK Яндекс.Игр подключается всегда. Это поведение не отключить, не удалив расширение.
3. Для каждой платформы и соответственно отдельно для Яндекс.Игр ведите отдельную ветку в вашем Git и не смешивайте в один файл код для каждой платформы, чтобы избежать больших ненужных ветвлений.

## Примеры кода

Расширение содержит демо-проект (директория `example`), с которым можно поиграться или использовать как внутренний отладочный экран в вашей игре.

![YaGames Demo](screenshot.png)

### 1. Initialization

To get started, you need to initialize the SDK using the `init` method.

```lua
local yagames = require("yagames.yagames")

local function init_handler(self, err)
    if err then
        print("Something bad happened :(", err)
    end
end

function init(self)
    yagames.init(init_handler)
end
```

### 2. Interstitial ad

Interstitial ads are ad blocks that completely cover the app background and show up before a user gets the data requested (for example, accessing the next game level).

*Don't call interstitial ads more often than once **every three minutes**. The ad window may fail to open if the calls are too frequent.*

* `open` - Called when an ad is opened successfully.
* `close` - Called when an ad is closed, an error occurred, or on ad failed to open due to too frequent calls. Used with the `was_shown` argument (type `boolean`), the value of which indicates whether an ad was shown.
* `offline` - Called when the network connection is lost (when offline mode is enabled).
* `error` - Called when an error occurrs. The error object is passed to the callback function.

```lua
local yagames = require("yagames.yagames")

local function adv_open(self)
    -- You should switch off all sounds!
end

local function adv_close(self, was_shown)
    -- You can switch sounds back!
end

local function adv_offline(self)
    -- Internet is offline
end

local function adv_error(self, err)
    -- Something wrong happened :(
end

function on_message(self, message_id, message)
    if message_id == hash("show_fullscreen_adv") then
        yagames.adv_show_fullscreen_adv({
            open = adv_open,
            close = adv_close,
            offline = adv_offline,
            error = adv_error
        })
    end
end
```

### 3. Rewarded videos

Rewarded videos are video ad blocks used to monetize games and earn a reward or in-game currency.

* `open` - Called when a video ad is displayed on the screen.
* `rewarded` - Called when a video ad impression is counted. Use this function to specify a reward for viewing the video ad.
* `close` - Called when a user closes a video ad or an error happens.
* `error` - Called when an error occurrs. The error object is passed to the callback function.

The `close` callback is called in any situations, even if there was an error.

```lua
local yagames = require("yagames.yagames")

local function rewarded_open(self)
    -- You should switch off all sounds!
end

local function rewarded_rewarded(self)
    -- Add coins!
end

local function rewarded_close(self)
    -- You can switch sounds back!
end

local function rewarded_error(self, err)
    -- Something wrong happened :(
end

function on_message(self, message_id, message)
    if message_id == hash("show_rewarded_video") then
        yagames.adv_show_rewarded_video({
            open = rewarded_open,
            rewarded = rewarded_rewarded,
            close = rewarded_close,
            error = rewarded_error
        })
    end
end
```

## Настройки расширения в `game.project`

```ini
[yagames]
sdk_init_options = {}
service_worker_url = sw.js
```

* `sdk_init_options` - JavaScript код. Это дополнительные опции инициализации Yandex Games SDK, передаются [в метод `YaGames.init`](https://yandex.ru/dev/games/doc/dg/sdk/sdk-about.html). Пример: `{ orientation: { value: "landscape", lock: true } }`.
* `service_worker_url` - Ссылка на файл Service Worker. В большинстве случаев это `sw.js`. Указание этой ссылки включает поддержку Service Worker.

## YaGames Lua API

Yandex.Games JavaScript SDK uses ES6 Promise for asynchronous operations. For Lua API promises were replaced with callback functions with arguments `(self, err, result)`, where

- `self` <kbd>userdata</kbd> - Script self reference.
- `err` <kbd>string</kbd> - Error code if something went wrong.
- `result` - Data if the operation should return something.

### Таблица соответствия с официальным SDK

| Yandex.Games JS SDK | YaGames Lua API |
| ------------------- | --------------- |
| `YaGames.init(options)` | `yagames.init(callback)` - The `options` can be set in the `yagames.sdk_init_options` setting. |
| `ysdk.deviceInfo.isDesktop()` | `yagames.device_info_is_desktop()` |
| `ysdk.deviceInfo.isMobile()` | `yagames.device_info_is_mobile()` |
| `ysdk.deviceInfo.isTablet()` | `yagames.device_info_is_tablet()` |
| `ysdk.adv.showFullscreenAdv({callbacks:{}})` | `yagames.adv_show_fullscreen_adv(callbacks)` [<kbd>Example</kbd>](#2-interstitial-ad) |
| `ysdk.adv.showRewardedVideo({callbacks:{}})` | `yagames.adv_show_rewarded_video(callbacks)` [<kbd>Example</kbd>](#3-rewarded-videos) |
| `ysdk.auth.openAuthDialog()` | `yagames.auth_open_auth_dialog(callback)` |
| `ysdk.getPlayer(options)` | `yagames.player_init(options, callback)` |
| `player.setData(data, flush)` | `yagames.player_set_data(data, flush, callback)` |
| `player.getData(keys)` | `yagames.player_get_data(keys, callback)` |
| `player.setStats(stats)` | `yagames.player_set_stats(stats, callback)` |
| `player.incrementStats(increments)` | `yagames.player_increment_stats(increments, callback)` |
| `player.getStats(keys)` | `yagames.player_get_stats(keys, callback)` |
| `player.getID()` <kbd>Deprecated</kbd> | `yagames.player_get_id()` <kbd>Deprecated</kbd> |
| `player.getUniqueID()` | `yagames.player_get_unique_id()` |
| `player.getIDsPerGame()` | `yagames.player_get_ids_per_game(callback)` |
| `player.getName()` | `yagames.player_get_name()` |
| `player.getPhoto(size)` | `yagames.player_get_photo(size)` |
| `ysdk.getPayments(options)` | `yagames.payments_init(options, callback)` |
| `payments.purchase(options)` | `yagames.payments_purchase(options, callback)` |
| `payments.getPurchases()` | `yagames.payments_get_purchases(callback)` - The result has the format `{ purchases = { ... }, signature = "..." }` |
| `payments.getCatalog()` | `yagames.payments_get_catalog(callback)` |
| `payments.consumePurchase(purchaseToken)` | `yagames.payments_consume_purchase(purchase_token, callback)` |

## Banner Ads

You can additionally monetize your game using Yandex Advertising Network *Real-Time Bidding* ad blocks. RTB block is rendered into HTML div block and placed over your game canvas.

The official documentation is here - [https://yandex.ru/support/partner2/web/products-rtb/about.html](https://yandex.ru/support/partner2/web/products-rtb/about.html)

### Creating RTB blocks

Create an RTB block in [the Yandex Advertising Network interface](https://partner2.yandex.ru/v2/context/rtb/) and copy **RTB id** of the block:

![RTB id](rtb_copy_id.png)

The ad block will be displayed within 30 minutes after saving the code and placing it on the game page. 

## Banner Ads Lua API

### yagames.banner_init(callback)
Loads Yandex Advertising Network SDK and calls the callback.

_PARAMETERS_
* __callback__ <kbd>function</kbd> - Function to call when the Yandex Advertising Network SDK has initialized.

The `callback` function is expected to accept the following values:

* __self__ <kbd>userdata</kbd> - Script self reference.
* __error__ <kbd>string</kbd> - Error code if something went wrong.

### yagames.banner_create(rtb_id, options, [callback])
Creates a DOM element (`<div></div>`) with `style="position: absolute"`, applies your CSS styles on it and renders an advertisement into the element.

_PARAMETERS_
* __rtb_id__ <kbd>string</kbd> - The unique RTB block ID. The block ID consists of a product ID (`R-A`), platform ID and the block's serial number.
* __options__ <kbd>table</kbd> - The table with key-value pairs.
* __callback__ <kbd>function</kbd> - The callback function that is invoked after ad rendering.

The `options` table can have these key-value pairs:
* __stat_id__ <kbd>integer</kbd> - The sample ID. A number between 1 and 1000000000. This will allow you to view group statistics for that block.
* __css_styles__ <kbd>string</kbd> - Sets inline CSS styles of the `<div></div>` element.
* __css_class__ <kbd>string</kbd> - Sets the value of the `class` attribute of the `<div></div>` element.
* __display__ <kbd>string</kbd> - The `display` property allows to show or hide the element. If you set `display` = `none`, it hides the entire element. Use `block` to show it back.

The `callback` function allows you to obtain information about whether the ad has been rendered (whether the ad was successfully selected when requested from the RTB system) and which particular ad was shown. The `callback` function is expected to accept the following values:

* __self__ <kbd>userdata</kbd> - Script self reference.
* __error__ <kbd>string</kbd> - Error code if something went wrong.
* __data__ <kbd>table</kbd> - The function obtains the `data.product` parameter with one of two values: `direct` — Yandex.Direct ads were shown in an RTB ad block, `rtb` — A media ad was shown in an RTB ad block.

If there were no suitable product listings at the auction to show your ad next to, then you can show your ad in the block. In this situation the `callback` function returns the error `No ads available.`.

### yagames.banner_delete(rtb_id)
Removes the DOM element.

_PARAMETERS_
* __rtb_id__ <kbd>string</kbd> - The unique RTB block ID. The block ID consists of a product ID (`R-A`), platform ID and the block's serial number.

### yagames.banner_refresh(rtb_id, [callback])
Requests SDK to render new advertisement.

_PARAMETERS_
* __rtb_id__ <kbd>string</kbd> - The unique RTB block ID. The block ID consists of a product ID (`R-A`), platform ID and the block's serial number.
* __callback__ <kbd>function</kbd> - The callback function that is invoked after ad rendering.

The `callback` function is described in the `yagames.banner_create` section above.

### yagames.banner_set(rtb_id, property, value)
Sets a named property of the specified banner.

_PARAMETERS_
* __rtb_id__ <kbd>string</kbd> - The unique RTB block ID. The block ID consists of a product ID (`R-A`), platform ID and the block's serial number.
* __property__ <kbd>string</kbd> - Name of the property to set.
* __value__ <kbd>string</kbd> - The value to set.

_PROPERTIES_:
* __stat_id__ <kbd>integer</kbd> - The sample ID. A number between 1 and 1000000000. This will allow you to view group statistics for that block.
* __css_styles__ <kbd>string</kbd> - Sets inline CSS styles of the `<div></div>` element.
* __css_class__ <kbd>string</kbd> - Sets the value of the `class` attribute of the `<div></div>` element.
* __display__ <kbd>string</kbd> - The `display` property allows to show or hide the element. If you set `display` = `none`, it hides the entire element. Use `block` to show it back.

## Sitelock

It's a good idea to protect your HTML5 game from simple copy-pasting to another website. YaGames has Sitelock API for that purpose. It's simple, but it's better than nothing.

By default, it checks hostnames `yandex.net` (CDN of the Yandex.Games) and `localhost` (for local debugging).

```lua
local sitelock = require("yagames.sitelock")

-- Also you can add your domains:
-- sitelock.add_domain("yourdomainname.com")

function init(self)
    if html5 and sitelock.is_release_build() then
        if not sitelock.verify_domain() then
            -- Show warning and pause the game
        end
    end
end
```

## License

Лицензия проекта - **MIT**. Разработан и поддерживается [@aglitchman](https://github.com/aglitchman). Основан на исходном коде [JsToDef](https://github.com/AGulev/jstodef).
