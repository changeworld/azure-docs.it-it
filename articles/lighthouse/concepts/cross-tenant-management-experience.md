---
title: Esperienze di gestione tra tenant
description: La gestione risorse delegate di Azure consente un'esperienza di gestione tra tenant.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 027d1d5e81d5a652a7e2d5441c40440c661f730f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778626"
---
# <a name="cross-tenant-management-experiences"></a>Esperienze di gestione tra tenant

I provider di servizi [](../overview.md) possono usare Azure Lighthouse per gestire le risorse per più clienti dall'interno del proprio tenant Azure Active Directory (Azure AD). Molte attività e servizi possono essere eseguiti tra tenant gestiti usando la [gestione delle risorse delegate di Azure.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> La gestione risorse delegate di Azure può essere usata anche [all'interno di un'azienda con più tenant Azure AD propri](enterprise.md) per semplificare l'amministrazione tra tenant.

## <a name="understanding-tenants-and-delegation"></a>Informazioni sui tenant e sulla delega

Un Azure AD tenant è una rappresentazione di un'organizzazione. Si tratta di un'istanza dedicata di Azure AD che un'organizzazione riceve quando crea una relazione con Microsoft iscrivendosi ad Azure, Microsoft 365 o ad altri servizi. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD e ha un proprio ID tenant (un GUID). Per altre informazioni, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md).

In genere, per gestire le risorse di Azure per un cliente, i provider di servizi devono accedere al portale di Azure usando un account associato al tenant del cliente, richiedendo a un amministratore del tenant del cliente di creare e gestire gli account utente per il provider di servizi.

Con Azure Lighthouse, il processo di onboarding specifica gli utenti all'interno del tenant del provider di servizi che potranno lavorare su sottoscrizioni delegate e gruppi di risorse nel tenant del cliente. Questi utenti possono quindi accedere al portale di Azure usando le proprie credenziali. All'interno del portale di Azure possono gestire le risorse appartenenti a tutti i clienti a cui hanno accesso. Per eseguire questa operazione, visitare la pagina [Clienti personali](../how-to/view-manage-customers.md) nel portale di Azure o accedere direttamente al contesto della sottoscrizione di tale cliente nel portale di Azure o tramite API.

Azure Lighthouse maggiore flessibilità per gestire le risorse per più clienti senza dover accedere a account diversi in tenant diversi. Un provider di servizi, ad esempio, può avere due clienti, con responsabilità e livelli di accesso diversi. Usando Azure Lighthouse, gli utenti autorizzati possono accedere al tenant del provider di servizi per accedere a queste risorse.

![Diagramma che mostra le risorse dei clienti gestite tramite un tenant del provider di servizi.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Supporto delle API e degli strumenti di gestione

È possibile eseguire le attività di gestione nelle risorse delegate direttamente nel portale oppure usando le API e gli strumenti di gestione, ad esempio l'interfaccia della riga di comando di Azure e Azure PowerShell. Quando si usano le risorse delegate, è possibile utilizzare tutte le API esistenti, purché la funzionalità sia supportata per la gestione tra tenant e l'utente disponga delle autorizzazioni appropriate.

Il Azure PowerShell [cmdlet Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) mostrerà per impostazione predefinita `TenantId` per il tenant di gestione. È possibile usare gli attributi e per ogni sottoscrizione, consentendo di identificare se una sottoscrizione restituita appartiene a un tenant gestito `HomeTenantId` o al tenant di `ManagedByTenantIds` gestione.

Analogamente, i comandi dell'interfaccia della riga di comando di Azure, [ad esempio az account list,](/cli/azure/account#az_account_list) mostrano `homeTenantId` gli attributi e `managedByTenants` . Se, quando si usa l'interfaccia della riga di comando di Azure, questi valori non vengono visualizzati, provare a cancellare la cache eseguendo `az account clear` seguito da `az login --identity`.

Nell'API REST di Azure i [comandi Sottoscrizioni - Get](/rest/api/resources/subscriptions/get) e Sottoscrizioni - [Elenco](/rest/api/resources/subscriptions/list) includono `ManagedByTenant` .

> [!NOTE]
> Oltre alle informazioni sui tenant Azure Lighthouse, i tenant visualizzati da queste API possono riflettere anche i tenant partner per Azure Databricks applicazioni gestite di Azure.

Sono inoltre disponibili API specifiche per l'esecuzione di Azure Lighthouse attività. Per altre informazioni, vedere la sezione **Riferimento**.

## <a name="enhanced-services-and-scenarios"></a>Scenari e servizi avanzati

La maggior parte delle attività e dei servizi può essere eseguita nelle risorse delegate tra i tenant gestiti. Di seguito sono riportati alcuni degli scenari principali in cui la gestione tra tenant può essere particolarmente efficace.

[Azure Arc](../../azure-arc/index.yml):

- Gestire i server ibridi su larga scala [- Azure Arc server abilitati:](../../azure-arc/servers/overview.md)
  - [Gestire computer Windows Server o Linux all'esterno di Azure connessi](../../azure-arc/servers/onboard-portal.md) a sottoscrizioni delegate e/o gruppi di risorse in Azure
  - Gestire computer connessi usando costrutti di Azure, ad esempio Criteri di Azure e assegnazione di tag
  - Assicurarsi che lo stesso set di criteri sia applicato negli ambienti ibridi dei clienti
  - Usare Centro sicurezza di Azure per monitorare la conformità negli ambienti ibridi dei clienti
- Gestire cluster Kubernetes ibridi su larga scala: Azure Arc [Kubernetes abilitato (anteprima):](../../azure-arc/kubernetes/overview.md)
  - [Gestire cluster Kubernetes connessi a](../../azure-arc/kubernetes/quickstart-connect-cluster.md) sottoscrizioni delegate e/o gruppi di risorse in Azure
  - [Usare GitOps](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) per i cluster connessi
  - Applicare i criteri nei cluster connessi

[Automazione di Azure](../../automation/index.yml):

- Usare gli account di Automazione per accedere alle risorse delegate e usarle

[Backup di Azure](../../backup/index.yml):

- Eseguire il backup e il ripristino dei dati dei clienti da carichi di lavoro locali, macchine virtuali [di Azure, condivisioni file di Azure e altro ancora](../..//backup/backup-overview.md#what-can-i-back-up)
- Visualizzare i dati per tutte le risorse dei clienti delegate in [Centro backup](../../backup/backup-center-overview.md)
- Usare l'[Explorer di Backup](../../backup/monitor-azure-backup-with-backup-explorer.md) per visualizzare le informazioni operative degli elementi di backup (incluse le risorse di Azure non ancora configurate per il backup) e le informazioni di monitoraggio (processi e avvisi) per le sottoscrizioni delegate. L'Explorer di Backup è attualmente disponibile solo per i dati delle macchine virtuali di Azure.
- Usare i [report di Backup](../../backup/configure-reports.md) tra sottoscrizioni delegate per tenere traccia delle tendenze cronologiche, analizzare l'utilizzo dello spazio di archiviazione dei backup e controllare i backup e i ripristini.

[Azure Blueprints](../../governance/blueprints/index.yml):

- Usare Azure Blueprints per orchestrare la distribuzione di modelli di risorse e altri artefatti [(è](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) necessario un accesso aggiuntivo per preparare la sottoscrizione del cliente)

[Gestione costi di Azure + Fatturazione:](../../cost-management-billing/index.yml)

- Dal tenant di gestione, i partner CSP possono visualizzare, gestire e analizzare i costi di consumo pre-imposta (non inclusi gli acquisti) per i clienti inclusi nel piano di Azure. Il costo sarà basato sulle tariffe al dettaglio e sull'accesso al controllo degli accessi in base al ruolo di Azure del partner per la sottoscrizione del cliente. Attualmente, è possibile visualizzare i costi di consumo alle tariffe al dettaglio per ogni singola sottoscrizione del cliente in base all'accesso con controllo degli accessi in base al ruolo di Azure.

[Azure Key Vault](../../key-vault/general/index.yml):

- Creare insiemi di credenziali delle chiavi nei tenant dei clienti
- Usare un'identità gestita per creare insiemi di credenziali delle chiavi nei tenant dei clienti

[Servizio Azure Kubernetes](../../aks/index.yml):

- Gestire gli ambienti Kubernetes ospitati e distribuire e gestire le applicazioni in contenitori nei tenant dei clienti
- Distribuire e gestire cluster nei tenant dei clienti
-   Usare Monitoraggio di Azure contenitori per monitorare le prestazioni nei tenant dei clienti

[Azure Migrate](../../migrate/index.yml):

- Creare progetti di migrazione nel tenant del cliente ed eseguire la migrazione delle macchine virtuali

[Monitoraggio di Azure](../../azure-monitor/index.yml):

- Visualizzare gli avvisi per le sottoscrizioni delegate, con la possibilità di visualizzare e aggiornare gli avvisi in tutte le sottoscrizioni
- Visualizzare i dettagli del log attività per le sottoscrizioni delegate
- [Log Analytics:](../../azure-monitor/logs/service-providers.md)eseguire query sui dati da aree di lavoro remote in più tenant (si noti che gli account di automazione usati per accedere ai dati dalle aree di lavoro nei tenant dei clienti devono essere creati nello stesso tenant)
- [Creare, visualizzare e gestire gli avvisi del log attività](../../azure-monitor/alerts/alerts-activity-log.md) nei tenant dei clienti
- Creare avvisi nei tenant dei clienti che attivano l'automazione, ad esempio Automazione di Azure runbook o Funzioni di Azure, nel tenant di gestione tramite webhook
- Creare [impostazioni di diagnostica](../..//azure-monitor/essentials/diagnostic-settings.md) nei tenant dei clienti per inviare i log delle risorse alle aree di lavoro nel tenant di gestione
- Per i carichi di lavoro SAP, [monitorare le metriche di SAP Solutions con una vista aggregata tra i tenant dei clienti](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Rete di Azure](../../networking/networking-overview.md):

- Distribuire e gestire le [schede di interfaccia di](../../virtual-network/index.yml) rete virtuale e di rete virtuale di Azure all'interno di tenant gestiti
- Distribuire e configurare il [Firewall di Azure](../../firewall/overview.md) per proteggere le risorse della rete virtuale dei clienti
- Gestire i servizi di [connettività rete WAN virtuale di Azure,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)e [gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Usare Azure Lighthouse per supportare gli scenari principali per il programma [Rete di Azure MSP](../../networking/networking-partners-msp.md)

[Criteri di Azure](../../governance/policy/index.yml):

- Creare e modificare definizioni di criteri all'interno di sottoscrizioni delegate
- Distribuire definizioni di criteri e assegnazioni di criteri tra più tenant
- Assegnare definizioni di criteri definite dal cliente all'interno di sottoscrizioni delegate
- I clienti visualizzano i criteri creati dal provider di servizi insieme ai criteri che hanno creato personalmente
- Può [correggere deployIfNotExists o modificare le assegnazioni all'interno del tenant gestito](../how-to/deploy-policy-remediation.md)
- Si noti che la visualizzazione dei dettagli di conformità per le risorse non conformi nei tenant dei clienti non è attualmente supportata

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Include ora l'ID tenant nei risultati della query restituiti, consentendo di identificare se una sottoscrizione appartiene a un tenant gestito

[Centro sicurezza di Azure](../../security-center/index.yml):

- Visibilità tra tenant
  - Monitorare la conformità ai criteri di sicurezza e garantire la copertura della sicurezza tra le risorse di tutti i tenant
  - Monitoraggio continuo della conformità alle normative tra più tenant in un'unica visualizzazione
  - Monitorare, valutare e classificare in ordine di priorità le raccomandazioni sulla sicurezza di utilità pratica con il calcolo del punteggio di sicurezza
- Gestione del comportamento di sicurezza tra tenant
  - Gestire i criteri di sicurezza
  - Intervenire sulle risorse non conformi alle raccomandazioni sulla sicurezza di utilità pratica
  - Raccogliere e archiviare i dati relativi alla sicurezza
- Rilevamento e protezione dalle minacce tra tenant
  - Rilevare le minacce nelle risorse dei tenant
  - Applicare controlli avanzati di protezione dalle minacce, ad esempio l'accesso JIT alle macchine virtuali
  - Rafforzare la configurazione dei gruppi di sicurezza di rete con Protezione avanzata adattiva per la rete
  - Assicurarsi che i server eseguano solo le applicazioni e i processi che devono eseguire con i controlli applicazione adattivi
  - Monitorare le modifiche apportate a file e voci del Registro di sistema importanti con Monitoraggio dell'integrità dei file
- Si noti che l'intera sottoscrizione deve essere delegata al tenant di gestione. Centro sicurezza di Azure scenari non sono supportati con i gruppi di risorse delegate

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Gestire le risorse di Azure Sentinel [nei tenant dei clienti](../../sentinel/multiple-tenants-service-providers.md)
- [Tenere traccia degli attacchi e visualizzare gli avvisi di sicurezza in più tenant](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Visualizzare gli eventi imprevisti](../../sentinel/multiple-workspace-view.md) in Azure Sentinel di lavoro distribuite tra tenant

[Integrità dei servizi di Azure](../../service-health/index.yml):

- Monitorare l'integrità delle risorse dei clienti con Integrità risorse di Azure
- Tenere traccia dell'integrità dei servizi di Azure usati dai clienti

[Azure Site Recovery](../../site-recovery/index.yml):

- Gestire le opzioni di ripristino di emergenza per le macchine virtuali di Azure nei tenant dei clienti (si noti che non è possibile usare gli account per `RunAs` copiare le estensioni vm)

[Macchine virtuali di Azure](../../virtual-machines/index.yml):

- Usare le estensioni macchina virtuale per fornire attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure
- Usare la diagnostica di avvio per risolvere i problemi delle macchine virtuali di Azure
- Accedere alle macchine virtuali con la console seriale
- Integrare macchine virtuali con Azure Key Vault per password, segreti o chiavi crittografiche per la crittografia del disco usando l'identità gestita tramite criteri [,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)assicurando che i segreti siano archiviati in un Key Vault nei tenant gestiti
- Si noti che non è possibile usare Azure Active Directory per l'accesso remoto alle macchine virtuali

Richieste di supporto:

- [Aprire le richieste di supporto **da**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) Guida e supporto tecnico portale di Azure per le risorse delegate (selezionando il piano di supporto disponibile per l'ambito delegato)
- Usare [l'API Quota di Azure](/rest/api/reserved-vm-instances/quotaapi) per visualizzare e gestire le quote dei servizi di Azure per le risorse dei clienti delegate

## <a name="current-limitations"></a>Limitazioni correnti

In tutti gli scenari tenere presenti le limitazioni correnti seguenti:

- Le richieste gestite da Azure Resource Manager possono essere eseguite usando Azure Lighthouse. Gli URI delle operazioni per queste richieste iniziano con `https://management.azure.com`. Le richieste gestite da un'istanza di un tipo di risorsa, ad esempio l'accesso ai segreti di Key Vault o l'accesso ai dati di archiviazione, non sono tuttavia supportate con Azure Lighthouse. Gli URI delle operazioni per queste richieste iniziano in genere con un indirizzo univoco per l'istanza, ad esempio `https://myaccount.blob.core.windows.net` o `https://mykeyvault.vault.azure.net/`. Il secondo è in genere usato per le operazioni di dati invece che per le operazioni di gestione.
- Le assegnazioni di ruolo devono [usare i ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md). Tutti i ruoli predefiniti sono attualmente supportati con la gestione risorse delegate di Azure, ad eccezione di Proprietario o di qualsiasi ruolo predefinito con [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) autorizzazione. Il ruolo Amministratore Accesso utenti è supportato solo per uso limitato nell'[assegnazione di ruoli a identità gestite](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  I ruoli personalizzati e i [ruoli di amministratore della sottoscrizione classica](../../role-based-access-control/classic-administrators.md) non sono supportati.
- Sebbene sia possibile caricare sottoscrizioni che utilizzano Azure Databricks, gli utenti nel tenant di gestione non possono al momento avviare le aree di lavoro Azure Databricks in una sottoscrizione delegata.
- Anche se è possibile eseguire l'onboarded di sottoscrizioni e gruppi di risorse con blocchi delle risorse, tali blocchi non impediranno l'esecuzione di azioni da parte degli utenti nel tenant di gestione. Le [assegnazioni di rifiuto](../../role-based-access-control/deny-assignments.md) che proteggono le risorse gestite dal sistema, ad esempio quelle create dalle applicazioni gestite di Azure o Azure Blueprints (assegnazioni di rifiuto assegnate dal sistema), impediscono agli utenti del tenant di gestione di agire su tali risorse; tuttavia, a questo punto gli utenti del tenant del cliente non possono creare le proprie assegnazioni di rifiuto (assegnazioni di rifiuto assegnate dall'utente).
- La delega delle sottoscrizioni in [un cloud nazionale](../../active-directory/develop/authentication-national-cloud.md) e nel cloud pubblico di Azure o in due cloud nazionali separati non è supportata.

## <a name="next-steps"></a>Passaggi successivi

- È possibile eseguire l'onboard Azure Lighthouse clienti usando modelli [di Azure Resource Manager](../how-to/onboard-customer.md) o pubblicando un'offerta di servizi gestiti pubblici o privati [Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visualizzare e gestire i clienti](../how-to/view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.