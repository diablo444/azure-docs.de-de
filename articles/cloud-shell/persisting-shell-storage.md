---
title: Beibehalten von Dateien Azure Cloud Shell (Vorschau) | Microsoft-Dokumentation
description: "Exemplarische Vorgehensweise für das Beibehalten von Dateien in Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 540cd10066e055e2dc132445b9adba5a4112d63a
ms.contentlocale: de-de
ms.lasthandoff: 05/16/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Beibehalten von Dateien in Azure Cloud Shell
Beim ersten Start fragt Azure Cloud Shell nach Ihrem Abonnement, um ein LRS-Speicherkonto und eine Azure-Dateifreigabe für Sie zu erstellen.

![](media/storage-prompt.png)

### <a name="three-resources-will-be-created-on-your-behalf-in-a-supported-region-nearest-to-you"></a>In einer unterstützten Region in Ihrer Nähe werden in Ihrem Auftrag drei Ressourcen erstellt:
1. Eine Ressourcengruppe namens `cloud-shell-storage-<region>`
2. Ein Speicherkonto namens `cs-uniqueGuid`
3. Eine Dateifreigabe namens `cs-<user>-<domain>-com-uniqueGuid`

Die Dateifreigabe wird als `clouddrive` in Ihrem $Home-Verzeichnis eingebunden. Die Dateifreigabe wird auch zum Speichern eines 5 GB großen Images verwendet, das für Sie erstellt wird und Ihr $Home-Verzeichnis automatisch aktualisiert und beibehält. Dies ist ein einmaliger Vorgang, die Einbindung erfolgt für nachfolgende Sitzungen automatisch.

### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell verwendet die beiden unten stehenden Methoden zum Beibehalten von Dateien:
1. Es wird ein Datenträgerimage Ihres $Home-Verzeichnisses erstellt, um Dateien in $Home beizubehalten. Dieses Datenträgerimage wird in der von Ihnen angegebenen Dateifreigabe als `acc_<User>.img` unter `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` gespeichert.

2. Die angegebene Dateifreigabe wird zur direkten Interaktion mit der Freigabe als `clouddrive` in Ihr $Home-Verzeichnis eingebunden. 
`/Home/<User>/clouddrive` wird `fileshare.storage.windows.net/fileshare` zugeordnet.
 
> [!Note]
> Alle Dateien in Ihrem $Home-Verzeichnis, wie etwa SSH-Schlüssel, werden beständig in Ihrem Datenträgerimage gespeichert, das in der eingebundenen Dateifreigabe gespeichert ist. Wenden Sie beim beständigen Speichern von Informationen in Ihrem $Home-Verzeichnis und der eingebundenen Dateifreigabe bewährte Methoden an.

## <a name="using-clouddrive"></a>Verwenden des clouddrive-Befehls
Benutzer können in Cloud Shell einen Befehl namens `clouddrive` ausführen, der die manuelle Aktualisierung der in Cloud Shell eingebundenen Dateifreigabe ermöglicht.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Einbinden eines neuen clouddrive-Verzeichnisses

### <a name="pre-requisites-for-manual-mounting"></a>Voraussetzungen für die manuelle Einbindung
Cloud Shell erstellt beim ersten Start ein Speicherkonto und eine Dateifreigabe für Sie. Sie können die Dateifreigabe jedoch mit dem Befehl `clouddrive mount` aktualisieren.

Wenn Sie eine vorhandene Dateifreigabe einbinden, müssen Speicherkonten folgende Voraussetzungen erfüllen:
1. Der lokal oder global redundante Speicher muss Dateifreigaben unterstützen.
2. Das Speicherkonto muss sich in Ihrer zugewiesenen Region befinden. Beim Onboarding wird die Region, der Sie zugewiesen sind, in der Ressourcengruppe namens `cloud-shell-storage-<region>` aufgelistet.

### <a name="supported-storage-regions"></a>Unterstützte Speicherregionen
Azure Files muss sich in der gleichen Region befinden wie der Computer, in den der Dienst eingebunden werden soll. Cloud Shell-Computer sind in folgenden Regionen vorhanden:
|Bereich|Region|
|---|---|
|Amerika|USA, Osten; USA, Süden-Mitte; USA, Westen|
|Europa|„Europa, Norden“, „Europa, Westen“|
|Asien-Pazifik|Indien, Mitte; Asien, Südosten|

### <a name="mount-command"></a>Befehl zum Einbinden

> [!NOTE]
> Wenn Sie eine neue Dateifreigabe einbinden, wird ein neues Benutzerimage für Ihr $Home-Verzeichnis erstellt, da das vorherige Image von $Home in der vorherigen Dateifreigabe gespeichert ist.

1. Führen Sie `clouddrive mount` mit den folgenden Parameter aus. <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Um weitere Informationen zu erhalten, führen Sie `clouddrive mount -h` aus: <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Aufheben der Einbindung von clouddrive
Sie können die Einbindung einer Dateifreigabe in Cloud Shell jederzeit aufheben. Cloud Shell erfordert jedoch eine eingebundene Dateifreigabe, daher werden Sie aufgefordert, in der nächsten Sitzung eine neue Dateifreigabe zu erstellen und einzubinden, wenn Sie die aktuelle Freigabe entfernen.

So trennen Sie eine Dateifreigabe von Cloud Shell:
1. Führen Sie `clouddrive unmount` aus.
2. Lesen und bestätigen Sie die Eingabeaufforderungen.

Ihre Dateifreigabe ist weiterhin vorhanden, es sei denn, sie wird manuell gelöscht. Cloud Shell sucht in nachfolgenden Sitzungen nicht mehr nach dieser Dateifreigabe.

Um weitere Informationen zu erhalten, führen Sie `clouddrive mount -h` aus: <br>
![](media/unmount-h.png)

> [!WARNING]
> Dieser Befehl löscht keine Ressourcen. Wenn Sie jedoch die Ressourcengruppe, das Speicherkonto oder die Dateifreigabe löschen, die bzw. das Cloud Shell zugeordnet ist, werden sowohl das Datenträgerimage Ihres $Home-Verzeichnisses als auch alle Dateien in Ihrer Dateifreigabe gelöscht. Dieser Vorgang kann nicht rückgängig gemacht werden.

## <a name="list-clouddrive"></a>Auflisten des clouddrive-Verzeichnisses
So ermitteln Sie, welche Dateifreigabe als `clouddrive` eingebunden ist:
1. Führen Sie `df` aus. 

Der Dateipfad zu „clouddrive“ zeigt den Namen Ihres Speicherkontos und die Dateifreigabe in der URL.

`//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>Übertragen Sie lokale Dateien auf Cloud Shell
Das `clouddrive`-Verzeichnis wird mit dem Blatt „Storage“ im Azure-Portal synchronisiert. Verwenden Sie dieses, um lokale Dateien in Ihre oder aus Ihrer Dateifreigabe zu übertragen. Die Aktualisierung der Dateien in Cloud Shell wird auf der Benutzeroberfläche der Dateifreigabe reflektiert, wenn das Blatt aktualisiert wird.

### <a name="download-files"></a>Herunterladen von Dateien
![](media/download.gif)
1. Navigieren Sie zur eingebundenen Dateifreigabe
2. Wählen Sie im Portal die Zieldatei aus.
3. Klicken Sie auf „Herunterladen“

### <a name="upload-files"></a>Hochladen von Dateien
![](media/upload.gif)
1. Navigieren Sie zur eingebundenen Dateifreigabe
2. Wählen Sie „Hochladen“ aus
3. Wählen Sie die Datei aus, die Sie hochladen möchten
4. Bestätigen Sie den Upload

Sie sollten die Datei jetzt zugreifbar in Ihrem clouddrive-Verzeichnis in Cloud Shell sehen.

## <a name="next-steps"></a>Nächste Schritte
[Cloud Shell – Schnellstart](quickstart.md) 
[Informationen zu Azure File Storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 
[Informationen zu Speichertags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 
