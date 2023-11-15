Это групповой проект. Группа состояла из 5 человек. Внутри команда должна была разделить между собой задания 
самомстоятельно и выбрать базовый проект, который каждый член группы разрабатывал самостоятельный до этого момента.
Мой базовый проект находится [здесь](https://github.com/AmirKhus/java-filmorate)

Функционал который был добавлен.

1. Функциональность «Отзывы».
2. Функциональность «Поиск».
3. Функциональность «Общие фильмы».
4. Функциональность «Рекомендации».
5. Функциональность «Лента событий».

Я выбрал реализовать функционал «Отзывы» и отриосвать конечную er-диаграмму.

В ходе написания кода:
* Придерживался дизайн проектирования исходного кода
* Не вносились правки в исходный текс, а благодаря паттернам поведения только дорабатывался
* 
### Описание задачи

В приложении должны появиться отзывы на фильмы. Добавленные отзывы должны иметь рейтинг и несколько дополнительных характеристик.

Характеристики отзыва.

1. Оценка — полезно/бесполезно.
2. Тип отзыва — негативный/положительный.

Рейтинг отзыва.

У отзыва имеется рейтинг. При создании отзыва рейтинг равен нулю. Если пользователь оценил отзыв как полезный, это увеличивает его рейтинг на 1. Если как бесполезный, то уменьшает на 1.

Отзывы должны сортироваться по рейтингу полезности.

### API

`POST /reviews`

Добавление нового отзыва.

`PUT /reviews`

Редактирование уже имеющегося отзыва.

`DELETE /reviews/{id}`

Удаление уже имеющегося отзыва.

`GET /reviews/{id}`

Получение отзыва по идентификатору.

`GET /reviews?filmId={filmId}&count={count}`
Получение всех отзывов по идентификатору фильма, если фильм не указан то все. Если кол-во не указано то 10.

- `PUT /reviews/{id}/like/{userId}`  — пользователь ставит лайк отзыву.
- `PUT /reviews/{id}/dislike/{userId}`  — пользователь ставит дизлайк отзыву.
- `DELETE /reviews/{id}/like/{userId}`  — пользователь удаляет лайк/дизлайк отзыву.
- `DELETE /reviews/{id}/dislike/{userId}`  — пользователь удаляет дизлайк отзыву.


# DB structure

![DB_structure.png](DB%20structure%2FDB_structure.png)

# ***Примеры запросов***
<details>
  <summary><h3>Для пользователей:</h3></summary>

* Получение друзей

``` SQL
  SELECT *
  FROM friendship
  WHERE user_id = 3 
  AND state_of_friendship = true;
```
* Получение общих друзей у двух пользователей 
```` SQL
  SELECT * FROM users AS us
  JOIN FRIENDSHIP AS fr1 ON us.user_id = fr1.friend_id
  JOIN FRIENDSHIP AS fr2 ON us.user_id = fr2.friend_id
  WHERE fr1.user_id = ? AND fr2.user_id = ?;
 ```` 
* создание пользователя
```SQL
INSERT INTO users (email, login, name, birthday)
VALUES ( ?, ?, ?, ? );
```
* редактирование пользователя
```SQL
UPDATE users
SET email = ?,
    login = ?,
    name = ?,
    birthday = ?
WHERE user_id = ?
```
* получение списка всех пользователей
```SQL
SELECT *
FROM users
```

</details>

<details>
  <summary><h3>Для фильмов:</h3></summary>

* получение списка 10 популярных фильмов
````SQL
  SELECT f.film_id, f.film_name, f.description, f.release_date, f.duration,r.mpa_id, r.mpa_name
  FROM films AS f
  JOIN rating AS r ON f.mpa_id = r.mpa_id
  LEFT JOIN FILM_LIKE AS l ON f.film_id = l.film_id
  GROUP BY f.film_id
  ORDER BY COUNT(l.user_id) DESC
  LIMIT 10;
````

* создание фильма
```SQL
INSERT INTO films (name, description, release_date, duration_in_minutes, mpa_rating_id)
VALUES (?, ?, ?, ?, ?)
```
* редактирование фильма
```SQL
UPDATE films
SET name = ?,
    description = ?,
    release_date = ?,
    duration_in_minutes = ?,
    mpa_rating_id = ?
WHERE film_id = ?
```
* получение списка всех фильмов
```SQL
SELECT films.*, mpa_rating.mpa_name, COUNT(film_likes.user_id) AS rate
FROM films
LEFT JOIN mpa_rating ON films.mpa_rating_id = mpa_rating.mpa_rating_id
LEFT JOIN film_likes ON films.film_id = film_likes.film_id
GROUP BY films.film_id
ORDER BY films.film_id
```
</details>
