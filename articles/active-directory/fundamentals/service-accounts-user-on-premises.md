---
title: Protezione degli account del servizio basati su utenti | Azure Active Directory
description: Guida alla protezione degli account utente locali.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417468"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Protezione degli account del servizio basati su utenti in Active Directory

Gli account utente locali sono l'approccio tradizionale per proteggere i servizi in esecuzione in Windows. Usare questi account come ultima risorsa quando gli account del servizio gestiti globali (servizi gestiti) e gli account del servizio gestiti autonomi (sMSAs) non sono supportati dal servizio. Per informazioni sulla selezione del tipo di account migliore da usare, vedere Panoramica degli account del servizio locale. Verificare anche se è possibile spostare il servizio per usare un account del servizio di Azure, ad esempio un'identità gestita o un principio di servizio. 

È possibile creare account utente locali per fornire un contesto di sicurezza per i servizi e concedere i privilegi necessari ai servizi per accedere alle risorse locali e di rete. Richiedono la gestione manuale delle password in modo analogo a qualsiasi altro account utente Active Directory (AD). Gli amministratori del servizio e del dominio sono tenuti a osservare i processi di gestione delle password complesse per mantenere protetti questi account.

Quando si usa un account utente come account del servizio, usarlo solo per un singolo servizio. Denominarlo in modo da rendere chiaro che si tratta di un account del servizio e del servizio. 

## <a name="benefits-and-challenges"></a>Vantaggi e problemi

Vantaggi

Gli account utente locali sono il tipo di account più versatile da usare con i servizi. Gli account utente usati come account del servizio possono essere controllati da tutti i criteri che regolano gli account utente normali. Detto questo, usarli solo se non è possibile usare un MSA. Valutare inoltre se un account computer è un'opzione migliore. 

Problemi con gli account utente locali

Le seguenti richieste sono associate all'uso di account utente locali.

| Problematiche| Soluzioni di prevenzione |
| - | - |
| La gestione delle password è un processo manuale che può causare tempi di inattività del servizio e di sicurezza più vulnerabili.| Assicurarsi che la complessità delle password e la modifica della password siano regolate da un processo affidabile che garantisce aggiornamenti regolari con password complesse. <br> Coordinare la modifica della password con un aggiornamento della password nel servizio, che comporterà tempi di inattività del servizio. |
| L'identificazione degli account utente locali che fungono da account del servizio può essere difficile.| Documentare e gestire i record degli account di servizio distribuiti nell'ambiente in uso. <br> Tenere traccia del nome dell'account e delle risorse a cui viene assegnato l'accesso. <br> Si consiglia di aggiungere un prefisso di svc_ a tutti gli account utente usati come account del servizio. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Trovare gli account utente locali usati come account del servizio

Gli account utente locali sono esattamente come qualsiasi altro account utente di Active Directory. Di conseguenza, può essere difficile trovare questi account perché non esiste un singolo attributo di un account utente che lo identifica come un account del servizio. 

Si consiglia di creare una convenzione di denominazione facilmente identificabile per qualsiasi account utente utilizzato come account del servizio.

Ad esempio, aggiungere "Service-" come prefisso e denominare il servizio: "Service-HRDataConnector".

È possibile utilizzare alcuni degli indicatori seguenti per trovare questi account del servizio, tuttavia, questi account potrebbero non trovarli.

* Account attendibili per la delega.

* Account con nomi dell'entità servizio.

* Account la cui password è impostata in modo da non scadere mai.

È possibile eseguire i comandi di PowerShell seguenti per trovare gli account utente locali creati per i servizi.

### <a name="find-accounts-trusted-for-delegation"></a>Trovare gli account attendibili per la delega

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Trovare gli account con i nomi dei principi di servizio

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Trovare gli account con password impostate in modo da non scadere mai

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


È anche possibile controllare l'accesso alle risorse sensibili e archiviare i log di controllo in un sistema SIEM (Security Information and Event Management). Usando sistemi come Azure Log Analytics o Azure Sentinel, è possibile cercare e analizzare e gli account del servizio.

## <a name="assess-security-of-on-premises-user-accounts"></a>Valutare la sicurezza degli account utente locali

Valutare la sicurezza degli account utente locali usati come account di servizio usando i criteri seguenti:

* Quali sono i criteri di gestione delle password?

* L'account è membro di tutti i gruppi con privilegi?

* L'account dispone di accesso in lettura/scrittura a risorse importanti?

### <a name="mitigate-potential-security-issues"></a>Attenuare i potenziali problemi di sicurezza

La tabella seguente illustra i potenziali problemi di sicurezza e le mitigazioni corrispondenti per gli account utente locali.

| Problemi di sicurezza| Soluzioni di prevenzione |
| - | - |
| Gestione delle password|* Assicurarsi che la complessità delle password e la modifica della password siano regolate da un processo affidabile che garantisce aggiornamenti regolari con requisiti di password complesse. <br> * Modificare la password con un aggiornamento della password per ridurre al minimo i tempi di inattività del servizio. |
| L'account è un membro di gruppi con privilegi.| Controllare le appartenenze ai gruppi. Rimuovere l'account dai gruppi con privilegi. Concedere all'account solo i diritti e le autorizzazioni necessari per eseguire il servizio (consultare il fornitore del servizio). Ad esempio, potrebbe essere possibile negare l'accesso in locale o negare l'accesso interattivo. |
| L'account dispone di accesso in lettura/scrittura alle risorse sensibili.| Controllare l'accesso alle risorse sensibili. Archiviare i log di controllo in un SIEM (Azure Log Analytics o Azure Sentinel) per l'analisi. Correggere le autorizzazioni delle risorse se viene rilevato un livello di accesso indesiderato. |


## <a name="move-to-more-secure-account-types"></a>Passa a tipi di account più sicuri

Microsoft non consiglia ai clienti di usare account utente locali come account del servizio. Per tutti i servizi che usano questo tipo di account, valutare se è possibile configurarlo per l'uso di un gMSA o sMSA.

Valutare inoltre se il servizio stesso può essere spostato in Azure, in modo da poter usare più tipi di account di servizio più sicuri. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sulla protezione degli account del servizio

* [Introduzione agli account del servizio locali](service-accounts-on-premises.md)

* [Account del servizio gestito del gruppo sicuro](service-accounts-group-managed.md)

* [Proteggere gli account del servizio gestiti autonomi](service-accounts-standalone-managed.md)

* [Account computer protetti](service-accounts-computer.md)

* [Proteggere gli account utente](service-accounts-user-on-premises.md)

* [Governare gli account di servizio locali](service-accounts-govern-on-premises.md)

 
