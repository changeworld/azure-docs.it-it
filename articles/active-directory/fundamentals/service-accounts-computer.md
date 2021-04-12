---
title: Protezione degli account computer | Azure Active Directory
description: Guida alla protezione degli account computer locali.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417511"
---
# <a name="securing-computer-accounts"></a>Protezione degli account computer

L'account computer o l'account LocalSystem è un account predefinito con privilegi elevati che consente di accedere a praticamente tutte le risorse nel computer locale. Questo account non è associato ad alcun account utente connesso. I servizi in esecuzione come LocalSystem accedono alle risorse di rete presentando le credenziali del computer ai server remoti. Presenta le credenziali nel formato <domain_name>\<computer_name> $. Il nome predefinito di un account computer è NT AUTHORITY\SYSTEM. Può essere usato per avviare un servizio e fornire il contesto di sicurezza per il servizio.

![[Immagine 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Vantaggi dell'utilizzo dell'account computer

L'account computer offre i seguenti vantaggi.

* **Accesso locale senza restrizioni**: l'account computer fornisce l'accesso completo alle risorse locali del computer.

* **Gestione automatica delle password**: l'account computer Elimina la necessità di modificare manualmente le password. Questo account è invece membro del Active Directory e la password dell'account viene modificata automaticamente. Elimina inoltre la necessità di registrare il nome dell'entità servizio per il servizio.

* **Diritti di accesso limitati all'esterno del computer**: l'elenco di controllo di accesso (ACL) predefinito in Active Directory Domain Services consente l'accesso minimo per gli account computer. Se il servizio venisse hackerato, avrebbe accesso limitato alle risorse della rete.

## <a name="assess-security-posture-of-computer-accounts"></a>Valutare il comportamento di sicurezza degli account computer

Potenziali problemi e mitigazioni associate quando si usano gli account computer. 

| Problemi| Soluzioni di prevenzione |
| - | - |
| Gli account computer sono soggetti a eliminazione e ricreazione quando il computer esce e si riunisce al dominio.| Convalidare la necessità di aggiungere un computer a un gruppo di Active Directory e verificare quale account computer è stato aggiunto a un gruppo usando gli script di esempio forniti in questa pagina.| 
| Se si aggiunge un account computer a un gruppo, a tutti i servizi in esecuzione come LocalSystem nel computer vengono assegnati i diritti di accesso del gruppo.| Essere selettivi delle appartenenze ai gruppi dell'account computer. Evitare di rendere gli account computer membri di qualsiasi gruppo di amministratori di dominio perché il servizio associato ha accesso completo ai Active Directory Domain Services. |
| Impostazioni predefinite di rete non corrette per LocalSystem| Non presupporre che l'account computer disponga dell'accesso limitato predefinito alle risorse di rete. Al contrario, controllare attentamente le appartenenze ai gruppi per questo account. |
| Servizi sconosciuti in esecuzione come LocalSystem| Assicurarsi che tutti i servizi in esecuzione con l'account LocalSystem siano servizi Microsoft o servizi attendibili di terze parti. |


## <a name="find-services-running-under-the-computer-account"></a>Trovare i servizi in esecuzione con l'account computer

Usare il cmdlet di PowerShell seguente per trovare i servizi in esecuzione nel contesto LocalSystem

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Trovare gli account computer membri di un gruppo specifico**

Usare il cmdlet di PowerShell seguente per trovare gli account computer che sono membri di un gruppo specifico.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Trovare gli account computer che sono membri di gruppi con privilegi**

Usare il cmdlet di PowerShell seguente per trovare gli account computer membri dei gruppi Identity Administrators (Domain Admins, Enterprise Admins, Administrators)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Sposta da account computer

> [!IMPORTANT]
> Gli account computer sono account con privilegi elevati e devono essere usati solo quando il servizio necessita di un accesso illimitato alle risorse locali nel computer e non è possibile usare un account del servizio gestito (MSA).

* Verificare con il proprietario del servizio se il servizio può essere eseguito con un account del servizio gestito e usare un account del servizio gestito del gruppo (gMSA) o un account del servizio gestito autonomo (sMSA) se il servizio lo supporta.

* Usare un account utente di dominio con solo i privilegi necessari per eseguire il servizio.

## <a name="next-steps"></a>Passaggi successivi 

Vedere gli articoli seguenti sulla protezione degli account del servizio

* [Introduzione agli account del servizio locali](service-accounts-on-premises.md)

* [Account del servizio gestito del gruppo sicuro](service-accounts-group-managed.md)

* [Proteggere gli account del servizio gestiti autonomi](service-accounts-standalone-managed.md)

* [Account computer protetti](service-accounts-computer.md)

* [Proteggere gli account utente](service-accounts-user-on-premises.md)

* [Governare gli account di servizio locali](service-accounts-govern-on-premises.md)

 

 
