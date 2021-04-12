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
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029506"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Problemi noti-Azure Arc Enabled Data Services (anteprima)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Marzo 2021

### <a name="data-controller"></a>Controller dati

- È possibile creare un controller dati in modalità di connessione diretta con il portale di Azure. La distribuzione con altri strumenti di Azure Arc Enabled Data Services non è supportata. In particolare, non è possibile distribuire un controller dati in modalità di connessione diretta con uno qualsiasi degli strumenti seguenti in questa versione.
   - Azure Data Studio
   - Azure Data CLI (`azdata`)
   - Strumenti nativi Kubernetes

   [Distribuire il controller dati di Azure Arc | Modalità di connessione diretta](deploy-data-controller-direct-mode.md) spiega come creare il controller dati nel portale. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Iperscalabilità PostgreSQL abilitata per Azure Arc

- Non è supportata la distribuzione di un gruppo di server con iperscalabilità di Azure Arc abilitato in un controller di dati Arc abilitato per la modalità di connessione diretta.
- Il passaggio di un valore non valido al `--extensions` parametro durante la modifica della configurazione di un gruppo di server per consentire ad altre estensioni di reimpostare in modo errato l'elenco di estensioni abilitate è stato creato al momento della creazione del gruppo di server e impedisce all'utente di creare estensioni aggiuntive. L'unica soluzione disponibile quando si verifica questo problema consiste nell'eliminare il gruppo di server e ridistribuirlo.

## <a name="february-2021"></a>2021 febbraio

### <a name="data-controller"></a>Controller dati

- La modalità del cluster di connessione diretta è disabilitata

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Iperscalabilità PostgreSQL abilitata per Azure Arc

- Il ripristino temporizzato non è supportato per l'archiviazione NFS.
- Non è possibile abilitare e configurare l'estensione pg_cron nello stesso momento. Per questa operazione è necessario usare due comandi. Un comando per abilitarlo e un comando per configurarlo. 

   Ad esempio:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   Il primo comando richiede il riavvio del gruppo di server. Quindi, prima di eseguire il secondo comando, verificare che lo stato del gruppo di server sia passato dall'aggiornamento alla fase di preparazione. Se si esegue il secondo comando prima del completamento del riavvio, l'operazione avrà esito negativo. In tal caso, è sufficiente attendere alcuni istanti ed eseguire di nuovo il secondo comando.

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


## <a name="next-steps"></a>Passaggi successivi

> **Si desidera fare semplicemente una prova?**  
> Inizia rapidamente a usare [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) su AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.

- [Installare gli strumenti client](install-client-tools.md)
- [Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)
- [Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)
- [Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)
- [Provider di risorse per i servizi di Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Note sulla versione-Azure Arc Enabled Data Services (anteprima)](release-notes.md)
