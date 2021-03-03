---
title: Protezione degli account del servizio gestiti del gruppo | Azure Active Directory
description: Guida alla protezione degli account dei computer degli account del servizio gestiti del gruppo.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644828"
---
# <a name="securing-group-managed-service-accounts"></a>Protezione degli account del servizio gestiti del gruppo

Gli account del servizio gestito del gruppo (servizi gestiti) sono account di dominio gestiti usati per la protezione dei servizi. Servizi gestiti può essere eseguito su un singolo server o in un server farm, ad esempio sistemi protetti da un Load Balancer di rete (NLB) o un server Internet Information Services (IIS). Una volta configurati i servizi per l'uso di un'entità gMSA, la gestione delle password per tale account viene gestita da Windows.

## <a name="benefits-of-using-gmsas"></a>Vantaggi dell'uso di servizi gestiti

Servizi gestiti offrono una soluzione di gestione delle identità unica con maggiore sicurezza, riducendo al tempo stesso il sovraccarico amministrativo:

* **Impostazione di password complesse**. Servizi gestiti utilizzano password complesse generate in modo casuale 240 byte. La complessità e la lunghezza delle password di gMSA riducono al minimo la probabilità che un servizio venga compromesso dagli attacchi di forza bruta o del dizionario.

* **Ciclare regolarmente le password**. Servizi gestiti sposta la gestione delle password a Windows, che modifica la password ogni 30 giorni. Gli amministratori del servizio e del dominio non devono più pianificare le modifiche delle password o gestire le interruzioni del servizio per garantire la protezione degli account del servizio. 

* **Supporto della distribuzione nelle server farm**. La possibilità di distribuire servizi gestiti a più server consente il supporto di soluzioni con bilanciamento del carico in cui più host eseguono lo stesso servizio. 

* **Supporto della gestione semplificata del nome dell'entità server (SPN)**. È possibile configurare SPN usando PowerShell al momento della creazione dell'account. Inoltre, i servizi che supportano le registrazioni del nome SPN automatico possono eseguire questa operazione in base a gMSA, le autorizzazioni gMSA specificate sono impostate correttamente. 

## <a name="when-to-use-gmsas"></a>Quando usare servizi gestiti

Usare servizi gestiti come tipo di account preferito per i servizi locali a meno che un servizio, ad esempio il clustering di failover, non lo supporti.

> [!IMPORTANT]
> È necessario testare il servizio con servizi gestiti prima della distribuzione nell'ambiente di produzione. A tale scopo, configurare un ambiente di testing e assicurarsi che l'applicazione possa usare il gMSA e accedere alle risorse necessarie per l'accesso. Per ulteriori informazioni, vedere [supporto per gli account del servizio gestiti del gruppo](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019).


Se un servizio non supporta l'uso di servizi gestiti, l'opzione migliore successiva consiste nell'usare un account del servizio gestito autonomo (sMSA). sMSAs offrono le stesse funzionalità di un gMSA, ma sono destinate alla distribuzione solo in un singolo server.

Se non è possibile usare gMSA o sMSA è supportato dal servizio, è necessario che il servizio sia configurato per l'esecuzione come account utente standard. Gli amministratori del servizio e del dominio sono tenuti a osservare i processi di gestione delle password complesse per mantenere sicuro l'account.

## <a name="assess-the-security-posture-of-gmsas"></a>Valutare il comportamento di sicurezza di servizi gestiti

Servizi gestiti sono intrinsecamente più sicure degli account utente standard, che richiedono la gestione delle password in corso. Tuttavia, è importante considerare l'ambito di accesso di servizi gestiti quando si esamina il comportamento di sicurezza complessivo.

La tabella seguente illustra i potenziali problemi di sicurezza e le mitigazioni per l'uso di servizi gestiti.

| Problemi di sicurezza| Soluzioni di prevenzione |
| - | - |
| gMSA è un membro di gruppi con privilegi. | Controllare le appartenenze ai gruppi. A tale scopo, è possibile creare uno script di PowerShell per enumerare tutte le appartenenze ai gruppi, quindi filtrare un file CSV risultante in base ai nomi dei file gMSA. <br>Rimuovere gMSA da gruppi con privilegi.<br> Concedere a gMSA solo i diritti e le autorizzazioni necessari per eseguire il servizio (consultare il fornitore del servizio). 
| gMSA dispone di accesso in lettura/scrittura alle risorse sensibili. | Controllare l'accesso alle risorse sensibili. Archiviare i log di controllo in un SIEM, ad esempio Azure Log Analytics o Azure Sentinel, per l'analisi. Rimuovere le autorizzazioni per le risorse non necessarie se viene rilevato un livello di accesso indesiderato. |


## <a name="find-gmsas"></a>Trova servizi gestiti

È possibile che l'organizzazione abbia già creato servizi gestiti. Eseguire il cmdlet di PowerShell seguente per recuperare questi account:

Per lavorare in modo efficace, servizi gestiti deve trovarsi nell'unità organizzativa degli account dei servizi gestiti.

  
![Screenshot della OU dell'account del servizio gestito.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Per trovare MSAs di servizio che potrebbero non essere presenti, vedere i comandi seguenti.

**Per trovare tutti gli account di servizio, inclusi servizi gestiti e sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>Gestisci servizi gestiti

Per la gestione di servizi gestiti è possibile usare i cmdlet di PowerShell seguenti Active Directory:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> A partire da Windows Server 2012, i cmdlet *-ADServiceAccount funzionano con servizi gestiti per impostazione predefinita. Per ulteriori informazioni sull'utilizzo dei cmdlet precedenti, vedere [**Introduzione con gli account del servizio gestiti del gruppo**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Passare a un gMSA
Servizi gestiti sono il tipo di account del servizio più sicuro per le esigenze locali. Se è possibile passare a uno, è necessario. Inoltre, è consigliabile trasferire i servizi in Azure e gli account di servizio in Azure Active Directory.

1.  Verificare che la [chiave radice KDS sia distribuita nella foresta](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Si tratta di un'operazione una tantum.

2. [Creare un nuovo gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Installare il nuovo gMSA in ogni host che esegue il servizio.
   > [!NOTE] 
   > Per altre informazioni sulla creazione e l'installazione di gMSA in un host, prima di configurare il servizio per l'uso di gMSA, vedere [Introduzione con gli account del servizio gestiti del gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. Modificare l'identità del servizio in gMSA e specificare una password vuota.

5. Verificare che il servizio funzioni con la nuova identità gMSA.

6. Eliminare l'identità dell'account del servizio precedente.

 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sulla protezione degli account del servizio

* [Introduzione agli account del servizio locali](service-accounts-on-premises.md)

* [Account del servizio gestito del gruppo sicuro](service-accounts-group-managed.md)

* [Proteggere gli account del servizio gestiti autonomi](service-accounts-standalone-managed.md)

* [Account computer protetti](service-accounts-computer.md)

* [Proteggere gli account utente](service-accounts-user-on-premises.md)

* [Governare gli account di servizio locali](service-accounts-govern-on-premises.md)