# JavaScript про API браузеров (семинары)

## Урок 3. Сетевые запросы

### Домашнее задание

#### Цель:

Разработать веб-приложение, которое каждый день будет отображать новое случайное изображение из коллекции Unsplash, давая пользователю возможность узнать больше о фотографе и сделать "лайк" изображению.

#### Регистрация на Unsplash:

- Перейдите на веб-сайт Unsplash (https://unsplash.com/).

- Зарегистрируйтесь или войдите в свой аккаунт. (если у вас не было регистрации до этого, новый аккаунт создавать не нужно).

#### Создание приложения:

- Перейдите на страницу разработчика Unsplash (https://unsplash.com/developers).
- Нажмите "New Application".
- Заполните необходимую информацию о приложении (можете использовать http://localhost для тестирования).
- Получите свой API-ключ после создания приложения.

#### Разработка веб-приложения:

- Создайте HTML-страницу с элементами: изображение, имя фотографа, кнопка "лайк" и счетчик лайков.
- Используя JavaScript и ваш API-ключ, получите случайное изображение из Unsplash каждый раз, когда пользователь загружает страницу.
- Отобразите информацию о фотографе под изображением.
- Реализуйте функционал "лайка". Каждый раз, когда пользователь нажимает кнопку "лайк", счетчик должен увеличиваться на единицу.

#### \* Дополнительные задачи (по желанию):

- Добавьте функцию сохранения количества лайков в локальное хранилище, чтобы при новой загрузке страницы счетчик не сбрасывался.
- Реализуйте возможность просмотра предыдущих "фото дня" с сохранением их в истории просмотров.

---

### Решение

При первом старте приложения создаем в локальном хранилище две записи:

1. Дата начала предыдущего воскресенья
2. Структуру `images` — массив, содержащий семь объектов (по количеству дней недели) вида:

```js
container.url = regular;
container.user = name;
container.localLike = 0;
```

3. Запрашиваем `unsplash`:

```js
const url = `https://api.unsplash.com/photos/random?client_id=${CLIENT_ID}&count=${maxElement}&query=game-wallpaper`;
```

подставив в строку `CLIENT_ID` — ключ, полученный в разделе developers, `maxElement` количество иллюстраций (к конкретном случае — 7, по дням недели), `game-wallpaper` — тематику иллюстраций.

4. Получив ответ и распарсив его, наполняем `images` так, чтобы `container.url` содержал ссылку на иллюстрацию, а `container.user` — имя ее автора. Поле `container.localLike` при инициализации оставляем равным нулю.

При втором и последующих стартах — проверяем, не прошла ли неделя с загрузки информации с `unsplash`. Если это так — считаем что это первый запуск, и повторяем инициализацию, описанную выше.

Файл `init.js` отработал. В локальном хранилище две записи:

```js
localStorage.setItem("startDate", JSON.stringify(sunday));
localStorage.setItem("photoData", JSON.stringify(images));
```

Файл `homework.js`: непосредственно слайдер.
Блок слайдера содержит три кадра с иллюстрациями. При инициализации — слева направо будут выставлены кадры предыдущего дня, сегодняшнего и завтрашнего, с соответствующей информацией из массива `images`. Для сегодняшнего числа к информации о дне недели добавляем фразу `Привет, сегодня…`.
Добавляем слушатель событий ко всей конструкции `.carousel`. В зависимости от объекта, по которому был сделан клик — вызываем одно- или многократную анимацию смены слайдов в соответствующую сторону, или увеличиваем счетчик лайков в поле центрального фрейма:

```js
// Modify likes quantity, update screen, re-set localstorage
function likeClick(event) {
  images[activeFrame].localLike++;
  event.target.innerHTML = `&#10084;${images[activeFrame].localLike}`;
  localStorage.setItem("photoData", JSON.stringify(images));
}
```

Анимация слайдера: в зависимости от направления движения фреймов с иллюстрациями, изменияем при необходимости направление выравнивания кадров внутри слайдера, добавляем новый фрейм, после чего противоположный фрейм последовательно за время `timeStep` уменьшаем по ширине до нуля и удаляем.
