# Компоненты

## Использование компонентов

Компоненты используются на страницах и внутри других компонентов. Разметка компонентов генерируется функцией `b`.   
Она принимает три параметра:

* Имя компонента
* Массив параметров, по умолчанию пуст.
* Функция выводит разметку. Если третим параметром передать `true`, то разметка не выведется, а вернётся из функции в виде строки.

Два основных способа использования:

* Нестандартный тег `th-b`. `data-b` — тип компонента, `data-param` — объект с настройками. Внутренности тега окажутся значением поля `blockContent` компонента.

```html
<th-b data-b="counter" data-param='{
	"value": "12"
}'>Содержимое</th-b>
```

* Вызов функции `b` напрямую. Позволяет дополнительно обрабатывать разметку компонента перед выводом.

```php
<?=b('counter', array(
	"value": "12"
))?>
```


## Создание компонентов

```php
<?php

// Компонент реализуется с помощью обычной функции на ПХП.
// `$param` — массив параметров компонента
function TextCounter($param=array(''))
{
	
	// компонент должен уметь работать и без переданных параметров
	$defaults = array(

		// эти свойства желательны для всех
		'tag' => 'div',
		'mod' => '',
		'attr' => '',

		// свойство компонента
		'value' => '',

		// может быть несколько типов одного компонента
		'type' => 'main',

		// свойство, в которое передаётся
		// содержимое нестандартного тега
		'blockContent' => 'Текст блока «TextCounter»'
	);

	// массивы с переданными и предустановленными
	// параметрами объединяются
	$options = array_merge($defaults, $param);

	// типы компонента оборачиваются в условия
	// разметку компонента можно выводить внутри функции
	// возвращать ничего не нужно
	if ($options['type'] === 'main') {?>

		<!-- так использовать переменные в разметке -->
		<<?=$options['tag']?> class="TextCounter <?=$options['mod']?>" <?=$options['attr']?>>

			<input type="text" class="TextCounter__input" value="<?=$options['value']?>">

			<span class="TextCounter__сontrols">
				<span class="TextCounter__сontrol TextCounter__сontrol--more" tabindex="0">Больше</span>
				<span class="TextCounter__сontrol TextCounter__сontrol--less" tabindex="0">Меньше</span>
			</span>

			<!-- внутри компонентов можно использовать другие компоненты -->
			<th-b data-b="subTextCounter" data-param='{
				"value": "12"
			}'></th-b>

			<!-- добавление содержимого по условию -->
			<?php if ($options['recalculate'] === true): ?>
				<button class="TextCounter__recalculate" type="submit">Пересчитать</button>
			<?php endif; ?>
						
			<!-- здесь выводится содержимое -->
			<!-- нестандартного тега компонента -->
			<!-- <th-b>blockContent</th-b> -->
			<?=$options['blockContent']?>
		</<?=$options['tag']?>>

	<?} elseif ($options['type'] === 'notMain') {

// разметку можно хранить в строке
// удобно заменять нужные части по заглушкам
// неудобства:
// нет подсветки кода
// сложно подменять содержимое
$html =	<<<EOL
	<%tag% class="TextCounter %mod%" %attr%>
		<input type="text" class="TextCounter__input" value="%value%">
		<span class="TextCounter__сontrols">
			<span class="TextCounter__сontrol TextCounter__сontrol--more" tabindex="0">Больше</span>
			<span class="TextCounter__сontrol TextCounter__сontrol--less" tabindex="0">Меньше</span>
		</span>
		%subBlock%
		%recalculate%
		%blockContent%
	</%tag%>
EOL;
		
		// разметку блока можно получать и функцией `b`
		$options['subBlock'] = b('subTextCounter', array(
			"value": 12
		), true);

		// подмена содержимого может происходить так
		if ($options['recalculate'] === true) {
			$options['recalculate'] = '<button class="TextCounter__recalculate" type="submit">Пересчитать</button>';
		}
		else {
			$options['recalculate'] = '';
		};

		// функция замены переменных
		foreach ($options as $key => $value) {
			if (gettype($value)==='string') {
				$html = str_replace('%'.$key.'%', $value, $html);
			};
		};

		// в этом случае функция должна возвращать разметку
		return $html;

	};

};

?>
```
