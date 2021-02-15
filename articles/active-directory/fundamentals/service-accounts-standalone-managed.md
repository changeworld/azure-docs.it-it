---
title: Protezione degli account dei servizi gestiti autonomi | Azure Active Directory
description: Guida alla protezione degli account dei servizi gestiti autonomi.
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
ms.openlocfilehash: 08a919338789a02d50cbb6976ee50b214cb0d612
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417471"
---
# <a name="securing-standalone-managed-service-accounts"></a>Protezione degli account dei servizi gestiti autonomi

Gli account del servizio gestiti autonomi (sMSAs) sono account di dominio gestiti usati per proteggere uno o più servizi in esecuzione in un server. Non possono essere riutilizzati in più server. sMSAs forniscono la gestione automatica delle password, la gestione semplificata del nome dell'entità servizio (SPN) e la possibilità di delegare la gestione ad altri amministratori. 

In Active Directory, sMSAs sono collegati a un server specifico che esegue un servizio. È possibile trovare questi account elencati nello snap-in utenti e computer di Active Directory di Microsoft Management Console.

![Schermata dello snap-in Active Directory utenti e computer che illustra l'unità organizzativa account del servizio gestito.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Gli account del servizio gestiti sono stati introdotti con Windows Server 2008R2 Active Directory schema e richiedono un livello minimo del sistema operativo di Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>Vantaggi dell'uso di sMSAs

sMSAs offrono una maggiore sicurezza rispetto agli account utente usati come account del servizio, riducendo al tempo stesso il sovraccarico amministrativo:

* Impostazione di password complesse. sMSAs utilizzano password complesse generate in modo casuale 240 byte. La complessità e la lunghezza delle password di sMSA riducono al minimo la probabilità che un servizio venga compromesso dagli attacchi di forza bruta o del dizionario.

* Ciclare regolarmente le password. Windows modifica automaticamente la password di sMSA ogni 30 giorni. Gli amministratori del servizio e del dominio non devono pianificare le modifiche della password o gestire i tempi di inattività associati.

* Semplificazione della gestione del nome SPN. I nomi dell'entità servizio vengono aggiornati automaticamente se il livello di funzionalità del dominio (DFL) è Windows Server 2008 R2. Ad esempio, il nome dell'entità servizio viene aggiornato automaticamente negli scenari seguenti:

   * L'account computer host è stato rinominato. 

   * Il nome DNS del computer host è stato modificato.

   * Quando si aggiungono o si rimuovono parametri Sam-AccountName o DNS-hostname aggiuntivi tramite [PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-adserviceaccount?view=win10-ps)

## <a name="when-to-use-smsas"></a>Quando usare sMSAs

sMSAs può semplificare le attività di gestione e sicurezza. Utilizzare sMSAs quando si dispone di uno o più servizi distribuiti in un singolo server e non è possibile utilizzare un gMSA. 

> [!NOTE] 
> Sebbene sia possibile utilizzare sMSAs per più di un servizio, è consigliabile che ogni servizio disponga di una propria identità a scopo di controllo. 

Se l'autore del software non è in grado di indicare se può utilizzare un MSA, è necessario testare l'applicazione. A tale scopo, creare un ambiente di testing e verificare che possa accedere a tutte le risorse necessarie. Per istruzioni dettagliate, vedere [creare e installare un SMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) .

### <a name="assess-security-posture-of-smsas"></a>Valutare il comportamento di sicurezza di sMSAs

sMSAs sono intrinsecamente più sicure degli account utente standard, che richiedono la gestione delle password in corso. Tuttavia, è importante considerare l'ambito di accesso di sMSAs come parte del comportamento di sicurezza complessivo.

La tabella seguente illustra come attenuare i potenziali problemi di sicurezza posti da sMSAs.

| Problemi di sicurezza| Soluzioni di prevenzione |
| - | - |
| sMSA è un membro di gruppi con privilegi|Rimuovere sMSA da gruppi con privilegi elevati, ad esempio Domain Admins. <br> Usare il modello con privilegi minimi e concedere a sMSA solo i diritti e le autorizzazioni necessarie per eseguire i servizi. <br> Se non si è certi delle autorizzazioni necessarie, consultare l'autore del servizio. |
| sMSA dispone di accesso in lettura/scrittura alle risorse sensibili.|Controllare l'accesso alle risorse sensibili. Archiviare i log di controllo in un SIEM (Azure Log Analytics o Azure Sentinel) per l'analisi. <br> Correggere le autorizzazioni delle risorse se viene rilevato un livello di accesso indesiderato. |
| Per impostazione predefinita, la frequenza di rollover della password di sMSA è 30 giorni| È possibile utilizzare criteri di gruppo per ottimizzare la durata a seconda dei requisiti di sicurezza aziendali. <br> * È possibile impostare la durata della scadenza della password usando il percorso seguente. <br>Computer Computer\criteri\impostazioni Windows\Impostazioni di Windows\Impostazioni Options\Domain membro: validità massima password account computer |



### <a name="challenges-with-smsas"></a>Problemi con sMSAs

I problemi associati a sMSAs sono i seguenti:

| Problematiche| Soluzioni di prevenzione |
| - | - |
| Possono essere usati in un singolo server.| Usare servizi gestiti se è necessario usare l'account tra server. |
| Non possono essere usati tra domini.| Usare servizi gestiti se è necessario usare l'account tra domini. |
| Non tutte le applicazioni supportano sMSAs.| Se possibile, utilizzare servizi gestiti. Se non si utilizza un account utente standard o un account computer come consigliato dall'autore dell'applicazione. |


## <a name="find-smsas"></a>Trova sMSAs

In qualsiasi controller di dominio eseguire DSA. msc ed espandere il contenitore account del servizio gestiti per visualizzare tutti i sMSAs. 

Il comando di PowerShell seguente restituisce tutti sMSAs e servizi gestiti nel dominio Active Directory. 

`Get-ADServiceAccount -Filter *`

Il comando seguente restituisce solo sMSAs nel dominio Active Directory.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Gestisci sMSAs

Per la gestione di sMSAs è possibile usare i cmdlet di PowerShell seguenti Active Directory:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Passa a sMSAs

Se un servizio dell'applicazione supporta sMSA ma non servizi gestiti e attualmente usa un account utente o un account computer per il contesto di sicurezza, [creare e installare un SMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) nel server. 

Idealmente, spostare le risorse in Azure e usare le identità gestite di Azure o le entità servizio.

 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sulla protezione degli account del servizio

* [Introduzione agli account del servizio locali](service-accounts-on-premises.md)

* [Account del servizio gestito del gruppo sicuro](service-accounts-group-managed.md)

* [Proteggere gli account del servizio gestiti autonomi](service-accounts-standalone-managed.md)

* [Account computer protetti](service-accounts-computer.md)

* [Proteggere gli account utente](service-accounts-user-on-premises.md)

* [Governare gli account di servizio locali](service-accounts-govern-on-premises.md)

 
