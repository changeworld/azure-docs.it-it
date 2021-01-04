---
ms.openlocfilehash: 77866fcb72515d5167d9f06b7d9ad91c59bc7945
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97700586"
---
Questo articolo elenca le versioni e le funzionalità di Azure Active Directory Connect agente di provisioning che sono state rilasciate. Il team di Azure AD aggiorna periodicamente l'agente di provisioning con nuove caratteristiche e funzionalità. L'agente di provisioning viene aggiornato automaticamente quando viene rilasciata una nuova versione. 

Microsoft fornisce supporto diretto per la versione più recente dell'agente e per una versione precedente.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Stato della versione

23 novembre 2020: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Supporto per [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Supporto per i gruppi fino a dimensioni inferiori a 1500 membri durante il ciclo di sincronizzazione incrementale o differenziale. Questa operazione è applicabile quando si usa il filtro di ambito gruppo
* Supporto per gruppi di grandi dimensioni con dimensione membro fino a 15.000
* Miglioramenti della sincronizzazione iniziale
* Registrazione dettagliata avanzata
* Introduzione del [modulo AADCloudSyncTools di PowerShell](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Limitazioni fisse per consentire l'installazione di Agent in un server non in lingua inglese
* Supporto per il filtraggio di pH solo per gli oggetti nell'ambito (originariamente, sono stati sincronizzati gli hash delle password per tutti gli oggetti)
* Correzione del problema di perdita di memoria nell'agente
* Miglioramento dei log di provisioning
* Supporto per la configurazione del [timeout della connessione LDAP](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Supporto per la configurazione dell' [inseguimento dei riferimenti](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stato della versione

4 dicembre 2019: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Include il supporto per il [provisioning del cloud Azure ad Connect](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) per sincronizzare gli utenti, i contatti e i dati di gruppo dall'Active Directory locale al Azure ad


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stato della versione

9 settembre 2019: rilasciata per l'aggiornamento automatico

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Possibilità di configurare la traccia e la registrazione aggiuntive per il debug dei problemi dell'agente di provisioning
* Possibilità di recuperare solo gli attributi Azure AD configurati nel mapping per migliorare le prestazioni di sincronizzazione

### <a name="fixed-issues"></a>Problemi risolti

* Correzione di un bug per cui l'agente si trovava in uno stato che non risponde se si sono verificati problemi con Azure AD errori di connessione
* Correzione di un bug che causava problemi durante la lettura dei dati binari da Azure Active Directory
* Correzione di un bug per cui l'agente non è riuscito a rinnovare l'attendibilità con il servizio di identità cloud ibrido

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stato della versione

23 gennaio 2019: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Rinnovato l'agente di provisioning e l'architettura del connettore per ottenere prestazioni, stabilità e affidabilità migliori 
* Semplificazione della configurazione dell'agente di provisioning tramite l'installazione guidata guidata dall'interfaccia utente 
* Aggiunta del supporto per aggiornamenti automatici degli agenti


