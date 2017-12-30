---
id: tutorial-how-to-verify-ubuntu-pl
summary: Dzięki weryfikacji obrazu ISO możesz upewnić się, że pobrany system nie został uszkodzony.
categories: desktop
language: pl
tags: tutorial,installation,verify,ubuntu,polish,hidden
difficulty: 3
status: published
published: 2017-12-30
feedback_url: https://github.com/m4sk1n/tutorials.ubuntu.com/issues
author: Canonical Web Team <webteam@canonical.com>
translator: Marcin Mikołajczak

---

# Jak zweryfikować pobrany obraz Ubuntu

## Wprowadzenie
Duration: 2:00

: Switch language: [EN](tutorial/tutorial-how-to-verify-ubuntu) | PL

positive
: Aby wykonać następujące kroki potrzebujesz aplikacji terminala. Następujące kroki zakładają podstawową znajomość wiersza poleceń.

Weryfikowanie obrazu ISO pomaga w upewnieniu się o zgodności danych i autentyczności pobranego obrazu. Proces ten jest prosty, ale składa się z wielu kroków. Są to:

1. Pobranie plików SHA256SUMS i SHA256SUMS.gpg
2. Uzyskanie klucza używanego do podpisywania obrazów z serwera kluczy Ubuntu
3. Weryfikacja podpisu
4. Sprawdzenie obrazu ISO Ubuntu z użyciem sha256sum i pobranych sum kontrolnych

Po zweryfikowaniu pliku ISO możesz przejść do instalacji Ubuntu lub uruchomienia w trybie live z CD/DVD lub dysku USB.

## Pobieranie sum
Duration: 2:00

Pobierz pliki SHA256SUMS i SHA256SUMS.gpg z dowolnego serwera Ubuntu i umieść je w tym samym katalogu.

[Pobierz sumy i podpis dla Ubuntu 16.04.3 LTS&nbsp;&rsaquo;](http://releases.ubuntu.com/16.04)

![zrzut ekranu](https://assets.ubuntu.com/v1/f1cce1af-verify-1-releases.png)

## Uzyskaj klucz podpisu
Duration: 0:04

positive
: Wskazówka – na nielinuksowych systemach może być wymagane pobranie narzędzi GPG, aby wykonać następny krok. Aby sprawdzić, czy masz zainstalowane narzędzia GPG, wykonaj polecenie `gpg --version` lub `gpg2 --version`.

Get the public keys from the Ubuntu key server and add them to your keyring.

```bash
gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys "8439 38DF 228D 22F7 B374 2BC0 D94A A3F0 EFE2 1092" "C598 6B4F 1257 FFA8 6632 CBA7 4618 1433 FBB7 5451"
```
```bash
gpg: katalog ,,/home/ubuntu/.gnupg'' utworzony
gpg: nowy plik ustawień ,,/home/ubuntu/.gnupg/gpg.conf'' został utworzony
gpg: OSTRZEŻENIE: opcje w ,,/home/ubuntu/.gnupg/gpg.conf'' nie są jeszcze uwzględnione
gpg: zbiór kluczy ,,/home/ubuntu/.gnupg/secring.gpg'' został utworzony
gpg: zbiór kluczy ,,/home/ubuntu/.gnupg/pubring.gpg'' został utworzony
gpg: zapytanie o klucz EFE21092 z serwera hkp keyserver.ubuntu.com
gpg: zapytanie o klucz FBB75451 z serwera hkp keyserver.ubuntu.com
gpg: /home/ubuntu/.gnupg/trustdb.gpg: baza zaufania utworzona
gpg: klucz 46181433FBB75451: klucz publiczny ,,Ubuntu CD Image Automatic Signing Key <cdimage@ubuntu.com>'' wczytano do zbioru
gpg: klucz D94AA3F0EFE21092: klucz publiczny ,,Ubuntu CD Image Automatic Signing Key (2012) <cdimage@ubuntu.com>'' wczytano do zbioru
gpg: brak absolutnie zaufanych kluczy
gpg: Ogółem przetworzonych kluczy: 2
gpg:               dołączono do zbioru: 2  (RSA: 1)
```

Zweryfikuj te odciski palców

```bash
gpg --list-keys --with-fingerprint 0xFBB75451 0xEFE21092
```
```bash
pub 1024D/FBB75451 2004-12-30
Odcisk klucza = C598 6B4F 1257 FFA8 6632 CBA7 4618 1433 FBB7 5451
uid Ubuntu CD Image Automatic Signing Key cdimage@ubuntu.com

pub 4096R/EFE21092 2012-05-11
Odcisk klucza = 8439 38DF 228D 22F7 B374 2BC0 D94A A3F0 EFE2 1092
uid Ubuntu CD Image Automatic Signing Key (2012) cdimage@ubuntu.com
```

## Zweryfikuj podpis
Duration: 2:00

Możesz teraz zweryfikować podpis.

```bash
gpg --verify SHA256SUMS.gpg SHA256SUMS
```
```bash
gpg: Podpisano w Fri 25 Mar 04:36:20 2016 GMT kluczem DSA o numerze FBB75451
gpg: Poprawny podpis złożony przez ,,Ubuntu CD Image Automatic Signing Key <cdimage@ubuntu.com>'' [nieznane]
gpg: OSTRZEŻENIE: Ten klucz nie jest poŚwiadczony zaufanym podpisem!
gpg:         Nie ma pewności co do tożsamości osoby która złożyła podpis.
Odcisk klucza głównego: C598 6B4F 1257 FFA8 6632  CBA7 4618 1433 FBB7 5451
gpg: Podpisano w Fri 25 Mar 04:36:20 2016 GMT kluczem RSA o numerze EFE21092
gpg: Poprawny podpis złożony przez ,,Ubuntu CD Image Automatic Signing Key (2012) <cdimage@ubuntu.com>'' [nieznane]
gpg: OSTRZEŻENIE: Ten klucz nie jest poŚwiadczony zaufanym podpisem!
gpg:          Nie ma pewności co do tożsamości osoby która złożyła podpis.
Odcisk klucza głównego: 8439 38DF 228D 22F7 B374  2BC0 D94A A3F0 EFE2 1092
```

positive
: Jest to przykład „prawidłowego” podpisu. GPG sprawdza tylko poprawność podanego pliku. Ostrzeżenie informuje, że Twoja obecna baza zaufanych kluczy GnuPG nie ufa temu kluczowi i dopóki nie zweryfikujesz i nie podpiszesz jednego z publicznych kluczy należących do osób podpisujących klucz obraz ISO Ubuntu, będziesz otrzymywać takie ostrzeżenia.

## Sprawdzanie obrazu ISO
Duration: 2:00

Musisz utworzyć sumę kontrolną pobranego pliku ISO i porównać ją z pobranym plikiem SHA256SUM.

Upewnij się, że pobrane pliki SHA256SUMS i SHA256SUMS.gpg znajdują się w tym samym katalogu, w którym obraz ISO Ubuntu. Później wykonaj następujące polecenia w terminalu.

Na Ubuntu musisz wykonać następujące polecenie:

```bash
sha256sum -c SHA256SUMS 2>&1 | grep OK
```

Na macOS polecenie wygląda tak:

```bash
shasum -a 256 -c SHA256SUMS 2>&1 | grep OK
```

Jeżeli korzystasz z systemu Windows, możesz pobrać [narzędzie SHA-256](http://www.labtestproject.com/files/win/sha256sum/sha256sum.exe). Po jego uzyskaniu, polecenie wygląda w ten sposób

```bash
sha256sum.exe -c SHA256SUMS
```

Wynik polecenia powinien wyglądać tak:

```bash
ubuntu-16.04.2-desktop-amd64.iso: OK
```

Jeżeli nie uzyskano wyniku (lub jest inny niż powyższy), musisz pobrać obraz instalacyjny ponownie.

## Uzyskiwanie pomocy

Jeżeli zgubisz się, możesz łatwo uzyskać pomoc.

* [Ask Ubuntu](https://askubuntu.com/)
* [Fora Ubuntu](https://ubuntuforums.org/)
* [Pomoc na kanałach IRC](https://wiki.ubuntu.com/IRC/ChannelList)
