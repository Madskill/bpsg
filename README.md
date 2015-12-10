# Bessarabov's Perl Style Guide

Записываю сюда все правила, которым следую при написании Perl кода. Порядок
правил ничего не значит, правила никак не сгруппированы.

Текущая версия документа не является стабильной. Что угодно может меняться в
любой момент времени (возможно, когда-нибудь это документ будет
версионироваться по [SemVer](http://semver.org/), но до этого еще очень
далеко).

## 1. Кодировка файлов UTF-8

Это самая популярная кодировка, конечно, стоит использовать именно ее.

## 2. В файлах не должен использоваться BOM

BOM — это [Byte order mark](http://ru.wikipedia.org/wiki/Byte_order_mark),
штука с помощью которого указывают кодировку файла. Поскольку уже есть
правило 1, которое говорит что кодировка всегда UTF-8, то BOM использовать
избытачно.

## 3. POD всегда начинается с `=encoding UTF-8`

Далеко не всегда в POD есть какие-то символы, которых нет в простой 8-и битной
кодировке [Latin1](http://en.wikipedia.org/wiki/Latin1).. Когда такие символы
есть, то конечно, обязательно нужно узазывать `=encoding UTF-8`. Но даже
когда весь POD написан простым latin1, все равно стоит указывать кодирову
UTF-8, чтобы изменение текста не потребовало еще и дописывание директивы.

[Ссылка на stackoverflow]( http://stackoverflow.com/questions/18109154/what-string-should-be-used-to-specify-encoding-in-perl-pod-utf8-utf-8-or/)
 с ответом что из всех возможных кодировок "utf8", "UTF-8", "utf-8" нужно писать именно "UTF-8".

И директива `=encoding UTF-8` всегда должна быть самой первой директирой POD.

## 4. Для имен переменных используется нижний регистр и символ "_"

Нужно писать `$some_variable`, а не `$SomeVariable` или как-то иначе.

Предпосылки этого — то что это самый читаемый стандарт и это очень
распространенная практика в мире Perl.

## 5. Переменная с именем файла называется $file_name

Достаточно часто в коде встречаются переменные которые хранят в себе имя
какого-то файла. Такую переменую называют по разному: иногда `$filename`, а
иногда `$file_name`. Какого-то очивидного плюса нет ни у одного варианта, но
чтобы был какой-то стандарт, я выбрал вариант `$file_name`, так как на мой
взгляд это красивее.

[Википедия](http://en.wikipedia.org/wiki/Filename) тоже не вносит
однозначного ответа как правильно, цитирую:

> A filename (also written as two words, file name) ...

## 6. Не должно быть закомментированного кода

Исходный код должно быть просто читать. Если в коде есть закомментированные
строки, то возникает вопрос почему эти строки не используются, — не ошибка ли
это. Чтобы убрать такие вопросы в исходном коде не должно быть
закомментированных строк кода.

Даже есть серьезные основания считать что совсем скоро код поменяется, не
нужно писать закомментированные участки в расчете на будущее. Вполне возможно,
что появятся другие более срочные задачи или поменяются планы, а код останется
с комментарием, который никогда не будет использован.

Неправильно:

    $model->do_work();
    # Uncomment to outut debug:
    #
    # say "Model after do_work()";
    # say "Model status: " . $model->status;
    # say "Model output: " . $model->output;

Правильно:

    $model->do_work();

## 7. Всегда использовать прагмы strict и warnings (c FATAL all)

Во всех скриптах и модулях дожено быть как можно выше:

    use strict;
    use warnings FATAL => 'all';

Пример проблемы которая может возникнуть если не использовать в посте "[Грабля
Perl кода my $success = false;](https://ivan.bessarabov.ru/blog/perl-boolean-barewords)"

## 8. В папке t/ должны быть только файлы с расширеним .t

Вот однострочник с помощью которого можно найти файлы, которые не соблюдают это условие:

    find t/ -type f|perl -nalE 'say if $_ !~ /.t\z/'

## 9. Библиотеки, которые нужны для тестов нужно класть в папку t_lib/

## 10. Любой тест можно запустить из корня прокта с помощью perl без дополнительных ключей

Тесты должны быть написаны так, чтобы их можно было запустить с помощю простой команды:

    perl t/sample.t

Если тест работает если указать `perl -Ilib t/sample.t`, то это баг, коорый нужно править
(стоит использовать [lib::abs](https://metacpan.org/pod/lib::abs))

## 11. В тестировании не использовать subtest()

https://metacpan.org/pod/Test::More#subtest — вот это использовать не нужно.

## 12. Не выравнивать ключи в хеше

Нужно писать так:

    my $data = {
        a => 1,
        bar => 2,
        asdf => 3,
    };

А не так:

    my $data = {
        a    => 1,
        bar  => 2,
        asdf => 3,
    };

Причина — в том случае если появлется более длинный ключ, то нужно
переформатировать весь хеш. Даже если переформатирование делается не
руками, а с помощью специального скрипта все равно это плохо — так как в
diff комита с этими изменениями будет слишком много всего, а так же
будет merge confilict в том случае если несколко человек работали над
одним и тем же куском кода.

## 13. Синтаксис для if else

Праивильный вариант:

    if ( $arr eq 'expected string' ) {
        ...
    } else {
        ...
    }

## 14. При записи элементов массива или хеша в столбик, конечная запятая обязательна

Правильно:

    my @arr = (
        1,
        2,
        5,
    );

    my %h = (
        a => 1,
        b => 2,
    );

Неправильно:

    my @arr = (
        1,
        2,
        5
    );

    my %h = (
        a => 1,
        b => 2
    );

Причина — сложнее поддерживать (есть опасность забыть вставить запятую
при добавлении нового элемента в конце), плюс избыточная сложночть diff
комита в том случае если нужно добавить не только элемент, но еще и запятую
перед ним.

## 15. Не использовать переменные $a и $b нигде, кроме как в sort

## 16. Не использовать FindBin

Вместо такого:

    use FindBin qw($Bin);
    use lib "$Bin/lib";

Использовать:

    use lib::abs qw(
        ../lib
    );

## 17. Не использвать $ в регулярных выражениях, а использовать \z

Цитата из [perlre](http://perldoc.perl.org/perlre.html):

    \z  Match only at end of string

А символ `$` матчит конец строки или конец строки плюс \n

## 18. В папке lib/ должны быть только файлы с расширем .pm

Вот однострочник с помощью которого можно найти файлы, которые не соблюдают это условие:

    find lib/ -type f|perl -nalE 'say if $_ !~ /.pm\z/'

## 19. Для всех публиных методов/функций должен быть POD перед кодом

    =head2 do_stuff

    ...

    =cut

    sub do_stuff {
        ...
    }
