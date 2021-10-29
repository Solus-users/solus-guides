##Package.yml
Все пакеты создаются из одного файла сборки, который предоставляет все необходимые метаданные для диспетчера пакетов, а также шаги по упаковке, необходимые для создания пакета. Это соответствует спецификации YAML.

Формат
Все файлы package.yml должны быть действительными YAML.

Файл организован в виде иерархии ключ → значение. Инструмент ypkg анализирует файл package.yml для создания соответствующего пакета декларативным образом, поэтому большинство ключей представляют собой простые строки, списки или вложенные пары ключ → значение. Особый случай - этапы упаковки, которые представляют собой скрипты.

Ниже приведен пример файла:

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

Ключи
Не все поля в package.yml являются обязательными, но есть небольшой выбор. Ниже представлен полный список доступных полей.
    
Обязательные ключи
|----|----|----|----|
| Название ключа | Тип | Описание |
| name | строка | Имя пакета. Это также используется в качестве основы для всех имен вложенных пакетов. Если это не неизбежно, оно должно совпадать с именем восходящего потока |
| version | Строка | Версия текущего упакованного программного обеспечения. В большинстве случаев он взят из архива |
| release | Целочисленное значение | Задает номер текущего выпуска. Обновления в номере пакета основаны на этом номере выпуска, а не на номере версии. Таким образом, чтобы выпускать обновление для пользователей, это число должно быть увеличено на единицу.
| license | строка (-и) | Действующая лицензия (-и) на апстрим. Постарайтесь убедиться, что они используют идентификаторы SPDX |
