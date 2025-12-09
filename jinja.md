# Jinja2: Передача данных, циклы и условия

Эта документация описывает, как передавать данные из Python в HTML-шаблоны с помощью шаблонизатора Jinja2. Рассмотрены три ключевых сценария:

- работа с простыми переменными (строки, числа),
- работа со списками объектов (циклы for),
- обработка неполных данных (условия if).

## Передача простых переменных
Самый базовый способ использовать Jinja2 — подставить отдельные значения в шаблон: заголовок страницы, имя пользователя, общее количество товаров и т.п.

Python-код (main.py)
```python
from jinja2 import Environment, FileSystemLoader

env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('simple.html')

# Передаём отдельные переменные
page_title = "Лучшие кроссовки 2025"
total_count = 42
is_premium = True

rendered_page = template.render(
    title=page_title,
    count=total_count,
    premium=is_premium
)

with open('simple.html', 'w', encoding='utf-8') as f:
    f.write(rendered_page)
```
Шаблон (template.html)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>{{ title }}</title>
</head>
<body>
    <h1>{{ title }}</h1>
    <p>В каталоге: {{ count }} моделей кроссовок.</p>
    
    {% if premium %}
        <p style="color: gold;">⭐ Вы просматриваете премиум-каталог!</p>
    {% endif %}
</body>
</html>
```

### Что происходит:

- Значения title, count, premium передаются как отдельные именованные аргументы в render().
- В шаблоне они подставляются через двойные фигурные скобки: {{ title }}.
- Логическое значение premium используется в условии if.


## Передача списков объектов при помощи цикла for

Для отображения множества однотипных элементов (товаров, статей, комментариев) используется список словарей.

```python
sneakers = [
    {"brand": "Nike", "model": "Air Max", "price": 150, "image": "nike.jpg"},
    {"brand": "Adidas", "model": "Ultraboost", "price": 180, "image": ""},
    {"brand": "Puma", "model": "RS-X", "price": 120, "image": "puma.jpg"}
]

rendered = template.render(sneakers=sneakers)
```
В HTML-шаблоне (template.html) используйте конструкцию {% for ... %} для итерации по списку:
Шаблон с циклом в html

```html
<h2>Каталог</h2>
{% for item in sneakers %}
  <div class="sneaker">
    <h3>{{ item.brand }} {{ item.model }}</h3>
    <p>Цена: {{ item.price }} $</p>
  </div>
{% endfor %}
```
### Особенности:
- Имя переменной в цикле (item) выбираете сами.
- Доступ к полям — через точку: {{ item.price }}.
- Цикл автоматически завершится после последнего элемента, место завершения цикла определяется написанием стуктуры {% endfor %}.

##  Условия if: гибкость при неполных данных
Часто данные не идеальны: поля могут быть пустыми, отсутствовать или иметь специальные значения. Jinja2 позволяет адаптировать отображение под такие случаи.

### Как интерпретируются значения в условиях:

В Jinja2 ложными (False) считаются:

- Пустая строка ("")
- None
- Ноль (0)
- Пустой список ([])
- Пустой словарь ({})

Все остальные значения — истинные (True).

### Пример: 

Файл: main.py

```python
from jinja2 import Environment, FileSystemLoader

# Настройка шаблонов
env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('catalog.html')

# Данные
sneakers = [
    {"title": "Nike Air Max", "price": "$150", "image": "nike.jpg"},
    {"title": "Adidas Ultraboost", "price": "$180", "image": ""},
    {"title": "New Balance 574", "price": "$130", "image": "nb.jpg"}
]

# Рендеринг
output = template.render(sneakers=sneakers)

# Сохранение
with open('output.html', 'w', encoding='utf-8') as f:
    f.write(output)
```

Файл: templates/catalog.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Каталог кроссовок</title>
</head>
<body>
    <h1>Наши кроссовки</h1>
    
    {% for sneaker in sneakers %}
    <div class="sneaker-item">
        <h2>{{ sneaker.title }}</h2>
        <p><strong>Цена:</strong> {{ sneaker.price }}</p>
        {% if sneaker.image %}
            <img src="{{ sneaker.image }}" width="150">
        {% else %}
            <em>Изображение временно недоступно</em>
        {% endif %}
    </div>
    <hr>
    {% endfor %}
</body>
</html>
```

## Краткие выводы

- Передавайте данные в шаблон в виде списков словарей, а не отдельных переменных.
- Используйте {% for item in list %}...{% endfor %} для генерации повторяющихся блоков.
- Применяйте {% if variable %}...{% else %}...{% endif %} для обработки отсутствующих или условных данных.
- Вся логика подготовки данных должна быть в Python; шаблон отвечает только за отображение.

Эти принципы лежат в основе эффективной работы с Jinja2 в веб-разработке, генерации отчётов, электронных писем и других задач.
