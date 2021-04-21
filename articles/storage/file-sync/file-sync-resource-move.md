---
title: Sincronizzazione file di Azure spostamento delle risorse e modifiche della topologia
description: Informazioni su come spostare le risorse di sincronizzazione tra gruppi di risorse, sottoscrizioni e tenant Azure Active Directory (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796979"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Spostare Sincronizzazione file di Azure risorse in un gruppo di risorse, una sottoscrizione o un tenant AAD diverso

Questo articolo descrive come apportare modifiche al gruppo di risorse, alla sottoscrizione o al tenant di Azure Active Directory (AAD) per le risorse cloud Sincronizzazione file di Azure e gli account di archiviazione di Azure.

Quando si pianifica di apportare modifiche alle Sincronizzazione file di Azure cloud, è importante prendere in considerazione le risorse di archiviazione contemporaneamente. Sono disponibili le risorse seguenti:

**Sincronizzazione file di Azure risorse (in ordine gerarchico)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Servizio di sincronizzazione archiviazione
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Server registrato
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Gruppo di sincronizzazione
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Endpoint cloud
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Endpoint server

In Sincronizzazione file di Azure, l'unica risorsa in grado di eseguire lo spostamento è la risorsa del servizio di sincronizzazione archiviazione. Tutte le sottorisorse sono associate al relativo elemento padre e non possono essere spostate in un altro servizio di sincronizzazione archiviazione.

**Risorse di archiviazione di Azure (in ordine gerarchico)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Account di archiviazione
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Condivisione file

In Archiviazione di Azure, l'unica risorsa che può essere spostata è l'account di archiviazione. Una condivisione file di Azure, come risorsa secondaria, non può essere spostata in un account di archiviazione diverso.

## <a name="supported-combinations"></a>Combinazioni supportate

Quando si pianifica lo spostamento di una risorsa, è necessario considerare insieme l'account di archiviazione e la risorsa di Sincronizzazione file di Azure di primo livello, denominata servizio di sincronizzazione archiviazione.

Come procedura consigliata, il servizio di sincronizzazione archiviazione e gli account di archiviazione con condivisioni file di sincronizzazione devono trovarsi sempre nella stessa sottoscrizione. Queste combinazioni sono supportate:

* Il servizio di sincronizzazione archiviazione e gli account di archiviazione si trovano in **gruppi di risorse diversi** (stesso tenant di Azure)
* Il servizio di sincronizzazione archiviazione e gli account di archiviazione si trovano in **sottoscrizioni diverse** (stesso tenant di Azure)

> [!IMPORTANT]
> Tramite diverse combinazioni di spostamenti, un servizio di sincronizzazione archiviazione e gli account di archiviazione possono terminare in sottoscrizioni diverse, regolate da tenant AAD diversi. La sincronizzazione sembra funzionare, ma non è una configurazione supportata. La sincronizzazione può essere interrotta in futuro senza possibilità di tornare a una condizione di lavoro.

Quando si pianifica lo spostamento delle risorse, è necessario tenere presenti diverse considerazioni per lo spostamento all'interno dello stesso [tenant di AAD](#move-within-the-same-azure-active-directory-tenant) e il passaggio [a un tenant AAD diverso.](#move-to-a-new-azure-active-directory-tenant) Quando si spostano tenant di AAD, spostare sempre le risorse di sincronizzazione e archiviazione insieme.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Spostarsi all'interno dello stesso tenant Azure Active Directory

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Immagine che mostra il portale di Azure per una risorsa del servizio di sincronizzazione archiviazione, con il comando Sposta espanso. Mostra le opzioni di spostamento del gruppo di risorse e di spostamento della sottoscrizione." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Un modo pratico per spostare una risorsa del servizio di sincronizzazione archiviazione è usare il portale di Azure. Passare al servizio di sincronizzazione archiviazione da spostare e selezionare **Sposta** dalla barra dei comandi. Gli stessi passaggi si applicano allo spostamento di un account di archiviazione. È anche possibile spostare tutte le risorse in un gruppo di risorse in questo modo. Lo spostamento di un intero gruppo di risorse è consigliato quando si dispone del servizio di sincronizzazione archiviazione e di tutti gli account di archiviazione usati in questo gruppo di risorse.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Quando si sposta una risorsa dell'account di archiviazione, la sincronizzazione verrà interrotta immediatamente. È necessario autorizzare manualmente la sincronizzazione ad accedere agli account di archiviazione pertinenti nella nuova sottoscrizione. Nella [Sincronizzazione file di Azure di autorizzazione di accesso all'archiviazione](#azure-file-sync-storage-access-authorization) verranno forniti i passaggi necessari.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Passare a un nuovo tenant Azure Active Directory database

Le singole risorse, ad esempio un servizio di sincronizzazione archiviazione o gli account di archiviazione, non possono essere spostate da sole in un tenant AAD diverso. Solo le sottoscrizioni di Azure possono spostare i tenant di AAD. Si pensi alla struttura della sottoscrizione nel nuovo tenant di AAD. È possibile usare una sottoscrizione dedicata per Sincronizzazione file di Azure. 

1. Creare una sottoscrizione di Azure o determinarne una esistente nel tenant precedente che deve essere spostata.
1. [Eseguire uno spostamento della sottoscrizione all'interno dello stesso tenant AAD](#move-within-the-same-azure-active-directory-tenant) del servizio di sincronizzazione archiviazione e di tutti gli account di archiviazione associati.
1. La sincronizzazione verrà interrotta. Completare immediatamente lo spostamento del tenant o [ripristinare la capacità della sincronizzazione di accedere agli account di archiviazione che hanno spostato](#azure-file-sync-storage-access-authorization). È quindi possibile passare al nuovo tenant di AAD in un secondo momento.

Dopo che tutte Sincronizzazione file di Azure risorse correlate sono state sequenziate nella propria sottoscrizione, è possibile spostare l'intera sottoscrizione nel tenant di AAD di destinazione. La [guida al trasferimento della](../../role-based-access-control/transfer-subscription.md) sottoscrizione consente di pianificare ed eseguire tale trasferimento.

> [!WARNING]
> Quando si trasferisce una sottoscrizione da un tenant a un altro, la sincronizzazione viene interrotta immediatamente. È necessario autorizzare manualmente la sincronizzazione per accedere agli account di archiviazione pertinenti nella nuova sottoscrizione. La [Sincronizzazione file di Azure autorizzazione di accesso alle](#azure-file-sync-storage-access-authorization) risorse di archiviazione illustra i passaggi necessari.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Immagine che mostra il portale di Azure panoramica della sottoscrizione, con il comando Cambia directory nella parte centrale della pagina." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        È possibile avviare la migrazione dopo aver creato un piano e le autorizzazioni necessarie:
        1. Nel pannello portale di Azure passare alla sottoscrizione e al **pannello** Panoramica.
        1. Selezionare **Cambia directory**
        1. Seguire i passaggi della procedura guidata per assegnare il nuovo tenant di AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Sincronizzazione file di Azure'accesso alle risorse di archiviazione

Quando gli account di archiviazione vengono spostati in una nuova sottoscrizione o all'interno di una sottoscrizione in un nuovo tenant di Azure Active Directory (AAD), la sincronizzazione verrà interrotta. L'accesso in base al ruolo viene usato per autorizzare Sincronizzazione file di Azure accesso a un account di archiviazione e non viene eseguita la migrazione di queste assegnazioni di ruolo con le risorse.

### <a name="azure-file-sync-service-principal"></a>Sincronizzazione file di Azure'entità servizio

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Immagine che mostra il portale di Azure, la gestione delle sottoscrizioni e i provider di risorse registrati." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        L Sincronizzazione file di Azure'entità servizio deve esistere nel tenant di AAD prima di poter autorizzare l'accesso di sincronizzazione a un account di archiviazione. </br></br> Quando si crea una nuova sottoscrizione di Azure, il provider di Sincronizzazione file di Azure *microsoft.StorageSync* viene registrato automaticamente con la sottoscrizione. La registrazione del provider di risorse rende disponibile *un'entità* servizio per la sincronizzazione Azure Active Directory tenant che regola la sottoscrizione. Un'entità servizio è simile a un account utente in AAD. È possibile usare l'entità Sincronizzazione file di Azure servizio per autorizzare l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. L'unica sincronizzazione delle risorse a cui deve accedere sono gli account di archiviazione contenenti le condivisioni file che devono essere sincronizzate. *Microsoft.StorageSync* deve essere assegnato al ruolo predefinito **Lettore** e accesso ai dati nell'account di archiviazione. </br></br> Questa assegnazione viene eseguita automaticamente tramite il contesto utente dell'utente connesso quando si aggiunge una condivisione file a un gruppo di sincronizzazione o, in altre parole, si crea un endpoint cloud. Quando un account di archiviazione passa a una nuova sottoscrizione o a un tenant AAD, questa assegnazione di ruolo viene persa e deve essere ristabilita [manualmente.](#establish-sync-access-to-a-storage-account)
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Se la sottoscrizione di Azure di destinazione non è stata creata di recente, verificare che il provider di risorse *Microsoft.StorageSync* sia registrato con la sottoscrizione. In caso contrario, aggiungerlo manualmente nello stesso pannello del portale.

### <a name="establish-sync-access-to-a-storage-account"></a>Stabilire l'accesso di sincronizzazione a un account di archiviazione

[L Sincronizzazione file di Azure'entità](#azure-file-sync-service-principal) servizio deve essere usata per autorizzare l'accesso a un account di archiviazione tramite il controllo degli accessi in base al ruolo. *Microsoft.StorageSync* deve essere assegnato al ruolo predefinito **Lettore** e accesso ai dati nell'account di archiviazione. 

Questa assegnazione viene in genere eseguita automaticamente tramite il contesto utente dell'utente connesso quando si aggiunge una condivisione file a un gruppo di sincronizzazione o, in altre parole, si crea un endpoint cloud. Tuttavia, quando un account di archiviazione passa a una nuova sottoscrizione o a un tenant AAD, questa assegnazione di ruolo viene persa e deve essere ristabilita manualmente.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Immagine che mostra l'entità servizio Microsoft.StorageSync assegnata al ruolo Lettore e accesso ai dati in un account di archiviazione":::
    :::column-end:::
    :::column:::
        Nel portale di Azure passare all'account di archiviazione a cui è necessario autorizzare nuovamente l'accesso di sincronizzazione. <ol><li>Selezionare **Controllo di accesso (IAM)** nel sommario a sinistra.</li><li>Selezionare la scheda Assegnazioni di ruolo per elencare gli utenti e le applicazioni (entità servizio) che hanno accesso all'account di archiviazione.</li><li>Selezionare **Aggiungi**</li><li>Nel campo **Ruolo selezionare** **Lettore e Accesso ai dati.**</li><li>Nel campo **Seleziona digitare** *Microsoft.StorageSync*, selezionare il ruolo e fare clic su **Salva**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Passare a un'area di Azure diversa

Il Sincronizzazione file di Azure *di* sincronizzazione archiviazione e gli account di archiviazione che contengono condivisioni file sincronizzate hanno un'area di Azure in cui vengono distribuite. È possibile determinare tale area quando si crea una risorsa. L'area del servizio di sincronizzazione archiviazione e le risorse dell'account di archiviazione devono corrispondere. Queste aree non possono essere modificate in nessuno dei due tipi di risorse dopo la creazione.

L'assegnazione di un'area diversa a una risorsa è diversa da un'area di [failover,](#region-fail-over)che può essere supportata, a seconda dell'impostazione di ridondanza dell'account di archiviazione.

## <a name="region-fail-over"></a>Failover a livello di area

[Archiviazione di Azure offre opzioni di ridondanza geografica](../common/storage-redundancy.md#geo-redundant-storage) per un account di archiviazione. Queste opzioni di ridondanza possono causare problemi per gli account di archiviazione usati con Sincronizzazione file di Azure. Il motivo principale è che la replica tra aree geograficamente distanti non viene eseguita da Sincronizzazione file di Azure, ma da una tecnologia di replica di archiviazione incorporata nel sottosistema di archiviazione in Azure. Non può comprendere lo stato dell'applicazione e Sincronizzazione file di Azure è un'applicazione con file sincronizzati da e verso condivisioni file di Azure in un determinato momento. Se si sceglie una di queste opzioni di ridondanza dell'archiviazione con pagamento geografico, non si perderanno tutti i dati in un'emergenza su larga scala. Tuttavia, è necessario prevedere [la perdita di dati](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Il failover non è mai un sostituto appropriato per il provisioning delle risorse nell'area di Azure corretta. Se le risorse si mandono nell'area "errata", è necessario prendere in considerazione l'interruzione della sincronizzazione e la configurazione della sincronizzazione nelle nuove condivisioni file di Azure distribuite nell'area desiderata.

Un failover a livello di area può essere avviato da Microsoft in un evento catastrofico che rende i data center in un'area di Azure inaffiabili per un lungo periodo di tempo. La definizione del tempo di inattività che l'azienda può sostenere potrebbe essere inferiore al tempo che Microsoft è pronto a lasciare passare prima di avviare un failover a livello di area. Per una situazione di questo tipo, [i failover possono](../common/storage-initiate-account-failover.md)essere avviati anche dai clienti .

> [!IMPORTANT]
> In caso di failover, è necessario descrizione di un ticket di supporto per i servizi di sincronizzazione archiviazione che hanno effetto per il nuovo funzionamento della sincronizzazione.

## <a name="see-also"></a>Vedi anche

- [Panoramica delle guide alla migrazione di condivisione file e sincronizzazione di Azure](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Risolvere i problemi di Sincronizzazione file di Azure](file-sync-troubleshoot.md)
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)