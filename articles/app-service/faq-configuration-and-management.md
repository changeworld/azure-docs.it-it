---
title: Domande frequenti sulla configurazione
description: Risposte alle domande frequenti sui problemi di configurazione e gestione per Servizio app di Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4b6ceef837d985db004e9d925b554c54c287424
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834535"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Domande frequenti sulla configurazione e sulla gestione per App Web di Azure

Questo articolo offre risposte alle domande frequenti sui problemi di configurazione e gestione per la [funzionalità App Web del servizio app di Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Esistono limitazioni che occorre tenere in considerazione se si desidera spostare le risorse del servizio app?

Se si intende spostare le risorse del servizio app in un nuovo gruppo di risorse o in una sottoscrizione, occorre considerare alcune limitazioni. Per altre informazioni, vedere [Limitazioni del servizio app](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Come si utilizza un nome di dominio personalizzato per un'app Web?

Per le risposte alle domande comuni sull'uso di un nome di dominio personalizzato con l'app Web di Azure, vedere il video di sette minuti [Aggiungere un nome di dominio personalizzato](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Il video offre una procedura dettagliata su come aggiungere un nome di dominio personalizzato. Viene descritto come utilizzare il proprio URL anziché l'URL *.azurewebsites.net con l'app Web del servizio app. È anche possibile vedere una procedura dettagliata su [come eseguire il mapping di un nome di dominio personalizzato](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Come si acquista un nuovo dominio personalizzato per un'app Web?

Per informazioni su come acquistare e configurare un dominio personalizzato per l'app Web del servizio app, vedere [Acquistare e configurare un nome di dominio personalizzato nel servizio app](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Ricerca per categorie caricare e configurare un certificato TLS/SSL esistente per l'app Web?

Per informazioni su come caricare e configurare un certificato TLS/SSL personalizzato esistente, vedere Aggiungere un [certificato TLS/SSL all'app del servizio app.](configure-ssl-certificate.md)


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Ricerca per categorie acquistare e configurare un nuovo certificato TLS/SSL in Azure per l'app Web?

Per informazioni su come acquistare e configurare un certificato TLS/SSL per l'app Web del servizio app, vedere Aggiungere un [certificato TLS/SSL all'app del servizio app.](configure-ssl-certificate.md)


## <a name="how-do-i-move-application-insights-resources"></a>Come si spostano le risorse di Application Insights?

Attualmente, Azure Application Insights non supporta l'operazione di spostamento. Se il gruppo di risorse originale include una risorsa di Application Insights, non è possibile spostare tale risorsa. Se si include la risorsa di Application Insights quando si tenta di spostare un'app del servizio app, l'intera operazione di spostamento ha esito negativo. Tuttavia, per il corretto funzionamento dell'app non è necessario che la risorsa Application Insights e il piano di servizio app risiedano nello stesso gruppo di risorse in cui si trova l'app stessa.

Per altre informazioni, vedere [Limitazioni del servizio app](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Dove è possibile trovare un elenco di controllo del materiale sussidiario e altre informazioni sulle operazioni di spostamento delle risorse?

[Limitazioni del servizio app](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) mostra come spostare le risorse in una nuova sottoscrizione o in un nuovo gruppo di risorse nella stessa sottoscrizione. È possibile ottenere informazioni sull'elenco di controllo dello spostamento delle risorse, apprendere quali servizi supportano l'operazione di spostamento e acquisire altri dettagli sulle limitazioni del servizio app e su altri argomenti.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Come si imposta il fuso orario del server per l'app Web?

Per impostare il fuso orario del server per l'app Web:

1. Nel portale di Azure nella sottoscrizione del servizio app, andare al menu **Impostazioni applicazione**.
2. In **Impostazioni app**, aggiungere questa impostazione:
    * Key = WEBSITE_TIME_ZONE
    * Value = *fuso orario desiderato*
3. Selezionare **Salva**.

Per i servizi app eseguiti in Windows, vedere l'output del comando `tzutil /L` di Windows. Usare il valore della seconda riga di ogni voce. Ad esempio: "Ora solare Tonga". Alcuni di questi valori sono elencati anche nella **colonna Fuso** orario in [Fusi orari predefiniti](/windows-hardware/manufacture/desktop/default-time-zones).

Per i servizi app eseguiti in Linux, impostare un valore dal [database IANA TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Ad esempio: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Perché i processi Web continui talvolta hanno esito negativo?

Per impostazione predefinita, le app Web vengono scaricate se restano inattive per un determinato periodo di tempo. Ciò consente al sistema di conservare le risorse. Nei piani Basic e Standard, è possibile attivare l'impostazione **Sempre attivata** per mantenere l'app Web sempre caricata. Se nell'app Web vengono eseguiti processi Web continui, è necessario attivare **Sempre attivata**; in caso contrario, l'esecuzione dei processi Web potrebbe non essere affidabile. Per altre informazioni, vedere [Creare un processo Web con esecuzione continua](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Come si ottiene l'indirizzo IP in uscita per un'app Web?

Per ottenere l'elenco di indirizzi IP in uscita per un'app Web:

1. Nel pannello dell'app Web del portale di Azure, passare al menu **Proprietà**.
2. Cercare **indirizzi ip in uscita**.

Viene visualizzato l'elenco di indirizzi IP in uscita.

Per informazioni su come ottenere l'indirizzo IP in uscita se il sito Web è ospitato in un Ambiente del servizio app, vedere [Indirizzi di rete in uscita](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Come si ottiene un indirizzo IP in ingresso dedicato o riservato per un'app Web?

Per configurare un indirizzo IP dedicato o riservato per le chiamate in ingresso effettuate al sito Web dell'app Azure, installare e configurare un certificato TLS/SSL basato su IP.

Si noti che per utilizzare un indirizzo IP dedicato o riservato per le chiamate in ingresso, il piano del servizio app deve essere di tipo Basic o superiore.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>È possibile esportare il certificato del servizio app per utilizzarlo all'esterno di Azure, ad esempio per un sito Web ospitato in un'altra posizione? 

Sì, è possibile esportarli per usarli all'esterno di Azure. Per altre informazioni, vedere [Domande frequenti per i certificati del servizio app e i domini personalizzati](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>È possibile esportare il certificato del servizio app per utilizzarli con altri servizi cloud di Azure?

Il portale offre un'esperienza di alto livello di distribuzione di un certificato del servizio app tramite Key Vault nelle app del servizio app. Tuttavia, alcuni clienti hanno inviato richieste per utilizzare questi certificati all'esterno della piattaforma del servizio app, ad esempio con macchine virtuali di Azure. Per informazioni su come creare una copia PFX locale del certificato del servizio app per poter usare il certificato con altre risorse di Azure, vedere [Creating a local PFX copy of an App Service certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/) (Creazione di una copia PFX locale di un certificato del servizio app).

Per altre informazioni, vedere [Domande frequenti per i certificati del servizio app e i domini personalizzati](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Perché viene visualizzato il messaggio "Parzialmente completato" quando si tenta di eseguire il backup di un'app web?

Gli errori di backup sono comunemente causati dal fatto che alcuni file sono utilizzati dall'applicazione. I file in uso sono bloccati durante l'esecuzione del backup. Ciò impedisce che ne venga eseguito il backup ed è possibile che venga visualizzato lo stato "Parzialmente completato". È possibile evitare questo problema escludendo i file dal processo di backup. È possibile scegliere di eseguire il backup solo degli elementi necessari. Per altre informazioni, vedere [Backup delle sole parti importanti del sito con app web di Azure](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Come si rimuove un'intestazione dalla risposta HTTP?

Per rimuovere le intestazioni dalla risposta HTTP, aggiornare il file di web.config sito. Per altre informazioni, vedere [Rimuovere intestazioni del server standard nei siti Web di Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Il servizio app è conforme con lo standard PCI 3.0 e 3.1?

Attualmente, la funzionalità delle app Web del servizio app di Azure è conforme con PCI Data Security Standard (DSS) versione 3.0 livello 1. La versione 3.1 di PCI DSS fa parte del piano d'azione. È già in corso la pianificazione per la modalità di adozione dello standard più recente.

Il certificato PCI DSS versione 3.1 richiede la disabilitazione di Transport Layer Security (TLS) 1.0. Attualmente, la disabilitazione di TLS 1.0 non è un'opzione per la maggior parte dei piani del servizio app. Tuttavia, se si utilizza l'ambiente del servizio app o si è disposti a eseguire la migrazione del carico di lavoro per l'ambiente del servizio app, è possibile ottenere più controllo dell'ambiente. A tal fine, occorre disabilitare TLS 1.0 contattando il supporto tecnico di Azure. Nei piani per il futuro immediato si prevede di rendere accessibili agli utenti queste impostazioni.

Per altre informazioni, vedere [Conformità dell'app web del servizio app di Microsoft Azure con lo standard PCI 3.0 e 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Come si utilizzano gli slot di distribuzione e l'ambiente di gestione temporanea?

Nei piani del servizio app Standard o Premium, quando si distribuisce l'app Web nel servizio app, è possibile eseguire la distribuzione in uno slot di distribuzione separato, anziché in quello di produzione predefinito. Gli slot di distribuzione sono App Web live con i propri nomi host. È possibile scambiare il contenuto dell'app Web e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione.

Per altre informazioni sull'utilizzo degli slot di distribuzione, vedere [Configurare un ambiente di gestione temporanea nel servizio app](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Come si accede e come si esaminano i log dei processi Web?

Per esaminare i log dei processi Web:

1. Accedere al sito **Web Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Selezionare il processo Web.
3. Selezionare il pulsante **Attiva/Disattiva Output**.
4. Per scaricare il file di output, selezionare il collegamento **Download**.
5. Per le singole esecuzioni, selezionare **Singolo richiamo**.
6. Selezionare il pulsante **Attiva/Disattiva Output**.
7. Selezionare il collegamento di download.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Si sta tentando di utilizzare Connessioni ibride con SQL Server. Perché viene visualizzato il messaggio "System.OverflowException: Overflow di un'operazione aritmetica"?

Se si utilizza Connessioni ibride per accedere a SQL Server, un aggiornamento di Microsoft .NET del 10 maggio 2016 potrebbe causare problemi nelle connessioni. È possibile che venga visualizzato questo messaggio:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Soluzione

L'eccezione è stata causata da un problema con Gestione connessione ibrida che poi è stato risolto. Assicurarsi di [aggiornare Gestione connessione ibrida](https://go.microsoft.com/fwlink/?LinkID=841308) per risolvere il problema.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Ricerca per categorie aggiungere una regola di riscrittura URL?

Per aggiungere una regola di riscrittura URL, creare un file web.config con le voci di configurazione pertinenti nella **cartella wwwroot.** Per altre informazioni, vedere app Azure [Services: Understanding URL rewrite](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Come si controlla il traffico in ingresso nel servizio app?

A livello di sito, sono disponibili due opzioni per controllare il traffico in ingresso nel servizio app:

* Attivare le restrizioni di IP dinamico. Per informazioni su come attivare le restrizioni di IP dinamico, vedere [Restrizioni di IP e dominio per siti Web di Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Attivare la protezione di modulo. Per informazioni su come attivare la sicurezza del modulo, vedere [Firewall dell'applicazione Web ModSecurity nei siti Web di Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Se si utilizza l'ambiente del servizio app, è possibile utilizzare il [firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Come è possibile bloccare le porte in un'app Web del servizio Web?

Nell'ambiente del tenant condiviso del servizio app, non è possibile bloccare porte specifiche data la natura dell'infrastruttura. Anche le porte TCP 4020, 4022 e 4024 potrebbero essere aperte per il Visual Studio remoto.

Nell'ambiente del servizio app, si ha il controllo totale sul traffico in ingresso e in uscita. È possibile utilizzare i Gruppi di sicurezza di rete per limitare o bloccare porte specifiche. Per altre informazioni sull'ambiente del servizio app, vedere [Introduzione all'ambiente del servizio app](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Come si acquisisce una traccia F12?

È possibile acquisire una traccia F12 in due modi:

* Traccia HTTP F12
* Output di console F12

### <a name="f12-http-trace"></a>Traccia HTTP F12

1. In Internet Explorer, andare al proprio sito Web. È importante accedere prima di eseguire i passaggi successivi. In caso contrario, la traccia F12 acquisisce i dati di accesso sensibili.
2. Premere F12.
3. Verificare che la scheda **Rete** sia selezionata, quindi selezionare il pulsante **Riproduci** di colore verde.
4. Eseguire i passaggi per riprodurre il problema.
5. Selezionare il pulsante **Termina** di colore rosso.
6. Selezionare il pulsante **Salva** (icona del disco) e salvare il file HAR (in Internet Explorer e Microsoft Edge) *oppure* fare clic con il pulsante destro del mouse sul file HAR, quindi selezionare **Salva come HAR con contenuto** (in Chrome).

### <a name="f12-console-output"></a>Output di console F12

1. Selezionare la scheda **Console**.
2. Per ogni scheda che contiene più di zero elementi, selezionare la scheda (**Errore**, **Avviso** o **Informazioni**). Se la scheda non è selezionata, l'icona della scheda è grigia o nera quando si sposta il cursore.
3. Fare clic con il pulsante destro del mouse nell'area dei messaggi del riquadro, quindi scegliere **Copia tutto**.
4. Incollare il testo copiato in un file, quindi salvare il file.

Per visualizzare un file HAR, è possibile utilizzare il [visualizzatore HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Perché viene visualizzato un errore quando si tenta di connettere un'app Web del servizio app a una rete virtuale connessa a ExpressRoute?

Se si tenta di connettere un'app Web di Azure a una rete virtuale connessa a Microsoft Azure ExpressRoute, l'esito è negativo. Viene visualizzato il messaggio seguente: "Il gateway non è di tipo VPN".

Attualmente, non è possibile avere connessioni VPN da punto a sito a una rete virtuale connessa a ExpressRoute. VPN da punto a sito ed ExpressRoute non possono coesistere per la stessa rete virtuale. Per altre informazioni, vedere [Limiti e limitazioni delle connessioni ExpressRoute e VPN da sito a sito](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Come si connette un'app Web del servizio app a una rete virtuale che dispone di un gateway con routing statico (basato su criteri)?

Attualmente, la connessione di un'app Web del servizio app a una rete virtuale che dispone di un gateway con routing statico (basato su criteri) non è supportata. Se la rete virtuale di destinazione esiste già, deve avere una VPN da punto a sito abilitata con un gateway di routing dinamico per poter eseguire la connessione a un'app. Se il gateway è impostato per il routing statico, è possibile abilitare una VPN da punto a sito. 

Per altre informazioni, vedere [Integrare un'app in una rete virtuale di Azure](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Nell'ambiente del servizio app, perché è possibile creare solo un piano di servizio app anche se sono disponibili due ruoli di lavoro?

Per garantire tolleranza di errore, un ambiente del servizio app richiede l'allocazione di almeno una risorsa di calcolo aggiuntiva per ogni pool di ruoli di lavoro. Non è possibile assegnare un carico di lavoro a una risorsa di calcolo aggiuntiva.

Per altre informazioni, vedere [Come creare un ambiente del servizio app](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Perché vengono visualizzati i timeout quando si tenta di creare un ambiente del servizio app?

Talvolta, la creazione di un ambiente del servizio app ha esito negativo. In tal caso, viene visualizzato l'errore seguente nei log attività:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Per risolvere questo problema, assicurarsi che nessuna delle condizioni seguenti sia vera:
* La subnet è troppo piccola.
* La subnet non è vuota.
* ExpressRoute ostacola i requisiti di connettività di rete di un ambiente del servizio app.
* Un gruppo di sicurezza di rete non valido ostacola i requisiti di connettività di rete di un ambiente del servizio app.
* Il tunneling forzato è attivato.

Per altre informazioni, vedere [Problemi più frequenti quando si distribuisce o si crea un nuovo ambiente del servizio app di Azure](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Perché non è possibile eliminare il piano del servizio app?

Non è possibile eliminare un piano del servizio app se sono presenti app del servizio app associate al piano del servizio app. Prima di eliminare un piano del servizio app, rimuovere tutte le app del servizio app associate dal piano del servizio app.

## <a name="how-do-i-schedule-a-webjob"></a>Come è possibile pianificare un processo Web?

Per creare un processo Web pianificato, utilizzare le espressioni Cron:

1. Creare un file settings.job.
2. In questo file JSON, includere una proprietà di pianificazione tramite un'espressione Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Per altre informazioni sui processi Web pianificati, vedere [Creare un processo Web pianificato utilizzando un'espressione Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Come si esegue il test di penetrazione per l'app del servizio app?

Per eseguire il test di penetrazione, [inviare una richiesta](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Come si configura un nome di dominio personalizzato per un app We del servizio app che utilizza Gestione traffico?

Per informazioni su come utilizzare un nome di dominio personalizzato con un'app del servizio app che utilizza Gestione traffico di Microsoft Azure per il bilanciamento del carico, vedere [Configurare un nome di dominio personalizzato per un'app Web di Azure con Gestione traffico](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Il certificato del servizio app è contrassegnato per illecito. Come posso risolvere il problema?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durante la verifica del dominio di un acquisto di certificati di servizio app, potrebbe venire visualizzato il messaggio seguente:

"Il certificato è stato segnalato per una potenziale frode. È in corso l'esame della richiesta. Se il certificato non diventa utilizzabile entro 24 ore, contattare il supporto tecnico di Azure".

Come indica il messaggio, il processo di verifica della frode potrebbe richiedere fino a 24 ore. Durante questo periodo di tempo, il messaggio rimarrà visualizzato.

Se il certificato di servizio app continua a visualizzare questo messaggio dopo 24 ore, eseguire il seguente script di PowerShell. Lo script contatta direttamente il [provider del certificato](https://www.godaddy.com/) per risolvere il problema.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Come funzionano autenticazione e autorizzazione nel servizio app?

Per la documentazione dettagliata sull'autenticazione e l'autorizzazione nel servizio app, vedere i documenti relativi ai vari accessi di identificazione dei provider:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Account Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Come si reindirizza il dominio predefinito *.azurewebsites.net al dominio personalizzato dell'app Web di Azure?

Quando si crea un nuovo sito Web tramite le app Web in Azure, viene assegnato un dominio predefinito *nomesito*.azurewebsites.net al sito. Se si aggiunge un nome host personalizzato al sito e non si vuole che gli utenti possano accedere al dominio *.azurewebsites.net predefinito, è possibile reindirizzare l'URL predefinito. Per informazioni su come reindirizzare tutto il traffico dal dominio predefinito del sito Web al dominio personalizzato, vedere [Reindirizzare il dominio predefinito al dominio personalizzato nelle app Web di Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Come si determina la versione di .NET installata nel servizio app?

Il modo più rapido per trovare la versione di Microsoft .NET installata nel servizio app consiste nell'utilizzare la console Kudu. È possibile accedere alla console Kudu dal portale o tramite l'URL dell'app del servizio app. Per istruzioni dettagliate, vedere [Determinare la versione di .NET installata nel servizio app](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Perché la scalabilità automatica non funziona come previsto?

Se la scalabilità automatica di Azure non riduce né aumenta il numero di istanze dell'app Web come previsto, è possibile che sia in esecuzione uno scenario in cui la scalabilità non viene applicata intenzionalmente per evitare un ciclo infinito a causa dell'instabilità. Ciò in genere si verifica quando non il margine tra le soglie di riduzione e aumento non è adeguato. Per informazioni su come evitare l'instabilità e per le procedure consigliate per la scalabilità automatica, vedere [Procedure consigliate per la scalabilità automatica](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Perché la scalabilità automatica talvolta viene applicata solo parzialmente?

La scalabilità automatica si attiva quando le metriche superano i limiti preconfigurati. In alcuni casi, è possibile notare che la capacità viene riempita solo parzialmente rispetto al previsto. Questa situazione può verificarsi quando il numero di istanze desiderato non è disponibile. In questo scenario, la scalabilità automatica utilizza il numero di istanze disponibili per un riempimento parziale. La scalabilità automatica esegue quindi la logica di ribilanciamento per ottenere più capacità. Alloca le istanze rimanenti. Ciò potrebbe richiedere alcuni minuti.

Se il numero previsto di istanze non viene visualizzato dopo pochi minuti, è possibile che il riempimento parziale fosse sufficiente per portare le metriche entro i limiti. In alternativa, è possibile che la scalabilità automatica abbia ridotto le istanze avendo raggiunto il limite inferiore delle metriche.

Se queste condizioni non sono applicabili e il problema persiste, inviare una richiesta di supporto.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Come si attiva la compressione HTTP per il contenuto?

Per attivare la compressione sia per i tipi di contenuto sia statici che dinamici, aggiungere il codice seguente al file web.config a livello di applicazione:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

È inoltre possibile specificare i tipi MIME statici e dinamici che si desidera comprimere. Per altre informazioni, vedere la risposta a una domanda del forum in [Impostazioni di httpCompression in un semplice sito Web di Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Come si effettua la migrazione da un ambiente locale al servizio app?

Per eseguire la migrazione di siti da server Web Linux e Windows al servizio app, è possibile utilizzare Azure App Service Migration Assistant. Lo strumento di migrazione crea app Web e database in Azure in base alle esigenze, quindi pubblica il contenuto. Per altre informazioni, vedere [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Perché il certificato viene rilasciato per 11 mesi e non per un anno intero?

Per tutti i certificati rilasciati dopo il 1° settembre 2020, la durata massima è ora di 397 giorni. I certificati rilasciati prima del 1° settembre 2020 hanno una validità massima di 825 giorni fino a quando non vengono rinnovati, reimpostati e così via. Eventuali certificati rinnovati dopo il 1° settembre 2020 saranno interessati da questa modifica e gli utenti potrebbero notare una validità più breve nei certificati rinnovati.
GoDaddy ha implementato un servizio di sottoscrizione che allo stesso tempo soddisfa i nuovi requisiti e tempo rispetta i certificati dei clienti esistenti. Trenta giorni prima della scadenza del certificato appena rilasciato, il servizio rilascia automaticamente un secondo certificato che estende la durata alla data di scadenza originale. Il servizio app sta collaborando con GoDaddy per affrontare questa modifica e assicurarsi che i clienti ricevano la durata completa dei loro certificati.
