# Podstawy linii komend w *nixach

## Uruchamianie skryptów

Kolejna sprawa to:

> Mam problem bo nie działa mi [...] szybkie odpalanie programu przez kropkę.

Tutaj trzeba zwrócić uwagę na dwie sprawy. Na samym początku pliku w pierwszej linii powinien znajdować się komentarz:

```python
#!/usr/bin/env python
```

Jest to [shebang](https://en.wikipedia.org/wiki/Shebang_\(Unix\)). Tego typu komentarz powinien być obecny w każdym pliku będącym wykonywalnym skryptem. Shebang daje informację systemowi operacyjnemu, którego interpretera skryptów ma użyć do wykonania instrukcji z tego pliku. W naszym przypadku jest to polecenie systemowe, które otwiera domyślny, interaktywny shell Pythona. Jeżeli wywołasz to bez `#!` z linii komend to się przekonasz sam:

```bash
/usr/bin/env python
```

Drugą niezbędną sprawą, by uruchamianie przez `.` działało to nadanie praw wykonawczych (ang. *executable*) plikowi, który chcesz uruchamiać.

Załóżmy, że napisałem skrypt w Pythonie, który chcę wywoływać z linii komend. Plik nazywa się `hello.py` i zawiera:

```python
#!/usr/bin/env python
print "Hello World!"
```

Czyli po uruchomieniu powinienem zobaczyć wydrukowaną jedną linijkę tekstu `Hello World!`. Gdy próbuję go uruchomić teraz to dostanę błąd, ponieważ nie mam praw wykonawczych na tym pliku. 

```bash
$ ./hello.py
bash: ./hello.py: Permission denied
```

![](https://gist.github.com/tomasz/6a6da097f17f929d1954/raw/ecee6982e8694beb2f470760fd3a821b3ae23601/1-9.png)

Mogę to zmienić korzystając z komendy `chmod`. Pełna komenda to:

```bash
chmod u+x hello.py
```

Co to robi:

- `chmod` to program do zarządzania prawami odczytu, zapisu i wykonywania plików. Ponieważ w Linuxach, wszystko jest traktowane jak pliki, uprawnienia plików są fundamentalną częścią całego systemu.
- `u+x` jest poleceniem dla `chmod` mówiącym by użytkownikowi (`u` od *user*) posiadającemu ten plik, dodać (`+`) możliwość wykonywania pliku (`x` od *e**x**ecutable*).
- `hello.py` to naturalnie nazwa pliku, któremu chcemy zmienić prawa.

Na zrzucie powyżej widać sytuację przed i po zmianie uprawnień. Uprawnienia pliku to pierwsza kolumna wyników z polecenia `ls -l`, które drukuje szczegółowe informacje o plikach, obecnych w bieżącym katalogu. Przed zmianą uprawnienia pliku były

	-rw-rw-r--

Wyróżniamy trzy uprawnienia dla trzech grup. Uprawnienia to `r` -- `read`, `w` -- `write` i omówiony już `x` od `execute`. Teraz warto zauważyć, że te trzy uprawnienia są powtórzone trzy razy. Każda trójka jest przypisana różnych stopniom przynależności pliku. Spokojnie, brzmi źle, ale jest to banalnie proste.

Każdy z plików ma jeszcze przypisaną informację, do którego użytkownika oraz do której grupy użytkowników należy. Są to odpowiednio trzecia i czwarta kolumna z wydruku polecenia `ls -l`.  
Pomijam pierwszy znak z ciągu uprawnień, jest odpowiedzialny za specjalne uprawnienia, o których nie ma sensu tu pisać.  
Tak więc, pierwsza trójka praw `rw-` oznacza, że `u` -- `user` użytkownik, który jest właścicielem pliku może ten plik czytać, oraz do niego pisać, ale **nie** może go wykonywać.  
Kolejna trójka `rw-` mówi, że każdy użytkownik z grupy, do której należy plik, też może go czytać i modyfikować, ale nie uruchamiać.  
Natomiast ostatnia trójka uprawnień `r--` dotyczy wszystkich pozostałych użytkowników systemu, i pozwala im tylko ten plik czytać, bez praw do modyfikacji i uruchamiania.

Po wywołaniu komendy `chmod u+x hello.py` uprawnienia pliku zmieniły się na `-rwxrw-r--`. Nadałem prawo do wykonania pliku właścicielowi pliku, to znaczy samemu sobie. Teraz wykonanie pliku daje oczekiwane rezultaty:

	$ python hello.py
	Hello World!
	$ ./hello.py
	Hello World!
