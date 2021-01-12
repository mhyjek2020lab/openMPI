# openMPI
## Wprowadzenie

To repozytorium powstało w ramach zajeć laboratoryjnych PWiR.
Z wykorzystaniem kodów mozna zbudowć kontener Dockerowy, zawierający środowisko
OpenMPI wraz z bibliotekami pomocniczymi. Kontener obsługuje rownież serwer OpenSSH
co umożliwia wykorzystanie wielu kontenerów połączonych ze sobą.

## Budowa kontenera
  Należy sklonować repozytorium https://github.com/mhyjek2020lab/openMPI.git
  Aby zbudować kontener posłużono się poleceniem docker build
  Instrukcje konfigacyjne dla obrazu znajdują się w pliku Dockerfile. 
  Na obrazie ubuntu został zainstalowany serwer openssh, python wraz z kilkoma bibliotekami oraz 
  openmpi z bibliotekami.
    
  Tak zbudowany obraz został wrzucony do repozytorium na dockerhubie 
  https://hub.docker.com/repository/docker/151969/openmpi
  
  

## Przygotowanie klastra HPC

Należy pobrać obraz dockera z repozytorium:
```
  sudo docker pull 151969/openmpi:last
```
W katalogu "openMPI" ze sklonowanego repozytorium znajduje sie plik "docker-compose.yml". Jest to plik, który definiuje mpi_head oraz mpi_node. Oba kontenery obsługują ten sam obraz dockerowy. 
mpi_head udostepnia swój serwer SSH systemowi hosta
mpi_node jest klientem

Komenda do utworzenia klastra n=5 klientow oraz jednego serwera.
```
  sudo docker-compose scale mpi_node=5 mpi_head=1
```
By wyswielić liczbę uruchomionych kontenerow, używamy:
```
  sudo docker ps -a
```
## Uruchomienie programu na klastrze HPC
Na chwilę obecną działa polecenie mpirun.

Programy MPI sa wykonywane w kontenerze mpi_head, który będzie uruchamiał procesy 
na poszczególnych klientach.
Nalezy uruchomić kontener mpi_head, za pomocą:
```
  sudo docker exec -it docker_mpi_head_1 /bin/bash
```

Nastepnie nalezy przejsc do katalogu /usr/bin
```
  cd /usr/bin
```
### Tworzenie i kompilacja przykładowego programu

Utworzono plik o nazwie mpi.c

  ```
  touch mpi.c
  vi mpi.c
  ```
  
  ```
  #include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    // Initialize the MPI environment
    MPI_Init(NULL, NULL);

    // Get the number of processes
    int world_size;
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);

    // Get the rank of the process
    int world_rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);

    // Get the name of the processor
    char processor_name[MPI_MAX_PROCESSOR_NAME];
    int name_len;
    MPI_Get_processor_name(processor_name, &name_len);

    // Print off a hello world message
    printf("Hello world from processor %s, rank %d out of %d processors\n",
           processor_name, world_rank, world_size);

    // Finalize the MPI environment.
    MPI_Finalize();
}
```

Aby kompilować pliki MPI, używamy polecenia mpicc
```
  mpicc -o mpi mpi.c	
```
W tym kontenerze program zostaż już skompilowany.

Aby uruchomic program MPI nalezy wpisac:
``
  mpirun --allow-run-as-root -n 5 mpi
``
Tym poleceniem zostało wykorzystanych 5 klientów do uruchomienia programu. 


Zamiast przykładowego programu mpi.c mozna uruchomić dowolny program OpenMPI.
