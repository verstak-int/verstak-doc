# Создание компонентов

```php
<?php

// Компонент реализуется с помощью обычной функции на ПХП.
// `$param` — массив параметров компонента
function Component($param=array(''))
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
		'blockContent' => 'Текст блока «Component»'
	);

	// массивы с переданными и предустановленными
	// параметрами объединяются
	$options = array_merge($defaults, $param);

	// типы компонента оборачиваются в условия
	// разметку компонента можно выводить внутри функции
	// возвращать ничего не нужно
	if ($options['type'] === 'main') {?>

		<!-- так использовать переменные в разметке -->
		<<?=$options['tag']?> class="Component <?=$options['mod']?>" <?=$options['attr']?>>

			<input type="text" class="Component__input" value="<?=$options['tag']?>">

			<span class="Component__сontrols">
				<span class="Component__сontrol Component__сontrol--more" tabindex="0">Больше</span>
				<span class="Component__сontrol Component__сontrol--less" tabindex="0">Меньше</span>
			</span>

			<!-- внутри компонентов можно использовать другие компоненты -->
			<th-b data-b="subComponent" data-param='{
				"value": "12"
			}'></th-b>

			<!-- добавление содержимого по условию -->
			<?php if ($options['recalculate'] === true): ?>
				<button class="Component__recalculate" type="submit">Пересчитать</button>
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
	<%tag% class="Component %mod%" %attr%>
		<input type="text" class="Component__input" value="%value%">
		<span class="Component__сontrols">
			<span class="Component__сontrol Component__сontrol--more" tabindex="0">Больше</span>
			<span class="Component__сontrol Component__сontrol--less" tabindex="0">Меньше</span>
		</span>
		%recalculate%
		%blockContent%
	</%tag%>
EOL;
		
		// подмена содержимого может происходить так
		if ($options['recalculate'] === true) {
			$options['recalculate'] = '<button class="Component__recalculate" type="submit">Пересчитать</button>';
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
