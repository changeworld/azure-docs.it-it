---
title: Gestione degli account del servizio Azure Active Directory
description: Principi e procedure per la gestione del ciclo di vita degli account del servizio in Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4311d0acc7c417bf31c71f46e6c25c65312b894d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034533"
---
# <a name="governing-azure-ad-service-accounts"></a>Gestione degli account del servizio Azure AD

Esistono tre tipi di account del servizio in Azure Active Directory (Azure AD): [identità gestite](service-accounts-managed-identities.md), [entità servizio](service-accounts-principal.md)e account utente utilizzati come account del servizio. Quando si creano questi account del servizio per l'uso automatizzato, vengono concesse le autorizzazioni per accedere alle risorse in Azure e Azure AD. Le risorse possono includere Microsoft 365 servizi, applicazioni Software as a Service (SaaS), applicazioni personalizzate, database, sistemi HR e così via. Governare Azure AD account del servizio significa gestire la creazione, le autorizzazioni e il ciclo di vita per garantire sicurezza e continuità.

> [!IMPORTANT] 
> Non è consigliabile usare account utente come account del servizio perché sono intrinsecamente meno sicuri. Sono inclusi gli account di servizio locali sincronizzati con Azure AD, perché non vengono convertiti in entità servizio. È invece consigliabile usare identità gestite o entità servizio. Si noti che in questo momento l'uso dei criteri di accesso condizionale non è possibile con le entità servizio, ma la funzionalità è in arrivo.


## <a name="plan-your-service-account"></a>Pianificare l'account del servizio

Prima di creare un account del servizio o di registrare un'applicazione, documentare le informazioni sulla chiave dell'account del servizio. La presenza di informazioni documentate rende più semplice monitorare e governare efficacemente l'account. Si consiglia di raccogliere i dati seguenti e di tenerne traccia nel database di gestione della configurazione centralizzata (CMDB).

| Data| Descrizione| Dettagli |
| - | - | - |
| Proprietario| Utente o gruppo che è responsabile per la gestione e il monitoraggio dell'account del servizio.| Effettuare il provisioning del proprietario con le autorizzazioni necessarie per monitorare l'account e implementare una soluzione per attenuare i problemi. La mitigazione dei problemi può essere eseguita dal proprietario o tramite una richiesta. |
| Scopo| Come verrà usato l'account.| Eseguire il mapping dell'account del servizio a un servizio, un'applicazione o uno script specifico. Evitare di creare account del servizio multiutilizzo. |
| Autorizzazioni (ambiti)| Set previsto di autorizzazioni.| Documentare le risorse a cui sarà possibile accedere e le autorizzazioni per tali risorse. |
| Collegamento CMDB| Collegamento alle risorse a cui accedere e agli script in cui viene usato l'account del servizio.| Assicurarsi di documentare i proprietari delle risorse e degli script in modo che sia possibile comunicare gli effetti upstream e downstream necessari delle modifiche. |
| Valutazione dei rischi| Rischio e conseguenze aziendali se l'account venisse compromesso.| Usare queste informazioni per limitare l'ambito delle autorizzazioni e determinare chi deve avere accesso alle informazioni sull'account. |
| Periodo per la revisione| Pianificazione in base alla quale l'account del servizio deve essere esaminato dal proprietario.| Usare questo per pianificare le comunicazioni e le verifiche di revisione. Documento che cosa deve verificarsi se una revisione non viene eseguita da un momento specifico dopo il periodo di revisione pianificato. |
| Durata| Durata massima prevista per l'account.| Usare questa utilità per pianificare le comunicazioni con il proprietario e infine per disabilitare l'eliminazione degli account. Laddove possibile, impostare una data di scadenza per le credenziali, in cui non è possibile eseguire automaticamente il rollover delle credenziali. |
| Nome| Nome standardizzato dell'account| Creare uno schema di denominazione per tutti gli account del servizio in modo che sia possibile eseguire ricerche, ordinare e filtrare facilmente sugli account del servizio. |


## <a name="use-the-principle-of-least-privileges"></a>Usa il principio dei privilegi minimi
Concedere all'account del servizio solo le autorizzazioni necessarie per eseguire le proprie attività e nient'altro. Se un account del servizio necessita di autorizzazioni di livello elevato, ad esempio un livello di privilegi di amministratore globale, valutare il motivo e provare a ridurre le autorizzazioni necessarie.

È consigliabile eseguire le procedure seguenti per i privilegi dell'account del servizio.

**Autorizzazioni**

* Non assegnare ruoli predefiniti agli account del servizio. Usare invece il [modello di concessione delle autorizzazioni OAuth2 per Microsoft Graph](/graph/api/resources/oauth2permissiongrant)

* Se all'entità servizio deve essere assegnato un ruolo con privilegi, è consigliabile assegnare un [ruolo personalizzato](https://docs.microsoft.com/azure/active-directory/roles/custom-create) con privilegi specifici, necessari, in base a un intervallo di tempo.

* Non includere gli account del servizio come membri di qualsiasi gruppo con autorizzazioni elevate. 

* [Usare PowerShell per enumerare i membri dei ruoli con privilegi](/powershell/module/azuread/get-azureaddirectoryrolemember), ad esempio   
`Get-AzureADDirectoryRoleMember`e filtro per ObjectType "entità servizio".

   oppure utilizzare  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Usare gli ambiti OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) per limitare la funzionalità a cui un account del servizio può accedere su una risorsa.
* Le entità servizio e le identità gestite possono usare gli ambiti OAuth 2,0 in un contesto delegato che rappresenta un utente connesso o come account del servizio nel contesto dell'applicazione. Nel contesto dell'applicazione non è connesso.

* Verificare che gli account di servizio degli ambiti richiedano risorse per assicurarsi che siano appropriati. Se, ad esempio, un account richiede file. ReadWrite. All, valutare se è effettivamente necessario solo file. Read. all. Per ulteriori informazioni sulle autorizzazioni, vedere per [Microsoft Graph riferimento alle autorizzazioni](https://docs.microsoft.com/graph/permissions-reference).

* Assicurarsi di considerare attendibile lo sviluppatore dell'applicazione o dell'API con l'accesso richiesto alle risorse.

**Duration**

* Limitare le credenziali dell'account del servizio (segreto client, certificato) a un periodo di utilizzo previsto.

* Pianificare le verifiche periodiche dell'utilizzo e dello scopo degli account del servizio. Verificare che le revisioni vengano eseguite prima della scadenza dell'account.

Quando si ha una chiara conoscenza dello scopo, dell'ambito e delle autorizzazioni necessarie, creare l'account del servizio. 

[Creare e usare identità gestite](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

[Creazione e uso di entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Usare un'identità gestita quando possibile. Se non è possibile usare un'identità gestita, usare un'entità servizio. Se non è possibile usare un'entità servizio, quindi usare solo un account utente Azure AD.

 

## <a name="build-a-lifecycle-process"></a>Creazione di un processo del ciclo di vita

La gestione del ciclo di vita di un account del servizio inizia con la pianificazione e termina con l'eliminazione permanente. 

Questo articolo ha precedentemente trattato la parte relativa alla pianificazione e alla creazione. È inoltre necessario monitorare, verificare le autorizzazioni, determinare l'utilizzo continuato di un account e infine effettuare il deprovisioning dell'account.

### <a name="monitor-service-accounts"></a>Monitorare gli account del servizio

Monitorare in modo proattivo gli account di servizio per assicurarsi che i modelli di utilizzo dell'account di servizio riflettano i modelli previsti e che l'account del servizio venga ancora utilizzato attivamente.

**Raccogliere e monitorare gli accessi degli account di servizio usando uno dei metodi seguenti:**

* Usare i log di Sign-In Azure AD nel portale di Azure AD.

* Esportazione dei log di Sign-In Azure AD in [archiviazione di Azure](https://docs.microsoft.com/azure/storage/), [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/)o monitoraggio di [Azure](https://docs.microsoft.com/azure/azure-monitor/logs/data-platform-logs).


![Screenshot che mostra la schermata di accesso dell'entità servizio.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**L'intelligence da cercare nei log Sign-In include:**

* Sono presenti account del servizio che non eseguono più l'accesso al tenant?

* I modelli di accesso degli account del servizio cambiano?

Si consiglia di esportare Azure AD registri di accesso e importarli negli strumenti di gestione delle informazioni e degli eventi di sicurezza (SIEM) esistenti, ad esempio Azure Sentinel. Usare SIEM per compilare avvisi e dashboard.

### <a name="review-service-account-permissions"></a>Controllare le autorizzazioni dell'account del servizio

Esaminare periodicamente le autorizzazioni concesse e gli ambiti a cui si accede dagli account del servizio per verificare se possono essere ridotti eliminati.

* Usare [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) per [creare l'automazione per il controllo e la documentazione degli](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) ambiti a cui viene concesso il consenso a un account del servizio.

* Usare PowerShell per [esaminare le credenziali delle entità servizio esistenti](https://github.com/AzureAD/AzureADAssessment) e verificarne la validità.

* Non impostare le credenziali dell'entità servizio su "Nessuna scadenza".

* Usare i certificati o le credenziali archiviate in Azure Azure Vault laddove possibile.

L'esempio di PowerShell gratuito Microsoft raccoglie le informazioni sulle credenziali e le concessioni OAuth2 dell'entità servizio, le registra in un file con valori delimitati da virgole (CSV) e un dashboard di esempio Power BI per interpretare e usare i dati. Per ulteriori informazioni, vedere [AzureAD/AzureADAssessment: strumenti per la valutazione di uno stato e configurazione del tenant di Azure ad (github.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Ricertificare l'utilizzo dell'account del servizio

Stabilire un processo di revisione per assicurarsi che gli account del servizio vengano periodicamente rivisti dai rispettivi proprietari e dalla sicurezza o dal team IT a intervalli regolari. 

**Il processo deve includere:**

* Come determinare il ciclo di revisione di ogni account di servizio (dovrebbe essere documentato nella CMDB).

* Le comunicazioni tra proprietario e sicurezza o i team IT prima dell'avvio delle revisioni.

* Tempistica e contenuto delle comunicazioni di avviso se la verifica non viene eseguita.

* Istruzioni su come procedere se i proprietari non riescono a esaminare o rispondere. È possibile, ad esempio, disabilitare l'account, ma non eliminarlo, fino al completamento della verifica.

* Istruzioni per determinare le dipendenze upstream e downstream e per notificare ad altri proprietari di risorse qualsiasi effetto.

**La revisione deve includere il proprietario e il suo partner IT per la certificazione:**

* L'account è ancora necessario.

* Le autorizzazioni concesse all'account sono adeguate e necessarie oppure viene richiesta una modifica.

* L'accesso all'account e alle relative credenziali è controllato.

* Le credenziali utilizzate dall'account sono appropriate per quanto riguarda il rischio per cui è stata valutata l'account (tipo di credenziale e durata delle credenziali)

* Il Punteggio di rischio dell'account non è stato modificato dopo l'ultima ricertificazione

* Aggiornamento della durata prevista dell'account e della successiva data di ricertificazione.

### <a name="deprovision-service-accounts"></a>Effettuare il deprovisioning degli account del servizio

* * Effettuare il deprovisioning degli account del servizio nelle seguenti circostanze: * * * *

* Lo script o l'applicazione per cui è stato creato l'account di servizio è stato ritirato.

* La funzione all'interno dello script o dell'applicazione per cui viene usato l'account del servizio, ad esempio l'accesso a una risorsa specifica, viene ritirata.


* L'account del servizio viene sostituito con un account del servizio diverso.

* Le credenziali sono scadute o l'account è altrimenti non funzionante e non sono presenti reclami.

**I processi per il deprovisioning devono includere le seguenti attività.**

1. Una volta effettuato il deprovisioning dell'applicazione o dello script associato, [monitorare](../reports-monitoring/concept-all-sign-ins.md#sign-ins-report) gli accessi e le risorse dall'account del servizio.

   * Se l'account è ancora attivo, determinarne la modalità di utilizzo prima di eseguire i passaggi successivi.
 
2. Se si tratta di un'identità del servizio gestito, disabilitare l'accesso dell'account del servizio, ma non rimuoverlo dalla directory.

3. Revocare le assegnazioni di ruolo e le concessioni di consenso OAuth2 per l'account del servizio.

4. Dopo un periodo definito e un ampio avviso per i proprietari, eliminare l'account del servizio dalla directory.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla protezione degli account dei servizi di Azure, vedere:

[Introduzione agli account del servizio di Azure](service-accounts-introduction-azure.md)

[Protezione delle identità gestite](service-accounts-managed-identities.md)

[Sicurezza dei principi di servizio](service-accounts-principal.md)




 

 
