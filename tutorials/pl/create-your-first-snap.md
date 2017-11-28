---
id: create-your-first-snap-pl
summary: Korzystając ze snapcrafta opiszemy tworzenie pierwszego snapa i główne koncepcje snapcrafta.
categories: packaging
status: Published
feedback link: https://github.com/m4sk1n/tutorials-ubuntu-pl
tags: snapcraft, usage, build, beginner, idf-2016
difficulty: 1
published: 2016-08-31

---

# Tworzenie pierwszego snapa

## Omówienie
Duration: 1:00

`snapcraft` jest narzędziem do tworzenia snapów. Odczytując prosty plik z instrukcjami rozpoczyna proces budowania za nas. Nauczymy się korzystać ze snapcrafta, zobaczymy jakie to proste i utworzymy naszego pierwszego snapa.


![IMAGE](https://assets.ubuntu.com/v1/074862f8-snaps-hero.png)




### Czego się nauczysz?

  - Jak zainstalować snapcraft.
  - Jak utworzyć nowy projekt.
  - Jak zbudować snapa.
  - Jak zadeklarować metadane snapa.
  - Jak aplikacje tworzone są z części.
  - I nie tylko…

### Czego potrzebujesz?

  - Dowolnej dystrybucji GNU/Linuxa wspierającej snapy.
  - Podstawowej znajomości wiersza poleceń, umiejętności edycji plików.
  - Oczekujemy, że potrafisz już zainstalować snapa i wiesz, czym on jest. Dobrym wprowadzeniem do tego jest poradnik „[podstawowe użycie snapów]”.


Survey
: Jak wykorzystasz ten poradnik?
 - Tylko przeczytam go
 - Przeczytam i wykonam ćwiczenia
: Jakie jest Twoje obecne doświadczenie?
 - Nowicjusz
 - Średniozaawansowany
 - Doświadczony


## Wprowadzenie
Duration: 1:00

Jeżeli używasz Ubuntu 16.04 LTS lub Ubuntu 16.10, zdobycie potrzebnych narzędzi jest bardzo proste.


positive
: Informacja: pakiet snapcrafta dla innych dystrybucji GNU/Linuxa jest przygotowywany.


### Jeżeli korzystasz z Ubuntu Core

Ubuntu Core nie jest typową dystrybucją Linuxa: nie ma tu polecenia „apt”, system jest tylko do odczytu. Jak utworzyć i skompilować snapa w takim środowisku? Istnieje specjalny snap do tego!

Zainstaluj snapa “**classic**”. Wprowadzi nas w chroot, w którym uzyskamy dostęp do tradycyjnej dystrybucji Ubuntu. Tam możemy zainstalować więcej pakietów, zmieniać projekty plików i uruchomić snapcrafta aby utworzyć naszego snapa. Katalog **home** jest współdzielony między kontenerem a Ubuntu Core. Możemy więc zbudować snapa, opuścić chroot, zainstalować i przetestować go poza środowiskiem chroot.

Aby zainstalować snapa classic wprowadź:


```bash
$ sudo snap install classic --edge --devmode
```

Za każdym razem, kiedy chcesz zainstalować pakiet, zbudować snapa itp., wprowadź:


```bash
$ sudo classic
(classic)foo@localhost:~$
```

Zauważysz, że powłoka jest poprzedzona tekstem `(classic)`, aby przypomnieć, że uruchamiamy te polecenia wewnątrz snapa classic. Możesz opuścić tą powłokę wprowadzając `exit`.

Możemy teraz wykonać polecenia i budować snapy tak, jak na tradycyjnym systemie (nie będziemy przypominać o tym w tym i następnych poradnikach). Pamiętaj, że kiedy wykonujesz polecenie “**snap**” (nie snapcraft) aby dokonywać interakcji lub instalować snapy, musisz znajdować się poza poleceniem snap (możesz na przykład rozpocząć drugie połączenie SSH).

Zacznijmy od utworzenia bazy danych apt w snapie classic:



```bash
(classic)foo@localhost:~$ sudo apt update
```

### Instalacja zależności

Teraz wprowadź:


```bash
$ sudo apt install snapcraft build-essential
```

W ten sposób zainstalujesz snapcrafta i kilka dodatkowych narzędzi potrzebnych do naszego przykładu. Ponieważ snapcraft jest zbudowany modularnie (aby pozwolić na tworzenie snapów dla każdego rodzaju projektów), niektóre narzędzia muszą być instalowane oddzielnie. W naszym przypadku, jest to `build-essential`.

Jesteśmy gotowi, przejdźmy do tworzenia pierwszego snapa!



## Budowanie snapów jest proste!
Duration: 4:00

### Tworzenie projektu

Ponieważ zaczniemy od podstaw, pierwszym krokiem będzie utworzenie katalogu i skorzystanie z prostego szablonu. Wprowadź w terminalu:

```bash
$ mkdir hello
$ cd hello
~/hello$ snapcraft init
Created snap/snapcraft.yaml.
Edit the file to your liking or run `snapcraft` to get started
```

snapcraft utworzył prosty plik `snapcraft.yaml` w katalogu `snap/`, w którym zadeklarujemy budowę i właściwości naszego snapa. Będziemy musieli oczywiście trochę zmodyfikować go na potrzeby snapa, którego chcemy zbudować.


negative
: Ważne: Musimy upewnić się, że katalog hello znajduje się w katalogu domowym. Unikaj katalogów takich jak /tmp lub snap try (polecenie, o którym wspomnimy wkrótce). Za każdym razem, kiedy będziemy pracować nad snapem, będziemy korzystać z katalogu w katalogu domowym.


### Opisywanie snapa

Spójrzmy na górną część pliku snapcraft.yaml. Powinien wyglądać mniej więcej tak:


```bash
name: my-snap-name # you probably want to 'snapcraft register <name>'
version: '0.1' # just for humans, typically '1.2+git' or '1.3.2'
summary: Single-line elevator pitch for your amazing snap # 79 char long summary
description: |
  This is my-snap's description. You have a paragraph or two to tell the
  most important story about your snap. Keep it under 100 words though,
  we live in tweetspace and your description wants to look good in the snap
  store.
grade: devel # must be 'stable' to release into candidate/stable channels
confinement: devmode # use 'strict' once you have the right plugs and slots
```

Ta część pliku `snapcraft.yaml` jest obowiązkowa i opisuje podstawowe dane meta snapa. Opiszmy je wiersz po wierszu:

  - **name** zawiera nazwę snapa.
  - **version** to obecna wersja snapa. Jest ona odczytywalna dla człowieka. Porządek nie ma znaczenia, snapy będą posiadać również **vision**, która jest niezależna od wersji. Jest oddzielona, więc możesz wysyłać wielokrotnie snapa o tej samej wersji dla wielu architektur. Spójrz na to jak na ciąg znaków wskazujący użytkownikowi wersję snapa, tak jak „stable”, „2.0” itd.
  - **summary** jest krótkim, jednoliniowym opisem snapa.
  - **description** to opis, który powinien pozwolić użytkownikowi na zdecydowanie, czy ten snap jest dla nich przydatny. Opis może składać się z wielu linii, jeżeli jest poprzedzony znakiem **|**.
  - **grade** może oznaczać kanał wersji snapa. Snapy oznaczone jako "devel" nie trafią do kanałów stabilnych.
  - **confinement** może zawierać `devmode` lub `strict`. Wrócimy do tego później. Kiedy tworzymy nowego snapa, zawsze lepiej jest zostawić `devmode`.

Ok. To sporo, jak na podstawy. Dostosujmy naszego pierwszego snapa. Zmień górną część pliku, aby wyglądała tak:


```bash
name: hello
version: '2.10'
summary: GNU Hello, the "hello world" snap
description: |
  GNU hello prints a friendly greeting.
grade: devel
confinement: devmode
```


positive
: Krótka inforamcja o **version**: znajduje się ona w cudzysłowiu ('`2.10`'), ponieważ jest ciągiem znaków, nie liczbą. W praktyce może to być ciąg niezawierający liczb, np. '`mojapierwszawersja`', co jest dozwolone. Wersja jest przeznaczona dla użytkownika i nie wymaga żadnego porządkowania, aby została dostarczona.
```

Proste, prawda?

### Dodanie elementu

Snap może skłądać się z wielu elementów. Tutaj opisane jest kilka przykładów:

  - Snapy składające się z kilku logicznych części, np. snap serwera składający się z serwera web, bazy danych i właściwej aplikacji lub gra składająca się z silnika i danych właściwej gry.
  - Snapy składające się z części o różnym pochodzeniu.
  - Części budowane są w różny sposób.

Nasz snap **hello** będzie z początku składał się tylko z jednej części. Wkrótcce będziemy go jednak rozszerzać krok po kroku.

Dwa obowiązkowe elementy każdego snapa to definicja **source** i **plugin**. Wskazują one „co” (source) i „jak” (plugin). Jako źródło możesz wykorzystać zdalne repozytorium (np. **git**), **tarball** lub **położenie lokalne**. Snapcraft zawiera wiele wtytczek które pozwolą na budowę prawie każdego typowego rodzaju projektu (np. autotools, cmake, go, maven, nodejs, python2, python3 itp.).

Aby zbudować **hello**, dodaj do pliku `snapcraft.yaml`:


```bash
parts:
  gnu-hello:
	source: http://ftp.gnu.org/gnu/hello/hello-2.10.tar.gz
	plugin: autotools
```

Na liście części dodamy naszą, o nazwie `gnu-hello` (może to być cokolwiek). Jako źródło (**source**) użyjemy tarballa z serwera FTP projektu GNU – snapcraft pobierze i rozpakuje go automatycznie. Jako wtyczkę wybierz **autotools** korzystające ze standardowego `./configure && make && make install`, aby skompilować program.


```bash
Możesz uzyskać kompletną listę wspieranych wtyczek używając polecenia „`snapcraft list-plugins`”. Są to mapowania dla najpopularniejszych narzędzi używanych do budowania projektów.
```


Teraz przejdźmy do bardziej ekscytującej części: zbudujmy to! Wszystko co musimy zrobić to:


```bash
~/hello$ snapcraft
[...]
Staging gnu-hello
Priming gnu-hello
Snapping 'hello' |                                          	 
Snapped hello_2.10_amd64.snap
```

If your system does not have installed some dependencies such as "autopoint" and "libtool" needed for this example, snapcraft will prompt for the system password to install them.

Właśnie udało Ci się zbudować pierwszego snapa! Gratuluję! Teraz go zainstalujemy.


```bash
~/hello$ sudo snap install --devmode hello_2.10_amd64.snap

hello 2.10 installed
~/hello$ snap list
Name             Version                           Rev  Developer   Notes
hello            2.10                              x1               devmode
ubuntu-core      16.04.1                           352  canonical   -
```

Korzystanie z `--devmode` jest zwykle dobrym pomysłem, jest też najlepszą praktyką podczas tworzenia snapów. Podczas tworzenia, wymogi bezpieczeństwa nie sa priorytetem. Najpierw należy sprawić, aby program działał. W ten sposób skupisz się na początku na bezpieczeństwie, a odpowiednia izolacja będzie kolejnym krokiem.

Dobrze, spróbujmy uruchomić snapa wprowadzając `hello`:

Na desktopowym Ubuntu uzyskamy:

```bash
~/hello$ hello
Program 'hello' można znaleźć w następujących pakietch:
 * hello
 * hello-traditional
Spróbuj: sudo apt install <swybrany pakiet>
```

Możesz też napotkać inny błąd, jeżeli wcześniej instalowano snapa hello:

```bash
~/hello$ hello
bash: /snap/bin/hello Nie ma takiego pliku ani katalogu
...
```


Na Ubuntu Core:

```bash
~/hello$ hello
hello: command not found
```

Polecenie nie istnieje, mimo że kejst częścią naszego zainstalowanego snapa! To dlatego, że domyślnie snapy nie udostępniają użytkownikowi niczego (poleceń, usług…). Musimy wyraźnie to zaznaczyć, co zrobimy w następnym kroku.


negative
: Jeżeli działa to u Ciebie, sprawdź czy używasz właściwego polecenia `hello`. Sprawdź wynik polecenia `which hello` – może to być np. `/usr/bin/hello`. To, czego potrzebujemy będzie położone w `/snap/bin`.




## Uwidocznienie naszej aplikacji przez snapa!
Duration: 3:00


positive
: Zaczynasz stąd lub zgubiłeś/aś się?
Sprawdź lub pobierz [tutaj][here4], aby zobaczyć jak obecnie powinien wyglądać Twój katalog.


### Definiowanie poleceń

Aby udostępnić usługi i polecenia użytkownikom z musimy określić je w pliku `snapcraft.yaml`! musimy uwzględnić kilka rzeczy:

  - Usługi będą automatycznie włączane/wyłączane.
  - Polecenia będą częścią przestrzeni nazwz więc możesz np. zainstalować tego samego snapa od różnych wydawców i niech będą one na siebie „nachodziły”.
  - Możesz ustalić, jakich uprawnień będą wymagać polecenia i usługi.
  - I nie tylko…

Udostępnienie polecenia `hello` będzie bardzo proste, więc zróbmy to. Wszystko co musisz zrobić to dodanie do pliku `snapcraft.yaml`:


```bash
apps:
  hello:
    command: bin/hello
```

W ten sposób definiujemy aplikację `hello`, która wskazuje na plik wykonywalny `bin/hello` w strukturze plików snapa.
W ten sposób polecenie `hello` będzie dostępne dla użytkowników.

Zwykle zalecamy umieszczenie tego elementu po opisie snapa (z nazwą, wersją, kanałem…) i przed opisem części. Kolejność nie ma znaczenia, ale czyni plik `snapcraft.yaml` bardziej czytelnym. Wzorcowy plik wygląda tak:

  – Ogólne informacje o snapie (nazwa, wersja, kanał…)
  – Udostępniane polecenia (programy)
  – Instrukcje budowania (części)

### Iterating over your snap

Ponieważ zdefiniowaliśmy  polecenie, zbudujmy snapa jeszcze raz aby zobaczyć, czy działa. Zamiast uruchomić snapcraft, wykorzystany sposób na szybkie uruchomienie snapa podczas jego tworzenia:


```bash
~/hello$ snapcraft prime
Skipping pull gnu-hello (already ran)
Skipping build gnu-hello (already ran)
Skipping stage gnu-hello (already ran)
Skipping prime gnu-hello (already ran)
~/hello$ sudo snap try --devmode prime/

hello 2.10 mounted from ~/hello/prime
```

Kazaliśmy snapcraftowi wykonać wszystkie kroki budowania, aż do kroku „prime”. To tam budowane i łączone są wszystkie części snapów. Tam znajduje się rozpakowana zawartość pakietu snap. Możemy skorzystać z polecenia `snap try`, które zainstaluje niespakowanego snapa na potrzeby testu. Czyni to testowanie prostszym.



positive
: Zauważ: Kroki wykonywane przez snapcrafta to: **pull** (pobieranie źródeł dla wszystkich elementów), **build** (budowa), **stage** (linkowanie zainstalowanych części), **prime** (usunięcie niepotrzebnych plików), **snap** (utworzenie snapa z zawartości katalogu prime/). Każdy krok wymaga ukończenia poprzedniego kroku.


To powinno zacząć działać, prawda?


```bash
~/hello$ hello
Hello, world!
~/hello$ which hello
/snap/bin/hello
```

Gratuluję! Udało Ci się utworzyć pierwszego snapa!


negative
: Ważne: Jeżeli **hello** nie działa lub wyświetla się błąd `cannot change current working directory to the original directory: No such file or directory`, prawdopodobnie snap został utworzony poza katalogiem domowym (pamiętam o zaleceniu z pierwszego kroku). Przykładem katalogu, w którym nie możesz utworzyć snapa jest `/tmp/`. Możesz naprawić to odinstalowując snapa poleceniem `snap remove hello` i zaczynając ponownie w dowolnym miejscu w katalogu domowym.

## Snap składający się z wielu elementów
Duration: 3:00


positive
: Zgubiłeś/aś się lub zaczynasz stąd?
Sprawdź lub pobierz [tutaj][here1] obecną zawartość katalogu.


Dodajmy teraz kolejny element, aby uczynić naszego snapa trochę bardziej interesującym. W definicji części, dodaj:


```bash
parts:
  […]
  gnu-bash:
	source: http://ftp.gnu.org/gnu/bash/bash-4.3.tar.gz
	plugin: autotools
```

Zauważ, że ten element (o nazwie `gnu-bash`) wygląda prawie jak `gnu-hello` z poprzednich kroków. Również pobiera on tarball i używa wtyczki `autotools`.
Z poprzedniego kroku wiesz, że musisz określić aplikację, aby udostępnić ją użytkownikom Twojego snapa. Zróbmy to. Do definicji aplikacji dodajmy:


```bash
apps:
  […]
  bash:
	command: bash
```

Tym razem, nazwa polecenia jest inna od nazwy snapa. Domyślnie wszystkie polecenia są dostępne dla użytkownika jako `<nazwa-snapa>.<polecenie>`. Ta aplikacja będzie dostępna jako `hello.bash`, aby uniknąć konfliktu z systemowym `/bin/bash` lub innym snapem zawierającym polecenie `bash`. Jednak, jak pewnie pamiętasz, pierwszy program jest nazwany po prostu `hello`. Dzieje się tak, gdy <polecenie> jest równe <nazwa-snapa>. Zamiast `.hello.hello` używany więc prostszego `hello`.

Nasz snap będzie dostarczał teraz dwa programy: `hello` i `hello.bash`.

Zauważ, że jako polecenie wprowadziliśmy „`bash`”, nie „`bin/bash`”, jak przy `$SNAP` dla „`hello`”. Oba rozwiązania są prawidłowego. Dlaczego? `snapcraft` i `snapd` opanowują Twoje polecenia, tworząc pewne zmienne środowiskowe za Ciebie. technicznie, `$SNAP/bin` jest Twoim `$PATH` w tym snapie. Dzięki temu, polecenie jest dostępne, mimo braku wyraźnego wskazania na nie. Opiszemy więcej zmiennych środowiskowych w następnych krokach, gdy zbudujemy snapa.

Teraz wykonaj:


```bash
~/hello$ snapcraft prime
```

aby rozpocząć budowę. Tylko element gnu-bash musi zostać zbudowany (nic nie zmieniło się w pozostałych), co potrwa względnie szybko.

### Nasze pierwsze niepowodzenie

O nie! Budowanie nie powiodło się:


```bash
Parts 'gnu-bash' and 'gnu-hello' have the following file paths in common which have different contents:
share/info/dir
```

Co to oznacza? Zarówno element `gnu-hello`, jak i `gnu-bash` chcą dostarczyć  `share/info/dir` o innej zawartości. Musimy zapobiec temu zderzeniu. Zwykle mamy w takim przypadku dwie opcje:

  – Udostępnienie tylko jednego pliku z jednej z tych części. Jeżeli uznamy ten plik za niepotrzebny lub jest identyczny w obu przypadkach, możemy nakazać snapcraftowi nie używać jednego z nich, używając słów kluczowych `snap` i `stage`.
  – Przeniesienie jednego z tych plików do innego położenia.

Na szczęście, drugie rozwiązanie jest proste w implementacj i dobrze jest uwzględnić oba pliki. The `./configure` script of bash comes with an `--infodir` option, which will set the new location of the info directory. Let’s use `/var/bash/info`. All you need to do is make your gnu-bash definition look like this:



```bash
  gnu-bash:
	source: http://ftp.gnu.org/gnu/bash/bash-4.3.tar.gz
	plugin: autotools
	configflags: ["--infodir=/var/bash/info"]
```

This is how you tell snapcraft to pass `--infodir=/var/bash/info` as an argument to `./configure` during the build.


positive
: Note: The `configflags` option is specific to the `autotools` plugin. Luckily `snapcraft` comes with a built-in help system. You can easily find out about all the options by running
`snapcraft help sources` for source options,
`snapcraft help <plugin-name>` for any given plugin,
`snapcraft list-plugins` for a list of all available plugins.


Now just run


```bash
~/hello$ snapcraft clean gnu-bash -s build
Skipping cleaning priming area for gnu-bash (already clean)
Skipping cleaning staging area for gnu-bash (already clean)
Cleaning build for gnu-bash
~/hello$ snapcraft prime
[...]
Skipping build gnu-hello (already ran)
Staging gnu-bash
Staging gnu-hello
Priming gnu-bash
Priming gnu-hello
```

Here you clean just the build step of the `gnu-bash` part, so the source does not need to be re-downloaded. Then the build is run again and it passes! Now let’s see if our new commands work fine.


```bash
~/hello$ sudo snap try --devmode prime

hello 2.10 mounted from /home/daniel/hello/prime
~/hello$ hello
Hello, world!
~/hello$ hello.bash
bash-4.3$ env
[ Environment variables list ]
bash-4.3$ exit
```


positive
: Note: You will see that the environment variables available from your snap are a little different from your user environment: some additional variables are added like **$SNAP_*** and some system environment variables have been altered to point to your snap directory, like **$PATH** or **$LD_LIBRARY_PATH**. Take the time to get familiar with them!


Excellent work! You have it all nice and working!



## Removing devmode
Duration: 2:00


positive
: Lost or starting from here?
Check or download [here][here2] to see what your current directory should look like.


One last thing you might want to do before the snap is ready for wider consumption is to remove the `devmode` status.


negative
: Users of snaps using `devmode`, will need to pass `--devmode` during the installation, so they explicitly agree to trust you and your snap. Another benefit of removing devmode is that you will be able to ship your snap on the **stable** or **candidate** channels (you can only publish to the other channels, like **beta** or **edge** as your snap is less trusted) and users will be able to search for it using `snap find`.


For this to be declared in your snap, let’s set `confinement` to `strict` in your `snapcraft.yaml`:


```bash
confinement: strict
```

Now let’s build the snap and install it! We are going to call `snapcraft` without `prime` this time and `snap install` (as opposed to `snap try` earlier) to try everything under normal conditions. You should also stop using the `--devmode` switch to really test it under confinement:


```bash
~/hello$ snapcraft
Skipping pull gnu-bash (already ran)
Skipping pull gnu-hello (already ran)
Skipping build gnu-bash (already ran)
Skipping build gnu-hello (already ran)
Skipping stage gnu-bash (already ran)
Skipping stage gnu-hello (already ran)
Skipping prime gnu-bash (already ran)
Skipping prime gnu-hello (already ran)
Snapping 'hello' -
Snapped hello_2.10_amd64.snap
```

Let’s try to install it!


```bash
~/hello$ sudo snap install hello_2.10_amd64.snap
error: cannot find signatures with metadata for snap "hello_2.10_amd64.snap"
```

Oh, that didn’t go well! Indeed, you are trying to install a snap which isn’t signed by the store. Previously, we did local installations via **devmode** which implied (in addition to run without confinement) that unsigned local snap was ok to be installed on your system. Here, as we won’t specify devmode anymore, we need to be more explicit and indicate that it’s ok to install this unsigned snap thanks to the **--dangerous** option.


```bash
~/hello$ sudo snap install hello_2.10_amd64.snap --dangerous

hello 2.10 installed
```

To complete our tests, let’s see if the command still work as expected:


```bash
~/hello$ hello
Hello, world!
~/hello$ hello.bash
bash-4.3$ ls /home
ls: cannot open directory '/home': Permission denied
bash-4.3$ exit
```

What’s happening here? Your snap is not broken, it’s confined now, so can only access its own respective directories.


positive
: Note: For other snaps you might need to declare if commands or services need special permissions, for example if they need access to the network, or audio. A tutorial on interfaces, slots and plugs will bring more information on this very topic.


You are done. This snap is ready for publication!

## Upload to the store
Duration: 5:00


positive
: Lost or starting from here?
Check or download [here][here3] to see what your current directory should look like.


Apps can easily be uploaded to the store via [My Apps]. Registering an account is easy, so let’s do that first.

### Registering an account

Open My Apps in your browser and follow the instructions to register an account:


![IMAGE](https://assets.ubuntu.com/v1/2cb98ee4-screen1.png)

Select "I am a new Ubuntu One user” and complete the needed data.

![IMAGE](https://assets.ubuntu.com/v1/32614c93-screen2.png)

Enter your email address, name and password, accept the terms of service and create the account. Remember as well to choose a namespace on myapps before registering your snap.

It’s then time for you to get authenticated from snapcraft:

If it is your first time you will get a message to enable multifactor authentication and agree with the developer terms and conditions.


```bash
~/hello$ snapcraft login
Enter your Ubuntu One SSO credentials.
Email: myemail@provider.com
Password:
One-time password (just press enter if you don't use two-factor authentication):
Authenticating against Ubuntu One SSO.
Login successful.
```

You can logout any time with simply `snapcraft logout`.

### Register a new snap name

Before being able to upload any snap, you need to register (meaning: reserving) a name. That way, this snap name is yours, and you will be able to upload snaps matching this name.

To reserve a new name, simply try:


```bash
~/hello$ snapcraft register hello-<yourname>
snapcraft register hello-<yourname>
Registering hello-<yourname>.
Congratulations! You're now the publisher for 'hello-<yourname>'.
```

If the name is already reserved, you can either dispute that name or just pick a new one.

If you changed your snap name while registering, you need to rebuild this snap with that new name:

  - change your snap name in `snapcraft.yaml`:

```bash
name: hello-didrocks
[…]
```
  - as we want to publish it in the candidate channel, we need to set its grade to stable:

```bash
grade: stable
```
  - and just rebuild your snap with:

```bash
~/hello$ snapcraft
Skipping pull gnu-bash (already ran)
Skipping pull gnu-hello (already ran)
Skipping build gnu-bash (already ran)
Skipping build gnu-hello (already ran)
Skipping stage gnu-bash (already ran)
Skipping stage gnu-hello (already ran)
Skipping prime gnu-bash (already ran)
Skipping prime gnu-hello (already ran)
Snapping 'hello-didrocks' -                                      
Snapped hello-didrocks_2.10_amd64.snap
```

### Upload your snap

It’s high time to make this snap available to the world!

Let’s try to publish it to the **candidate** channel for now:

```bash
~/hello$ snapcraft push hello-didrocks_2.10_amd64.snap --release=candidate
Uploading hello-didrocks_2.10_amd64.snap.
Uploading hello-didrocks_2.10_amd64.snap [================================] 100%
Ready to release!|                                                              
Revision 1 of 'hello-didrocks' created.
The candidate channel is now open.

Channel    Version    Revision
---------  ---------  ----------
stable     -          -
candidate  2.10       1
beta       ^          ^
edge       ^          ^
```

You should receive an email telling you that your snap is pending review (automatic checking). If you are not using any reserved interfaces and security checks are passing, it would be available to the user (in the **candidate**, **beta** and **edge** channels in this case) to your users via a simple:


```bash
$ sudo snap install hello-didrocks --channel=candidate
```


negative
: As you uploaded an amd64 binary, only people on amd64 machine will get access to this snap. You can either only focus on one architecture to support, build yourself for all supported architectures your snap, or use **launchpad snap build service** to push your `snapcraft.yaml`, and get resulting snaps built on all architectures for you! More information are [available here].

From here, if you are happy with the testing of your snap, you can snapcraft push again the same file, omitting `--release` option to publish a revision 2 in the **stable** channel. You can use the website as well and check the box for the channels you want this revision 1 to be published to.

Remember that snaps with `confinement: devmode` can’t be published to the **stable** or **candidate** channels.


positive
: Note: both of these steps (registering and uploading new snaps) are also available through the web interface. You then just upload your .snap files after clicking on **New Snap** on the website and filling in the form.

The web interface will give you information about the publication status. Take a look to see all the available options!

## That’s all folks!
Duration: 1:00

### Easy, wasn’t it?

Congratulations! You made it!


positive
: Final code: Your final code directory should now look like [this]. Do not hesitate to download and build your snap from it if you only read it through!


By now you should successfully have built your first snap, fixed build issues, exposed user commands, learned about uploading snaps to the store and found out about a lot of other useful details (plugins, snapcraft’s help system, release channels in the store, etc.). Snapcraft is easy to use as it is declarative and just uses a few different keywords, so it’s memorisable quite quickly. After using it just a couple of times, it should become second nature to you.

### Next steps

  - You can have a look at “[build a nodejs service]” which is the logical follow up of that tutorial, bringing your some debugging techniques, more information on confinement and how to package some snap application as a service.
  - Learn some more advanced techniques on how to use your snap system looking for our others tutorials!
  - Join the snapcraft.io community on the [snapcraft forum].

### Further readings
  - [Snapcraft.io documentation] is a good place to start reading the whole snap and snapcraft documentation.
  - Documentation on [interfaces].
  - [Snapcraft syntax reference], covering various available options like the daemon ones.
  - Check how you can [contact us and the broader community].





[basic snap usage]: https://tutorials.ubuntu.com/tutorial/basic-snap-usage
[here1]: https://github.com/ubuntu/snap-tutorials-code/tree/master/create-your-first-snap/step3
[here2]: https://github.com/ubuntu/snap-tutorials-code/tree/master/create-your-first-snap/step4
[here3]: https://github.com/ubuntu/snap-tutorials-code/tree/master/create-your-first-snap/step5
[here4]: https://github.com/ubuntu/snap-tutorials-code/tree/master/create-your-first-snap/step6
[My Apps]: https://dashboard.snapcraft.io/dev/snaps/
[available here]: https://kyrofa.com/posts/building-your-snap-on-device-there-s-a-better-way
[this]: https://github.com/ubuntu/snap-tutorials-code/tree/master/create-your-first-snap/final
[build a nodejs service]: https://tutorials.ubuntu.com/tutorial/build-a-nodejs-service
[snapcraft forum]: https://forum.snapcraft.io/
[Snapcraft.io documentation]: http://snapcraft.io/docs/
[interfaces]: https://snapcraft.io/docs/core/interfaces
[Snapcraft syntax reference]: http://snapcraft.io/docs/build-snaps/syntax
[contact us and the broader community]: http://snapcraft.io/community/
