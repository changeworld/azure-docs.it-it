---
title: Come creare un'offerta di servizio gestito in Microsoft Commercial Marketplace
description: Scopri come creare una nuova offerta di servizio gestito per Azure Marketplace usando il programma Commercial Marketplace nel centro per i partner Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918273"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Come creare un'offerta di servizio gestito per il Marketplace commerciale

Questo articolo illustra come creare un'offerta di servizio gestito per Microsoft Commercial Marketplace tramite il centro per i partner.

Per pubblicare un'offerta di servizio gestito, è necessario avere ottenuto una competenza Microsoft Gold o Silver nella piattaforma cloud. Se non è già stato fatto, leggere [pianificare un'offerta di servizio gestito per il Marketplace commerciale](./plan-managed-service-offer.md). Consente di preparare le risorse necessarie quando si crea l'offerta nel centro per i partner.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra, selezionare **Commercial Marketplace**  >  **Overview**.
3. Nella scheda Panoramica selezionare **+ nuovo offerta**  >  **servizio gestito**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Immagine del menu di spostamento di sinistra.":::

4. Nella finestra di dialogo **nuova offerta** immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account. Questo ID è visibile nell'URL dell'elenco di Marketplace commerciale e nei modelli di Azure Resource Manager, se applicabile. Se ad esempio si immette test-offer-1 in questa casella, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Ogni offerta nell'account deve avere un ID offerta univoco.
    * Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri.
    * Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

5. Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner. Non è visibile negli archivi online ed è diverso dal nome dell'offerta indicato ai clienti.
6. Per generare l'offerta e continuare, selezionare **Crea**.

## <a name="configure-lead-management"></a>Configurare la gestione dei lead

Connetti il tuo sistema CRM (Customer Relationship Management) all'offerta del Marketplace commerciale per ricevere le informazioni di contatto del cliente quando un cliente esprime interesse per il servizio di consulenza. È possibile modificare questa connessione in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Per configurare la gestione dei lead nel centro per i partner:

1. Nel centro per i partner passare alla scheda **installazione offerta** .
2. In **Lead cliente** selezionare il collegamento **Connetti** .
3. Nella finestra di dialogo **Dettagli connessione** selezionare una destinazione principale nell'elenco.
4. Completare i campi visualizzati. Per i passaggi dettagliati, vedere gli articoli seguenti:

    * [Configurare l'offerta per inviare lead alla tabella di Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configurare l'offerta per inviare lead a dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (in precedenza Dynamics CRM online)
    * [Configurare l'offerta per inviare lead all'endpoint HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configurare l'offerta per inviare lead a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configurare l'offerta per inviare lead a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Per convalidare la configurazione specificata, selezionare il **collegamento convalida**.
6. Una volta configurati i dettagli della connessione, selezionare **Connetti**.
7. Selezionare **Salva bozza**.

Dopo aver inviato l'offerta per la pubblicazione nel centro per i partner, la connessione verrà convalidata e verrà inviato un responsabile del test. Quando si visualizza l'anteprima dell'offerta prima che venga avviata, testare la connessione del lead provando ad acquistare l'offerta nell'ambiente di anteprima.

> [!TIP]
> Assicurarsi che la connessione alla destinazione dei clienti potenziali rimanga aggiornata, in modo da non perdere alcun cliente potenziale.

## <a name="configure-offer-properties"></a>Configurare le proprietà dell'offerta

Nella pagina delle proprietà dell'offerta nel centro per i partner verranno definite le categorie applicabili all'offerta e ai contratti legali. Queste informazioni assicurano che il servizio gestito venga visualizzato correttamente nello Store online e offerto al set di clienti corretto.

### <a name="select-a-category"></a>Selezionare una categoria

In **categorie** selezionare almeno una e fino a cinque categorie per raggruppare l'offerta nelle aree di ricerca del Marketplace commerciale appropriate.

### <a name="provide-terms-and-conditions"></a>Specificare i termini e le condizioni

In **Legal** specificare i termini e le condizioni per questa offerta. I clienti dovranno accettarli prima di usare l'offerta. È anche possibile specificare l'URL in cui sono disponibili i termini e le condizioni.

Prima di continuare, selezionare **Salva bozza**.

## <a name="next-step"></a>Passaggio successivo

* [Configurare l'elenco di offerte del servizio gestito](./create-managed-service-offer-listing.md)