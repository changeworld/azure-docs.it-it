---
title: Gestire la disponibilità elevata con ridondanza della zona - portale di Azure - Server flessibile di Database di Azure per MySQL
description: Questo articolo descrive come abilitare o disabilitare la disponibilità elevata con ridondanza della zona nel server flessibile di Database di Azure per MySQL tramite il portale di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: e217dcaeafd553803f5c9699ab6d7779ed755b67
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818288"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Gestire la disponibilità elevata con ridondanza della zona nel server flessibile di Database di Azure per MySQL (anteprima)

Questo articolo descrive come abilitare o disabilitare la configurazione della disponibilità elevata con ridondanza della zona nel server flessibile.

La funzionalità a disponibilità elevata effettua il provisioning della replica primaria e standby fisicamente separata in zone diverse. Per altri dettagli, vedere la [documentazione sui concetti relativi alla disponibilità elevata](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> È possibile abilitare la disponibilità elevata con ridondanza della zona solo durante la creazione di server flessibili.

Questa pagina fornisce linee guida su come abilitare o disabilitare la disponibilità elevata. Questa operazione non modifica le altre impostazioni, tra cui la configurazione della rete virtuale, le impostazioni del firewall e la conservazione dei backup. Analogamente, la disabilitazione della disponibilità elevata è un'operazione online e non influisce sulla connettività e sulle operazioni dell'applicazione.

> [!IMPORTANT]
> La disponibilità elevata con ridondanza della zona è disponibile in un set limitato di aree: Asia sud-orientale, Stati Uniti occidentali 2, Europa occidentale e Stati Uniti orientali.  

## <a name="enable-high-availability-during-server-creation"></a>Abilitare la disponibilità elevata durante la creazione del server

In questa sezione vengono forniti dettagli specifici per i campi correlati alla ha. È possibile seguire questi passaggi per distribuire la disponibilità elevata durante la creazione del server flessibile.

1.  Nella finestra [portale di Azure](https://portal.azure.com/)scegliere server flessibile e fare clic su **Crea**.  Per informazioni dettagliate su come compilare i dettagli, ad esempio **Sottoscrizione,** Gruppo di **risorse,** **Nome** server, **Area** e altri campi, vedere la documentazione relativa alla creazione del server.

2.  Fare clic sulla casella di controllo **Disponibilità elevata con ridondanza della zona** nell'opzione Disponibilità.

3.  Per modificare il calcolo e l'archiviazione predefiniti, fare clic  **su Configura server**.

4.  Se l'opzione a disponibilità elevata è selezionata, il livello burstable non sarà disponibile per la scelta. È possibile scegliere i **livelli di calcolo Utilizzo** generico o **Ottimizzato** per la memoria.

    > [!IMPORTANT]
    > La disponibilità elevata con ridondanza della zona è disponibile solo per il piano **tariffario*** Utilizzo generico _ e _ *_Ottimizzato_* per la memoria *.

5.  Selezionare le **dimensioni di calcolo** desiderate dall'elenco a discesa.

6.  Selezionare **Dimensioni di archiviazione** in GiB usando la barra temporale scorrevole e selezionare il periodo di conservazione dei **backup** compreso tra 7 e 35 giorni.   

## <a name="disable-high-availability"></a>Disabilitare la disponibilità elevata

Seguire questa procedura per disabilitare la disponibilità elevata per il server flessibile già configurato con la ridondanza della zona.

1.  [Nell'portale di Azure](https://portal.azure.com/)selezionare il server flessibile di Database di Azure per MySQL esistente.

2.  Nella pagina del server flessibile fare clic su **Disponibilità elevata** nel pannello anteriore per aprire la pagina a disponibilità elevata.

3.  Fare clic sulla casella **di controllo disponibilità elevata con ridondanza** della zona per disabilitare l'opzione e fare clic su **Salva** per salvare la modifica.

4.  Verrà visualizzata una finestra di dialogo di conferma in cui è possibile confermare la disabilitazione della ha.

5.  Fare clic **sul pulsante Disabilita disponibilità** elevata per disabilitare la disponibilità elevata.

6.  Verrà visualizzata una notifica che indica che è in corso la rimozione delle autorizzazioni della distribuzione a disponibilità elevata.


## <a name="forced-failover"></a>failover forzato

Seguire questa procedura per forzare il failover dal server flessibile primario a quello di standby

1.  Nella pagina [portale di Azure](https://portal.azure.com/)selezionare il server flessibile di Database di Azure per MySQL esistente con la funzionalità di disponibilità elevata abilitata.

2.  Nella pagina del server flessibile fare clic su **Disponibilità elevata** nel pannello anteriore per aprire la pagina a disponibilità elevata.

3.  Controllare la **zona di disponibilità primaria** e la zona di disponibilità **standby**

4.  Fare clic **su Failover forzato** per avviare la procedura di failover manuale. Un popup informa l'utente del tempo previsto per il failover a seconda del carico di lavoro corrente nel database primario e della recenza dell'ultimo checkpoint, legge il messaggio e fa clic su OK.
 
5. Verrà visualizzata una notifica che indica che il failover è in corso.

6. Al termine del failover nel server di standby, verrà visualizzata una notifica.

7. Controllare la nuova **zona di disponibilità primaria** e la zona di disponibilità **standby**.

![Come eseguire il failover forzato](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Passaggi successivi

-   Informazioni sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
