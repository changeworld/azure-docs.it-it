---
title: Che cos'è un test drive? Marketplace commerciale Microsoft
description: Spiegazione della funzionalità test drive Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818891"
---
# <a name="what-is-a-test-drive"></a>Che cos'è un test drive?

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, generando lead altamente qualificati e determinando una conversione maggiore. Un test drive porta il prodotto alla vita in uno scenario di implementazione reale. I clienti che provano il prodotto stanno dimostrando una chiara intenzione di acquistare una soluzione simile. Usare questa funzionalità a proprio vantaggio seguendo i lead più avanzati.

Anche i clienti traggono vantaggio da test drive. Consentendo loro di provare prima il prodotto, si riduce l'attrito del processo di acquisto. Inoltre, test drive viene eseguito il provisioning preliminare, ad esempio i clienti non devono scaricare, configurare o configurare il prodotto.

## <a name="how-does-it-work"></a>Come funziona?

I test drive sono istanze gestite che avviano la soluzione o l'applicazione su richiesta per i clienti che la richiedono. Una volta assegnata test drive'istanza, è disponibile per l'uso da parte del cliente per un periodo impostato. Al termine del periodo, viene eliminato per creare spazio per un altro cliente.

Gli editori gestiscono e configurano le impostazioni test drive in Partner Center. I dettagli della configurazione tecnica variano a seconda del tipo di offerta. Per istruzioni dettagliate, vedere la configurazione [tecnica del test drive.](./test-drive-technical-configuration.md)

I potenziali clienti individuano test drive come CTA nell'offerta in [AppSource.](https://appsource.microsoft.com/en-US/) Forniscono le informazioni di contatto e accettano le condizioni e l'informativa sulla privacy dell'offerta, quindi ottengono l'accesso all'ambiente preconfigurato per provarlo per un periodo fisso. I clienti ricevono una versione di valutazione manuale e auto-guidata delle funzionalità e dei vantaggi principali del prodotto e si riceve un vantaggio prezioso.

## <a name="types-of-test-drives"></a>Tipi di test drive

Nel marketplace commerciale sono disponibili test drive diversi per le offerte selezionate a seconda del tipo di prodotto, scenario e marketplace in uso:

- Azure Resource Manager
    - Applicazioni Azure
    - SaaS
    - Macchine virtuali
- Hosted test drive
    - Dynamics 365 for Business Central (attualmente non supportato)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- App per la logica (solo in modalità di supporto)
- Power BI

Per informazioni dettagliate sulla configurazione di uno di questi test drive, vedere [Configurazione tecnica del test drive.](./test-drive-technical-configuration.md) 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Questo modello di distribuzione contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usano solo risorse di Azure. Il Azure Resource Manager test drive è disponibile per questi tipi di offerta: 

- Applicazioni di Azure
- SaaS
- Macchine virtuali

>[!NOTE]
>Questa è l'unica opzione test drive per le offerte di macchine virtuali e applicazioni Azure.

### <a name="hosted-test-drive-recommended"></a>Hosted test drive (scelta consigliata)

Un'test drive host elimina la complessità della configurazione consentendo a Microsoft di ospitare e gestire il servizio che esegue il provisioning e il deprotezione test drive utenti. Se si dispone di un'offerta Microsoft AppSource, creare il test drive per connettersi a un'istanza di Dynamics AX/CRM. È possibile usare i tipi di offerte di AppSource seguenti:

- Usare [Dynamics 365 for Customer Engagement](dynamics-365-customer-engage-offer-setup.md) e Power Apps per un sistema di Customer Engagement, ad esempio vendite, servizio, servizio di progetto e servizio sul campo.
- Usare [Dynamics 365 for Operations per](partner-center-portal/create-new-operations-offer.md) un sistema di pianificazione delle risorse aziendali finance and operations, ad esempio finanza, operazioni e produzione, supply chain.

### <a name="logic-app-test-drive"></a>App per la logica test drive

Questo tipo di test drive non è ospitato da Microsoft e usa modelli Azure Resource Manager (ARM) per i tipi di offerta Dynamics AX/CRM. Sarà necessario eseguire il modello arm per creare le risorse necessarie nella sottoscrizione di Azure. Il test drive dell'app per la logica è attualmente solo in modalità di supporto e non è consigliato da Microsoft. Per informazioni dettagliate sulla configurazione di un test drive dell'app per la logica, vedere Configurazione tecnica del [test drive.](./test-drive-technical-configuration.md)

### <a name="power-bi-test-drive"></a>Test drive di Power BI

Si tratta semplicemente di un collegamento incorporato a un dashboard personalizzato. Qualsiasi prodotto che dimostra solo un oggetto visivo Power BI interattivo deve usare questo tipo di test drive.

## <a name="transforming-examples"></a>Esempi di trasformazione

Il processo di trasformazione di un'architettura di risorse in un test drive può essere ostentare. Vedere questi esempi di come trasformare al meglio le architetture correnti.

[Trasformare un modello di sito Web in un test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Trasformare un modello di macchina virtuale in un test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Trasformare un modello Resource Manager esistente in un test drive](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generare lead dal test drive

Un marketplace commerciale test drive è un ottimo strumento per i marketer. È consigliabile incorporarlo nelle attività di go-to-market quando si avvia per generare più lead per l'azienda. Per indicazioni dettagliate, vedere [Clienti potenziali dell'offerta del marketplace commerciale.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)

Se si chiude un accordo con un test drive cliente potenziale, assicurarsi di registrarlo in [Microsoft Partner Sales Connect.](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) Inoltre, è molto importante sapere che il cliente ha vinto quando un test drive ha svolto un ruolo.

## <a name="other-resources"></a>Altre risorse

Risorse aggiuntive per i test drive:

- [Procedure consigliate per i test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Panoramica](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (file con estensione pdf, assicurarsi che il blocco popup sia disattivato)

## <a name="next-step"></a>Passaggio successivo

- [Configurazione tecnica del test drive](test-drive-technical-configuration.md)