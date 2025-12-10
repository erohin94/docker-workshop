# docker-workshop

Workshop Codespaces

Создаю репозиторий, в gitignore добавляю python.

Для работы буду использовать GitHub Codespaces - это облачная среда разработки (VS Code в браузере), которая запускается прямо в GitHub.
Там есть полностью настроенный контейнер Linux со всем, что нужно для разработки: Python, Docker, Git, расширения VS Code, терминал и т. д..

<img width="896" height="424" alt="image" src="https://github.com/user-attachments/assets/6db5cabc-bc0c-4bf0-945e-87d3699174c1" />

Видим как в браузере открылся VS Code.

<img width="1916" height="919" alt="image" src="https://github.com/user-attachments/assets/68c91b36-3ddf-4d90-a2ca-8648fe6aad5b" />

Можно работать прям в браузере, но лучше открыть через VS Code установленный локально. Для этого кликаю в левый нижний угол и выбираю открыть VS Code Descktop.

<img width="894" height="629" alt="image" src="https://github.com/user-attachments/assets/16ecb5f4-2894-44c5-b41b-4321eb6a7eef" />

Открыли VS Code на компьютере

<img width="2209" height="1041" alt="image" src="https://github.com/user-attachments/assets/26c07899-009c-439c-a2ed-666e6fa928c4" />

Проверить версию python 

<img width="498" height="53" alt="image" src="https://github.com/user-attachments/assets/f3e663b1-2c67-4b7f-81f3-5f59ea4bb02e" />

Ввожу команду (как в Linux) `PS1="> "` чтобы сделать строку короче.

<img width="433" height="69" alt="image" src="https://github.com/user-attachments/assets/b645d126-13ea-487c-8ea5-0aeaffda801c" />

Когда запускаю новый терминал, строка становится опять длинной. Чтобы постоянно это не делать, просто пропишем в bashrc. Прописываю команду `echo 'PS1="> "' > ~/.bashrc`.

Файл .bashrc — это конфигурационный файл настроек оболочки Bash, который запускается каждый раз, когда открываешь новый интерактивный терминал Bash (например, в Linux, macOS или внутри Codespaces/Docker-контейнера).
Через него можно менять поведение терминала под себя.

<img width="444" height="88" alt="image" src="https://github.com/user-attachments/assets/5ef0507c-9ede-4bef-a9a7-b874024ea984" />

Теперь когда запустили новый терминал, у нас короткая строка (просто галочка, а раньше было `@erohin94 ➜ /workspaces/docker-workshop (main) $`).

<img width="1454" height="301" alt="image" src="https://github.com/user-attachments/assets/33f38545-74b9-40ff-b049-38e1400486c2" />
