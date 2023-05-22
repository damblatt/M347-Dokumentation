# M347 - Dokumentation

Diese Dokumentation soll als Lernhilfe dienen und wichtige Themen, einsatzbereit zur Repetition, zur Verfügung stellen.



## Container vs. Virtual Machine

Während in jeder virtuellen Maschine ein vollständiges Betriebssystem läuft, bringt ein Container nur die wirklich benötigten Komponenten ohne Betriebssystem mit. Die Container verwenden alle denselben Kernel, nämlich denjenigen des Host-Rechners. Dies spart eine Menge Ressourcen und es können daher sehr viel mehr Container auf einem Host laufen als klassische virtuelle Maschinen.

Container sind als Einheit **isoliert** und sind **portierbar**.



## Containerisierung

**... bezeichnet die Virtualisierung mit Hilfe von Containern.**

### Architektur

![Docker-Architektur](https://gbssg.gitlab.io/m347/img/kap1/docker-architecture.svg)

![1.1.2](https://gbssg.gitlab.io/m347/img/kap1/1-2.PNG)

### Begriffe

| Begriff    | Definition                                                   |
| ---------- | ------------------------------------------------------------ |
| Image      | Schreibgeschützte Vorlage mit dem Speicherabbild eines Containers. |
| Container  | Aktive Instanz eines Images.                                 |
| Layer      | Teil eines Images. Enthält einen Befehl oder eine Datei, die dem Image hinzugefügt wurde. |
| Repository | Satz gleichnamiger Images mit verschiedenen Tags, zumeist Versionen. |
| Registry   | Verwaltet Repositories. z.B. DockerHub                       |
| Dockerfile | Textdatei mit allen Befehlen, um ein Image zusammenzustellen. |

#### Docker-Daemon

- lauscht auf Docker-API-Anforderungen.
- verwaltet Docker-Objekte wie Images, Container, Netzwerke und Volumes. 
- kann mit anderen Daemons kommunizieren, um Docker-Dienste zu verwalten.

#### Docker-Client

- ist ein Befehlszeilenprogramm (CLI), das die REST-API des Docker-Daemons aufruft. 
- kann mit mehr als einem Daemon kommunizieren.

#### Docker-Registry

- speichert Docker-Images.
- sucht standardmässig nach Images auf Docker Hub. 
- eigene, private Registries möglich

Mit Befehl **docker pull** oder **docker run** werden die benötigten Images aus der konfigurierten Registry gezogen. Der Befehl **docker push** überträgt ein Image in die konfigurierte Registry.



## Commands

### Image herunterladen

````bash
docker pull ubuntu:latest
````

### Container starten

````bash
docker run -it --name my-ubuntu-container ubuntu:latest
````

````bash
docker start -i my-ubuntu-container
````

### Container stoppen

````bash
docker stop my-ubuntu-container
````

### Container löschen

````bash
docker rm my-ubuntu-container
````

### Image löschen

````bash
docker rmi ubuntu:latest
````



![1.4.1](https://gbssg.gitlab.io/m347/img/kap1/4-1.PNG)



## Image Namen

Docker-Image-Namen setzen sich aus drei Teilen zusammen:

```
source/imagename:tag
```

- **source** gibt den Namen der Organisatio (oder Person) an, die das Image erstellt hat, z.B. docker
- **imagename** der Name des Images, z.B. getting-started
- **tag** die Versionsnummer des Images, z.B. 22.04

Wird keine source angegeben, nimmt docker an, dass eines der offiziellen Dockerimages gemeint ist. Wird kein tag angegeben, wird automatisch das tag latest verwendet.



## Datenspeicherung

Standardmässig werden die Daten im Container selbst abgelegt. Das Problem dabei liegt darin, dass die Daten somit bei Neuerstellung verloren gehen (rm). Die Lösung dazu sind Volumes.



## Volumes

Werden Verzeichnisse im Dockerfile mit VOLUME gekennzeichnet. Das bewirkt, dass die Daten nun ausserhalb auf dem Hostrechner gespeichert werden.

Man unterscheidet dabei:

### Unbenannte Volumes (anonymous volumes)

Wird beim Erstellen des Containers nichts eingestellt, liegt das Verzeichnis in einem zufällig benannten Unterverzeichnis von /var/lib/docker/volumes. Durch die zufällige Bezeichnung wird eine praktikable Verwaltung verunmöglicht.

Eine Wiederverwendung nach einem Löschen des Containers ist ebenfalls nicht möglich, da bei jeder Neuerstellung eine neue zufällige Bezeichnung des Volumes erzeugt wird.

````bash
# download image and start container
docker run -d --name mariadb-test -e MYSQL_ROOT_PASSWORD=geheim mariadb

# locates data
docker inspect -f '{{.Mounts}}' mariadb-test
````

### Benannte Volumes (named volumes)

Im Gegensatz zu unbenannten Volumes wird der Name des Volumes nun selbst gewählt.

`````bash
-v volumename:containerverzeichnis
`````

````bash
docker run -d --name mariadb-test2 -v myvolume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=geheim mariadb
````

### Volumes in eigenen Verzeichnissen (mounted volumes)

Als Alternative zu den benannten Volumes kann anstelle eines Namens auch ein Verzeichnis angegeben werden.

````bash
-v hostverzeichnis:containerverzeichnis
````

````bash
docker run -d --name mariadb-test3 -v /home/vmadmin/database:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=geheim mariadb
````



## Docker Commands

| Command    | Beschreibung                                                 |
| ---------- | ------------------------------------------------------------ |
| ADD        | Kopiert Dateien vom **Host** oder einer **entfernten URL** in das Dateisystem des Images. |
| CMD        | Setzt das Kommando, das beim Start des Containers ausgeführt wird, sofern es **nicht übersteuert** wird. |
| COPY       | Kopiert Dateien vom **Host** in das Dateisystem des Images.  |
| ENTRYPOINT | Setzt das Kommando, das beim Start des Containers **immer** ausgeführt wird. |
| ENV        | Setzt eine Umgebungsvariable.                                |
| EXPOSE     | Definiert die zur Laufzeit des Containers aktiven Netzwerk-Ports. |
| FROM       | Setzt das Basis-Image für die nachfolgenden Anweisungen.     |
| LABEL      | Fügt dem Image Metadaten hinzu.                              |
| RUN        | Führt das angegebene Kommando einmalig während **docker build** aus und erzeugt dadurch einen neuen **Image-Layer**. |
| USER       | Gibt den Account für RUN, CMD und ENTRYPOINT an.             |
| VOLUME     | Definiert einen Mount Point auf ein Verzeichnis auf dem Host oder einem anderen Container. |
| WORKDIR    | Legt das Arbeitsverzeichnis für RUN, CMD, COPY etc. fest.    |

## Sonstiges

sudo service docker restart
