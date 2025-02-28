---
author: Wale Soyinka
contributors: Steven Spencer, Ganna Zhyrnova
tested on: Всі версії
tags:
  - впровадження системного адміністрування
  - лабораторна вправа
  - користувачі
  - групи
---



# Лабораторна робота 6: Керування користувачами та групами

## Завдання

Виконавши цю лабораторну роботу, ви зможете

-  Додавати та видаляти користувачів у системі
-  Додавати та видаляти групи у системі
-  Змінювати користувачів і групи в системі
-  Змінювати паролі

Приблизний час виконання цієї лабораторної роботи: 40 хвилин

## Облікові записи користувачів

Керування користувачами є важливим у будь-якій багатокористувацькій мережевій операційній системі. Linux — це багатокористувацька мережева операційна система. Без користувачів не було б потреби в багатокористувацькій мережевій операційній системі!

Керування користувачами в системі тісно пов’язане з безпекою системи. Є така стара приказка:
> Система безпечна настільки, наскільки безпечний її найслабший користувач.

Linux успадковує стару традицію UNIX щодо управління доступом до файлів, програм та інших ресурсів на основі кожного користувача та групи.

Як і майже всі інші конфігурації в Linux, керування користувачами можна здійснювати шляхом безпосереднього редагування конфігураційних файлів, які знаходяться в ієрархії файлової системи. Ця лабораторія досліджуватиме керування користувачами вручну, а також за допомогою системних утиліт.

Ми також коротко розглянемо дозволи на файли та право власності.

Важливі файли керування користувачами та групами наведено в таблиці нижче. Також обговорюються деякі поля/записи у файлах.

### /etc/passwd

* **Призначення:** інформація облікового запису користувача
* **Зміст:**
    * логін
    * зашифрований пароль
    * ідентифікатор користувача (UID)
    * ідентифікатор групи (GID)
    * повне ім'я користувача
    * домашній каталог користувача
    * оболонка за замовчуванням

### /etc/shadow

* **Призначення:** захист інформації облікового запису користувача
* **Зміст:**
    * логін
    * хешований пароль
    * днів з 1 січня 1970 р. цей пароль було востаннє змінено
    * днів, до закінчення яких пароль не можна змінювати. Зазвичай нуль.
    * днів, після чого пароль необхідно змінити
    * днів до закінчення терміну дії пароля, що користувач попереджається про незавершений термін дії
    * днів після закінчення терміну дії пароля обліковий запис вважається неактивним і вимкненим
    * днів з 1 січня 1970 р., коли обліковий запис буде вимкнено
    * зарезервований

### /etc/group

* **Призначення:** інформація про групу
* **Зміст:**
    * назва групи
    * пароль групи
    * ідентифікатор групи (GID)
    * список користувачів, що входять до групи

### /etc/skel

* **Призначення:** зберігає шаблони для застосування до нових облікових записів

## Загальні утиліти

Кілька спільних інструментів, використовуваних в день-день користувача користувача і групового управління діяльністю, використовуються як:

### `useradd`

    ```bash

    Використання: useradd [options] LOGIN
        useradd -D
        useradd -D [options]

    Options:
        --badname                 не перевіряє погані імена
    -b, --base-dir BASE_DIR       базовий каталог для домашнього каталогу нового облікового запису
        --btrfs-subvolume-home    використовує підтом BTRFS для домашнього каталогу
    -c, --comment COMMENT         Поле GECOS нового облікового запису
    -d, --home-dir HOME_DIR       домашній каталог нового облікового запису
    -D, --defaults                друкує або змінює типову конфігурацію useradd
    -e, --expiredate EXPIRE_DATE  термін дії нового облікового запису
    -g, --gid GROUP               ім'я або ідентифікатор основної групи нового облікового запису
    -G, --groups GROUPS          список додаткових груп нового облікового запису
    -h, --help                   відображає це довідкове повідомлення та виходить
    -k, --skel SKEL_DIR           використовує цей альтернативний каркасний каталог
    -K, --key KEY=VALUE           замінює параметри /etc/login.defs за замовчуванням
    -l, --no-log-init             не додає користувача до баз даних lastlog і faillog
    -m, --create-home            створює домашній каталог користувача
    -M, --no-create-home          не створює домашній каталог користувача
    -N, --no-user-group           не створює групу з іменем користувача
    -o, --non-unique              дозволяють створювати користувачів з повторюваними (неунікальним) UID
    -p, --password PASSWORD      зашифрований пароль нового облікового запису
    -r, --system                  створює системний обліковий запис
    -R, --root CHROOT_DIR         каталог для chroot
    -P, --prefix PREFIX_DIR       каталог префіксів, де розташовано файли /etc/*
    -s, --shell SHELL            оболонка входу нового облікового запису
    -u, --uid UID                 ідентифікатор користувача нового облікового запису
    -U, --user-group              створює групу з іменем користувача
    -Z, --selinux-user SEUSER     використовує певний SEUSER для відображення користувача SELinux
    ```

### `groupadd`

    ```bash
    Використання: groupadd [options] GROUP

    Options:
    -f, --force                  завершується успішно, якщо група вже існує, і скасовує -g, якщо GID уже використовується
    -g, --gid GID                 використовує GID для нової групи
    -h, --help                   відображає це довідкове повідомлення та виходить
    -K, --key KEY=VALUE          замінює параметри /etc/login.defs за замовчуванням
    -o, --non-unique             дозволяє створювати групи з повторюваними (неунікальними) GID
    -p, --password PASSWORD       використовує цей зашифрований пароль для нової групи
    -r, --system                  створює системний обліковий запис
    -R, --root CHROOT_DIR         каталог для chroot
    -P, --prefix PREFIX_DI       префікс каталогу
    -U, --users USERS           список користувачів-членів цієї групи
    ```
### `passwd`

    ```bash
    Використання: passwd [OPTION...] <accountName>
    -k, --keep-tokens      зберігає непрострочені маркери автентифікації
    -d, --delete            видаляє пароль для вказаного облікового запису (лише root); також знімає блокування паролем, якщо воно є
    -l, --lock              блокує пароль для вказаного облікового запису (лише root)
    -u, --unlock            розблоковує пароль для вказаного облікового запису (лише root)
    -e, --expire            закінчується термін дії пароля для вказаного облікового запису (лише root)
    -f, --force             силова операція
    -x, --maximum=DAYS      максимальний термін служби пароля (лише root)
    -n, --minimum=DAYS      мінімальний термін дії пароля (лише root)
    -w, --warning=DAYS     кількість днів, які користувачі отримують попередження до закінчення терміну дії пароля (тільки root)
    -i, --inactive=DAYS     кількість днів після закінчення терміну дії пароля, коли обліковий запис стає вимкненим (лише root)
    -S, --status           повідомляє про статус пароля для вказаного облікового запису (тільки root)
      --stdin             читає нові маркери зі стандартного вводу (лише root)

    Параметри довідки:
      -?, --help              Показує це довідкове повідомлення
      --usage            Відображає коротке повідомлення про використання
    ```

## Завдання 1

### Створення нового користувача вручну

Поки що під час попередніх лабораторних робіт ви використовували систему як найпотужніший користувач у системі – користувач `root`. Це не є хорошою практикою у виробничій системі, оскільки це робить систему вразливою з точки зору безпеки. Користувач root може завдавати та відновлювати необмежену кількість шкоди системі.

Окрім суперкористувача, інші користувачі мають обмежений доступ до файлів і каталогів. Завжди використовуйте машину як звичайний користувач. Тут будуть прояснені дві плутані концепції.

- По-перше, домашнім каталогом користувача root є «/root».
- По-друге, кореневий каталог є верхнім каталогом, відомим як каталог / (коса риска). («/root» відрізняється від «/ »)

У цій лабораторній роботі ви створите нового користувача під назвою «Me Mao». Ім’я користувача для “Me Mao” буде першим ім’ям – “me”. Цей новий користувач належатиме до групи «me». Пароль буде «a1b2c3»

!!! Warning "Важливо"

    Конфігурації системи зазвичай відповідають певному формату. Завжди важливо дотримуватися цього формату під час ручного редагування конфігураційних файлів. Один із способів зробити це — знайти та скопіювати наявний запис у файлі, а потім змінити скопійований рядок/розділ будь-якими новими змінами. Це допоможе зменшити ймовірність ваших помилок.


1. Увійдіть на комп'ютер як root

2. Використовуйте команду tail, щоб переглянути останні 4 записи внизу файлу `/etc/passwd`.

    ```bash
    [root@localhost root]# tail -n 4 /etc/passwd
        apache:x:48:48:Apache:/var/www:/sbin/nologin
        xfs:x:43:43:X Font Server:/etc/X11/fs:/sbin/nologin
        ntp:x:38:38::/etc/ntp:/sbin/nologin
        gdm:x:42:42::/var/gdm:/sbin/nologin    
    ```

    Ви будете редагувати файл passwd, використовуючи формат, показаний вище.


#### Щоб створити користувача

1. Нам потрібно буде відредагувати файл `/etc/passwd`.

    Запустіть обраний редактор і відкрийте файл «/etc/passwd»

    Додайте наведений нижче текст унизу або в кінці файлу:

    ```bash
    me:x:500:500:me mao:/home/me:/bin/bash    
    ```

2. Збережіть зміни та закрийте файл passwd.

3. Далі ми відредагуємо файл `/etc/shadow`. Запустіть редактор і відкрийте файл «/etc/shadow». Додайте новий запис, як наведений нижче, у нижній частині файлу - поставте зірочку (*) у полі пароля. Впишіть:

    ```bash
    me:x:11898:11898:99999:7
:::   
    ```

4. Збережіть зміни та закрийте тіньовий файл.

5. Далі ми відредагуємо файл `/etc/group`. Запустіть редактор і відкрийте файл `/etc/group`. У нижній частині файлу додайте новий запис, наприклад:

    ```bash
    me:x:1000:me
    ```

6. Збережіть зміни та закрийте файл групи.

7. Час створити домашній каталог.

    Скопіюйте весь вміст каталогу «/etc/skel» у каталог /home, перейменувавши новий каталог на ім’я користувача, тобто «/home/me». Впишіть:

    ```bash
    [root@localhost root]# cp -r /etc/skel /home/me
    ```

8. Користувач root володіє каталогом, який ви щойно створили, оскільки вона його створила. Щоб користувач «me mao» міг використовувати каталог, ви зміните дозволи/право власності на папку. Впишіть:

    ```bash
    [root@localhost root]# chown -R me:me /home/me      
    ```
9. Створіть пароль для користувача. Встановіть значення пароля `a!b!c!d!`. Ви будете використовувати утиліту «passwd». Введіть «passwd» і дотримуйтесь підказок

    ```bash
    [root@localhost root]# passwd me
    Changing password for user me.
        New password:
        Retype new password:
        passwd: all authentication tokens updated successfully.
    ```

10. Вийдіть із системи, коли закінчите.

## Завдання 2

### Автоматичне створення нового користувача

Існує безліч утиліт, доступних для спрощення всіх завдань/кроків, які ми вручну виконували в попередній вправі. Ми лише пройшли через процес створення користувача вручну, щоб ви могли бачити, що насправді відбувається у фоновому режимі.

У цій вправі ми використаємо деякі звичайні утиліти для керування та спрощення процесу.

Ви створите ще один обліковий запис для користувача «Ying Yang», ім’я для входу буде «ying».

А паролем для «ying» буде «y@i@n@g@».

Ви також створите групу під назвою «common» і додасте до неї користувачів «me» та «ying».

#### Для автоматичного створення нового облікового запису

1. Увійдіть на комп'ютер як root.

2. Ви створите користувача ying, використовуючи всі значення за замовчуванням команди `useradd`. Впишіть:

    ```bash
    [root@localhost root]# useradd -c "Ying Yang" ying
    ```

3. Використовуйте команду `tail`, щоб перевірити додавання, яке ви щойно зробили до файлу `/etc/passwd`. Впишіть:

    ```bash
    flatpak:x:982:982:User for flatpak system helper:/:/sbin/nologin
    pesign:x:981:981:Group for the pesign signing daemon:/run/pesign:/sbin/nologin
    me:x:1000:1000:99999:7
:::
    ying:x:1001:1001:Ying Yang:/home/ying:/bin/bash
    ```

    !!! Question "Питання"

     Перелічити новий запис тут?

4.  Користувач ying не зможе увійти в систему, доки ви не створите пароль для користувача. Установіть пароль ying на `y@i@n@g@`. Впишіть:

    ```bash
    [root@localhost root]# passwd ying
    Changing password for user ying.
        New password:   **********
        Retype new password: **********
        passwd: all authentication tokens updated successfully.
    ```

5. Використовуйте утиліту `id`, щоб швидко переглянути інформацію про нових користувачів, яких ви щойно створили. Впишіть:

    ```bash
    [root@localhost root]# id me
        uid=1000(me) gid=1000(me) groups=1000(me)
    ```

6. Зробіть те ж саме для користувача ying. Впишіть:

    ```bash
    [root@localhost root]# id ying
        uid=501(ying) gid=501(ying) groups=501(ying)
    ```

#### Для автоматичного створення нової групи

1. Скористайтеся програмою `groupadd`, щоб створити нову групу «common».

    ```bash
    [root@localhost root]# groupadd common
    ```

2. Перегляньте кінець файлу `/etc/group`, щоб побачити нове доповнення.

    !!! Question "Питання"
   
        Яка команда для цього?

3. Використовуйте команду `usermod`, щоб додати існуючого користувача до існуючої групи. Давайте додамо користувача ying до групи `common`, яку ми щойно створили на кроці 1. Впишіть:

    ```bash
    [root@localhost root]# usermod -G common -a ying
    ```

4. Зробіть те ж саме для користувача me. Впишіть:

    ```bash
    [root@localhost root]# usermod -G common -a me
    ```

5. Знову запустіть команду `id` для користувачів «ying» і «me». Що змінилося?

6. Використовуйте команду `grep`, щоб переглянути зміни в записі групи `common` у файлі. Впишіть:

     ```bash
    [root@localhost root]# grep common /etc/group
        common:x:1002:ying,me
    ```

#### Щоб змінити обліковий запис користувача


1. Використовуйте команду `usermod`, щоб змінити поле коментаря для користувача «me». Новий коментар, який ви додасте, буде «first last». Впишіть:

    ```bash
    [root@localhost root]# usermod -c "first last" me
    ```

    Використовуйте команду `tail`, щоб перевірити ваші зміни у файлі `/etc/passwd`. Напишіть змінений рядок нижче?

2. Що таке оболонка входу користувача me?

3. Скористайтеся командою `usermod` ще раз, щоб змінити оболонку входу мене на оболонку csh. Впишіть:

    ```bash
    [root@localhost root]# usermod -s /bin/csh me
    ```

4. Нарешті, скористайтеся командою `usermod`, щоб скасувати всі зміни, які ви зробили для користувача «me» вище.

    Відновити значення (оболонку входу тощо) до їхніх початкових значень. Запишіть команди для цього?

## Завдання 3

### Встановити користувача

Не завжди зручно повністю вийти з системи, щоб увійти як інший користувач. Це може бути тому, що у вас виконуються певні завдання, і ви не хочете їх завершувати. Програма `su` (встановити користувача) використовується, щоб тимчасово стати іншим користувачем. Ви можете «su» зі звичайного облікового запису користувача на обліковий запис root або навпаки.

Він змінює поточного користувача на права доступу тимчасового користувача.

За замовчуванням змінні середовища HOME, LOGNAME і USER будуть встановлені на значення тимчасового користувача.

#### Щоб тимчасово стати іншим користувачем

1. Увійшовши в систему як користувач root, перейдіть на користувача «me». Впишіть:

    ```bash
    [root@localhost root]# su   me

    [me@localhost root]$
    ```

    Команда `su` не запропонувала вам ввести пароль користувача me, оскільки ви root

2. Змінити на домашній каталог me.

    ```bash
    [me@localhost root]$ cd

    [me@localhost me]$ cd
    ```

3. Тимчасово ввійшовши як me, використовуйте `su`, щоб увійти як користувач ying. Впишіть:

    ```bash
    [me@localhost me]$ su  ying
    password:
    [ying@localhost me]$
    ```

4. Щоб вийти з типу облікового запису ying:

    ```bash
    [ying@localhost me]$ exit
    ```

    Це поверне вас до облікового запису me.

5. Вийдіть із облікового запису me, щоб повернутися до кореневого облікового запису. Що це за команда?

#### Змусити `su` успадкувати всі змінні середовища нового користувача

1. Щоб змусити `su` використовувати всі змінні середовища тимчасового користувача. Впишіть:

    ```bash
    [root@system1 root]# su - me

    [me@system1 me]$
    ```

    Різниця очевидна відразу. Зверніть увагу на поточний робочий каталог.

2. Повністю вийдіть із системи та перезавантажте машину.

3. Ви закінчили цю лабораторну роботу!
