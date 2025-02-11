    
# Educational Platform Database Project

Projekt został stworzony przez **[Filipa Mokrzyckiego](https://github.com/Filipmok-agh)**, **[Piotra Kacprzaka](https://github.com/pkacprzak5)** oraz **[Mateusza Wójcika](https://github.com/wmaqk1)** w ramach opracowania bazy danych dla platformy edukacyjnej, implementowanej przy użyciu **Microsoft SQL Server**. Celem projektu było zaprojektowanie efektywnego systemu bazy danych, który wspiera potrzeby zarządzania danymi.

## Kluczowe cechy:

- **Wymagania**: Opis zadania projektowego znajduje się w pliku [Wymagania](Wymagania.pdf).
- **Dokumentacja**: Cała dokumentacja projektu została skompilowana w pliku [Dokumentacja](Dokumentacja.md). Dokument ten zawiera szczegółowe wyjaśnienia dotyczące projektowania bazy danych, architektury systemu oraz innych istotnych informacji technicznych.
- **Schemat**: Pełny schemat bazy danych dostępny jest w pliku [Schemat](/Projektowanie/schemat.svg), który oferuje wizualną reprezentację struktury bazy danych, tabel i relacji.
- **Generator Danych**: Dane dla platformy zostały wygenerowane przy użyciu **Fakera**, biblioteki Pythona, która tworzy bardzo realistyczne i sensowne dane testowe. Proces generowania danych jest dokładnie udokumentowany w pliku [data_generate](/generowanie%20danych/data_generate.ipynb), który zawiera prostą instrukcję, jak dane zostały wygenerowane.
- **[Funkcje](/implementacja/funkcje.md), [Triggery](/implementacja/triggery.md), [Widoki](/implementacja/widoki.md), [Procedury](/implementacja/procedury.md) oraz [Tabele](/implementacja/tabele)** : są opisane w osobnych plikach. Umożliwia to łatwą nawigację i zrozumienie poszczególnych elementów bazy danych.

## Technologie użyte:
- **Microsoft SQL Server**: Baza danych została opracowana przy użyciu Microsoft SQL Server, wykorzystując funkcje do zarządzania danymi relacyjnymi.
- **Python (Faker)**: Generowanie danych odbywało się za pomocą biblioteki **Faker** w Pythonie, w celu stworzenia realistycznych, syntetycznych danych testowych dla platformy..