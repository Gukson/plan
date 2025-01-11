
# Instrukcja Konfiguracji VLAN i Przełączników Cisco

## Spis Treści
1. [Konfiguracja Sieci i Przełącznika](#zadanie-1-konfiguracja-sieci-i-przełącznika)
2. [Tworzenie Sieci VLAN](#zadanie-2-tworzenie-sieci-vlan)
3. [Weryfikacja Połączenia](#zadanie-3-weryfikacja-połączenia)
4. [Konfiguracja Dodatkowego Przełącznika](#zadanie-4-konfiguracja-dodatkowego-przełącznika)
5. [Konfiguracja Łącza Trunkowego](#zadanie-5-konfiguracja-łącza-trunkowego)
6. [Konfiguracja z Trzema Przełącznikami](#zadanie-6-konfiguracja-z-trzema-przełącznikami)
7. [Konfiguracja Protokołu STP](#zadanie-7-konfiguracja-protokołu-stp)

---

## Zadanie 1: Konfiguracja Sieci i Przełącznika

### Krok 1: Wyczyść przełącznik i ustaw nazwę
1. Połącz się z przełącznikiem przez konsolę i wejdź w tryb uprzywilejowany:
   ```bash
   enable
   ```
2. Usuń bieżącą konfigurację i dane VLAN:
   ```bash
   erase startup-config
   delete vlan.dat
   reload
   ```
3. Ustaw nazwę przełącznika na S1:
   ```bash
   configure terminal
   hostname S1
   ```

### Krok 2: Zweryfikuj konfigurację
1. Sprawdź, czy przełącznik ma konfigurację domyślną:
   ```bash
   show version
   show vlan
   ```

### Krok 3: Weryfikacja połączenia między hostami
1. Ustaw adresy IP na hostach zgodnie z pulą przydzieloną w instrukcji.
2. Przeprowadź test łączności (ping) między hostami:
   ```bash
   ping <adres_drugiego_hosta>
   ```
3. Sprawdź połączenie z Internetem (na PC1):
   ```bash
   ping <adres bramy lub zewnętrzny adres IP>
   ```

---

## Zadanie 2: Tworzenie Sieci VLAN

### Krok 1: Tworzenie VLAN-ów
1. Wejdź w tryb konfiguracji i utwórz VLANy:
   ```bash
   configure terminal
   vlan 2
   name pracownicy
   vlan 3
   name goscie
   exit
   ```

2. Zweryfikuj stworzone VLAN-y:
   ```bash
   show vlan
   ```

### Krok 2: Przypisanie portów do VLAN-ów
1. Przypisz porty do VLAN 2 i VLAN 3 zgodnie z tabelą (np. VLAN 2: fa0/1-4, VLAN 3: fa0/6, fa0/11):
   ```bash
   configure terminal
   interface range fa0/1-4
   switchport mode access
   switchport access vlan 2
   exit
   interface range fa0/6, fa0/11
   switchport mode access
   switchport access vlan 3
   exit
   ```

2. Sprawdź konfigurację portów:
   ```bash
   show vlan
   show int fa0/1 switchport
   ```

---

## Zadanie 3: Weryfikacja Połączenia

### Testy połączeń między hostami
1. Podłącz hosty do portów VLAN zgodnie ze schematem.
2. Testuj łączność między PC1 a PC2 za pomocą polecenia `ping`.
3. Sprawdź, czy urządzenia w różnych VLAN-ach nie mogą się komunikować.

---

## Zadanie 4: Konfiguracja Dodatkowego Przełącznika

### Krok 1: Skonfiguruj drugi przełącznik (S2)
1. Powtórz kroki z Zadania 1 dla przełącznika S2.
2. Skonfiguruj VLANy na S2, korzystając z tego samego ID:
   ```bash
   configure terminal
   vlan 2
   name pracownicy
   vlan 3
   name goscie
   exit
   ```

### Krok 2: Przypisanie portów
1. Przypisz porty do VLANów na S2 zgodnie z tabelą:
   ```bash
   interface range fa0/4, fa0/7, fa0/9
   switchport mode access
   switchport access vlan 2
   exit
   interface range fa0/12-21
   switchport mode access
   switchport access vlan 3
   exit
   ```

---

## Zadanie 5: Konfiguracja Łącza Trunkowego

### Krok 1: Skonfiguruj trunk na obu przełącznikach
1. Na S1:
   ```bash
   configure terminal
   interface fa0/24
   switchport mode trunk
   exit
   ```

2. Na S2:
   ```bash
   configure terminal
   interface fa0/24
   switchport mode trunk
   exit
   ```

### Krok 2: Weryfikacja trunk
1. Sprawdź status łącza trunkowego:
   ```bash
   show int trunk
   ```

2. Zweryfikuj łączność między PC1 a PC2 w różnych VLANach.

---

## Zadanie 6: Konfiguracja z Trzema Przełącznikami

### Krok 1: Dodaj trzeci przełącznik (S3)
1. Na S3 ustaw łącza trunkowe:
   ```bash
   configure terminal
   interface range fa0/1, fa0/24
   switchport mode trunk
   exit
   ```

### Krok 2: Weryfikacja
1. Sprawdź połączenie między PC1 i PC2 zgodnie ze schematem.
2. Zweryfikuj konfigurację trunków na S1, S2, i S3:
   ```bash
   show vlan
   show int trunk
   ```

---

## Zadanie 7: Konfiguracja Protokołu STP

### Krok 1: Ustaw priorytety STP
1. Na S1 ustaw priorytet dla VLAN 2 jako root primary:
   ```bash
   configure terminal
   spanning-tree vlan 2 root primary
   exit
   ```

2. Na S2 ustaw priorytet dla VLAN 3 jako root secondary:
   ```bash
   configure terminal
   spanning-tree vlan 3 root secondary
   exit
   ```

### Krok 2: Weryfikacja
1. Wyświetl drzewo STP:
   ```bash
   show spanning-tree
   ```

### Krok 3: Symulacja awarii
1. Odłącz łącze i sprawdź, czy sieć rekonfiguruje się automatycznie.
