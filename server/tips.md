# Полезные советы

Для реализации своего плагина берите за основу какой либо существующий. Подсматривайте существующие возможности в стандартных плагинах


## Кратко о том, что можно менять на сервере 



На сервере можно менять очень и очень многое. Нельзя добавить новый ресурс, так на стороне клиента его нет. Но можно сделать, что ядерное будет делать земли непригодными для заселения навсегда.

Можно ребалансить игру, как угодно. Можно переопределять поведение существующих построек, как душе угодно (но клиент будет видеть армию на водных провинциях с помощью маяка, даже если на стороне сервера он не дает этот бонус, т.к. это вписано в код на стороне клиента)

На сервер можно добавлять пользовательские сценарии и играть на них без каких-либо проблем. Возможно, будет не хватать перевода, но и это можно исправить, если экспортировать данные перевода, дописать в них перевод на нужные языки и разослать игрокам, чтобы поставили этот перевод в настройках

## Структура проекта:


#### Корень


.git - данные гита

assets - тут лежат данные о том, с каком провинции на какую можно перемещаться на разных картах

core - ядро игры

defnet - модуль, использующийся для облегчения работы с сетью

scripts - разные скрипты

.gitignore - какие данные не надо отправлять в систему версий

admins.dat - список админов (их uuid)

banned.dat - список забаненных по uuid

banned_ip.dat - список забаненных по ip

chat.dat - тут хранятся все сообщения, отправленные в чат

convert_to_global.bat - батник, который одним кликом переводит исходники игры в исходники сервера (они немного отличаются). Эта штука нужна мне чисто для автоматизации

convert_to_global.lua - то же самое, что и выше, только тут код на lua, эту штуку вызывает батник

environment - всякие глобальные функции. Вроде вывода таблиц на экран, функций кодирования данных в JSON для отправки по сети, а еще определение глобальных модулей, которые должны быть доступно во всех скриптах, например, luasocket

game_data.json - текущее сохранение сервера

log - лог сервера. Какие плагины и когда загружались, когда какие действия происходили

luaxxhash.lua - вспомогательный модуль, который нужен был для проверки, корректно ли пришли игровые данные на устройства (пока не используется, но пригодится в случае переписывания системы отправки данных)

README.md - описание сервера

server_crash - файл с данными о всех падениях сервера

server_settings.lua - настройки сервера (порт, название, время на ход и т.д.)

start.bat - батник для старта сервера. Внутри в цикле вызывается след. файл, чтобы сервер рестартовался после падения автоматически

start.lua - скрипт lua для старта сервера

start.sh - то же самое, что и start.bat, только для linux


#### Папка core


ai - боты

server - основной скрипт сервера и разные плагины

army_functions.lua - взаимодействие армий. Найм, перемещение, разные виды оружия

buildings_calc_functions.lua - модуль обработки действий от построек

calc_functions - функции, которые рассчитываются каждый ход (сколько денег тратится на содержание армии, на сколько вырастет население)

core.lua - ядро, все возможные действия в игре определены здесь. В зависимости от того, какой режим игры (сервер, клиент, одиночная) над этим скриптом находится определенная оболочка. Например, если режим игры одиночный и игрок переместил армию, то модуль одиночной игры вызывает модуль ядра и приказывает отрисовать изменение на карте. Если же игрок на сервере и не хост, то модуль клиента отправляет соответствующее сообщение на сервер, а сам вызывает модуль ядра, чтобы тот изменил игровые данные, т.к. действие совершено

event_system.lua - система событий. Пример: когда кто-то объявляет кому-то войну, то вызывается зарегистрированная в этой системе функцию. Сейчас используется только для балансировщика сил.

game_values.lua - игровые значения. Например, сколько стоит найм армии, когда начинается инфляция, раз в сколько ходов можно менять идеологию

global_functions.lua - глобальные функции. Например, найти путь от одной провинции до другой, узнать список соседей провинции, получить перевод какой-либо страны, посчитать очки страны

ideology.lua - модуль, в котором описана работа идеологий. Например, какой бонус к золоту в зависимости от выбранной идеологии или как должна происходить атака, если выбран коммунизм

offers.lua - система сообщений между странами. Например, кто-то отправил кому-то торговлю или объявил войну

relations.lua - отношения между странами. Начать войну, узнать кто с кем воюет. Узнать, вассал ли какая-то страна

timer.lua - модуль таймера. Чтобы каждые 3 минуты происходила смена хода или каждые 30 сек сервер проверял соединение