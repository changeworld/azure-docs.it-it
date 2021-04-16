---
title: Ripristino - Hyperscale (Citus) - Database di Azure per PostgreSQL - portale di Azure
description: Questo articolo descrive come eseguire operazioni di ripristino in Database di Azure per PostgreSQL - Hyperscale (Citus) tramite l'portale di Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: aebfeed055fad7c1108620ab494236640285aa1e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495042"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Ripristino temporato di un gruppo di Hyperscale (Citus) server

Questo articolo illustra le procedure dettagliate per eseguire ripresi tempormente per un gruppo Hyperscale (Citus) server usando i backup. [](concepts-hyperscale-backup.md#point-in-time-restore-pitr) È possibile ripristinare il backup meno recente o in un punto di ripristino personalizzato entro il periodo di conservazione.

## <a name="restoring-to-the-earliest-restore-point"></a>Ripristino al punto di ripristino meno recente

Seguire questa procedura per ripristinare il Hyperscale (Citus) del server al backup esistente meno recente.

1.  Nel [portale di Azure](https://portal.azure.com/)scegliere il gruppo di server da ripristinare.

2.  Fare **clic su Panoramica** nel pannello sinistro e quindi su **Ripristina**.

    > [!IMPORTANT]
    > Se il **pulsante** Ripristina non è ancora presente per il gruppo di server, aprire una supporto tecnico di Azure richiesta.

3.  La pagina di ripristino chiederà di scegliere  tra il punto di ripristino **meno** recente e un punto di ripristino personalizzato e verrà visualizzata la data meno recente.

4.  Selezionare **Punto di ripristino meno recente.**

5.  Specificare un nuovo nome di gruppo di server nel **campo Ripristina nel nuovo server.** Gli altri campi (sottoscrizione, gruppo di risorse e posizione) vengono visualizzati ma non modificabili.

6.  Fare clic su **OK**.

7.  Verrà visualizzata una notifica che indica che l'operazione di ripristino è stata avviata.

Infine, seguire le [attività post-ripristino](#post-restore-tasks).

## <a name="restoring-to-a-custom-restore-point"></a>Ripristino in un punto di ripristino personalizzato

Seguire questa procedura per ripristinare il gruppo Hyperscale (Citus) server a una data e a un'ora a scelta.

1.  Nel [portale di Azure](https://portal.azure.com/)scegliere il gruppo di server da ripristinare.

2.  Fare **clic su Panoramica** nel pannello sinistro e fare clic su **Ripristina**

    > [!IMPORTANT]
    > Se il **pulsante** Ripristina non è ancora presente per il gruppo di server, aprire una supporto tecnico di Azure richiesta.

3.  La pagina di ripristino chiederà di scegliere  tra il punto di ripristino **meno** recente e un punto di ripristino personalizzato e verrà visualizzata la data meno recente.

4.  Scegliere **Punto di ripristino personalizzato.**

5.  Selezionare data e ora per **Punto di ripristino (UTC)** e specificare un nuovo nome di gruppo di server nel **campo Ripristina in un nuovo server.** Gli altri campi (sottoscrizione, gruppo di risorse e posizione) vengono visualizzati ma non modificabili.
 
6.  Fare clic su **OK**.

7.  Verrà visualizzata una notifica che indica che l'operazione di ripristino è stata avviata.

Infine, seguire le [attività post-ripristino](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Attività successive al ripristino

Dopo un ripristino, è necessario eseguire le operazioni seguenti per ripristinare e eseguire gli utenti e le applicazioni:

* Se il nuovo server è destinato a sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server
* Assicurarsi che sia disponibile un firewall a livello di server appropriato per la connessione degli utenti. Queste regole non vengono copiate dal gruppo di server originale.
* Modificare i parametri del server PostgreSQL in base alle esigenze. I parametri non vengono copiati dal gruppo di server originale.
* Verificare che siano presenti account di accesso e autorizzazioni a livello di database appropriati.
* Configurare gli avvisi in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [backup e ripristino](concepts-hyperscale-backup.md) in Hyperscale (Citus).
* Impostare [gli avvisi suggeriti](./howto-hyperscale-alert-on-metric.md#suggested-alerts) Hyperscale (Citus) gruppi di server.
