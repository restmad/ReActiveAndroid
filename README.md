![logo][logo]

<p align="center">
<a href="https://travis-ci.org/ImangazalievM/ReActiveAndroid"><img alt="Build Status" src="https://api.travis-ci.org/ImangazalievM/ReActiveAndroid.svg?branch=master"/></a>
<a href="https://bintray.com/imangazaliev/maven/reactiveandroid/_latestVersion"><img alt="Download" src="https://api.bintray.com/packages/imangazaliev/maven/reactiveandroid/images/download.svg"/></a>
<img alt="minSdkVersion 14" src="https://img.shields.io/badge/minSdkVersion-14-blue.svg"/>
<a href="https://opensource.org/licenses/MIT"><img alt="License: MIT" src="https://img.shields.io/badge/License-MIT-blue.svg"/></a>
</p>

<p align="center">
  <strong>Full Documentation: <a href="https://imangazalievm.gitbooks.io/reactiveandroid">ReActiveAndroid</a></strong>
</p>

ReActiveAndroid is Android ORM based on popular library [ActiveAndroid](https://github.com/pardom/ActiveAndroid). Unfortunately, the author of the library stopped maintaining it, so I decided to continue maintain the library instead of him.

New features in ReActiveAndroid in comparison with ActiveAndroid:

- multiple databases support
- more convenient migration mechanism
- a new and improved syntax for creating SQL queries
- One-to-Many relation
- inherited models
- table/model change notifications
- RxJava 2 support
- fixed bugs and more

In the plans:

- Annotation Processing instead of Java Reflection
- improved compatibility with Kotlin
- composite primary key support
- SQL Cipher support
- AutoValue support


## Installation

Add this to your app **build.gradle**:

```gradle
implementation 'com.reactiveandroid:reactiveandroid:1.4.3'
```

## Initial setup

First, we need to create a database class:

```java
@Database(name = "MyDatabase", version = 1)
public class AppDatabase {

}
```

In the `@Database` annotation  we specified the name of the database without an extension and the version of the schema.

Next, we need to initialize the library in the `onCreate` method of the Application class:

```java
DatabaseConfig appDatabase = new DatabaseConfig.Builder(AppDatabase.class)
        .build();

ReActiveAndroid.init(new ReActiveConfig.Builder(this)
        .addDatabaseConfigs(appDatabase)
        .build());
```

## Creating tables

To create a table, we need to create a model class that inherits from the `Model` class and annotate it with `@Table`:

```java
@Table(name = "Notes", database = AppDatabase.class)
public class Note extends Model {

    @PrimaryKey
    private Long id;
    @Column(name = "title")
    private String title;
    @Column(name = "text")
    private String text;

    public Note(String title, String text) {
        this.title = title;
        this.text = text;
    }

    public Long getId() {
        return id;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getTitle() {
        return title;
    }

    public void setText(String text) {
        this.text = text;
    }

    public String getText() {
        return text;
    }

}
```

In the `@Table` annotation we specify table name and the database class in which the table belongs. Also you can create a model class without inheritance from the `Model`, but then you will not have access to `save()` and `delete()` methods.

Now we can save, update and delete records:

```java
Note note = new Note("Title", "Text");
note.save();

note.setText("New text");
note.save();

note.delete();
```

## Query building

Building a query in ReActiveAndroid is like building a normal SQL statement:

```java
//inserting record
Insert.into(Note.class).columns("title", "text").values("Title", "Text").execute();

//getting all table records
List<Note> notes = Select.from(Note.class).fetch();

//getting a specific record
Note note = Select.from(Note.class).where("id = ?", 1).fetchSingle();

//getting a selection of records
List<Note> notes = Select.from(Note.class).where("title = ?", "title").fetch();

//updating record
Update.table(Note.class).set("title = ?", "New title").where("id = ?", 1).execute();

//deleting record
Delete.from(Note.class).where("id = ?", 1).execute();
```

## License

```
The MIT License

Copyright (c) 2017-2018 Mahach Imangazaliev

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

[logo]: https://raw.githubusercontent.com/ImangazalievM/ReActiveAndroid/master/art/logo.png
