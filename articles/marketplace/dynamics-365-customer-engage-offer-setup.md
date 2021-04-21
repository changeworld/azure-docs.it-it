---
title: Creare un'offerta Dynamics 365 for Customer Engagement & PowerApps Microsoft AppSource (Azure Marketplace).
description: Creare un'offerta Dynamics 365 for Customer Engagement & PowerApps Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820504"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Come creare un'offerta Di Dynamics 365 for Customer Engagement & PowerApps

Questo articolo descrive come creare un'offerta Dynamics 365 for Customer Engagement & PowerApps. Tutte le offerte per Dynamics 365 passano attraverso il processo di certificazione. mentre il percorso di valutazione consente agli utenti di distribuire la soluzione in un ambiente Dynamics 365 reale.

Prima di iniziare, creare un account marketplace commerciale in [Partner Center](partner-center-portal/create-account.md) e assicurarsi che sia registrato nel programma del marketplace commerciale.

## <a name="before-you-begin"></a>Prima di iniziare

Rivedere [Pianificare un'offerta Dynamics 365.](marketplace-dynamics-365.md) Verranno illustrati i requisiti tecnici per questa offerta e verranno elencate le informazioni e gli asset necessari al momento della creazione.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 for Customer Engagement e PowerApps**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Mostra le opzioni di menu del riquadro sinistro e il pulsante &quot;Nuova offerta&quot; con Customer Engagement selezionato.":::

> [!IMPORTANT]
> Dopo la pubblicazione di un'offerta, tutte le modifiche apportate in Partner Center vengono visualizzate Microsoft AppSource solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre un'offerta dopo la modifica.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta e nei modelli Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. L'ID può includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se l'ID editore è `testpublisherid` e si immette **test-offer-1,** l'indirizzo Web dell'offerta sarà `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato in AppSource. È diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Questo nome non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta. Partner Center apre la **pagina Configurazione dell'offerta.**

## <a name="alias"></a>Alias

Immettere un nome descrittivo che verrà utilizzato per fare riferimento a questa offerta solo all'interno Partner Center. L'alias dell'offerta (precompilato con ciò che è stato immesso al momento della creazione dell'offerta) non verrà usato nel marketplace ed è diverso dal nome dell'offerta visualizzato ai clienti. Se si vuole aggiornare il nome dell'offerta in un secondo momento, vedere la pagina [Presentazione dell'offerta.](dynamics-365-customer-engage-offer-listing.md)

## <a name="setup-details"></a>Dettagli di configurazione

Per **How do you want potential customers to interact with this listing offer?**(Come si vuole che i potenziali clienti interagiscano con questa offerta di inserzione?) selezionare l'opzione che si vuole usare per questa offerta.

- **Scaricalo subito (gratuito): è** possibile elencare gratuitamente l'offerta ai clienti.
- **Versione di valutazione gratuita (inserzione):** consente di elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

    > [!NOTE]
    > I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

- **Contattami:** raccogliere le informazioni di contatto del cliente connettendo il sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Clienti potenziali](#customer-leads).

## <a name="test-drive"></a>Test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti, offrendo loro l'accesso a un ambiente preconfigurato per un numero fisso di ore. L'offerta test drive comporta un aumento del tasso di conversione e genera lead altamente qualificati. Per altre informazioni, iniziare con [Che cos'è un test drive?](what-is-test-drive.md).

> [!TIP]
> Un test drive è diverso da una versione di valutazione gratuita. È possibile offrire una versione test drive, una versione di valutazione gratuita o entrambe. Entrambi offrono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e auto-guidata delle funzionalità e dei vantaggi principali del prodotto dimostrati in uno scenario di implementazione reale.

Per abilitare un test drive, selezionare la casella di controllo **Abilita** un test drive e selezionare il **tipo di** test drive . L'applicazione verrà configurata test drive un secondo momento. Con test drive, è anche necessario configurare l'offerta in un sistema CRM per i lead dei clienti (vedere la sezione successiva). Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

## <a name="customer-leads"></a>Clienti potenziali

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Per altre informazioni, vedere [Lead dei clienti dell'offerta del marketplace commerciale.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di spostamento a sinistra, **Proprietà**.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le proprietà dell'offerta](dynamics-365-customer-engage-properties.md)
- [Procedure consigliate per le inserzioni di offerte](gtm-offer-listing-best-practices.md)