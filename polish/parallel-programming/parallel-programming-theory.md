# Parallel Programming Theory

## 1. Przetwarzanie współbieżne, równoległe i rozproszone – charakterystyka, różnice

### Rodzaje:

**Współbieżne** - dwa wątki wykonują się współbieżnie gdy rozkazy jednego wątku zaczną być wykonywane **zanim zakończy się wykonywanie rozkazów drugiego**, uruchomionego wcześniej (przeplatanie wątków)

**Równoległe** - szczególny przypadek współbieżnego: wtedy gdy przynajmniej **niektóre z rozkazów wątków wykonywanych współbieżnie są realizowane w tym samym czasie.** (jednoczesne wykonywanie wielu obliczeń w trakcie rozwiązywania jednego problemu.)(maszyny wielordzeniowe- dwa rozkazy jednocześnie)

- od lat każdy uruchomiony program jest wykonywany współbieżnie z innymi programami (wielozadaniowość systemów operacyjnych) - każdy program jest osobnym zbiorem rozkazów, a systemy op. pozwalają na ich współbieżne wykonywanie.

**Rozproszone -** szczególny przypadek równoległego: wątki składające się na program wykonywane są **na różnych komputerach połączonych siecią** (Wykonują one osobno poszczególne etapy zadania i odsyłają wyniki do jednego wspólnego centrum nadzoru.)

- często do celów testowania, systemy rozproszone uruchamiamy na jednym komputerze, więc istotą systemu rozproszonego jest fakt, że może on być wykonywane na różnych komputerach połączonych siecią.

### **Cele przetwarzania równoległego i rozproszonego:**

- **zwiększenie efektywności obliczeń**  = szybsze wykonywanie zadań = skrócenie czasu rozwiązywania konkretnego pojedynczego zadania obliczeniowego
    - efektywne, gdy pozwala na kilku do kilkunastokrotne skrócenie czasu realizacji zadań w stosunku do czasu sekwencyjnego
- **zwiększenie niezawodności przetwarzania** - np. gdy kilka wątków pracuje naraz i jeden ulega awarii pozostają inne świadcząc odpowiednie usługi, aż do czasu naprawy pierwszego wątku, szczególnie przetwarzanie rozproszone - awaria jednego komputera nie musi prowadzić do zaprzestania dostarczania usług
- **zwiększenie elastyczności wykorzystania dostępnych zasobów komputerowych**
    - przykładem np. cloud computing - użytkownik korzystający z lokalnej maszyny ma dostęp do zasobów danego systemu, który sam dobiera odpowiednie oprogramowanie, sprzęt.

## 2. Proces, wątek – charakterystyki, różnice; funkcje: fork (exec, wait), clone – parametry, przykłady użycia

### Proces

**-** program w trakcie wykonania; sama definicja zawiera w sobie wiele mechanizmów, za pomocą których SO zarządzają wykonywaniem programów.

- każdy proces jest związany z wykonywalnym plikiem binarnym, który zawiera kod program oraz wątki wykonujące rozkazy
- obecnie rozkazy z tego pliku są coraz częściej wykonywane przez wiele współbieżnie pracjących wątków, tak więc **rozróżniamy procesy jednowątkowe i wielowątkowe.**
- zarządza nim SO, który przydziela odpowiednie obszary pamięci(inne dla różnych współbieżnie wykonywanych procesów) ,zarządza dostępem do pamięci, urządzeń IO, nadzoruje przydział procesora
    - aby tego dokonać SO tworzy dla każdego procesu strukturę danych, zwana blokiem kontrolnym procesu
- każdy proces posiada odrębną przestrzeń adresową, każdy proces może dokonywać odczyt/zapis komórek pamięci tylko w swojej przestrzeni adresowej - segmentation fault w Linux = próba dostępu do przestrzeni adresowej innego procesu.

**Tworzenie nowego procesu:**

1. **fork —** tworzenie nowego procesu potomnego, przez proces macierzysty - ma inny identyfikator ale jest prawie kopią macierzystego (kopia bloku kontrolnego). (macierzysty PID:0, potomny:jakis tam)
- CZASEM: posiadają tą samą przestrzeń adresową: wtedy SO musi tak zarządzać korzystaniem z pamięci, żeby utrzymywać wrażenie posiadania odrębnych!

2. **execl**

Następnie proces potomny wywołuje **execl (**jedną z potomnych exec - powiazanie procesu z plikiem wykonywalnym) - w następstwie proces ją wykonujący zaczyna wykonywać kod z pliku wskazanego jako argument procedury tzn. 

1. Umieszczenie w przestrzeni adresowej procesu potomnego nowego kodu z pliku wykonywalnego
2. Zmiana bloku kontrolnego procesu - tak, żeby odpowiadał realizacji nowo wczytanego pliku binarnego
1. **wait() - proces nadrzędny wywołuje zaraz po użyciu fork**
    - powoduje zawieszenie pracy wątku nadrzędnego aż do czasu zakończenia pracy wątku potomnego
    - (jest to też mechanizm synchronizacji wątków!)

### Wątek

- sekwencyjnie wykonywany zbiór rozkazów (część procesu)
- w przypadku braku synchronizacji dopuszczalne jest przeplatanie rozkazów
- kod dla wszystkich wątków jest ten sam, w tej samej przestrzeni adresowej
- pojedynczy wątek korzysta z przestrzeni adresowej procesu nie tylko dla wykonywanego przez siebie kodu, ale także dla danych na których pracuje.
- **Przestrzeń adresowa to cecha procesu, a nie wątku**
- każdy wątek posiada odrębny stos (aby trzymać swoje zmienne prywatne,niedostępne dla innych wątków!)(ta sama zmienna prywatna w każdym wątku odnosi się do innej komórki pamięci (ale w tej samej przestrzeni adresowej)(te zmienne prywatne to zmienne lokalne i argumenty)

## 3. Wątki Pthreads – tworzenie, niszczenie, zarządzanie; przykłady tworzenia i przesyłania argumentów do wątków

- do uzupełnienia

## 4. Współbieżność: wyścig (race condition, data race), sposoby unikania – różne rodzaje synchronizacji

### **Wyścig**

- dwa lub więcej wątków mają dostęp do danych dzielonych i próbują je zmieniać w tym samym czas
- algorytm planowania wątków może skakać po różnych wątkach w tym jakimkolwiek czasie, więc nie wiadomo, w jakiej kolejności wątki będą próbować uzyskać dostęp do dzielonych danych
- oba wątki więc, uczestniczą w wyścigu do dostępu i zmiany danych dzielonych
- wszystko jest zależne od thread scheduling algorithm
- aby uniknąć sytuacji kiedy wątki wykonują dane operacje po spełnieniu jakiegoś warunku, np zmienna równa X należy zwykle ustawiać lock żeby zastrzec, że tylko jeden wątek ma do tego dostęp w tym czasie
- inne wątki w tym czasie czekają, dopóki lock nie zostanie zniesiony

### **Data race vs race condition**

- **data race** jest sytuacja gdy:
    1. Dwuie instrukcje z różnych wątków uzyskują dostęp do tej samej komórki pamięci
    2. Przynajmniej jeden z tych dostępów to write
    3. Nie ma synchronizacji do tych dostępów
- **race condition** to błąd semantyczny-  wada w synchronizacji lub kolejności zdarzeń prowadząca do błędnego zachowania programu
- większość race conditions są spodobane przez data race, ale nie zawsze:
    - dajmy na to, że poprawność programu jest zagrożona bo wątek1 zapisuje x=1, a wątek2 zapisuje x=2 to wpływa na błędne zachowanie programu, mimo, że nie ma wyścigu danych!
- łatwiej wykrywać data races, niż race condition

**Synchronizacja**

- realizacja programowa: bariera, sekcja krytyczna, operacje atomowe
- realizacja sprzętowa: komputery macierzowe, architektura SIMD(single instruction multiple data - każdy procesor procesuje różne dane) [SPMD to dużo szersze pojęcie gdzie programy są podzielone pomiędzy wiele procesorów i operują na różnych danych]

## 5. Wzajemne wykluczanie: sposoby realizacji, wykorzystywane narzędzia i konieczne własności realizujących funkcji

- **wzajemne wykluczanie** - wymaganie, aby ciąg operacji na pewnym zasobie(zwykle pamięci) był wykonany w trybie wyłącznym przez tylko jeden z potencjalnie wielu procesów
- pojęcie sekcji krytycznej - jest to ten ciąg który wykonany musi być w trybie wyłącznym
- przy wejściu do sekcji krytycznej proces wykonuje protokół wejścia, w którym sprawdza czy może wejść do tej sekcji krytycznej
- po wyjściu wykonuje protokół wyjścia aby poinformować inne proces że opuścił sekcje krytyczną i inny proces może ją zająć

1. W sekcji krytycznej może być jeden proces, nie mogą być więc intrukcje przeplatane

2. Proces może się zatrzymać w sekcji lokalnej, nie może w sekcji krytycznej.

- w systemach unixowych używa się mechanizmów zapewniających wzajemne wykluczanie tzw **mutexów (mutual exclusion - skrót)**

Proces:

1. Tworzenie obiektu mutex **mutex_init**- wykonanie funkcji pozostawia zmienną mutex w stanie zablokowanym
2. Zablokowanie sekcji krytycznej **mutex_lock -** gdy przynajmniej jeden proces wykonał wcześniej funkcję mutex_lock zmienna mutex będzie oznaczona jako zajęta, proces bieżący wykonujący tę funkcję będzie wstrzymany
3. Zwolnienie sekcji krytycznej **mutex_unlock -** gdy jakieś procesy czekają na wejście do sekcji krytycznej to jeden z nich zostanie odblokowany i wejdzie do niej, jak nie ma procesów oczekujących to sekcja zostanie oznaczona jako wolna
4. Skasowanie obiektu mutex **mutex_destroy**

## 7. Wzajemne wykluczanie: mutexy – wykorzystanie w bibliotece Pthreads

- Mutexy to podstawowy sposób na implementację synchronizacji wątków by chronić dzielone dane kiedy występuje wiele prób zapisu
- zmienna mutex działa jak lock zabezpieczający dostęp do dzielonych danych, działa tak ze tylko jeden wątek może zablokować zmienną mutex w danym czasie, nawet jak wiele wątków próbuje to zrobić w tym samym czasie tylko jednemu się to uda
- mutexy zapobiegają "race condition"
- najczęściej są własnie przydatne gdy wiele wątków próbuje nadpisać jakąś zmienną glonalną, gwarantuje to powodzenie w tym
- kiedy wiele wątków walczy o mutexa, przegrani blokują się, a gdy odblokowywują to używają **trylock**" zamiast "lock"

### W Pthreads:

INICJALIZACJA:

**pthread_mutex_t mutexname = PTHREAD_MUTEX_INITIALIZER;**

albo dynamicznie pthread_mutex_init()

- atrybuty można dopisać jako attr, ale muszą byc typu pthread_mutexattr_t
    - przykłady: protocol, priorityceiling, processshared

**pthread_mutex_destroy() -** NISZCZENIE

**pthread_mutex_lock() , pthread_mutex_trylock(), pthread_mutex_unlock() - jak w pyt5.**

## 6. Współbieżność: cechy pożądane programów, rodzaje błędów wykonania

- cechami pożądanymi jest oczywiście poprawność, poprawność synchronizacji przez cały czas działania

### Wstęp - przeplot

- ciąg złożony z przeplecionych ciągów wykonawczych procesów składowych
- musimy uwzględnić każdą możliwą kolejność współbieżnych akcji
- jeśli chcemy pokazać, że program nie działa poprawnie to wystarczy pokazać jeden przeplot, przy którym dochodzi do sytuacji błędnej
- ciągów takich jest nieskończona ilość, bo procesy które się składają na program współbieżny działają w pętli nieskończonej
- błąd jednak ciężko znaleźć, bo może się ujawnić dopiero po 10000 uruchomieniach..

### Własność bezpieczeństwa

- "nigdzie nie dojdzie do niepożądanej sytuacji"
- warunek przebywania w sekcji krytycznej max 1 procesu; zasada wzajemnego wykluczania

### Własność żywotności

- gdybyśmy martwili się tylko o bezpieczeństwo to wtedy wystarczyłoby ustawić czerwone światło dla każdego procesu i żadnego nie wpuszczać, co byłoby bezpieczne tylko po co.
- **"każdy proces, który chce wejść do sekcji krytycznej, w końcu wejdzie"** - nie jest mowa o każdym tylko o tych co chcą wejść!

### Błędy wykonania w wyniku braku żywotności:

- zakleszczenie - globalny brak żywotności - nic się nie dzieje, system nie pracuje, oczekując na zajście zdarzenia kóre nigdy nie zajdzie
    - np dwa procesy potrzebują zasób1 i zasób2 - najpierw proces1 dostanie zasób1, a proces2 dostanie zasób2, a potem proces1 potrzebuje zasobu2, a proces2 potrzebuje zasobu1, a żaden nie chce zrezygnować z zasobu który już otrzymał więc oczekiwanie będzie w nieskończoność
- zagłodzenie - lokalny brak żywotności, czyli zawsze pracuje pewna grupa procesów, ale inne cały czas stoją - jak auta na skrzyżowaniu jadące ciągle wschód-zachód  nie puszczające aut z północy

## 8. Pojęcia SPMD i MPMD

### Ogólne

- oba to wysokopoziomowe modele programowania równoległego
- SPMD jest popularniejszy

### SPMD

Single Program Multiple Data

- **wiele niezależnych procesorów wykonuje ten sam program** (wątki nie muszą uruchamiać jednak całego programu, mogą tylko potrzebne dla nich części - w przeciwieństwie do SIMD) **na różnych danych**
- ten sam kod może być współbieżnie wykonywany przez inne wątki z innymi danymi wejściowymi, czyli każdy wątek coś tam policzy i całkowitym efektem będzie rozwiązanie szerszego problemu
- jeśli nie zostaną wprowadzone mechanizmy synchronizacji pracy wątków, kolejność realizacji rozkazów różnych wątków może być dowolna, każdy wątek pracuje sekwencyjnie, sposób ich przeplatania nie jest okeślony i za każdym razem może być inny. (Gdy mamy maszynę wieloprocesorową to trzeba dopuścić możliwość, że dwa rozkazy są wykonywane jednocześnie)
- **Implementacja SPMD** polega na wprowadzeniu pojedynczej liczby, identyfikatora różnego dla każdego z wątków współbieżnie realizujących ten sam kod,  a następnie wykorzystywanie tego identyfikatora do zróżnicowania zadania wykonywanego przez każdy wątek
- SINGLE PROGRAM: wszystkie zadania

### MPMD

Multiple Program Multiple Data

- wiele niezależnych procesorów wykonuje przynajmniej dwa niezależne programy
- działa niby tak, że system wybiera jeden węzeł który zostaje managerem, który uruchamia program który wysyła wszystkie dane do innych węzłów, z których każdy uruchamia drugi program. Każdy z tych węzłów wysyła wyniki bezpośrednio do menedżera. Przykład: Playstation 3

## 9. Tworzenie programów równoległych: wzorce, sposoby realizacji

### Dwa kroki o zasadniczym znaczeniu:

1. Wykrycie dostępnej współbieżności w trakcie realizacji programu.
2. Określenie koniecznej synchronizacji lub wymiany komunikatów pomiędzy procesami lub wątkami realizującymi problem.

### Wzorce:

- są to ogólne wskazówki rozwiązania problemu dekompozycji zadania na równoległe podzadania.
- konkretne programy będą realizować wiele wzorców
1. **Manager-worker** - jest jeden proces nadrzędny zarządzający innymi
2. **Dziel i rządź** - dynamiczne, rekursywne zarządzanie obliczeniami
3. **Zrównoleglenie pętli** - każdy z procesorów oblicza fragment pętli
4. **Przetwarzanie potokowe** - cykl dzieli się na odrębne bloki przetwarzania, z których każdy oprócz ostatniego jest połączony z następnym → dane przechodzą po tych blokach do następnego ...
5. **Aktorzy** - przetwarzanie sterowane zdarzeniami - przepływ programu zależy od wydarzeń
6. **MapReduce** - na każdym logicznym rekordzie danych wykonuje się **map** - otrzymuje średnie wyniki w postaci par (key,value) , a potem wykonuje się **reduce** na wszystkich wartościach o tym samym kluczu.

## 10. Tworzenie programów równoległych: typy dekompozycji

1. **Dekompozycja domeny** - podział danych na obszary - na każdym z podobszarów wykonywany jest jednolity algorytm
2. **Dekompozycja funkcjonalna** - w zadaniu wyodrębnia się funkcje, które mogą być wykonywane niezależnie (uwaga: dzieli się przetwarzanie, nie dzieli się danych!)
3. **Dekompozycja rekursywn**a - wielokrotne dzielenie zadania na wymiarowo mniejsze problemy (wykonywane tak długo, aż wymiar zadania będzie na tyle mały, że rozwiązanie go będzie proste)
4. **Dekompozycja eksploatacyjna** - przeszukiwanie przestrzeni rozwiązań - np. poszukiwanie drogi w labiryncie

## 11. Dekompozycja dla pętli, tablic 1D: blokowa, cykliczna, mieszana

1. Blokowa - dane podzielone na liczbę porcji liczbą liczbie procesów
2. Cykliczna - po kolei przypisujemy iteracjom kolejne wątki, aż do ostatniego wątku, przechodzimy od nowa do pierwszego i znowu pokolei kolejne wątki. każdy wątek otrzyma tyle iteracji ile wynosi liczba iteracji/liczbe wątków
3. Blokowo-cykliczna
- Kiedy przypadków mało, a wątków relatywnie dużo to w blokowej większość z nich jest
bezczynna, natomiast w cyklicznej kończą mając jedną iterację mniej.

## 12. Dekompozycja dla tablic 2D: wierszowa, kolumnowa, szachownicowa

- wierszowa/kolumnowa - jeden lub więcej wierszy/kolumn dla każdego z wątków
- szachownicowa - jeden lub więcej elementów dla każdego z wątków

wewnątrz dokonujemy dekompozycji już blokowej cyklicznej lub blokowo-cyklicznej

## 13. Java: wątki - zarządzanie, przydzielanie zadań wątkom

- wątek w Javie to obiekt, wielowątkowość jest wbudowana w podstawy systemu
- klasa Object zawiera metody związane z przetwarzaniem współbieżnym (wait, notify, notifyAll)
- każdy obiekt może się stać elementem przetwarzania współbieżnego

- wątki można tworzyć za pomocą interfejsu Runnable oraz przez rozszerzanie Thread klasy

- **THREAD: stworzenie instancji tej klasy i odpalenie metody start(), która to odpala metodę run() - run należy zaiplementować też.**

```java
public class Main extends Thread {
  public static void main(String[] args) {
    Main thread = new Main();
    thread.start();
    System.out.println("This code is outside of the thread");
  }
  public void run() {
    System.out.println("This code is running in a thread");
  }
}
```

- **klasa implementująca RUNNABLE: trzeba włożyć jako argument konstruktora Thread instancję obiektu naszej klasy, i odpalić metodę start(), ona odpala run.  Start() Tworzy nowy wątek który wykonuje kod napisany w run.**

```java
public class Main implements Runnable {
  public static void main(String[] args) {
    Main obj = new Main();
    Thread thread = new Thread(obj);
    thread.start();
    System.out.println("This code is outside of the thread");
  }
  public void run() {
    System.out.println("This code is running in a thread");
  }
}
```

- Kiedy klasa rozszerza Thread nie może już rozszerzać żadnej innej klasy a jak implementuje interfejs Runnable to już git.

## 14. Java: pule wątków – interfejsy: Executor, ExecutorService, klasy: Executors,ForkJoinPool; przydzielanie zadań wątkom i synchronizacja

### Framework Executor

- jest elementem pakietu *java.util.concurrent*
- dzięki niemu praca z wątkami w Javie jest łatwiejsza
- jest używany do odpalania obiektów Runnable bez tworzenia nowych wątków za każdym razem **( ponowne używanie stworzonych już wątków!)**
- Interfejsy:
    - **Executor** - zawiera metodę execute() by rozpocząć zadanie wyspecyfikowane w obiekcie Runnable
    - **ExecutorService** - to Executor wraz z przydatnymi metodami do kontroli wykonania zadania
- klasa: **Executors**:
    - zawiera metody wytwórcze do tworzenia różnych typów ExecutorService

- **Thread pool**
    - to **kolekcja obiektów typu Runnable**(**work queue**) i **kolekcja ciągle działających wątków** (sprawdza czy jest jakaś nowa praca do zrobienia, a jeśli jest to odpali Runnable object z kolejki)
    - by używać executor framework - musimy stworzyć thread pool i zgłosić zadanie do wykonania.
    - metody klasy Executors do tworzenia thread pools:
        - **newSingleThreadExecutor()** - tworzy jeden wątek, więc można wykonywać tylko 1 zadanie w 1 momencie, reszta będzie czekać w kolejce.
        - **newFixedThreadPool() -** podajemy liczbę wątków (dobre gdy chcemy mieć limit współbieżnych zadań)
        - **newCachedThreadPool() -** max rozmiar thread pool równy max integer value w javie (ale generalnie będzie tworzyć i kończyć wątki gdy są nieaktywne po minucie)(dobre gdy liczy się wydajność)
        - **newScheduledThreadPool() -** pozwala planować zadania do odpalenia po jakimś danym opóźnieniu tudzież w jakichś odstępach czasowych

Przykład użycia:

**ExecutorService** executor = **Executors**.**newFixedThreadPool(3);**

- na końcu trzeba zakończyć pracę Thread poola przy użuciu **shutdown()**

### ForkJoinPool

- **rekursywne przetwarzanie tasków;** dziel i rządź; dzieli poszczególne taski na mniejsze, potem przetwarza mniejsze taski, łączy wyniki

### Synchronizacja

Metoda(dopisać po public/private) lub blok(zapis jak funkcja w jsie) o nazwie jak się można domyślić: **synchronized** 

- no typowo, realizacja przez jakiś wątek uniemożliwia realizacje przez inny wątek w tym samym czasie - wątki które zgłaszają chęć wykonania dowolnej synchronizowanej metody ustawiają się w kolejce
- ten sam wątek może walić kilka kolejnych wywołań synchronizowanych metod, gdy realizue wszystkie z tego obiektu JVM wznawia działanie pierwszego wątku oczekującego w kolejce

[pytanie 16] producer-consumer problem, mechanizm monitora z java.lang.Object, wait,notify,notifyAll

## 15. Java: interfejsy – Runnable, Callable, Future, RecursiveAction, RecursiveTask

**Runnable -** interfejs który należy zaimplementować do klasy której instancja ma być wywoływana przez wątek. Zadanie jest wykonywane przez metodę run, nie zwraca wyniku

**Callable** - umożliwia definiowanie zadań, które zwracają wynik i daje exception

**Future -** reprezentuje wynik asynchronicznego zadania, kiedy tworzone jest asynchroniczne zadanie zwracany jest obiekt tego typu, posiada metodę **get()** zwracającą Object,(ale może też być obiekt własnej klasy), metodę future.**cancel()** by anulować zadanie, można również sprawdzić czy zadanie jest skończone przez **isDone()** i wynik jest dostępny i **future.isCancelled()** by sprawdzić czy został odwołany.

klasy **RecursiveAction i RecursiveTask -** implementują interfejs Future. pierwszy nie zwraca wyniku(tylko null), drugi zwraca rekursywnie wynik ForkJoinTask.

## 16. Java: wzajemne wykluczanie

- Zwykle jest dokonywane w javie przy użyciu bloku(zaposywanego jak funkcje w jsie) albo zaznaczeniu metody jako **synchronized**
- Wtedy tylko jeden wątek może wykonywać taką metodę w danym czasie
- **monitor -** mechanizm do kontroli współbieżnego dostępu do obiektu
- Sam mechanizm synchronizacji jest zapewniony w hierarchii obiektów w java.lang.Object. **Każdy obiekt ma więc automatycznie monitor (mutex) przypisany do niego.** Jeśli wywołujemy jakąś metodę synchronized to deklarujemy, że podczas uruchomienia musi zostać założony lock na monitor tego obiektu przed wywołaniem tej metody i musi go potem zwolnić.
- monitory istnieją tylko dla obiektów, NIE DLA FUNKCJI, więc jak mamy jakiś obiekt i kilka funkcji synchronized to i tak jest jeden monitor
- wszystkie obiekty mogą działać jako monitor, mają lock ale i mają metody wait, notify i notifyAll - wszystkie mogą zostać wywołane jeśli założony zostanie lock synchronizujący

### Synchronizacja:

- producer-consumer problem: gdy Producer produkuje jakieś zasoby, a Consumer je konsumuje, to producer musi je włożyć w jakieś miejsce pamięci nazywane buforem, a konsumer musi je stamtąd zabrać, ale oboje nie mogą korzsytać jednocześnie z tego buforu
- kiedy jakiś wątek wywołuje **wait()** do jakiegoś obiektu, wątek zawiesza się i dodaje do wait set dopóki jakiś inny wątek wywoła notify() lub notifyAll() na tym obiekcie. - notify budzi wątki, które są w wait set monitora tego obiektu. (notify budzi randomowo, a notifyAll budzi wszystkie - obudzone wątki będa rywalizować o dostęp**)**

## 17. Zmienne warunku, rozwiązanie problemu producenta-konsumenta

### Zmienne warunku

- zmienne wspólne dla wątków
- służą do identyfikacji grupy uśpionych wątków

**int pthread_cond_x, gdzie x=**

**init(cond, cond_attr) -** tworzenie zmiennej warunku **cond**

**wait(cond,mutex)** - uśpienie wątku w miejscu identyfikaowanym przez zmienną **cond** (pthread_cont_t *cond) - wątek idzie spanko do momentu, aż jakiś inny wątek wyśle sygnał budzenia dla tej zmiennej **cond**

**signal(cond)** - budzenie pierwszego w kolejności wątku oczekującego dla zmiennej **cond**

**broadcast(cond) -** budzenie wszystkich wątków oczekujących na tej zmiennej **cond**

**Rozwiązanie problemu producenta-konsumenta:**

Najpierw 

zainicjować mutex_t(mutex) i cond_t (nie_pelny, nie_pusty)

zainicjować **pthready**: (pthread_t) producent, konsument (**można je potem zidentyfikować)**

zainicjować zasób(pozwala na wstawianie i pobieranie z niego)

utworzyć pthread_create(dać jako arg. producent i produkuj, potem konsumuent i konsmuj) - **utworzenie wątków z pthreadów i dopisanie funkcji jakie mają wykonywać**

utworzyć pthread_join(dać jako argument producent, a potem konsument) - **zapewnia mechanizm pozwalający aplikacji czekać na udane zakończenie wykonania wątku**

**produkuj**: w pętli for:

zakładamy **pthread_mutex_lock(&mutex)**

dopóki (**bufor pełny**) **wait(nie_pelny, mutex) - póki bufor pełny usypiamy wątek w zmiennej warunku nie_pełny**

**wstawZasób - jak się zwolni wstawiamy**

unlock(mutex) **zwolnij muteksa**

signal(**&nie_pusty**) - **obudź wątek oczekujący dla zmiennej nie_pusty**

**konsumuj**: w pętli for:

zakładamy **pthread_mutex_lock(&mutex)**

dopóki (**bufor pusty**) **wait(nie_pusty, mutex) - póki bufor pusty usypiamy wątek w zmiennej warunku nie_pusty**

**pobierzZasób**

unlock(mutex) **zwolnij muteksa**

signal(**&nie_pełny**) - **obudź wątek oczekujący dla zmiennej warunku nie_pełny**

## 18. Problem czytelników i pisarzy – zamki odczytu i zapisu

- jedna grupa procesów (pisarze) modyfikuje zasób, druga grupa (czytelnicy) tylko odczytuje stan zasobu
    - pisać może jednocześnie tylko jeden wątek
    - czytać może wiele wątków naraz

### Rozwiązanie przy pomocy monitorów:

Pisarze: **chcę pisać → piszę → koniec pisania**, analogicznie czytelnicy

**(protokół wejścia → operacja → protokół wyjścia)**

wprowadzamy **condition** (zmienne warunku): **czytelnicy**, **pisarze**

**protokół wejścia chcę_pisać**

jeśli l_czytelników+l_pisarzy>0 **wait**(**pisarze**)  - **jeśli są jacyś czytelnicy albo pisarze to wątek na zmiennej pisarze idzie spanko (i będzie czekał na wybudzenie)**

liczba_pisarzy++

**protokół wyjścia koniec_pisania**

liczba_pisarzy--

jeśli ~empty(**czytelnicy**) **signal**(**czytelnicy**) - **jeśli jakiś wątek oczekuje na zmiennej czytelnicy to to go budzimy** (pozwala działać czytelnikom- nie dopuszcza do zagłodzenia)

w przeciwnym przypadku: **signal**(**pisarze**) - **jeśli nikt nie oczekuje to budzimy jakiś wątek oczekujący na zmiennej pisarze**

**protokół wejścia chcę_czytać**

jeśli l.pisarzy> 0 lub ~empty(pisarze) **wait**(**czytelnicy**) **jeśli są jacyś pisarze lub jakiś wątek oczekuje na zmiennej pisarze to wątek na zmiennej czytelnicy idzie spanko**

l_czytelnikow++

**signal**(**czytelnicy**) **budzimy jakiś wątek oczekujący na zmiennej czytelnicy**

**protokół wyjścia koniec_czytania**

l_czytelników--

jeśli l_czytelników = 0 **signal**(**pisarze**) **budzimy pisarzy przy wyjściu, nie wpuszczamy nowych przy wychodzeniu**

### Zamki odczytu/zapisu:

- alternatywa do stosowania konstrukcji programistycznych jest zastosowanie gotowych API pod konkretne problemy, np tutaj te zamki:

    **pthread_rq_lock_x, gdzie x=:**

    **init, destroy -** tworzenie zamków na podstawie odp. obiektów, niszczenie zamków

    **rdlock, tryrdlock -** zamknięcie do odczytu (**read lock)**

    **wrlock**, **trywrlock** - zamknięcie do zapisu (**write lock)**

    **unlock -** otwarcie zamka

## 19. OpenMP – charakterystyka, elementy składowe

### Charakterystyka:

- API dla programów napisanych w C/C++; zestaw dyrektyw kompilatora który wspiera programowanie równoległe w środowiskach dzielonej pamięci
- znajduje regiony równoległe jako bloki kodu, które mogą być odpalone równolegle
- dewelopera zadaniem jest włożyć dyrektywy kompilatora w równoległe miejsca kodu, i te dyrektywy wskazują bibliotece OpenMP by uruchomić ten region równolegle.

**Zalety:** 

- przenośność kodu - niezależny od platformy
- prostość - w porównaniu do MPI - nie trzeba wysyłać wiadomości
- dekompozycja jest wykonywana automatycznie przez dyrektywy
- skalowalność podobna do MPI
- jeśli używamy sekwencyjnych kompilatorów, to dyrektywy openmp są w postaci komentarzy, więc ten sam kod może być odpalony zarówno równolegle jak i sekwencyjnie

**Wady:**

- możliwość wprowadzenia bugów synchronizacji i błędów semantycznych tj. race conditions
- wymaga kompilatora który wspiera openmpi

### Elementy składowe:

Są to odpowiednie mechanizmy które można podzielić na:

1. K**ontrola równoległości**
    - dyrektywa **parallel**
2. **Dystrybucja pracy na wątki**
    - dyrektywy:
        - **omp for** lub **omp do**
        - **sections**
        - **single**
        - **master**
3. **Zarządzanie danymi**
    - klauzule:
        - **shared**
        - **private**
4. **Synchronizacja**
    - dyrektywy:
        - **critical**
        - **atomic**
        - **ordered**
        - **barrier**
        - **nowait**
5. **Środowisko uruchomieniowe**
    - zmienianie liczby wątków i zmienne środowiskowe
        - **omp_set_num_threads**() - ustawianie liczby wątków
        - **omp_get_thread_num() -** zwracanie liczby wątków
        - **OMP_NUM_THREADS -** zmienna środowiskowa, początkowa liczba i max liczba wątków
        - **OMP_SCHEDULE** - planowanie wykonywania zadań: static, dynamic, runtime and guided.

## 20. OpenMP: dyrektywa parallel – składnia, działanie, klauzule

**#pragma omp parallel**

- tworzy tyle wątków ile jest rdzeni w systemie
- wszystkie wątki naraz wykonują równoległy obszar (dopóki nie użyjemy dyrektyw podziału pracy)
- kiedy każdy wątek gdy wychodzi z równoległego regionu to jest on zakańczany

    **klauzule:**

    **if** - warunek od którego zależy czy pętla będzie wykonana równolegle czy sekwencyjnie

    **num_threads -**  ustawia liczbę wątków

    **klauzule zmiennych**

    **private(var)** -  każdy wątek w zbiorze wątków trzyma prywatną kopię tej zmiennej

    **shared(var) -** istnieje jedna instancja ten zmiennej która jest przekazywana między wszystkimi wątkami. 

    **firstprivate(var)** - każdy wątek powienien mieć własną kopię zmiennej var, która powinna być zainicjalizowana z wartością zmiennej var, bo istniała zanim nadeszło wykonanie równoległe

    **reduction(operacja:var)**  informujemy kompilator że do wartości zmiennej będziemy wykonywać operację (np dodawanie to +, odejmowanie to -). kompilator wtedy utworzy odpowiednią liczbę prywatnych kopii zmiennej var dla każdego wątku i rodzieli iteracje między wątki. każdy wątek będzie operował na swojej kopii var, po wyliczeniu wartości wyliczone przez wszytskie wątki są do siebie dodawane(albo inna operacja!). Wartość obliczonej zmiennej jest dostępna również poza sekcją parallel

    **copyin -** mechanizm pozwalający na udostępnienie wartości zmiennej threadprivate z wątku master do zmiennej threadprivate każdego innego wątku wykonującego równoległy region

## 21. OpenMP: zmienne w obszarze równoległym, dyrektywy i klauzule dotyczące zmiennych

zmienne albo **shared**(jedna kopia udostępniana między wątkami) albo **private**(prywatna dla wątku)

**firstprivate**-własna kopia, ale z wartością pocz. taką jaką miała zanim weszłą w r.równol.

**lastprivate -** na końcu przywraca wartość dzielonej zmiennej (w for albo sections, nie parallel)

**reduction -** każdy wątek pracuje na swojej kopii ale pozniej wykonywana jest operacja(np+)

**copyin -**  master-wątek.threadprivate → wszystkie-wątki.threadprivate

- **default(shared)** - działa tak samo jak nie zostanie nic zadeklarowane, czyli domyślnie, a działa to tak, że jakakolwiek zmienna w regionie równoległym będzie traktowana jakby była zadeklarowana w shared. (chyba, że jest to zmienna iteracyjna pętli, wtedy jest private!)
- **default(none)** wywali błąd kompilatora jak nie napiszemy jakie są zmienne (private, shared, reduction, firstprivate,lastprivate) - tzn. musimy dopisać te klauzule i w nawiasie te zmienne dla każdej zmiennej
- Sensem używania default(none) jest oznaczenie, że to programista musi
dokonać podziału które zmienne są shared, a które private. Dzięki temu nieuważny programista nie popełni błędu polegającego na braku oznaczenia jaka jest dana zmienna. Używanie defualt(none) jest zalecaną praktyką zwiększającą niezawodność pisanego kodu.

## 22. OpenMP: dyrektywy synchronizacji i podziału pracy

### Dyrektywy podziału pracy:

**omp for** lub **omp do** -do podzielenia iteracji pętli pomiędzy wątki

**sections -** nadawanie kolejnych niezależnych bloków do różnych wątków

**single** - blok kodu wykonywany tylko przez jeden wątek(barrier musi być na końcu)

**master** - podobny do single, ale będzie wykonywany przez master thread(bez barrier na koncu)

### **Dyrektywy synchronizacji:**

**critical -** sekcja krytyczna - zabezpieczenie przed race conditions - wykonanie tego bloku tylko przez jeden wątek w jednym czasie

**atomic -** j/w ale mają mniejszy koszt więc trochę szybsze, (critical zapewnia serializację całego bloku, a atomic tylko operacji)(tak więc do pojedynczego obliczenia użyć atomic, a bardziej skomplikowanych regionów)(tak więc po deklaracji, następny update pamięci będzie wykonany w sposób atomic, ale nie czyni całego kodu atomic)

**ordered -** różne wątki wykonują się współbieżnie dopóki nie uzyskają na swojej drodze ordered region, który ma zostać wykonany sekwencyjnie

**barrier -** każdy wątek czeka dopóki wszystkie inne wątki dojdą do tego punktu. (generalnie to dyrektywy podziału pracy mają taką barierę na końcu)

**nowait -** wątki wykonujące swoją robotę mogą kontynować bez czekania aż inne wątki skończą, bez tej klauzuli wątki napotkają barierę synchronizacji na końcu konstruktu podziału pracy

## 23. OpenMP: zrównoleglenie pętli, działanie, zarządzanie wykonaniem – klauzule

1. Zdefiniować jakiś blok równoległy **#pragma omp parallel [clauses]**
2. Zdefiniować w nim **#pragma omp for [clauses]** 

    dozwolone klauzule:

    klauzule zmiennych(**private**, **firstprivate**, **lastprivate**), 

    **reduction - praca na swojej kopii ale później operacja (np+)**

    **nowait - jeśli się umieści wątki nie muszą czekać aż inne skończą. JAK TEGO NIE MA TO MUSZĄ CZEKAĆ, DOMYŚLNIE UMIESZCZANA JEST NIEJAWNA BARIERA**

    **schedule(type, chunk) -** **szczególnie przdyatne właśnie w for-loop i do-loop. Iteracje są właśnie przupisywane do wątku na podstawie klauzuli schedule**

    - **static** - każdy wątek dostaje swoje iteracje zanim zaczną wykonywać iteracje pętli. Domyślnie liczba iteracji dla wątki jest równa dla każdego wątku, jednak uzupełnienie drugiego argumentu (chunk) może wskazać ile kolejnych iteracji na być przypisanych do wątków
    - **dynamic**- niektóre iteracje są przypisane do mniejszej liczby wątków, kiedy jakiś wątek zakończy swoje iteracje, dostaje kolejne z tych co zostały, drugi argument(chunk) definiuje ile kolejnych iteracji ma dostać wątek
    - **guided -**  podobnie jak wyżej, ale liczba przydzielanych iteracji maleje z czasem, aż do osiągnięcia wartości z drugiego argumentu(chunk).

Uwagi:

- gdy zmieni się wartość zmiennej index to mogą być nieznane skutki
- wyrażenia logiczne wewnątrz pętli to mogą być jedynie <,≤,>,≥
- pętle nie mogą zawierać break

## 24. OpenMP: równoległość zadań, działanie, zarządzanie wykonaniem – klauzule

konstrukt taska: **#pragma omp task** 

konstrukt synchronizacji tasków: **#pragma omp taskwait -** działa jak barrier dla tasków

(jest jeszcze **#pragma omp taskyield -** oznaczenie miejsca w którym wykonanie zadania może być wstrzymane w celu uruchomienia innego zadania)

**Przykład**:

konstukt **single, np. #pragma omp single nowait** 

- single oznacza, że task się stworzy tylko dla jednego wątku, a nie *num_threads* razy.
- nowait oznacza, że zostanie zniesiona niejawna blokada na końcu bloku dyrektywy podziału pracy single
- zniesiona została blokada, ale taskwait znowu nakłada blokadę i upewnia się że wszystkie taski zostały wykonane

```c
#pragma omp single nowait //utworzenie bloku kodu wykonywanego przez jeden wątek
{                        //zniesniona blokada na końcu przeznowait
   #pragma omp task      
   foo();
   #pragma omp task
   bar();
}    //blak blokady bo był nowait
#pragma omp taskwait   //blokada taskwait
```

- **taski są ogólnie kolejkowane i wykonywane we wskazanych task scheduling points**
- **taskwait -** upewania się, że aktualny strumień wykonania sie zatrzyma dopóki wszystkie taski się wykonają
- task może zostać wykonany od razu albo właśnie zaplanowane wykonanie na później.

    ### **Dozwolone klauzule:**

    **klauzule zmiennych**: default, private, firstprivate,shared

    **if** - gdy warunek jest spełniony to zlecane do wykonania, gdy nie jest spełniony zadanie jest wykonywane od razu, 

    **untied** - jeśli zostanie wstrzymane wykonywanie, może być kontynuowany przez inny wątek

    **final** - jeśli warunek jest prawdziwy to kolejne zadania wykonywane są od razu

    **mergeable** - dla zadań zagnieżdżonych

## 25. Zależności danych – typy, wpływ na możliwość zrównoleglenia kodu, zależności przenoszone w pętli

### Zależności danych

- wzajemne uzależnienie instrukcji nakładające ograniczenia na kolejność na ich wykonywania
- instrukcje wykonywane w bezpośrednim sąsiedztwie czasowym operują na tych samych danych i chociaż jedna z instrukcji dokonuje zapisu

zależności wyjścia,  **write-after-write**

anty-zależności **write-after-read**

- nie jest to realny problem, można wyeliminować jakieś instrukcje lub przemianować zmienne

zależności rzeczywiste **read-after-write (true dependencies)** 

- uniemożliwiają zrównoleglenie algorytmu - konieczne jest jego przeformułowanie w celu uzyskania wersji możliwej do zrównoleglenia

**aliasing -**  pokrywanie się wskaźników o różnych nazwach, przez co na pierwszy rzut oka zależność nie jest widoczna

**zależności przenoszone w pętli**

- nie mamy pewności co do kolejności wykonania pętli

## 26. MPI – charakterystyka: model wykonania, podstawowe funkcje

### MPI = Message Passing Interface

- ustandaryzowany interfejs programowania (podejście do programowania równoległego przez przez wysyłanie wiadomości)
- **wciąż to tylko standard**, ma różne implementacje jak OpenMPI - opensource, albo MPICH dla superkomputerów
- zwykle tworzymy stałą liczbę procesów, tj. jeden proces na procesor(rdzeń)
- procesy te mogą wywoływać różne programy (MPI model jest MPMD - multiple program multiple data)(Konsument-producent problem), ale nie tylko, **możemy też działać jako SIMD - single instruction multiple data**
- często używane w sieciach rozproszonych (klieent-serwer) wysyłanie zamówienia od procesu klienta do procesu serwera, wykonywanie zamówienia przez serwer, przesyłanie odpowiedzi do klienta

### Podstawowe funkcje:

**MPI_X, gdzie X:**

**INIT** - inicjalizacja

**FINALIZE** - koniec obliczeń

**COMM_SIZE** - określa liczbę procesów

**COMM_RANK** - określa id procesu

**SEND** - wysyła wiadomość

**RECV** - odbiera wiadomość

**COMM_WORLD -** wszystkie procesy są zgrupowane w communicator - domyślny communicator

## 27. MPI: komunikacja punkt-punkt – adresowanie, dane, tryby komunikacji

- MPI gwarantuje zachowanie porządku przyjmowania(to samo źródło, ten sam id, ten sam communicator)

### Rodzaje procedur:

**Przesyłanie blokujące (synchroniczne) -** nadawca czeka po wysłaniu komunikatu do czasu, aż
odbiorca wykona operację odbioru =

Podstawowe operacje: **Send** i **Receive**

**Send** wysyła bufor danych jakiegoś typu do innego procesu, argumenty:

- wskaźnik do buforu - trzyma dane które chcemy wysłać do innego procesu
- typ danych (MPI_CHAR, MPI_INT, MPI_FLOAT, MPI_DOUBLE etc.)
- liczba elementów w buforze
- tag - typ komunikacji
- destination id - RANK procesu do którego chcemy wysłać dane
- komunikator

**Receive** działa podobnie ale:

- source id - RANK procesu od którego oczekujemy na wiadomość
- status - pozwala uzyskać więcej info o wiadomości którą odebrałęm

można użyć równiez:

MPI_ANY_SOURCE - gdy chcemy dostać wiadomość z dowolnego źródła:

MPI_ANY_TAG -dowolny typ komunikacji

**Przesyłanie nieblokujące  (asynchroniczne)-** używa się:

Komunikat jest umieszczany w kolejce komunikatów oczekujących na przyjęcie przez odbiorcę, a proces nadawczy może kontynuować działanie

- **MPI_Isend** → przygotowuje się request, request jest wywoływane gdy oba procesy są gotowe do synchronizacji / tylko przygotowuje wysyłkę ale nic nie wysyła. (tylko MPI_Request)
- **MPI_IRecv** - zwraca obiekt **MPI_Request**
- **Waitning** -
    - blokuje proces dopóki request się nie spełni
    - czeka na zakończenie requesta - gdy request się zakończy instacja MPI_Status jest zwracana w zmiennej status
        - **MPI_Wait** lub **MPI_Waitany** -
- **Testing -** sprawdza czy request może być zakończony, jeśli tak request jest automatycznie zakańczany a dane przenoszone
    - **MPI_Test** lub **MPI_Testany**

### Inne:

**MPI_Barrier**- blokuje wszystkie procesy w komunikatorze, by zapauzować dopóki każdy proces dotrze do tego punktu

### **Tryby przesyłania komunikatów**

**MPI_Send -"standard"  zależny od implementacji MPI → ona wybiera co jest dla ciebie najlepsze** (w OpenMPI bufered dla krótkich wiadomości a dla długich coś a la synchronous)

- specjalny - jawnie wskazany przez programistę
    - **MPI_Bsend** - buffered - trzyma wszystkie dane do wysłania w buforze i wraca do wykonania, czyli wraca od razu do wykonania nawet jak nie zostanie wywołane jeszcze Recv
    - **MPI_Rsend** - ready - startuje gdy odpowiednia odpowiedź została uzyskana
    - **MPI_Ssend** - synchronous- czeka na odpowiednie Recv by zakończyć, upewnia się, że oba procesy są gotowe do tranferu

**MPI_PACK** i **MPI_UNPACK** - można zapakować dane do buforu przed wysłaniem, a potem odpakować z buforu po dostaniu

## 28. MPI: komunikacja grupowa, schematy komunikacji, przykładowe funkcje interfejsu

**Broadcasting -** jeden proces ****broadcastuje wiadomosc do wsyzstkich procesow

**Scatter -** jeden proces rozdziela dane by wysłać po kawałku do każdego procesu

- MPI_Scatter jest bardzo podobne do MPI_Bcast. Podstawową różnicą jest to, że MPI_Bcast wysyła dokładnie te same dane do wszystkich procesów, podczas gdy MPIScatter może wysyłać różne fragmenty tablicy do różnych procesów. (one to many)

**Reduction** - jeden proces dostaje dane od wszystkich procesów i wykonuje na nich operacje(np.+)

**Gather(przeciwność Scatter) -** jeden proces zbiera dane od innych procesów
