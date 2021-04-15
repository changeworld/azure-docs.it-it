---
title: Informazioni sulla fattura del Contratto del cliente Microsoft in Azure
description: Informazioni su come leggere e comprendere la fattura del Contratto del cliente Microsoft in Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: ff53131f3078b33b7e7d853c1fca891b0b86d792
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484600"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termini presenti nella fattura del Contratto del cliente Microsoft in Azure

Questo articolo si applica a un account di fatturazione di Azure per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

La fattura contiene un riepilogo degli addebiti e le istruzioni per il pagamento. È disponibile per il download in formato PDF (Portable Document Format) dal [Portale di Azure](https://portal.azure.com/). In alternativa, è possibile riceverla tramite posta elettronica. Per altre informazioni, vedere [Visualizzare e scaricare la fattura di Microsoft Azure](download-azure-invoice.md).

Guardare il video [Informazioni Contratto del cliente Microsoft fattura](https://www.youtube.com/watch?v=e2LGZZ7GubA) per informazioni sulla fattura e su come analizzarne gli addebiti.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Periodo di fatturazione

La fattura viene emessa mensilmente. È possibile individuare il giorno del mese in cui si riceveranno le fatture controllando la *data della fattura* nelle proprietà del profilo di fatturazione nel [portale di Azure](https://portal.azure.com/). Gli addebiti maturati tra la fine del periodo di fatturazione e la data della fattura saranno inclusi nella fattura del mese successivo, perché rientrano nel periodo di fatturazione successivo. Le date di inizio e di fine del periodo di fatturazione per ogni fattura sono elencate nel documento PDF della fattura sopra **Riepilogo fatturazione**.

Se si esegue la migrazione da un EA a un Contratto del cliente Microsoft, si continuano a ricevere fatture per l'EA fino alla data di migrazione. La nuova fattura per il Contratto del cliente Microsoft viene generata il quinto giorno del mese dopo la migrazione. La prima fattura mostra un addebito parziale dalla data di migrazione. Le fatture successive vengono generate ogni mese e mostrano tutti gli addebiti per ogni mese.

### <a name="changes-for-pay-as-you-go-subscriptions"></a>Modifiche per le sottoscrizioni con pagamento in base al go

Quando una sottoscrizione viene transizione, trasferita o annullata, l'ultima fattura generata contiene gli addebiti per il ciclo di fatturazione precedente e il nuovo ciclo di fatturazione incompleto.

Ad esempio:

Si supponga che il ciclo di fatturazione della sottoscrizione con pagamento in base al go sia dal giorno 8 al giorno 7 di ogni mese. La sottoscrizione è stata trasferita a un Contratto del cliente Microsoft il 16 novembre. L'ultima fattura con pagamento in base al carico ha addebiti per l'8 ottobre 2020 fino al 7 novembre 2020. Include anche gli addebiti per il nuovo ciclo di fatturazione parziale per il Contratto del cliente Microsoft dall'8 novembre 2020 al 16 novembre 2020. Di seguito è riportata un'immagine di esempio.

:::image type="content" source="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" alt-text="Immagine di esempio di una fattura che mostra l'ultimo ciclo di fatturazione." lightbox="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" :::

## <a name="invoice-terms-and-descriptions"></a>Termini presenti nella fattura e descrizioni

Le sezioni seguenti riportano i termini importanti presenti nella fattura e le descrizioni per ogni termine.

### <a name="invoice-summary"></a>Riepilogo fattura

**Riepilogo fattura** si trova nella parte superiore della prima pagina e contiene informazioni sul profilo di fatturazione e sulla modalità di pagamento.

![Sezione di riepilogo della fattura](./media/mca-understand-your-invoice/invoicesummary.png)

| Termine | Descrizione |
| --- | --- |
| Venduto a |Indirizzo della persona giuridica, riportato nelle proprietà dell'account di fatturazione|
| Indirizzo di fatturazione |Indirizzo di fatturazione del profilo di fatturazione che riceve la fattura, riportato nelle proprietà del profilo di fatturazione|
| Profilo di fatturazione |Nome del profilo di fatturazione che riceve la fattura |
| Numero ordine d'acquisto |Numero d'ordine d'acquisto, facoltativo, assegnato dall'utente per il monitoraggio |
| Numero di fattura |Numero di fattura univoco, generato da Microsoft, usato a scopo di monitoraggio |
| Data fattura |Data in cui la fattura viene generata, di solito da 5 a 12 giorni dopo la fine del ciclo di fatturazione. È possibile controllare la data della fattura nelle proprietà del profilo di fatturazione.|
| Condizioni di pagamento |Modalità di pagamento della fattura Microsoft. *Scadenza entro 30 giorni* significa che il pagamento deve essere effettuato entro 30 giorni dalla data della fattura. |

### <a name="billing-summary"></a>Riepilogo fatturazione

**Riepilogo fatturazione** mostra gli addebiti per il profilo di fatturazione dal periodo di fatturazione precedente, i crediti applicati, le imposte e l'importo totale dovuto.

![Sezione Riepilogo fatturazione](./media/mca-understand-your-invoice/billingsummary.png)

| Termine | Descrizione |
| --- | --- |
| Charges|Numero totale di addebiti Microsoft per il profilo di fatturazione dall'ultimo periodo di fatturazione |
| Credits |Crediti ricevuti da restituzioni |
| Crediti Azure applicati | Crediti Azure applicati automaticamente agli addebiti di Azure per ogni periodo di fatturazione |
| Subtotale |Importo dovuto al lordo delle imposte |
| Imposta |Tipo e importo delle imposte da pagare, a seconda del paese/area geografica del profilo di fatturazione. Se non è necessario pagare imposte, questa voce non verrà visualizzata nella fattura. |
| Risparmi totali stimati |Importo totale stimato che si è risparmiato grazie agli sconti validi. Se applicabili, i tassi di sconto validi sono elencati sotto gli articoli acquistati nei dettagli per sezione della fattura. |

### <a name="invoice-sections"></a>Sezioni della fattura

Per ogni sezione della fattura per il profilo di fatturazione verranno visualizzati gli addebiti, l'importo dei crediti Azure applicati, le imposte e l'importo totale dovuto.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Dettagli per sezione della fattura

I dettagli mostrano il costo per ogni sezione della fattura, suddiviso per ordine di prodotti. All'interno di ogni ordine di prodotti, il costo viene scomposto in base al tipo di servizio. Gli addebiti giornalieri per i prodotti e i servizi sono riportati nel portale di Azure e nel file CSV relativo all'utilizzo e agli addebiti di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti nella fattura per un Contratto del cliente Microsoft](review-customer-agreement-bill.md).

L'importo totale dovuto per ogni famiglia di servizi viene calcolato sottraendo i *crediti Azure* da *crediti/addebiti* e aggiungendo le *imposte*:


![Dettagli per sezione della fattura](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Termine |Descrizione |
| --- | --- |
| Prezzo unitario | Prezzo unitario valido del servizio (nella valuta dei prezzi) usato per valutare l'utilizzo. È univoco per un prodotto, una famiglia di servizi, un contatore e un'offerta. |
| Qtà | Quantità acquistata o utilizzata durante il periodo di fatturazione |
| Charges/Credits (Addebiti/crediti) | Importo netto degli addebiti dopo l'applicazione di crediti e rimborsi |
| Credito Azure | Importo dei crediti Azure applicati a Charges/Credits (Addebiti/crediti)|
| Aliquota d'imposta | Aliquota/e d'imposta in base al paese/area geografica |
| Importo imposte | Importo delle imposte applicate all'acquisto in base all'aliquota |
| Totale | Importo totale dovuto per l'acquisto |

### <a name="how-to-pay"></a>Metodo di pagamento

Nella parte inferiore della fattura sono riportate le istruzioni per il relativo pagamento. Si può pagare tramite assegno o bonifico oppure online. In caso di pagamento online, è possibile usare una carta di credito o crediti Azure, se applicabili.

### <a name="publisher-information"></a>Informazioni sull'editore

Se nella fattura sono inclusi servizi di terze parti, il nome e l'indirizzo di ogni editore sono elencati nella parte inferiore della fattura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sugli addebiti nella fattura del profilo di fatturazione](review-customer-agreement-bill.md)
- [Come ottenere la fattura e i dati di utilizzo giornalieri di Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](../manage/ea-pricing.md)
- [Visualizzare i documenti fiscali per il profilo di fatturazione](mca-download-tax-document.md)
