---
title: "Microsoft Power BI Embedded – Herstellen einer Verbindung mit einer Datenquelle"
description: Power BI Embedded, Verbinden mit Datenquellen
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 5c4b5e09ac5e336504be32533ad011aad102af75
ms.openlocfilehash: fdc0d76741d5078978e1bfeadd156e84cac3a48a


---
# <a name="connect-to-a-data-source"></a>Herstellen einer Verbindung mit einer Datenquelle
Mit **Power BI Embedded**können Sie Berichte in Ihre eigene Anwendung einbetten. Wenn Sie einen Power BI-Bericht in Ihre App einbetten, stellt der Bericht eine Verbindung mit den zugrunde liegenden Daten her, indem er eine Kopie der Daten **importiert** oder über **DirectQuery** eine **direkte Verbindung** mit der Datenquelle herstellt.

Im Folgenden werden die Unterschiede zwischen der Verwendung des **Importvorgangs** und der Verwendung von **DirectQuery** aufgeführt.

| Importieren | DirectQuery |
| --- | --- |
| Tabellen, Spalten *und Daten* werden in das Dataset des Berichts importiert oder kopiert. Um Änderungen anzuzeigen, die an den zugrunde liegenden Daten vorgenommen wurden, müssen Sie ein Dataset aktualisieren bzw. ein vollständiges aktuelles Dataset importieren. |Nur *Tabellen und Spalten* werden in das Dataset des Berichts importiert oder kopiert. Sie sehen immer die aktuellen Daten. |

Mit Power BI Embedded können Sie DirectQuery mit Clouddatenquellen verwenden, jedoch derzeit nicht mit lokalen Datenquellen.

> [!NOTE]
> Das lokale Datengateway wird derzeit mit Power BI Embedded nicht unterstützt. Das bedeutet, dass Sie DirectQuery nicht mit lokalen Datenquellen verwenden können.

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

**DirectQuery**
* Azure SQL-Datenbank
* Azure SQL Data Warehouse

**Import**

Sie können den Import unter Verwendung aller in Power BI Desktop verfügbaren Datenquellen durchführen. Es ist jedoch **nicht** möglich, diese Daten in Power BI Embedded zu aktualisieren. Stattdessen müssen Sie Änderungen an der PBIX-Datei in Power BI Embedded hochladen. Dies liegt daran, dass kein Gateway verfügbar ist. 

## <a name="benefits-of-using-directquery"></a>Vorteile der Verwendung von DirectQuery
Die Verwendung von **DirectQuery**bietet zwei wesentliche Vorteile:

* **direkte Verbindung** können Sie Visualisierungen über sehr umfangreiche Datasets in Fällen erstellen, in denen es ansonsten unmöglich wäre, sämtliche Daten zunächst zu importieren.
* Bei Änderungen an den zugrunde liegenden Daten kann eine Datenaktualisierung erforderlich sein, und für einige Berichte, die stets aktuelle Daten darstellen müssen, sind eventuell umfangreiche Datenübertragungen erforderlich, sodass das erneute Importieren von Daten nicht durchführbar ist. Im Gegensatz dazu werden in **DirectQuery** -Berichten immer aktuelle Daten verwendet.

## <a name="limitations-of-directquery"></a>Einschränkungen von DirectQuery
   Es gibt ein paar Einschränkungen bei der Verwendung von **DirectQuery**:

* Alle Tabellen müssen aus einer einzigen Datenbank stammen.
* Wenn die Abfrage sehr komplex ist, tritt ein Fehler auf. Um den Fehler zu beheben, müssen Sie die Abfrage so umgestalten, dass sie weniger komplex ist. Wenn die Abfrage komplex sein muss, müssen Sie die Daten importieren, statt **DirectQuery** einzusetzen.
* Das Filtern von Beziehungen ist auf eine Richtung beschränkt, es können nicht beide Richtungen verwendet werden.
* Sie können den Datentyp einer Spalte nicht ändern.
* Standardmäßig gelten Einschränkungen für DAX-Ausdrücke, die in Measures zulässig sind. Informationen hierzu finden Sie unter [DirectQuery und Measures](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery und Measures
Um sicherzustellen, dass die an die zugrunde liegende Datenquelle gesendeten Abfragen eine akzeptable Leistung zeigen, werden den Measures Einschränkungen auferlegt. Bei Verwendung von **Power BI Desktop** können fortgeschrittene Benutzer diese Einschränkung umgehen, indem sie **Datei > Optionen und Einstellungen > Optionen** auswählen. Wählen Sie im Dialogfeld **Optionen** die Option **DirectQuery**, und wählen Sie dann die Option **Unbeschränkte Measures im DirectQuery-Modus zulassen**. Wenn diese Option ausgewählt ist, kann jeder DAX-Ausdruck verwendet werden, der für ein Measure gültig ist. Benutzer müssen sich jedoch bewusst sein, dass einige Ausdrücke, die beim Importieren der Daten eine sehr gute Leistung zeigen, im **DirectQuery** -Modus zu sehr langsamen Abfragen an die Back-End-Datenquelle führen können. 

## <a name="see-also"></a>Weitere Informationen
* [Erste Schritte mit Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Weitere Fragen? [Power BI-Community ausprobieren](http://community.powerbi.com/)




<!--HONumber=Dec16_HO3-->


