---
title: "Что такое протокол SSH? Генерация ключей с помощью ssh-keygen"
description: "SSH (сокращение от Secure Shell) — это сетевой протокол, посредством которого два компьютера могут взаимодействовать и обмениваться данными. Важно, что данные при этом шифруются, поэтому протокол SSH считается безопасным и подходит для передачи чувствительных данных (персональных и пр.)."
---

# Безопасный доступ на сервер: протокол SSH и авторизация с помощью ключей

SSH (сокращение от Secure Shell) — это сетевой протокол, посредством которого два компьютера могут взаимодействовать и обмениваться данными. Важно, что данные при этом шифруются, поэтому [протокол SSH](https://ru.wikipedia.org/wiki/SSH) считается безопасным и подходит для передачи чувствительных данных (персональных и пр.).

С помощью SSH можно подключаться к компьютерам или удаленным серверам, выполнять на них команды, копировать и редактировать файлы — этих возможностей достаточно для полноценного администрирования. Благодаря удобству и безопасности SSH широко применяется в корпоративной инфраструктуре, в том числе для установки обновлений и управления бизнес-критичными системами.

Поддержка SSH встроена в операционные системы Unix и Linux — вы можете подключаться к другому компьютеру по протоколу SSH из любого терминала (например, bash). Для работы под управлением Windows есть бесплатные SSH-клиенты (например, Putty). Начиная с версии Windows 10 и Windows Server 2019 в состав операционной системы включен клиент OpenSSH (он может быть не установлен на компьютере изначально, но добавляется в систему по запросу, одной командой).

## Как работает подключение по SSH

В работе по протоколу SSH участвуют две стороны: SSH-сервер — он отвечает за аутентификацию пользователей и обработку передаваемых данных, и SSH-клиент — с его помощью можно подключиться к серверу и выполнять на нем различные команды.

Надежность SSH обеспечивается тем, что в процессе подключения создается безопасное соединение («туннель»), по которому передаются только зашифрованные данные — они шифруются на клиенте перед передачей и расшифровываются на сервере после получения.

На сервере выделяется определенный порт для подключения по SSH (по умолчанию используется 22 порт, но для повышения безопасности рекомендуется его менять). Клиент обращается к открытому порту на сервере и передает данные для аутентификации. Сервер «слушает» (постоянно опрашивает) открытый порт, и при получении запроса проверяет подлинность клиента — аутентифицирует его. Если клиент прошел проверку, между клиентом и сервером устанавливается соединение, по которому пересылаются команды и данные.

SSH-сервер поддерживает три способа аутентификации: по IP-адресу клиента, по логину/паролю или по ключу. 
Аутентификация по IP-адресу предполагает, что все подключения с указанного адреса автоматически принимаются. Этот способ небезопасен и используется крайне редко. Аутентификация по логину и паролю наиболее привычна для пользователей, но имеет ряд недостатков. Самым безопасным способом считается аутентификация по ключу.

## Аутентификация с помощью ключей SSH

[Ключ](https://ru.wikipedia.org/wiki/%D0%9A%D0%BB%D1%8E%D1%87_(%D0%BA%D1%80%D0%B8%D0%BF%D1%82%D0%BE%D0%B3%D1%80%D0%B0%D1%84%D0%B8%D1%8F)) — это последовательность символов. Размеры ключа могут быть разными; чем длиннее ключ, тем выше его надежность. Минимально надежной считается длина 1024 бит (в {{ yandex-cloud }} используются ключи в 2048 бит).

Для каждого пользователя генерируется своя уникальная пара ключей: закрытый ключ хранится у пользователя, а открытый размещается на сервере. Имея закрытый ключ, можно по специальному алгоритму восстановить открытый ключ. Но обратное невозможно — по открытому ключу вычислить закрытый ключ нельзя, можно только проверить соответствие. По открытому ключу сообщения зашифровываются, а по закрытому – расшифровываются. Таким образом, только владелец ключа может расшифровать предназначенное ему сообщение.

Как правило, ключи хранятся в папке .ssh и представляют собой пары файлов с одинаковыми именами (по умолчанию при генерации ключа предлагается имя id_rsa). Файл с открытым ключом имеет расширение .pub, а файл с закрытым ключом сохраняется без расширения.

Важное правило безопасности: закрытый ключ никогда и никому не пересылается (даже администратору) и не выкладывается в открытые источники, иначе он будет скомпрометирован.

Для доступа к разным сервисам и серверам генерируются свои ключи (команду генерации мы рассмотрим чуть ниже).
Для дополнительной защиты при генерации пары SSH-ключей можно задать еще и пароль, который будет запрашиваться каждый раз при обращении к закрытому ключу (но этот пароль можно оставить пустым и не использовать). Пароль обеспечивает защиту даже в том случае, если злоумышленник получил доступ к компьютеру, на котором хранится закрытый ключ.

### Преимущества SSH-ключей

По правилам безопасности для каждого логина необходим свой уникальный пароль определенной длины и сложности, который нужно периодически менять. Записывать пароли не рекомендуется, а запоминать их — целая проблема. В отличие от логинов/паролей, ключи хранятся в файлах, их не нужно запоминать (а значит, нет опасности забыть).
Ключи гораздо длиннее паролей, поэтому их практически невозможно подобрать. Они обеспечивают высокую степень защиты, необходимую бизнесу из-за рисков хакерских атак. Пользователи сегодня часто подключаются к корпоративной сети удаленно, с личных компьютеров и по незащищенным Wi-Fi сетям — это ужесточает требования к защите серверов. 
Кроме того, ключи удобно использовать в скриптах, с помощью которых администраторы автоматизируют различные рутинные операции: установку обновлений, создание архивных копий, конфигурирование систем и сервисов. Это особенно востребовано из-за широкого распространения Agile-разработки и практик DevOps.

В {{ yandex-cloud }} для подключения к виртуальной машине предпочтительный способ авторизации — [с помощью ключей SSH](https://cloud.yandex.ru/docs/compute/operations/images-with-pre-installed-software/operate) (хотя авторизация по логину и паролю тоже поддерживается). Открытый ключ размещается в виртуальной машине, а закрытый ключ хранится у пользователя. 

## Команды для работы с SSH-ключами 

### Как сгенерировать ключ SSH

Для работы с SSH-ключами используются утилиты, входящие в оболочку OpenSSH. Они работают под Linux, Windows и MacOS.

Для генерации ключей предназначена утилита ssh-keygen. Также в состав OpenSSH входят утилиты ssh-agent для кеширования (хранения) ключей, ssh-add для добавления ключей в ssh-agent и [еще несколько программ](https://www.openssh.com/).

Чтобы сгенерировать пару SSH-ключей, в консоли Linux или Windows выполните команду: 
```
ssh-keygen -t rsa -b 2048
```
Параметр `–t` задает тип создаваемого ключа. В большинстве случаев используются ключи rsa (т.е. на основе криптографического алгоритма RSA).
Параметр `–b` определяет размер ключа в битах. Размер зависит от требований конкретного сервиса. Для {{ yandex-cloud }} нужен ключ длиной 2048 бит. 

В процессе выполнения ssh-keygen запросит дополнительный пароль для доступа к закрытому ключу. Если вы не хотите указывать пароль, оставьте пустую строку и нажмите Enter.
После выполнения команды ssh-keygen будут созданы два файла — с закрытым и открытым ключом (название и расположение файлов можно указать вручную в момент выполнения программы, по умолчанию это папка .ssh и файлы id_rsa). Если вы планируете создавать отдельные ключи для разных серверов или сервисов, давайте файлам значимые имена. Например, ключи для подключения к {{ yandex-cloud }} можно сохранить в файлах yc_rsa.

### Как авторизоваться с помощью ключа SSH

Как уже говорилось выше, публичную часть SSH-ключа (содержимое файла с расширением .pub) нужно поместить на сервер или в виртуальную машину. 

Если вы работаете с облаком {{ yandex-cloud }} с помощью консоли управления, при создании виртуальной машины Linux содержимое открытого ключа нужно скопировать в поле SSH-ключ. Если вы создаете виртуальную машину с помощью команды (например, через интерфейс командной строки CLI), то в качестве одного из параметров указывается расположение файла с публичным ключом, и в процессе выполнения команды этот ключ копируется в виртуальную машину.
Если с сервером или виртуальной машиной могут напрямую работать несколько пользователей, для каждого из них создается отдельный каталог, в нем создается папка .ssh, и в эту папку помещается уникальный публичный ключ пользователя.

Чтобы подключиться к виртуальной машине по протоколу SSH, используйте команду
```
ssh login@ip_address
```
где `login` — это логин пользователя, а `ip_address` — IP-адрес сервера или публичный адрес виртуальной машины в облаке.

Если вы подключаетесь к этому IP-адресу и с этим ключом впервые, у вас будет запрошено согласие на подключение по неизвестному ранее ключу. После этого указанный IP-адрес будет добавлен в файлы known_hosts и config на вашем компьютере (если этих файлов еще нет в той же папке .ssh, они будут созданы, а если файлы уже есть — в них будет добавлена информация про указанный IP-адрес).