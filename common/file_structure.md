# Файловая структура

* **[application](#application)**
* **[components](#components)**
* **[extensions](#extensions)**
* **[framework](#framework)**
* **[templates](#templates)**
* [.FF_DEBUG](#ff_debug)
* [.FF_DEVMODE](#ff_devmode)
* [index.php](#index_php)

<a id="application"></a>
## [application]

Папка приложения (сайта). По идее должна также содержать подпапки `components` и `templates`, но я для удобства вынес их в корень сайта.


### [configs]

Содержит файлы настроек сайта, библиотек и расширений.
Обязательным файлом является `config.php` в нем содержатся основные настройки движка


### [libraries]

Содержит файлы пользовательских библиотек и расширение классов системных библиотек.

#### Пример расширения системной библиотеки
Допустим нам нужно добавить метод `r_cyrillic()` в библиотеку `Form`. Для этого создадим файл `application/libraries/form.php`
Со следующим кодом:
	
	<?php
	class Form extends SYS_Form
	{
		public function r_cyrillic($val)
		{
			return preg_match('@^[а-я]+$@iu', $val);
		}
	}

После чего станет возможным выполнить следующий код:

	<?php
	$this->load->library('form');
	echo $this->form->r_cyrillic('тест');

<a id="components"></a>
## [components]

Содержит компоненты (основные строительные элементы сайта) сайта.

<a id="extensions"></a>
## [extensions]

Содержит расширения (модули) сайта.

<a id="framework"></a>
## [framework]

Файлы движка.

### [core]

Основные файлы ядра

#### `constants.php`
Файл содержит константы используемые в движке

#### `run.php`
Стартовый файл движка. Самый первый исполняемый файл.

#### `sys.php`
Статический глобальный класс, является ядром всего движка. Содержит все объекты всех загруженных библиотек, моделей, компонентов и т.д. Подробнее будет описано в API

### [helpers]
Файлы статических классов (помошников). Фактически это набор вспомогательных функций объедененных в классе.

Помошники загружаются автоматически при первом обращении к ним.

К примеру:

	<?=h_form::open() ?>


### [libraries]

Файлы библиотек ядра.


<a id="templates"></a>
## [templates]

Папка с шаблонами сайта. За вывод шаблонов отвечает библиотека `Template`.

<a id="ff_debug"></a>
## .FF_DEBUG
Если данный файл имеется в корне сйата то константа `FF_DEBUG` будет иметь значение `TRUE`.

Используется для включения и выключения отладки сайта.

Используется в `framework/core/run.php`

<a id="ff_devmode"></a>
## .FF_DEVMODE
Если данный файл имеется в корне сйата то константа `FF_DEVMODE` будет иметь значение `TRUE`

Используется для конфигурации копии разработчика. Например для подключения к другой БД.

Используется в `application/configs/config.php`

<a id="index_php"></a>
## index.php

То же что и `framework/core/run.php`.

Можно обойтись и без него. Для этого в `.htaccess` вместо `index.php` нужно указать путь к `run.php`