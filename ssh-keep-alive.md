# Сохранение соединения по SSH

Если у вас проблема с разрывом соединения **ssh** по бездействию, то нужно создать файл **`~/.ssh/config`** следующего содержания:

```
Host *
    ServerAliveInterval 600
```