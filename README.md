
# Kuziora Karol 95468 - Zadanie Lab 11

## Task A
### Deployment
Poniższe różnice wynikają z faktu, że StatefulSet jest przeznaczony dla aplikacji wymagających zachowania trwałości danych (np. bazy danych), w przeciwieństwie do Deployment który służy do bezstanowych aplikacji gdzie kolejność i tożsamość podów nie ma znaczenia.

1. Wykorzystywany do wdrażania aplikacji typu Stateless
2. Wszystkie pody tworzone są równocześnie
3. Pody usuwane są bez uwzględnienia kolejności
4. Nazwy generowane są w sposób randomowy
5. Wszystkie pody korzystają z tego samego PV (Persistance Volume)

### Statefulset
1. Wykorzystywany do wdrażania aplikacji typu Stateful.
2. Pody tworzone są w określonej kolejności - jeden po drugim
3. Pody usuwane są w odwrotnej kolejnosci w której zostały utworzone
4. Nazwy tworzone są w konkretny sposób np. mongo-0, mongo-1, mongo-2
5. Każdy z podów ma przydzielony swój PV

## Task B
Ten dokument jest kontynuacją mojego rozwiązania zadania 2. Wdrażałem w nim aplikację Roommate w środowisku Kubernetes z wykorzystaniem StatefulSet.
https://github.com/Montu9/PFSwChO_Zadanie_2

Podczas skalowania w góre lub w dół zostaną utworzone kolejne repliki głownego (Primary) zasobu.

W przypadku mongodb konieczne jest wykorzystanie dodatkowego pliku mongo-keyfile wykorzystywanym do autoryzacji podczas tworzenia replik.
```
spec:
            containers:
                - name: mongo
                  image: mongo:4.2.0
                  command:
                      - "mongod"
                      - "--bind_ip_all"
                      - "--replSet"
                      - "rs0"
                      - "--keyFile"
                      - "/etc/mongodb/mongo-keyfile"
                      - "--auth"
```

## Task C
Cała funkcjonalność Statefulset oraz repliki została zaprezentowana na screenshot'ach.

Zaprezentowano:
 - stan początkowy - w którym, zgodnie z deklaracją istnieje tylko jeden pod mongo. Posiada jeden przypisany volumen. Również można zauważyć że nazwa poda nie jest losowa. Serwis z opcją `clusterIP: None` pozwala na dostęp do podów jedynie wewnątrz klastra.
 - skalowanie w górę oraz w dół - skalowanie w góre tworzy kolejne pody, również zgodnie z konwencją nazw. Są one tworzone jeden po drugim. Skalowanie w góre lub dół wspiera synchornizacje z głownym podem.
 - dodawanie replik - synchornizacja w moim przypadku odbywała się ręcznie poprzez dodanie replik do konfiguracji mongo
 rs.add("mongo-1.mongo-headless.roommate.svc.cluster.local:27017")
rs.add("mongo-2.mongo-headless.roommate.svc.cluster.local:27017")
 - poprawnośc działania




