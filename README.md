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

Создадим директорию pipeline. Внутри нее создадим файл pipeline.py

```
import sys
print("arguments", sys.argv)

day = int(sys.argv[1])
print(f"Running pipeline for day {day}")
```

Когда запускаем код, все что передаем в консоль, передается в `sys.argv`. Первое значение в выводимом списке это название скрипта, второе и последующее, это тоже мы передали.

<img width="774" height="257" alt="image" src="https://github.com/user-attachments/assets/ad848a5d-d636-46a2-9f8f-76dd8066a598" />

Таким образом можно параметризировать пайплан, тоесть передавать тот мемсяц или день который нам нужен.

Добавим pandas в глобальную среду `pip install pandas`, для тестирования кода:

```
import pandas as pd

df = pd.DataFrame({"A": [1, 2], "B": [3, 4]})
print(df.head())

df.to_parquet(f"output_day_{sys.argv[1]}.parquet")
```

Чтобы код сработал и файл сохранился в паркет, надо установить pyarrow. Можно установить с помощью pip и тогда он устанавливается глобально в вашей системе. 
Это может привести к конфликтам, если разным проектам требуются разные версии одного и того же пакета.

`pip install pandas pyarrow`

Вместо этого мы хотим использовать виртуальную среду - изолированную среду Python, которая хранит зависимости для этого проекта отдельно от других проектов и от вашего системного Python.

Мы будем использовать `uv` - современный, быстрый пакет для Python и менеджер проектов, написанный на Rust. Он намного быстрее, чем `pip`, и автоматически обрабатывает виртуальные среды.

`pip install uv`

<img width="694" height="121" alt="image" src="https://github.com/user-attachments/assets/6eb53316-25f5-4ce5-b58a-77f9c8e5e28f" />

Теперь инициализируем проект на Python с помощью uv:

`uv init --python=3.13`

<img width="242" height="52" alt="image" src="https://github.com/user-attachments/assets/08e21c6a-622c-45a0-ab6a-99073bc34d05" />

Видим что появилась структура папок. При этом создается файл `pyproject.toml` для управления зависимостями и файл `.python-версии`.

<img width="263" height="136" alt="image" src="https://github.com/user-attachments/assets/6cd57c66-bae8-494a-aa1f-bf159291d429" />

Сравним версии Python:

```
uv run which python  # Python in the virtual environment
uv run python -V

which python        # System Python
python -V
```

Версия python в системе

<img width="346" height="71" alt="image" src="https://github.com/user-attachments/assets/7fc508fa-a01a-4775-aff1-7fa5da38beb0" />

Версия python в виртуальном окружении

<img width="423" height="137" alt="image" src="https://github.com/user-attachments/assets/44a69643-d59b-47cf-b900-f9b9fcecac48" />

Так же при запуске команды `uv run which python` создалось виртуальное окружение и появилась папка venv

<img width="255" height="328" alt="image" src="https://github.com/user-attachments/assets/6008e0f3-b03d-4af5-ad9f-53e0865b40bf" />

Теперь чтобы работать с изолированной средой, буду использовать `uv`.

`uv run` -  использует изолированную среду.

Добавим pandas и pyarrow

`uv add pandas pyarrow`

После того как установили библиотеки, в файле `pyproject.toml` добавились зависимости

<img width="787" height="268" alt="image" src="https://github.com/user-attachments/assets/51bc89ce-67bd-4338-b220-960db829c18d" />

Запускаю код:

```
import sys
import pandas as pd

print("arguments", sys.argv)
day = int(sys.argv[1])
df = pd.DataFrame({"A": [1, 2], "B": [3, 4]})
print(df.head())
df.to_parquet(f"output_day_{sys.argv[1]}.parquet")
print(f"Running pipeline for day {day}")
```

И вижу наш паркет файл

<img width="995" height="449" alt="image" src="https://github.com/user-attachments/assets/ae83fa93-593d-4e57-8de2-122ea697d684" />

Обновим файл `.gitignore` чтобы в гитхаб не заливались файлы формата `parquet`. Пропишем `*.parquet` и сохраним файл. После сохранения все файлы формата .parquet, слева на панели будут выделятся серым цветом.

<img width="950" height="386" alt="image" src="https://github.com/user-attachments/assets/bbe3877e-48bc-455f-b379-d1f1a3677155" />

После сделаем коммит

```
git status
git add .
cd ..
git add .
git status
git commit -m 'pipline'
```

<img width="682" height="680" alt="image" src="https://github.com/user-attachments/assets/43a0527c-2bd5-4f66-a331-a9cda69abb7f" />

# Dockerizing the Pipeline

Теперь поместим скрипт в контейнер. Создадим следующий файл Dockerfile:

```
# base Docker image that we will build on
FROM python:3.13.11-slim

# set up our image by installing prerequisites; pandas in this case
RUN pip install pandas pyarrow

# set up the working directory inside the container
WORKDIR /app
# copy the script to the container. 1st name is source file, 2nd is destination
COPY pipeline.py pipeline.py

# define what to do first when the container runs
# in this example, we will just run the script
ENTRYPOINT ["python", "pipeline.py"]
```

**Объяснение:**

- `FROM`: Базовый образ, на чем основывается образ (Python 3.13)
- `RUN`: Выполнение команд во время сборки, установка pandas и pyarrow
- `WORKDIR`: Устанавливаю рабочую директорию
- `COPY`: Копирую файлы в образ, тоесть скопировал pipline.py в рабочий каталог /app в докере
- `ENTRYPOINT`: Команда для запуска по умолчанию

**Создадим образ:**

`docker build -t test:pandas .`

`-t` - это тег. `test` — имя образа. `pandas` — тег (версия). Если тег не указан, по умолчанию будет использоваться значение latest. `COPY <source> <destination>` - копирую файл локально: pipeline.py в контейнер: /app/pipeline.py

<img width="902" height="510" alt="image" src="https://github.com/user-attachments/assets/0c1d7e11-7813-4640-be79-21823e84b8f7" />

Теперь можно запустить контейнер и передать ему аргумент, чтобы наш конвейер получил его:

`docker run -it test:pandas some_number` где `some_number` какое то число

<img width="240" height="105" alt="image" src="https://github.com/user-attachments/assets/4a87ba4d-d304-4c53-aa4d-42dc072011a7" />

Должны получить тот же результат, что и при самостоятельном запуске конвейерного скрипта.

*Примечание: в этих инструкциях предполагается, что `pipeline.py` и `Dockerfile` находятся в одном каталоге. Команды `Docker` также должны запускаться из того же каталога, что и эти файлы.*

Каждый `docker run` создаёт новый контейнер с чистой файловой системой. Файл существует только внутри того конкретного контейнера. То есть запустили два раза, создалось два контейнера

<img width="883" height="71" alt="image" src="https://github.com/user-attachments/assets/8aa2ef27-108c-4ead-af16-74b841860a41" />

`docker run -it --entrypoint=bash --rm test:pandas`

Когда использую эту команду, то по заверншению работы с контейнером, все файлы удалятся. Видно, что находимся в каталоге /app который указали выше в докер файле.
И когда выполняю `ls` то видим файл pipline.py который сохранили. Но файла parquet ещё нет, потому что не запускали скрипт.

Что здесь происходит:

`docker run` — создаёт новый контейнер

`-it` — интерактивный режим (можно вводить команды)

`--entrypoint=bash` — переопределяет `ENTRYPOINT` из `Dockerfile`
(вместо `python pipeline.py` запускается bash)

`--rm` — контейнер удалится автоматически, файл исчезнет, когда ты выйдешь

`test:pandas` — образ

Тоесть мы оказались внутри контейнера:`root@<container_id>:/app#`

<img width="376" height="51" alt="image" src="https://github.com/user-attachments/assets/0e61d0e3-8e9a-4da1-8f3a-f57157130fe8" />

Чтобы увидеть parquet файл, внутри контейнера надо выполнить: `python pipeline.py 12`. Скрипт отработал и создал файл внутри контейнера.

<img width="458" height="154" alt="image" src="https://github.com/user-attachments/assets/38f925f7-9299-4cb9-a7e6-43bb566121e7" />

Теперь можно выйти из контейнера `exit`

<img width="220" height="51" alt="image" src="https://github.com/user-attachments/assets/b91ba281-2e34-42f4-977b-de8e7320b449" />

Посмотерть список всех запущенных контейнеров: `docker ps` - покажет контейнеры, которые сейчас работают.

Список всех контейнеров (включая остановленные): `docker ps -a`

Увидим контейнеры которые сосздали ранее выполняя команду `docker run` два раза.

<img width="923" height="126" alt="image" src="https://github.com/user-attachments/assets/e08d2a18-6d02-4e78-8201-50d213f07c01" />

Удалим контенеры: `docker rm <container_id или имя>`

<img width="924" height="189" alt="image" src="https://github.com/user-attachments/assets/01dfaf6f-a215-41d0-b103-bf562dc1cf90" />

Если запустить сборку еще раз то увидим `CACHED`, это значит что команды не будут выполняться плвторно. Докер воспользуется тем что уже было выполнено. Это экономит время.

<img width="910" height="305" alt="image" src="https://github.com/user-attachments/assets/59d0cc06-557a-490a-a9fb-c5d7f8202ca5" />

49:00
