---
title: Disponibilità e ridondanza in Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Informazioni su disponibilità e ridondanza in Azure Key Vault.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 3c5afc92044fcb109bedd38298b0b027ebeb437d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749691"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilità e ridondanza dell'insieme di credenziali delle chiavi di Azure

L'insieme di credenziali delle chiavi di Azure dispone di più livelli di ridondanza, per garantire che le chiavi e i segreti rimangano disponibili per l'applicazione anche quando si verificano errori di singoli componenti del servizio.

> [!NOTE]
> Le informazioni della guida si applicano agli insiemi di credenziali. I pool del modulo di protezione hardware gestito usano un diverso modello di disponibilità elevata e ripristino di emergenza. Per altre informazioni, vedere la [guida al ripristino di emergenza del modulo di protezione hardware gestito](../managed-hsm/disaster-recovery-guide.md) for more information.

I contenuti dell'insieme di credenziali delle chiavi vengono replicati all'interno dell'area e in un'area secondaria distante almeno 250 chilometri, ma all'interno della stessa area geografica per mantenere una durabilità elevata delle chiavi e dei segreti. Per informazioni dettagliate su coppie di aree specifiche, vedere il documento [Aree abbinate di Azure](../../best-practices-availability-paired-regions.md). L'eccezione al modello delle aree abbinate è costituita dal Brasile meridionale, che offre solo l'opzione di mantenere i dati residenti all'interno del Brasile meridionale. L'area Brasile meridionale usa l'archiviazione con ridondanza della zona (ZRS) per replicare i dati tre volte all'interno della singola località/area. Per AKV Premium, solo 2 delle 3 aree vengono usate per replicare i dati da HSM.  

Se si verificano errori di singoli componenti del servizio dell'insieme di credenziali delle chiavi, per gestire la richiesta subentrano componenti alternativi all'interno dell'area in modo che non si verifichi alcuna riduzione delle prestazioni delle funzionalità. Non è necessario intraprendere alcuna azione per avviare questo processo, si verifica automaticamente ed è trasparente per l'utente.

Nella rara eventualità che l'intera area di Azure risulti non disponibile, le richieste eseguite per Azure Key Vault in tale area vengono indirizzate automaticamente (*failover*) a un'area secondaria, ad eccezione dell'area Brasile meridionale. Quando l'area primaria diventa di nuovo disponibile, le richieste vengono reindirizzate a tale area (*failback*). Anche in questo caso non è richiesta alcuna azione perché questa operazione viene eseguita in modo automatico.

Nell'area Brasile meridionale è necessario pianificare il ripristino degli insiemi di credenziali delle chiavi di Azure in caso di errori nell'area. Per eseguire il backup e il ripristino dell'insieme di credenziali delle chiavi di Azure in un'area di propria scelta, completare i passaggi descritti in dettaglio in [Backup di Azure Key Vault](backup.md). 

Grazie a questa progettazione a disponibilità elevata, Azure Key Vault non richiede alcun tempo di inattività per le attività di manutenzione.

Esistono alcune limitazioni che è necessario tenere presenti:

* Nel caso del failover di un'area, il failover del servizio può richiedere alcuni minuti. Le richieste effettuate durante questo periodo prima del failover potrebbero non riuscire.
* Se si usa un collegamento privato per connettersi all'insieme di credenziali delle chiavi, potrebbero essere necessari fino a 20 minuti per ristabilire la connessione in caso di failover. 
* Dopo il failover, l'insieme di credenziali delle chiavi è in modalità di sola lettura. Le richieste supportate in questa modalità sono le seguenti:
  * List certificates
  * Get certificates
  * List secrets
  * Get secrets
  * List keys
  * Get (properties of) keys
  * Encrypt
  * Decrypt
  * Wrap
  * Unwrap
  * Verifica
  * Sign
  * Backup

* Durante il failover, non sarà possibile apportare modifiche alle proprietà dell'insieme di credenziali delle chiavi. Non sarà possibile modificare le impostazioni e le configurazioni del firewall o dei criteri di accesso.

* Dopo il failback di un failover, tutti i tipi di richiesta, ad esempio le richieste di lettura *e* scrittura, risultano disponibili.
