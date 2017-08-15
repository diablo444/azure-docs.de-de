---
title: Grundlegendes zu Warnungen in Azure Log Analytics | Microsoft-Dokumentation
description: "Mit Warnungen in Log Analytics werden wichtige Informationen in Ihrem OMS-Repository identifiziert, und Sie können proaktiv über Probleme informiert werden oder Aktionen aufrufen, um zu versuchen, die Probleme zu beheben.  In diesem Artikel werden die verschiedenen Arten von Warnungsregeln und ihre Definition beschrieben."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 951e76d3fb18d9e433b148e82d4d6cee9417ce6d
ms.contentlocale: de-de
ms.lasthandoff: 07/28/2017

---
# <a name="understanding-alerts-in-log-analytics"></a>Grundlegendes zu Warnungen in Log Analytics

Mit Warnungen werden in Log Analytics wichtige Informationen in Ihrem Log Analytics-Repository identifiziert.  Dieser Artikel enthält Details zur Funktionsweise von Warnungsregeln in Log Analytics und beschreibt die Unterschiede verschiedener Arten von Warnungsregeln.

Informationen zum Erstellen von Warnungsregeln finden Sie in den folgenden Artikeln:

- Erstellen von Warnungsregeln im [Azure-Portal](log-analytics-alerts-creating.md)
- Erstellen von Warnungsregeln mithilfe einer [Resource Manager-Vorlage](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Erstellen von Warnungsregeln mithilfe der [REST-API](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Warnungsregeln

Warnungen werden mithilfe von Warnungsregeln erstellt, für die in regelmäßigen Abständen automatisch Protokollsuchen durchgeführt werden.  Wenn die Ergebnisse der Protokollsuche bestimmte Kriterien erfüllen, wird ein Warnungsdatensatz erstellt.  Die Regel kann dann automatisch eine oder mehrere Aktionen ausführen, um Sie proaktiv über die Warnung zu informieren oder einen anderen Prozess aufzurufen.  Verschiedene Typen von Warnungsregeln verwenden für diese Analyse unterschiedliche Logik.

![Log Analytics-Warnungen](media/log-analytics-alerts/overview.png)

Warnungsregeln werden anhand der folgenden Details definiert:

- **Protokollsuche**:  Die Abfrage, die bei jeder Auslösung der Warnungsregel ausgeführt wird.  Mit den von dieser Abfrage zurückgegebenen Datensätzen wird ermittelt, ob eine Warnung erstellt werden muss.
- **Zeitfenster**:  Gibt den Zeitraum für die Abfrage an.  Die Abfrage gibt nur Datensätze zurück, die innerhalb dieses aktuellen Zeitbereichs erstellt wurden.  Dies kann ein beliebiger Wert zwischen 5 Minuten und 24 Stunden sein. Wenn das Zeitfenster beispielsweise auf 60 Minuten festgelegt ist und die Abfrage um 13:15 Uhr ausgeführt wird, werden nur Datensätze zurückgegeben, die zwischen 12:15 und 13:15 Uhr erstellt wurden.
- **Häufigkeit**:  Gibt an, wie oft die Abfrage ausgeführt werden soll. Dies kann ein beliebiger Wert zwischen 5 Minuten und 24 Stunden sein. Er sollte kleiner als oder gleich dem Zeitfensterwert sein.  Wenn der Wert größer als das Zeitfenster ist, besteht das Risiko, dass Datensätze ausgelassen werden.<br>Angenommen, Sie verwenden ein Zeitfenster von 30 Minuten und eine Häufigkeit von 60 Minuten.  Wenn die Abfrage um 13:00 Uhr ausgeführt wird, gibt sie die Datensätze für den Zeitraum zwischen 12:30 und 13:00 Uhr zurück.  Wenn die Abfrage dann das nächste Mal um 14 Uhr ausgeführt wird, gibt sie die Datensätze für den Zeitraum zwischen 13:30 und 14:00 Uhr zurück.  Alle Datensätze, die zwischen 13:00 und 13:30 erstellt werden, werden also nicht ausgewertet.
- **Schwellenwert**:  Die Ergebnisse der Protokollsuche werden ausgewertet, um festzustellen, ob eine Warnung generiert werden soll.  Der Schwellenwert ist für verschiedene Typen von Warnungsregeln unterschiedlich.

Es gibt zwei Typen von Warnungsregeln in Log Analytics.  Diese Typen werden in den nachstehenden Abschnitten ausführlich beschrieben.

- **[Anzahl von Ergebnissen](#number-of-results-alert-rules)**. Einzelne Warnung, die generiert wird, wenn die Anzahl der von der Protokollsuche zurückgegebenen Datensätze einen angegebenen Wert überschreitet.
- **[Metrische Maßeinheit](#metric-measurement-alert-rules)**.  Warnung, die für jedes Objekt in den Ergebnissen der Protokollsuche bei Werten generiert wird, die den angegebenen Schwellenwert überschreiten.

Die Unterschiede zwischen den Warnungsregeltypen sind wie folgt.

- Die Warnungsregel **Anzahl von Ergebnissen** erstellt stets eine einzelne Warnung, während die Warnungsregel **Metrische Maßeinheit** eine Warnung für jedes Objekt erzeugt, das den Schwellenwert überschreitet.
- Warnungsregeln vom Typ **Anzahl von Ergebnissen** erzeugen eine Warnung, wenn der Schwellenwert ein einziges Mal überschritten wird. Warnungsregeln des Typs **Metrische Maßeinheit** können eine Warnung generieren, wenn der Schwellenwert in einem bestimmten Zeitintervall mit einer bestimmten Häufigkeit überschritten wird.

## <a name="number-of-results-alert-rules"></a>Warnungsregeln des Typs „Anzahl von Ergebnissen“
Warnungsregeln des Typs **Anzahl von Ergebnissen** erzeugen eine einzige Warnung, wenn die von der Suchabfrage zurückgegebene Anzahl von Datensätze den angegebenen Schwellenwert überschreitet.

### <a name="threshold"></a>Schwellenwert
Der Schwellenwert für die Warnungsregel **Anzahl von Ergebnissen** ist lediglich größer oder kleiner als ein bestimmter Wert.  Eine Warnung wird erstellt, wenn die Anzahl der von der Protokollsuche zurückgegebenen Datensätze dieses Kriterium erfüllt.

### <a name="scenarios"></a>Szenarien

#### <a name="events"></a>Ereignisse
Diese Art von Warnungsregel ist ideal für das Arbeiten mit Ereignissen wie Windows-Ereignisprotokollen, Syslog und benutzerdefinierten Protokollen.  Es kann ratsam sein, eine Warnung zu erstellen, wenn ein bestimmtes Fehlerereignis erstellt wird oder wenn mehrere Fehlerereignisse innerhalb eines bestimmten Zeitfensters erstellt werden.

Legen Sie zum Auslösen einer Warnung aufgrund eines einzelnen Ereignisses die Anzahl von Ergebnissen auf einen Wert größer als 0 und sowohl die Häufigkeit als auch das Zeitfenster auf 5 Minuten fest.  Die Abfrage wird dann alle 5 Minuten ausgeführt. Außerdem wird eine Prüfung auf das Vorhandensein eines einzelnen Ereignisses durchgeführt, das seit der letzten Ausführung der Abfrage erstellt wurde.  Bei einem längeren Intervall verlängert sich ggf. der Zeitraum zwischen der Ereigniserfassung und der Warnungserstellung.

Für einige Anwendungen wird unter Umständen gelegentlich ein Fehler protokolliert, für den nicht unbedingt eine Warnung ausgelöst werden muss.  Es kann beispielsweise sein, dass die Anwendung versucht, den Vorgang mit dem Fehler erneut durchzuführen, und dass der Vorgang dann erfolgreich ist.  In diesem Fall sollten Sie die Warnung ggf. nur erstellen, wenn mehrere Ereignisse innerhalb eines bestimmten Zeitfensters erstellt werden.  

Es kann auch vorkommen, dass Sie eine Warnung erstellen möchten, ohne dass ein entsprechendes Ereignis vorliegt.  Für einen Prozess können beispielsweise regelmäßig Ereignisse protokolliert werden, um anzugeben, dass er richtig funktioniert.  Wenn innerhalb eines bestimmten Zeitfensters nicht eines dieser Ereignisse protokolliert wird, sollte eine Warnung erstellt werden.  In diesem Fall sollten Sie den Schwellenwert auf **Kleiner als 1** festlegen.

#### <a name="performance-alerts"></a>Leistungswarnungen
[Leistungsdaten](log-analytics-data-sources-performance-counters.md) werden ähnlich wie Ereignisse als Datensätze im OMS-Repository gespeichert.  Falls gewarnt werden soll, wenn ein Leistungsindikator einen bestimmten Schwellenwert überschreitet, sollte dieser Schwellenwert in die Abfrage einbezogen werden.

Falls beispielsweise eine Warnung erfolgen soll, wenn der Prozessor zu mehr als 90 Prozent ausgelastet ist, können Sie eine Abfrage wie die folgende verwenden und dabei den Schwellenwert für die Warnungsregel auf **Größer als 0** festlegen:

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Falls eine Warnung erfolgen soll, wenn der Prozessor innerhalb eines bestimmten Zeitfensters im Schnitt zu mehr als 90 Prozent ausgelastet ist, können Sie wie im folgenden Beispiel eine Abfrage mit dem [measure-Befehl](log-analytics-search-reference.md#commands) verwenden und dabei den Schwellenwert für die Warnungsregel auf **Größer als 0** festlegen:

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> Falls für Ihren Arbeitsbereich ein Upgrade auf die [neue Log Analytics-Abfragesprache](log-analytics-log-search-upgrade.md) durchgeführt wurde, müssen die obigen Abfragen wie folgt geändert werden: `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>Warnungsregeln des Typs „Metrische Maßeinheit“

>[!NOTE]
> Warnungsregeln des Typs „Metrische Maßeinheit“ befinden sich derzeit in der öffentlichen Vorschauphase.

Warnungsregeln des Typs **Metrische Maßeinheit** erzeugen eine Warnung für jedes Objekt in einer Abfrage mit einem Wert, der einen angegebenen Schwellenwert überschreitet.  Sie weisen gegenüber Warnungsregeln des Typs **Anzahl von Ergebnissen** die folgenden Unterschiede auf.

#### <a name="log-search"></a>Protokollsuche
Während Sie für eine Warnungsregel des Typs **Anzahl von Ergebnissen** eine beliebige Abfrage verwenden können, gelten für die Abfrage für eine Wartungsregel des Typs „Metrische Maßeinheit“ bestimmte Anforderungen.  Sie muss den Befehl [Measure](log-analytics-search-reference.md#commands) enthalten, um die Ergebnisse anhand eines bestimmten Felds zu gruppieren. Dieser Befehl muss die folgenden Elemente enthalten.

- **Aggregatfunktion**.  Bestimmt die zu erfolgende Berechnung und möglicherweise ein numerisches zu aggregierendes Feld.  Beispielsweise gibt **count()** die Anzahl der Datensätze in der Abfrage zurück, während **avg(CounterValue)** den Durchschnitt des Felds „CounterValue“ in diesem Intervall zurückgibt.
- **Gruppierungsfeld**.  Ein Datensatz mit einem aggregierten Wert wird für jede Instanz dieses Felds erstellt, und für jede kann eine Warnung generiert werden.  Wenn Sie beispielsweise eine Warnung für jeden Computer generieren möchten, wählen Sie **Computer**.   
- **Intervall**:  Definiert das Intervall, über das die Daten aggregiert werden.  Bei Angabe von **5 Minuten** wird beispielsweise ein Datensatz für jede Instanz des Gruppierungsfelds erstellt, das für das für die Warnung angegebene Zeitfenster in 5-Minuten-Intervallen aggregiert wird.

#### <a name="threshold"></a>Schwellenwert
Der Schwellenwert für Warnungsregeln des Typs „Metrische Maßeinheit“ wird mittels eines Aggregatwerts und einer Anzahl von Verstößen definiert.  Wenn bei der Protokollsuche ein Datenpunkt diesen Wert überschreitet, gilt dies als Verstoß.  Wenn die Anzahl der Verstöße für ein beliebiges Objekt in den Ergebnissen den angegebenen Wert überschreitet, wird eine Warnung für dieses Objekt generiert.

#### <a name="example"></a>Beispiel
Angenommen, Sie wünschen sich eine Warnung, wenn ein beliebiger Computer binnen 30 Minuten dreimal die Prozessornutzung von 90 % überschreitet.  Dazu erstellen Sie eine Warnungsregel mit den folgenden Details.  

**Abfrage:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Zeitfenster:** 30 Minuten<br>
**Warnungshäufigkeit:** 5 Minuten<br>
**Aggregatwert:** Größer als 90<br>
**Warnung auslösen basierend auf:** Gesamtanzahl der Verstöße größer als 3<br>

Die Abfrage ermittelt einen Durchschnittswert für jeden Computer in 5-Minuten-Intervallen.  Diese Abfrage wird alle 5 Minuten für die in den letzten 30 Minuten gesammelten Daten ausgeführt.  Nachstehend sehen Sie Beispieldaten für drei Computer.

![Ergebnisse der Beispielabfrage](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Bei diesem Beispiel werden separate Warnungen für srv02 und srv03 erstellt, da diese Computer im Zeitfenster dreimal gegen den Schwellenwert 90 % verstoßen haben.  Wenn **Warnung auslösen basierend auf:** in **Aufeinander folgend** geändert wird, wird nur für srv03 eine Warnung generiert, da dieser bei drei aufeinander folgenden Stichproben gegen den Schwellenwert verstoßen hat.

## <a name="alert-records"></a>Warnungsdatensätze
Für Warnungsdatensätze, die in Log Analytics mit Warnungsregeln erstellt wurden, ist **Warnung** als **Typ** und **OMS** als **SourceSystem** festgelegt.  Sie verfügen über die Eigenschaften, die in der folgenden Tabelle aufgeführt sind.

| Eigenschaft | Beschreibung |
|:--- |:--- |
| Typ |*Warnung* |
| SourceSystem |*OMS* |
| *Object*  | Warnungen des Typs [Metrischer Messwert](#metric-measurement-alert-rules) weisen eine Eigenschaft für das Gruppierungsfeld auf.  Wenn z.B. bei der Protokollsuche eine Gruppierung anhand von „Computer“ erfolgt, weist der Warnungsdatensatz das Feld „Computer“ mit dem Namen des Computers als Wert auf.
| AlertName |Name der Warnung. |
| AlertSeverity |Schweregrad der Warnung. |
| LinkToSearchResults |Link zur Log Analytics-Protokollsuche, mit der die Datensätze aus der Abfrage zurückgegeben werden, mit der die Warnung erstellt wurde |
| Abfrage |Der Text der Abfrage, die ausgeführt wurde. |
| QueryExecutionEndTime |Gibt das Ende des Zeitraums für die Abfrage an. |
| QueryExecutionStartTime |Gibt den Beginn des Zeitraums für die Abfrage an. |
| ThresholdOperator | Operator, der von der Warnungsregel verwendet wird. |
| ThresholdValue | Wert, der von der Warnungsregel verwendet wird. |
| TimeGenerated |Datum und Uhrzeit der Warnungserstellung. |

Es gibt noch andere Arten von Warnungsdatensätzen, die von der [Alert Management-Lösung](log-analytics-solution-alert-management.md) und von [Power BI-Exporten](log-analytics-powerbi.md) erstellt werden.  Für diese ist **Warnung** als **Typ** festgelegt, aber sie unterscheiden sich durch ihr **SourceSystem**.


## <a name="next-steps"></a>Nächste Schritte
* Installieren Sie die [Lösung für die Warnungsverwaltung](log-analytics-solution-alert-management.md), um die in Log Analytics erstellten Warnungen und die über System Center Operations Manager gesammelten Warnungen zu analysieren.
* Informieren Sie sich weiter über [Protokollsuchen](log-analytics-log-searches.md) , bei denen Warnungen generiert werden können.
* Arbeiten Sie eine exemplarische Vorgehensweise für das [Konfigurieren eines Webooks](log-analytics-alerts-webhooks.md) mit einer Warnungsregel durch.  
* Informieren Sie sich darüber, wie Sie [Runbooks in Azure Automation](https://azure.microsoft.com/documentation/services/automation) schreiben, um von Warnungen identifizierte Probleme zu beheben.

