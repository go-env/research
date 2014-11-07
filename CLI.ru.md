CLI features
============

Домен по умолчанию localhost для локального использования пакетов,
для публикации репозитория нужно задать реальный домен.
VCS поддерживаемые `go get` приводятся к терминологии `git`.


Base features
-------------

* Установка пакетов, обновляемых go get
* Фиксация версий пакетов, для привязки кода к версиям пакетов
* Поиск по базе пакетов и их версий
* Публикация пакетов под пользовательским доменом
* Удаление пакетов и их зависимостей

Установка upstream-версий пакетов
--------------------------------------

Аналог go get:

    gopkg github.com/gorilla/mux → $GOPATH/src/github.com/gorilla/mux

    build to $GOPATH/pkg/$ARCH/github.com/gorilla/mux.a
    import "github.com/gorilla/mux"

зависимости внутри пакета затаскиваются автоматически.
Далее можно обновлять через `go get`.

Установка фиксированных версий
--------------------------------------

Забрать версию для коммита:

    gopkg -c a1b2c3d4e5f6 github.com/gorilla/mux → $GOPATH/src/github.com/gorilla/mux-a1b2c3d4e5f6

    build to $GOPATH/pkg/$ARCH/github.com/gorilla/mux.a1b2c3d4e5f6.a
    import mux "github.com/gorilla/mux.a1b2c3d4e5f6"

Для получения последнего коммита:

    gopkg -c last github.com/gorilla/mux

Или для тега:

    gopkg -t rel-1.2 github.com/gorilla/mux → $GOPATH/src/github.com/gorilla/mux.t.rel-1.2

    build to $GOPATH/pkg/$ARCH/github.com/gorilla/mux.t.rel-1.2.a
    import mux "github.com/gorilla/mux.t.rel-1.2"

Версии по тегу или коммиту забираются как срез репозитория, без сохранения
служебных папок vcs.

Пакеты фиксированных версий не обновляются через `go get`. Т.к. версия уже
зафиксирована, то обновление имеет смысл, только если произошли изменения в истории
vcs. Поэтому `gogkg -u` повторно заберёт из репозитория версию на данный момент времени.

Automode for fixed versions
---------------------------

Без указания версии (возможно это стоит оставить единственным вариантом, похожим способом
версии распознаются в gopkg.in):

    gopkg -a -t rel-1.2 github.com/gorilla/mux → $GOPATH/src/github.com/gorilla/mux.rel-1.2

    build to $GOPATH/pkg/$ARCH/github.com/gorilla/mux.rel-1.2.a
    import mux "github.com/gorilla/mux.rel-1.2"

В порядке приоритета ищется коммит, затем тег, затем бранч подпадающий под маску имени.
Выбирается версия, которая первой подпадает под маску.

Минус подхода: потенциальная возможность ошибочных определений версий, если например
есть совпадающие имена веток и тегов.

Имеет смысл сделать подход дефолтным, но дополнительно дать возможность явно указывать
вид привязки - комиит, тег или бранч.

Установка в локальный домен
----------------------------------

Для локального форка пакета можно забрать его под localhost:

    gopkg -l github.com/gorilla/mux → $GOPATH/src/localhost/gorilla/mux

    build to $GOPATH/pkg/$ARCH/localhost/gorilla/mux.a
    import "localhost/gorilla/mux"

Эти пакеты можно обновлять через `go get` (при запущенном демоне `gopkgd`), хотя это
не имеет особого смысла. Может быть полезно, если требуется вести несколько разных
$GOPATH содержащих часть пакетов из общего репозитория. В этом случае репозиторий
`/var/lib/gopkg/localhost` для `go get` играет роль удалённого репозитория,
как скажем github и пр.

Пакеты фиксированных версий здесь также поддерживаются `go get`, например:

    gopkg -l -t rel-1.2 github.com/gorilla/mux → $GOPATH/src/localhost/gorilla/mux.t.rel-1.2

    build to $GOPATH/pkg/$ARCH/localhost/gorilla/mux.t.rel-1.2.a
    import "localhost/gorilla/mux.t.rel-1.2"
    go get -u localhost/gorilla/mux.t.rel-1.2

    gopkg -l -b develop github.com/gorilla/mux → $GOPATH/src/localhost/gorilla/mux.b.develop

    build to $GOPATH/pkg/$ARCH/localhost/gorilla/mux.b.develop.a
    import "localhost/gorilla/mux.b.develop"
    go get -u localhost/gorilla/mux.b.develop

Установка под кастомный домен
-------------------------------------

Для публикации форков пакетов, например для ведения внутрикорпоративного репозитория
с форками можно устанавливать пакеты так (пример для домена `example.com`):

    gopkg domain example.com
    gopkg -d github.com/gorilla/mux → $GOPATH/src/example.com/gorilla/mux

    import "example.com/gorilla/mux"

Далее эти пакеты могут забираться с удалённых машин (публикация через демон `gopkgd`):

    go get example.com/gorilla/mux

Пример для ветки:

    gopkg -d -b develop github.com/gorilla/mux → $GOPATH/src/example.com/gorilla/mux.b.develop

    go get example.com/gorilla/mux.b.develop
    import "example.com/gorilla/mux.b.develop"

Установка зависимостей
---------------------------

При установке пакета зависимости парсятся из исходных текстов и устанавливаются
автоматически. Если зависимость уже была установлена, она используется, её обновление
не производится.

При установке фиксированных версий по умолчанию зависимости устанавливаютя из
upstream. Можно зафиксировать версии зависимостей, будет установлена фиксация
по коммиту для каждой зависимости, а также для самого пакета.

В примере, считаем, что в ветке master последним является коммит a1b2c3d4e5f6 для
пакета mux и пакет mux требует как зависимость пакет sample. В свою очередь для
sample последним является коммит 1234 и он хранится на launchpad.net (под `bzr`):

    gopkg -F github.com/gorilla/mux → $GOPATH/src/github.com/gorilla/mux.a1b2c3d4e5f6
       → $GOPATH/src/launchpad.net/sample.1234

Обновление пакета
---------------------

    gopkg -u github.com/gorilla/mux

Аналогично для пакетов фиксированных версий.

Правило для обновлений через `go get`: обновлять через `go get` можно пакеты
не имеющие привязки к версии, либо имеющие привязку к пользовательскому домену.
В остальных случаях следует использовать `gopkg -u`.

Короткое правило, чтобы не запоминать правило выше: обновлять всё через `gopkg -u`
и не трогать `go get`.

Зависимости пакета не обновляются.
Для обновления пакета и всех его зависимостей:

    gopkg -U github.com/gorilla/mux

Поиск
-------

Локальный поиск:

    gopkg -s mux

Выдаст пакеты совпадающие по имени, при наличии фиксированных версий
выведет их список в виде, пригодном для оператора `import`:

    github.com/gorilla/mux             branches: master, develop; tags: rel-1.1, rel-1.2
    github.com/gorilla/mux.b.develop
    github.com/gorilla/mux.t.rel-1.1
    github.com/gorilla/mux.t.rel-1.2

Можно указывать домен через -d

Глобальный поиск потребует вебсервиса с индексом пакетов гитхаба и пр.

    gopkg search mux

Локальный поисковик поднимается на `gopkgd`.

Связывание версий
----------------------

Не рекомендуемый hack mode:

    gopkg link -c a1b2c3d4e5f6 github.com/gorilla/mux

или

    gopkg link github.com/gorilla/mux.a1b2c3d4e5f6

установит связь (файловые ссылки) upstream-версии github.com/gorilla/mux с её
коммитом a1b2c3d4e5f6. Далее при импорте

    import "github.com/gorilla/mux"

реально будет импортироваться фиксированная версия для указанного коммита.
Это нужно, чтобы фиксировать версии пакетов без рефакторинга использующего
их кода. Хотя может затруднить репозиторий и логику обновлений. gopkg сохранит
метаданные о фиксации, чтобы использовать их при обновлениях пакета.

    gopkg link github.com/gorilla/mux

покажет есть ли привязка этого пакета к конкретным версиям:

    github.com/gorilla/mux → github.com/gorilla/mux.a1b2c3d4e5f6

Кастомное окружение
------------------------

Можно создать новый GOPATH со своим окружением, которое будет скопировано из текущего.
При этом фиксированные версии будут преобразованы в обычные. В файле конфига (в формате
`gopkg list`) перечисляются необходимые версии, из которых будет создана версия в новом
окружении.

    gopkg make-env <newpath>

TODO описать подробнее

Удаление
----------

Удаление из $GOPATH если от этого пакета не зависят остальные пакеты:

    gopkg remove github.com/gorilla/mux

Удаление всех найденных вхождений пакета для разных коммитов и доменов:

    gopkg remove -A github.com/gorilla/mux

При удалении пакета удаляются также установленные с ним зависимости. Это
можно отменить, удалив только сам пакет:

    gopkg remove -K github.com/gorilla/mux

Построение дерева зависимостей
-------------------------------------

Инициализация или обновление дерева зависимостей по $GOPATH:

    gopkg tree

Обновление всех пакетов
--------------------------

    gopkg upgrade

Фикс импорта пакета в исходных текстах
-----------------------------------------------

Для удобства импорта:

    goimp github.com/gorilla/mux.t.rel-1.2
    или
    goimp -t rel-1.2 github.com/gorilla/mux

Пройдёт по исходным текстам пакета в текущей директории и заменит импорты любых версий
github.com/gorilla/mux на указанную версию с тегом rel-1.2.

Состав ПО
-----------

Утилиты `gopkg`, `goimp` и библиотека `gopkgd`. Утилита либо поднимает gopkgd по запросу,
для разовых операций, либо, если находит работающий в виде демона экземпляр,
использует его.
