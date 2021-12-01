# Результат домашнего задания к занятию "3.3. Операционные системы, лекция 1"
1. Какой системный вызов делает команда cd?
    * `chdir("/tmp")`
2. Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.
    * `openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3`
3. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).
    * `cat /dev/null > /proc/PID/fd/X`
4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?
    * **"Зомби"** - это фантомный процесс, который просто имеет запись в таблице процессов, но при этом никакие ресурсы кроме пространства таблицы процессов, не используются.
    *  **"Сирота"** - процесса суть в том, что его родительский процесс был по какой-либо причине прерван, и сирота теперь переходит под руководство init, чей ID процесса равен 1. PID сиротского процесса получит значение 1. Пользователь также может создать подобный процесс, отсоединив его от терминала. Сиротские процессы используют много ресурсов.
5. На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты?
    * `sudo opensnoop-bpfcc`  
    ```bash
    PID    COMM               FD ERR PATH
    766    vminfo              4   0 /var/run/utmp
    580    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
    580    dbus-daemon        18   0 /usr/share/dbus-1/system-services
    580    dbus-daemon        -1   2 /lib/dbus-1/system-services
    580    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
    ```
6. Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.
    * ```bash
      uname({sysname="Linux", nodename="vagrant", ...}) = 0    
      write(1, "Linux vagrant 5.4.0-80-generic #"..., 105Linux vagrant 5.4.0-80-generic #90-Ubuntu SMP Fri Jul 9 22:49:44 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux) = 105
      ```    
7. Чем отличается последовательность команд через ; и через && в bash? Есть ли смысл использовать в bash &&, если применить set -e?
    * `;` - разделитель команд, позволяет записывать две и более команд в одной строке.
    * `&&` - логическая операция AND (логическое И). В операциях проверки условий, оператор && возвращает 0 (success) тогда, и только тогда, когда оба операнда имеют значение true (ИСТИНА).
    * `set -e` - прерывает сессию при любом ненулевом значении исполняемых команд. В случае &&  вместе с set-e - вероятно не имеет смысла, так как при ошибке, выполнение команд прекратиться. 
8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?
    * ```bash
      e - указывает оболочке выйти, если команда дает ненулевой статус выхода
      u - неустановленные/не заданные параметры и переменные считаются как ошибки
      x - печатает аргументы команды во время выполнения
      o pipefail - возвращает код возврата набора/последовательности команд, ненулевой при последней команды или 0 для успешного выполнения команд.
      ```
    * Для сценария повышает деталезацию вывода ошибок и завершит сценарий при наличии ошибок, на любом этапе выполнения сценария.
9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе?
    * `S, Ss, Ssl, Ss+` - процессы ожидающие завершения (спящие с прерыванием "сна")
    * `I, I<` - фоновые(бездействующие) процессы ядра