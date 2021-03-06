## Передача параметров в класс: массивом или через аргументы?

Одно из типичных ошибочных и отвратительнейших архитектурных решений — передавать параметры в класс в виде массива:

```php
class test 
{
    protected $view_count;
    protected $append_count;
    protected $trace;

    function addParams($params) 
    {
        $this->view_count = $params['view_count'];
        $this->append_count = $params['append_count'];
        $this->trace = $params['trace'];
    }

    // ...
}

$params = [
    'view_count' => 10,
    'append_count' => 15,
    'trace' => 'soft',
];

$test = new test();
$test->addParams($params);
```

Передавать параметры в класс в виде массива в 99% случаев — просто отвратительно. Причины следующие:


1. Придется знать/заучивать ключи массива.
2. Редактор кода не подскажет/покажет, что какие аргументы принимает метод.
3. Осложняется phpDoc документирование.
4. В принимающем методе будет "портянка" из проверок и валидаций каждого элемента массива параметров.

В итоге для разработчика интерфейс метода становится черным ящиком.

**Если класс подразумевает значения некоторых свойств, без определения которых функционирование объекта невозможно в полном объеме, то надо писать сеттеры и геттеры. На каждое свойство. Это хорошая и правильная практика. Сеттеры и геттеры позволяют получать и изменить свойства объекта в процессе исполнения программы.
Класс должен обладать внятным и прозрачным интерфейсом. Передача массива параметров — это антипод такого класса.**

Когда можно передавать массив с параметрами в функцию или метод? В очень незначительных случаях, когда пропуск того или иного элемента такого массива особо ни на что не повлияет. Или где разработчик не может предугадать, сколько параметров придёт при вызове метода.

Пример ниже демонстрирует применение обоих подходов. Класс Mail, осуществляющий отправку почты, имеет прозрачный интерфейс — для каждого свойства класса, без определения которого невозможна корректная отправка письма, определен set-метод. Но также и определен метод Mail::setData(), который принимает массив параметров — данных для шаблона письма. В данном случае это вполне объяснимо: класс Mail универсален и может отправлять письма с любыми шаблонами. Соответственно предугадать, какие данные нужны для шаблона письма невозможно. Поэтому, тут вполне уместно передавать массив с параметрами. Если в массиве параметров будет пропущен один из элементов, необходимых для генерации шаблона, то в худшем случае в теле письма не будет выведена часть информации.

```php
$sendmail = new Mail();
$sendmail->setTo($user->getEmail());
$sendmail->setFrom('info@server.com');
$sendmail->setReplyTo('noreply@server.com');
$sendmail->setHeader('Вам письмо');
$sendmail->setLang('ru');
$sendmail->setTemplate('RegistrationSendData.mail'));
$sendmail->setData(['user' => $user, 'date' => new Datetime()]);
$sendmail->send();
```
