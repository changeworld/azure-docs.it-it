---
title: Server del gruppo per la valutazione con Azure Migrate | Microsoft Docs
description: Viene descritto come raggruppare i server prima di eseguire una valutazione con il servizio Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780855"
---
# <a name="create-a-group-for-assessment"></a>Creare un gruppo per la valutazione

Questo articolo descrive come creare gruppi di server per la valutazione con Azure Migrate: individuazione e valutazione.

[Azure migrate](migrate-services-overview.md) consente di eseguire la migrazione ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="grouping-servers"></a>Raggruppamento di server

Si raccolgono i server in gruppi per valutare se sono adatti per la migrazione ad Azure e per ottenere le stime di dimensionamento e costo di Azure. Esistono due modi per creare i gruppi:

- Se si conoscono i server di cui è necessario eseguire la migrazione insieme, è possibile creare manualmente il gruppo in Azure Migrate.
- Se non si è certi dei server che devono essere raggruppati, è possibile usare la funzionalità di visualizzazione delle dipendenze in Azure Migrate per creare gruppi. 

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="create-a-group-manually"></a>Creazione manuale di un gruppo

È possibile creare un gruppo nello stesso momento in cui viene [creata una valutazione](how-to-create-assessment.md).

Se si desidera creare un gruppo manualmente al di fuori della creazione di una valutazione, procedere come segue:

1. Nella **panoramica**> progetto Azure migrate fare clic su **valuta ed Esegui la migrazione dei server**. In **Azure migrate: individuazione e valutazione** fare clic su **gruppi** .
    - Se non è ancora stato aggiunto lo strumento Azure Migrate: Discovery and Assessment, fare clic per aggiungerlo. [Altre informazioni](how-to-assess.md)
    - Se non è ancora stato creato un progetto di Azure Migrate, vedere [altre informazioni](./create-manage-projects.md).

    ![Selezionare i gruppi](./media/how-to-create-a-group/select-groups.png)

2. Fare clic sull'icona di **gruppo** .
3. In **Crea gruppo** specificare un nome per il gruppo e in **nome Appliance** Selezionare il dispositivo Azure migrate in uso per l'individuazione del server.
4. Dall'elenco server selezionare i server che si desidera aggiungere al gruppo > **Crea**.

    ![Creare un gruppo](./media/how-to-create-a-group/create-group.png)

È ora possibile usare questo gruppo quando si [Crea una valutazione della macchina virtuale di Azure](how-to-create-assessment.md) o [una soluzione VMware di Azure (AVS)](how-to-create-azure-vmware-solution-assessment.md) o [una valutazione di Azure SQL](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Affinare un gruppo con mapping delle dipendenze

Il mapping delle dipendenze consente di visualizzare le dipendenze tra i server. In genere si utilizza il mapping delle dipendenze quando si desidera valutare i gruppi di server con livelli di confidenza più elevati.
- Consente di controllare le dipendenze tra server prima di eseguire una valutazione. 
- Consente inoltre di pianificare in modo efficace la migrazione ad Azure, assicurando che non venga lasciato nulla e quindi evitando interruzioni delle sorprese durante la migrazione.
- È possibile individuare i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme e determinare se un sistema in esecuzione sta ancora servendo gli utenti o se è un candidato per la rimozione delle autorizzazioni anziché per la migrazione.

Se è già stato [configurato il mapping delle dipendenze](how-to-create-group-machine-dependencies.md)e si desidera affinare un gruppo esistente, procedere come segue:

1. Nella scheda **Server** , nel riquadro **Azure migrate: individuazione e valutazione** , fare clic su **gruppi**.
2. Fare clic sul gruppo che si desidera ridefinire.
    - Se non è ancora stato configurato il mapping delle dipendenze, nella colonna **dipendenze** viene visualizzato lo stato **richiesta installazione** . Per ogni server per il quale si desidera visualizzare le dipendenze, fare clic su **richiede l'installazione**. Installare un paio di agenti in ogni server prima di poter eseguire il mapping delle dipendenze del server. [Altre informazioni](how-to-create-group-machine-dependencies.md)

        ![Aggiungi mapping delle dipendenze](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se è già stato configurato il mapping delle dipendenze, nella pagina gruppo fare clic su **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.

3. Dopo aver fatto clic su **Visualizza dipendenze**, la mappa delle dipendenze del gruppo Mostra quanto segue:

    - Connessioni TCP in ingresso (client) e in uscita (Server) da e verso tutti i server del gruppo in cui sono installati gli agenti di dipendenza.
    - I server dipendenti per i quali non sono installati gli agenti di dipendenza sono raggruppati in base ai numeri di porta.
    - I server dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno del server. Espandere ogni casella Server per visualizzare i processi.
    - Proprietà del server (incluso FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella Server per visualizzare i dettagli.

4. Per visualizzare le dipendenze in un intervallo di tempo di propria scelta, modificare l'intervallo di tempo (un'ora per impostazione predefinita) specificando le date di inizio e di fine o la durata.

    > [!NOTE]
    > L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare [monitoraggio di Azure per eseguire query sui dati dipendenti](how-to-create-group-machine-dependencies.md) per un periodo di tempo più lungo.

5. Dopo aver identificato le dipendenze che si desidera aggiungere o rimuovere dal gruppo, è possibile modificare il gruppo. Usare Ctrl + clic per aggiungere o rimuovere server dal gruppo.

    - È possibile aggiungere solo i server che sono stati individuati.
    - L'aggiunta e la rimozione di server invalida le valutazioni passate per un gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare e usare il [mapping delle dipendenze](how-to-create-group-machine-dependencies.md) per creare gruppi con attendibilità elevata.