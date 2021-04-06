---
title: Approfondimento sul funzionamento di Azure AD Connect Cloud Sync
description: Questo argomento fornisce informazioni approfondite sul funzionamento della sincronizzazione cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613840"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Approfondimento sulla sincronizzazione cloud: funzionamento

## <a name="overview-of-components"></a>Panoramica dei componenti

![Funzionamento](media/concept-how-it-works/how-1.png)

La sincronizzazione cloud si basa sui servizi Azure AD e dispone di due componenti chiave:

- **Agente di provisioning**: l'agente di provisioning Azure ad Connect Cloud è lo stesso agente della giornata lavorativa in ingresso e basato sulla stessa tecnologia lato server del proxy applicazione e l'autenticazione pass-through. Richiede solo la connessione in uscita e gli agenti vengono aggiornati automaticamente. 
- **Servizio di provisioning**: lo stesso servizio di provisioning come provisioning in uscita e il provisioning in ingresso della giornata lavorativa che usa un modello basato su utilità di pianificazione. In caso di sincronizzazione cloud, il provisioning delle modifiche viene effettuato ogni 2 minuti.


## <a name="initial-setup"></a>Configurazione iniziale
Durante la configurazione iniziale, vengono eseguite alcune operazioni che rendono possibile la sincronizzazione cloud.  Si tratta di: 

- **Durante l'installazione dell'agente**: si configura l'agente per i domini di Active Directory di cui si vuole eseguire il provisioning.  Questa configurazione registra i domini nel servizio di identità ibrido e stabilisce una connessione in uscita al bus di servizio in ascolto per le richieste.
- **Quando si Abilita il provisioning**: si seleziona il dominio di Active Directory e si Abilita il provisioning che viene eseguito ogni 2 minuti. Facoltativamente, è possibile deselezionare la sincronizzazione dell'hash delle password e definire la posta elettronica di notifica. È anche possibile gestire la trasformazione degli attributi usando le API Microsoft Graph.


## <a name="agent-installation"></a>Installazione dell'agente
Di seguito è riportata una procedura dettagliata di ciò che si verifica quando l'agente di provisioning cloud è installato.

- Prima di tutto, il programma di installazione installa i file binari dell'agente e il servizio Agent in esecuzione con l'account del servizio virtuale (rete SERVICE\AADProvisioningAgent).  Un account del servizio virtuale è un tipo speciale di account che non ha una password ed è gestito da Windows.
- Il programma di installazione avvia quindi la procedura guidata.
- La procedura guidata richiederà le credenziali Azure AD, effettuerà l'autenticazione e recupererà un token.
- La procedura guidata chiede quindi le credenziali degli amministratori di dominio del computer corrente.
- Utilizzando queste credenziali, l'account del servizio gestito generale dell'agente (GMSA) per questo dominio viene creato o individuato e riutilizzato se esiste già.
- Il servizio Agent viene ora riconfigurato per l'esecuzione in GMSA.
- La procedura guidata chiede ora la configurazione del dominio con l'account amministratore (EA)/Domain amministratore (DA) per ogni dominio di cui l'agente deve eseguire il servizio.
- L'account GMSA viene quindi aggiornato con le autorizzazioni che consentono l'accesso a ogni dominio immesso in precedenza.
- Successivamente, la procedura guidata attiva la registrazione dell'agente
- L'agente crea un certificato e usa il token di Azure AD, registra se stesso e il certificato con il servizio di registrazione del servizio di identità ibrido (HIS).
- La procedura guidata attiva una chiamata AgentResourceGrouping. Questa chiamata al servizio di amministrazione consente di assegnare l'agente a uno o più domini di Active Directory nella sua configurazione.
- Tramite la procedura guidata viene ora riavviato il servizio Agent.
- L'agente chiama un servizio bootstrap al riavvio (e ogni 10 minuti dopo) per verificare la disponibilità di aggiornamenti della configurazione.  Il servizio bootstrap convalida l'identità dell'agente.  Aggiorna inoltre l'ora dell'ultimo bootstrap.  Questo è importante perché se gli agenti non eseguono il bootstrap, non ricevono endpoint del bus di servizio aggiornati e potrebbero non essere in grado di ricevere richieste. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Che cos'è SCIM (System for Cross-domain Identity Management)?

La [specifica scim](https://tools.ietf.org/html/draft-scim-core-schema-01) è uno standard usato per automatizzare lo scambio di informazioni sull'identità utente o gruppo tra domini di identità, ad esempio Azure ad. SCIM sta diventando lo standard più comune per il provisioning e, in combinazione con standard di federazione come OpenID Connect o SAML, offre agli amministratori una soluzione end-to-end basata su standard per la gestione degli accessi.

L'agente di provisioning di Azure AD Connect Cloud USA SCIM con Azure AD per eseguire il provisioning e il deprovisioning di utenti e gruppi.

## <a name="synchronization-flow"></a>Flusso di sincronizzazione
![provisioning ](media/concept-how-it-works/provisioning-4.png) dopo aver installato l'agente e abilitato il provisioning, si verifica il flusso seguente.

1.  Una volta configurata, il servizio di provisioning di Azure AD chiama il servizio ibrido Azure AD per aggiungere una richiesta al bus di servizio. L'agente mantiene costantemente una connessione in uscita al bus di servizio in ascolto delle richieste e preleva immediatamente il sistema per la richiesta di gestione delle identità tra domini (SCIM). 
2.  L'agente suddivide la richiesta in query separate in base al tipo di oggetto. 
3.  AD restituisce il risultato all'agente e l'agente filtra questi dati prima di inviarli a Azure AD.  
4.  Agent restituisce la risposta SCIM a Azure AD.  Queste risposte sono basate sul filtro che si è verificato all'interno dell'agente.  L'agente utilizza l'ambito per filtrare i risultati. 
5.  Il servizio di provisioning scrive le modifiche in Azure AD.
6. Se si tratta di una sincronizzazione delta anziché di una sincronizzazione completa, viene usato il cookie/filigrana. Le nuove query otterranno modifiche da tale cookie/filigrana in avanti.

## <a name="supported-scenarios"></a>Scenari supportati:
Per la sincronizzazione cloud sono supportati gli scenari seguenti.


- **Cliente ibrido esistente con una nuova foresta**: la sincronizzazione Azure ad Connect viene usata per le foreste primarie. La sincronizzazione cloud viene usata per il provisioning da una foresta di Active Directory (inclusa la disconnessione). Per ulteriori informazioni, vedere l'esercitazione [qui](tutorial-existing-forest.md).

 ![Ibrido esistente](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Nuovo cliente ibrido**: non viene usato Azure ad Connect Sync. La sincronizzazione cloud viene utilizzata per il provisioning da una foresta AD.  Per ulteriori informazioni, vedere l'esercitazione [qui](tutorial-single-forest.md).
 
 ![Nuovi clienti](media/tutorial-single-forest/diagram-2.png)

- **Cliente ibrido esistente**: la sincronizzazione Azure ad Connect viene usata per le foreste primarie. La sincronizzazione cloud è pilota per un piccolo set di utenti nelle foreste primarie [qui](tutorial-existing-forest.md).

 ![Progetto pilota esistente](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Per ulteriori informazioni, vedere [topologie supportate](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
