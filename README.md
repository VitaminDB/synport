<img src="synport-128.png" alt="synport" width="96" align="right">

# synport

**Arch Linux ARM next to Android on phones and tablets — one USB installer for Windows and Linux (adb + fastboot).**

At power-on you pick the system with the volume keys: **Vol+ — Arch Linux, Vol− — Android**. Android, its apps
and data stay where they are. The system runs with GPU acceleration and the synshell touch desktop; no TWRP, no root.

| Device | Codename | Status |
|---|---|---|
| Xiaomi 12T Pro / Redmi K50 Ultra | `diting` | **released** — the first device (stock Android GKI kernel) |
| ONYX BOOX Note Air 4C | `noteair4c` | ported, release coming |
| Samsung Galaxy Tab A 8.0 (2019) LTE, SM-T295 | `gto` | ported, release coming |

More devices will follow — see [Your phone](#your-phone).

[Русский](#по-русски) · [Requirements](#requirements) · [Install](#install-in-3-steps) ·
[What works](#what-works-xiaomi-12t-pro) · [Known issues](#known-issues-xiaomi-12t-pro) · [Remove](#remove) · [Downloads](#downloads) ·
[Your phone](#your-phone)

**Video:** [installation from Windows, start to desktop (≈4 min)](https://youtu.be/a1eQKbndKZ0)

This repository holds the releases only: the installer for Windows and Linux and the system image.
Bug reports and questions — [Issues](../../issues), ideas and requests for other phones —
[Discussions](../../discussions). Website: https://alexeyev.dev/synport/

## Requirements

Xiaomi 12T Pro / Redmi K50 Ultra (first device):

| | |
|---|---|
| Phone | Xiaomi 12T Pro or Redmi K50 Ultra (`diting`) |
| Android on it | HyperOS **`OS2.0.5.0.VLFCNXM`** (China, Android 15). The installer checks it first and tells you which ROM to flash if it differs |
| Bootloader | **unlocked** (official *Mi Unlock*; unlocking wipes the phone) |
| Computer | Windows 10/11 or Linux x86_64, USB cable |
| Free space on the phone | about 20 GB |

No TWRP, no root, no drivers to install by hand: `adb` and `fastboot` are downloaded by synport if missing, and on
Windows synport binds the WinUSB driver that comes with Windows to the phone in fastboot mode (one administrator
prompt).

The image contains **no proprietary files** of Xiaomi or Qualcomm: on first boot the phone takes its drivers and
firmware from its own partitions. That is why the Android version must match exactly.

## Install in 3 steps

1. **Download and unpack** `synport-diting-<version>.zip` from [Releases](../../releases/latest)
   (synport for Windows and Linux plus the image folder; keep them together).
2. **Prepare the phone:** Settings → About phone → tap *OS version* 7 times; Additional settings → Developer options →
   *USB debugging* on. Connect the cable and allow this computer on the phone's screen.
3. **Run synport** (`synport.exe` on Windows, `./synport` on Linux), choose the apps you want, press **Install**.

The computer copies the image and starts the installer on the phone from RAM (`fastboot boot` — nothing is flashed
yet). The phone then works by itself for about 3 minutes: unpacks the system, takes its drivers from its own
partitions, builds a boot image with the Vol+/Vol− menu, flashes it and restarts into Arch Linux.
If anything fails before the boot image is flashed, the phone simply restarts into Android and synport shows the
reason. The whole installation takes about 10 minutes (most of it is copying 1.7 GB over USB).

Command line instead of the window: `synport install --release` (`--npu` — also the NPU runtime, see below).
`synport status` shows the installation log at any time.

## What works (Xiaomi 12T Pro)

- Touch screen, display 60/90/120 Hz, GPU acceleration (Vulkan / OpenGL, Mesa turnip)
- Wi-Fi, Bluetooth (with audio), mobile data, calls and SMS, GPS
- Sound: speakers, headphones, microphone
- Cameras (all five, including the 200 MP main camera)
- Sensors: rotation, light, proximity, compass; vibration, IR blaster, flashlight
- Suspend with the screen off, USB-OTG
- Android apps in a container, X11 apps, Windows programs and games via Wine (optional)
- NPU (Hexagon): optional, the Qualcomm AI Runtime is downloaded from Qualcomm's site at install
  (only the files the phone needs, read from the 1.5 GB archive in parts; you accept Qualcomm's license)
- Pairing with your computer (synlink): screen, input, files, notifications; on pairing the phone offers to take
  the Wi-Fi networks of the computer

## Known issues (Xiaomi 12T Pro)

- Only one ROM is supported: `OS2.0.5.0.VLFCNXM` (China). Global and EEA ROMs — not yet.
- Fingerprint reader — not supported.
- Apps chosen from the Arch repositories are installed on first boot **when the phone gets Internet** (Wi-Fi);
  until then their icons are not shown.
- The 200 MP main camera needs one file from Xiaomi's global OTA; synport takes it on the computer during the
  install. Without Internet on the computer the phone downloads it itself later over Wi-Fi.
- Steam (optional): the ARM64 Steam client itself is not in the release — it is downloaded from Valve on first launch
  (≈345 MB; you accept the Steam Subscriber Agreement). Windows games run through Wine.
- Steam, Wine and the other optional apps of the release can be chosen only during the installation for now.
- The bootloader stays unlocked (synport never locks it).

## Remove

Boot into Android (Vol− at power-on), connect the phone, run synport and press **Remove**
(or `synport uninstall`). synport offers to save your files (`/home`) to the computer, flashes the stock boot image
back (checked by sha256), deletes the system image and shows the result. Outside its own folder
(`/data/local/tmp/syn`) the installation changes only the boot partition, and that is restored.

Do **not** lock the bootloader with any modified partition: flash the full fastboot ROM first, then
`fastboot flashing lock` (it wipes the phone).

## Downloads

Each release:

| File | What |
|---|---|
| `synport-diting-<version>.zip` | everything for the installation: synport for Windows and Linux, the image folder |
| `synport-<version>-windows-x86_64.zip`, `synport-<version>-linux-x86_64.tar.zst` | synport alone (for `status`, `uninstall`) |
| `SHA256SUMS` | checksums |
| `gpl-<version>.tar.gz` | GPL sources: kernel configuration and revision, BusyBox, initramfs scripts, build files of the bundled LGPL packages |

## Your phone

synport is one installer for every device of the project; new devices come as separate releases. Ports are made
device by device and take from a week to months, so they are funded by the people who want them:

1. **Requests and votes first.** Ask for your model in the [device requests thread](https://github.com/VitaminDB/synport/discussions/1) (model, codename,
   `adb shell getprop` without serial numbers and accounts) and vote for the existing requests. During about a month
   after this release we collect requests.
2. **Then a fundraiser** for the 2–3 most requested models: the goal is from $5,000 (phone + work). If the goal is
   not reached, donors get a refund or move the money to another model. You can also send a phone (used is fine) —
   then only the work is funded. Companies can order a port; the price is set after studying the device.

What a port needs: an unlockable bootloader; Qualcomm with a GKI kernel or mainline support is best, MediaTek and
Exynos only after an assessment. A port goes in levels — basic (boot, screen, touch, Wi-Fi, USB), then full (sound,
camera, modem, sensors) — without a promise that every part of every phone will work. Details and the status of the
fundraisers: https://alexeyev.dev/synport/

## License

Free of charge, binary only — see [LICENSE.txt](LICENSE.txt). Redistribute only the unmodified release archive.
Third-party parts keep their licences: `THIRD-PARTY-LICENSES.html` (libraries in synport) and
`gpl-<version>.tar.gz` of every release (Linux kernel — Android GKI, GPL-2.0 — with its configuration and revision,
BusyBox, initramfs scripts, build files of the bundled packages). The image consists of Arch Linux ARM packages
under their own licences (sources — archlinuxarm.org). Proprietary firmware is not distributed: the phone uses its own.

---

## По-русски

**Arch Linux ARM рядом с Android на телефонах и планшетах — один установщик по USB для Windows и Linux (adb + fastboot).**

При включении система выбирается клавишами громкости: **Vol+ — Arch Linux, Vol− — Android**. Android, его
приложения и данные остаются на месте. Система работает с ускорением GPU и сенсорной оболочкой synshell; TWRP и root
не нужны.

| Устройство | Кодовое имя | Состояние |
|---|---|---|
| Xiaomi 12T Pro / Redmi K50 Ultra | `diting` | **релиз** — первое устройство (стоковое ядро Android GKI) |
| ONYX BOOX Note Air 4C | `noteair4c` | портирован, релиз готовится |
| Samsung Galaxy Tab A 8.0 (2019) LTE, SM-T295 | `gto` | портирован, релиз готовится |

Будут и другие устройства — см. [Ваш телефон](#ваш-телефон).

**Видео:** [установка с Windows от запуска до рабочего стола (≈4 мин)](https://youtu.be/a1eQKbndKZ0)

Здесь только релизы: установщик для Windows и Linux и образ системы. Ошибки и вопросы — в [Issues](../../issues),
идеи и заявки на другие телефоны — в [Discussions](../../discussions). Сайт: https://alexeyev.dev/synport/

### Что нужно

Для Xiaomi 12T Pro / Redmi K50 Ultra (первое устройство):

- Xiaomi 12T Pro или Redmi K50 Ultra (`diting`).
- Прошивка HyperOS **`OS2.0.5.0.VLFCNXM`** (Китай, Android 15). Установщик проверит её первой и подскажет, какую
  поставить, если версия другая.
- **Разблокированный загрузчик** (официальный *Mi Unlock*; разблокировка стирает телефон).
- Компьютер с Windows 10/11 или Linux x86_64, USB-кабель; ~20 ГБ свободной памяти на телефоне.

TWRP, root и ручная установка драйверов не нужны: adb и fastboot synport скачает сам, а на Windows для режима
fastboot назначит телефону встроенный в Windows драйвер WinUSB (один запрос администратора).

В образе **нет закрытых файлов** Xiaomi и Qualcomm: при первой загрузке телефон берёт драйверы и прошивки из своих
разделов. Поэтому версия Android должна совпадать точно.

### Установка в 3 шага

1. **Скачайте и распакуйте** `synport-diting-<версия>.zip` из [Releases](../../releases/latest) (synport для
   Windows и Linux и папка образа; держите их вместе).
2. **Подготовьте телефон:** Настройки → О телефоне → 7 раз по «Версия ОС»; Расширенные настройки → Для
   разработчиков → «Отладка по USB». Подключите кабель и разрешите этот компьютер на экране телефона.
3. **Запустите synport** (`synport.exe` на Windows, `./synport` на Linux), выберите программы и нажмите
   **«Установить»**.

Компьютер копирует образ и запускает установщик на телефоне из памяти (`fastboot boot`, пока ничего не
прошивается). Дальше телефон всё делает сам примерно за 3 минуты: распаковывает систему, берёт драйверы из своих
разделов, собирает boot с меню Vol+/Vol−, прошивает его и перезагружается в Arch Linux. Если что-то пошло не так до
прошивки boot, телефон просто вернётся в Android, а synport покажет причину. Вся установка — около 10 минут (больше всего
времени уходит на копирование 1,7 ГБ по USB).

Без окна: `synport install --release` (`--npu` — ещё и среда NPU). `synport status` — журнал установки.

### Что работает (Xiaomi 12T Pro)

- Сенсорный экран, дисплей 60/90/120 Гц, ускорение GPU (Vulkan / OpenGL, Mesa turnip)
- Wi-Fi, Bluetooth (и звук), мобильный интернет, звонки и SMS, GPS
- Звук: динамики, наушники, микрофон
- Камеры (все пять, включая основную 200 Мп)
- Датчики: поворот, освещённость, приближение, компас; вибромотор, ИК-передатчик, фонарик
- Сон с выключенным экраном, USB-OTG
- Приложения Android в контейнере, программы X11, программы и игры Windows через Wine (по выбору)
- NPU (Hexagon): по выбору, среда Qualcomm AI Runtime скачивается с сайта Qualcomm при установке
  (только нужные телефону файлы из архива в 1,5 ГБ, по частям; вы принимаете лицензию Qualcomm)
- Связь с компьютером (synlink): экран, ввод, файлы, уведомления; при спаривании телефон предлагает забрать сети
  Wi-Fi компьютера

### Известные ограничения (Xiaomi 12T Pro)

- Поддерживается одна прошивка: `OS2.0.5.0.VLFCNXM` (Китай). Глобальная и EEA — пока нет.
- Сканер отпечатков не поддерживается.
- Программы из репозиториев Arch, выбранные при установке, ставятся при первой загрузке, **когда у телефона
  появится интернет** (Wi-Fi); до этого их значков нет.
- Основной камере 200 Мп нужен один файл из глобальной OTA Xiaomi: synport берёт его на компьютере при установке.
  Нет интернета на компьютере — телефон скачает его сам позже по Wi-Fi.
- Steam (по выбору): самого клиента Steam для ARM64 в релизе нет — он скачивается с серверов Valve при первом
  запуске (≈345 МБ; вы принимаете Соглашение подписчика Steam). Игры для Windows идут через Wine.
- Steam, Wine и другие программы релиза на выбор пока можно поставить только при установке.
- Загрузчик остаётся разблокированным (synport его не блокирует).

### Удаление

Загрузите Android (Vol− при включении), подключите телефон, запустите synport и нажмите **«Удалить»**
(или `synport uninstall`). synport предложит сохранить ваши файлы (`/home`) на компьютер, вернёт стоковый boot
(сверка sha256), удалит образ системы и покажет итог. Вне своей папки (`/data/local/tmp/syn`) установка меняет
только раздел boot — он возвращается.

Не блокируйте загрузчик с изменёнными разделами: сначала полная прошивка fastboot-ROM, потом
`fastboot flashing lock` (стирает телефон).

### Файлы релиза

- `synport-diting-<версия>.zip` — всё для установки: synport для Windows и Linux, папка образа;
- `synport-<версия>-windows-x86_64.zip`, `synport-<версия>-linux-x86_64.tar.zst` — только synport (`status`, `uninstall`);
- `SHA256SUMS` — контрольные суммы;
- `gpl-<версия>.tar.gz` — исходники GPL: конфигурация и ревизия ядра, BusyBox, скрипты initramfs, файлы сборки
  LGPL-пакетов из релиза.

### Ваш телефон

synport — один установщик для всех устройств проекта; новые устройства выходят отдельными релизами. Порт делается под
каждое устройство отдельно и занимает от недели до месяцев, поэтому его оплачивают те, кому он нужен:

1. **Сначала заявки и голоса.** Попросите свою модель в [теме заявок на устройства](https://github.com/VitaminDB/synport/discussions/1) (модель, кодовое имя,
   `adb shell getprop` без серийных номеров и учётных записей) и голосуйте за существующие заявки. Примерно месяц
   после этого релиза собираем заявки.
2. **Потом сбор** на 2–3 самые востребованные модели: цель — от $5 000 (телефон + работа). Не набралось — деньги
   возвращаются или переносятся на другую модель по выбору донора. Можно прислать телефон (подойдёт б/у) — тогда
   сбор только на работу. Компании могут заказать порт; цена — после изучения устройства.

Что нужно для порта: разблокируемый загрузчик; лучше всего Qualcomm с ядром GKI или поддержкой mainline, MediaTek и
Exynos — только после оценки. Порт идёт уровнями — базовый (загрузка, экран, тач, Wi-Fi, USB), затем полный (звук,
камера, модем, датчики) — без обещания, что заработает каждый модуль каждого телефона. Подробности и состояние
сборов: https://alexeyev.dev/synport/

### Лицензия

Бесплатно, в двоичном виде — см. [LICENSE.txt](LICENSE.txt). Распространять можно только неизменённый архив
релиза. Сторонние части — под своими лицензиями: `THIRD-PARTY-LICENSES.html` (библиотеки в synport) и
`gpl-<версия>.tar.gz` каждого релиза (ядро Linux — Android GKI, GPL-2.0, — его конфигурация и ревизия, BusyBox,
скрипты initramfs, файлы сборки пакетов из релиза). Образ состоит из пакетов Arch Linux ARM под их лицензиями
(исходники — archlinuxarm.org). Закрытые прошивки не распространяются: телефон использует свои.
