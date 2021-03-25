---
title: Ottimizzare i costi per File di Azure con capacità riservata
titleSuffix: Azure Files
description: Informazioni su come risparmiare sui costi per le distribuzioni di condivisione file di Azure usando File di Azure capacità riservata.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027597"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Ottimizzare i costi per File di Azure con capacità riservata
È possibile risparmiare sui costi di archiviazione per le condivisioni file di Azure con prenotazioni di capacità. File di Azure capacità riservata offre uno sconto sulla capacità per i costi di archiviazione quando si esegue il commit di una prenotazione per uno o tre anni. Una prenotazione fornisce una quantità fissa di capacità di archiviazione per il periodo di prenotazione.

File di Azure capacità riservata può ridurre significativamente i costi di capacità per l'archiviazione dei dati nelle condivisioni file di Azure. Quanto si salva dipende dalla durata della prenotazione, dalla capacità totale che si sceglie di riservare e dalle impostazioni di livello e ridondanza selezionate per le condivisioni file di Azure. La capacità riservata fornisce uno sconto per la fatturazione e non influisce sullo stato delle condivisioni file di Azure.

Per informazioni sui prezzi relativi alla capacità di prenotazione per File di Azure, vedere [prezzi file di Azure](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Condizioni di prenotazione per File di Azure
Le sezioni seguenti descrivono i termini di una prenotazione di capacità File di Azure.

### <a name="reservation-capacity"></a>Capacità prenotazione
È possibile acquistare File di Azure capacità riservata in unità di 10 TiB e 100 TiB al mese per un periodo di un anno o di tre anni.

### <a name="reservation-scope"></a>Ambito prenotazione
File di Azure capacità riservata è disponibile per una singola sottoscrizione o per più sottoscrizioni (ambito condiviso). Quando l'ambito è una singola sottoscrizione, lo sconto per la prenotazione viene applicato solo alla sottoscrizione selezionata. Se l'ambito è limitato a più sottoscrizioni, lo sconto relativo alla prenotazione viene condiviso tra le sottoscrizioni all'interno del contesto di fatturazione del cliente. Una prenotazione si applica all'uso nell'ambito acquistato e non può essere limitata a un account di archiviazione, un contenitore o un oggetto specifico all'interno della sottoscrizione.

Una prenotazione di capacità per File di Azure copre solo la quantità di dati archiviati in una sottoscrizione o in un gruppo di risorse condivise. Gli addebiti per transazioni, larghezza di banda e trasferimento dati non sono inclusi nella prenotazione. Non appena si acquista una prenotazione, i costi di capacità corrispondenti agli attributi di prenotazione vengono addebitati in base alle tariffe di sconto anziché alle tariffe a consumo. Per altre informazioni sulle prenotazioni di Azure, vedere [che cosa sono le prenotazioni di Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Livelli e opzioni di ridondanza supportati
File di Azure capacità riservata è disponibile solo per le condivisioni file standard distribuite in account di archiviazione per utilizzo generico versione 2 (GPv2). La capacità riservata non è disponibile per le condivisioni file di Azure nei livelli Premium o con ottimizzazione per le transazioni.

Attualmente, solo le condivisioni file di Azure nei livelli ad accesso frequente e sporadico supportano le prenotazioni. Tutte le ridondanze di archiviazione supportano le prenotazioni. Per ulteriori informazioni sulle opzioni di ridondanza, vedere [file di Azure ridondanza](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Requisiti di sicurezza per l'acquisto
Per acquistare la capacità riservata:

- È necessario avere il ruolo di **proprietario** per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
- Per le sottoscrizioni aziendali, è necessario abilitare l' **aggiunta di istanze riservate** nel portale EA. In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare File di Azure capacità riservata.

## <a name="determine-required-capacity-before-purchase"></a>Determinare la capacità richiesta prima dell'acquisto
Quando si acquista una prenotazione di File di Azure, è necessario scegliere l'opzione Region, tier e ridondanza per la prenotazione. La prenotazione è valida solo per i dati archiviati in tale area, livello e ridondanza. Si supponga, ad esempio, di acquistare una prenotazione per i dati negli Stati Uniti occidentali per il livello di accesso frequente tramite l'archiviazione con ridondanza della zona (ZRS). Tale prenotazione non si applica ai dati negli Stati Uniti orientali, i dati nel livello ad accesso sporadico o i dati nell'archiviazione con ridondanza geografica (GRS). Tuttavia, è possibile acquistare un'altra prenotazione per le esigenze aggiuntive.  

Sono disponibili prenotazioni per i blocchi 10 TiB o 100 TiB, con sconti maggiori per i blocchi 100 TiB. Quando si acquista una prenotazione nel portale di Azure, Microsoft può fornire consigli sulla base dell'utilizzo precedente per determinare la prenotazione da acquistare.

## <a name="purchase-azure-files-reserved-capacity"></a>Acquistare File di Azure capacità riservata
È possibile acquistare File di Azure capacità riservata tramite l' [portale di Azure](https://portal.azure.com). Usare pagamenti anticipati o mensili per acquistare la prenotazione. Per altre informazioni sull'acquisto con pagamenti mensili, vedere [acquistare prenotazioni di Azure con pagamenti anticipati o mensili](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Per informazioni sull'identificazione dei termini di prenotazione appropriati per il proprio scenario, vedere [comprendere lo sconto sulla capacità riservata di archiviazione di Azure](../../cost-management-billing/reservations/understand-storage-charges.md).

Per acquistare la capacità riservata, seguire questa procedura:

1. Passare al pannello [prenotazioni acquisti](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) nel portale di Azure.  
1. Selezionare **file di Azure** per acquistare una nuova prenotazione.  
1. Compilare i campi obbligatori come descritto nella tabella seguente:

    ![Screenshot che illustra come acquistare la capacità riservata](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Campo  |Descrizione  |
   |---------|---------|
   |**Ambito**   |  Indica il numero di sottoscrizioni che possono utilizzare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/> Se si seleziona **condiviso**, lo sconto relativo alla prenotazione viene applicato alla capacità di file di Azure in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso include tutte le singole sottoscrizioni con tariffe con pagamento in base al consumo create dall'amministratore account.  <br/><br/>  Se si seleziona **singola sottoscrizione**, viene applicato lo sconto di prenotazione alla capacità file di Azure nella sottoscrizione selezionata. <br/><br/> Se si seleziona **gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato alla capacità file di Azure nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione. <br/><br/> È possibile modificare l'ambito di prenotazione dopo aver acquistato la prenotazione.  |
   |**Sottoscrizione**  | Sottoscrizione usata per pagare la prenotazione File di Azure. Il metodo di pagamento per la sottoscrizione selezionata viene utilizzato per l'addebito dei costi. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Contratto Enterprise (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P): per una sottoscrizione aziendale, gli addebiti vengono dedotti dal saldo prepagato di Azure per la registrazione (in precedenza denominato impegno monetario) o addebitati come eccedenze. <br/><br/> Sottoscrizione singola con tariffe con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P): per una sottoscrizione singola con tariffe con pagamento in base al consumo, i costi vengono addebitati sul metodo di pagamento con carta di credito o fatturazione per la sottoscrizione.    |
   | **Area** | Area in cui è attiva la prenotazione. |
   | **Livello** | Il livello in cui è attiva la prenotazione. *Le opzioni* includono accesso *frequente e accesso* sporadico. |
   | **Ridondanza** | Opzione di ridondanza per la prenotazione. Le opzioni includono *con ridondanza locale*, *ZRS*, *GRS* e *GZRS*. Per ulteriori informazioni sulle opzioni di ridondanza, vedere [file di Azure ridondanza](storage-files-planning.md#redundancy). |
   | **Frequenza di fatturazione** | Indica la frequenza con cui l'account viene fatturato per la prenotazione. Le opzioni includono *mensile* o *iniziale*. |
   | **Dimensioni** | Quantità di capacità da riservare. |
   |**Termine**  | Un anno o tre anni.   |

1. Dopo aver selezionato i parametri per la prenotazione, il portale di Azure Visualizza il costo. Il portale Mostra anche la percentuale di sconto rispetto alla fatturazione con pagamento in base al consumo.

1. Nel pannello **prenotazioni acquisti** esaminare il costo totale della prenotazione. È anche possibile specificare un nome per la prenotazione.

Dopo l'acquisto di una prenotazione, questa viene applicata automaticamente a tutte le condivisioni file di Azure esistenti che corrispondono alle condizioni della prenotazione. Se non sono ancora state create condivisioni file di Azure, la prenotazione viene applicata ogni volta che si crea una risorsa che corrisponde alle condizioni della prenotazione. In entrambi i casi, il termine della prenotazione inizia immediatamente dopo un acquisto riuscito.

## <a name="exchange-or-refund-a-reservation"></a>Scambiare o rimborsare una prenotazione
È possibile scambiare o rimborsare una prenotazione, con determinate limitazioni. Queste limitazioni sono descritte nelle sezioni seguenti.

Per scambiare o rimborsare una prenotazione, accedere ai dettagli della prenotazione nella portale di Azure. Selezionare **scambio** o **rimborso** e seguire le istruzioni per inviare una richiesta di supporto. Quando la richiesta è stata elaborata, Microsoft invierà un messaggio di posta elettronica per confermare il completamento della richiesta.

Per altre informazioni sui criteri di prenotazione di Azure, vedere la pagina relativa agli [scambi self-service e ai rimborsi per le prenotazioni di Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Scambiare una prenotazione
Lo scambio di una prenotazione consente di ricevere un rimborso ripartito proporzionalmente in base alla parte inutilizzata della prenotazione. È quindi possibile applicare il rimborso al prezzo di acquisto di una nuova prenotazione File di Azure.

Non esiste alcun limite al numero di scambi che è possibile apportare. Non sono inoltre previste tariffe associate a uno scambio. La nuova prenotazione acquistata deve avere un valore uguale o maggiore rispetto al credito riprodotto dalla prenotazione originale. Una prenotazione di File di Azure può essere scambiata solo per un'altra prenotazione File di Azure e non per una prenotazione per nessun altro servizio di Azure.

### <a name="refund-a-reservation"></a>Rimborsare una prenotazione
È possibile annullare una prenotazione di File di Azure in qualsiasi momento. Quando si Annulla, si riceverà un rimborso proporzionale in base al periodo rimanente della prenotazione, meno una tariffa per la terminazione anticipata del 12%. Il rimborso massimo all'anno è $50.000.

L'annullamento di una prenotazione interrompe immediatamente la prenotazione e restituisce i mesi rimanenti a Microsoft. Il saldo rivalutato rimanente, meno la tariffa, verrà rimborsato alla forma originale di acquisto.

## <a name="expiration-of-a-reservation"></a>Scadenza di una prenotazione
Quando una prenotazione scade, qualsiasi File di Azure capacità utilizzata in tale prenotazione viene fatturata in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

Si riceverà una notifica tramite posta elettronica 30 giorni prima della scadenza della prenotazione e nuovamente alla data di scadenza. Per continuare a sfruttare i vantaggi offerti dalla prenotazione, rinnovarla non più tardi rispetto alla data di scadenza.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti
In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulle prenotazioni di Azure](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Informazioni sul modo in cui vengono applicati gli sconti di prenotazione ai servizi di archiviazione di Azure](../../cost-management-billing/reservations/understand-storage-charges.md)