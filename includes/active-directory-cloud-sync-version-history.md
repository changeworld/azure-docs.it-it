---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900994"
---
Questo articolo elenca le versioni e le funzionalità di Azure Active Directory Connect agente di provisioning che sono state rilasciate. Il team di Azure AD aggiorna periodicamente l'agente di provisioning con nuove caratteristiche e funzionalità. L'agente di provisioning viene aggiornato automaticamente quando viene rilasciata una nuova versione. 

Microsoft fornisce supporto diretto per la versione più recente dell'agente e per una versione precedente.

## <a name="113540"></a>1.1.354.0

20 gennaio 2021: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
- Miglioramento dell'esperienza GMSA, incluso il supporto per l'account GMSA creato in precedenza
- Supporto dei [cmdlet di PowerShell](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) per l'installazione di gMSA
- [Supporto dell'interfaccia](../articles/active-directory/cloud-sync/how-to-install-pshell.md) della riga di comando per installazione agente (installazione invisibile all'utente)
- Diagnostica aggiuntiva per i problemi di quarantena dell'origine agente
- Correzioni di bug che includono la riduzione dell'utilizzo della memoria dei filtri di ambito OU, l'esecuzione di pH solo per gli utenti in ambito, la gestione di oggetti annidati in OU quando si usano l'ambito dell'unità organizzativa e così via. 


### <a name="fixed-issues"></a>Problemi risolti
-    Impedisci la quarantena quando il gruppo di ambito è esterno all'ambito
-   Quando vengono configurati i filtri di ambito, il processo pH ora funziona solo per gli utenti inclusi nell'ambito
-   L'agente si bloccherà durante l'aggiornamento
-   Sincronizzazione iniziale per gli oggetti nelle ou annidate quando si usa l'ambito dell'unità organizzativa
-   Rendere il Repair-AADCloudSyncToolsAccount più affidabile
-   Ridurre l'utilizzo della memoria di grandi dimensioni dei filtri di ambito OU
-   Il controllo del ruolo amministratore ha esito negativo se i membri del ruolo contengono un gruppo di sicurezza
-   Correzione del problema di autorizzazione della cartella GMSA che impedisce il rinnovo del certificato dell'agente







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Stato della versione

23 novembre 2020: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Supporto per [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Supporto per i gruppi fino a dimensioni inferiori a 1500 membri durante il ciclo di sincronizzazione incrementale o differenziale. Questa operazione è applicabile quando si usa il filtro di ambito gruppo
* Supporto per gruppi di grandi dimensioni con dimensione membro fino a 15.000
* Miglioramenti della sincronizzazione iniziale
* Registrazione dettagliata avanzata
* Introduzione del [modulo AADCloudSyncTools di PowerShell](../articles/active-directory/cloud-sync/reference-powershell.md)
* Limitazioni fisse per consentire l'installazione di Agent in un server non in lingua inglese
* Supporto per il filtraggio di pH solo per gli oggetti nell'ambito (originariamente, sono stati sincronizzati gli hash delle password per tutti gli oggetti)
* Correzione del problema di perdita di memoria nell'agente
* Miglioramento dei log di provisioning
* Supporto per la configurazione del [timeout della connessione LDAP](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Supporto per la configurazione dell' [inseguimento dei riferimenti](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stato della versione

4 dicembre 2019: rilasciato per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Include il supporto per [Azure ad Connect sincronizzazione cloud](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) per sincronizzare i dati utente, di contatto e di gruppo da Active Directory locale a Azure ad


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