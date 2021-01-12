# openMPI
1.0 wstep

To repozytorium powstalo w ramach zajec laboratoryjnych PWiR.
Z wykorzystaniem kodow mozna zbudowac kontener Dockerowy, zawierajacy srodowisko
OpenMPI wraz z bibliotekami pomocniczymi. Kontener obsluguje rowniez serwer OpenSSH
co umozliwia wykorzystanie wielu kontenerow polaczonych ze soba.

1.1 budowa kontenera

1.5 dzialanie kstastra HPC

nalezy pobrac obraz dockera z repozytorium:

sudo docker pull 151969/openmpi:last

w katalogu "openMPI" ze sklonowanego repozytorium GIT https://github.com/mhyjek2020lab/openMPI.git
znajduje sie plik "docker-compose.yml". Jest to plik, ktory definiuje mpi_head 
oraz mpi_node. Oba kontenery obsluguja ten sam obraz dockerowy. 
mpi_head udostepnia swoj serwer SSH systemowi hosta
mpi_node jest klientem

Komenda do utworzenia klastra n=5 klientow oraz jednego serwera.

sudo docker-compose scale mpi_node=5 mpi_head=1

By wyswielic liczbe uruchomionych kontenerow, uzywamy:

  sudo docker ps -a
  
Programy MPI sa wykonywane w kontenerze mpi_head, ktory bedzie uruchamial procesy 
na poszczegolnych klientach.
Nalezy uruchomic kontener mpi_head, za pomoca::

  sudo docker exec -it docker_mpi_head_1 /bin/bash

Nastepnie nalezy przejsc do katalogu /usr/bin
  cd /usr/bin

Aby kompilowac pliki MPI, uzywamy polecenia mpicc
W tym kontenerze program zostal juz skompilowany

Aby uruchomic program MPI nalezy wpisac:
  mpirun --allow-run-as-root -n 5 mpi

Tym poleceniem zostaly wykorzystane 5 klientow do uruchomienia programu. 

Zamiast przykladowego programu mpi.c mozna uruchomic dowolny program OpenMPI
