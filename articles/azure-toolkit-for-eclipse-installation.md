---
title: "Installieren des Azure Toolkit für Eclipse | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie das Azure Toolkit für Eclipse installieren."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 35cddba38c364dfb2f6a8646b0014d48ca4cb795
ms.contentlocale: de-de
ms.lasthandoff: 06/08/2017


---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Installieren des Azure-Toolkits für Eclipse
Das Azure Toolkit für Eclipse stellt Vorlagen und Funktionen bereit, die Ihnen das einfache Erstellen, Entwickeln, Testen und Bereitstellen von Azure-Anwendungen mithilfe der Eclipse-Entwicklungsumgebung ermöglichen. Das Azure Toolkit für Eclipse ist ein Open-Source-Projekt. Der Quellcode ist verfügbar unter der MIT-Lizenz unter <https://github.com/microsoft/azure-tools-for-java>.

Die folgenden Schritte veranschaulichen die Installation des Azure-Toolkits für Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>So installieren Sie das Azure-Toolkit für Eclipse
1. Starten Sie Eclipse.
2. Klicken Sie auf das Menü **Help** (Hilfe) und dann auf **Install New Software** (Neue Software installieren), wie in der folgenden Abbildung gezeigt.
   
    ![Installieren des Azure-Toolkits für Eclipse][01]
3. Geben Sie im Dialogfeld **Available Software** (Verfügbare Software) im Textfeld **Work with** (Arbeiten mit) `http://dl.microsoft.com/eclipse` ein, und drücken Sie dann die **EINGABETASTE**.
4. Aktivieren Sie im Fenster **Name** die Option **Azure Toolkit for Eclipse**, und deaktivieren Sie **Contact all update sites during install to find required software**. Der Bildschirm sieht dann etwa wie folgt aus:
   
    ![Installieren des Azure-Toolkits für Eclipse][02]
5. Nach dem Erweitern von **Azure Toolkit for Eclipse**(Azure Toolkit für Eclipse) sehen Sie die folgenden Elemente:
   
   * **Application Insights-Plug-In for Java:**Diese Komponente ermöglicht Ihnen die Verwendung der Telemetriedatenprotokollierung und der Analysedienste von Azure für Ihre Anwendungen und Server-Instanzen.
   * **Azure Access Control Services Filter:**Diese Komponente bietet Unterstützung für die Authentifizierung von Anwendungsbenutzern mit Azure ACS, sodass Szenarios mit einmaligem Anmelden ermöglicht werden und die Identitätslogik aus der Anwendung externalisiert wird.
   * **Azure Common Plugin (Allgemeines Azure-Plug-In):**Diese Komponente stellt die allgemeine Funktionalität bereit, die für andere Toolkit-Komponenten erforderlich ist.
   * **Azure Explorer for Eclipse (Azure-Explorer für Eclipse):**Diese Komponente stellt die allgemeine Funktionalität bereit, die für andere Toolkit-Komponenten erforderlich ist.
   * **Azure Plugin for Eclipse with Java (Azure-Plug-In für Eclipse mit Java):**Diese Komponente bietet Unterstützung für die Entwicklung von Projekten, mit deren Hilfe Java-Anwendungen für die Microsoft Azure-Cloud in Eclipse und über die Befehlszeile erstellt, getestet und bereitgestellt werden können.
   * **Azure Web Apps Plugin with Java (Azure Web Apps-Plug-In mit Java):**Diese Komponente bietet Unterstützung für die Bereitstellung von Java-Webanwendungen in Microsoft Azure-Web-App-Containern.
   * **Microsoft JDBC Driver 4.2 for SQL Server (Microsoft JDBC-Treiber 4.2 für SQL Server):**Diese Komponente stellt die JDBC-API für SQL Server und Microsoft Azure SQL-Datenbank für die Java Platform Enterprise Edition 8 bereit.
   * **Package for Apache Qpid Client Libraries for JMS (Paket für Apache Qpid-Clientbibliotheken für JMS):**Diese Komponente stellt die JMS-Clientkomponente aus dem Apache Qpid-Projekt bereit, um Ihrer Anwendung die Verwendung von AMQP-Messaging in Microsoft Azure zu ermöglichen.
   * **Package for Microsoft Azure Libraries for Java (Paket für Microsoft Azure-Bibliotheken für Java):** Diese Komponente stellt APIs für den Zugriff auf Microsoft Azure-Dienste wie Storage, Service Bus, Service Runtime usw. bereit.
6. Klicken Sie auf **Weiter**.
 (Wenn ungewöhnliche Verzögerungen bei der Installation des Toolkits auftreten, achten Sie darauf, dass **Contact all update sites during install to find required software** deaktiviert ist.)
7. Klicken Sie im Dialogfeld **Install Details** (Installationsdetails) auf **Weiter**.
   
    ![Installationsdetails überprüfen][03]
8. Überprüfen Sie im Dialogfeld **Review Licences** die Bedingungen der Lizenzverträge. Wenn Sie dem Lizenzvertrag zustimmen, klicken Sie auf **I accept the terms of the license agreements** (Ich akzeptiere die Bedingungen der Lizenzvereinbarungen), und klicken Sie dann auf **Fertig stellen**. (Bei den restlichen Schritten wird davon ausgegangen, dass Sie dem Lizenzvertrag zugestimmt haben. Wenn Sie die Bedingungen der Lizenzverträge nicht akzeptieren, beenden Sie den Installationsprozess.)
   
    ![Review Licences][04]
   
    Eclipse lädt die erforderlichen Pakete herunter und installiert diese.
   
    ![Installationsstatus][05]
9. Wenn Sie aufgefordert werden, Eclipse zum Abschließen der Installation neu zu starten, klicken Sie auf **Yes**(Ja).
   
    ![Aufforderung zum Neustarten][06]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu den Azure-Toolkits für Java-IDEs finden Sie unter den folgenden Links:

* [Azure-Toolkit für Eclipse]
  * [Neuerungen im Azure-Toolkit für Eclipse]
  * *Installieren des Azure-Toolkits für Eclipse (dieser Artikel)*
  * [Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse]
  * [Erstellen einer „Hello World“-Web-App für Azure in Eclipse]
* [Azure Toolkit für IntelliJ]
  * [Neuerungen im Azure-Toolkit für IntelliJ]
  * [Installieren des Azure Toolkit für IntelliJ]
  * [Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ]
  * [Erstellen einer „Hello World“-Web-App für Azure in IntelliJ]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center].

<!-- URL List -->

[Azure-Toolkit für Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit für IntelliJ]: ./azure-toolkit-for-intellij.md
[Erstellen einer „Hello World“-Web-App für Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Erstellen einer „Hello World“-Web-App für Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installieren des Azure Toolkit für IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Neuerungen im Azure-Toolkit für Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Neuerungen im Azure-Toolkit für IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

