# Тест разработчика C# №2 (шаблон)

<aside>
⚠️ Отвечайте самостоятельно, не стремитесь подсмотреть в справочниках, гугле и тому подобных. Если не знаете ответ на вопрос - совершенно нормально его пропустить. Качество и самостоятельность ответов будем обсуждать на очных интервью, поэтому предлагаем не читерить и экономить Ваше и наше время.
  
⚠️ Публикуйте свой Gist с расширением MD чтобы сохранить форматирование и читаемость.

</aside>

# 1. Общие вопросы

### 1.1 String и DateTime

Что будет выведено в консоль и почему?

```csharp
class Program {
  static String location;
  static DateTime time;
 
  static void Main() {
    Console.WriteLine(location == null ? "location is null" : location);
    Console.WriteLine(time == null ? "time is null" : time.ToString());
  }
}
```

---

> Ваш ответ
> 
> 1) "location is null", т.к. строка - ссылочный тип, но не проинициализирована, потому принимает значение null
> 2) минимальное значение даты (01.01.0001), т.к. DateTime значимый не Nullable тип и не может принимать значение null

### 1.2 Enumerators

Что будет выведено в консоль?

```csharp
IEnumerable<int> EnumerateInts(int length) {
  for (var i = 0; i < length; i++) {
    Console.WriteLine(i);
    yield return i;
  }
}

var items = EnumerateInts(3);
```

---

> Ваш ответ
> 
> Ничего. Генератор yield не создает коллекцию.

### 1.3 Базовые классы List

Перечислите приблизительно на память от каких базовых классов наследуется `List<>` и какие интерфейсы он реализует?

---

> Ваш ответ
> 
> Интерфейсы: IList<T>, IEnumerable<T>, ICollection<T> (а также их необобщенные версии)

# 2. Entity Framework

### 2.1 Запросы

Какие запросы (приблизительно) будут направлены в SQL-сервер во время исполнения следующего кода:

```csharp
class Student {
  public int Id {get;set;}
  public string Name {get;set;}
  public int Course {get;set;}
  public DateTime BirthDate {get;set;}
}

var student = context.Students.FirstOrDefault(s => s.Id == 1);
student.Name = "Oleg";
context.SaveChanges();
```

---

> Ваш ответ
> 
```sql
UPDATE Students
SET Name = "Oleg"
WHERE Id = 
(SELECT Id 
FROM Students
WHERE Id = 1
LIMIT 1)
```

### 2.2 Трекинг изменений

Для чего предназначен метод `IQueryable.AsNoTracking()`? Когда его следует использовать?

---

> Ваш ответ
> 
> Позволяет не отслеживать изменения сущностей и не сохранять изменения в БД. Используется в запросах на получение данных (только для чтения).

### 2.3 Запросы

Что произойдёт при выполнении первой строки и второй? Какого типа будут переменные `items1` и `items2`?

```csharp
var items1 = context.Students.Take(5).ToList();
var items2 = context.Students.ToList().Take(5);
```

---

> Ваш ответ
> 
> 1) Будут выбраны первые 5 записей. Тип - List<Student>
> 2) Будут выбраны первые 5 записей. Тип - IEnumerable<Student>

# 3. Dependency Injection

### 3.1. Жизненный цикл

Опишите чем отличается регистрация типов или объектов методами `.AddTransient()`, `.AddScoped()` и `.AddSingleton()`?

---

> Ваш ответ
> 
> `.AddTransient()` - объект регистрируется каждый раз, когда его запрашивают.
> `.AddScoped()` - объект регистрируется один раз при HTTP-запросе.
> `.AddSingleton()` - объект регистрируется один раз при запуске приложения.

# 4. Разное

### 4.1. Паттерн MVVM

Что такое MVVM? Почему он обрёл популярность именно в WPF?

> Ваш ответ
> 
> MVVM (Модель - Представление - Модель представления) паттерн, который позволяет разделить модель, логику и UI, что обеспечивает гибкость и расширяемость разработки. MVVM в WPF позволяет создавать привязку данных с использованием команд, а не событий, а также управлять зависимостями с помощью Dependency Injection.

### 4.2. INotifyPropertyChanged

Опишите состав интерфейса INotifyPropertyChanged. В чём разница?

```csharp
PropertyChanged("City");
PropertyChanged(null);
```

> Ваш ответ
> 
> Интерфейс содержит единственное поле - событие PropertyChanged, которое происходит, когда изменяется свойство, которое передается ему в качестве аргумента.

### 4.3 Методы HTTP-протокола

Объясните разницу между PUT, POST, PATCH.

> Ваш ответ
> 
> PUT обычно используется для обновления данных, POST - для отправки данных. Оба запроса отправляют данные в теле запроса.

# 5. Задачи

### 5.1 Проектирование БД

Спроектируйте таблицы в реляционной БД для хранения двумерных матриц произвольного размера (M**×**N). Укажите в свободной форме состав полей таблицы/таблиц.

---

> Ваш ответ
> 
```sql
CREATE TABLE Matrix
(
    id integer NOT NULL,
    rows integer NOT NULL DEFAULT 1,
    columns integer NOT NULL DEFAULT 1,
    PRIMARY KEY (id)
); 
```

### 5.2 Уникальный символ

Напишите реализацию функции, которая принимает на вход строку из символов `a-z`, а на выходе выдаёт первый уникальный символ (который встречается в строке ровно 1 раз), либо `null`, если такого символа нет.

---

```csharp
public static char? FirstUnique(string str) {
   Dictionary<char, int> dict = [];

  foreach (char c in str) 
  { 
      if (dict.TryGetValue(c, out int value))
      {
          dict[c] = ++value;
      }
      else
      {
          dict.Add(c, 1);
      }
  }
  return dict.FirstOrDefault(kv => kv.Value == 1).Key;
}
```
