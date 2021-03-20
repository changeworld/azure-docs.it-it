---
title: Domande frequenti su Azure AD Connect Cloud Sync
description: Questo documento descrive le domande frequenti per la sincronizzazione cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613532"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Domande frequenti su Azure Active Directory Connect Cloud Sync

Informazioni sulle domande frequenti per Azure Active Directory (Azure AD) Connect Cloud Sync.

## <a name="general-installation"></a>Installazione generale

**D: con quale frequenza viene eseguita la sincronizzazione cloud?**

Il provisioning cloud è pianificato per l'esecuzione ogni 2 minuti. Ogni 2 minuti, verrà effettuato il provisioning di qualsiasi modifica apportata all'hash delle password, a gruppi e a utenti.

**D: visualizzazione degli errori di sincronizzazione dell'hash delle password alla prima esecuzione. Perché?**

Si tratta di un comportamento previsto. Gli errori sono dovuti all'oggetto utente non presente in Azure AD. Una volta effettuato il provisioning dell'utente in Azure AD, il provisioning degli hash delle password verrà effettuato nell'esecuzione successiva. Attendere che vengano completate un paio di esecuzioni e verificare che la sincronizzazione degli hash delle password non generi più errori.

**D: cosa accade se l'istanza di Active Directory dispone di attributi non supportati dalla sincronizzazione cloud (ad esempio, le estensioni di directory)?**

Il provisioning cloud viene eseguito ed effettua il provisioning degli attributi supportati. Il provisioning degli attributi non supportati non verrà effettuato in Azure AD. Esaminare le estensioni della directory in Active Directory e assicurarsi che questi attributi non siano necessari per la propagazione Azure AD. Se uno o più attributi sono necessari, è consigliabile usare il servizio di sincronizzazione Azure AD Connect o spostare le informazioni richieste in uno degli attributi supportati, ad esempio gli attributi di estensioni 1-15.

**D: qual è la differenza tra Azure AD Connect Sync e la sincronizzazione cloud?**

Con il servizio di sincronizzazione di Azure AD Connect, il provisioning viene effettuato nel server di sincronizzazione locale. La configurazione viene archiviata nel server di sincronizzazione locale. Con Azure AD Connect sincronizzazione cloud, la configurazione del provisioning viene archiviata nel cloud e viene eseguita nel cloud come parte del servizio di provisioning Azure AD. 

**D: è possibile usare la sincronizzazione cloud per eseguire la sincronizzazione da più Active Directory foreste?**

Sì. Il provisioning cloud può essere usato per la sincronizzazione da più foreste di Active Directory. Nell'ambiente a più foreste, tutti i riferimenti (ad esempio, Manager) devono trovarsi all'interno del dominio.  

**D: come viene aggiornato l'agente?**

Gli agenti vengono aggiornati automaticamente da Microsoft. Per i team IT, in questo modo si riduce il carico di test e convalida delle nuove versioni degli agenti. 

**D: è possibile disabilitare l'aggiornamento automatico?**

La disabilitazione dell'aggiornamento automatico non è supportata.

**D: è possibile modificare l'ancoraggio di origine per la sincronizzazione cloud?**

Per impostazione predefinita, la sincronizzazione cloud usa MS-DS-coerenza-GUID con un fallback a ObjectGUID come ancoraggio di origine. La modifica dell'ancoraggio di origine non è supportata.

**D: le nuove entità servizio vengono visualizzate con i nomi di dominio AD quando si usa la sincronizzazione cloud. È previsto?**

Sì, la sincronizzazione cloud crea un'entità servizio per la configurazione del provisioning con il nome di dominio come nome dell'entità servizio. Non apportare modifiche alla configurazione dell'entità servizio.

**D: cosa accade quando è necessario un utente sincronizzato per modificare la password all'accesso successivo?**

Se la sincronizzazione degli hash delle password è abilitata in sincronizzazione cloud e l'utente sincronizzato è necessario per modificare la password all'accesso successivo in AD locale, il Cloud Sync non esegue il provisioning dell'hash della password "da modificare" per Azure AD. Dopo che l'utente ha cambiato la password, viene effettuato il provisioning dell'hash della password da AD ad Azure AD.

**D: la sincronizzazione cloud supporta il writeback di MS-DS-consistencyGUID per qualsiasi oggetto?**

No, la sincronizzazione cloud non supporta il writeback di MS-DS-consistencyGUID per qualsiasi oggetto (inclusi gli oggetti utente). 

**D: si esegue il provisioning degli utenti tramite la sincronizzazione cloud. La configurazione è stata eliminata. Perché è ancora possibile visualizzare gli oggetti precedenti sincronizzati in Azure AD?** 

Quando si elimina la configurazione, la sincronizzazione cloud non rimuove automaticamente gli oggetti sincronizzati in Azure AD. Per assicurarsi che non siano presenti oggetti obsoleti, cambiare l'ambito della configurazione impostandolo su un gruppo o su unità organizzative vuote. Dopo l'effettuazione del provisioning e la pulizia degli oggetti, disabilitare ed eliminare la configurazione. 

**D: cosa significa che la soluzione ibrida di Exchange non è supportata?**

La funzionalità di distribuzione ibrida di Exchange consente la coesistenza di cassette postali di Exchange sia in locale che in Microsoft 365. Azure AD Connect sincronizza un set specifico di attributi da Azure AD alla directory locale.  L'agente di provisioning cloud non sincronizza attualmente questi attributi nella directory locale e pertanto non è supportato come sostituzione per Azure AD Connect.

**D: è possibile installare l'agente di provisioning cloud in Windows Server Core?**

No, l'installazione dell'agente in Server Core non è supportata.

**D: è possibile utilizzare un server di staging con l'agente di provisioning cloud?**

No, i server di gestione temporanea non sono supportati.

**D: è possibile sincronizzare gli account utente Guest?**

No, la sincronizzazione degli account utente Guest non è supportata.

**D: se si sposta un utente da un'unità organizzativa con ambito per la sincronizzazione cloud a un'unità organizzativa con ambito Azure AD Connect, cosa accade?**

L'utente verrà eliminato e ricreato.  Il passaggio di un utente da un'unità organizzativa con ambito per la sincronizzazione cloud verrà visualizzato come operazione di eliminazione.  Se l'utente viene spostato in un'unità organizzativa gestita da Azure AD Connect, ne verrà eseguito nuovamente il provisioning in Azure AD e verrà creato un nuovo utente.

**D: se si rinomina o si sposta l'unità organizzativa che rientra nell'ambito del filtro di sincronizzazione cloud, cosa accade all'utente creato in Azure AD?**

Nessun pacchetto.  Se l'unità organizzativa viene rinominata o spostata, gli utenti non verranno eliminati.

**D: Azure AD Connect Cloud Sync supporta gruppi di grandi dimensioni?**

Sì. Attualmente è supportato un massimo di 50.000 membri del gruppo sincronizzati con il filtro dell'ambito dell'unità organizzativa. Allo stesso tempo, quando si usa il filtro dell'ambito del gruppo, è consigliabile lasciare le dimensioni del gruppo a meno di 1500 membri. Il motivo è che anche se è possibile sincronizzare un gruppo di grandi dimensioni come parte del filtro di ambito del gruppo, quando si aggiungono membri al gruppo in base ai batch superiori a 1500, la sincronizzazione Delta avrà esito negativo. 

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
