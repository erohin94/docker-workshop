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

Мы можем перезапустить один из них, но не будем этого делать, потому что это не очень хорошая практика. Они занимают много места, поэтому удалим их: ```docker rm `docker ps -aq````

Команда `docker ps -aq` просто выведет идентификаторы контейнеров.
