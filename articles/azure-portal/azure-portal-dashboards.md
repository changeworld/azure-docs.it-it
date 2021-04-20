---
title: Creare un dashboard nella portale di Azure
description: Questo articolo descrive come creare e personalizzare un dashboard nel portale di Azure.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726075"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Creare un dashboard nella portale di Azure

I dashboard sono una visualizzazione mirata e organizzata delle risorse cloud nel portale di Azure. Usare i dashboard come area di lavoro in cui è possibile monitorare le risorse e avviare rapidamente attività per le operazioni quotidiane. Creare dashboard personalizzati basati su progetti, attività o ruoli utente, ad esempio.

Il portale di Azure fornisce un dashboard predefinito come punto di partenza. È possibile modificare il dashboard predefinito e creare e personalizzare dashboard aggiuntivi.

> [!NOTE]
> Ogni utente può creare fino a 100 dashboard privati. Se si [pubblica e si condivide il dashboard,](azure-portal-dashboard-share-access.md)questo verrà implementato come risorsa di Azure nella sottoscrizione e non verrà conteggiato per questo limite.

Questo articolo descrive come creare un nuovo dashboard e personalizzarlo. Per informazioni sulla condivisione dei dashboard, vedere Condividere dashboard di Azure usando il controllo [degli accessi in base al ruolo di Azure.](azure-portal-dashboard-share-access.md)

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard

Questo esempio illustra come creare un nuovo dashboard privato con un nome assegnato. Tutti i dashboard sono privati al momento della creazione, anche se è possibile scegliere di pubblicare e condividere il dashboard con altri utenti dell'organizzazione, se lo si desidera.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu portale di Azure selezionare **Dashboard.** La visualizzazione predefinita potrebbe essere già impostata sul dashboard.

    ![Screenshot dell'portale di Azure con dashboard selezionato.](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selezionare **Nuovo dashboard** e quindi Dashboard **vuoto.**

    ![Screenshot delle opzioni del nuovo dashboard.](./media/azure-portal-dashboards/create-new-dashboard.png)

    Questa azione apre **La raccolta riquadri,** da cui è possibile selezionare i riquadri, e una griglia vuota in cui si disporranno i riquadri.

1. Selezionare il **testo Dashboard personale** nell'etichetta del dashboard e immettere un nome che consente di identificare facilmente il dashboard personalizzato.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Screenshot di una griglia vuota con raccolta riquadri.":::

1. Per salvare il dashboard così come è, selezionare **Fine personalizzazione nell'intestazione** di pagina. In caso contrario, passare alla sezione successiva per aggiungere riquadri e salvare il dashboard.

La visualizzazione dashboard mostra ora il nuovo dashboard. Selezionare la freccia accanto al nome del dashboard per visualizzare i dashboard disponibili. L'elenco può includere dashboard creati e condivisi da altri utenti.

## <a name="edit-a-dashboard"></a>Modificare un dashboard

A questo punto, è possibile modificare il dashboard per aggiungere, ridimensionare e disporre i riquadri che rappresentano le risorse di Azure.

### <a name="add-tiles-from-the-tile-gallery"></a>Aggiungere riquadri dalla raccolta riquadri

Per aggiungere riquadri a un dashboard, seguire questa procedura:

1. Selezionare ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **l'icona modifica Modifica** nell'intestazione di pagina del dashboard.

    ![Screenshot del dashboard con l'opzione Modifica evidenziata.](./media/azure-portal-dashboards/dashboard-edit.png)

1. Esplorare la **raccolta riquadri** o usare il campo di ricerca per trovare un determinato riquadro. Selezionare il riquadro da aggiungere al dashboard.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Screenshot della raccolta riquadri.":::

1. Selezionare **Aggiungi** per aggiungere il riquadro al dashboard con le dimensioni e la posizione predefinite. In caso contrario, trascinare il riquadro nella griglia e posizionarlo nel punto desiderato. Aggiungere i riquadri desiderati, ma ecco un paio di idee:

    - Aggiungere **Tutte le** risorse per visualizzare le risorse già create.

    - Se si lavora con più di  un'organizzazione, aggiungere il riquadro Identità organizzazione al dashboard per mostrare chiaramente a quale organizzazione appartengono le risorse.

1. Se si desidera, ridimensionare il riquadro trascinando e rilasciando l'angolo inferiore destro del riquadro.

1. Per salvare le modifiche, selezionare **Salva nell'intestazione** di pagina. È anche possibile visualizzare in anteprima le modifiche senza salvare selezionando **Anteprima** nell'intestazione di pagina. Nella schermata di anteprima  è possibile selezionare Salva per mantenere  le **modifiche,** Ignora per rimuoverle o Modifica per tornare alle opzioni di modifica e apportare altre modifiche.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Screenshot delle opzioni Anteprima, Salva ed Elimina.":::

### <a name="pin-content-from-a-resource-page"></a>Aggiungere contenuto da una pagina della risorsa

Un altro modo per aggiungere riquadri al dashboard è direttamente da una pagina delle risorse.

Molte pagine di risorse includono un'icona a forma di puntina nella barra dei comandi. Se si seleziona questa icona, è possibile aggiungere un riquadro che rappresenta la pagina di origine a un dashboard esistente o a un nuovo dashboard creato.

![Screenshot della barra dei comandi della pagina con l'icona aggiungi](./media/azure-portal-dashboards/dashboard-pin-blade.png)

In alcuni casi, un'icona a forma di segnaposto può essere visualizzata anche da contenuto specifico all'interno di una pagina, il che significa che è possibile aggiungere un riquadro per tale contenuto specifico anziché per l'intera pagina.

### <a name="resize-or-rearrange-tiles"></a>Ridimensionare o ridisporre i riquadri

Per modificare le dimensioni di un riquadro o per ridisporre i riquadri in un dashboard, seguire questa procedura:

1. Selezionare ![ Modifica icona Modifica ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **nell'intestazione** di pagina.

1. Selezionare il menu di scelta rapida nell'angolo superiore destro di un riquadro. Scegliere quindi le dimensioni del riquadro. I riquadri che supportano qualsiasi dimensione includono anche un "handle" nell'angolo inferiore destro che consente di trascinare il riquadro fino alle dimensioni desiderate.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Screenshot del dashboard con il menu dimensioni riquadro aperto.":::

1. Selezionare un riquadro e trascinarlo in una nuova posizione nella griglia per disporre il dashboard.

### <a name="additional-tile-configuration"></a>Configurazione aggiuntiva del riquadro

Alcuni riquadri potrebbero richiedere più configurazione per visualizzare le informazioni desiderate. Ad esempio, il **riquadro Grafico** metriche deve essere configurato per visualizzare una metrica da Monitoraggio di Azure. È anche possibile personalizzare i dati del riquadro per eseguire l'override delle impostazioni predefinite dell'ora del dashboard.

Qualsiasi riquadro che deve essere configurato visualizza un banner fino a quando non si personalizza il riquadro. Per il **grafico Metriche**, il banner è Modifica **in Metriche**. Per personalizzare il riquadro:

1. Nell'intestazione di pagina selezionare **Salva per** uscire dalla modalità di modifica.

1. Selezionare il banner e quindi eseguire la configurazione richiesta.

    ![Screenshot del riquadro che richiede la configurazione](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Un riquadro markdown consente di visualizzare contenuto statico personalizzato nel dashboard. Possono trattarsi di istruzioni di base, un'immagine, un set di collegamenti ipertestuali o anche informazioni di contatto. Per altre informazioni sull'uso di un riquadro markdown, vedere Usare un riquadro markdown nei [dashboard di Azure per visualizzare il contenuto personalizzato.](azure-portal-markdown-tile.md)

### <a name="customize-tile-data"></a>Personalizzare i dati dei riquadri

I dati nel dashboard mostrano automaticamente l'attività delle ultime 24 ore. Per visualizzare un intervallo di tempo diverso solo per questo riquadro, seguire questa procedura:

1. Selezionare **Personalizza dati riquadro** dal menu di scelta rapida o dal filtro icona del filtro ![ ](./media/azure-portal-dashboards/dashboard-filter.png) nell'angolo superiore sinistro del riquadro.

    ![Screenshot del menu di scelta rapida del riquadro.](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Selezionare la casella di controllo **Sostituisci le impostazioni dell'ora del dashboard a livello di riquadro.**

    ![Screenshot della finestra di dialogo per configurare le impostazioni relative all'ora del riquadro.](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Scegliere l'intervallo di tempo da visualizzare per questo riquadro. È possibile scegliere tra gli ultimi 30 minuti e gli ultimi 30 giorni o definire un intervallo personalizzato.

1. Scegliere la granularità temporale da visualizzare. È possibile visualizzare ovunque da incrementi di un minuto a un mese.

1. Selezionare **Applica**.

## <a name="delete-a-tile"></a>Eliminare un riquadro

Per rimuovere un riquadro da un dashboard, eseguire una delle operazioni seguenti:

- Selezionare il menu di scelta rapida nell'angolo superiore destro del riquadro, quindi **selezionare Rimuovi dal dashboard.**

- Selezionare ![ l'icona modifica ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifica per** accedere alla modalità di personalizzazione. Passare il puntatore del mouse nell'angolo superiore destro del riquadro, quindi selezionare l'icona elimina icona ![ elimina per rimuovere il riquadro dal ](./media/azure-portal-dashboards/dashboard-delete-icon.png) dashboard.

   ![Screenshot che mostra come rimuovere il riquadro dal dashboard.](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonare un dashboard

Per usare un dashboard esistente come modello per un nuovo dashboard, seguire questa procedura:

1. Assicurarsi che nella visualizzazione dashboard sia visualizzato il dashboard che si vuole copiare.

1. Nell'intestazione di pagina selezionare clona ![ icona ](./media/azure-portal-dashboards/dashboard-clone.png) **Clona**.

1. Viene aperta una copia del dashboard denominata Clone of your dashboard name **(Clona** *del nome del dashboard)* in modalità di modifica. Usare i passaggi precedenti in questo articolo per rinominare e personalizzare il dashboard.

## <a name="publish-and-share-a-dashboard"></a>Pubblicare e condividere un dashboard

Quando si crea un dashboard, è privato per impostazione predefinita, il che significa che l'utente è l'unico a poterlo vedere. Per rendere disponibili i dashboard ad altri utenti, è possibile pubblicarli e condividerli. Per altre informazioni, vedere [Condividere i dashboard di Azure tramite controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Aprire un dashboard condiviso

Per trovare e aprire un dashboard condiviso, seguire questa procedura:

1. Selezionare la freccia accanto al nome del dashboard.

1. Selezionare dall'elenco visualizzato di dashboard. Se il dashboard da aprire non è elencato:

    1. selezionare **Sfoglia tutti i dashboard.**

        ![Screenshot del menu di selezione del dashboard](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Nel campo **Tipo** selezionare **Dashboard condivisi.**

        ![Screenshot del menu di selezione di tutti i dashboard](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selezionare una o più sottoscrizioni. È anche possibile immettere testo per filtrare i dashboard in base al nome.

    1. Selezionare un dashboard dall'elenco dei dashboard condivisi.

## <a name="delete-a-dashboard"></a>Eliminare un dashboard

Per eliminare definitivamente un dashboard privato o condiviso, seguire questa procedura:

1. Selezionare il dashboard da eliminare dall'elenco accanto al nome del dashboard.

1. Selezionare ![ l'icona ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Elimina Elimina** dall'intestazione di pagina.

1. Per un dashboard privato, selezionare **OK nella** finestra di dialogo di conferma per rimuovere il dashboard. Per un dashboard condiviso, nella finestra di dialogo di conferma selezionare la casella di controllo per confermare che il dashboard pubblicato non sarà più visualizzabile da altri utenti. Quindi selezionare **OK**.

    ![Screenshot della conferma dell'eliminazione.](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Ripristinare un dashboard eliminato

Se si è nel cloud di Azure globale e si elimina un _dashboard_ pubblicato nel portale di Azure, è possibile ripristinare il dashboard entro 14 giorni dall'eliminazione. Per altre informazioni, vedere [Ripristinare un dashboard eliminato nel portale di Azure](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Passaggi successivi

- [Condividere i dashboard di Azure usando il controllo degli accessi in base al ruolo di Azure](azure-portal-dashboard-share-access.md)
- [Creare i dashboard di Azure a livello di codice](azure-portal-dashboards-create-programmatically.md)
