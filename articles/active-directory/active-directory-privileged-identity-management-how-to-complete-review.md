---
title: "Abschließen einer Zugriffsüberprüfung | Microsoft Docs"
description: "Nachdem Sie eine Überprüfung in Azure AD Privileged Identity Management gestartet haben, erfahren Sie, wie Sie sie abschließen und die Ergebnisse anzeigen"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: b31863124b4c1aa64951de5a9afb1d6d85d54ee0
ms.contentlocale: de-de
ms.lasthandoff: 02/06/2017

---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Abschließen einer Zugriffsüberprüfung in Azure AD Privileged Identity Management
Nachdem eine [Sicherheitsüberprüfung gestartet wurde](active-directory-privileged-identity-management-how-to-start-security-review.md), können Administratoren für privilegierte Rollen den privilegierten Zugriff überprüfen. Azure AD Privileged Identity Management (PIM) sendet automatisch eine E-Mail, in der Benutzer aufgefordert werden, ihren Zugriff zu überprüfen. Benutzern, die diese E-Mail nicht erhalten, können Sie die Anweisungen unter [Ausführen einer Sicherheitsüberprüfung](active-directory-privileged-identity-management-how-to-perform-security-review.md)senden.

Wenn der Zeitraum für die Sicherheitsüberprüfung abgelaufen ist oder alle Benutzer die Selbstüberprüfung abgeschlossen haben, führen Sie die Schritte in diesem Artikel aus, um die Überprüfung zu verwalten und die Ergebnisse anzuzeigen.

## <a name="manage-security-reviews"></a>Verwalten von Sicherheitsüberprüfungen
1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/) , und wählen Sie auf dem Dashboard die Anwendung **Azure AD Privileged Identity Management** aus.
2. Wählen Sie auf dem Dashboard den Abschnitt **Zugriffsüberprüfungen** aus.
3. Wählen Sie die Zugriffsüberprüfung aus, die Sie verwalten möchten.

Auf dem Detailblatt der Zugriffsüberprüfung stehen eine Reihe von Optionen zum Verwalten dieser Überprüfung zur Verfügung.

![Schaltflächen der PIM-Zugriffsüberprüfung – Screenshot][1]

### <a name="remind"></a>Erinnerung
Wenn eine Zugriffsüberprüfung so eingerichtet ist, dass die Benutzer sich selbst überprüfen, wird über die Schaltfläche **Erinnerung** eine Benachrichtigung gesendet. 

### <a name="stop"></a>Beenden
Alle Zugriffsüberprüfungen weisen ein Enddatum auf, Sie können jedoch die Schaltfläche **Beenden** verwenden, um sie zu einem frühen Zeitpunkt abzuschließen. Wenn nicht alle Benutzer zu diesem Zeitpunkt überprüft wurden, ist dies nach dem Beenden der Überprüfung nicht mehr möglich. Eine Überprüfung kann nicht neu gestartet werden, nachdem sie beendet wurde.

### <a name="apply"></a>Anwenden
Nachdem eine Überprüfung abgeschlossen wurde, weil das Enddatum erreicht ist oder weil sie manuell beendet wurde, wird mit der Schaltfläche **Übernehmen** das Ergebnis der Überprüfung implementiert. Wenn bei der Überprüfung der Zugriff eine Benutzers verweigert wurde, ist dies der Schritt, mit dem die Rollenzuweisung entfernt wird.  

### <a name="export"></a>Export
Wenn Sie die Ergebnisse der Sicherheitsüberprüfung manuell anwenden möchten, können Sie die Überprüfung exportieren. Über die Schaltfläche **Export** können Sie den Download einer CSV-Datei starten. Sie können die Ergebnisse in Excel oder einem anderen Programm verwalten, in dem sich CSV-Dateien öffnen lassen.

### <a name="delete"></a>Löschen
Wenn Sie an einer Überprüfung nicht weiter interessiert sind, löschen Sie sie. Über die Schaltfläche **Löschen** entfernen Sie die Überprüfung aus der PIM-Anwendung.

> [!IMPORTANT]
> Vor dem Löschvorgang wird keine Warnung angezeigt. Vergewissern Sie sich daher, dass Sie die Überprüfung tatsächlich löschen möchten. 

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png

