---
title: "Tutorial: Konfigurieren von ThousandEyes für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in ThousandEyes konfigurieren."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: e6bc2eab3cc1adcf26857ed98d920177a51455ea
ms.contentlocale: de-de
ms.lasthandoff: 07/19/2017

---

# <a name="tutorial-configuring-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von ThousandEyes für die automatische Benutzerbereitstellung


In diesem Tutorial werden die Schritte beschrieben, die Sie in ThousandEyes und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in ThousandEyes automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben. 

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Einen ThousandEyes-Mandanten, für den mindestens der [Standard-Tarif](https://www.thousandeyes.com/pricing) aktiviert ist 
*   Ein Benutzerkonto in ThousandEyes mit Teamadministratorberechtigungen 

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der [ThousandEyes-SCIM-API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), die für ThousandEyes-Teams mit dem Standard-Tarif oder einem höheren Tarif zur Verfügung steht.

## <a name="assigning-users-to-thousandeyes"></a>Zuweisen von Benutzern zu ThousandEyes

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden. 

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre ThousandEyes-App benötigen. Anschließend können Sie diese Benutzer Ihrer ThousandEyes-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Wichtige Tipps zum Zuweisen von Benutzern zu ThousandEyes

*   Es wird empfohlen, ThousandEyes einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Beim Zuweisen eines Benutzers zu ThousandEyes müssen Sie entweder die Rolle **Benutzer** oder eine andere gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Die Rolle **Standardzugriff** funktioniert nicht für die Bereitstellung. Diese Benutzer werden übersprungen.


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurieren der Benutzerbereitstellung in ThousandEyes 

In diesem Abschnitt wird das Herstellen einer Verbindung von Azure AD mit der ThousandEyes-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in ThousandEyes basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für ThousandEyes aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten für ThousandEyes in Azure AD


1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Suchen Sie über das Suchfeld nach Ihrer ThousandEyes-Instanz, wenn Sie ThousandEyes bereits für einmaliges Anmelden konfiguriert haben. Wählen Sie andernfalls **Hinzufügen**, und suchen Sie im Anwendungskatalog nach **ThousandEyes**. Wählen Sie ThousandEyes in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre ThousandEyes-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![ThousandEyes-Bereitstellung](./media/active-directory-saas-thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Geben Sie im Abschnitt **Admin Credentials** (Administratoranmeldeinformationen) das von Ihrem ThousandEyes-Konto generierte geheime Token ****ein. (Sie finden das Token in Ihrem ThousandEyes-Konto: **Sicherheit und Authentifizierung**). 

    ![ThousandEyes-Bereitstellung](./media/active-directory-saas-thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer ThousandEyes-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr ThousandEyes-Konto über Teamadministratorberechtigungen verfügt, und wiederholen Sie Schritt 5.

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen „Bei Fehler E-Mail-Benachrichtigung senden“.

8. Klicken Sie auf **Speichern**. 

9. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit ThousandEyes synchronisieren**.

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit ThousandEyes synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in ThousandEyes für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

11. Um den Azure AD-Bereitstellungsdienst für ThousandEyes zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

12. Klicken Sie auf **Speichern**. 

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die ThousandEyes im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 20 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](active-directory-enterprise-apps-manage-provisioning.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](active-directory-saas-provisioning-reporting.md)

