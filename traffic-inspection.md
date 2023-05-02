# Выживаем с инспекцией трафика

Тебе не повезло и на твоей галере используют инспекцию https трафика? По сути DPI система при инспекции  https трафика проводит MITM атаку, а так как с этим видом атак пытаются бороться, то у тебя нормально не будет работать куча нужного софта. Ниже я расскажу, как пофиксить частые ошибки в некоторых популярных программных продуктах.

## Установка корневого доверенного сертификата в Linux (CentOS, Fedora)

Копируем предоставленный сертификат в **`/etc/pki/ca-trust/source/anchors/`** и выполняем команду:

```bash
sudo update-ca-trust
```

## WGET

`wget` запускаем с ключиком **`--no-check-certificate`**, пример:

```bash
[semets@my-pc conf]$ wget https://example.com/awesome.zip --no-check-certificate
```

## YUM/DNF

В конфигурацию репозитория для **`yum/dnf`** добавляем **`sslverify=0`**, пример:

```bash
name=Fedora $releasever - $basearch
#baseurl=http://download.fedoraproject.org/pub/fedora/linux/releases/$releasever/Everything/$basearch/os/ 
metalink=https://mirrors.fedoraproject.org/metalink?repo=fedora-$releasever&arch=$basearch
enabled=1
metadata_expire=7d 
repo_gpgcheck=0
type=rpm
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$releasever-$basearch 
skip_if_unavailable=False
sslverify=0
```

## Visual Studio Code (VSCode, VSCodium)

Нужно запускать его с ключом: **`--ignore-certificate-errors`**

Для VSCodium (бинарная сборка для линукса) нужно отредактировать файл /usr/share/applications/codium.desktop приведя его к такому виду:

```
[Desktop Entry]
Name=VSCodium
Comment=Code Editing. Redefined.
GenericName=Text Editor
Exec=/usr/share/codium/codium --ignore-certificate-errors --no-sandbox --unity-launch %F
Icon=vscodium
Type=Application
StartupNotify=false
StartupWMClass=VSCodium
Categories=Utility;TextEditor;Development;IDE; 
MimeType=text/plain;inode/directory;
Actions=new-empty-window;
Keywords=vscode;

[Desktop Action new-empty-window]
Name=New Empty Window 
Exec=/usr/share/codium/codium --ignore-certificate-errors --no-sandbox --new-window %F 
Icon=vscodium
```

Стоит отметить, что в линукс десктоп файл находится под управлением пакетного менеджера со всеми вытекающими из этого последствиями.

## Google Chrome (Chromium)

Если при работе c Google Chrome вы получаете ошибку:

```
NET::ERR_CERT_WEAK_SIGNATURE_ALGORITHM
```

то вам по аналогии с приложениями написанными с использованием Electron, нужно запускать Google Chrome с ключом **`--ignore-certificate-errors`**, иначе у вас ничего не будет нормально открываться.

## PIP (Python)

Для корректной работы pip нужно указывать `**--trusted-host**` для того, что-бы **pip** начал доверять нашему самоподписному сертификату, рабочий пример у меня выглядит так:

```bash
(zabbix-cli-venv) [semets@my-pc my-project]$ pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org pyzabbix
```