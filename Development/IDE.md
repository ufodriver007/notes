## PyCharm
[Инструкция для активации](https://306.antroot.ru/jetbrains-activation)
[Ключи](https://3.jetbra.in/)

[Спсок пиратских License servers](https://search.censys.io/search?resource=hosts&sort=RELEVANCE&per_page=25&virtual_hosts=EXCLUDE&q=services.http.response.headers.location%3A+account.jetbrains.com%2Ffls-auth) Вводить адрес только с `http://`

#### Сочетания клавиш (Hotkeys)
|Описание|Хоткей|
|------|--------|
|Поиск по проекту|`Ctrl-Shift-N`
|Поиск по файлу|`Ctrl-Shift-F`
|Запуск файла|`Shift-F10`
|Закомментировать выделенное|`Ctrl-/`
|Отформатировать код|`Ctrl-Alt-L`
|Автодополнение|`Ctrl-Space`
|Показать все места использования|`Ctrl-B`
|Сохранение|`Ctrl-S`
|Дублировать строку|`Ctrl-D`
|Удалить строку|`Ctrl-Y`
|Выделить сразу несколько строк|`Alt-Click`
|Умный поиск|`Shift-Shift`
|Импорт|`Alt-Enter`

## VS Code
#### Плагины
Python(Microsoft)
pylance
pylint
Black Formatter
Django
Docker
Dev Containers
CSS Peek
Auto Rename Tag
IntelliSense for CSS class names in HTML
Live Server
Code Runner
ESLint


#### Файлы и их назначение
1. **%name%.code-workspace**  
    Настройки для данного Workspace. Может содержать настройки плагинов, путей к интерпретатору и тому подобное. Удобно, можно передавать его другим.
2. **launch.json**  
    Настройка логики и параметров запуска отладки проекта. Может содержать несколько вариантов настройки для одного и того же проекта.
3. **settings.json**  
    Может содержать настройки плагинов, путей к интерпретатору и тому подобное. Переопределяет настройки из %name%.code-workspace.
4. **devcontainer.json**  
    Файл содержащий настройки процесса сборки контейнера и его содержимого
#### Сочетания клавиш

Чтобы запускать отладочный Django сервер сочетанием клавиш `Ctrl-F5` нужно перейти во вкладку запуск и отладка(`Ctrl-Shift-D`) и там будет предложено создать `launch.json`.

|Описание|Хоткей|
|------|--------|
|Выбор темы|`Ctrl-K Ctrl-T`
|Увеличить интерфейс|`Ctrl-+`
|Уменьшить интерфейс|`Ctrl--`
|Сохранить файл|`Ctrl-S`
|Форматировать документ|`Ctrl-Shift-I`
|Множественное редактирование|`Alt-Click`
|Переход к определению|`F12`
|Закомментировать строку|`Ctrl-/`
|Найти|`Ctrl-F`
|Поиск по файлам|`Ctrl-P`|
|Глобальный поиск|`Ctrl-Shift-F`
|Глобальная замена|`Ctrl-Shift-H`
|Переход к строке|`Ctrl-G`
|Переместить код|`Alt-Arrow Up`
|Переименовать переменную|`F2`
|Удалить строку целиком|`Shift-Del`
|Выделить всю строку|`Ctrl-L`
|Создать шаблон html|`!-TAB`

>[!info] Для описания настроек проекта нужно создать папку `.vscode` и в ней `settings.json`. Эти же настройки можно использовать как глобальные настройки

Пример `settings.json` для `django` проекта
```json
{
    "git.autoRepositoryDetection": "subFolders",
    "breadcrumbs.enabled": true,

    "python.languageServer": "Pylance",
    "python.analysis.typeCheckingMode": "off",
    "python.analysis.diagnosticMode": "openFilesOnly",
    "python.analysis.autoSearchPaths": true,
    "python.analysis.autoImportCompletions": true,
    "python.analysis.completeFunctionParens": true,
    "python.analysis.inlayHints.variableTypes": true,
    "python.analysis.inlayHints.functionReturnTypes": true,
    "python.analysis.importFormat": "absolute",
    "python.analysis.enablePytestSupport": true,
    "python.analysis.indexing": true,
    "python.analysis.packageIndexDepths": [
        {
            "name": "django",
            "depth": 3,
            "includeAllSymbols": true
        }
    ],
      
    "[python]": {
        "editor.defaultFormatter": "ms-python.black-formatter"
    },
    "emmet.includeLanguages": {
        "django-html": "html"
    },
    "files.associations": {
        "**/*.html": "html",
        "**/templates/*/*.html": "django-html",
        "**/templates/*/*/*.html": "django-html",
        "**/templates/*": "django-txt",
        "**/requirements{/**,*}.{txt,in}": "pip-requirements"
    },
    "django.snippets.exclude": [
        "cms",
        "wagtail"
    ],


    "[django-html]": {
        "editor.defaultFormatter": "batisteo.vscode-django",
        "breadcrumbs.showClasses": true,
        "editor.quickSuggestions": {
            "other": true,
            "comments": true,
            "strings": true
        }
    }
}
```

Для настройки Pylint создаём в корне файл `.pylintrc` 
```
[MESSAGES CONTROL]
disable = missing-function-docstring,
          missing-module-docstring,
          missing-class-docstring,
          import-outside-toplevel,
          attribute-defined-outside-init,
          line-too-long,
          invalid-str-returned,
          no-member
```