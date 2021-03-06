# AutoUpdateIB

## Автообновление информационных баз типовых конфигураций 1С

Скрипт, реализованый на Oscript, который позволяет запускать фоновый процесс обновления типовых баз 1С.
Главной отличительно особенностью есть то, что для его работы надо только заполнить файл настроек и он готов к использованию. Настройки задаются в файле **settings.json**, где можно описать:

- Общие настройки подключения
- Список баз, которые надо обслужить.

Движок обновления взят тут - [https://github.com/BlackDrak0n/oscript-AutoUpdateIB](https://github.com/BlackDrak0n/oscript-AutoUpdateIB)]
Поиск обновления происходит с помощью старого способа - через **/ipp/ITSREPV/V8Update/Configs**

## Возможности

- Обновление баз
  - Поиск для каждой базы наличия обновления
  - Если в каталоге файлов обновления нужная версия отсутствует, то запускается скачивание и распаковка
  - После этого запускается процесс обновления, принятие изменений, потверждение легальности
- Отправка уведомлений через телеграм с состоянием процессов для каждой базы
- Сохранение лога скрипта в файл в каталоге обработки **log** для возможного анализа проблемных ситуаций

## Настройки скрипта

Настройки необходимо указывать в файле проекта **settings.json**, который имеет следующую структуру:

- ПараметрыОбновления  
  - ИмяПользователяДляОбновлений - имя пользователя ИТС для доступа к обновлениям
  - ПарольПользователяДляОбновлений - пароль пользователя ИТС
  - КаталогВременныхФайлов - каталог для хранения временных фалов работы скрипта
  - КаталогФайловОбновления - каталог для сохранения обновлений, можно использовать общий каталог для обновлений 1С, tmplt
  - ТокенТелеграмБота - как и указано, токен телеграм бота для отправки уведомлений
  - ЧатыТелеграм - массив с идентификаторами чатов для уведомления
- БазыДляОбновления - массив с описанием баз для обновления
  - Идентификатор - имя базы
  - Включено - булево, отметка, котора позволяет включить или выключить базу из обработки
  - ЗавершениеРаботыПользователейПередОбновлением - булево, позволяет определить порядок запуска завершения пользователей.Имеет место быть при ограниченном количестве пользователських лицензий.
  - ИмяСервера1С - имя сервера 1С, для подключения
  - ИмяБазы - имя базы, для подключения
  - ИмяПользователяБазы - имя пользователя, для подключения
  - ПарольПользователяБазы - пароль пользователя, для подключения
  - ТипКонфигурации - строка, которая определяет тип конфы, участвует в построении ссылки для получения обновления Использовались пока "AccountingCorp" (Бухгалтерия 3 Корп) и "HRMCorp" (ЗУП 3 Корп)
  - ВерсияРелиза - версия релиза, используется для построения ссылки для получения обновления
  - ВерсияПлатформыСокр - версия платформы ("83"), используется для построения ссылки для получения обновления
  - ИмяКоннектора - имя, под которым зарегестрирован COM-коннектор для базы, например "V8.ComConnector"или  "V8.3.18.ComConnector", Используется для подключения
  - ВерсияПлатформы - пока не используется

```javascript
{
    "ПараметрыОбновления": 
    {   
        "ИмяПользователяДляОбновлений": "user",//ИТС
        "ПарольПользователяДляОбновлений": "pass",//ИТС
        "КаталогВременныхФайлов" : "D:\\tmp",
        "КаталогФайловОбновления" : "D:\\tmplt\\1c",
        "ТокенТелеграмБота" : "xxx:yyy",
        "ЧатыТелеграм" : [
            "zzzzz"
        ]
    },
    "БазыДляОбновления": [
        {
            "Идентификатор" : "Бухгалтерия",
            "Включено" : false,
            "ЗавершениеРаботыПользователейПередОбновлением" : false,
            "ИмяСервера1С":"server",
            "ИмяБазы":"buh",
            "ИмяПользователяБазы":"Admin",
            "ПарольПользователяБазы":"password",
            "ТипКонфигурации":"AccountingCorp",
            "ВерсияРелиза":"30",
            "ВерсияПлатформыСокр":"83",
            "ИмяКоннектора":"V8.ComConnector",//или к примеру V8.3.18.ComConnector
            "ВерсияПлатформы":"8.3.18.1208"
        },
        {
            "Идентификатор" : "ЗУП",
            "Включено" : false,
            "ЗавершениеРаботыПользователейПередОбновлением" : false,
            "ИмяСервера1С":"server",
            "ИмяБазы":"zup",
            "ИмяПользователяБазы":"Admin",
            "ПарольПользователяБазы":"password",
            "ТипКонфигурации":"HRMCorp",
            "ВерсияРелиза":"31",
            "ВерсияПлатформыСокр":"83",
            "ИмяКоннектора":"V8.ComConnector",//или к примеру V8.3.18.ComConnector
            "ВерсияПлатформы":"8.3.18.1208"
        }
    ]
}
```

## Запуск

На вход скрипт принимает один параметр - **полный путь к файлу настроек**.
Желательно указывать полный путь к скрипту и файлу настроек при запуске, т.к. идет запись лога в папку внутри папки скрипта.
Например

```bat
oscript "D:\Github\AutoUpdateIB\src\AutoUpdateRunner.os" "D:\Github\AutoUpdateIB\settings.json"
```
