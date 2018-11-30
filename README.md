# UMLDiagrams
UML in EAP 9 diagrams

## Sequence diagrams

#### Wallet initialization

```sequence
User->Raspberry Wallet:Podanie ziarna portfela
User->Raspberry Wallet:Podanie danych wejściowych \ndla m modułów
Raspberry Wallet->Shamir:Wygenerowanie m kluczy\n z czego n jest wymaganych
loop dla kazdego wybranego modułu
Raspberry Wallet->Moduły:Ustaw input
Raspberry Wallet->Moduły:Zaszyfruj czesc klucza
Raspberry Wallet->Baza Danych:Zapisz zaszyfrowaną czesc klucza\n dla danego modułu 
end 
Raspberry Wallet->BitcoinJ: stworz portfel na podstawie ziarna
BitcoinJ->BitcoinJ: synchronizacja
loop dla kazdego wybranego modułu 
Raspberry Wallet->Baza Danych:Pobierz zaszyfrowaną \nczęśc klucza 
Baza Danych-->Raspberry Wallet:Zwróć zaszyfrowaną \nczęść klucza
Raspberry Wallet->Moduły: Odszyfruj czesc klucza
Moduły-->Raspberry Wallet: Zwróć odszyfrowanąnczęść klucza
end
Raspberry Wallet->Shamir: Rekonstrukcja klucza \nna podstawie czesci
Shamir-->Raspberry Wallet: Klucz prywatny

BitcoinJ->BitcoinJ: Zaszyfruj portfel \n haszem klucza prywatnego
BitcoinJ->BitcoinJ: Zapisz portfel do pliku 
BitcoinJ->BitcoinJ: Odszyfruj portfel \nhaszem klucza prywatnego
```

#### Wallet unlocking

```sequence
Użytkownik->Raspberry Wallet:Żądanie odszyfrowania portfela
Raspberry Wallet->Użytkownik: Żądanie podania wymaganych danych do oblokowania modułów
Użytkownik->Raspberry Wallet: Wprowadzenie danych
Raspberry Wallet->Moduły: Wprowadzenie danych

loop odszyfrowywanie pojedycznych sekretów
Raspberry Wallet->Raspberry Wallet: Wczytanie [1 z n] zaszyfrowanych sekretów
Raspberry Wallet->Moduły: Żądanie odszyfrowania sekretu
Moduły-->Raspberry Wallet: Zwrócenie odszyfrowanego sekretu
  alt błąd
  Moduły->Raspberry Wallet: Bład odszyfrowywania
  end
end

Raspberry Wallet->Użytkownik: Zwrócenie rezultatu odszyfrowywania portfela
```

#### Authorization server wallet initialization

```sequence
Raspberry Wallet->Raspberry Wallet:Wygenerowanie lub odczytanie UUID portfela
Raspberry Wallet->Serwer autoryzacji:Rejestracja portfela(UUID, hasło)
Serwer autoryzacji->Serwer autoryzacji:Sprawdzenie czy nie był zarejestrowany
alt błąd - portfel już zarejestrowany
Serwer autoryzacji-->Raspberry Wallet:Błąd rejestracji
end 
Serwer autoryzacji->Serwer autoryzacji:Wygenerowanie hasha hasła
Serwer autoryzacji->Redis:Zapisanie danych logowania portfela
Serwer autoryzacji-->Raspberry Wallet:Sukces
Raspberry Wallet->Serwer autoryzacji:Logowanie(UUID, hasło)
Serwer autoryzacji->Redis:Weryfikacja danych
Serwer autoryzacji->Redis:Zapisanie tokenu sesji
Serwer autoryzacji-->Raspberry Wallet: Zwrócenie tokenu sesji

Raspberry Wallet->Serwer autoryzacji:Zapisanie sekretu modułu(token, sekret)
Serwer autoryzacji->Redis:Weryfikacja tokenu sesji
Serwer autoryzacji->Redis:Zapisanie sekretu portfela
Serwer autoryzacji-->Raspberry Wallet:Sukces
```


![Contribution guidelines for this project](przypadkiUzycia.png)
