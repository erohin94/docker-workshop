# Docker-workshop

Workshop Codespaces

# Docker and PostgreSQL: Data Engineering Workshop

Рассмотрим основы Docker и рабочие процессы разработки данных с использованием контейнеров Docker.

**Мы рассмотрим:**

- Введение в Docker и контейнеризацию
- Запуск PostgreSQL в контейнере Docker
- Загрузка данных в PostgreSQL
- Работа с pgAdmin для управления базами данных
- Сеть Docker и сопоставление портов
- Docker Compose для приложений с несколькими контейнерами
- Создание конвейера загрузки данных
- SQL обновление с использованием реальных данных
- Лучшие практики для рабочих процессов инженерии данных в контейнерах

# Первоначальная настройка

Создаю репозиторий, при создании в gitignore добавляю python.

Для работы буду использовать GitHub Codespaces - это облачная среда разработки (VS Code в браузере), которая запускается прямо в GitHub.
Там есть полностью настроенный контейнер Linux со всем, что нужно для разработки: Python, Docker, Git, расширения VS Code, терминал и т. д..

<img width="471" height="439" alt="image" src="https://github.com/user-attachments/assets/878e5d8d-ee97-48d3-a1b2-70fb0bab15ae" />

Видим как в браузере открылся VS Code.

<img width="1916" height="919" alt="image" src="https://github.com/user-attachments/assets/68c91b36-3ddf-4d90-a2ca-8648fe6aad5b" />

Можно работать прям в браузере, но лучше открыть через VS Code установленный локально. Для этого кликаю в левый нижний угол и выбираю открыть VS Code Descktop.

<img width="894" height="629" alt="image" src="https://github.com/user-attachments/assets/16ecb5f4-2894-44c5-b41b-4321eb6a7eef" />

Открыли VS Code на компьютере

<img width="2209" height="1041" alt="image" src="https://github.com/user-attachments/assets/26c07899-009c-439c-a2ed-666e6fa928c4" />

Можем проверить версию python.

<img width="498" height="53" alt="image" src="https://github.com/user-attachments/assets/f3e663b1-2c67-4b7f-81f3-5f59ea4bb02e" />

Ввожу в терминале команду (как в Linux) `PS1="> "` чтобы сделать строку, которую Bash показывает перед вводом команды, короче (будет просто галочка).

<img width="433" height="69" alt="image" src="https://github.com/user-attachments/assets/b645d126-13ea-487c-8ea5-0aeaffda801c" />

Когда запускаю новый терминал, можно заметить что строка становится опять длинной. Чтобы постоянно не вводить команду для сокращения отображения промта баша, просто пропишем в bashrc команду. Прописываю команду `echo 'PS1="> "' > ~/.bashrc`.

Файл .bashrc — это конфигурационный файл настроек оболочки Bash, который запускается каждый раз, когда открываешь новый интерактивный терминал Bash (например, в Linux, macOS или внутри Codespaces/Docker-контейнера).
Через него можно менять поведение терминала под себя.

<img width="444" height="88" alt="image" src="https://github.com/user-attachments/assets/5ef0507c-9ede-4bef-a9a7-b874024ea984" />

Теперь когда запустили новый терминал, у нас короткая строка (просто галочка, а раньше было `@erohin94 ➜ /workspaces/docker-workshop (main) $`).

<img width="1454" height="301" alt="image" src="https://github.com/user-attachments/assets/33f38545-74b9-40ff-b049-38e1400486c2" />

Этого можно не делать, но так выглядит не так заграможденно.

# Введение в Docker

Docker — это программное обеспечение для контейнеризации, которое позволяет изолировать программное обеспечение так же, как это делают виртуальные машины, но с гораздо меньшими затратами.

Образ Docker — это моментальный снимок контейнера, который мы можем использовать для запуска нашего программного обеспечения или, в данном случае, наших конвейеров данных. Экспортируя наши образы Docker в облачные сервисы, такие как Amazon Web Services или Google Cloud Platform, мы можем запускать там наши контейнеры.

**Почему Docker?**

Docker обладает следующими преимуществами:

- Воспроизводимость: везде одинаковая среда.
- Изоляция: приложения работают независимо друг от друга.
- Портативность: можно запускать в любом месте, где установлен Docker.

Docker используется во множестве ситуаций:

- Интеграционные тесты: CI/CD конвейеры
- Запуск пайплайнов в облаке: AWS Batch, Kubernetes Jobs
- Spark: аналитический движок для обработки больших данных
- Serverless: AWS Lambda, Google Cloud Functions

Вводим команду и видим что Docker установлен.

<img width="589" height="329" alt="image" src="https://github.com/user-attachments/assets/732b1140-0736-4010-9db9-60a0ef924918" />

Все что находится внутри контейнера Docker, полностью изолированно от того что есть на нашем хост компьютере. Можно все удалить внутри контейнера и это никак не повлияет на наш хост компьютер.

Запустить простой контейнер: `docker run hello-world`

<img width="648" height="445" alt="image" src="https://github.com/user-attachments/assets/40a1cb0b-719c-4b10-8dfb-5b41c19bd447" />

Запустить что-нибудь более сложное: `docker run ubuntu`

<img width="580" height="107" alt="image" src="https://github.com/user-attachments/assets/5218a446-2059-41d3-b8c4-13e21730f99b" />

Загрузили образ Ubuntu, но ничего непроизошло. Так как мы запустили образ Docker, что то выполнилось и мы вернулись снова к нашему компьютеру. Поэтому можем провалится внутрь контейнера Ubuntu.

Ничего не происходит. Необходимо запустить его в -it режиме: `docker run -it ubuntu`

<img width="166" height="35" alt="image" src="https://github.com/user-attachments/assets/3b0bf8fe-d563-4cf5-8537-48f2d6194d8b" />

<img width="722" height="35" alt="image" src="https://github.com/user-attachments/assets/e7da0bfb-9e17-47ca-afa6-1cf54b773230" />

Теперь все делаем изолированно от хост машины.

Проверить установлен ли puthon: `python3`

У нас там нет python, так что установим его: `apt update && apt install python3`

Проверяем: `python3 -V`

Это та версия которая установлена внутри Docker

<img width="595" height="94" alt="image" src="https://github.com/user-attachments/assets/ba87f7ca-5914-487d-8800-3e2e643fdf29" />

При установке попросит нажать `Do you want to continue? [Y/n] Y` и выбрать регион, нажимаем просто везде `1`. Либо `8` потом `1`.

<img width="1443" height="276" alt="image" src="https://github.com/user-attachments/assets/985136c2-3e74-4ee9-b135-02ea69513aa5" />

Проверка

<img width="244" height="37" alt="image" src="https://github.com/user-attachments/assets/a3eb82fa-f7a9-4f24-bc34-fc58e2409662" />

Тепрь нажимаю `Ctrl+D` и выхожу из контейнера. И снова оказываюсь на своем хост компьютере.

<img width="214" height="50" alt="image" src="https://github.com/user-attachments/assets/a38fe3fe-340b-447a-ab15-083587cd5ab5" />

Если захочу опять вернуться в контейнер, то ввожу `docker run -it ubuntu` и проверяю версию python. Вижу что команда не найдена. Хотя ранее устанавливали его.

<img width="291" height="103" alt="image" src="https://github.com/user-attachments/assets/34daddc1-d67c-4180-9415-a65a3de05069" />

Важно: Контейнеры Docker не имеют состояния - любые изменения, внесенные внутри контейнера, не будут сохранены при завершении работы контейнера и его повторном запуске. Поэтому опять начинаем с нуля. Когда выходим из контейнера и используем его снова, изменения исчезают.

Каждый раз когда мы заходим в контейнер Docker. Мы запускаем контейнер, мы создаем контейнер из его из образа Docker image.

Когда запускаем `docker run` создается экземпляр этого образа, тоесть контейнер docker. Этот образ содержит полный снимок всей опреационной системы. Поэтому на основе этого образа создается контенейр, тоесть это некая интрукция. Поэтому когда выходим из контейнера, состояние не сохраняется. 

Запустим другой образ Docker: `docker run -it python:3.13.11`. Вместо ubuntu используем этот образ докер. В этом образе докер установлен `python:3.13.11`.

<img width="638" height="256" alt="image" src="https://github.com/user-attachments/assets/fc5bac53-cb0c-4900-a436-547a41f8eea1" />

Этот образ довольно большой, поэтому можно использовать slim версию: `docker run -it python:3.13.11-slim`

<img width="578" height="227" alt="image" src="https://github.com/user-attachments/assets/63627f0c-54cd-4584-9056-2aada26724fc" />

Раньше у нас был bash терминал, сейчас у нас есть приглашение python (три розовые галочки). Поэтому можем выполнить какой то python код.

<img width="177" height="36" alt="image" src="https://github.com/user-attachments/assets/9bfe6b30-d1fb-4081-aa29-38add7dd178d" />

Если захотим запустить bash вместо python, то надо изменить точку входа: `docker run -it --entrypoint=bash python:3.13.11-slim` и теперь будем находится в образе докер python:3.13.11-slim но с доступом к bash.

<img width="817" height="154" alt="image" src="https://github.com/user-attachments/assets/665571e1-3df4-42f8-b4d0-cd1d65254806" />

Видим что это та версия которая установлена ранее.

Можно создать файл `echo 123 > file` - перенаправляю вывод текста 123 в файл вместо терминала. Проверить что он создался `ls`, проверить его содержимое `cat file`, после чего выйти из контейнера и заново зайти, то этого файла уже не будет.

<img width="862" height="221" alt="image" src="https://github.com/user-attachments/assets/eddab13c-a3a1-4765-86c0-1899eb41963d" />

Ранее говорили что контейнеры не сохраняют состояние и данные, то это не совсем так. Когда выполнили команду run, версия контейнера все равно где то сохранилась. Так что потенциально мы можем продолжить работу, над тем что было создано, но не должны этого делать, так как это антипатерн.

Но если надо продолжить, выполняем: `docker ps -a` чтобы проверить остановленные контейнеры. После чего смогу продолжить работу с файлами которые были созданы.

<img width="1012" height="225" alt="image" src="https://github.com/user-attachments/assets/0273554c-11c5-4efe-bfe5-32d67abec987" />

Мы можем перезапустить один из них, но не будем этого делать, потому что это не очень хорошая практика. Они занимают много места, поэтому удалим их: 
```
docker rm `docker ps -aq`
```
Команда `docker ps -a` просто выведет идентификаторы контейнеров.

<img width="710" height="362" alt="image" src="https://github.com/user-attachments/assets/4a907dae-1994-4245-a478-b304abbda64b" />

Очистили нашу среду.

Теперь допутим хотим сохранить состояние, что надо сделать? Представим что есть папка test и в ней что то есть (file1.txt, file2.txt, file3.txt). В файле file1.txt есть текст. Остальные файлы пустые

```
mkdir test
cd test
touch file1.txt file2.txt file3.txt
echo "Hello from host" > file1.txt
```

<img width="320" height="172" alt="image" src="https://github.com/user-attachments/assets/728c25d5-2ef3-465a-b300-c549d7e3ddfb" />

Нам надо получить доступ к этим файлам из нашего контейнера Docker. Для этого надо создать скрипт чтобы запускать из контейнера докер. 

Но для начала создадим простой скрипт test/script.py, который запускается локально на нашем компьютере и показывает файлы в папке, позже будем запускть его внтури контейнера Docker:

```
from pathlib import Path

current_dir = Path.cwd()
current_file = Path(__file__).name

print(f"Files in {current_dir}:")

for filepath in current_dir.iterdir():
    if filepath.name == current_file:
        continue

    print(f"  - {filepath.name}")

    if filepath.is_file():
        content = filepath.read_text(encoding='utf-8')
        print(f"    Content: {content}")
```

<img width="916" height="557" alt="image" src="https://github.com/user-attachments/assets/ace50221-0c87-441f-9483-f5a09aa1bbba" />

Теперь мы хотим запустить этот скрипт внутри Docker. Используем тома. Поднимемся на уровень выше `cd..`. Сейчас нахожусь в корневом каталоге.

<img width="230" height="87" alt="image" src="https://github.com/user-attachments/assets/5b6f96ac-d714-40f9-b053-7d8c7e57243d" />

Таким образом папка test будет доступна как на хост машине, так и внутри докер контейнера. Тоесть все что находится в папке test, будет достпуно внутри контейнера докер. Для этого использую последнюю команду докер.

`docker run -it --entrypoint=bash -v $(pwd) python:3.13.11-slim`

Эта команда запускает интерактивный контейнер из образа python:3.13.11-slim и сразу открывает в нём bash-терминал (вместо стандартного запуска Python).
Флаг -v $(pwd) монтирует текущую директорию хоста в контейнер как volume, чтобы можно было работать с файлами из неё внутри контейнера.

<img width="1005" height="507" alt="image" src="https://github.com/user-attachments/assets/0a8504e0-e161-4238-b204-1e5727ac0bd9" />

<img width="496" height="87" alt="image" src="https://github.com/user-attachments/assets/b052b36f-0896-45f5-b861-606ae3db6473" />

Отобразим содержимое папки test:

Сейчас мы внутри контейнера, поэтому сначала выйду из него `exit` затем ввожу команду

`docker run -it --entrypoint=bash -v $(pwd)/test:/app/test python:3.13.11-slim`

Эта команда запускает интерактивный контейнер из образа python:3.13.11-slim, открывая внутри bash.
Каталог $(pwd)/test с хоста монтируется в контейнер по пути /app/test, поэтому файлы из этой папки доступны и изменяемы внутри контейнера. 

<img width="773" height="103" alt="image" src="https://github.com/user-attachments/assets/5ea00280-8fc5-49ab-9ad6-bfafebb65630" />

Видим папку app, захожу в нее `cd app`. А затем в папку `test`.

<img width="331" height="118" alt="image" src="https://github.com/user-attachments/assets/ab5f62ed-1794-49fb-8a34-fbf26b78310d" />

Видим все файлы, которые могу запустить с помощью python, так как в этом контейнере есть python:

<img width="356" height="203" alt="image" src="https://github.com/user-attachments/assets/b679415f-43bb-4bb2-9db4-618d9681dab9" />

То есть если у нас есть какие то файлы на нашем хост компьютере, которые мы хотим сделать доступными в контейнере, мы можем это легко сделать.

Теперь попробуем все снести внутри контейнера, для этого выполняю команду:

`rm -rf /` - Команда пытается удалить абсолютно всё: систему, библиотеки, бинарники, конфиги, данные.

<img width="377" height="86" alt="image" src="https://github.com/user-attachments/assets/caea8346-2376-45a7-8661-22e5750c393a" />

И вводим еще раз `rm -rf --no-preserve-root /` и нажимаю enter.

Проверяем и видим что ничего не работает, так как все было удалено, сломали нашу систему. 

<img width="689" height="113" alt="image" src="https://github.com/user-attachments/assets/0383f20e-23fb-4df3-95e8-ae5d444c8677" />

Но так как мы в докер, то ничего страшного не произошло. Поэтому просто выходим `exit` и заново запускаем `docker run -it --entrypoint=bash -v $(pwd)/test:/app/test python:3.13.11-slim`

<img width="426" height="64" alt="image" src="https://github.com/user-attachments/assets/67a0360e-9c86-4dd5-ad24-aadae071da91" />

<img width="762" height="69" alt="image" src="https://github.com/user-attachments/assets/5f12c1fa-1789-46cc-9c36-a7733e350d15" />

# Виртуальная среда и конвейеры передачи данных (Data Pipelines)

**Конвейер передачи данных (Data Pipelines)** - это сервис, который принимает данные в качестве входных данных и выводит выходные данные. Например, считывает CSV-файл, каким-либо образом преобразует данные и сохраняет их в виде таблицы в базе данных PostgreSQL

<img width="775" height="336" alt="image" src="https://github.com/user-attachments/assets/913cf971-f3bb-4161-8747-1e22bfe3de37" />

Использую [датасет nyc taxi](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

Мы создадим конвейеры, которые:

- Загружают данные в формате CSV из Интернета
- Преобразуют и очищают данные с помощью pandas
- Загружают их в PostgreSQL для запросов
- Обрабатывают данные по частям для обработки больших файлов
