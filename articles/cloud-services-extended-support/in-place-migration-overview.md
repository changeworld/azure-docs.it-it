---
title: Eseguire la migrazione di servizi cloud di Azure (versione classica) a servizi cloud di Azure (supporto esteso)
description: Panoramica della migrazione dai servizi cloud (versione classica) al servizio cloud (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286912"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Eseguire la migrazione di servizi cloud di Azure (versione classica) a servizi cloud di Azure (supporto esteso)

Questo articolo fornisce una panoramica sullo strumento di migrazione supportato dalla piattaforma e su come usarlo per eseguire la migrazione di [servizi cloud di Azure (versione classica)](../cloud-services/cloud-services-choose-me.md) a [servizi cloud di Azure (supporto esteso)](overview.md).

Lo strumento di migrazione usa le stesse API e ha la stessa esperienza della [migrazione della macchina virtuale (classica)](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview). 

> [!IMPORTANT]
> La migrazione da servizi cloud (versione classica) a servizi cloud (supporto esteso) con lo strumento di migrazione è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se è necessaria assistenza per la migrazione, vedere le risorse seguenti: 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): supporto Microsoft e della community per la migrazione.
- [Supporto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D)per la migrazione di Azure: team di supporto dedicato per assistenza tecnica durante la migrazione. I clienti senza supporto tecnico possono usare la [funzionalità di supporto gratuita](https://aka.ms/cs-migration-errors) fornita in modo specifico per questa migrazione.
- Se la società o l'organizzazione ha collaborato con Microsoft o collabora con i rappresentanti Microsoft, come gli architetti di soluzioni cloud o i responsabili degli account tecnici, rivolgersi ad essi per ottenere ulteriori risorse per la migrazione.
- Completare [questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) per fornire commenti e suggerimenti o generare problemi al team del prodotto servizi cloud (supporto esteso). 

## <a name="migration-benefits"></a>Vantaggi della migrazione
La migrazione supportata dalla piattaforma offre i vantaggi principali seguenti:
- La migrazione è orchestrata completamente dalla piattaforma, spostando l'intera distribuzione e le risorse associate a Azure Resource Manager.
- Nessuna migrazione del tempo di inattività.
- Migrazione semplice e rapida rispetto ad altri percorsi di migrazione riducendo al minimo le attività manuali. 
- Mantiene l'indirizzo IP e l'etichetta DNS dei servizi cloud come parte della migrazione. 

Per altri vantaggi e perché è necessario eseguire la migrazione, vedere [servizi cloud (supporto esteso)](overview.md) e [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Configurare l'accesso per la migrazione

Per eseguire questa migrazione, è necessario essere aggiunti come coamministratore per la sottoscrizione e registrare i provider necessari. 

1. Accedere al portale di Azure.
3. Nel menu Hub, selezionare Sottoscrizione. Se questa voce non viene visualizzata, selezionare Tutti i servizi.
3. Individuare la voce della sottoscrizione appropriata e quindi esaminare il campo ruolo personale. Per un coamministratore, il valore deve essere amministratore account. Se non si è in grado di aggiungere un coamministratore, contattare un amministratore del servizio o un coamministratore per la sottoscrizione per essere aggiunti.

4. Registrare la sottoscrizione per lo spazio dei nomi Microsoft. ClassicInfrastructureMigrate usando il [portale](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) o [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Registrare la sottoscrizione per la funzionalità di anteprima della migrazione di servizi cloud usando il [portale](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) o l' [interfaccia](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) della riga

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Verificare lo stato della registrazione. Il completamento della registrazione può richiedere alcuni minuti. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>In che modo la migrazione per i servizi cloud (versione classica) è diversa dalle macchine virtuali (versione classica)?
Azure Service Manager supporta due diversi prodotti di calcolo, [macchine virtuali di Azure (classiche)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) e [servizi cloud di Azure (versione classica)](../cloud-services/cloud-services-choose-me.md) o ruoli Web/di lavoro. I due prodotti variano a seconda del tipo di distribuzione che si trova all'interno del servizio cloud. Servizi cloud di Azure (versione classica) usa il servizio cloud contenente le distribuzioni con i ruoli Web/di lavoro. Macchine virtuali di Azure (versione classica) usa un servizio cloud che contiene le distribuzioni con macchine virtuali IaaS.

L'elenco degli scenari supportati è diverso tra i servizi cloud (versione classica) e le macchine virtuali (versione classica) a causa delle differenze nei tipi di distribuzione.

## <a name="migration-steps"></a>Passaggi della migrazione
 
I clienti possono migrare le distribuzioni di servizi cloud (classiche) usando le stesse quattro operazioni usate per eseguire la migrazione delle macchine virtuali (versione classica). 

1. **Convalida migrazione** : convalida che la migrazione non verrà impedita da scenari comuni non supportati.
2. **Prepara migrazione** : duplica i metadati delle risorse in Azure Resource Manager. Tutte le risorse sono bloccate per le operazioni di creazione/aggiornamento/eliminazione per assicurarsi che i metadati delle risorse siano sincronizzati tra Server Manager di Azure e Azure Resource Manager. Tutte le operazioni di lettura funzioneranno usando le API servizi cloud (versione classica) e servizi cloud (supporto esteso).
3. **Interrompi migrazione** : rimuove i metadati delle risorse da Azure Resource Manager. Sblocca tutte le risorse per le operazioni di creazione/aggiornamento/eliminazione.
4. **Migrazione del commit** : rimuove i metadati delle risorse da Azure Service Manager. Sblocca la risorsa per le operazioni di creazione/aggiornamento/eliminazione. Abort non è più consentito dopo il tentativo di commit.

>[!NOTE]
> Prepare, Abort e commit sono idempotente e pertanto, in caso di esito negativo, un nuovo tentativo dovrebbe risolvere il problema.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Image Mostra il diagramma dei passaggi associati alla migrazione.":::

Per altre informazioni, vedere [Panoramica della migrazione supportata dalla piattaforma di risorse IaaS dal modello di distribuzione classica a Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Risorse e funzionalità supportate disponibili per la migrazione associata a servizi cloud (versione classica)
-   Account di archiviazione
-   Reti virtuali
-   Gruppi di sicurezza di rete
-   Indirizzi IP pubblici riservati
-   Elenchi di controllo di accesso endpoint
-   Route definite dall'utente
-   Servizio di bilanciamento del carico interno
-   Migrazione di certificati a Key Vault
-   Plug-in ed estensione (basati su XML e JSON)
-   Alle attività di avvio/arresto
-   Distribuzioni con rete accelerata
-   Distribuzioni con ruoli singoli o multipli
-   Servizio di bilanciamento del carico di base
-   Input, input dell'istanza, endpoint interni
-   Indirizzi IP pubblici dinamici
-   Nome DNS 
-   Regole del traffico di rete
-   Rete virtuale Hypernet

## <a name="supported-configurations--migration-scenarios"></a>Configurazioni supportate/scenari di migrazione
Si tratta di scenari principali che coinvolgono combinazioni di risorse, funzionalità e servizi cloud. L'elenco non è completo.

| Servizio | Configurazione | Commenti | 
|---|---|---|
| [Servizi di dominio Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Reti virtuali che contengono Azure Active Directory servizi del dominio. | È supportata la rete virtuale contenente sia la distribuzione del servizio cloud che Azure AD servizi del dominio. Il cliente deve innanzitutto migrare separatamente Azure AD servizi del dominio e quindi eseguire la migrazione della rete virtuale rimanente solo con la distribuzione del servizio cloud |
| Servizio cloud | Servizio cloud con una distribuzione solo in un unico slot. | È possibile eseguire la migrazione dei servizi cloud contenenti una distribuzione di produzione o slot di staging |
| Servizio cloud | Distribuzione non in una rete virtuale visibile pubblicamente (distribuzione della rete virtuale predefinita) | Un servizio cloud può trovarsi in una rete virtuale visibile pubblicamente, in una rete virtuale nascosta o non in una rete virtuale.  I servizi cloud in una rete virtuale nascosta e le reti virtuali visibili pubblicamente sono supportati per la migrazione. Il cliente può usare l'API Validate per stabilire se una distribuzione si trova all'interno di una rete virtuale predefinita o meno e quindi determinare se è possibile eseguirne la migrazione. |
|Servizio cloud | Estensioni XML (BGInfo, debugger di Visual Studio, Distribuzione Web e debug remoto). | Tutte le estensioni XML sono supportate per la migrazione 
| Rete virtuale | Rete virtuale contenente più servizi cloud. | La rete virtuale contiene più servizi cloud è supportata per la migrazione. Viene eseguita la migrazione della rete virtuale e di tutti i servizi cloud all'interno di esso insieme a Azure Resource Manager. |
| Rete virtuale | Migrazione di reti virtuali create tramite il portale (richiede l'uso di "gruppo Resource-Group-Name VNet-Name" nel file. cscfg)  | Come parte della migrazione, il nome della rete virtuale in cscfg verrà modificato in modo da usare Azure Resource Manager ID della rete virtuale. (sottoscrizione/sottoscrizione-ID/Resource-Group/Resource-Group-Name/Resource/VNET-Name) <br><br>Per gestire la distribuzione dopo la migrazione, aggiornare la copia locale del file con estensione cscfg per iniziare a usare Azure Resource Manager ID anziché il nome della rete virtuale. <br><br>Un file con estensione cscfg che usa lo schema di denominazione precedente non supererà la convalida. 
| Rete virtuale | Migrazione della distribuzione con i ruoli in una subnet diversa. | Un servizio cloud con ruoli diversi in subnet diverse è supportato per la migrazione. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Risorse e funzionalità non disponibili per la migrazione
Si tratta di scenari principali che coinvolgono combinazioni di risorse, funzionalità e servizi cloud. L'elenco non è completo. 

| Risorsa | Passaggi successivi/soluzione | 
|---|---|
| Regole di scalabilità automatica | La migrazione passa, ma vengono eliminate le regole. [Ricreare le regole dopo la](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) migrazione nei servizi cloud (supporto esteso). | 
| Avvisi | La migrazione viene completata ma gli avvisi vengono eliminati. [Ricreare le regole dopo la](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) migrazione nei servizi cloud (supporto esteso). | 
| Gateway VPN | Rimuovere il gateway VPN prima di iniziare la migrazione e quindi ricrearlo al termine. | 
| Express Route Gateway (solo nella stessa sottoscrizione della rete virtuale) | Rimuovere il gateway Express Route prima di iniziare la migrazione e quindi ricreare il gateway al termine della migrazione. | 
| Quota  | Non viene eseguita la migrazione della quota. [Richiedere la nuova quota](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) per Azure Resource Manager prima della migrazione perché la convalida abbia esito positivo. | 
| Gruppi di affinità | Non supportata. Rimuovere tutti i gruppi di affinità prima della migrazione.  | 
| Reti virtuali che usano il [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)| Prima di eseguire la migrazione di una rete virtuale con peering a un'altra rete virtuale, eliminare il peering, migrare la rete virtuale a Gestione risorse e ricreare il peering. Ciò può causare tempi di inattività a seconda dell'architettura. | 
| Rete virtuale contenente ambienti del servizio app | Non supportato | 
| Rete virtuale contenente servizi HDInsight | Non supportata. 
| Reti virtuali che contengono distribuzioni di Gestione API | Non supportata. <br><br> Per eseguire la migrazione della rete virtuale, modificare la rete virtuale della distribuzione di gestione API. Si tratta di un'operazione senza tempi di inattività. | 
| Circuiti ExpressRoute classici | Non supportata. <br><br>È necessario eseguire la migrazione di questi circuiti a Azure Resource Manager prima di iniziare la migrazione di PaaS. Per altre informazioni, vedere [Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](../expressroute/expressroute-howto-move-arm.md). |  
| Controllo degli accessi in base al ruolo | Dopo la migrazione, l'URI della risorsa passa da `Microsoft.ClassicCompute` a `Microsoft.Compute` criteri RBAC deve essere aggiornato dopo la migrazione. | 
| Gateway applicazione | Non supportato. <br><br> Rimuovere il gateway applicazione prima di iniziare la migrazione e quindi ricreare il gateway applicazione al termine della migrazione per Azure Resource Manager | 

## <a name="unsupported-configurations--migration-scenarios"></a>Configurazioni/scenari di migrazione non supportati

| Configurazione/scenario  | Passaggi successivi/soluzione | 
|---|---|
| Migrazione di alcune distribuzioni precedenti non in una rete virtuale | Alcune distribuzioni di servizi cloud non in una rete virtuale non sono supportate per la migrazione. <br><br> 1. usare l'API Validate per verificare se la distribuzione è idonea per la migrazione. <br> 2. se idoneo, le distribuzioni verranno spostate in Azure Resource Manager in una rete virtuale con prefisso "DefaultRdfeVnet" | 
| Migrazione delle distribuzioni che contengono la distribuzione di slot di produzione e di gestione temporanea usando indirizzi IP dinamici | La migrazione di un servizio cloud a due slot richiede l'eliminazione dello slot di gestione temporanea. Una volta eliminato lo slot di staging, eseguire la migrazione dello slot di produzione come servizio cloud indipendente (supporto esteso) in Azure Resource Manager. Quindi ridistribuire l'ambiente di gestione temporanea come nuovo servizio cloud (supporto esteso) e renderlo scambiabile con il primo. | 
| Migrazione delle distribuzioni che contengono la distribuzione degli slot di produzione e di gestione temporanea usando indirizzi IP riservato | Non supportata. | 
| Migrazione della distribuzione di produzione e gestione temporanea in una rete virtuale diversa|Per la migrazione di un servizio cloud a due slot è necessario eliminare lo slot di staging. Una volta eliminato lo slot di staging, eseguire la migrazione dello slot di produzione come servizio cloud indipendente (supporto esteso) in Azure Resource Manager. Una nuova distribuzione di servizi cloud (supporto esteso) può quindi essere collegata alla distribuzione migrata con la proprietà scambiabile abilitata. È possibile riutilizzare i file delle distribuzioni della distribuzione dello slot di staging precedente per creare la nuova distribuzione scambiabile. | 
| Migrazione di un servizio cloud vuoto (servizio cloud senza distribuzione) | Non supportata. | 
| Migrazione della distribuzione contenente il plug-in Desktop remoto e le estensioni desktop remoto | Opzione 1: rimuovere il plug-in Desktop remoto prima della migrazione. Questa operazione richiede modifiche ai file di distribuzione. La migrazione verrà quindi superata. <br><br> Opzione 2: rimuovere l'estensione desktop remoto ed eseguire la migrazione della distribuzione. Dopo la migrazione, rimuovere il plug-in e installare l'estensione. Questa operazione richiede modifiche ai file di distribuzione. <br><br> Rimuovere il plug-in e l'estensione prima della migrazione. [Non è consigliabile](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) usare i plug-in per i servizi cloud (supporto esteso).| 
| Reti virtuali con distribuzione PaaS e IaaS |Non supportato <br><br> Spostare le distribuzioni PaaS o IaaS in una rete virtuale diversa. Questa operazione causerà tempi di inattività. | 
Distribuzioni di servizi cloud che usano dimensioni dei ruoli legacy, ad esempio Small o overlarge. | La migrazione verrà completata, ma le dimensioni del ruolo verranno aggiornate in modo da usare le dimensioni dei ruoli moderni. Non sono state apportate modifiche alle proprietà dei costi o degli SKU e la macchina virtuale non verrà riavviata per questa modifica. Aggiornare tutti gli elementi di distribuzione per fare riferimento a queste nuove dimensioni dei ruoli moderni. Per altre informazioni, vedere [dimensioni delle macchine virtuali disponibili](available-sizes.md)|
| Migrazione del servizio cloud in una rete virtuale diversa | Non supportato <br><br> 1. spostare la distribuzione in una rete virtuale classica diversa prima della migrazione. Questa operazione causerà tempi di inattività. <br> 2. eseguire la migrazione della nuova rete virtuale a Azure Resource Manager. <br><br> Oppure <br><br> 1. eseguire la migrazione della rete virtuale a Azure Resource Manager <br>2. spostare il servizio cloud in una nuova rete virtuale. Questa operazione causerà tempi di inattività. | 
| Servizio cloud in una rete virtuale a cui non è assegnata una subnet esplicita | Non supportata. La mitigazione comporta lo stato di trasferimento del ruolo in una subnet, che richiede un riavvio del ruolo (tempo di inattività) | 


## <a name="post-migration-changes"></a>Modifiche post-migrazione
La distribuzione di servizi cloud (versione classica) viene convertita in una distribuzione del servizio cloud (supporto esteso). Per altri dettagli, vedere la [documentazione relativa ai servizi cloud (supporto esteso)](deploy-prerequisite.md) .  

### <a name="changes-to-deployment-files"></a>Modifiche ai file di distribuzione 

Modifiche minime apportate al file. csdef e. cscfg del cliente per rendere i file di distribuzione conformi ai requisiti di Azure Resource Manager e servizi cloud (supporto esteso). Post-migrazione recupera i nuovi file di distribuzione o aggiorna i file esistenti. Questa operazione sarà necessaria per le operazioni di aggiornamento/eliminazione.  

- Rete virtuale usa l'ID di risorsa Azure Resource Manager completo anziché solo il nome della risorsa nella sezione NetworkConfiguration del file. cscfg. Ad esempio: `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Per le reti virtuali appartenenti allo stesso gruppo di risorse del servizio cloud, è possibile scegliere di aggiornare il file con estensione cscfg a usando solo il nome della rete virtuale.  

- Le dimensioni classiche come Small, large, large sono sostituite dai nomi delle nuove dimensioni, Standard_A *. È necessario modificare i nomi delle dimensioni nei nuovi nomi nel file con estensione csdef. Per ulteriori informazioni, vedere [prerequisiti per la distribuzione di servizi cloud (supporto esteso)](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Usare l'API Get per ottenere la copia più recente dei file di distribuzione. 
    - Ottenere il modello usando il [portale](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), l' [interfaccia](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)della riga di comando e l' [API REST](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 
    - Ottenere il file con estensione csdef usando [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) o l' [API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    - Ottenere il file con estensione cscfg usando [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) o l' [API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Modifiche all'automazione del cliente, alla pipeline di integrazione continua/recapito continuo, script personalizzati, dashboard personalizzati, strumenti personalizzati e così via.  

I clienti devono aggiornare gli strumenti e l'automazione per iniziare a usare le nuove API/comandi per gestire la distribuzione. Il cliente può adottare facilmente nuove funzionalità e funzionalità di Azure Resource Manager/servizi cloud (supporto esteso) nell'ambito di questa modifica. 

- Modifiche ai nomi di risorse e gruppi di risorse post-migrazione
    - Come parte della migrazione, i nomi di poche risorse come il servizio cloud, gli indirizzi IP pubblici e così via cambiano. Queste modifiche potrebbero dover essere riflesse nei file di distribuzione prima dell'aggiornamento del servizio cloud. [Altre informazioni sui nomi delle risorse cambiano](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Ricreare le regole e i criteri necessari per gestire e ridimensionare i servizi cloud 
    - Non viene eseguita la migrazione [delle regole di scalabilità automatica](configure-scaling.md) . Dopo la migrazione, ricreare le regole di scalabilità automatica.  
    - Non viene eseguita la migrazione degli [avvisi](enable-alerts.md) . Dopo la migrazione, ricreare gli avvisi.
    - Il Key Vault viene creato senza criteri di accesso. [Creare criteri appropriati](../key-vault/general/assign-access-policy-portal.md) nel Key Vault per visualizzare o gestire i certificati. I certificati saranno visibili in impostazioni nella scheda denominata Secrets (segreti).

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica della migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Eseguire la migrazione ai servizi cloud (supporto esteso) usando il [portale di Azure](in-place-migration-portal.md)
- Eseguire la migrazione ai servizi cloud (supporto esteso) con [PowerShell](in-place-migration-powershell.md)
