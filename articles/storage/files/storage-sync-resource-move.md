---
title: Spostamento delle risorse Sincronizzazione file di Azure e modifiche della topologia
description: Informazioni su come spostare le risorse di sincronizzazione tra i gruppi di risorse, le sottoscrizioni e i tenant di Azure Active Directory (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555556"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Spostare Sincronizzazione file di Azure risorse in un gruppo di risorse, una sottoscrizione o un tenant di AAD diversi

Questo articolo descrive come apportare modifiche al tenant di gruppo di risorse, sottoscrizione o Azure Active Directory (AAD) per le risorse del cloud Sincronizzazione file di Azure e gli account di archiviazione di Azure.

Quando si pianifica di apportare modifiche alle risorse Sincronizzazione file di Azure cloud, è importante prendere in considerazione le risorse di archiviazione nello stesso momento. Sono disponibili le risorse seguenti:

**Risorse Sincronizzazione file di Azure (in ordine gerarchico)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Servizio di sincronizzazione archiviazione
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Server registrato
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Gruppo di sincronizzazione
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Endpoint cloud
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Endpoint server

In Sincronizzazione file di Azure, l'unica risorsa che è in grado di trasferire è la risorsa del servizio di sincronizzazione archiviazione. Tutte le sottorisorse sono associate al relativo elemento padre e non possono essere spostate in un altro servizio di sincronizzazione archiviazione.

**Risorse di archiviazione di Azure (in ordine gerarchico)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Account di archiviazione
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Condivisione file

In archiviazione di Azure, l'unica risorsa che è in grado di trasferire è l'account di archiviazione. Una condivisione file di Azure, come sottorisorsa, non può essere spostata in un account di archiviazione diverso.

## <a name="supported-combinations"></a>Combinazioni supportate

Quando si pianifica uno spostamento di risorse, l'account di archiviazione e la risorsa Sincronizzazione file di Azure di primo livello, denominata *servizio di sincronizzazione archiviazione*, devono essere considerati insieme.

Come procedura consigliata, il servizio di sincronizzazione archiviazione e gli account di archiviazione con condivisioni file di sincronizzazione devono trovarsi sempre nella stessa sottoscrizione. Queste combinazioni sono supportate:

* Il servizio di sincronizzazione archiviazione e gli account di archiviazione si trovano in **gruppi di risorse diversi** (lo stesso tenant di Azure)
* Il servizio di sincronizzazione archiviazione e gli account di archiviazione si trovano in **sottoscrizioni diverse** (lo stesso tenant di Azure)

> [!IMPORTANT]
> Attraverso diverse combinazioni di spostamenti, un servizio di sincronizzazione archiviazione e gli account di archiviazione possono finire in sottoscrizioni diverse, governate da diversi tenant di AAD. La sincronizzazione sembra funzionare, ma questa non è una configurazione supportata. La sincronizzazione può arrestarsi in futuro senza la possibilità di tornare a una condizione di lavoro.

Quando si pianifica lo spostamento delle risorse, è necessario tenere presenti diverse considerazioni per [lo spostamento nello stesso tenant di AAD e lo](#move-within-the-same-azure-active-directory-tenant) spostamento in [un tenant AAD diverso](#move-to-a-new-azure-active-directory-tenant). Quando si spostano i tenant di AAD, spostare sempre le risorse di sincronizzazione e archiviazione insieme.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Spostarsi all'interno dello stesso tenant di Azure Active Directory

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Immagine che mostra il portale di Azure per una risorsa del servizio di sincronizzazione archiviazione, con il comando Move espanso. Mostra le opzioni di spostamento del gruppo di risorse e di spostamento della sottoscrizione." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Un modo pratico per spostare una risorsa del servizio di sincronizzazione archiviazione consiste nell'usare il portale di Azure. Passare al servizio di sincronizzazione archiviazione che si desidera spostare e selezionare **Sposta** dalla barra dei comandi. Gli stessi passaggi sono validi per lo stato di un account di archiviazione. È anche possibile spostare tutte le risorse in un gruppo di risorse in questo modo. Il trasferimento di un intero gruppo di risorse è consigliato quando il servizio di sincronizzazione archiviazione e tutti gli account di archiviazione usati in questo gruppo di risorse.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Quando si sposta una risorsa dell'account di archiviazione, la sincronizzazione viene arrestata immediatamente. È necessario autorizzare manualmente la sincronizzazione per accedere agli account di archiviazione rilevanti nella nuova sottoscrizione. La sezione [autorizzazione di accesso all'archiviazione sincronizzazione file di Azure](#azure-file-sync-storage-access-authorization) fornirà i passaggi necessari.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Passare a un nuovo tenant Azure Active Directory

Le singole risorse, ad esempio un servizio di sincronizzazione archiviazione o gli account di archiviazione, non possono essere spostate da soli in un tenant AAD diverso. Solo le sottoscrizioni di Azure possono spostare i tenant di AAD. Si pensi alla struttura di sottoscrizione nel nuovo tenant di AAD. È possibile usare una sottoscrizione dedicata per Sincronizzazione file di Azure. 

1. Creare una sottoscrizione di Azure (o determinarne una esistente nel vecchio tenant che dovrebbe essere spostata.
1. [Eseguire uno spostamento della sottoscrizione all'interno dello stesso tenant di AAD](#move-within-the-same-azure-active-directory-tenant) del servizio di sincronizzazione archiviazione e di tutti gli account di archiviazione associati.
1. La sincronizzazione viene arrestata. Completare immediatamente lo spostamento del tenant o [ripristinare la capacità di sincronizzazione di accedere agli account di archiviazione spostati](#azure-file-sync-storage-access-authorization). Sarà quindi possibile passare al nuovo tenant di AAD in un secondo momento.

Una volta che tutte le risorse di Sincronizzazione file di Azure correlate sono state sequestrate nella propria sottoscrizione, è possibile spostare l'intera sottoscrizione nel tenant di AAD di destinazione. La [Guida alla sottoscrizione Transfer](../../role-based-access-control/transfer-subscription.md) consente di pianificare ed eseguire un trasferimento di questo tipo.

> [!WARNING]
> Quando si trasferisce una sottoscrizione da un tenant a un altro, la sincronizzazione viene arrestata immediatamente. È necessario autorizzare manualmente la sincronizzazione per accedere agli account di archiviazione rilevanti nella nuova sottoscrizione. La sezione [autorizzazione di accesso all'archiviazione sincronizzazione file di Azure](#azure-file-sync-storage-access-authorization) fornirà i passaggi necessari.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Immagine che mostra il pannello Panoramica di portale di Azure, sottoscrizione, evidenziando il comando della barra degli strumenti cambia directory al centro, nella parte superiore della pagina." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Si è pronti per avviare la migrazione dopo avere creato un piano e le autorizzazioni necessarie:
        1. Nel portale di Azure passare al pannello sottoscrizione, **Panoramica** .
        1. Selezionare **Cambia directory**
        1. Seguire i passaggi della procedura guidata per assegnare il nuovo tenant di AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Autorizzazione di accesso all'archiviazione Sincronizzazione file di Azure

Quando gli account di archiviazione vengono spostati in una nuova sottoscrizione o vengono spostati all'interno di una sottoscrizione di un nuovo tenant di Azure Active Directory (AAD), la sincronizzazione verrà arrestata. L'accesso in base al ruolo (RBAC) viene usato per autorizzare Sincronizzazione file di Azure ad accedere a un account di archiviazione e non viene eseguita la migrazione di queste assegnazioni di ruolo con le risorse.

### <a name="azure-file-sync-service-principal"></a>Entità servizio Sincronizzazione file di Azure

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Immagine che mostra i provider di risorse registrati portale di Azure, gestione delle sottoscrizioni." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Per autorizzare l'accesso di sincronizzazione a un account di archiviazione, è necessario che l'entità servizio Sincronizzazione file di Azure esista nel tenant di AAD. </br></br> Quando si crea una nuova sottoscrizione di Azure oggi, il provider di risorse Sincronizzazione file di Azure *Microsoft. StorageSync* viene registrato automaticamente con la sottoscrizione. La registrazione del provider di risorse renderà disponibile un' *entità servizio* per la sincronizzazione nel tenant Azure Active Directory che governa la sottoscrizione. Un'entità servizio è simile a un account utente in AAD. È possibile usare l'entità servizio Sincronizzazione file di Azure per autorizzare l'accesso alle risorse tramite il controllo degli accessi in base al ruolo (RBAC). L'unica sincronizzazione delle risorse richiede l'accesso a è costituita dagli account di archiviazione contenenti le condivisioni file che dovrebbero essere sincronizzate. *Microsoft. StorageSync* deve essere assegnato al lettore di ruolo predefinito **e all'accesso ai dati** nell'account di archiviazione. </br></br> Questa assegnazione viene eseguita automaticamente tramite il contesto utente dell'utente connesso quando si aggiunge una condivisione file a un gruppo di sincronizzazione o, in altre parole, si crea un endpoint cloud. Quando un account di archiviazione si sposta in una nuova sottoscrizione o in un tenant AAD, questa assegnazione di ruolo viene persa e [deve essere ristabilita manualmente](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Se la sottoscrizione di Azure di destinazione non è stata creata di recente, verificare che il provider di risorse *Microsoft. StorageSync* sia registrato con la sottoscrizione. In caso contrario, aggiungerlo manualmente nello stesso pannello del portale.

### <a name="establish-sync-access-to-a-storage-account"></a>Stabilire l'accesso di sincronizzazione a un account di archiviazione

Per autorizzare l'accesso a un account di archiviazione tramite il controllo degli accessi in base al ruolo (RBAC), è necessario usare l' [entità servizio sincronizzazione file di Azure](#azure-file-sync-service-principal) . *Microsoft. StorageSync* deve essere assegnato al lettore di ruolo predefinito **e all'accesso ai dati** nell'account di archiviazione. 

Questa assegnazione viene in genere eseguita automaticamente tramite il contesto utente dell'utente connesso quando si aggiunge una condivisione file a un gruppo di sincronizzazione o, in altre parole, si crea un endpoint cloud. Tuttavia, quando un account di archiviazione si sposta in una nuova sottoscrizione o in un tenant di AAD, questa assegnazione di ruolo viene persa e deve essere ristabilita manualmente.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Immagine che Visualizza l'entità servizio Microsoft. StorageSync assegnata al ruolo lettura e accesso ai dati in un account di archiviazione":::
    :::column-end:::
    :::column:::
        Nella portale di Azure passare all'account di archiviazione necessario per riautorizzare l'accesso alla sincronizzazione. <ol><li>Selezionare **controllo di accesso (IAM)** nel sommario a sinistra.</li><li>Selezionare la scheda assegnazioni di ruolo per elencare gli utenti e le applicazioni (entità servizio) che hanno accesso all'account di archiviazione.</li><li>Selezionare **Aggiungi**</li><li>Nel **campo ruolo** selezionare **Reader and Data Access**.</li><li>Nel **campo Seleziona** digitare *Microsoft. StorageSync*, selezionare il ruolo e fare clic su **Salva**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Passare a un'altra area di Azure

Il servizio di *sincronizzazione archiviazione* risorse sincronizzazione file di Azure e gli account di archiviazione che contengono condivisioni file sincronizzati hanno un'area di Azure in cui vengono distribuiti. Questa area viene determinata quando si crea una risorsa. L'area del servizio di sincronizzazione archiviazione e le risorse dell'account di archiviazione devono corrispondere. Non è possibile modificare queste aree per uno dei due tipi di risorsa dopo la loro creazione.

L'assegnazione di un'area diversa a una risorsa è diversa dal [failover](#region-fail-over)di un'area, che può essere supportata, a seconda dell'impostazione di ridondanza dell'account di archiviazione.

## <a name="region-fail-over"></a>Failover area

[Archiviazione di Azure offre opzioni di ridondanza geografica](../common/storage-redundancy.md#geo-redundant-storage) per un account di archiviazione. Queste opzioni di ridondanza possono causare problemi per gli account di archiviazione usati con Sincronizzazione file di Azure. Il motivo principale è che la replica tra aree geograficamente distanti non viene eseguita da Sincronizzazione file di Azure, ma da una tecnologia di replica di archiviazione integrata nel sottosistema di archiviazione in Azure. Non è in grado di comprendere lo stato dell'applicazione e Sincronizzazione file di Azure è un'applicazione con file sincronizzati da e verso le condivisioni file di Azure in un determinato momento. Se si sceglie una di queste opzioni di ridondanza dell'archiviazione distribuite geograficamente, non si perderanno tutti i dati in caso di emergenza su larga scala. Tuttavia, è necessario [anticipare la perdita di dati](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Il failover non è mai un sostituto appropriato per il provisioning delle risorse nell'area di Azure corretta. Se le risorse si trovano nell'area "errata", è necessario prendere in considerazione l'interruzione della sincronizzazione e l'impostazione della sincronizzazione sulle nuove condivisioni file di Azure distribuite nell'area desiderata.

Un failover a livello di area può essere avviato da Microsoft in un evento irreversibile che consente di eseguire il rendering dei data center in un'area di Azure inabili per un lungo periodo di tempo. La definizione di tempi di inattività che l'azienda può sostenere potrebbe essere inferiore al tempo che Microsoft è pronto a superare prima di avviare un failover a livello di area. Per una situazione di questo tipo, [i failover possono anche essere avviati dai clienti](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> In caso di failover, è necessario archiviare un ticket di supporto per i servizi di sincronizzazione archiviazione interessati affinché la sincronizzazione funzioni nuovamente.

## <a name="see-also"></a>Vedi anche

- [Panoramica della condivisione file di Azure e delle guide alla migrazione della sincronizzazione](storage-files-migration-overview.md)
- [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)
- [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
