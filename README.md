<h1 align="center">🚀 VKAPI</h1>

**VKAPI** - библиотека для упрощённой работы с API ВКонтакте на PHP 7.4

## Установка

```
composer require krypt0nn/vkapi
```

Для ручной установки необходимо распаковать библиотеку в удобное вам место и подключить файл ``VKAPI.php``

## Примеры работы

### Авторизация

```php
<?php

use VKAPI\VK;

$API = new VK ('логин', 'пароль');

print_r ($API->users->get ([
    'user_ids' => 1
]));

print_r ($API->users->get ()); // Сам себя~
```

### Авторизация с поддержкой 2ФА

```php
<?php

use VKAPI\VK;

$API = new VK ('логин', 'пароль', function ()
{
    // Читаем и возвращаем 2ФА код, который ввёл пользователь (в консоль)
    return readline ('2fa code: ');
});
```

### Авторизация с токеном доступа

```php
<?php

use VKAPI\VK;

$API = new VK ('токен доступа');
```

### LongPoll API

```php
<?php

namespace VKAPI;

$API      = new VK ('токен доступа');
$longpoll = new LongPoll ($API);

# Бесконечно принимаем запросы от LongPoll API
while (true)
    if (sizeof ($updates = $longpoll->getUpdates ()) > 0)
        print_r ($updates);
```

*Поддерживается работа как с пользователями, так и с сообществами**

### Чат бот

```php
<?php

namespace VKAPI;

$API      = new VK ('токен доступа');
$longpoll = new LongPoll ($API);

# Создаём объект реализации чат бота
$bot = new Bot ($longpoll, function ($message)
{
    echo $message['from_id'] .' | '. $message['text'] . PHP_EOL;
});

# И в бесконечном цикле обновляем его
while (true)
    $bot->update ();
```

*Поддерживается работа как с пользователями, так и с сообществами**

## Функционал сообществ

### Callback API

```php
<?php

namespace VKAPI;

$vk = new VK ('токен сообщества');
$callback = new Callback ($vk, 'строка подтверждения');

# Задаём обработчик события на запрос типа "message_new"
$callback->on ('message_new', function ($params) use ($vk)
{
    $vk->messages->send ([
        'message' => 'Привет! Мне показалось что ты сказал "'. $params['message']['text'] .'"',
        'peer_id' => $params['message']['from_id']
    ]);
});

# Выполняем обработку запроса к callback API
$callback->process ();
```

### Клавиатура

```php
<?php

namespace VKAPI;

use VKAPI\Buttons\Text;

# true - сделать ли клавиатуру встроенной в сообщение (inline клавиатура)
$keyboard = new Keyboard (new VK ('токен сообщества'), true);

# 0 - первый ряд
$keyboard->buttons->add (0, new Text ('Hello, World!'));

# 1 - второй ряд
$keyboard->buttons->add (1, (new Text ('Yes'))->setColor ('positive'));
$keyboard->buttons->add (1, (new Text ('No'))->setColor ('negative'));

// []   - дополнительные параметры message.send
// true - клавиатура будет отображена всего 1 раз
$keyboard->send ('peer id', 'Тесто', [], true);
```

### Карусель

```php
<?php

namespace VKAPI;

use VKAPI\Carousel\Text;

$carousel = new Carousel (new VK ('токен сообщества'));

# Создаём элемент для карусели
$element = new Text ('Привет, Мир!', 'Тестовый элемент карусели');
$element->buttons->add (new Buttons\Text ('Я просто кнопка~~'));

# Добавляем элемент в карусель
$caruosel->add ($element);

# Отправка карусели. Указать peer id получателя и сообщение для отправки
$carousel->send ('peer id', 'Привет! Я тут карусель сделал, не посмотришь?');
```

Для подробной документации рекомендуется обратиться к комментариям в коде

Автор: [Подвирный Никита](https://vk.com/technomindlp). Специально для [Enfesto Studio Group](https://vk.com/hphp_convertation)