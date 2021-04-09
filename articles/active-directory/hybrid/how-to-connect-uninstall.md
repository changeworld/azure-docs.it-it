---
title: Disinstallare Azure AD Connect
description: In questo documento viene descritto come disinstallare Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934185"
---
# <a name="uninstall-azure-ad-connect"></a>Disinstallare Azure AD Connect

In questo documento viene descritto come disinstallare correttamente Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Disinstalla Azure AD Connect dal server
Per prima cosa è necessario rimuovere Azure AD Connect dal server in cui è in esecuzione.  Eseguire la procedura descritta di seguito:

 1. Nel server in cui è in esecuzione Azure AD Connect passare al **Pannello di controllo**.
 2. Fare clic su **Disinstalla un** programma 
  ![ Disinstalla un programma](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Selezionare **Azure AD Connect**.
 ![Selezionare Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Quando richiesto, fare clic su **Sì** per confermare.
 5. Con questa conferma verrà visualizzata la schermata Azure AD Connect.  Fare clic su **Rimuovi**.
 ![Rimuovi](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Al termine dell'azione, fare clic su **Esci**.
 7. ![Esci](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Tornare al **Pannello di controllo** fare clic su **Aggiorna** e tutti i componenti dovrebbero essere stati rimossi.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
- [Installare Azure AD Connect usando un database ADSync esistente](how-to-connect-install-existing-database.md)
- [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md)

