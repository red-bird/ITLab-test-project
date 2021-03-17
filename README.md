# *ITLab test project*
## Ссылки
[Техническое задание](https://github.com/RTUITLab/Recruit)

[ТЗ для backend'a](https://github.com/RTUITLab/Recruit/blob/master/requirements/back/README.md)

#### Сервисы:

* [Discovery](https://github.com/red-bird/EurekaServer)
* [Config](https://github.com/red-bird/ConfigServer)
* [Api gateway](https://github.com/red-bird/api-gateway)
* [Покупки](https://github.com/red-bird/PurchaseServer)
* [Магазины](https://github.com/red-bird/shops-service)
* [Заводы](https://github.com/red-bird/factory-service)

## Чем я пользовался
* OS: Windows 10 Pro
* SDK: openJDK 15 (хотя проекты и на других версиях работали)
* Docker Desktop
* IntelliJ Idea
* Git Kraken
* Github (Неожиданно)
* Postman

## Что это вообще?
Проект представляет собой набор сервисов имитирующих работу интернет магазина, который хранит информацию о имеющихся товарах,
принимает поставки с заводов, а также предоставляет саму возможность покупки.

## А поподробнее?
Вот картинка, которую я ни разу не "одолжил".
![image](https://spring.io/images/diagram-microservices-dark-4a2e5817aac093437f4f3b3a5be8be88.svg)
В реальности конечно не так все круто, но тоже неплохо, как я уже говорил, имеется 3 сервиса
* Сервис "Покупки"
* Сервис "Магазин"
* Сервис "Завод"

Сразу замечу, весь проект написан с помощью [фреймворка spring boot](https://spring.io/projects/spring-boot), 
а также инструментов [spring cloud](https://spring.io/projects/spring-cloud).

Каждый сервис имеет доступ к своей базе данных (в моем случае [postgresql](https://hub.docker.com/_/postgres)), все они 
регистрируются у *Discovery server* (в моем случае [Netflix Eureka](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-server)), 
а она как и все получает свою конфигурацию у [Config server](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-config-server).

Целых три сервиса, пока конечно терпимо, но что дальше? Не запоминать же все порты. Данную проблему решает 
[api gateway](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-gateway),
с помощью него мы имеем доступ ко всем сервисам по одному порту, о как удобно.

Существует механизм "прерывания схемы" с помощью [resilience4J](https://docs.spring.io/spring-cloud-circuitbreaker/docs/current/reference/html/),
 который повышает надежность системы, защищая он переполнения пула запросов.
  
  Также механизм отправления запросов от сервиса к сервису
 осуществлен с помощью [OpenFeign](https://spring.io/projects/spring-cloud-openfeign) (Декларативный Rest клиент).
 
 Была предпринята попытка отправлять логику на [elk stack](https://www.elastic.co/what-is/elk-stack), но не вышло/времени не хватило

## А как поднять?
![Docker](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Docker_%28container_engine%29_logo.svg/220px-Docker_%28container_engine%29_logo.svg.png)

А как договаривались в тех. задании, с помощью инструмента [docker-compose](https://docs.docker.com/compose/),
 он входит также в [docker desktop](https://www.docker.com/products/docker-desktop). Все образы загружены на [мой профиль
 docker hub](https://hub.docker.com/u/redbird127), а файл *docker-compose.yml* со всеми настройками лежит в этом репозитории.
 
 Поднимается все с помощью двух команд:
 
 `docker-compose pull`
 
 `docker-compose up -d`
 
 Запуск займет достаточно времени, и прямо после запуска api-gateway не будет принимать запросы, ему нужно время сориентироваться.
 
 Можно попробовать поменять настройки в yml файле, например, чтобы открыть другие порты, но только осторожно.
 
 ## А пользоваться как?
 Я мог бы тут, конечно, описать что да как, но за меня это сделает [open api 3.0](https://swagger.io/blog/news/whats-new-in-openapi-3-0/)
 , он же swagger.
 
 К сожалению, по какой-то неведомой мне причине получить ui страницу с документацией через api gateway не предоставляется возможным,
 поэтому я приложу пути к документации:
  * Для Сервиса "Покупки" - *http://localhost:8081/swagger-ui.html*
  * Для Сервиса "Магазин" - *http://localhost:8082/swagger-ui.html*
  * Для Сервиса "Заводы" - *http://localhost:8083/swagger-ui.html*
  
  Если поменяли порты у сервисов, то тут тоже меняются.
  
 ### api gateway
 По умолчанию путь к api gateway это:
 *http://localhost:8080*
 
 Чтобы обратиться к конкретному сервису, в адресную строку добавляется `/api/{название сервиса}` и получается:
 
 `http://localhost:8080/api/{название сервиса}`
 
 Названия всех сервисов для адресной строки:
 * `purchase/` - сервис "Покупки"
 * `shops/` - сервис "Магазины"
 * `factory/` - сервис "Заводы"
 
 А дальше как в документации описано, но приведу пример:
 
 `http://localhost:8080/api/shops/shops` - с помощью GET запроса получаем список всех магазинов.
 
 ## А тесты?
 А тесты лежат в репозитории, экспортировал коллекцию из [Postman'a](https://www.postman.com/), их нужно импортировать и готово.
 
 К сожалению [Karate-api](https://github.com/intuit/karate) у меня так и не заработал, тестировал на всех jdk 1.8, 
 openjdk 10,11,12,15
 
 Юнит тесты были.. но их слишком долго писать
 
 ## На этом все, держите капибару
 ![Капибара](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fkapibara.com.ua%2Fimage%2Fcatalog%2Fverstka%2Fvodosvinka-ili-kapibara-900x500.jpg&f=1&nofb=1)
