---
title: "Überwachen der Integrität und Nutzung Ihrer Anwendung mit Application Insights"
description: "Erste Schritte mit Application Insights. Analysieren Sie die Auslastung, Verfügbarkeit und Leistung Ihres lokalen oder Microsoft Azure-Anwendungen."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 69ead621c179bf49f17ed3274be4b625fc587556
ms.contentlocale: de-de
ms.lasthandoff: 07/10/2017

---
# <a name="monitor-performance-in-web-applications"></a>Leistung in Webanwendungen überwachen


Stellen sie sicher, dass Ihre Anwendung optimal funktioniert, und stellen Sie Fehler umgehend fest. [Application Insights][start] informiert Sie über alle Leistungsprobleme und Ausnahmefälle. So können Sie die Ursachen schnell ermitteln und diagnostizieren.

Application Insights kann Java- und ASP.NET-Webanwendungen und -Dienste sowie WCF-Dienste überwachen. Das Hosting kann lokal, auf virtuellen Computern oder als Microsoft Azure-Websites erfolgen. 

Auf Clientseite kann Application Insights Telemetriedaten von Webseiten und eine Vielzahl von Geräten sammeln, einschließlich iOS-, Android- und Windows Store-Apps.

>[!Note]
> Wir bieten eine neue Oberfläche zum Auffinden langsamer Seiten in Ihrer Webanwendung. Wenn Sie keinen Zugriff darauf haben, aktivieren Sie ihn durch Konfigurieren der Vorschauoptionen auf dem Blatt [Vorschauversion](app-insights-previews.md). Unter [Auffinden und Beseitigen von Leistungsengpässen mithilfe der interaktiven Leistungsuntersuchung](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation) erfahren Sie mehr zu dieser neuen Oberfläche.

## <a name="setup"></a>Einrichten der Leistungsüberwachung
Falls Sie Application Insights Ihrem Projekt noch nicht hinzugefügt haben (d. h., wenn es nicht über ApplicationInsights.config verfügt), gehen Sie nach einer der folgenden Methoden vor, um zu beginnen:

* [ASP.NET-Web-Apps](app-insights-asp-net.md)
  * [Ausnahmeüberwachung hinzufügen](app-insights-asp-net-exceptions.md)
  * [Abhängigkeitsüberwachung hinzufügen](app-insights-monitor-performance-live-website-now.md)
* [J2EE-Web-Apps](app-insights-java-get-started.md)
  * [Abhängigkeitsüberwachung hinzufügen](app-insights-java-agent.md)

## <a name="view"></a>Untersuchen von Leistungsmetriken
Navigieren Sie im [Azure-Portal](https://portal.azure.com)zu der Application Insights-Ressource, die Sie für Ihre Anwendung eingerichtet haben. Das Blatt "Übersicht" zeigt grundlegende Leistungsdaten:

Klicken Sie auf ein beliebiges Diagramm, um weitere Details und Ergebnisse über einen längeren Zeitraum anzuzeigen. Klicken Sie beispielsweise auf die Kachel "Requests", und wählen Sie dann einen Zeitraum aus.

![Zu mehr Daten durchklicken und einen Zeitraum auswählen](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Klicken Sie auf ein Diagramm, um die anzuzeigenden Metriken auszuwählen, oder fügen Sie ein neues Diagramm hinzu, und wählen Sie dann die Metriken aus:

![Auf Graph klicken, um Metrik auszuwählen](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Deaktivieren Sie alle Metriken**, um die insgesamt verfügbare Auswahl anzuzeigen. Die Metriken werden in Gruppen unterteilt. Wenn ein Mitglied einer Gruppe ausgewählt wird, werden nur die weiteren Mitglieder dieser Gruppe angezeigt.

## <a name="metrics"></a>Was bedeutet was? Leistungskacheln und Berichte
Ihnen stehen verschiedene Leistungsmetriken zur Verfügung. Lassen Sie uns mit denen beginnen, die standardmäßig im Anwendungsblatt angezeigt werden.

### <a name="requests"></a>Requests
Die in einem angegebenen Zeitraum empfangenen HTTP-Anforderungen. Vergleichen Sie den Wert mit den Ergebnissen anderer Berichte, um das Verhalten Ihrer Anwendung mit wechselnder Last zu beurteilen.

HTTP-Anforderungen umfassen alle GET- oder POST-Anforderungen für Seiten, Daten und Bilder.

Klicken Sie auf die Kachel, um Zählwerte für bestimmte URLs zu erhalten.

### <a name="average-response-time"></a>Average response time
Misst die Zeit zwischen dem Eingang einer Webanforderung bei Ihrer Anwendung und der zurückgegebenen Antwort.

Die Punkte zeigen einen variablen Durchschnittswert. Falls viele Anforderungen eingehen, gibt es ggf. vom Durchschnitt deutlich nach oben oder unten abweichende Spitzen im Graphen.

Achten Sie auf ungewöhnliche Spitzen. Im Allgemeinen können Sie mit einer längeren Antwortzeit bei steigender Zahl der Anforderungen rechnen. Falls der Anstieg nicht proportional ist, gelangt Ihre Anwendung möglicherweise an Ressourcengrenzen wie die CPU-Leistung oder die Kapazität eines verwendeten Diensts.

Klicken Sie auf die Kachel, um Zeiten für bestimmte URLs zu erhalten.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Slowest requests
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Zeigt, welche Anforderungen möglicherweise eine Leistungsfeinabstimmung erfordern.

### <a name="failed-requests"></a>Failed requests
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Zählwert von Anforderungen, die nicht abgefangene Ausnahmefehler verursacht haben.

Klicken Sie auf die Kachel, um Details zu bestimmten Fehlern anzuzeigen, und wählen Sie einzelne Anforderungen aus, um die jeweiligen Details anzuzeigen. 

Nur eine repräsentative Menge an Fehler wird zur individuellen Überprüfung zurückgehalten.

### <a name="other-metrics"></a>Other metrics
Um andere verfügbare Metriken aufzurufen, klicken Sie auf einen Graphen und wählen dann alle Metriken ab. Daraufhin werden alle verfügbaren Metriken angezeigt. Klicken Sie auf (i), um die jeweilige Definition der Metrik anzuzeigen.

![Alle Metriken abwählen, um alle verfügbaren anzuzeigen](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Wenn Sie eine Metrik auswählen, werden alle anderen deaktiviert, die nicht im selben Diagramm angezeigt werden können.

## <a name="set-alerts"></a>Festlegen von Benachrichtigungen
Fügen Sie eine Benachrichtigung hinzu, wenn Sie per E-Mail über ungewöhnliche Werte einer beliebigen Metrik informiert werden möchten. Sie können auswählen, ob die E-Mail an die Kontoadministratoren oder an bestimmte E-Mail-Adressen gesendet wird.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Legen Sie die Ressource vor den anderen Eigenschaften fest. Wählen Sie nicht die Webtestressourcen, wenn Sie Benachrichtigungen für Leistungs- oder Nutzungsmetriken festlegen möchten.

Achten Sie auf die Einheiten, die beim Eingeben des Schwellenwerts gefordert sind.

*Ich sehe keine Schaltfläche zum Hinzufügen von Benachrichtigungen.* – Handelt es sich um ein Gruppenkonto, für das Sie nur über schreibgeschützten Zugriff verfügen? Wenden Sie sich an den Kontoadministrator.

## <a name="diagnosis"></a>Diagnostizieren von Problemen
Im Folgenden finden Sie einige Tipps zum Feststellen und Diagnostizieren von Leistungsproblemen:

* Richten Sie [Webtests][availability] ein, um benachrichtigt zu werden, falls Ihre Website nicht erreichbar ist oder fehlerhaft bzw. langsam reagiert. 
* Vergleichen Sie den Request-Zählwert mit anderen Metriken, um festzustellen, ob Fehler oder langsame Reaktionen mit der Last zusammenhängen.
* [Fügen Sie Ihrem Code Trace-Anweisungen hinzu bzw. suchen Sie diese][diagnostic], um Probleme besser einzugrenzen.
* Überwachen Sie den Betrieb Ihrer Web-App mithilfe von [Live Metrics Stream][livestream].
* Erfassen Sie den Zustand Ihrer .NET-Anwendung mithilfe des [Momentaufnahmedebuggers][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-performance-investigation"></a>Auffinden und Beseitigen von Leistungsengpässen mithilfe der interaktiven Leistungsuntersuchung

Mithilfe der neuen interaktiven Application Insights-Leistungsuntersuchung können Sie Bereiche in Ihrer Web-App mit schwacher Gesamtleistung bestimmen. Sie können schnell bestimmte Seiten finden, die Sie ausbremsen, und mithilfe des [Tools für die Profilerstellung](app-insights-profiler.md) prüfen, ob es eine Korrelation zwischen diesen Seiten gibt.

### <a name="create-a-list-of-slow-performing-pages"></a>Erstellen einer Liste der langsamen Seiten 

Der erste Schritt zum Bestimmen von Leistungsproblemen ist das Abrufen einer Liste mit langsam reagierenden Seiten. Der nachstehende Screenshot veranschaulicht, wie Sie mithilfe des Blatts „Leistung“ eine Liste von Seiten abrufen, die potenziell weiter untersucht werden sollten. Anhand dieser Seite können Sie rasch erkennen, dass es um 18:00 Uhr und erneute gegen 22:00 Uhr zu einer langsameren Antwortzeit gekommen ist. Sie können auch erkennen, dass der Vorgang zum Abrufen von Kundendetails einige lang andauernde Operationen mit einer mittleren Antwortzeit von 507,05 Millisekunden aufweist. 

![Interaktive Application Insights-Leistungsuntersuchung](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Detailuntersuchung bestimmter Seiten

Nachdem Sie eine Momentaufnahme der Leistung Ihrer App erstellt haben, erhalten Sie weitere Details zu bestimmten langsamen Vorgängen. Klicken Sie auf einen Vorgang in der Liste, um die Details anzuzeigen (siehe unten). Anhand des Diagramms können Sie erkennen, dass die Leistung auf einer Abhängigkeit basierte. Sie können auch die Anzahl der Benutzer sehen, bei denen die verschiedenen Antwortzeiten aufgetreten sind. 

![Application Insights-Blatt „Vorgänge“](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Detailuntersuchung eines bestimmten Zeitraums

Nachdem Sie einen zu untersuchenden Zeitpunkt bestimmt haben, setzen Sie die Detailuntersuchung mit den spezifischen Vorgängen fort, die die Verlangsamung der Leistung ggf. verursacht haben. Beim Klicken auf einen bestimmten Zeitpunkt werden die Details der Seite angezeigt (siehe unten). Beim nachstehenden Beispiel können Sie die für einen bestimmten Zeitraum aufgeführten Vorgänge sowie die Serverantwortcodes und die Vorgangsdauer erkennen. Angezeigt wird auch die URL zum Öffnen einer TFS-Arbeitsaufgabe, wenn Sie diese Informationen an Ihr Entwicklungsteam senden müssen.

![Application Insights-Zeitsegment](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Detailuntersuchung eines bestimmten Vorgangs

Nachdem Sie einen zu untersuchenden Zeitpunkt bestimmt haben, setzen Sie die Detailuntersuchung mit den spezifischen Vorgängen fort, die die Verlangsamung der Leistung ggf. verursacht haben. Klicken Sie auf einen Vorgang in der Liste, um die Details des Vorgangs anzuzeigen (siehe unten). In diesem Beispiel sehen Sie, dass der Vorgang fehlgeschlagen ist und dass Application Insights die Details der Ausnahme bereitstellt, die die Anwendung ausgelöst hat. Wiederum können Sie problemlos eine TFS-Arbeitsaufgabe auf diesem Blatt erstellen.

![Application Insights-Blatt „Vorgänge“](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>Nächste Schritte
[Webtests][availability]: Lassen Sie in regelmäßigen Abständen aus aller Welt Webanforderungen an Ihre Anwendung senden.

[Diagnostische Spuren protokollieren und suchen][diagnostic]: Fügen Sie Trace-Aufrufe ein, und durchsuchen Sie die Ergebnisse, um Probleme einzugrenzen.

[Nutzungsnachverfolgung][usage]: Erfahren Sie, wie Ihre Anwendung genutzt wird.

[Problembehandlung][qna] und Fragen und Antworten



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md




