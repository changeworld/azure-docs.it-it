---
title: Creazione di Apache Kafka per cloud con soluzione cloud-soluzioni per partner Azure
description: Questo articolo descrive come usare la portale di Azure per creare un'istanza di Apache Kafka per il cloud Confluent.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253692"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Guida introduttiva: Introduzione a Apache Kafka per il cloud Confluent

In questa Guida introduttiva si userà il portale di Azure per creare un'istanza di Apache Kafka per il cloud Confluent.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free/).
- È necessario avere il ruolo _proprietario_ o _collaboratore_ per la sottoscrizione di Azure. L'integrazione tra Azure e Confluent può essere configurata solo dagli utenti con accesso _proprietario_ o _collaboratore_ . Prima di iniziare, [verificare di disporre dell'accesso appropriato](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Trova l'offerta

Usare il portale di Azure per trovare la Apache Kafka per l'applicazione cloud Confluent.

1. In un Web browser passare al [portale di Azure](https://portal.azure.com/) ed eseguire l'accesso.

1. Se il **Marketplace** è stato visitato in una sessione recente, selezionare l'icona dalle opzioni disponibili. In caso contrario, cercare _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Icona del Marketplace.":::

1. Dalla pagina **Marketplace** sono disponibili due opzioni in base al tipo di piano desiderato. È possibile iscriversi per un piano con pagamento in base al consumo o un piano di impegno. Il pagamento in base al consumo è disponibile pubblicamente. Il piano di impegno è disponibile per i clienti che sono stati approvati per un'offerta privata.

   - Per i clienti con **pagamento in base** al consumo, cercare _Apache Kafka nel cloud Confluent_. Selezionare l'offerta per Apache Kafka nel cloud Confluent.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Cerca nell'offerta di Azure Marketplace.":::

   - Per i clienti con **impegno** , selezionare il collegamento per **visualizzare le offerte private**. Per l'impegno è necessario iscriversi per una quantità minima di spesa. Usare questa opzione solo quando si è certi che è necessario il servizio per un periodo di tempo prolungato.

     :::image type="content" source="media/view-private-offers.png" alt-text="Visualizza le offerte private.":::

     Cercare _Apache Kafka nel cloud Confluent_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Selezionare offerta privata.":::

## <a name="create-resource"></a>Crea risorsa

Dopo aver selezionato l'offerta per Apache Kafka nel cloud Confluent, si è pronti per configurare l'applicazione.

1. Se nella sezione precedente sono state selezionate offerte private, saranno disponibili due opzioni per i tipi di piano:

    - Cloud con pagamento in base al consumo
    - Impegno per il piano di commit

   Se non sono state selezionate offerte private, sarà disponibile solo l'opzione con pagamento in base al consumo.

   Selezionare il piano da usare e selezionare **imposta + Sottoscrivi**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Configurare e sottoscrivere.":::

1. Nella pagina Crea nozioni di base **sulle risorse cloud** , specificare i valori seguenti. Al termine, selezionare **Avanti: Tag**.

    :::image type="content" source="media/setup-basics.png" alt-text="Modulo per configurare una risorsa cloud Confluent.":::

    | Proprietà | Descrizione |
    | ---- | ---- |
    | **Sottoscrizione** | Dal menu a discesa selezionare la sottoscrizione di Azure in cui eseguire la distribuzione. È necessario disporre dell'accesso _proprietario_ o _collaboratore_ . |
    | **Gruppo di risorse** | Specificare se si vuole creare un nuovo gruppo di risorse o usare un gruppo di risorse esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../azure-resource-manager/management/overview.md). |
    | **Nome organizzazione Confluent** | Assegnare un nome alla risorsa SaaS (Software as a Service). |
    | **Area** | Dal menu a discesa selezionare una delle aree seguenti: <br/><br/> Australia orientale, Canada centrale, Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Francia centrale, Europa settentrionale, Asia sudorientale, Regno Unito meridionale, Stati Uniti centro-occidentali, Europa occidentale, Stati Uniti occidentali 2 |
    | **Pianificare** | Selezionare **con pagamento in base al** consumo o **impegno**. |
    | **Termine fatturazione** | Precompilato in base al piano di fatturazione selezionato. |
    | **Prezzo** | Precompilato in base al piano Confluent selezionato. |

1. In **tag** specificare le coppie **nome** / **valore** per i tag che si desidera applicare alla risorsa. Dopo aver immesso i tag, selezionare **Verifica + crea**.

    :::image type="content" source="media/setup-tags.png" alt-text="Aggiungere i tag del progetto.":::

1. Esaminare le impostazioni fornite. Quando si è pronti, selezionare **Crea**.

1. Sono necessari alcuni minuti per creare la risorsa. È possibile visualizzare lo stato della distribuzione nelle **notifiche**. Al termine della distribuzione, selezionare la risorsa per visualizzare la pagina **Panoramica** .

    :::image type="content" source="media/deployment-status.png" alt-text="Stato della distribuzione.":::

   Se viene visualizzato un errore, vedere la [sezione relativa alla risoluzione dei problemi Apache Kafka per le soluzioni cloud confluenti](troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire la risorsa cloud Confluent](manage.md)
