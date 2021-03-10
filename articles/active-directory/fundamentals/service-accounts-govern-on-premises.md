---
title: Governare gli account di servizio locali | Azure Active Directory
description: Guida alla creazione e all'esecuzione di un processo del ciclo di vita dell'account per gli account del servizio
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
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557370"
---
# <a name="governing-on-premises-service-accounts"></a>Governare gli account di servizio locali

Sono disponibili quattro tipi di account di servizio locali in Windows Active Directory:

* [Account del servizio gestito del gruppo](service-accounts-group-managed.md) (servizi gestiti)

* [account del servizio gestito autonomi](service-accounts-standalone-managed.md) (sMSAs)

* [Account computer](service-accounts-computer.md)

* [Account utente che funzionano come account del servizio](service-accounts-user-on-premises.md)


È fondamentale controllare attentamente gli account del servizio per: 

* Proteggere gli account di servizio in base ai requisiti e allo scopo del caso d'uso.

* Gestire il ciclo di vita degli account del servizio e le relative credenziali.

* Valutare gli account del servizio in base al rischio che verranno esposti e alle autorizzazioni che contengono, 

* Assicurarsi che Active Directory e Azure Active Directory non dispongano di account di servizio non aggiornati con autorizzazioni potenzialmente di lunga portata.

## <a name="principles-for-creating-a-new-service-account"></a>Principi per la creazione di un nuovo account del servizio

Utilizzare i criteri seguenti durante la creazione di un nuovo account del servizio.

| Principi| Considerazioni | 
| - |- | 
| Mapping degli account del servizio| Associare l'account del servizio a un singolo servizio, applicazione o script. |
| Proprietario| Assicurarsi che sia presente un proprietario che richiede e assuma la responsabilità per l'account. |
| Ambito| Definire l'ambito in modo chiaro e prevedere la durata dell'utilizzo per l'account del servizio. |
| Scopo| Creare account del servizio per un singolo scopo specifico. |
| Privilege| Applicare il principio dei privilegi minimi per: <br>Non assegnarli mai a gruppi predefiniti come gli amministratori.<br> Rimuovere i privilegi del computer locale laddove appropriato.<br>Personalizzazione dell'accesso e uso della delega Active Directory per l'accesso alla directory.<br>Uso delle autorizzazioni di accesso granulare.<br>Impostazione delle scadenze degli account e delle restrizioni basate sulla posizione per gli account del servizio basato su utente |
| Utilizzo di monitoraggio e controllo| Monitorare i dati di accesso e verificare che corrispondano all'utilizzo previsto. Impostare gli avvisi per l'utilizzo anomalo. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Applicare il privilegio minimo per gli account utente e limitare l'utilizzo eccessivo dell'account

Usare le impostazioni seguenti con gli account utente usati come account del servizio:

* [**Scadenza dell'account**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): impostare l'account del servizio in modo che scada automaticamente un set di tempo dopo il periodo di verifica, a meno che non sia stato determinato che dovrebbe continuare

*  **LogonWorkstations**: limitare le autorizzazioni per la posizione in cui l'account del servizio può accedere. Se viene eseguito localmente in un computer e accede solo a risorse in tale computer, limitarlo alla registrazione in qualsiasi altra posizione.

* [**Impossibile modificare la password**](/powershell/module/addsadministration/set-aduser): evitare che l'account del servizio modifichi la propria password impostando il parametro su false.

 
## <a name="build-a-lifecycle-management-process"></a>Creazione di un processo di gestione del ciclo di vita

Per mantenere la sicurezza degli account del servizio, è necessario gestirli dal momento in cui si identifica la necessità fino a quando non vengono rimosse le autorizzazioni. 

Usare il processo seguente per la gestione del ciclo di vita degli account del servizio:

1. Raccogliere le informazioni sull'utilizzo per l'account
1. Eseguire l'onboarding dell'account del servizio e dell'app nel database di gestione della configurazione (CMDB)
1. Eseguire la valutazione del rischio o la revisione formale
1. Creare l'account del servizio e applicare restrizioni.
1. Pianificare ed eseguire revisioni ricorrenti. Modificare le autorizzazioni e gli ambiti secondo le esigenze.
1. Annullare il provisioning dell'account quando appropriato.

### <a name="collect-usage-information-for-the-service-account"></a>Raccogliere le informazioni sull'utilizzo per l'account del servizio

Raccogliere le informazioni aziendali rilevanti per ogni account del servizio. Nella tabella seguente sono riportate le informazioni minime da raccogliere, ma è necessario raccogliere tutti gli elementi necessari per creare il business case per l'esistenza degli account.

| Dati| Dettagli |
| - | - |
| Proprietario| Utente o gruppo che è responsabile dell'account del servizio |
| Scopo| Scopo dell'account del servizio |
| Autorizzazioni (ambiti)| Set di autorizzazioni previsto |
| Collegamenti al database di gestione della configurazione (CMDB)| Account del servizio cross-link con script di destinazione/applicazione e proprietari |
| Rischio| Punteggio di rischio e impatto aziendale basato sulla valutazione dei rischi di sicurezza |
| Durata| Durata massima prevista per consentire la pianificazione della scadenza o della ricertificazione degli account |


 

Idealmente, effettuare la richiesta di un account Self-Service e richiedere le informazioni rilevanti. Proprietario, che può essere un'applicazione o un proprietario aziendale, un membro IT o un proprietario dell'infrastruttura. L'uso di uno strumento come Microsoft Forms per questa richiesta e le informazioni associate ne semplificano la portabilità allo strumento di inventario CMDB se l'account è approvato.

### <a name="onboard-service-account-to-cmdb"></a>Eseguire l'onboarding dell'account del servizio in CMDB

Archiviare le informazioni raccolte in un'applicazione di tipo CMDB. Oltre alle informazioni aziendali, includere tutte le dipendenze con altre infrastrutture, app e processi.  Questo repository centrale renderà più semplice:

* Valutare i rischi.

* Configurare l'account del servizio con restrizioni obbligatorie.

* Informazioni sulle dipendenze funzionali e di sicurezza pertinenti.

* Eseguire verifiche regolari per la sicurezza e la necessità continua.

* Contattare i proprietari per la revisione, il ritiro e la modifica dell'account del servizio.

Si consideri un account del servizio utilizzato per eseguire un sito Web e disponga dei privilegi per connettersi a uno o più database SQL. Le informazioni archiviate in CMDB per questo account del servizio potrebbero essere:

|Dati | Dettagli|
| - | - |
| Proprietario, Vice| John Bloom, Anna Mayers |
| Scopo| Eseguire la pagina Web HR e connettersi a HR-databases. Può rappresentare l'utente finale durante l'accesso ai database. |
| Autorizzazioni, ambiti| HR-WebServer: accesso locale, esecuzione di una pagina Web<br>HR-SQL1: accesso locale, lettura in tutte le risorse umane * database<br>HR-SQL2: accesso locale, lettura su STIPENDIo * database |
| Centro di costo| 883944 |
| Valutazione del rischio| Media Effetto aziendale: medio; informazioni private; Media |
| Limitazioni dell'account| Accesso a: solo server menzionati; Impossibile modificare la password. Criteri di MBI-Password; |
| Durata| illimitato |
| Ciclo di Revisione| Bi-annuale (dal proprietario, dal team di sicurezza, dalla privacy) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Eseguire la valutazione dei rischi o la verifica formale dell'utilizzo dell'account del servizio

Date le autorizzazioni e lo scopo, valutare il rischio che l'account possa presentare all'applicazione o al servizio associato e all'infrastruttura in caso di compromissione. Considerare i rischi diretti e indiretti. 

* Che cosa farebbe un antagonista a ottenere l'accesso diretto?

* Quali altre informazioni o sistemi possono accedere all'account del servizio?

* È possibile utilizzare l'account per concedere autorizzazioni aggiuntive?

* Come si saprà quando le autorizzazioni cambiano?

La valutazione dei rischi, una volta eseguita e documentata, potrebbe avere un impatto su:

* Restrizioni per gli account

* Durata dell'account

* Requisiti per la revisione degli account (cadenza e revisori)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Creare un account del servizio e applicare le restrizioni degli account

Creare un account del servizio solo dopo aver documentato le informazioni rilevanti nella CMDB ed eseguire una valutazione dei rischi. Le restrizioni degli account devono essere allineate alla valutazione del rischio. Quando pertinente per la valutazione, tenere presenti le restrizioni seguenti:

* [Scadenza dell'account](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * Per tutti gli account utente usati come account del servizio, definire una data di fine realistica e definita da usare. Impostare questa opzione con il flag "scadenza account". Per ulteriori informazioni, vedere[ set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration). 

* Accedi a ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* Requisiti dei [criteri password](../../active-directory-domain-services/password-policy.md)

* Creazione in un [percorso dell'unità organizzativa](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) che garantisce la gestione solo per gli utenti con privilegi

* Configurare e raccogliere il controllo per [rilevare le modifiche apportate](/windows/security/threat-protection/auditing/audit-directory-service-changes) all'account del servizio e [usare l'account del servizio](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Quando si è pronti per l'immissione in produzione, concedere l'accesso all'account del servizio in modo sicuro. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Pianificare verifiche periodiche degli account del servizio

Configurare le verifiche periodiche degli account del servizio classificati come media e ad alto rischio. Le revisioni devono includere: 

* Attestazione del proprietario per la necessità continua dell'account e la giustificazione dei privilegi e degli ambiti.

* Esaminare i team per la privacy e la sicurezza, inclusa la valutazione delle connessioni upstream e downstream.

* I dati dei controlli assicurano che vengano utilizzati solo a scopo designato

### <a name="deprovision-service-accounts"></a>Effettuare il deprovisioning degli account del servizio

Nel processo di deprovisioning rimuovere prima le autorizzazioni e il monitoraggio, quindi rimuovere l'account, se necessario.

Effettuare il deprovisioning degli account del servizio quando:

* Lo script o l'applicazione per cui è stato creato l'account di servizio è stato ritirato.

* La funzione all'interno dello script o dell'applicazione, per cui viene usato l'account del servizio, ad esempio l'accesso a una risorsa specifica, viene ritirata.

* L'account del servizio è stato sostituito con un account del servizio diverso.

Dopo aver rimosso tutte le autorizzazioni, usare questo processo per rimuovere l'account.

1. Una volta effettuato il deprovisioning dell'applicazione o dello script associato, monitorare gli accessi e le risorse per gli account del servizio associati per assicurarsi che non vengano usati in un altro processo. Se si è certi che non è più necessario, andare al passaggio successivo.

2. Disabilitare l'accesso dell'account del servizio e assicurarsi che non sia più necessario. Creare un criterio di business per gli account temporali che devono rimanere disabilitati.

3. Eliminare l'account del servizio dopo che il criterio resta disabilitato viene completato. 

   * Per MSAs, è possibile [disinstallarlo](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) usando PowerShell o eliminarlo manualmente dal contenitore account del servizio gestito.

   * Per gli account computer o utente, è possibile eliminare manualmente l'account da in Active Directory.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sulla protezione degli account del servizio

* [Introduzione agli account del servizio locali](service-accounts-on-premises.md)

* [Account del servizio gestito del gruppo sicuro](service-accounts-group-managed.md)

* [Proteggere gli account del servizio gestiti autonomi](service-accounts-standalone-managed.md)

* [Account computer protetti](service-accounts-computer.md)

* [Proteggere gli account utente](service-accounts-user-on-premises.md)

* [Governare gli account di servizio locali](service-accounts-govern-on-premises.md)