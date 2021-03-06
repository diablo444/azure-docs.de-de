---
title: "SQL-Abfragemetriken für die Azure Cosmos DB-DocumentDB-API | Microsoft-Dokumentation"
description: "Erfahren Sie, wie die SQL-Abfrageleistung von Azure Cosmos DB-Anforderungen instrumentiert und ein Debugging durchgeführt wird."
keywords: SQL-Syntax, SQL-Abfrage, SQL-Abfragen, JSON-Abfragesprache, Datenbankkonzepte und SQL-Abfragen, Aggregatfunktionen
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: b2fa8e8f-7291-45a3-9bd1-7284ed9077f8
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: d8b0bde3778054042c32dbc9c9e08d0b2f1fd3ca
ms.contentlocale: de-de
ms.lasthandoff: 07/27/2017

---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Optimieren der Abfrageleistung mit Azure Cosmos DB
Azure Cosmos DB bietet eine [SQL-API zum Abfragen von Daten](documentdb-sql-query.md), ohne dass Schemas oder sekundäre Indizes erforderlich sind. Dieser Artikel enthält folgende Informationen für Entwickler:

* Allgemeine Informationen zur Funktionsweise der SQL-Abfrageausführung in Azure Cosmos DB
* Informationen zu Abfrageanforderungen, Antwortheadern und Client-SDK-Optionen
* Tipps und bewährte Methoden für die Abfrageleistung
* Beispiele für die Verwendung von SQL-Ausführungsstatistiken zum Debuggen der Abfrageleistung

## <a name="about-sql-query-execution"></a>Informationen über die Ausführung von SQL-Abfragen

In Azure Cosmos DB speichern Sie Daten in Containern, die auf eine beliebige [Speichergröße oder einen beliebigen Anforderungsdurchsatz](partition-data.md) erweitert werden können. Azure Cosmos DB skaliert nahtlos Daten im Hintergrund über physische Partitionen hinweg, um das Datenwachstum im bereitgestellten Durchsatz zu verarbeiten. Sie können mit der REST-API oder einer der unterstützten [DocumentDB-SDKs](documentdb-sdk-dotnet.md) SQL-Abfragen an einen beliebigen Container ausgeben.

Ein kurzer Überblick über die Partitionierung: Sie definieren einen Partitionsschlüssel wie „city“, der bestimmt, wie Daten auf physischen Partitionen aufgeteilt werden. Daten, die zu einem einzelnen Partitionsschlüssel gehören (z.B. „city“ == „Seattle“), werden auf einer physischen Partition gespeichert, wobei eine einzelne physische Partition in der Regel jedoch mehrere Partitionsschlüssel aufweist. Wenn eine Partition die Speichergröße erreicht, teilt der Dienst die Partition nahtlos in zwei neue Partitionen auf, und teilt den Partitionsschlüssel gleichmäßig über diese Partitionen hinweg auf. Da Partitionen vorübergehend sind, verwenden die APIs eine Abstraktion eines „Partitionsschlüsselbereichs“, der die Bereiche von Partitionsschlüsselhashes bezeichnet. 

Wenn Sie eine Abfrage an Azure Cosmos DB ausgeben, führt das SDK folgende logische Schritte aus:

* Analysieren Sie die SQL-Abfrage, um den Abfrageausführungsplan zu bestimmen. 
* Wenn die Abfrage einen Filter für den Partitionsschlüssel wie `SELECT * FROM c WHERE c.city = "Seattle"` enthält, wird sie an eine einzelne Partition weitergeleitet. Wenn die Abfrage keinen Filter für einen Partitionsschlüssel enthält, wird sie in allen Partitionen ausgeführt, und die Ergebnisse werden auf Clientseite zusammengeführt.
* Die Abfrage wird basierend auf der Clientkonfiguration seriell oder parallel in jeder Partition ausgeführt. In jeder Partition führt die Abfrage möglicherweise eine oder mehrere Roundtrips durch, je nach Komplexität der Abfrage, der konfigurierten Seitengröße und dem bereitgestelltem Durchsatz der Sammlung. Bei jeder Ausführung wird die Anzahl der [Anforderungseinheiten](request-units.md) (Request Units, RUs) zurückgegeben, die bei der Ausführung der Abfrage genutzt werden, und optional die Abfrageausführungsstatistik. 
* Das SDK führt eine Zusammenfassung der Abfrageergebnisse über die Partitionen hinweg durch. Wenn die Abfrage beispielsweise eine ORDER BY-Klausel über die Partitionen hinweg enthält, werden die Ergebnisse der einzelnen Partitionen sortiert zusammengeführt, um Ergebnisse in global sortierter Reihenfolge zurückzugeben. Wenn die Abfrage eine Aggregation wie `COUNT` enthält, wird die Anzahl der einzelnen Partitionen summiert, um die Gesamtzahl zu bestimmen.

Die SDKs stellen verschiedene Optionen für die Abfrageausführung bereit. In .NET sind diese Optionen beispielsweise in der `FeedOptions`-Klasse verfügbar. In der folgenden Tabelle werden diese Optionen beschrieben und erläutert, wie sie sich auf die Abfrageausführungszeit auswirken. 

| Option | Beschreibung |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Muss auf „true“ für eine Abfrage festgelegt werden, die über mehrere Partitionen hinweg ausgeführt werden muss. Dies ist eine explizite Kennzeichnung, um Ihnen bewusste Leistungstradeoffs während der Entwicklungszeit zu ermöglichen. |
| `EnableScanInQuery` | Muss auf „true“ festgelegt werden, wenn Sie die Indizierung deaktiviert haben, die Abfrage jedoch trotzdem mittels einer Überprüfung ausführen möchten. Diese Option ist nur anwendbar, wenn die Indizierung für den angeforderten Filterpfad deaktiviert ist. | 
| `MaxItemCount` | Die maximale Anzahl von Elementen, die pro Roundtrip an den Server zurückgegeben werden soll. Mit einem Wert von „-1“ können Sie festlegen, dass die Anzahl der Elemente vom Server verwaltet wird. Alternativ können Sie diesen Wert senken, um nur eine geringe Anzahl von Elementen pro Roundtrip abzurufen. 
| `MaxBufferedItemCount` | Dies ist eine clientseitige Option, mit der die Speicherbelegung bei der Durchführung der partitionsübergreifenden ORDER BY-Klausel beschränkt wird. Durch einen höheren Wert kann die Latenz der partitionsübergreifenden Sortierung verringert werden. |
| `MaxDegreeOfParallelism` | Ruft die Anzahl der gleichzeitigen Vorgänge ab bzw. legt diese fest, die auf Clientseite während der parallelen Abfrageausführung im Azure DocumentDB-Datenbankdienst ausgeführt wird. Ein positiver Eigenschaftswert beschränkt die Anzahl der gleichzeitigen Vorgänge auf den festgelegten Wert. Wenn ein kleinerer Wert als 0 festgelegt wird, wird die Anzahl der gleichzeitig auszuführenden Vorgänge automatisch vom System festgelegt. |
| `PopulateQueryMetrics` | Ermöglicht die ausführliche Protokollierung von Statistiken zu der Zeit, die in verschiedenen Phasen der Abfrageausführung wie der Kompilierzeit, Indexschleifenzeit sowie Dokumentladezeit aufgewendet wird. Sie können die Ausgabe der Abfragestatistik an den Azure-Support weitergeben, um Probleme zur Abfrageleistung zu diagnostizieren. |
| `RequestContinuation` | Sie können die Abfrageausführung fortsetzen, indem Sie den von jeder Abfrage zurückgegebenen opaken Fortsetzungstoken übergeben. Das Fortsetzungstoken kapselt alle Zustände, die für die Abfrageausführung erforderlich sind. |
| `ResponseContinuationTokenLimitInKb` | Sie können die maximale Größe des vom Server zurückgegebenen Fortsetzungstokens beschränken. Möglicherweise müssen Sie dies festlegen, wenn Ihr Anwendungshost Grenzwerte für die Größe des Antwortheaders vorsieht. Durch Festlegen dieses Werts kann sich die Gesamtdauer und die Anzahl der für die Abfrage verwendeten RUs erhöhen.  |

Nehmen wir als Beispiel eine Beispielabfrage für einen Partitionsschlüssel, der für eine Sammlung mit `/city` als Partitionsschlüssel angefordert und mit einem Durchsatz von 100.000 RU/s bereitgestellt wird. In .NET fordern Sie diese Abfrage mit `CreateDocumentQuery<T>` folgendermaßen an:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Der oben gezeigte SDK-Ausschnitt entspricht der folgenden REST-API-Anforderung:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Jede Abfrageausführungsseite entspricht einer REST-API `POST` mit dem Header `Accept: application/query+json` und der SQL-Abfrage im Text. Jede Abfrage führt eine oder mehrere Roundtrips zum Server durch, wobei der Token `x-ms-continuation` zwischen dem Client und dem Server wiederholt wird, um die Ausführung fortzusetzen. Die Konfigurationsoptionen unter „FeedOptions“ werden in Form von Anforderungsheadern an den Server übergeben. `MaxItemCount` entspricht beispielsweise `x-ms-max-item-count`. 

Die Anforderung gibt die folgende (zur besseren Lesbarkeit gekürzte) Antwort zurück:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Die von der Abfrage zurückgegebenen Schlüsselantwortheader umfassen Folgendes:

| Option | Beschreibung |
| ------ | ----------- |
| `x-ms-item-count` | Die Anzahl der in der Antwort zurückgegebenen Elemente. Dies ist abhängig vom angegebenen `x-ms-max-item-count`-Header, der Anzahl der Elemente, die in der maximalen Größe der Antwortnutzlast angepasst werden können, dem bereitgestellten Durchsatz und der Abfrageausführungszeit. |  
| `x-ms-continuation:` | Das Fortsetzungstoken zum Fortsetzen der Abfrageausführung, sofern zusätzliche Ergebnisse verfügbar sind. | 
| `x-ms-documentdb-query-metrics` | Die Abfragestatistik für die Ausführung. Dies ist eine durch Trennzeichen getrennte Zeichenfolge, die Statistiken zum Zeitaufwand in den verschiedenen Phasen der Abfrageausführung enthält. Dieser Wert wird zurückgegeben, wenn `x-ms-documentdb-populatequerymetrics` auf `True` festgelegt ist. | 
| `x-ms-request-charge` | Die Anzahl der von der Abfrage genutzten [Anforderungseinheiten](request-units.md). | 

Weitere Informationen über die REST-API-Anforderungsheader und -Optionen finden Sie unter [Abfragen von Ressourcen mithilfe der DocumentDB-REST-API](https://docs.microsoft.com/rest/api/documentdb/querying-documentdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Bewährte Methoden für die Abfrageleistung
Im Folgenden werden die gängigsten Faktoren vorgestellt, die sich auf die Abfrageleistung von Azure Cosmos DB auswirken. Die einzelnen Themen in diesem Artikel werden im Detail erläutert.

| Faktor | Tipp | 
| ------ | -----| 
| Bereitgestellter Durchsatz | Messen Sie die RUs pro Abfrage, und stellen Sie sicher, dass Sie über den erforderlichen bereitgestellten Durchsatz für Ihre Abfragen verfügen. | 
| Partitionierung und Partitionsschlüssel | Geben Sie Abfragen mit dem Partitionsschlüsselwert in der Filterklausel aufgrund geringer Latenz den Vorzug. |
| SDK und Abfrageoptionen | Befolgen Sie bewährte Methoden für SDK wie direkte Konnektivität, und optimieren Sie Optionen für die clientseitige Abfrageausführung. |
| Netzwerklatenz | Berücksichtigen Sie das Netzwerkoverhead bei der Messung, und verwenden Sie Multi-Homing-APIs, um den Lesevorgang über die am nächsten gelegene Region durchzuführen. |
| Indizierungsrichtlinien | Stellen Sie sicher, dass Sie über die erforderlichen Indizierungspfade bzw. die erforderliche Indizierungsrichtlinie für die Abfrage verfügen. |
| Abfrageausführungsmetriken | Analysieren Sie die Abfrageausführungsmetriken, um potenzielle Änderungen an Abfrage- und Datenformen zu identifizieren.  |

### <a name="provisioned-throughput"></a>Bereitgestellter Durchsatz
In Cosmos DB erstellen Sie Container für Daten, die jeweils mit einem reservierten Durchsatz, ausgedrückt in Anforderungseinheiten (RU) pro Sekunde und pro Minute, ausgestattet sind. Eine Lesevorgang von einem 1-KB-Dokument entspricht 1 RU, und jeder Vorgang (einschließlich Abfragen) wird basierend auf der jeweiligen Komplexität zu einer festen Anzahl von RUs normalisiert. Wenn Sie beispielsweise 1000 RU/s für Ihren Container bereitgestellt und eine Abfrage wie `SELECT * FROM c WHERE c.city = 'Seattle'` haben, für die 5 RUs erforderlich sind, können Sie solche Abfragen pro Sekunde gemäß folgender Formel ausführen: (1000 RU/s) / (5 RU/Abfrage) = 200 Abfragen/s. 

Wenn Sie mehr als 200 Abfragen/s übermitteln, beginnt der Dienst, die Rate eingehender Anforderungen bei über 200 Abfragen/s zu begrenzen. Die SDKs verarbeiten diesen Fall automatisch, indem Sie eine Wartezeit/Wiederholung ausführen. Daher werden Sie bei diesen Abfragen möglicherweise eine höhere Latenz feststellen. Durch Erhöhen des bereitgestellten Durchsatzes auf den erforderlichen Wert werden Latenz und Durchsatz Ihrer Abfragen verbessert. 

Weitere Informationen zu Anforderungseinheiten finden Sie unter [Anforderungseinheiten](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partitionierung und Partitionsschlüssel
Bei Azure Cosmos DB werden Abfragen in der Regel in der Reihenfolge von den schnellsten bzw. effizientesten Abfragen zu langsameren bzw. weniger effizienten Abfragen durchgeführt. 

* GET-Anfrage mit einem einzelnen Partitions- und Elementschlüssel
* Abfrage mit einer Filterklausel für einen einzelnen Partitionsschlüssel
* Abfrage ohne eine Gleichheits- oder Bereichsfilterklausel in einer Eigenschaft
* Abfrage ohne Filter

Abfragen, bei denen alle Partitionen konsultiert werden müssen, erfordern höhere Latenzen und verbrauchen mehr RUs. Da jede Partition über eine automatische Indizierung für alle Eigenschaften verfügt, kann die Abfrage in diesem Fall effizient über den Index verarbeitet werden. Durch Optionen für Parallelität können Sie Abfragen erstellen, die Partitionen schneller umfassen.

Weitere Informationen zur Partitionierung und zu Partitionsschlüsseln finden Sie unter [Partitionieren in Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK und Abfrageoptionen
Unter [Leistungstipps](performance-tips.md) und [Leistungstests](performance-testing.md) finden Sie Informationen darüber, wie die ideale clientseitige Leistung von Azure Cosmos DB erzielt wird. Dies umfasst die Verwendung aktueller SDKs, die Konfiguration plattformspezifischer Konfigurationen wie die Standardanzahl von Verbindungen, die Häufigkeit von Garbage Collections und die Verwendung einfacher Konnektivitätsoptionen wie Direkt/TCP. 

Optimieren Sie bei Abfragen `MaxBufferedItemCount` und `MaxDegreeOfParallelism`, um die besten Konfigurationen für Ihre Anwendung zu identifizieren, insbesondere, wenn Sie partitionsübergreifende Abfragen (ohne Filter für den Partitionsschlüsselwert) ausführen.

SDK-Releasehinweise und Informationen zu implementierten Klassen und Methoden finden Sie unter [DocumentDB-SDKs](documentdb-sdk-dotnet.md).

### <a name="network-latency"></a>Netzwerklatenz
Informationen zum Einrichten der globalen Verteilung und Herstellen einer Verbindung mit der nächstgelegenen Region finden Sie unter [Globale Azure Cosmos DB-Verteilung](tutorial-global-distribution-documentdb.md). Die Netzwerklatenz hat einen entscheidenden Einfluss auf die Abfrageleistung, wenn mehrere Roundtrips durchzuführen sind oder ein großes Resultset über die Abfrage abgerufen werden muss. 

### <a name="indexing-policy"></a>Indizierungsrichtlinie
Informationen zu Indizierungspfade, -arten und -modi sowie ihren Auswirkungen auf die Abfrageausführung finden Sie unter [Konfigurieren der Indizierungsrichtlinie](indexing-policies.md). Standardmäßig verwendet die Indizierungsrichtlinie Hashindizierung für Zeichenfolgen, was für Gleichheitsabfragen, jedoch nicht für Bereichsabfragen/Order by-Abfragen effizient ist. Wenn Sie Bereichsabfragen für Zeichenfolgen benötigen, wird empfohlen, den Bereichsindextyp für alle Zeichenfolgen anzugeben. 

## <a name="query-execution-metrics"></a>Abfrageausführungsmetriken
Sie können ausführliche Metriken zur Abfrageausführung erhalten, indem Sie den optionalen `x-ms-documentdb-populatequerymetrics`-Header (`FeedOptions.PopulateQueryMetrics` im .NET SDK) übergeben. Der zurückgegebene Wert in `x-ms-documentdb-query-metrics` hat folgende Schlüssel/Wert-Paare, die für die erweiterte Problembehandlung bei der Ausführung einer Abfrage vorgesehen sind. 

| Metrik | Einheit | Beschreibung | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | Millisekunden | Abfrageausführungszeit | 
| `queryCompileTimeInMs` | Millisekunden | Abfragekompilierzeit  | 
| `queryLogicalPlanBuildTimeInMs` | Millisekunden | Zeit zum Erstellen eines logischen Abfrageplans | 
| `queryPhysicalPlanBuildTimeInMs` | Millisekunden | Zeit zum Erstellen eines physischen Abfrageplans | 
| `queryOptimizationTimeInMs` | Millisekunden | Aufgewendete Zeit zum Optimieren der Abfrage | 
| `VMExecutionTimeInMs` | Millisekunden | Für die Abfragelaufzeit aufgewendete Zeit | 
| `indexLookupTimeInMs` | Millisekunden | Aufgewendete Zeit in der physischen Indexebene | 
| `documentLoadTimeInMs` | Millisekunden | Aufgewendete Zeit zum Laden von Dokumenten  | 
| `systemFunctionExecuteTimeInMs` | Millisekunden | Gesamte aufgewendete Zeit für die Ausführung von (integrierten) Systemfunktionen in Millisekunden  | 
| `userFunctionExecuteTimeInMs` | Millisekunden | Gesamte aufgewendete Zeit für die Ausführung von benutzerdefinierten Funktionen in Millisekunden | 
| `retrievedDocumentCount` | Millisekunden | Gesamtanzahl der abgerufenen Dokumente  | 
| `retrievedDocumentSize` | Byte | Gesamtgröße der abgerufenen Dokumente in Bytes  | 
| `outputDocumentCount` | count | Anzahl der Ausgabedokumente | 
| `writeOutputTimeInMs` | Millisekunden | Abfrageausführungsdauer in Millisekunden | 
| `indexUtilizationRatio` | Verhältnis (<=1) | Verhältnis zwischen der Anzahl der mit dem Filter abgeglichen Dokumente und der Anzahl der geladenen Dokumente  | 

Die Client-SDKs können intern möglicherweise mehrere Abfrageoperationen ausführen, um die Abfrage in jeder Partition verarbeiten zu können. Der Client führt mehrere Aufrufe pro Partition durch, wenn die gesamten Ergebnisse `x-ms-max-item-count` überschreiten, die Abfrage den für die Partition bereitgestellten Durchsatz überschreitet, die Abfragenutzlast die maximale Größe pro Seite erreicht oder die Abfrage den vom System zugeordneten Timeoutgrenzwert erreicht. Jede partielle Abfrageausführung gibt `x-ms-documentdb-query-metrics` für diese Seite zurück. 

Im Folgenden werden einige Beispielabfragen vorgestellt und erläutert, wie einige von der Abfrageausführung zurückgegebenen Metriken zu interpretieren sind: 

| Abfrage | Beispielmetrik | Beschreibung | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Die Anzahl der abgerufenen Dokumente beträgt 100+1 entsprechend der TOP-Klausel. Die Abfragezeit wird hauptsächlich für `WriteOutputTime` und `DocumentLoadTime` aufgewendet, da es sich um eine Überprüfung handelt. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | „RetrievedDocumentCount“ ist nun höher (500+1 entsprechend der TOP-Klausel). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Etwa 0,9 ms wird für „IndexLookupTime“ für eine Schlüsselsuche aufgewendet, da eine Indexsuche für `/N/?` durchgeführt wird. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Etwas mehr Zeit (1,7 ms) wird für „IndexLookupTime“ über einen Bereichsscan aufgewendet, da eine Indexsuche für `/N/?` durchgeführt wird. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Dieselbe Zeit wird wie bei den vorherigen Abfragen für `DocumentLoadTime` aufgewendet, jedoch weniger Zeit für `WriteOutputTime`, da nur eine Eigenschaft projiziert wird. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Etwa 213 ms wird für `UserDefinedFunctionExecutionTime` zur Ausführung der benutzerdefinierten Funktion für jeden Wert von `c.N` aufgewendet. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Etwa 0,6 ms wird für `IndexLookupTime` bezüglich `/Name/?` aufgewendet. Der Großteil der Abfrageausführungszeit (~7 ms) wird für `SystemFunctionExecutionTime` aufgewendet. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Die Abfrage wird als Überprüfung ausgeführt, da `LOWER` verwendet wird, und 500 von 2491 abgerufene Dokumente zurückgegeben werden. |


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu den unterstützten SQL-Abfrageoperatoren und Schlüsselwörtern finden Sie unter [SQL-Abfrage](documentdb-sql-query.md). 
* Weitere Informationen zu Anforderungseinheiten finden Sie unter [Anforderungseinheiten](request-units.md).
* Weitere Informationen zur Indizierungsrichtlinie finden Sie unter [Indizierungsrichtlinie](indexing-policies.md). 



