Пакет Фильтрации
------------
Данный реализует фильтрацию запросов по get параметрам для laravel

Установка
------------

### Install via Composer

Установить пакет можно добавив в ваш composer.json следующие строки

~~~
    "require": {
        "kiryanov/filter" : "*"
    },
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/Danila98/laravel-filter"
        }
    ]
~~~

Использование
------------

Ваша модель должна использовать трейт Filterable

~~~

use Kiryanov\Filter\Filter\Filterable;

~~~

Далее вы должны написать класс, раширяющий QueryFilter,
котором имена методов будут сответсвовать именам сортируемых полей (get параметрам)
Ниже приведен пример сортировки по int значению и массиву связанных моделей
~~~
class ColorFilter extends QueryFilter
{
    /**
     * @param integer $type
     */
    public function type(int $type)
    {
        $this->builder->where('type', $type);
    }

    /**
     * @param array $ids
     */
    public function tags(array $ids)
    {

        $this->builder->where(function (Builder $query) use ($ids) {
                $query->whereHas('tags', fn($query) => $query->whereIn('id', $ids))->with('tags');
        });
    }
~~~

Контролеер
------------

Далее просто используйте его в контроллере подобным образом
~~~
public function getColorsByFilter(ColorFilter $filter)
{
    $colors = Color::filter($filter)->get();
    return $colors;
}
~~~
