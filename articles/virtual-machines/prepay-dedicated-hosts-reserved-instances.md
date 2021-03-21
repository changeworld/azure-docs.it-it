---
title: Pagamento anticipato per gli host dedicati di Azure per risparmiare denaro
description: Informazioni su come acquistare istanze riservate degli host dedicati di Azure per risparmiare sui costi di calcolo.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 2f90d3698156e27780bc57e0ac9355b6811d20d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607416"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Salva i costi con le prenotazioni host dedicato di Azure

Quando si esegue il commit in un'istanza riservata di host dedicati di Azure, è possibile risparmiare denaro. Lo sconto relativo alla prenotazione viene applicato automaticamente al numero di host dedicati in esecuzione che corrispondono agli attributi e all'ambito di prenotazione. Non è necessario assegnare una prenotazione a un host dedicato per ottenere gli sconti. Un acquisto di istanze riservate copre solo la parte di calcolo dell'utilizzo e include i costi di licenza software. Vedere la [Panoramica degli host dedicati di Azure per le macchine virtuali](./dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Determinare lo SKU host dedicato corretto prima di acquistare


Prima di acquistare una prenotazione, è necessario determinare quale host dedicato è necessario. Uno SKU viene definito per un host dedicato che rappresenta la serie di macchine virtuali e il tipo. 

Per identificare la serie di macchine virtuali, è innanzitutto necessario passare alle dimensioni supportate per la [macchina virtuale Windows](./sizes.md) o [Linux](./sizes.md) .

Controllare quindi se è supportato in host dedicati di Azure. La pagina dei [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing) include l'elenco completo di SKU host dedicati, le informazioni sulla CPU e varie opzioni di prezzo, incluse le istanze riservate.

È possibile trovare diversi SKU che supportano la serie di macchine virtuali selezionata (con tipi diversi). Identificare lo SKU migliore confrontando la capacità dell'host (numero di vCPU). Si noti che sarà possibile applicare la prenotazione a più SKU host dedicati che supportano le stesse serie di macchine virtuali (ad esempio DSv3_Type1 e DSv3_Type2) ma non tra diverse serie di macchine virtuali (come DSv3 e ESv3).



## <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione degli acquisti

Le istanze riservate sono disponibili per la maggior parte delle dimensioni host dedicate, con alcune eccezioni.

Gli sconti per le prenotazioni non si applicano a quanto segue:

- **Cloud** : le prenotazioni non sono disponibili per l'acquisto nelle aree Germania o Cina.

- **Quota insufficiente** : una prenotazione con ambito per una singola sottoscrizione deve avere una quota di vCPU disponibile nella sottoscrizione per la nuova istanza riservata. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota di 10 vCPU per la serie DSv3, non è possibile acquistare un host dedicato di prenotazione che supporti questa serie. Il controllo della quota per le prenotazioni include le macchine virtuali e gli host dedicati già distribuiti nella sottoscrizione. Per risolvere il problema, è possibile [creare una richiesta di aumento della quota](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

- **Restrizioni di capacità** : in rari casi, Azure limita l'acquisto di nuove prenotazioni per sottoinsiemi di SKU host dedicati, a causa della capacità ridotta in un'area.

## <a name="buy-a-reservation"></a>Acquistare una prenotazione

È possibile acquistare un'istanza riservata di un'istanza host dedicata di Azure nell' [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Usare [pagamenti anticipati o mensili](../cost-management-billing/reservations/prepare-buy-reservation.md) per acquistare la prenotazione. Questi requisiti si applicano all'acquisto di un'istanza host dedicata riservata:

- È necessario avere un ruolo proprietario per almeno una sottoscrizione EA o una sottoscrizione con pagamento in base al consumo.

- Per le sottoscrizioni EA, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). Se tale impostazione è disabilitata, è necessario essere un amministratore del contratto EA della sottoscrizione.

- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Per acquistare un'istanza:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutti i servizi** \> **Prenotazioni**.

3. Selezionare **Aggiungi** per acquistare una nuova prenotazione e quindi fare clic su **host dedicati**.

4. Compilare i campi obbligatori. L'esecuzione di istanze host dedicate che corrispondono agli attributi selezionati è idonea per ottenere lo sconto per la prenotazione. Il numero effettivo delle istanze host dedicate che ottengono lo sconto dipendono dall'ambito e dalla quantità selezionati.

Se si dispone di un contratto Enterprise, è possibile utilizzare l' **opzione Aggiungi ulteriore** per aggiungere rapidamente ulteriori istanze. L'opzione non è disponibile per altri tipi di sottoscrizione.

| **Campo**           | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | La sottoscrizione usata per pagare la prenotazione. I costi per la prenotazione vengono addebitati al metodo di pagamento specificato nella sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto di servizio Microsoft o una sottoscrizione singola con tariffe a consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Gli addebiti vengono dedotti dal saldo del pagamento anticipato di Azure (in precedenza detto impegno monetario), se disponibile, oppure vengono applicati come eccedenza. Per una sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione. |
| Ambito               | L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | L'area di Azure coperta dalla prenotazione.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dimensioni host dedicate | Dimensioni delle istanze host dedicate.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termine                | Un anno o tre anni.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantità            | Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze dell'host dedicato in esecuzione che possono ottenere lo sconto per la fatturazione.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Gruppo di risorse singolo**: lo sconto per la prenotazione si applica solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.

- **Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.

- **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.

## <a name="usage-data-and-reservation-utilization"></a>Utilizzo prenotazione e dati di utilizzo

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. È possibile visualizzare l'istanza di macchina virtuale che ha ricevuto lo sconto di prenotazione per ogni prenotazione.

Per altre informazioni su come vengono visualizzati gli sconti di prenotazione nei dati di utilizzo, vedere informazioni [sull'utilizzo delle prenotazioni di Azure per la registrazione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se si è clienti con contratto Enterprise. Se si ha una sottoscrizione singola, vedere [informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

- Aggiornare l'ambito della prenotazione

- Flessibilità delle dimensioni dell'istanza (se applicabile)

- Proprietario

È anche possibile dividere una prenotazione in blocchi più piccoli e unire le prenotazioni già suddivise. Nessuna delle modifiche genera una nuova transazione commerciale o modifica la data di fine della prenotazione.

Non è possibile apportare i seguenti tipi di modifiche dopo l'acquisto, direttamente:

- Area della prenotazione esistente

- SKU

- Quantità

- Duration

Tuttavia, se si desidera apportare modifiche, è possibile *scambiare* una prenotazione.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Uso di host dedicati di Azure](./dedicated-hosts.md)

- [Prezzi degli host dedicati](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gestire le prenotazioni in Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Informazioni su come viene applicato lo sconto sulla prenotazione](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Costi del software Windows non inclusi nelle prenotazioni](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](/partner-center/azure-reservations)
