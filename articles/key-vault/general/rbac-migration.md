---
title: Eseguire la migrazione al controllo degli accessi in base al ruolo di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione dai criteri di accesso dell'insieme di credenziali ai ruoli di Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749079"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Eseguire la migrazione dai criteri di accesso dell'insieme di credenziali a un modello di autorizzazione per il controllo degli accessi in base al ruolo di Azure

Il modello di criteri di accesso dell'insieme di credenziali è un sistema di autorizzazione Key Vault per fornire l'accesso a chiavi, segreti e certificati. È possibile controllare l'accesso assegnando singole autorizzazioni all'entità di sicurezza (utente, gruppo, entità servizio, identità gestita) nell Key Vault ambito. 

Il controllo degli accessi in base al ruolo di Azure è un sistema di autorizzazione basato su [Azure Resource Manager](../../azure-resource-manager/management/overview.md) che offre una gestione degli accessi con granularità fine delle risorse di Azure. Con il controllo degli accessi in base al ruolo di Azure è possibile controllare l'accesso alle risorse creando assegnazioni di ruoli, costituite da tre elementi: entità di sicurezza, definizione del ruolo (set predefinito di autorizzazioni) e ambito (gruppo di risorse o singola risorsa). Per altre informazioni, vedere Controllo [degli accessi in base al ruolo di Azure.](../../role-based-access-control/overview.md)

Prima di eseguire la migrazione al controllo degli accessi in base al ruolo di Azure, è importante comprenderne i vantaggi e le limitazioni.

Vantaggi chiave del controllo degli accessi in base al ruolo di Azure rispetto ai criteri di accesso dell'insieme di credenziali:
- Fornisce un modello di controllo di accesso unificato per le risorse di Azure, la stessa API nei servizi di Azure
- Gestione centralizzata degli accessi per gli amministratori: gestire tutte le risorse di Azure in un'unica visualizzazione
- Integrato con [l Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) per il controllo degli accessi in base al tempo
- Assegnazioni di rifiuto: possibilità di escludere l'entità di sicurezza in un determinato ambito. Per informazioni, vedere Informazioni [sulle assegnazioni di rifiuto di Azure](../../role-based-access-control/deny-assignments.md)

Svantaggi del controllo degli accessi in base al ruolo di Azure:
- Latenza per le assegnazioni di ruolo: l'applicazione dell'assegnazione di ruolo può richiedere alcuni minuti. I criteri di accesso dell'insieme di credenziali vengono assegnati immediatamente.
- Numero limitato di assegnazioni di ruolo: 2000 assegnazioni di ruoli per sottoscrizione rispetto a 1024 criteri di accesso per Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Mapping dei criteri di accesso ai ruoli di Azure

Il controllo degli accessi in base al ruolo di Azure ha diversi ruoli predefiniti di Azure che è possibile assegnare a utenti, gruppi, entità servizio e identità gestite. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md).

Key Vault ruoli predefiniti per la gestione dell'accesso a chiavi, certificati e segreti:
- Key Vault amministratore
- Key Vault lettore
- Key Vault Certificate Officer
- Key Vault Crypto Officer
- Key Vault utente crypto
- Key Vault utente crittografia del servizio Crypto
- Key Vault Secrets Officer
- Key Vault Secrets User

Per altre informazioni sui ruoli predefiniti esistenti, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md)

I criteri di accesso dell'insieme di credenziali possono essere assegnati con autorizzazioni selezionate singolarmente o con modelli di autorizzazione predefiniti.

Modelli di autorizzazione predefiniti per i criteri di accesso:
- Chiave, segreto, Gestione certificati
- Gestione & chiave
- Gestione certificati & segreto
- Gestione chiavi
- Gestione dei segreti
- Gestione dei certificati
- Connettore SQL Server
- Azure Data Lake Storage o Archiviazione di Azure
- Backup di Azure
- Chiave del cliente di Exchange Online
- Chiave del cliente di SharePoint Online
- BYOK di Informazioni di Azure

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Mapping dei modelli di criteri di accesso ai ruoli di Azure
| Modello di criteri di accesso | Operazioni | Ruolo di Azure |
| --- | --- | --- |
| Gestione di chiavi, segreti, certificati | Chiavi: tutte le operazioni <br>Certificati: tutte le operazioni<br>Segreti: tutte le operazioni | Key Vault amministratore |
| Gestione & chiave | Chiavi: tutte le operazioni <br>Segreti: tutte le operazioni| Key Vault Crypto Officer <br> Key Vault Secrets Officer |
| Secret & Certificate Management | Certificati: tutte le operazioni <br>Segreti: tutte le operazioni| Key Vault Certificates Officer <br> Key Vault Secrets Officer|
| Gestione chiavi | Chiavi: tutte le operazioni| Key Vault Crypto Officer|
| Gestione dei segreti | Segreti: tutte le operazioni| Key Vault Secrets Officer|
| Gestione dei certificati | Certificati: tutte le operazioni | Key Vault Certificates Officer|
| Connettore SQL Server | Chiavi: get, list, wrap key, unwrap key | Key Vault utente crittografia del servizio Crypto|
| Azure Data Lake Storage o Archiviazione di Azure | Chiavi: get, list, unwrap key | N/D<br> Ruolo personalizzato obbligatorio|
| Backup di Azure | Chiavi: get, list, backup<br> Certificato: get, list, backup | N/D<br> Ruolo personalizzato obbligatorio|
| Chiave del cliente di Exchange Online | Chiavi: get, list, wrap key, unwrap key | Key Vault utente crittografia del servizio Crypto|
| Chiave del cliente di Exchange Online | Chiavi: get, list, wrap key, unwrap key | Key Vault utente crittografia del servizio Crypto|
| BYOK di Informazioni di Azure | Chiavi: get, decrypt, sign | N/D<br>Ruolo personalizzato obbligatorio|


## <a name="assignment-scopes-mapping"></a>Mapping degli ambiti di assegnazione  

Il controllo degli accessi in base al ruolo di Azure Key Vault l'assegnazione dei ruoli agli ambiti seguenti:
- Gruppo di gestione
- Subscription
- Resource group
- Key Vault risorsa
- Singola chiave, segreto e certificato

Il modello di autorizzazione dei criteri di accesso dell'insieme di credenziali è limitato all'assegnazione dei criteri Key Vault livello di risorsa, che 

In generale, è consigliabile avere un insieme di credenziali delle chiavi per ogni applicazione e gestire l'accesso a livello di insieme di credenziali delle chiavi. Esistono scenari in cui la gestione dell'accesso in altri ambiti può semplificare la gestione degli accessi.

- **Infrastruttura, amministratori e operatori della sicurezza: la gestione di gruppi di insiemi di credenziali delle chiavi a livello di gruppo di gestione, sottoscrizione o gruppo di risorse con criteri di accesso dell'insieme di credenziali richiede la gestione dei criteri per ogni insieme di credenziali delle chiavi. Il controllo degli accessi in base al ruolo di Azure consente di creare un'assegnazione di ruolo nel gruppo di gestione, nella sottoscrizione o nel gruppo di risorse. Tale assegnazione verrà applicata a tutti i nuovi insiemi di credenziali delle chiavi creati nello stesso ambito. In questo scenario è consigliabile usare l'accesso Privileged Identity Management accesso JUST-In-Time invece di fornire l'accesso permanente.
 
- **Applicazioni: esistono scenari in cui l'applicazione deve condividere il segreto con un'altra applicazione. Usando i criteri di accesso dell'insieme di credenziali è stato necessario creare un insieme di credenziali delle chiavi separato per evitare di concedere l'accesso a tutti i segreti. Il controllo degli accessi in base al ruolo di Azure consente invece di assegnare un ruolo con ambito per un singolo segreto usando un singolo insieme di credenziali delle chiavi.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Passaggi di migrazione dei criteri di accesso dell'insieme di credenziali al controllo degli accessi in base al ruolo di Azure
Esistono molte differenze tra il controllo degli accessi in base al ruolo di Azure e il modello di autorizzazione dei criteri di accesso dell'insieme di credenziali. Per evitare interruzioni durante la migrazione, è consigliabile seguire questa procedura.
 
1. **Identificare e assegnare ruoli:** identificare i ruoli predefiniti in base alla tabella di mapping precedente e creare ruoli personalizzati quando necessario. Assegnare ruoli agli ambiti, in base alle linee guida per il mapping degli ambiti. Per altre informazioni su come assegnare ruoli all'insieme di credenziali delle chiavi, vedere Fornire l'accesso alle Key Vault con un controllo [degli accessi in](rbac-guide.md) base al ruolo di Azure
1. **Convalidare l'assegnazione dei** ruoli: la propagazione delle assegnazioni di ruolo nel controllo degli accessi in base al ruolo di Azure può richiedere alcuni minuti. Per informazioni su come controllare le assegnazioni di ruolo, vedere [Elencare le assegnazioni di ruoli nell'ambito](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Configurare il monitoraggio e l'avviso nell'insieme** di credenziali delle chiavi: è importante abilitare la registrazione e la configurazione degli avvisi per le eccezioni di accesso negato. Per altre informazioni, vedere [Monitoraggio e avvisi per Azure Key Vault](./alert.md)
1. **Impostare il modello di autorizzazione di** controllo degli accessi in base al ruolo di Azure Key Vault: l'abilitazione del modello di autorizzazione controllo degli accessi in base al ruolo di Azure invaliderà tutti i criteri di accesso esistenti. Se si verifica un errore, è possibile tornare indietro con tutti i criteri di accesso esistenti rimasti invariati.

> [!NOTE]
> La modifica del modello di autorizzazione richiede l'autorizzazione "Microsoft.Authorization/roleAssignments/write", che fa parte dei ruoli [Proprietario](../../role-based-access-control/built-in-roles.md#owner) e Amministratore [accesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) utenti. I ruoli di amministratore della sottoscrizione classica, ad esempio "Amministratore del servizio" e "Co-amministratore", non sono supportati.

> [!NOTE]
> Quando il modello di autorizzazione controllo degli accessi in base al ruolo di Azure è abilitato, tutti gli script che tentano di aggiornare i criteri di accesso avranno esito negativo. È importante aggiornare questi script per l'uso del controllo degli accessi in base al ruolo di Azure.

## <a name="troubleshooting"></a>Risoluzione dei problemi
-  L'assegnazione di ruolo non funziona dopo alcuni minuti: in alcune situazioni le assegnazioni di ruolo possono richiedere più tempo. È importante scrivere la logica di ripetizione dei tentativi nel codice per coprire questi casi.
- Le assegnazioni di ruolo sono scomparse quando l'Key Vault è stata eliminata (eliminazione soft) e ripristinata. Si tratta attualmente di una limitazione della funzionalità di eliminazione soft in tutti i servizi di Azure. È necessario ricreare tutte le assegnazioni di ruolo dopo il ripristino.    

## <a name="learn-more"></a>Altre informazioni

- [Panoramica del controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)
- [Esercitazione sui ruoli personalizzati](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)