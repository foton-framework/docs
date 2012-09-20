# SYS_Model_Database

<!--$this->act_params['group_id'] =& $this->group_id;-->
* **[$table](#table-parapm)**
* **[$fields](#fields-parapm)**
* **[$name](#name-parapm)**
* **[$add_action](#add_action-parapm)**
* [**init**()](#init)
* [**create_table**()](#create_table)
* [**init_form**($table = NULL)](#init_form)
* [**set_table**($table)](#set_table)
* [**table**()](#table)
* [**prepare_row_result**(&$row)](#prepare_row_result)
* [**get**($table = NULL)](#get)
* [**get_result**($table = NULL)](#get_result)
* [**get_row**($table = NULL)](#get_row)
* [**insert**($table = NULL, $data = NULL)](#insert)
* [**update**($table = NULL, $data = NULL)](#update)
* [**delete**($table = NULL, $data = NULL)](#delete)
* [**count_all**($table = NULL)](#count_all)



<a id="table-parapm"></a>
## $table

Имя таблицы в БД.

	public $table = 'news';


<a id="fields-parapm"></a>
## $fields

Массив с описанием всех полей в таблице. Первое поле должно быть ключем.
Правильней задавать поля в методе `init()`. Тогда можно задать некоторые значения с помощью функций или переменных ([см. пример](#example)).
Краткий пример:

	<?php
	$this->fields[$this->table]['status'] = array(
		'label'   => 'Статус',
		'default' => 1,
		'field'   => 'radiogroup',
		'options' => 'status_list',
		'user_group' => array(1),
		'rules' => 'trim|required'
	);
	
###[label]###
Название поля

###[default]###
Значение по умолчанию

###[field]###
Тип поля. Доступные варианты:

* `input` - `<input type="text">`
* `password` - `<input type="password">`
* `hidden` - `<input type="hidden">`
* `radio` - `<input type="radio">`
* `radiogroup` - список элементов `radio`
* `checkbox` - `<input type="checkbox">`
* `checkboxgroup` - список элементов `checkbox`
* `file` - `<input type="file">`
* `select` - `<select>…</select>`
* `multiselect` - `<select multiple="multiple">…</select>`
* `multicheckbox` - альтернатива `multiselect`. Список элементов `checkbox`
* `textarea` - `<textarea>…</textarea>`
* `html` - визуальный редактор CKEditor

Подробней см. файлы

* `framework/helpers/form.hlp.php`
* `framework/libraries/form.php` - метод `field()`


###[options]###
Массив возможных значений. Можно передать не только массив но и название метода который возвращает массив. Это актуально когда значения берутся из БД.

Необходимо указать для полей: 
`select`, `multiselect`, `multicheckbox`, `radiogroup`, `checkboxgroup`

###[user_group]###
ID группы пользователей кому разрешено редактирования данного поля

###[rules]###
Правила валидации и обработки переданного значения формы. Правила разделены символом `|`.

Список правил:

1. `required` - Поле обязательно для заполнения
* `matches[field_name]` - Значение должно совпадать со значением поля `field_name`
* `length[min,max]` - Ограничения дилны строки
* `min_length[min]` - Минимсльная длина строки
* `max_length[max]` - Максимальная длина строки
* `alpha` - Только латинсие буквы
* `alpha_numeric` - Только латинсие буквы и цифры
* `alpha_dash` - Только латинсие буквы, цифры и символы: `-` `_`
* `integer` - Только цифры
* `numeric` - То же что и `is_numeric()`. Т.е целые и числа с плавающей точкой
* `valid_email` - Строка соотв. e-mail адресу
* `valid_url` - Строка соотв. url
* `translit` - Функция транслитерирует кирилическую строку. Склеивая при этом слова символом `-`
* `translit[field_name]` - Тоже что и `translit` только значение берется из поля `field_name`
* `unique[table_name]` - Проверка на уникальность значения
* любая php-функция. Например `trim`
* `callback[…]` - Выполняет метод указанный в аргументе. Метод указывается в специальном формате. Примеры смотри ниже.
	* `callback[ext.user.password_hash]` - Вызывает метод `sys::$ext->user->password_hash($val)`
	* `callback[model.blogs.check_fiedl]` - Вызывает метод `sys::$model->blogs->check_fiedl($val)`




<a id="name-parapm"></a>
## $name
Название модели. Используется для модуля `admin`

<a id="add_action-parapm"></a>
## $add_action = true|false
Разрешить или запретить добавление элементов из модуля `admin` (через "волшебную палочку")

<a id="init"></a>
## init()
Метод инициализации. Вызывается сразу после создания объекта модели.

<a id="create_table"></a>
## create_table()
Вспомогательный метод для быстрого создания таблицы БД. Свойства полей берутся из массива `$fields` данной модели.

<a id="init_form"></a>
## init_form($table = NULL)
Вспомогательный метод инициализации библиотеки `form`.

Пример:
	
	<?php
	$this->model =& $this->load->model('user'); // Загружаем модель
	
	$this->model->init_form(); // Инициализируем "форму"
	
	// Если данные отправленны и корректны
	if ($this->form->validation())
	{
		$user_id = $this->model->insert(); // Добавляем нового пользователя
	}

<a id="set_table"></a>
## set_table($table)
Устанавливает название таблицы

<a id="table"></a>
## table()
Возвращает название таблицы

<a id="prepare_row_result"></a>
## prepare_row_result(&$row)
Метод постобработки данных возвращаемых моделью. Данный метод выполняется только при использовании функций: `$model->get_row()` и `$model->get_result()`.
([см. пример](#example))

<a id="get"></a>
## get($table = NULL)
Возвращает дескриптор результата запроса или FALSE в случае ошибки. Часто метод используется для предварительной настрпойки запроса к БД. ([см. пример](#example))

<a id="get_result"></a>
## get_result($table = NULL)
Возвращает массив объектов результата запроса.

<a id="get_row"></a>
## get_row($table = NULL)
Возвращает объект данных результата запроса.

<a id="insert"></a>
## insert($table = NULL, $data = NULL)
Добавление новой записи в БД. По умолчанию данные (`$data`) берутся из массива `$_POST`. В массив данных попадают только те значения которые указаны в `$model->fields`

<a id="update"></a>
## update($table = NULL, $data = NULL)
Обновление записи в БД. По умолчанию данные (`$data`) берутся из массива `$_POST`. В массив данных попадают только те значения которые указаны в `$model->fields`

<a id="delete"></a>
## delete($table = NULL, $data = NULL)
Удаление записи из БД.

<a id="count_all"></a>
## count_all($table = NULL)
Возвращает кол-во записей

<a id="example"></a>
## Шаблон стндартной модели

	<?php
	class MODEL_Bolezni extends SYS_Model_Database
	{
		//--------------------------------------------------------
		
		public $com_name = 'bolezni';
		public $com_url  = '/bolezni/';
	
		public $table = 'bolezni';
		public $name  = 'Болезни';
	
		public $add_action = TRUE;
		
		public $thumbs = array(
			'thumb' => array(
				'size' => array(100, 100),
				'dist' => 'files/bolezni/t_',
				'crop' => TRUE
			),
			'image' => array(
				'size' => array(200, 200),
				'dist' => 'files/bolezni/',
				'crop' => FALSE
			)
		);
		
		//--------------------------------------------------------
		
		public function init()
		{
			$this->fields[$this->table] = array(
				'id'     => NULL,
				'views'  => NULL,
				'uid'    => array(
					'default' => $this->user->id
				),
				'status' => array(
					'label'   => 'Статус',
					'default' => 1,
					'field'   => 'radiogroup',
					'options' => 'status_list',
					'user_group' => array(1)
				),
				'id_key' => array(
					'label' => 'SEO Url',
					'field' => 'input',
					'rules' => 'trim|alpha_dash|translit[title]|max_length[250]|unique['.$this->table.']'
				),
				'postdate' => array(
					'label'   => 'Дата публикации',
					'default' => time(),
				),
				'title' => array(
					'label'  => 'Заголовок',
					'field'  => 'input',
					'rules'  => 'trim|strip_tags|required|max_length[250]'
				),
				'body' => array(
					'label' => 'Текст статьи',
					'field' => 'html',
					'rules' => 'trim|required|min_length[50]',
				),
				'img' => array(
					'label'   => 'Фото',
					'field'   => 'file',
					'rules'   => 'callback[model.'.$this->com_name.'.upload,img]',	
				),
			);
		}
		
		//--------------------------------------------------------
		
		public function update_views($id)
		{
			$this->db->where('id=?', $id);
			$this->db->set('views=views+1');
			$this->update();
		}
		
		//--------------------------------------------------------
		
		public function &prepare_row_result(&$row)
		{
			$row = parent::prepare_row_result($row);
			
			$row->full_link = $this->com_url . ($row->id_key ? $row->id_key : $row->id) . '/';
			
			if (isset($row->img))
			{
				foreach ($this->thumbs as $key => $thumb) $row->$key = '/' . $thumb['dist'] . $row->img;
			}
			
			return $row;
		}
		
		//--------------------------------------------------------
		
		public function get($table = NULL, $where = NULL)
		{
			if ($this->user->group_id != 1)
			{
				$this->db->where($this->table . '.status=1');
			}
			
			return parent::get($table, $where);
		}
		
		//--------------------------------------------------------
		
		public function status_list($val = NULL)
		{
			static $list = array(
				0 => 'Отключен',
				1 => 'Включен'
			);
			
			if ($val !== NULL) return $list[$val];
			
			return $list;
		}
		
		//--------------------------------------------------------
		
		public function upload($value, $callback, $field)
		{
			$key_value = $this->form->value('id');
			
			if ( ! $value) return TRUE;
			
			$this->load->library('upload');
			if ($key_value) $this->load->library('image');
	
			$this->upload->set_allowed_types('jpg');
			$this->upload->set_max_size(3);
			
			
			if ($result = $this->upload->run($field))
			{			
				if ($key_value)
				{
					$file_name = $key_value . '.jpg';
					$this->image->set_file_name($file_name);
					$this->image->process($result->full_path, $this->thumbs);
					return $file_name;
				}
					
				return TRUE;
			}
	
			$this->form->set_error_message($callback, $this->upload->error($field));
			return FALSE;
		}
		
		//--------------------------------------------------------
		
		public function insert($table = NULL, $data = NULL)
		{
			if ( ! $data)
			{
				$data = $_POST;
			}
	
			$id = parent::insert($table, $data);
			
			if ($data['img'])
			{
				$this->load->library('image');
				$file_name = $id . '.jpg';
				$this->image->set_file_name($file_name);
				$this->image->process($data['img'], $this->thumbs);
	
				$this->db->where('id=?', $id);
				$this->update(NULL, array('img'=>$file_name));
			}
			
			return $id;
		}
		
		//--------------------------------------------------------
	}