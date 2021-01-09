# JavaRushCosmo
Задание на стажировку.
Нужно дописать приложение, которое ведет учет космических кораблей в далеком будущем (в 3019 году). Должны быть реализованы следующие возможности:
1. получать список всех существующих кораблей;
2. создавать новый корабль;
3. редактировать характеристики существующего корабля;
4. удалять корабль;
5. получать корабль по id;
6. получать отфильтрованный список кораблей в соответствии с переданными
фильтрами;
7. получать количество кораблей, которые соответствуют фильтрам.
Для этого необходимо реализовать REST API в соответствии с документацией. В проекте должна использоваться сущность Ship, которая имеет поля:
   Long id
String name
String planet ShipType shipType Date prodDate
Boolean isUsed
Integer crewSize Double rating
ID корабля
Название корабля (до 50 знаков включительно) Планета пребывания (до 50 знаков включительно) Тип корабля
Дата выпуска.
Диапазон значений года 2800..3019 включительно Использованный / новый
Количество членов экипажа. Диапазон значений 1..9999 включительно.
Рейтинг корабля. Используй математическое округление до сотых.
          
Double speed
Максимальная скорость корабля. Диапазон значений 0,01..0,99 включительно. Используй математическое округление до сотых.
    Также должна присутствовать бизнес-логика:
Перед сохранением корабля в базу данных (при добавлении нового или при апдейте характеристик существующего), должен высчитываться рейтинг корабля и сохраняться в БД. Рейтинг корабля рассчитывается по формуле:
𝑅= 80·𝑣·𝑘 , 𝑦0−𝑦1+1
где:
v — скорость корабля;
k — коэффициент, который равен 1 для нового корабля и 0,5 для использованного; y0 — текущий год (не забудь, что «сейчас» 3019 год);
y1 — год выпуска корабля.
 
В приложении используй технологии:
1. Maven (для сборки проекта);
2. Tomcat 9 (для запуска своего приложения); 3. Spring;
4. Spring Data JPA;
5. MySQL (база данных (БД)).
Вот ссылка на архив с полезными книгами, которые помогут тебе в решении тестового задания. Не нужно их все перечитать, используй как справочники.
Отправка готового задания на проверку.
Результат нужно выложить на GitHub в публичный репозиторий.
Перед этим убедись, что все тесты проходят. ВАЖНО: Файл pom.xml изменять нельзя!
На странице стажировки нажми кнопку «Отправить заявку», заполни все поля и нажми «Отправить заявку». Дождись результатов автоматической проверки задания (этот процесс занимает до 1 мин). В отдельных случаях может понадобиться ручная проверка задания, которая может длиться несколько дней.
Обрати внимание.
1. Если в запросе на создание корабля нет параметра “isUsed”, то считаем, что пришло значение “false”.
2. Параметры даты между фронтом и сервером передаются в миллисекундах (тип Long) начиная с 01.01.1970.
3. При обновлении или создании корабля игнорируем параметры “id” и “rating” из тела запроса.
4. Если параметр pageNumber не указан – нужно использовать значение 0.
5. Если параметр pageSize не указан – нужно использовать значение 3.
6. Не валидным считается id, если он: - не числовой
- не целое число
- не положительный
7. При передаче границ диапазонов (параметры с именами, которые начинаются на «min» или «max») границы нужно использовать включительно.
  
REST API.
Get ships list
   URL Method URL Params
/ships
GET
Optional:
         Data Params Success Response
name=String planet=String shipType=ShipType after=Long before=Long isUsed=Boolean minSpeed=Double maxSpeed=Double minCrewSize=Integer maxCrewSize=Integer minRating=Double maxRating=Double order=ShipOrder pageNumber=Integer pageSize=Integer
None
Code: 200 OK Content: [
{
“id”:[Long], “name”:[String], “planet”:[String], “shipType”:[ShipType], “prodDate”:[Long], “isUsed”:[Boolean], “speed”:[Double], “crewSize”:[Integer], “rating”:[Double]
},
... ]
Поиск по полям name и planet происходить по частичному соответствию. Например, если в БД есть корабль с именем «Левиафан», а параметр name задан как «иа» - такой корабль должен отображаться в результатах (Левиафан).
pageNumber – параметр, который отвечает за номер отображаемой страницы при использовании пейджинга. Нумерация начинается с нуля
pageSize – параметр, который отвечает за количество результатов на одной странице при пейджинге
          Notes
  
Get ships count
   URL Method URL Params
/ships/count
GET
Optional:
name=String planet=String shipType=ShipType after=Long before=Long isUsed=Boolean minSpeed=Double maxSpeed=Double minCrewSize=Integer maxCrewSize=Integer minRating=Double maxRating=Double
None
Code: 200 OK Content: Integer
         Data Params Success Response
Notes
      
Create ship
   URL Method URL Params Data Params
Success Response
/ships POST None {
“name”:[String],
“planet”:[String],
“shipType”:[ShipType],
“prodDate”:[Long],
“isUsed”:[Boolean], --optional, default=false “speed”:[Double],
“crewSize”:[Integer] }
Code: 200 OK Content: {
“id”:[Long], “name”:[String], “planet”:[String], “shipType”:[ShipType], “prodDate”:[Long], “isUsed”:[Boolean], “speed”:[Double], “crewSize”:[Integer], “rating”:[Double]
}
Мы не можем создать корабль, если:
- указаны не все параметры из Data Params (кроме isUsed); - длина значения параметра “name” или “planet” превышает размер соответствующего поля в БД (50 символов);
- значение параметра “name” или “planet” пустая строка;
- скорость или размер команды находятся вне заданных пределов;
- “prodDate”:[Long] < 0;
- год производства находятся вне заданных пределов.
В случае всего вышеперечисленного необходимо ответить ошибкой с кодом 400.
                   Notes
  
Get ship
   URL
Method
URL Params Data Params Success Response
/ships/{id} GET
id
None
Code: 200 OK Content: {
“id”:[Long], “name”:[String], “planet”:[String], “shipType”:[ShipType], “prodDate”:[Long], “isUsed”:[Boolean], “speed”:[Double], “crewSize”:[Integer], “rating”:[Double]
}
Если корабль не найден в БД, необходимо ответить ошибкой с кодом 404.
Если значение id не валидное, необходимо ответить ошибкой с кодом 400.
                Notes
  
Update ship
   URL Method URL Params Data Params
Success Response
/ships/{id} POST
id
{
         “name”:[String], “planet”:[String], “shipType”:[ShipType], --optional “prodDate”:[Long], --optional “isUsed”:[Boolean], --optional “speed”:[Double], --optional “crewSize”:[Integer] --optional
}
--optional --optional
          Notes
Code: 200 OK Content: {
“id”:[Long], “name”:[String], “planet”:[String], “shipType”:[ShipType], “prodDate”:[Long], “isUsed”:[Boolean], “speed”:[Double], “crewSize”:[Integer], “rating”:[Double]
}
Обновлять нужно только те поля, которые не null.
Если корабль не найден в БД, необходимо ответить ошибкой с кодом 404.
Если значение id не валидное, необходимо ответить ошибкой с кодом 400.
  
Delete ship
   URL
Method
URL Params Data Params Success Response Notes
/ships/{id} DELETE id
Code: 200 OK
Если корабль не найден в БД, необходимо ответить ошибкой с кодом 404.
Если значение id не валидное, необходимо ответить ошибкой с кодом 400.
               
