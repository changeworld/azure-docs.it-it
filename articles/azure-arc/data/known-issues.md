---
title: Azure Arc Enabled Data Services-problemi noti
description: Ultimi problemi noti
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: d4667e8fa3a5624dddc3cb0dd792fc73ea812332
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693108"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Problemi noti-Azure Arc Enabled Data Services (anteprima)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 febbraio


- La modalità cluster connessa è disabilitata
- L'iperscalabilità di PostgreSQL abilitata per Azure Arc restituisce un messaggio di errore non accurato quando non è possibile eseguire il ripristino fino al punto nel tempo relativo indicato. Se, ad esempio, è stato specificato un punto nel tempo per il ripristino precedente rispetto a quello dei backup, il ripristino ha esito negativo con un messaggio di errore simile al seguente: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}` . Quando si verifica questo problema, riavviare il comando dopo aver indicato un punto nel tempo compreso nell'intervallo di date per cui si dispone di backup. Per determinare questo intervallo, elencare i backup ed esaminare le date in cui sono state eseguite.
- Quando si esegue un ripristino completo, è necessario un ID di backup. Per impostazione predefinita, se non si indica un ID di backup verrà usato il backup più recente. Questa operazione non funziona in questa versione.

## <a name="introduced-prior-to-february-2021"></a>Introdotta prima del 2021 febbraio

### <a name="data-controller"></a>Controller dati

- In Azure Kubernetes Service (AKS) Kubernetes versione 1.19. x non è supportata.
- In Kubernetes 1,19 `containerd` non è supportato.
- La risorsa del controller dei dati in Azure è attualmente una risorsa di Azure. Eventuali aggiornamenti come Delete non vengono propagati al cluster kubernetes.
- I nomi delle istanze non possono contenere più di 13 caratteri
- Nessun aggiornamento sul posto per il controller dati di Azure Arc o le istanze di database.
- Le immagini del contenitore di servizi dati abilitati per Arc non sono firmate.  Potrebbe essere necessario configurare i nodi di Kubernetes per consentire il pull delle immagini del contenitore senza firma.  Se ad esempio si usa Docker come runtime del contenitore, è possibile impostare la variabile di ambiente DOCKER_CONTENT_TRUST = 0 e riavviare.  Altri runtime del contenitore hanno opzioni simili, ad esempio in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Non è possibile creare istanze gestite di SQL Azure Arc abilitate o gruppi di server con iperscalabilità PostgreSQL dalla portale di Azure.
- Per il momento, se si usa NFS, è necessario impostare `allowRunAsRoot` su `true` nel file del profilo di distribuzione prima di creare il controller dati di Azure Arc.
- Solo autenticazione dell'account di accesso di SQL e PostgreSQL.  Nessun supporto per Azure Active Directory o Active Directory.
- Per la creazione di un controller di dati in OpenShift sono necessari vincoli di sicurezza rilassati.  Per informazioni dettagliate, vedere la documentazione.
- Se si usa il motore di Azure Kubernetes Service (AKS) nell'hub Azure Stack con il controller di dati di Azure Arc e le istanze di database, l'aggiornamento a una versione di Kubernetes più recente non è supportato. Disinstallare Azure Arc data controller e tutte le istanze di database prima di aggiornare il cluster Kubernetes.
- I cluster AKS che si estendono su [più zone di disponibilità](../../aks/availability-zones.md) non sono attualmente supportati per i servizi dati abilitati per Azure Arc. Per evitare questo problema, quando si crea il cluster AKS in portale di Azure, se si seleziona un'area in cui sono disponibili le zone, deselezionare tutte le zone dal controllo di selezione. Vedere l'immagine seguente:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Deselezionare le caselle di controllo per ogni zona per specificare nessuna.":::

### <a name="postgresql"></a>PostgreSQL

- L'iperscalabilità di PostgreSQL abilitata per Azure Arc restituisce un messaggio di errore non accurato quando non è possibile eseguire il ripristino fino al punto nel tempo relativo indicato. Se, ad esempio, è stato specificato un punto nel tempo per il ripristino precedente rispetto a quello contenuto nei backup, il ripristino avrà esito negativo con un messaggio di errore simile al seguente: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Quando si verifica questo problema, riavviare il comando dopo aver indicato un punto nel tempo compreso nell'intervallo di date per cui si dispone di backup. Questo intervallo verrà determinato elencando i backup e esaminando le date in cui sono state eseguite.
- Il ripristino temporizzato è supportato solo tra gruppi di server. Il server di destinazione di un'operazione di ripristino temporizzato non può essere il server da cui è stato richiesto il backup. Deve essere un gruppo di server diverso. Tuttavia, il ripristino completo è supportato per lo stesso gruppo di server.
- Quando si esegue un ripristino completo, è necessario un ID di backup. Per impostazione predefinita, se non si indica un ID di backup verrà usato il backup più recente. Questa operazione non funziona in questa versione.

## <a name="next-steps"></a>Passaggi successivi

> **Si desidera fare semplicemente una prova?**  
> Inizia rapidamente a usare [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) su AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.

- [Installare gli strumenti client](install-client-tools.md)
- [Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)
- [Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)
- [Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)
- [Provider di risorse per i servizi di Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Note sulla versione-Azure Arc Enabled Data Services (anteprima)](release-notes.md)
