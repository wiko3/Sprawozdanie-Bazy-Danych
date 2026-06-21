======================================================
Definiowanie bazy danych i wprowadzanie danych do bazy
======================================================

:Autorzy:
    1. Wiktor Głogowski
    2. Olga Grześkowiak
    3. Kamil Karaś

Rozdział ten opisuje proces fizycznego zdefiniowania struktur bazodanowych oraz zasilenia ich danymi testowymi. Prace przeprowadzono równolegle dla dwóch wybranych systemów zarządzania bazami danych: relacyjnego serwera **PostgreSQL** oraz lekkiej, plikowej bazy **SQLite**.

Definiowanie bazy danych
========================

Struktury tabel, relacje oraz więzy integralności dla obu systemów zostały utworzone na podstawie skryptów DDL (Data Definition Language) wygenerowanych w fazie projektowania fizycznego. W przypadku PostgreSQL struktury zdefiniowano z poziomu narzędzia pgAdmin, wykonując zapytania w oknie Query Tool. Dla bazy SQLite wykorzystano bezpośrednie połączenie z plikiem bazy (``wypozyczalnia.db``) i wykonano odpowiadające mu instrukcje DDL. 

Wybór mechanizmów wsadowego wprowadzania danych
===============================================

Ze względu na potrzebę załadowania większej ilości danych testowych, zrezygnowano z ręcznego wprowadzania rekordów komendą ``INSERT`` na rzecz wsadowego importu danych. Dane źródłowe zostały wcześniej przygotowane i wyeksportowane do płaskich plików tekstowych w formacie CSV, w których jako separatora kolumn użyto średnika (``;``).

PostgreSQL: pgAdmin (Import/Export Data)
----------------------------------------

Dla systemu PostgreSQL wybrano mechanizm wbudowany w graficzne środowisko zarządzania **pgAdmin**. Narzędzie *Import/Export Data* stanowi interfejs graficzny dla wysoce wydajnej komendy SQL ``COPY``. 

**Uzasadnienie wyboru:** Decyzja o wykorzystaniu natywnych narzędzi klienckich (wykonywanie skryptów w Query Tool oraz wbudowany mechanizm importu) wynika z potrzeby zachowania maksymalnej wydajności i bezpośredniej kontroli nad silnikiem bazy danych. Wykorzystanie Query Tool w połączeniu z bezpośrednim importem minimalizuje ścieżkę przetwarzania danych, eliminuje narzut komunikacyjny aplikacji i stanowi najbardziej optymalną, niskopoziomową metodę jednorazowego, wsadowego zasilenia struktur testowych, gwarantującą natychmiastową walidację więzów integralności przez sam serwer PostgreSQL.

SQLite: Skrypt Python (biblioteka Pandas)
-----------------------------------------

W przypadku SQLite zdecydowano się na podejście programistyczne, wykorzystując język Python oraz bibliotekę do analizy danych **Pandas** w połączeniu z wbudowanym modułem ``sqlite3``.

**Uzasadnienie wyboru:**
Baza SQLite jest bazą plikową, co sprawia, że skryptowanie operacji z wykorzystaniem Pythona jest wyjątkowo proste i umożliwia łatwą automatyzację. Biblioteka Pandas pozwala na bezproblemowe wczytanie plików CSV jako ramek danych za pomocą funkcji ``pd.read_csv``, a następnie automatyczne zrzucenie ich do tabel w bazie danych za pomocą metody ``to_sql``.

Poniżej przedstawiono fragment skryptu wykorzystanego do zasilenia bazy SQLite:

.. code-block:: python

   import pandas as pd
   import sqlite3

   # Wczytanie i import danych dla tabeli 'klient'
   df = pd.read_csv("klient_fill.csv", sep=";")
   df.head()
   
   conn = sqlite3.connect("wypozyczalnia.db")
   df.to_sql("klient", conn, if_exists="append", index=False)
   conn.close()

   # Wczytanie i import danych dla tabeli 'wypozyczenia'
   df = pd.read_csv("wypozyczenia_fill.csv", sep=";")
   df.head()
   
   conn = sqlite3.connect("wypozyczalnia.db")
   df.to_sql("wypozyczenia", conn, if_exists="append", index=False)
   conn.close()

   # Wczytanie danych dla tabeli 'stan_techniczny'
   df = pd.read_csv("stan_techniczny_fill.csv", sep=";")
   df.head()
   # (...) kontynuacja zapisu analogicznie do poprzednich tabel

Komentarz do procesu wprowadzania danych
========================================

Proces zasilania bazy danych danymi ujawnił kilka kluczowych aspektów technicznych, które wymagały kontroli:

1. **Zarządzanie kluczami obcymi:** Proces importu musiał przebiegać w ściśle określonej kolejności, aby nie naruszyć więzów integralności referencyjnej. Jako pierwsze do bazy musiały trafić tabele "niezależne", czyli słownikowe: ``klient`` oraz ``pojazdy``. Dopiero po zaindeksowaniu klientów i aut możliwe było importowanie rekordów do tabel podrzędnych: ``wypozyczenia`` oraz ``stan_techniczny``. Próba odwrócenia tej kolejności skutkowałaby odrzuceniem importu przez silnik bazy z powodu braku powiązanych kluczy głównych.
2. **Formatowanie plików źródłowych:** Zastosowanie średnika (``;``) jako separatora w plikach CSV jest dobrą praktyką w polskim środowisku lokalnym, gdzie przecinek często wykorzystywany jest jako separator miejsc dziesiętnych, co mogłoby prowadzić do konfliktów przy wczytywaniu danych numerycznych. Zdefiniowano również argument ``if_exists="append"`` w skrypcie Pythona, co gwarantowało dopisywanie danych do utworzonych wcześniej struktur (zgodnie z rygorystycznym DDL), zamiast ich nadpisywania.
3. **Kodowanie znaków:** Podczas pracy ze skryptami zaobserwowano problemy z kodowaniem polskich znaków diakrytycznych w nazwach plików oraz samych danych źródłowych. Nazwa pliku ``wypozyczenia_fill.csv`` jednoznacznie wskazuje na konflikt między kodowaniem Windows-1250 a standardem UTF-8. W docelowym środowisku produkcyjnym i przy kolejnych importach konieczne jest wymuszenie spójnego standardu, najlepiej UTF-8, zarówno dla zawartości plików CSV, jak i ich nazw, aby uniknąć problemów z odczytem danych.