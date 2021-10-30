# Package.yml
Все пакеты создаются из одного файла сборки, который предоставляет все необходимые метаданные для диспетчера пакетов, а также шаги по упаковке, необходимые для создания пакета. Это соответствует спецификации YAML.

## Формат
Все файлы package.yml должны быть действительными YAML.

Файл организован в виде иерархии ключ → значение. Инструмент ypkg анализирует файл package.yml для создания соответствующего пакета декларативным образом, поэтому большинство ключей представляют собой простые строки, списки или вложенные пары ключ → значение. Особый случай - этапы упаковки, которые представляют собой скрипты.

Ниже приведен пример файла:

```yml
name       : nano
version    : 2.9.5
release    : 96
source     :
    - https://www.nano-editor.org/dist/v2.9/nano-2.9.5.tar.xz : 7b8d181cb57f42fa86a380bb9ad46abab859b60383607f731b65a9077f4b4e19
license    : GPL-3.0-or-later
summary    : Small, friendly text editor inspired by Pico
component  : system.devel
description: |
        GNU nano is an easy-to-use text editor originally designed as a replacement for Pico, the ncurses-based editor from the non-free mailer package Pine (itself now available under the Apache License as Alpine).
setup      : |
    %patch -p1 < $pkgfiles/0001-Use-a-stateless-configuration.patch
    %reconfigure --enable-utf8 --docdir=/usr/share/doc/nano    
build      : |
        %make
install    : |
    %make_install
    install -D -m 00644 $pkgfiles/nanorc $installdir/usr/share/defaults/nano/nanorc
    install -D -m 00644 $pkgfiles/git.nanorc $installdir/usr/share/nano/git.nanorc
    # https://github.com/scopatz/nanorc
    for rcFile in $pkgfiles/nanorc-extras/*.nanorc; do
        install -m 00644 $rcFile $installdir/usr/share/nano
    done
```
## Ключи
Не все поля в package.yml являются обязательными, но есть небольшой выбор. Ниже представлен полный список доступных полей.
    
### Обязательные ключи

| Название ключа | Тип | Описание |
----|----|----
**name** | `строчное значение` | Имя пакета. Это также используется в качестве основы для всех имен вложенных пакетов. Если это не неизбежно, оно должно совпадать с именем восходящего потока |
**version** | `строчное значение` | Версия текущего упакованного программного обеспечения. В большинстве случаев он взят из архива |
**release** | `целочисленное значение` | Задает номер текущего выпуска. Обновления в номере пакета основаны на этом номере выпуска, а не на номере версии. Таким образом, чтобы выпускать обновление для пользователей, это число должно быть увеличено на единицу.
**license** | `строчное(-ые) значение(-я)`  | Действующая лицензия (-и) на апстрим. Постарайтесь убедиться, что они используют идентификаторы SPDX |
**source** | dict(s) | URL-адрес источника восходящего потока (т. Е. Tarball) с допустимым значением sha256sum. В качестве альтернативы URL-адрес репозитория git с префиксом git 
**component** | `строчное(-ые) значение(-я)` | Компонент / группа пакетов, к которой принадлежит этот пакет. Проверьте доступные компоненты через ```eopkg lc``` 
**summary** | `строчное(-ые) значение(-я)`| Краткое описание пакета или отображаемое имя. 
**description** | `строчное(-ые) значение(-я)` | Более подробное описание программного обеспечения, обычно берется с веб-сайта производителя. 

## Дополнительные, необязательные ключи

| Название ключа | Тип | Описание |
----|----|----
**clang** | `Буллево значение` | Set to `yes` if this package benefits from being built with Clang.
**extract** | `Буллево значение` | Set to `no` to disable automatic source extraction.
**autodep** | `Буллево значение` | Set to `no` to disable automatic binary dependency resolution at build time.
**emul32** | `Буллево значение` | Set to `yes` to enable an `-m32` build (32-bit libs).
**libsplit** | `Буллево значение` | Set to `no` to disable splitting of libraries into `devel` sub-packages.
**conflicts** | `строчное(-ые) значение(-я)` | Specify packages that cannot be installed together with this one.
**optimize** | `заданный список значений` | Specify preset keys to modify compiler and linker flags during build. You can learn more here |
**builddeps** | `list` | Specify build dependencies for the package. You can learn more here.
**rundeps** | `dict(s)` | Specify further runtime dependencies for the packages. You can learn more here |
**replaces** | `dict(s)` | Replace one package with another, used when renaming or deprecating packages for clean upgrade paths.
**patterns** | `dict(s)` | Allows fine grained control over file placement within the package or sub-packages. Useful for packages that are development only (i.e. `/usr/bin` files). |
**environment** | `Юникод` | Specify code that will be exported to all packaging steps of the build (i.e. exporting variables for the entire build).
**networking** | `Буллево значение` | Set to `yes` to enable networking within solbuild.
**homepage** | `строчное значение` | Provides a link to the package's homepage in the Software Center.

### Ключи шагов упаковки, необязательные ключи

Все шаги упаковки считаются необязательными, однако отсутствие шага `install` не приведет к созданию пакета. Каждый из этих ключей содержит контент, который будет помещен в сценарий и выполнен в контролируемой среде для выполнения сборки пакета. По сути, это сценарии Bash с предопределенной средой. 

Название шага | Описание
---- | ----
**setup** | Выполняется после извлечения исходников. Это правильное место для выполнения любой процедуры `configure` или` patch` к исходнику. 
**build** | Используйте этот шаг для запуска части сборки, то есть `make` 
**install** | Здесь вы должны установить файлы в каталог окончательной упаковки, то есть `make install` 
**check** | Здесь должны выполняться тесты / проверка, т.е. `make check` 
**profile** | Здесь должны быть указаны тесты профилирования. `ypkg` будет обрабатывать флаги установки для генерации данных профилирования и использования этих данных для оптимизированной сборки.


## Значения для оптимизации
Одно или несколько значений оптимизации могут быть указаны в списке с помощью ключа оптимизации в файле `package.yml`. Некоторые значения могут переопределять или конфликтовать друг с другом, и их следует использовать только там, где они обеспечивают повышение производительности или исправляют ошибку в пакете или сборке.

Значения для оптимизации| Описание
---- | ----
**speed** | Оптимизируйте пакет для производительности `-O3` плюс другие флаги.
**size** | Оптимизируйте сборку пакета, чтобы минимизировать размер `-Os`. Не поддерживается с лязгом.
**no-bind-now** | Настраивает пакет, чтобы отключить определенные флаги, где RELRO не поддерживается.
**no-symbolic** | Отключает флаг компоновщика -Wl, -Bsymbolic-functions.
**unroll-loops** |Включает `-funroll-loops`. Используйте это умеренно, только если это дает доказанную пользу.
**runpath** | Включает `-Wl, - enable-new-dtags`, чтобы компоновщик использовал RUNPATH вместо RPATH.
**avx256** | Отключает `-mprefer-vector-width = 128` в сборках avx2.
**thin-lto** | Включает Thin Link Time Optimization `-flto=thin` с поддерживаемым компоновщиком. 
**lto** | Включает Link Time Optimization `-flto`.

## Макросы

Для дополнительной помощи при упаковке доступен ряд макросов. Это просто сокращенные способы выполнения обычной операции сборки. Они также гарантируют согласованность итогового пакета. Эти макросы доступны только на наших этапах упаковки, поскольку они заменяются в сценарии перед выполнением.

### Использование

Макросы имеют префикс `%` и заменяются перед выполнением вашего скрипта. Макросы, заканчивающиеся на `%`, используются для предоставления скрипту имен каталогов или значений сборки.

``` bash
# Запуск макроса конфигурации с заданными аргументами
%configure --disable-static
```

### Действующие макросы

Макросы | Описание
---- | ----
**%autogen** | Runs autogen with our `%CONFOPTS%` to create a configure script then proceeds to run `%configure`.
**%cmake** | Configure cmake project with the distribution specific options, such as prefix and release type.
**%cmake_ninja** | Configure cmake project with ninja so it can be used with `%ninja_build`, `%ninja_install` and `%ninja_check` macros.
**%configure** | Runs `./configure` with our `%CONFOPTS%` variable macro.
**%make** | Runs the `make` command with the job count specified in `eopkg.conf`
**%make_install** | Perform a `make install`, using the `DESTDIR` variant. Should work for the vast majority of packages.
**%patch** | Sane patch macro to run in batch mode and not contaminate source tree on failure
**%apply_patches** | Applies all patches listed in the `series` file in `./files` folder.
**%reconfigure** | Updates build scripts such as `./configure` and proceeds to run `%configure`.

### Haskell Actionable Macros

Макросы | Описание
---- | ----
**%cabal_configure** | Runs cabal configure with prefix, libdir, etc. and ensures the necessary package.conf.d is copied to the correct location.
**%cabal_build** | Runs cabal build with `%JOBS%`
**%cabal_install** | Runs cabal copy to `$installdir`
**%cabal_register** | Runs cabal register to generate a pkg-config for package and version, then installs the conf file.

### Ninja Actionable Macros

Макросы | Описание
---- | ----
**%meson_configure** | Runs meson with our CFLAGS and appropriate flags such as libdir.
**%ninja_build** | Runs ninja and passes our `%JOBS%` variable. This macro obsoletes *%meson_build*.
**%ninja_install** | Runs meson install and passed the appropriate `DESTDIR` and `%JOBS%` variable. This macro obsoletes *%meson_install*.
**%ninja_check** | Runs ninja test and passes our `%JOBS%` variable. This macro obsoletes *%meson_check*.

### Perl Actionable Macros

Макросы | Описание
---- | ----
**%perl_setup** | Runs Perl setup scripts Build.pl or Makefile.pl with the appropriate variable flags.
**%perl_build** | Runs Perl build scripts or attempts `%make`.
**%perl_install** | Runs Perl install scripts or attempts `%make_install`.

### Python Actionable Macros

Макросы | Описание
---- | ----
**%python_setup** | Runs the build portion of a setup.py using python2.
**%python_install** | Runs the install portion of a setup.py, to the appropriate root, using python2.
**%python_test** | Without argument, runs the test portion of setup.py. With a `.py` script, execute the script with python2. With something else execute the command "as it is". ([More info](https://github.com/getsolus/ypkg/pull/1))
**%python_compile** | Compiles `*.py` files using python2. This is only useful where the build doesn't compile them already (indicated by availability of `*.pyc` files).
**%python3_setup** | Runs the build portion of a setup.py using python3.
**%python3_install** | Runs the install portion of a setup.py, to the appropriate root, using python3.
**%python3_test** | Without argument, runs the test portion of setup.py. With a `.py` script, execute the script with python3. With something else execute the command "as it is". ([More info](https://github.com/getsolus/ypkg/pull/1))
**%python3_compile** | Compiles `*.py` files using python3. This is only useful where the build doesn't compile them already (indicated by availability of `*.pyc` files).

### Ruby Actionable Macros

Макросы | Описание
---- | ----
**%gem_build** | Runs gem build.
**%gem_install** | Runs gem install with the appropriate parameters.

### Qt Actionable Macros

Макросы | Описание
---- | ----
**%qmake** | Runs qmake for Qt5 with the appropriate make flags.
**%qmake4** | Runs qmake for Qt4, as well as adding the necessary MOC, RCC, and UIC flags since those Qt4 executables end in -qt4.
**%qml_cache** | Compiles `*.qml` files into `*.qmlc` so they are compiled ahead of time.

### Waf Actionable Macros

Макросы | Описание
---- | ----
**%waf_configure** | Runs waf configure with prefix.
**%waf_build** | Runs waf and passes our `%JOBS%` variable.
**%waf_install** | Runs waf install and passed the appropriate `destdir` and `%JOBS%` variable

### Variable Macros

Макросы | Описание
---- | ----
**%ARCH%** | Indicates the current build architecture.
**%CC%** | C compiler
**%CFLAGS%** | cflags as set in `eopkg.conf`
**%CONFOPTS%** | Flags / options for configuration, such as `--prefix=%PREFIX%`. [Full List.](https://github.com/getsolus/ypkg/blob/master/ypkg2/rc.yml#L327-L329)
**%CXX%** | C++ compiler
**%CXXFLAGS%** | cxxflags as set in `eopkg.conf`
**%JOBS%** | jobs, as set in `eopkg.conf`
**%LDFLAGS%** | ldflags as set in `eopkg.conf`
**%LIBSUFFIX%** | Library suffix (either 32 for 32-bit or 64 for 64-bit)
**%PREFIX%** | Hard-coded prefix `/usr`
**%YJOBS%** | Job count without `-j` as set in `eopkg.conf`
**%installroot%** | Hard-coded install directory
**%libdir%** | The distribution’s default library directory, i.e. `/usr/lib64` (Alters for `emul32`)
**%version%** | Version of the package, as specified in the version key.
**%workdir%** | Hard-coded work directory (source tree)

## Переменные

Набор переменных экспортируется на наших этапах сборки. Они используются для обеспечения контекста и структуры сценариев

Переменные | Описание
---- | ----
**$CFLAGS** | cflags as set in `eopkg.conf`
**$CXXFLAGS** | cxxflags as set in `eopkg.conf`
**$LDFLAGS** | ldflags as set in `eopkg.conf`
**$CC** | C compiler
**$CXX** | C++ compiler
**$EMUL32BUILD** | Set only when compiling in `emul32` mode
**$installdir** | The install directory, i.e. where files are installed to for packaging
**$pkgfiles** | Refers to the `./files` directory relative to the `package.yml` file
**$sources** | Refers to the directory where your source files are stored e.g. `$sources/nano.tar.gz`
**$workdir** | The work, or source, directory of the package build

## Типы

The `package.yml` file uses native YAML types, however it follows syntactic conventions and may accept multiple value types for a given key.

### string

This is simply text, which does not need to be quoted.

### string(s)

Indicates that it is possible to use a `list` of strings, or one single `string`.

### integer

Whole, positive number, used in the `release` field.

### list

A YAML list (or array) can be expressed in multiple ways. A short array-notation would look like this:

`[one, two, three]`

They can also be expressed like this:

``` yaml
- First Value
- Second Value
- Third Value
```

### dict

Known as an associative array, this is key to value mapping. These are separated by a colon (`:`), the token on the left is taken to be a key, and the token on the right is the value.

`SomeKey: Some Value`

Note that each `ypkg key` in the YAML file is actually a dict.

### dict(s)

`dict(s)` consists of a list of `dict`s and some assumptions. We primarily make use of this to express advanced information within the package. These permit you to provide no key, and a value only.
In this instance, the key is implicitly assumed to be the package name (e.g. `nano`):

`- some value`

An explicit key, usually a sub-package name:

`- somekey: somevalue`

A mix of both:

``` yaml
- somevalue
- somekey: another value
```

The values may also be expressed in list form, still using the same default key logic:

``` yaml
- [one,two, three]
- somekey: [one,two, three]
- key:
    - value one
    - value two
    - value three
```

## Packaging Practices

Концепции в этом документе просто раскрывают синтаксис файла `package.yml`. Solus придерживается строгих правил и правил упаковки, которым должны следовать упаковщики. Они описаны в статье "Практика упаковки".

Original from https://getsol.us/articles/packaging/package.yml/en/ Translated into Russian Dr.Sheppard | GBOG@protonmail.com 
