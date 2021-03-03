---
title: Azure Arc Enabled Data Services-Note sulla versione
description: Note sulla versione più recenti
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: d3eb4e4b45a13872a11515dd4f53fcbbc069a954
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686683"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Note sulla versione-Azure Arc Enabled Data Services (anteprima)

Questo articolo evidenzia funzionalità, funzionalità e miglioramenti rilasciati o migliorati di recente per i servizi dati abilitati per Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 febbraio

### <a name="new-capabilities-and-features"></a>Nuove funzionalità e funzionalità

Numero di versione dell'interfaccia della riga di comando di Azure ( `azdata` ): 20.3.1. Scarica all'indirizzo [https://aka.ms/azdata](https://aka.ms/azdata) . È possibile installare `azdata` da [Install Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Altri aggiornamenti includono:

- Istanza gestita di SQL con abilitazione di Azure Arc
   - Disponibilità elevata con Gruppi di disponibilità Always On

- Azure Data Studio per l'iperscalabilità di PostgreSQL abilitata per Azure Arc: 
   - La pagina Panoramica Mostra ora lo stato del gruppo di server per nodo
   - È ora disponibile una nuova pagina delle proprietà per visualizzare altri dettagli sul gruppo di server
   - Configurare i parametri del motore Postgres dalla pagina **parametri nodo** =

Per i problemi associati a questa versione, vedere [problemi noti-Azure Arc Enabled Data Services (anteprima)](known-issues.md)

## <a name="january-2021"></a>Gennaio 2021

### <a name="new-capabilities-and-features"></a>Nuove funzionalità e funzionalità

Numero di versione dell'interfaccia della riga di comando di Azure ( `azdata` ): 20.3.0. Scarica all'indirizzo [https://aka.ms/azdata](https://aka.ms/azdata) . È possibile installare `azdata` da [Install Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Altri aggiornamenti includono:
- Portale localizzato disponibile per 17 nuove lingue
- Modifiche minime ai file con estensione YAML di Kube-Native
- Nuove versioni di Grafana e Kibana
- Problemi con gli ambienti Python quando si usa azdata nei notebook in Azure Data Studio risolti
- L'estensione pg_audit è ora disponibile per l'iperscalabilità PostgreSQL
- Non è più necessario un ID di backup quando si esegue un ripristino completo di un database con iperscalabilità PostgreSQL
- Lo stato (stato di integrità) viene segnalato per ogni istanza di PostgreSQL che costituisce un gruppo di server

   Nelle versioni precedenti lo stato era aggregato a livello di gruppo di server e non è stato riportato a livello di nodo PostgreSQL.

- Le distribuzioni di PostgreSQL ora rispettano i parametri di dimensione del volume indicati in crea comandi
- I parametri della versione del motore vengono ora rispettati durante la modifica di un gruppo di server
- La convenzione di denominazione dei pod per Azure Arc abilitata per l'iperscalabilità di PostgreSQL è cambiata

    È ora nel formato: `ServergroupName{c, w}-n` . Ad esempio, un gruppo di server con tre nodi, un nodo coordinatore e due nodi di lavoro sono rappresentati come:
   - `Postgres01c-0` (nodo coordinatore)
   - `Postgres01w-0` (nodo del ruolo di lavoro)
   - `Postgres01w-1` (nodo del ruolo di lavoro)

## <a name="december-2020"></a>Dicembre 2020

### <a name="new-capabilities--features"></a>Nuove funzionalità & funzionalità

Numero di versione dell'interfaccia della riga di comando di Azure ( `azdata` ): 20.2.5. Scarica all'indirizzo [https://aka.ms/azdata](https://aka.ms/azdata) .

Visualizzare gli endpoint per l'iperscalabilità di SQL Istanza gestita e PostgreSQL usando l'interfaccia della riga di comando di Azure ( `azdata` ) con i `azdata arc sql endpoint list` `azdata arc postgres endpoint list` comandi e.

Modificare le richieste e i limiti delle risorse di SQL Istanza gestita (core CPU e memoria) usando Azure Data Studio.

L'iperscalabilità di PostgreSQL abilitata per Azure Arc supporta ora il ripristino temporizzato oltre al ripristino del backup completo per entrambe le versioni 11 e 12 di PostgreSQL. La funzionalità di ripristino temporizzato consente di indicare una data e un'ora specifiche per il ripristino.

La convenzione di denominazione dei pod per Azure Arc abilitata per l'iperscalabilità di PostgreSQL è cambiata. È ora nel formato: ServergroupName {r, s}-_n_. Ad esempio, un gruppo di server con tre nodi, un nodo coordinatore e due nodi di lavoro sono rappresentati come:
- `postgres02r-0` (nodo coordinatore)
- `postgres02s-0` (nodo del ruolo di lavoro)
- `postgres02s-1` (nodo del ruolo di lavoro)

### <a name="breaking-change"></a>Modifica

#### <a name="new-resource-provider"></a>Nuovo provider di risorse

Questa versione introduce un [provider di risorse](../../azure-resource-manager/management/azure-services-resource-providers.md) aggiornato chiamato `Microsoft.AzureArcData`. Prima di poter usare questa funzionalità, è necessario registrare il provider di risorse. 

Per registrare il provider di risorse: 

1. Nella portale di Azure selezionare **sottoscrizioni** 
2. Scegliere la sottoscrizione
3. In **Impostazioni** selezionare **Provider di risorse** 
4. Cercare `Microsoft.AzureArcData` e selezionare **Registra** 

È possibile esaminare i passaggi dettagliati in [tipi e provider di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Questa modifica rimuove anche tutte le risorse di Azure esistenti caricate nel portale di Azure. Per usare il provider di risorse, è necessario aggiornare il controller dati e usare l'interfaccia della riga di comando più recente `azdata` .  

### <a name="platform-release-notes"></a>Note sulla versione della piattaforma

#### <a name="direct-connectivity-mode"></a>Modalità di connettività diretta

Questa versione introduce la modalità di connettività diretta. La modalità di connettività diretta consente al controller dati di caricare automaticamente le informazioni sull'utilizzo in Azure. Come parte del caricamento dell'utilizzo, la risorsa del controller di dati Arc viene creata automaticamente nel portale, se non è già stata creata tramite il `azdata` caricamento.  

Quando si crea il controller di dati, è possibile specificare la connettività diretta. Nell'esempio seguente viene creato un controller dati `azdata arc dc create` denominato `arc` utilizzando la modalità di connettività diretta ( `connectivity-mode direct` ). Prima di eseguire l'esempio, sostituire `<subscription id>` con l'ID sottoscrizione.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Problemi noti

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

#### <a name="postgresql"></a>PostgreSQL

- L'iperscalabilità di PostgreSQL abilitata per Azure Arc restituisce un messaggio di errore non accurato quando non è possibile eseguire il ripristino fino al punto nel tempo relativo indicato. Se, ad esempio, è stato specificato un punto nel tempo per il ripristino precedente rispetto a quello dei backup, il ripristino avrà esito negativo con un messaggio di errore simile a: errore: (404). Motivo: non trovato. Corpo della risposta HTTP: {"code": 404, "internalStatus": "NOT_FOUND", "Reason": "non è stato possibile ripristinare il backup per il server...}
Quando si verifica questo problema, riavviare il comando dopo aver indicato un punto nel tempo compreso nell'intervallo di date per cui si dispone di backup. Questo intervallo verrà determinato elencando i backup e esaminando le date in cui sono state eseguite.
- Il ripristino temporizzato è supportato solo tra gruppi di server. Il server di destinazione di un'operazione di ripristino temporizzato non può essere il server da cui è stato richiesto il backup. Deve essere un gruppo di server diverso. Tuttavia, il ripristino completo è supportato per lo stesso gruppo di server.
- Quando si esegue un ripristino completo, è necessario un ID di backup. Per impostazione predefinita, se non si indica un ID di backup verrà usato il backup più recente. Questa operazione non funziona in questa versione.

## <a name="october-2020"></a>Ottobre 2020 

Numero di versione dell'interfaccia della riga di comando di Azure ( `azdata` ): 20.2.3. Scarica all'indirizzo [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Modifiche che causano un'interruzione

Questa versione introduce le modifiche di rilievo seguenti: 

* Nella definizione di risorsa personalizzata PostgreSQL (CRD) il termine `shards` viene rinominato in `workers` . Questo termine ( `workers` ) corrisponde al nome del parametro della riga di comando.

* `azdata arc postgres server delete` richiede la conferma prima di eliminare un'istanza di postgres.  Usare `--force` per ignorare la richiesta.

### <a name="additional-changes"></a>Modifiche aggiuntive

* È stato aggiunto un nuovo parametro facoltativo a `azdata arc postgres server create` chiamato `--volume-claim mounts` . Il valore è un elenco delimitato da virgole di montaggi di attestazioni del volume. Un montaggio di attestazioni volume è una coppia di tipo di volume e il nome del PVC. L'unico tipo di volume attualmente supportato è `backup` .  In PostgreSQL, quando il tipo di volume è `backup` , il PVC viene montato in `/mnt/db-backups` .  Ciò consente di condividere i backup tra le istanze di PostgreSQL in modo che sia possibile ripristinare il backup di un'istanza di PostgreSQL in un'altra istanza.

* Nuovi nomi brevi per le definizioni di risorse personalizzate di PostgreSQL: 
  * `pg11` 
  * `pg12`
* Il caricamento dei dati di telemetria fornisce agli utenti:
   * Numero di punti caricati in Azure o 
   * Se non sono stati caricati dati in Azure, verrà richiesto di riprovare.
* `azdata arc dc debug copy-logs` ora legge anche da `/var/opt/controller/log` cartella e raccoglie i log del motore PostgreSQL in Linux.
*   Visualizzare un indicatore funzionante durante la creazione e il ripristino del backup con l'iperscalabilità PostgreSQL.
* `azdata arc postrgres backup list` include ora le informazioni sulle dimensioni del backup.
* La proprietà nome amministratore di SQL Istanza gestita è stata aggiunta alla colonna destra del pannello panoramica nel portale di Azure.
* Azure Data Studio supporta la configurazione del numero di nodi del ruolo di lavoro, vCore e le impostazioni di memoria per la scalabilità di PostgreSQL. 
* L'anteprima supporta il backup/ripristino per Postgres versione 11 e 12.

## <a name="september-2020"></a>Settembre 2020

Azure Arc Enabled Data Services è stato rilasciato per l'anteprima pubblica. I servizi dati abilitati per Arc consentono di gestire servizi dati ovunque.

- Istanza gestita di SQL
- PostgreSQL Hyperscale

Per istruzioni, vedere [quali sono i servizi dati abilitati per Azure Arc?](overview.md)

## <a name="next-steps"></a>Passaggi successivi

> **Si desidera fare semplicemente una prova?**  
> Inizia rapidamente a usare [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) su AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.

- [Installare gli strumenti client](install-client-tools.md)
- [Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)
- [Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)
- [Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)
- [Provider di risorse per i servizi di Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
