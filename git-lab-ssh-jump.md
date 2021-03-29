# Доступ к GitLab через ssh jump host



Всё, что написано ниже работает при условии, что у вас на рабочем ПК стоит линукс и СИБ согласовала вам доступ до него по 22 порту. Так-же на вашем рабочем ПК должен работать sshd.

Для удобства работы настройте аутентификацию на своеем рабочем ПК через ключи. Для этого нужно сгенерировать новые ключи (если их у вас еще нет) командой:

```
[cyfive@b87ef1c83773 /]# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/cyfive/.ssh/id_rsa):
Created directory '/home/cyfive/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/cyfive/.ssh/id_rsa.
Your public key has been saved in /home/cyfive/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:H8hDDAGmhKxuS6SubJVvjvueJDEdEbNKCfI9l0U/aHQ root@b87ef1c83773 
The key's randomart image is:
+---[RSA 2048]----+
|oo. o=+o+ E      |
|o+.+. +* +       | 
|. ooooo = o      | 
|... oo.+ . .     | 
|+  +..  S .      | 
|.+ oo    o .     | 
|+ o...    .      | 
|.+  +o.          | 
|+. o*=           |
+----[SHA256]-----+
[cyfive@b87ef1c83773 /]# 
```

Добавляем ключи на ваш рабочий ПК командой ssh-copy-id:

```bash
[cyfive@cyfive-book-03 develop]$ ssh-copy-id <ваше имя для доступа по SSH>@<ip адрес вашего рабочего ПК>
```

Теперь нужно создать файл **`~/.ssh/config`** следующего содержания:

```
Host work
    HostName <ip адрес вашего рабочего ПК>
    ForwardAgent yes
    User <ваше имя для доступа по SSH>
    IdentityFile ~/.ssh/id_rsa
Host gitlab.fciit.ru
    ProxyCommand ssh work -W [%h]:%p
```

Небольшие пояснения, в первой секции **`Host`** мы описываем быстрый доступ до нашего рабочего ПК, что-бы не вводить постоянно пароли и т.п., теперь при соединении с ним достаточно указать **`ssh work`**. Во второй секции мы описываем доступ до **GitLab** с проксированием через наш рабочий ПК. После этого можно будет пользоваться репозиториями в GitLab через ssh.

