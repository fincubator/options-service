# OPTIONS-SERVICE

**OPTIONS-SERVICE** является сервисом хранения настроек и мета-даты для [golos.io](https://golos.io).

За каждым пользователем закрепляется MongoDB-документ, хранящий в поле options объект настроек, где ключем, обычно,
является алиас микросервиса, а значением - произвольные данные, формат которых определяет сам микросервис.
Поддерживает запросы и с фронтенд-гейта, форсируя имя юзера, при этом передавать это имя в запросе не нужно.
Поддерживает множественный запрос - достаточно передать параметры в виде массива, на каждый из которых
будет произведен поиск и целевое действие.

API JSON-RPC:

 ```
 get:
   user: <string>       // Имя пользователя (не нужно в случае отправки с клиента)
   service: <string>    // Имя целевого сервиса (или типа мета-данных)
   path: <path_string>  // Путь до данных. 
                        // В случае пустой строки - вернет все настройки.
                        // В ином случае - попытается получить данные по пути, используя дот-нотацию
                        // Например notify.send.timeout укажет на то что необходимо найти ключ
                        // notify, внутри ключ send, а внутри него ключ timeout и вернуть значение,
                        // каким бы оно ни было.
                        // В случае если значение не найдено по любым причинам - результатом будет null.
   
 set:
   user: <string>       // Аналогично выше.
   service: <string>    // Аналогично выше
   path: <path_string>  // Аналогично выше, при отсутствии - создаст такой путь.
   data: <any>          // Любые данные для записи.
 ```

Возможные переменные окружения `ENV`:
   
  - `GLS_GATE_HOST` *(обязательно)* - адрес, который будет использован для входящих подключений связи микросервисов.
   Дефолтное значение при запуске без докера - `127.0.0.1`
  
  - `GLS_GATE_PORT` *(обязательно)* - адрес порта, который будет использован для входящих подключений связи микросервисов.
   Дефолтное значение при запуске без докера - `8080`
  
  - `GLS_METRICS_HOST` *(обязательно)* - адрес хоста для метрик StatsD.
   Дефолтное значение при запуске без докера - `127.0.0.1`
        
  - `GLS_METRICS_PORT` *(обязательно)* - адрес порта для метрик StatsD.
   Дефолтное значение при запуске без докера - `8125`
  
  - `GLS_MONGO_CONNECT` - строка подключения к базе MongoDB.  
   Дефолтное значение - `mongodb://mongo/admin`
  
  - `GLS_DAY_START` - время начала нового дня в часах относительно UTC, используется для таких вещей как валидация "1 пост в сутки".    
   Дефолтное значение - `3` (день начинается в 00:00 по Москве). 

Для запуска сервиса достаточно вызвать команду `docker-compose up` в корне проекта, предварительно указав необходимые `ENV` переменные.    
