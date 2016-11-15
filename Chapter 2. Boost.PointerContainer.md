# Часть 2.Boost.PointerContainer

Библиотека `Boost.PointerContainer` предоставляет контейнеры специализированные на управлении динамически выделенными объектами. Например, благодаря стандарту C++11 вы можете использовать `std::vector<std::unique_ptr<int>>` для создания такого контейнера. Однако, контейеры из Boost.PointerContainerмогут обеспечит дополнительный комфорт в этом деле.

<a id="ex.boost::ptr_vector_01"></a>
### Пример 2.1. Использование `boost::ptr_vector`
```cpp
#include <boost/ptr_container/ptr_vector.hpp>
#include <iostream>

int main()
{
  boost::ptr_vector<int> v;
  v.push_back(new int{1});
  v.push_back(new int{2});
  std::cout << v.back() << '\n';
}
```

Класс `boost::ptr_vector` в основном работает как `std::vector<std::unique_ptr<int>>`
(смотрите [Пример 2.1](#ex.boost::ptr_vector_01)).  Однако, поскольку `boost::ptr_vector`знает , что он хранит динамически выделенные объекты, функции - члены , как `back()`, возвращают ссылку на динамически выделенный объект , а не указатель. Таким образом, в примере **2** записывается в стандартный вывод.

<a id="ex.boost::boost::ptr_set_01"></a>
### Пример 2.2. `boost::ptr_vector`с интуитивно правильным порядком
```cpp
#include <boost/ptr_container/ptr_set.hpp>
#include <boost/ptr_container/indirect_fun.hpp>
#include <set>
#include <memory>
#include <functional>
#include <iostream>

int main()
{
  boost::ptr_set<int> s;
  s.insert(new int{2});
  s.insert(new int{1});
  std::cout << *s.begin() << '\n';

  std::set<std::unique_ptr<int>, boost::indirect_fun<std::less<int>>> v;
  v.insert(std::unique_ptr<int>(new int{2}));
  v.insert(std::unique_ptr<int>(new int{1}));
  std::cout << **v.begin() << '\n';
}
```
[Пример 2.2](#ex.boost::ptr_set_01)
 демонстрирует другую причину использовать специализированный контейнер. В примере динамически выделенные переменные типа `int` сохраняются в `boost::ptr_set` и `std::set`. `std::set` используется вместе с `std::unique_ptr`.

С `boost::ptr_set` порядок элементов зависит от `int` значений. `std::set` сравнивает указатели типа `std::unique_ptr`, но не переменные, на которые данные указатели ссылаются. Чтоб `std::set` отсортировал элементы, базирующиеся на `int` значениях, нужно сообщить контейнеру , как сравнивать элементы. В [примере 2.2](#ex.boost::ptr_set_01) используется `boost::indirect_fun` (предоставленный в Boost.PointerContainer). С помощью `boost::indirect_fun`,`std::set` сообщает, что не могут быть отсортированы элементы, базирующиеся на указателях типа `std::unique_ptr`,но могут быть отсортированы `int` переменные, на которые данные указатели ссылаются. Вот почему в примере **1** выводится дважды.

Кроме `boost::ptr_vector` и `boost::ptr_set` , есть и другие контейнеры , доступные для управления динамически выделенными объектами. Примерами таких  контейнеров являются `boost::ptr_deque` ,`boost::ptr_list` ,`boost::ptr_map`, `boost::ptr_unordered_set`и `boost::ptr_unordered_map`. Эти контейнеры соответствуют хорошо известным контейнерам из стандартной библиотеки.        

<a id="ex.Inserters_01"></a>
### Пример 2.3. Вставки для контейнеров из Boost.PointerContainer
```cpp
#include <boost/ptr_container/ptr_vector.hpp>
#include <boost/ptr_container/ptr_inserter.hpp>
#include <array>
#include <algorithm>
#include <iostream>

int main()
{
  boost::ptr_vector<int> v;
  std::array<int, 3> a{{0, 1, 2}};
  std::copy(a.begin(), a.end(), boost::ptr_container::ptr_back_inserter(v));
  std::cout << v.size() << '\n';
}
```

Boost.PointerContainer предоставляет вставки для своих контейнеров. Они определены в пространстве имен `boost::ptr_container`. Для того, чтобы иметь доступ к Inserters, вы должны включить файл заголовка  boost/ptr_container/ptr_inserter.hpp.

В [примере 2.3](#ex.Inserters_01)  используюется функция `boost::ptr_container::ptr_back_inserter()`, которыая создает вставки типа `boost::ptr_container::ptr_back_insert_iterator`. Эти вставки передаются std::copy() , чтобы скопировать все номера из массива **а** в вектор **V**. Поскольку v является контейнером типа `boost::ptr_vector`, который ожидает адреса динамически выделенных `int` объектов, то  вставка создает копии с помощью `new` в куче и добавляет адреса в контейнер.

Кроме `boost::ptr_container::ptr_back_inserter()`, `Boost.PointerContainer` содержит также функции `boost::ptr_container::ptr_front_inserter()` и `boost::ptr_container::ptr_inserter()`, чтобы создавать соответствующие вставки.       
