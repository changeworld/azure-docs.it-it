---
title: Introduzione agli account del servizio Active Directory | Azure Active Directory
description: Introduzione ai tipi di account del servizio in Active Directory e come proteggerli.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645617"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Introduzione agli account del servizio Active Directory

Un servizio ha un'identità di sicurezza primaria che determina i diritti di accesso per le risorse locali e di rete. Il contesto di sicurezza per un servizio Microsoft Win32 è determinato dall'account del servizio utilizzato per avviare il servizio. Un account del servizio viene usato per:
* identificare e autenticare un servizio
* avvio di un servizio completato
* accedere o eseguire codice o un'applicazione
* avviare un processo. 

## <a name="types-of-on-premises-service-accounts"></a>Tipi di account del servizio locali

In base al caso d'uso, è possibile usare un account del servizio gestito (MSA), un account computer o un account utente per eseguire un servizio. I servizi devono essere testati per confermare che possono usare un account del servizio gestito. Se possibile, è necessario utilizzarne uno.

### <a name="group-msa-accounts"></a>Account MSA del gruppo

Usare gli [account del servizio gestito del gruppo](service-accounts-group-managed.md) (servizi gestiti) laddove possibile per i servizi in esecuzione nell'ambiente locale. Servizi gestiti offrono una singola soluzione di identità per un servizio in esecuzione in un server farm o dietro un servizio di bilanciamento del carico di rete. Possono essere usati anche per un servizio in esecuzione su un singolo server. [Servizi gestiti requisiti specifici che devono essere soddisfatti](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Account MSA autonomi

Se non è possibile usare un gMSA, usare un [account del servizio gestito autonomo](service-accounts-standalone-managed.md)(SMSA). sMSAs richiede almeno Windows Server 2008R2. A differenza di servizi gestiti, sMSAs viene eseguito solo in un server. Possono essere usati per più servizi nel server.

### <a name="computer-account"></a>Account del computer

Se non è possibile usare un account del servizio gestito, esaminare l'uso di un [account computer](service-accounts-computer.md). L'account LocalSystem è un account locale predefinito che dispone di privilegi ampi sul computer locale e funge da identità del computer in rete.   
I servizi eseguiti come account LocalSystem accedono a una risorsa di rete utilizzando le credenziali dell'account del computer nel formato <domain_name>\<computer_name> .

NT AUTHORITY\SYSTEM è il nome predefinito per l'account LocalSystem. Può essere usato per avviare un servizio e fornire il contesto di sicurezza per il servizio.

> [!NOTE]
> Quando si utilizza un account computer, non è possibile stabilire quale servizio del computer utilizza tale account e pertanto non è in grado di controllare il servizio che sta apportando modifiche. 

### <a name="user-account"></a>Account utente

Se non è possibile usare un account del servizio gestito, provare a usare gli [account utente](service-accounts-user-on-premises.md). Gli account utente possono essere un account utente di dominio o un account utente locale.

Un account utente di dominio consente al servizio di sfruttare al meglio le funzionalità di sicurezza del servizio di Windows e Microsoft Active Directory Domain Services. Il servizio disporrà dell'accesso locale e di rete concesso all'account. Disporrà inoltre delle autorizzazioni di tutti i gruppi di cui l'account è membro. Gli account del servizio del dominio supportano l'autenticazione reciproca Kerberos.

Un account utente locale (formato nome: ".\UserName") esiste solo nel database SAM del computer host; non dispone di un oggetto utente in Active Directory Domain Services. Un account locale non può essere autenticato dal dominio. Pertanto, un servizio che viene eseguito nel contesto di sicurezza di un account utente locale non ha accesso alle risorse di rete, ad eccezione di un utente anonimo. I servizi in esecuzione nel contesto utente locale non possono supportare l'autenticazione reciproca Kerberos in cui il servizio viene autenticato dai relativi client. Per questi motivi, gli account utente locali non sono in genere appropriati per i servizi abilitati all'uso di directory.

> [!IMPORTANT]
> Gli account del servizio non devono essere membri di alcun gruppo con privilegi, in quanto l'appartenenza a gruppi con privilegi può costituire un rischio per la sicurezza. Ogni servizio deve disporre di un proprio account di servizio a scopo di controllo e sicurezza.

## <a name="choose-the-right-type-of-service-account"></a>Scegliere il tipo di account di servizio corretto


| Criteri| gMSA| sMSA| Account del computer| Account utente |
| - | - | - | - | - |
| L'app viene eseguita in un singolo server| Sì| Sì. Usare un gMSA se possibile| Sì. Usare un account del servizio gestito (MSA) se possibile| Sì. Se possibile, usare MSA. |
| L'app viene eseguita su più server| Sì| No| No. L'account è associato al server| Sì. Se possibile, usare MSA. |
| L'app viene eseguita dietro i bilanciamenti del carico| Sì| No| No| Sì. Usare solo se non è possibile usare un gMSA |
| App in esecuzione in Windows Server 2008 R2| No| Sì| Sì. Se possibile, usare MSA.| Sì. Se possibile, usare MSA. |
| Viene eseguito in Windows Server 2012| Sì| Sì. Usare gMSA se possibile| Sì. Usare MSA se possibile| Sì. Se possibile, usare MSA. |
| Requisito per limitare l'account del servizio a un singolo server| No| Sì| Sì. Usare sMSA se possibile| No. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Usare i log del server e PowerShell per esaminare

È possibile utilizzare i log del server per determinare i server e il numero di server su cui è in esecuzione un'applicazione.

È possibile eseguire il comando di PowerShell seguente per ottenere un elenco della versione di Windows Server per tutti i server della rete. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Trovare gli account di servizio locali

Si consiglia di aggiungere un prefisso, ad esempio "svc". A tutti gli account utilizzati come account del servizio. Questa convenzione di denominazione renderà più semplice trovare e gestire. Considerare inoltre l'utilizzo di un attributo Description per l'account del servizio e il proprietario dell'account del servizio, può trattarsi di un alias del team o di un proprietario del team di sicurezza.

Trovare gli account di servizio locali è fondamentale per garantirne la sicurezza. E può essere difficile per gli account non MSA. È consigliabile esaminare tutti gli account che hanno accesso alle risorse locali importanti e determinare quali account computer o utente possono fungere da account del servizio. È anche possibile usare i metodi seguenti per trovare gli account.

* Gli articoli per ogni tipo di account includono i passaggi dettagliati per individuare il tipo di account. Per i collegamenti a questi articoli, vedere la sezione passaggi successivi di questo articolo.

## <a name="document-service-accounts"></a>Account del servizio documenti

Una volta trovati gli account del servizio nell'ambiente locale, documentare le informazioni seguenti su ogni account. 

* Proprietario. Persona responsabile per la gestione dell'account.

* Scopo. Applicazione rappresentata dall'account o da altri scopi. 

* Ambiti di autorizzazione. Quali sono le autorizzazioni di cui dispone e che dovrebbero avere? Che cosa accade se è membro di un gruppo?

* Profilo di rischio. Qual è il rischio per l'azienda se questo account viene compromesso? Se il rischio è elevato, usare un MSA.

* Durata prevista e attestazione periodica. Per quanto tempo si prevede che questo account sia attivo? Con quale frequenza è necessario rivedere il proprietario e attestare la necessità di continuare?

* Sicurezza delle password. Per gli account utente e computer locale in cui è archiviata la password. Assicurarsi che le password vengano mantenute protette e che i documenti abbiano accesso. Prendere in considerazione l'uso di [Privileged Identity Management](../privileged-identity-management/pim-configure.md) per proteggere le password archiviate. 

  

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione degli account del servizio

* [Introduzione agli account del servizio locali](service-accounts-on-premises.md)

* [Account del servizio gestito del gruppo sicuro](service-accounts-group-managed.md)

* [Proteggere gli account del servizio gestiti autonomi](service-accounts-standalone-managed.md)

* [Account computer protetti](service-accounts-computer.md)

* [Proteggere gli account utente](service-accounts-user-on-premises.md)

* [Governare gli account di servizio locali](service-accounts-govern-on-premises.md)

