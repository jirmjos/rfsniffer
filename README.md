# rfsniffer
Целью проекта является поддержка приема сигналов от различных радиоустройств диапазона 433.92 (метеодатчики, пульты и т.п.) с помощью радиомодуля RFM69 и ядерного linux драйвера, эмулирующего lirc устройство

Для сборки требуется Docker контейнер из http://contactless.ru/wiki/index.php/%D0%9A%D0%B0%D0%BA_%D1%80%D0%B0%D0%B7%D1%80%D0%B0%D0%B1%D0%B0%D1%82%D1%8B%D0%B2%D0%B0%D1%82%D1%8C_%D0%9F%D0%9E_%D0%B4%D0%BB%D1%8F_Wiren_Board

Сборка:
- git submodule init
- git submodule update --remote
- autoreconf -fvi  
- ./configure 
- make

В результате получается 2 нужных файла:
- rftest (tests/rftest)
- rfsniffer (rfsniffer/rfsniffer)

Необходимо скопировать rftest и rfsniffer куда-либо на устройство

Cейчас все настроено на работу с WB5 (RFM подключен к /dev/spidev32766.0, его DIO2 виден через /dev/lirc0, mqtt доступен без авторизации на localhost). При необходимости настройки можно изменить через параметры командной строки (./rfsniffer -?)

Также, большинство настроек можно передавать через json config. Пример в rfsniffer/rfsniffer.json. Использование конфига делается через -c <config.json>

rftest проверяет работу в целом (логирование, парсер, работу с RFM69, прием и декодирование пакетов от RFM69), создание тестового устройства для WB.

rfsniffer слушает эфир через RFM69 и 
- При приеме данных от сенсора RST создает устройство(или обновляет показания) RST_{id сенсора} с двумя контролами (температура, влажность)
- При приеме данных от сенсора Oregon создает устройство(или обновляет показания) oregon_rx_{тип сенсора}_{id сенсора}_{канал} с двумя контролами (температура, влажность)
- При приеме данных от передатчика X10 создает устройство X10 (или обновляет последнюю команду)
- При приеме данных от пультов Livolo или Raex создает устройство Remotes(или обновляет) с контролами Raex и Livolo, содержащими последнюю команды
- Работает передача команд от устройст Noolite TX 0xd61 (61, 63) из интерфейса wirenboard

При необходимости могут быть добавлены почти любые устройства, использующие для обмена OOK модуляцию на частоте 433.92Mhz. 

P.S. debian pkg и описание логики работы декодера сделаю чуть позднее. 
