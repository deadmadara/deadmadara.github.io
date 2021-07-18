---
layout: post
title: Хранение данных в JSON (C#)
comments: true
published: true
---
Пример сериализации и десериализации в JSON при работе со строкой и файлом.

Есть класс, объекты которого нужно сериализовать:
```C#
 class Player
    {
        public string Name;
        public string Race;
        public string Class;
        public int Level;

        public Player() { }
        public Player(string n, string r, string c, int lvl)
        {
            Name = n; Race = r; Class = c; Level = lvl;
        }
    }
```

*Поля имеют модификатор public не просто так - если их закрыть, сериализовать их не получится.*

В C# есть стандартная библиотека *System.Text.Json*, с помощью которой можно проводить подобные операции. К сожалению, её методы у меня не сработали, поэтому пришлось искать другое решение. Судя по всему, и сами Microsoft на данный момент используют другое решение, а именно [JSON.Net](https://www.newtonsoft.com/json). 

Для установки пакета достаточно в редакторе нажать ПКМ по названию проекта, кликнуть "Управление пакетами NuGet", вбить в поиск JSON и среди результатов выбрать "Newtonsoft.Json". 

Далее в проекте необходимо указать `using Newtonsoft.Json` для работы с JSON и `using System.IO` для работы с файлами.

Создание объекта:
`Player p1 = new Player("Valeros", "Human", "Warrior", 1);`

**Сериализация**:
`string JsonString = JsonConvert.SerializeObject(p1);`

**Десериализация**:
`Player p2 = JsonConvert.DeserializeObject<Player>(JsonString);`

Для работы с файлами нужны более тонкие настройки, поэтому **требуется объект класса JsonSerializer**:
`JsonSerializer serializer = new JsonSerializer();`

**Сериализация с записью в файл:**:
```C#
StreamWriter StreamWriterExample = new StreamWriter(@"d:\json.txt");
JsonWriter JsonWriterExample = new JsonTextWriter(StreamWriterExample);
    serializer.Serialize(JsonWriterExample, p1);
    StreamWriterExample.Close();
```

**Дериализация из файла:**:
```C#
StreamReader StreamReaderExample = new StreamReader(@"d:\json.txt");
JsonReader JsonReaderExample = new JsonTextReader(StreamReaderExample);
    Player p3 = serializer.Deserialize<Player>(JsonReaderExample);
    StreamReaderExample.Close();
```

В обоих случаях при работе с файлом обязательно нужно использовать команду *.Close()*, иначе файл будет занят первым процессом и больше ничего с ним сделать не выйдет.

Исходный код [тут](https://github.com/deadmadara/SaveDataTest/blob/main/TestJSON.cs).
