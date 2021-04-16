---
title: Procedure consigliate per l'Azure Key Vault HSM gestito
description: Questo documento illustra alcune delle procedure consigliate per l'uso Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482518"
---
# <a name="best-practices-when-using-managed-hsm"></a>Procedure consigliate per l'uso del servizio HSM gestito

## <a name="control-access-to-your-managed-hsm"></a>Controllare l'accesso al HSM gestito

Il servizio HSM gestito è un servizio cloud che protegge le chiavi di crittografia. Poiché queste chiavi sono sensibili e business critical, assicurarsi di proteggere l'accesso ai HMS gestiti consentendo solo applicazioni e utenti autorizzati. Questo [articolo](access-control.md) offre una panoramica del modello di accesso. Illustra l'autenticazione e l'autorizzazione e il controllo degli accessi in base al ruolo.
- Creare un [Azure Active Directory di sicurezza per](../../active-directory/fundamentals/active-directory-manage-groups.md) gli amministratori del modulo di protezione HSM anziché assegnare il ruolo Di amministratore a singoli utenti. In questo modo si eviterà il "blocco amministrativo" in caso di eliminazione di singoli account.
- Bloccare l'accesso a gruppi di gestione, sottoscrizioni, gruppi di risorse e HMS gestiti: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso a gruppi di gestione, sottoscrizioni e gruppi di risorse
- Creare assegnazioni di ruolo per chiave usando il controllo degli accessi in base [al ruolo locale del modulo di protezione HSM gestito.](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Per mantenere la separazione dei compiti, evitare di assegnare più ruoli alle stesse entità. 
- Usare l'entità di accesso con privilegi minimi per assegnare i ruoli.
- Creare una definizione di ruolo personalizzata con un set preciso di autorizzazioni.

## <a name="choose-regions-that-support-availability-zones"></a>Scegliere le aree che supportano le zone di disponibilità

- Per garantire la migliore disponibilità elevata e resilienza della zona, scegliere le aree di Azure in zone di disponibilità [supportate.](../../availability-zones/az-overview.md) Queste aree vengono visualizzate come "Aree consigliate" nel portale di Azure.

## <a name="backup"></a>Backup

- Assicurarsi di eseguire backup regolari del HSM. È possibile eseguire backup a livello di HSM e per chiavi specifiche. 

## <a name="turn-on-logging"></a>Abilitare la registrazione

- [Attivare la registrazione](logging.md) per il servizio HSM. Configurare anche gli avvisi.

## <a name="turn-on-recovery-options"></a>Attivare le opzioni di ripristino

- [L'opzione Soft Delete](../general/soft-delete-overview.md) è impostata su on per impostazione predefinita.
- Attivare la protezione dall'eliminazione se si vuole proteggersi dall'eliminazione forzata del HSM anche dopo l'attivazione dell'eliminazione soft.

## <a name="next-steps"></a>Passaggi successivi

- Per [informazioni sul backup/ripristino](backup-restore.md) HSM completo, vedere Backup/ripristino completo.
- Vedere [Registrazione HSM gestita](logging.md) per informazioni su come usare Monitoraggio di Azure per configurare la registrazione
- Vedere [Gestire le chiavi HSM gestite per](key-management.md) la gestione delle chiavi.
- Vedere [Gestione dei ruoli HSM gestiti per](role-management.md) la gestione delle assegnazioni di ruolo.
