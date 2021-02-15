---
title: Gestisci progetti Azure Migrate su larga scala
description: Informazioni su come usare efficacemente Azure Migrate sulle risorse dei clienti Delegate.
ms.date: 01/29/2021
ms.topic: how-to
ms.openlocfilehash: ed2fc8ae1a3b87a3b2501c3a390fe7a1822003e4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393582"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>Gestisci progetti Azure Migrate su larga scala con Azure Lighthouse

Questo argomento offre una panoramica di come [Azure Lighthouse](../overview.md) può aiutarti a usare [Azure migrate](../../migrate/migrate-services-overview.md) in modo scalabile in più tenant Azure Active Directory (Azure ad).

Azure Lighthouse consente ai provider di servizi di eseguire operazioni su larga scala tra più tenant contemporaneamente, rendendo più efficienti le attività di gestione.

Azure Migrate offre un hub centralizzato per la valutazione e la migrazione ad Azure dei server, dell'infrastruttura, delle applicazioni e dei dati locali. In genere, i partner che eseguono valutazioni e migrazione su larga scala per più clienti devono accedere singolarmente a ogni sottoscrizione del cliente usando il [modello di sottoscrizione CSP (Cloud Solution Provider)](/partner-center/customers-revoke-admin-privileges) o [creando un utente Guest nel tenant del cliente](../../active-directory/external-identities/what-is-b2b.md).

L'integrazione di Azure Lighthouse con Azure Migrate consente ai provider di servizi di individuare, valutare ed eseguire la migrazione di carichi di lavoro per clienti diversi su larga scala, consentendo al contempo ai clienti di avere visibilità e controllo completi dei propri ambienti. Attraverso la gestione delle risorse delegate di Azure, i provider di servizi hanno una singola visualizzazione di tutti i progetti Azure Migrate gestiti tra più tenant del cliente.

> [!NOTE]
> Tramite Azure Lighthouse, i partner possono eseguire l'individuazione, la valutazione e la migrazione per le macchine virtuali VMware locali, le macchine virtuali Hyper-V, i server fisici e le istanze di AWS/GCP. Sono disponibili due opzioni per la [migrazione delle macchine virtuali VMware](../../migrate/server-migrate-overview.md). Attualmente, è possibile utilizzare solo il metodo di migrazione basato su agenti quando si utilizza un progetto di migrazione in una sottoscrizione di un cliente delegato. la migrazione tramite la replica senza agenti non è attualmente supportata tramite l'accesso delegato all'ambito del cliente.

> [!TIP]
> Per quanto riguarda i provider di servizi e i clienti in questo argomento, queste indicazioni si applicano anche alle [aziende che usano Azure Lighthouse per gestire più tenant](../concepts/enterprise.md).

A seconda dello scenario, è possibile creare il Azure Migrate nel tenant del cliente o nel tenant di gestione. Esaminare le considerazioni riportate di seguito e determinare il modello più adatto alle esigenze di migrazione del cliente.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Creare un progetto Azure Migrate nel tenant del cliente

Una delle opzioni per l'uso di Azure Lighthouse consiste nel creare il progetto Azure Migrate nel tenant del cliente. Quando si crea un progetto di migrazione, gli utenti nel tenant di gestione possono selezionare la sottoscrizione del cliente. Dal tenant di gestione, il provider di servizi può eseguire le operazioni di migrazione necessarie. Questo può includere la distribuzione del dispositivo Azure Migrate per individuare i carichi di lavoro, la valutazione dei carichi di lavoro mediante il raggruppamento di macchine virtuali e il calcolo dei costi correlati al cloud, la verifica della conformità della macchina virtuale e l'esecuzione della migrazione.

In questo scenario, nessuna risorsa verrà creata e archiviata nel tenant di gestione, anche se i passaggi di individuazione e valutazione possono essere avviati ed eseguiti da tale tenant. Tutte le risorse, ad esempio i progetti di migrazione, i report di valutazione per i carichi di lavoro locali e le risorse migrate nella destinazione di destinazione, verranno distribuite nella sottoscrizione del cliente. Tuttavia, il provider di servizi può accedere a tutti i progetti dei clienti dal proprio tenant e dall'esperienza del portale.

Questo approccio riduce al minimo il cambio di contesto per i provider di servizi che lavorano su più clienti, consentendo al tempo stesso ai clienti di proteggere tutte le risorse nei propri tenant.

Il flusso di lavoro per questo modello sarà simile al seguente:

1. Il cliente è caricato [nel Faro di Azure](onboard-customer.md). Il ruolo predefinito collaboratore è necessario per l'identità che verrà utilizzata con Azure Migrate. Per un esempio di utilizzo di questo ruolo, vedere il modello di esempio [Delegated-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) .
1. L'utente designato accede al tenant di gestione nell'portale di Azure, quindi passa a Azure Migrate. Questo utente [Crea un progetto di Azure migrate](../../migrate/create-manage-projects.md), selezionando la sottoscrizione appropriata del cliente delegato.
1. L'utente esegue quindi i [passaggi per l'individuazione e la valutazione](../../migrate/tutorial-discover-vmware.md).

   Per le macchine virtuali VMware, prima di configurare il dispositivo, è possibile limitare l'individuazione a server vCenter Data Center, cluster, una cartella di cluster, host, una cartella di host o singole macchine virtuali. Per impostare l'ambito, assegnare le autorizzazioni per l'account usato dall'appliance per accedere al server vCenter. Questa operazione è utile se le macchine virtuali di più clienti sono ospitate nell'hypervisor. Non è possibile limitare l'ambito di individuazione di Hyper-V.

    > [!NOTE]
    > È possibile individuare e valutare le macchine virtuali VMware per la migrazione usando Azure Migrate tramite l'accesso delegato fornito da Azure Lighthouse. Per la migrazione di macchine virtuali VMware, solo il metodo basato su agenti è attualmente supportato quando si utilizza un progetto di migrazione in una sottoscrizione di un cliente delegato.

1. Quando la sottoscrizione del cliente di destinazione è pronta, procedere con la migrazione tramite l'accesso concesso da Azure Lighthouse. Il progetto di migrazione contenente i risultati della valutazione e le risorse migrate verranno creati nel tenant del cliente nella sottoscrizione di destinazione.

> [!TIP]
> Prima della migrazione, è necessario distribuire una zona di destinazione per eseguire il provisioning delle risorse dell'infrastruttura di base e preparare la sottoscrizione in cui verrà eseguita la migrazione delle macchine virtuali. Per accedere o creare alcune risorse in questa area di destinazione, il ruolo predefinito del proprietario potrebbe essere obbligatorio, che non è attualmente supportato in Azure Lighthouse. Per tali scenari, il cliente potrebbe dover fornire il ruolo accesso guest o delegare l'accesso amministratore tramite il modello di sottoscrizione CSP. Per un approccio alla creazione di zone di destinazione multi-tenant, vedere la [soluzione demo multi-tenant-Landing-Zones](https://github.com/Azure/Multi-tenant-Landing-Zones) su GitHub.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Creare un progetto Azure Migrate nel tenant di gestione

In questo scenario, le operazioni correlate alla migrazione, ad esempio individuazione e valutazione, vengono comunque eseguite dagli utenti nel tenant di gestione. Tuttavia, il progetto di migrazione e tutte le risorse rilevanti si troveranno nel tenant partner e il cliente non avrà visibilità diretta nel progetto (sebbene sia possibile condividerle con i clienti se lo si desidera). La destinazione della migrazione per ogni cliente sarà la sottoscrizione del cliente.

Questo approccio consente ai provider di servizi di avviare rapidamente i progetti di individuazione e valutazione della migrazione, astraendondo i passaggi iniziali dalle sottoscrizioni dei clienti e dai tenant.

Il flusso di lavoro per questo modello sarà simile al seguente:

1. Il cliente è caricato [nel Faro di Azure](onboard-customer.md). Il ruolo predefinito collaboratore è necessario per l'identità che verrà utilizzata con Azure Migrate. Per un esempio di utilizzo di questo ruolo, vedere il modello di esempio [Delegated-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) .
1. L'utente designato accede al tenant di gestione nell'portale di Azure, quindi passa a Azure Migrate. Questo utente [Crea un progetto Azure migrate](../../migrate/create-manage-projects.md) in una sottoscrizione appartenente al tenant di gestione.
1. L'utente esegue quindi i [passaggi per l'individuazione e la valutazione](../../migrate/tutorial-discover-vmware.md). Le macchine virtuali locali verranno individuate e valutate nel progetto di migrazione creato nel tenant di gestione e quindi migrate da tale posizione.

   Se si gestiscono più clienti nello stesso host Hyper-V, è possibile individuare tutti i carichi di lavoro contemporaneamente. Le macchine virtuali specifiche del cliente possono essere selezionate nello stesso gruppo, quindi è possibile creare una valutazione e la migrazione può essere eseguita selezionando la sottoscrizione del cliente appropriata come destinazione di destinazione. Non è necessario limitare l'ambito di individuazione ed è possibile mantenere una panoramica completa di tutti i carichi di lavoro dei clienti in un progetto di migrazione.

1. Quando si è pronti, procedere con la migrazione selezionando la sottoscrizione del cliente delegato come destinazione per la replica e la migrazione dei carichi di lavoro. Le risorse appena create saranno presenti nella sottoscrizione del cliente, mentre i dati di valutazione e le risorse relative al progetto di migrazione rimarranno nel tenant di gestione.

Nota: è necessario modificare il file dei parametri per riflettere l'ambiente prima della distribuzione https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Riconoscimento dei partner per le migrazioni dei clienti

Come membro del [Microsoft Partner Network](https://partner.microsoft.com), è possibile collegare l'ID partner con le credenziali usate per gestire le risorse dei clienti Delegate. Questo consente a Microsoft di attribuire l'influenza e i ricavi usati da Azure all'organizzazione in base alle attività eseguite per i clienti, inclusi i progetti di migrazione.

Per altre informazioni, vedere [Collegare l'ID partner per verificare l'impatto sulle risorse delegate](partner-earned-credit.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure migrate](../../migrate/migrate-services-overview.md).
- Informazioni sulle altre [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md) supportate da Azure Lighthouse.
