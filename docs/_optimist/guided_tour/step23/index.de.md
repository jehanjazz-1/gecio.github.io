---
title: "23: Der Object Storage (S3 kompatibel)"
lang: de
permalink: /optimist/guided_tour/step23/
nav_order: 1230
parent: Guided Tour
---

# Schritt 23: Der Object Storage (S3 kompatibel)

## Einführung

In den vorigen Schritten haben wir bereits einige interessante Bausteine kennengelernt.
Als nächstes beschäftigen wir uns mit dem [Object Storage](https://en.wikipedia.org/wiki/Object_storage), der uns interessante Möglichkeiten bietet, Dateien zu speichern.

## Der Start (Benutzerdaten)

Damit wir auf den Object Storage zugreifen können, benötigen wir zunächst Login Daten (Credentials).
Dafür benötigen wir den OpenStack Client (siehe [Schritt 4](/optimist/guided_tour/step04/)), damit wir über OpenstackAPI die entsprechenden Daten erstellen können.
Der Befehl in der Kommandozeile dafür lautet:

```bash
openstack ec2 credentials create
```

Wenn die Daten korrekt erstellt worden sind, sieht die Ausgabe in etwa so aus:

```bash
$ openstack ec2 credentials create
+------------+-----------------------------------------------------------------+
| Field      | Value                                                           |
+------------+-----------------------------------------------------------------+
| access     | aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa                                 |
| links      | {u'self': u'https://identity.optimist.innovo.cloud/v3/users/bbb |
|            | bbbbbbbbbbbbbbbbbbbbbbbbbbbbb/credentials/OS-                   |
|            | EC2/aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'}                           |
| project_id | cccccccccccccccccccccccccccccccc                                |
| secret     | dddddddddddddddddddddddddddddddd                                |
| trust_id   | None                                                            |
| user_id    | bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb                                |
+------------+-----------------------------------------------------------------+
```

Nachdem die Zugangsdaten (Credentials) vorliegen, brauchen wir eine Möglichkeit, auf den S3 kompatiblen ObjectStorage zuzugreifen.

## Zugriff auf den S3 kompatiblen ObjectStorage

Es gibt verschiedene Optionen, auf den ObjectStorage zuzugreifen. Wir empfehlen dafür [s3cmd](https://s3tools.org/s3cmd).
Dieses kleine Tool ist einfach zu bedienen und zu nutzen.

Da wir bereits in [Schritt 4](/optimist/guided_tour/step04/) *pip* als Paketmanager installiert haben und nutzen, können wir S3cmd auch mit *pip* installieren:

```bash
pip install s3cmd
```

Da jetzt S3cmd installiert ist, müssen die vorher erstellten Zugangsdaten (Credentials) eingetragen werden. Nur so lässt sich S3cmd korrekt nutzen.
Alle wichtigen Informationen finden wir in der `.s3cfg` Datei. Falls diese noch nicht existiert, erstellen wir diese vorher.

Folgende Daten tragen wir dann in `.s3cfg` ein:

```bash
access_key = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
check_ssl_certificate = True
check_ssl_hostname = True
host_base = s3.es1.fra.optimist.innovo.cloud
host_bucket = s3.es1.fra.optimist.innovo.cloud
secret_key = dddddddddddddddddddddddddddddddd
use_https = True
```

## Der Bucket

Da wir Zugriff auf den S3 kompatiblen Object Storage haben, ist es an der Zeit, damit auch zu arbeiten.
Alle verfügbaren Befehle von s3cmd können mit dem folgendem Befehl angezeigt werden:

```bash
s3cmd --help
```

Als Nächstes erstellen wir einen Bucket.
Buckets entsprechen dabei im weitesten Sinne Ordnern, die wir für eine Struktur benötigen.
Eine Datei kann also nur in einem existierenden Bucket gespeichert werden. Der Name vom Bucket selbst ist einzigartig (über den gesamten Optimisten).
Wenn also bereits ein Bucket mit dem Namen "Test" besteht, kann dieser nicht erneut angelegt werden.
Daher ist es aus unserer Sicht eine gute Option, eine UUID zu nutzen und diese dann in der entsprechenden Applikation aufzulösen.

Auch gibt es die Möglichkeit, bei Buckets und auch bei Dateien, zwischen *public* und *private* zu unterscheiden.
Alle Buckets, die erstellt und die Dateien, die hochgeladen werden, sind per default *private*, d.h. wenn keine weiteren Einstellungen vorgenommen werden, kann nur der Ersteller auf den Bucket und den Inhalt zugreifen.
Dies lässt sich zum Beispiel per *Access Control List (ACL)* ändern.
WICHTIG: Sollte man einen kompletten Bucket auf *public* stellen, können auch Informationen über Dateien in diesem Bucket, die auf *private* gesetzt sind, abgerufen werden.

Nun kennen wir die wichtigsten Details und es ist Zeit, einen Bucket mit einer UUID zu erstellen:

```bash
$ s3cmd mb s3://e4d05df3-aa8e-4a37-b1b5-2745d189b189
Bucket 's3://e4d05df3-aa8e-4a37-b1b5-2745d189b189/' created
```

## Eine Datei hochladen

Da wir jetzt einen Bucket erstellt haben können wir im nächsten Schritt, eine oder auch mehrere Dateien hoch laden.
Dafür nehmen wir den Befehl `s3cmd put Dateiname s3://Name_des_Buckets`.

Eine Ausgabe kann dann so aussehen:

```bash
$ s3cmd put test.yaml s3://e4d05df3-aa8e-4a37-b1b5-2745d189b189
upload: 'test.yaml' -> 's3://e4d05df3-aa8e-4a37-b1b5-2745d189b189/test.yaml'  [1 of 1]
 4218 of 4218   100% in    0s     4.61 kB/s  done
```

## Zugriff auf die Datei erhalten

Die generelle URL für den Zugriff auf Dateien lautet in Optimist *<https://s3.es1.fra.optimist.innovo.cloud/Name_des_Buckets/Dateiname>*.

Damit auf die Datei aus unserem Beispiel zugegriffen werden kann, ist es notwendig, die Einstellung von *private* auf *public* zu ändern.
Dafür können wir, wie bereits unter dem Punkt "Der Bucket" erwähnt, die *Access Control List (ACL)* nutzen:

```bash
$ s3cmd setacl s3://e4d05df3-aa8e-4a37-b1b5-2745d189b189/test.yaml --acl-public
s3://e4d05df3-aa8e-4a37-b1b5-2745d189b189/test.yaml: ACL set to Public  [1 of 1]
```

Die Datei kann jetzt über folgenden Link aufgerufen werden:
<https://s3.es1.fra.optimist.innovo.cloud/e4d05df3-aa8e-4a37-b1b5-2745d189b189/test.yaml>

Um sie wieder auf *private* zu stellen, nutzen wir folgenden Befehl:

```bash
$ s3cmd setacl s3://e4d05df3-aa8e-4a37-b1b5-2745d189b189/test.yaml --acl-private
s3://e4d05df3-aa8e-4a37-b1b5-2745d189b189/test.yaml: ACL set to Private  [1 of 1]
```

## Zusammenfassung

In diesem Schritt haben wir den S3 kompatiblen Storage kennengelernt und die ersten Schritte im Umgang damit geübt.
