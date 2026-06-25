============
Wprowadzenie
============

Niniejszy dokument jest przeglądem i zgrupowaniem wiedzy zdobytej podczas zajęć laboratoryjnych z przedmiotu Bazy Danych. Zgromadzony materiał opisuje metodykę projektowania, stosowane narzędzia, a także techniki wprowadzania danych i konstruowania zapytań w środowiskach relacyjnych. Głównym elementem pracy pozostaje porównanie dwóch różnych architektur DBMS: bazy **SQLite** oraz silnika **PostgreSQL**. Układ opracowania naśladuje rzeczywisty proces powstawania oprogramowania, obejmując fazy od analizy sprzętowej, przez tworzenie modeli konceptualnych i logicznych, aż po finalną implementację i strojenie kwerend SQL.

Spis użytych repozytoriów
=========================

Prace nad projektem i testy laboratoryjne oparto na następujących repozytoriach:

* **Projektowe grupowe:** ``https://github.com/karaskamil/Sprawozdanie-Bazy-Danych``
* **Badania literaturowe - sprzęt dla baz danych:** ``https://github.com/karaskamil/Sprzet-dla-bazy-danych.git``
* **Badania literaturowe - konfiguracja bazy danych:** ``https://github.com/Youarecheck/Bazy_Danych_Tematyczne_Repo_MK.git``
* **Badania literaturowe - kontrola i konserwacja:** ``https://github.com/pawlos1337/Bazy-danych-temat.git``
* **Badania literaturowe - monitorowanie i diagnostyka:** ``https://github.com/OskarProgrammer/monitorowanie_i_diagnostyka.git``
* **Badania literaturowe - wydajność, skalowanie i replikacja:** ``https://github.com/KMachoK/Tematyczne/blob/main/index.rst``
* **Badania literaturowe - partycjonowanie danych:** ``https://github.com/domino0472/Partycjonowani-Danych``
* **Badania literaturowe - bezpieczeństwo:** ``https://github.com/oski486/BazyDanych-Subject.git``
* **Badania literaturowe - kopie zapasowe i odzyskiwanie danych:** ``https://github.com/Koko9077/Kopie-zapasowe-i-odzyskiwanie-danych.git``

Wprowadzenie tematyczne do ćwiczeń i eksperymentów
==================================================

Zajęcia koncentrowały się na zbudowaniu od podstaw i wdrożeniu systemu bazodanowego przeznaczonego do obsługi procesów w hipotetycznej wypożyczalni aut. Badania praktyczne pozwoliły sprawdzić zachowanie klasycznych reguł modelu relacyjnego - w tym ścisłych zasad integralności - w zderzeniu ze specyfiką dwóch mocno różniących się od siebie silników DBMS.

Do najważniejszych zadań realizowanych w trakcie spotkań laboratoryjnych należały:

1. **Opracowanie badań literaturowych:** Podstawa teoretyczna do dalszych zadań, w temacie "Sprzęt dla baz danych".
2. **Modelowanie i normalizacja:** Stworzenie modelu konceptualnego, logicznego (wraz z normalizacją) oraz fizycznego dla konkretnej bazy danych (wypożyczalnia samochodów).
3. **Migracja i mechanizmy wsadowe:** Porównanie wydajności graficznego importu przez pgAdmin z ładowaniem danych przy użyciu biblioteki Pandas w Pythonie.
4. **Analityka SQL:** Konstruowanie kwerend łączących, filtrujących oraz grupujących dane dla przedsiębiorstwa, zarówno dla SQLite, jak i PostgreSQL.

Struktura raportu
=================

Układ niniejszej dokumentacji ściśle odzwierciedla kolejne etapy projektowania i wdrażania rozwiązań bazodanowych:

* **Badania literaturowe:** Zarysowanie teoretycznego tła dotyczącego architektury i zasad utrzymania nowoczesnych relacyjnych baz danych na przykładzie środowiska PostgreSQL.
* **Planowanie baz danych i tworzenie dokumentacji:** Proces modelowania konceptualnego, logicznego (z uwzględnieniem normalizacji) oraz fizycznego z podziałem na SQLite i PostgreSQL.
* **Definiowanie bazy danych i wprowadzanie danych do bazy:** Omówienie procesu wdrażania schematu oraz import dużych wolumenów danych z plików CSV.
* **Zapytania do bazy danych:** Szczegółowa analiza kwerend SQL odpowiadających na problemy analityczne postawione w wytycznych do ćwiczeń.

Wnioski z przeprowadzonych ćwiczeń
==================================

Konkluzją płynącą z przeprowadzonych testów jest konieczność dobierania technologii DBMS adekwatnie do skali i wymagań docelowego projektu. Środowisko **PostgreSQL** jawi się jako idealny wybór dla zaawansowanych rozwiązań biznesowych. Gwarantuje ono pełną zgodność z zasadami ACID, niezawodność przy pracy wielowątkowej i najwyższy poziom bezpieczeństwa, co jednak wiąże się z potrzebą profesjonalnej administracji. Z drugiej strony **SQLite** stanowi doskonałą alternatywę dla projektów na wczesnym etapie rozwoju, mniejszych aplikacji oraz systemów osadzonych. Brak konieczności konfiguracji znacząco przyspiesza prace, choć odbywa się to kosztem wydajności przy jednoczesnych operacjach zapisu przez wielu użytkowników.

Zajęcia udowodniły ponadto, jak ogromne znaczenie ma odpowiednia normalizacja danych. Poprawne doprowadzenie schematu do trzeciej postaci normalnej (3NF) już w fazie projektowej skutecznie zapobiega powstawaniu anomalii podczas późniejszych modyfikacji rekordów, co z kolei radykalnie zmniejsza narzut pracy potrzebny na utrzymanie i rozwój systemu w przyszłości.