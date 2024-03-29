# Проектирование сервиса для размещения и просмотра объявлений
Авито — сервис для размещения объявлений о продаже б/у или новых вещей, техники, авто и т.д., их просмотра, также позволяющий покупателю связаться с продавцом для обсуждения товара.

## Содержание
1. [Тема и целевая аудитория](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#1-%D1%82%D0%B5%D0%BC%D0%B0-%D0%B8-%D1%86%D0%B5%D0%BB%D0%B5%D0%B2%D0%B0%D1%8F-%D0%B0%D1%83%D0%B4%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D1%8F)
2. [Расчет нагрузки](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#2-%D1%80%D0%B0%D1%81%D1%87%D0%B5%D1%82-%D0%BD%D0%B0%D0%B3%D1%80%D1%83%D0%B7%D0%BA%D0%B8)
3. [Логическая схема](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#3-%D0%BB%D0%BE%D0%B3%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F-%D1%81%D1%85%D0%B5%D0%BC%D0%B0)
4. [Физическая схема](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#4-%D1%84%D0%B8%D0%B7%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F-%D1%81%D1%85%D0%B5%D0%BC%D0%B0)
5. [Технологии](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#5-%D1%82%D0%B5%D1%85%D0%BD%D0%BE%D0%BB%D0%BE%D0%B3%D0%B8%D0%B8)
6. [Схема проекта](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#6-%D1%81%D1%85%D0%B5%D0%BC%D0%B0-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B0)
7. [Список серверов](https://github.com/thelvv/Highload_Coursework/blob/main/README.md#7-%D1%81%D0%BF%D0%B8%D1%81%D0%BE%D0%BA-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%BE%D0%B2)

## 1. Тема и целевая аудитория
### Аналоги:
- Юла
- eBay 
- Авто.ру (более узкая - автомобильная тематика, но схожая модель)

### MVP
- Размещение объявлений;
- Просмотр объявлений;
- Поиск по объявлениям;
- Регистрация;
- Чат между покупателем и продавцом.

### Целевая аудитория
Ежемесячная аудитория сервиса составляет более 44.7 млн человек.  
*Источник: [Презентация Авито](https://www.avito.ru/b2b-hub/resources/files/%D0%9C%D0%B5%D0%B4%D0%B8%D0%B0%D0%BA%D0%B8%D1%82.pdf)*

Статистика распределения пользователей по странам за январь 2023 года:

| Страна      | Доля пользователей, % |
| ---         | :---:                 |
| Россия      | 95.12 	              |
| Украина     | 2.34                  |
| Нидерланды  | 0.34                  |
| Казахстан   | 0.32                  |
| Беларусь    | 0.27                  |
| Другие      | 1.62                  |

*Источник: [SimilarWeb](https://www.similarweb.com/ru/website/avito.ru/#traffic)*

## 2. Расчет нагрузки
### Продуктовые метрики  

Месячная аудитория - __более 44.7 млн млн человек__ [*(ист.)*](https://www.avito.ru/company)  
Дневная аудитория - __более 14.7 млн человек__ [*(ист.)*](https://www.avito.ru/b2b-hub/resources/files/Медиакит.pdf)  
Среднее количество просматриваемых страниц за визит - __примерно 11 стр.__ [*(ист.)*](https://www.similarweb.com/ru/website/avito.ru/#overview)  
При примерно __50 млн активных пользователях__ и более чем __140 млн активных объявлений__ [*(ист.)*](https://www.avito.ru/company) получаем, что среднее число объявлений на пользователя - __3 объявления__ (с округлением в большую сторону).  

Пусть длина объявления 1024 символа или 1024 байта = __1 Кб__; к объявлению можно приложить 10 фото по 1 Мб каждое, то есть __10 Мб на все фото__, также пусть все чаты, связанные с данным объявлением, занимают 3072 байт = __3 Кб__. Таким образом можем пренебречь текстовыми данными, а одно объявление будет занимать примерно __10 Мб__ хранилища.  
С учетом того, что каждый пользователь имеет в среднем 3 объявления, а также, что он может заполнить информацию о себе (возьмем 2 Мб), то один пользователь будет занимать __32 Мб__ хранилища.  


| Действие                      | Количество в день (на одного пользователя) |
|-------------------------------|:------------------------------------------:|
| Размещение объявлений         | 0.008                                      |
| Просмотр и поиск объявлений   | 6                                          |

### Технические метрики
Хранение текстовых данных:  
*[4 Кб * 3 объявления + 2 Мб (пользовательские данные)] * 50 млн пользователей ~ __96 Тб__*  
  
Динамика роста:  
*[4 Кб * 3 + 2 Мб] * 2.6 млн (примерный рост количества пользователей в квартал) ~ __4.96 Тб/квартал__*

Хранение изображений:  
*10 Мб * 50 млн пользователей * 3 объявления ~ __1500 Тб__*  
  
Динамика роста:  
*10 Мб * 2.6 млн (примерный рост количества пользователей в квартал) ~ __24.8 Тб/квартал__*

| Тип данных          | Размер, Тб |
|---------------------|:----------:|
| Текстовые данные    | 96         |
| Изображения         | 1500       |

Пиковое потребление в течении суток:  
*[2 (коэфф. пиковой нагрузки) * 1.5 (коэфф. пиковой нагрузки в течение суток) * 14.7 млн пользователей * 11 страниц * 10 Мб (одно объявление)] / 86400 сек (сутки) ~ 54.8 Гбайт/сек = __438.6 Гбит/сек__*  

Суммарный суточный трафик (текстовые данные):  
*14.7 млн пользователей * 11 страниц * 3 Кб (одно объявление) ~ 468.6 Гб ~ __0.47 Тб__*  

Суммарный суточный трафик (изображения):  
*14.7 млн пользователей * 11 страниц * 10 Мб (одно объявление) ~ __1542 Тб__*

| Метрика                                 | Значение       |
|-----------------------------------------|----------------|
| Пиковое потребление в течении суток     | 438.6 Гбит/сек |
| Суммарный суточный трафик (текст)       | 0.47 Тб        |
| Суммарный суточный трафик (изображения) | 1542 Тб        |

RPS для просмотра объявления:  
*[14.7 млн пользователей * 6 просмотров] / 86400 сек ~ __1020 RPS__*

RPS для поиска объявления:  
*[14.7 млн пользователей * 6 поисковых запросов] / 86400 сек ~ __1020 RPS__*

RPS для размещения объявления:  
*400 тыс. (новых объявлений в день, [ист.](https://www.avito.ru/company)) / 86400 сек ~ __5 RPS__*

RPS для отправки сообщения:  
*[400 тыс. объявлений * 5 чатов * 20 сообщений] / 86400 сек ~ __463 RPS__* 

RPS для регистрации:  
*7955.4 (пользователей регистрируется за сутки, [ист.](https://www.avito.ru/b2b-hub/resources/files/%D0%9C%D0%B5%D0%B4%D0%B8%D0%B0%D0%BA%D0%B8%D1%82.pdf)) / 86400 сек ~ __0.1 RPS__* 

| Действие               | RPS    | RPS с учетом суточного пика (х1.5) |
|------------------------|:------:|:----------------------------------:|
| Просмотр объявления    | 1020   | 1530                               |
| Поиск объявления       | 1020   | 1530                               |
| Размещения объявления  | 5      | 7.5                                |
| Отправка сообщения     | 463    | 694.5                              |
| Регистрация            | 0.1    | 0.15                               |
| Всего                  | 2508.1 | 3762.2                             |

## 3. Логическая схема  
![Логическая схема БД](https://github.com/thelvv/Highload_Coursework/assets/59176894/d3efd3e8-5840-4ebe-9606-63ac7fa13380)


## 4. Физическая схема  
### Размер таблиц  

### Шардирование
Имеет смысл разделение по двум факторам: занимаемый размер и частота обновления данных.  
Таким образом, таблицы __Пользоатели, Категории, Объявления (основа), Сессии__ будут как менее интенсивно изменяемые, и таблицы __Просмотры объявлений, Чаты, Сообщения__ будут как более интенсивно изменяемые.  
Разделение по объему имеет следующий характер:  
Таблицы __Объявления (обе части)__ будут разделены на категории: __Товары, Недвижимость, Работа и услуги, Транспорт, Другое__, также к тяжеловесным относится __хранилище фотографий__, в котором находятся фотографии пользователей и фотографии из объявлений. Данное хранилище будет реализовано с помощью S3.

### Репликация
Каждая база данных обеспечивается 3 репликами. В Master производится запросы на вставку. Чтение происходит из Slave баз. Данная конфигурация увеличит надежность хранения данных и обеспечит достаточную скорость доступа к ним.

### Схема
![Физическая схема БД](https://user-images.githubusercontent.com/59176894/235453031-07285509-9867-4c86-9f37-40daba086c93.jpg)

## 5. Технологии 
### Backend
Для реализации бэкенда используется язык программирования Golang и микросервисную архитектуру. Данный ЯП обеспечивает высокий уровень надежности и быстродействия при низком пороге входа и простоте. Микросервисная архитектура обеспечивает автономность и независимость, а также хорошую масштабируемость  
Взаимодействие микросервисов будет осуществляться с использованием протокола gRPC, данные будут передаваться в формате protobuf

### Frontend
В качестве основного фреймворка для написания фронтенда выбран React.js, так как он является одним из самых популярных и удобных решений на рынке  
Протокол связи между фронтендом и бэкендом - протокол https  
Формат передачи данных - json  

### Хранение данных
Для хранения __информации о сессиях__ выбрана NoSQL СУБД __Redis__, так как она является распространенным решением для данной задачи
Для хранения __информации о чатах и сообщениях, информации о пользователях__ выбрана NoSQL СУБД __Tarantool__, так как она аналогично является распространенным решением для данной задачи  
Для хранения __информации просмотрах__ выбрана NoSQL СУБД __Clickhouse__ c дополнительным контуром, обеспечивающим асинхронную запись и синхронное чтение  
Для обеспечения __асинхронной записи и синхронного чтения__ выбрана __Kafka__  так как она является распространенным решением для данной задачи  
Для хранения __основной информации__ выбран __PostgreSQL__, так как данная СУБД надежная и гибкая, а также имеет открытый исходный код  
Для хранения __изображений__ выбрано местное __S3-совместимое хранилище__ (например, VK cloud), так как датацентры и CDN расположены рядом с основной целевой аудиторией

## 6. Схема проекта
![Схема проекта](https://github.com/thelvv/Highload_Coursework/assets/59176894/434bf61b-bdc5-405f-b9f5-7d832c1be307)

Балансировка между микросервисами обеспечивается NGINX

## 7. Список серверов
### NGINX
Конфигурация c запасом обеспечивает обработку пиковых значений RPS. Для отказоустойчивости и надежности используется два сервера [*(ист.)*](https://www.nginx.com/blog/testing-the-performance-of-nginx-and-nginx-plus-web-servers/)

| Характеристика         | Значение      |
|------------------------|:-------------:|
| CPU                    | 64 ядра       |
| RAM                    | 128 Гб        |
| Storage                | 64 Гб         | 
| Network                | 2 * 40 Гбит/с |
| Количество             | 2             |

### Микросервисы Auth, Ads, Chat
Микросервисы, написанные на Golang обеспечивают высокую производительность при приемлемых техничских характеристиках. [*(ист.)*](https://habr.com/ru/articles/324818/)  
Для отказоустойчивости и надежности серверы имеют запас по характеристикам, а также дубируются  

| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 128 Гб    |
| Storage                | 64 Гб     | 
| Network                | 1 Гбит/с  |
| Количество             | 6         |

### PostgreSQL
Для хранения частей БД объявлений выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти  
Сервера продублированы для повышения отказоустойчивости

| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 2048 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 6         |

### Redis
Для хранения и получения данных сессий выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти  
Сервера продублированы для повышения отказоустойчивости

| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 2048 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 2         |

### Tarantool
Для хранения и получения данных о чатах и сообщениях выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти и получения данных сессий достаточно средней конфигурации сервера и большего объема RAM для хранения в нем информации о сессиях  
Аналогично сервера продублированы для повышения отказоустойчивости

| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 2048 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 4         |

### Clickhouse
Для хранения и получения данных о просмотрах выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти  
Аналогично сервера продублированы для повышения отказоустойчивости

| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 2048 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 2         |

### Сервер для взаимодействия между Clickhouse и Kafka
Для организации взаимодействия между Clickhouse и Kafka выбраны промышленные серверные конфигурации c увеличенным объемом оперативной памяти  
Аналогично сервера продублированы для повышения отказоустойчивости
| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 64 ядра   |
| RAM                    | 256 Гб    |
| Storage                | 1024 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 2         |

### Сервер для Kafka
Выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти  
Аналогично сервера продублированы для повышения отказоустойчивости
| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 1024 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 2         |

### Сервер для кэша данных объявлений 
Для обработки данных об объявлениях выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти  
Аналогично сервера продублированы для повышения отказоустойчивости
| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 2048 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 2         |

### Сервер для кэша данных пользователей 
Для обработки данных о пользователях выбраны промышленные серверные конфигурации c увеличенным объемом постоянной и оперативной памяти  
Аналогично сервера продублированы для повышения отказоустойчивости
| Характеристика         | Значение  |
|------------------------|:---------:|
| CPU                    | 128 ядер  |
| RAM                    | 256 Гб    |
| Storage                | 2048 Гб   | 
| Network                | 1 Гбит/с  |
| Количество             | 2         |

### Коммутатор NGINX
Конфигурация c запасом обеспечивает обработку пиковых значений RPS. Для отказоустойчивости и надежности используется два сервера
| Характеристика         | Значение      |
|------------------------|:-------------:|
| CPU                    | 64 ядра       |
| RAM                    | 128 Гб        |
| Storage                | 64 Гб         | 
| Network                | 2 * 40 Гбит/с |
| Количество             | 2             |
