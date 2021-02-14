---
title: Gestire le prenotazioni di Azure
description: Informazioni su come gestire le prenotazioni di Azure. Vedere i passaggi per modificare l'ambito di prenotazione, dividere una prenotazione e ottimizzare l'uso della prenotazione.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 02/09/2021
ms.author: banders
ms.openlocfilehash: 717cf5acb63ee04852ccbb9aae2f7aed2b3c179a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392273"
---
# <a name="manage-reservations-for-azure-resources"></a>Gestire le prenotazioni per le risorse di Azure

Dopo aver acquistato una prenotazione di Azure, può essere necessario applicarla a una sottoscrizione diversa, sostituire la persona che la gestisce oppure cambiarne l'ambito. È inoltre possibile dividere una prenotazione in due prenotazioni per applicare alcune delle istanze acquistate a un'altra sottoscrizione.

Se si acquistano le istanze di macchina virtuale riservate di Azure, è possibile modificare l'impostazione di ottimizzazione per la prenotazione. Lo sconto della prenotazione può essere applicato alle macchine virtuali nella stessa serie oppure è possibile riservare capacità del data center per una dimensione di macchina virtuale specifica. È consigliabile provare a ottimizzare le prenotazioni in modo che vengano usate completamente.

*L'autorizzazione necessaria per gestire una prenotazione è separata dall'autorizzazione per la sottoscrizione.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Ordine di prenotazione e prenotazione

Quando si acquista una prenotazione, vengono creati due oggetti: **ordine di prenotazione** e **prenotazione**.

Al momento dell'acquisto, un ordine di prenotazione include un'unica prenotazione. Operazioni come la suddivisione, l'unione, il rimborso parziale o lo scambio creano nuove prenotazioni nell'**ordine di prenotazione**.

Per visualizzare un ordine di prenotazione, passare a **Prenotazioni**, selezionare la prenotazione e quindi selezionare **ID ordine prenotazione**.

![Esempio di dettagli dell'ordine di prenotazione che mostra l'ID ordine di prenotazione ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Una prenotazione eredita le autorizzazioni dal relativo ordine di prenotazione. Per scambiare o rimborsare una prenotazione, l'utente deve essere aggiunto all'ordine di prenotazione.

## <a name="change-the-reservation-scope"></a>Cambiare l'ambito della prenotazione

 Lo sconto per la prenotazione si applica a macchine virtuali, database SQL, Azure Cosmos DB o altre risorse che corrispondono alla prenotazione e vengono eseguite all'interno del relativo ambito. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare la prenotazione.

Per aggiornare l'ambito di una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'ambito.

Se si passa da un ambito condiviso a un ambito singolo, è possibile selezionare solo le sottoscrizioni di cui si è proprietari. È possibile selezionare solo le sottoscrizioni nello stesso contesto di fatturazione della prenotazione.

L'ambito si applica solo a singole sottoscrizioni con pagamento in base al consumo (offerte MS-AZR-0003P o MS-AZR-0023P), all'offerta Enterprise MS-AZR-0017P o MS-AZR-0148P oppure ai tipi di sottoscrizione CSP.

## <a name="who-can-manage-a-reservation-by-default"></a>Chi può gestire una prenotazione per impostazione predefinita

Per impostazione predefinita, le prenotazioni possono essere visualizzate e gestite dagli utenti seguenti:

- L'utente che acquista una prenotazione e l'amministratore account della sottoscrizione di fatturazione usata per acquistare la prenotazione vengono aggiunti all'ordine di prenotazione.
- Amministratori fatturazione con Contratto Enterprise e Contratto del cliente Microsoft.

Per consentire ad altre persone di gestire le prenotazioni sono disponibili due opzioni:

- Delegare la gestione degli accessi per un singolo ordine di prenotazione:
    1. Accedere al [portale di Azure](https://portal.azure.com).
    1. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
    1. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
    1. In Dettagli prenotazione selezionare l'ordine di prenotazione.
    1. Selezionare **Controllo di accesso (IAM)** .
    1. Selezionare **Aggiungi assegnazione di ruolo** > **Ruolo** > **Proprietario**. Se si vuole concedere un accesso limitato, selezionare un ruolo diverso.
    1. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
    1. Selezionare l'utente e quindi selezionare **Salva**.

- Aggiungere un utente come amministratore della fatturazione a un Contratto Enterprise o a un Contratto del cliente Microsoft:
    - Per un Contratto Enterprise, aggiungere gli utenti con il ruolo di _Amministratore dell'organizzazione_ per visualizzare e gestire tutti gli ordini di prenotazione applicabili al Contratto Enterprise. Gli utenti con il ruolo _Amministratore dell'organizzazione (sola lettura)_ possono solo visualizzare la prenotazione. Gli amministratori del reparto e i proprietari dell'account non possono visualizzare le prenotazioni _a meno che_ non vengano aggiunti ad esse in modo esplicito tramite Controllo di accesso (IAM). Per altre informazioni, vedere [Gestione dei ruoli Enterprise di Azure](../manage/understand-ea-roles.md).

        _Gli amministratori dell'organizzazione possono assumere la proprietà di un ordine di prenotazione e aggiungere altri utenti a una prenotazione tramite Controllo di accesso (IAM)._
    - Per un Contratto del cliente Microsoft, gli utenti con il ruolo di proprietario del profilo di fatturazione o di collaboratore per il profilo di fatturazione possono gestire tutti gli acquisti di prenotazioni effettuati usando il profilo di fatturazione. Gli utenti con il ruolo di lettore profilo di fatturazione e responsabile fatturazione possono visualizzare tutte le prenotazioni pagate con il profilo di fatturazione. Non possono però apportare modifiche alle prenotazioni.
    Per altre informazioni, vedere [Ruoli e attività del profilo di fatturazione](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Visualizzazione o gestione delle prenotazioni da parte degli amministratori della fatturazione

1. Passare a **Gestione dei costi e fatturazione** e quindi sul lato sinistro della pagina selezionare **Transazioni di prenotazione**.
2. Se si hanno le autorizzazioni necessarie per la fatturazione, è possibile visualizzare e gestire le prenotazioni. Se non è visualizzata alcuna prenotazione, verificare di aver eseguito l'accesso con il tenant di Azure AD in cui sono state create le prenotazioni.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividere una prenotazione singola in due prenotazioni

 Dopo aver acquistato più di un'istanza di risorsa all'interno di una prenotazione, è possibile assegnare le istanze all'interno di tale prenotazione a sottoscrizioni diverse. Per impostazione predefinita, tutte le istanze sono associate a un ambito (sottoscrizione singola, gruppo di risorse o ambito condiviso). Si supponga ad esempio di aver acquistato una prenotazione per 10 istanze di VM e di aver specificato la sottoscrizione A come ambito. Si vuole in seguito impostare l'ambito di sette istanze di VM sulla sottoscrizione A e quello delle restanti tre sulla sottoscrizione B. La divisione di una prenotazione consente di ottenere questo risultato. Dopo aver diviso una prenotazione, l'ID prenotazione originale viene annullato e vengono create due nuove prenotazioni. La divisione non ha effetti sull'ordine di prenotazione: non si crea una nuova transazione commerciale e le prenotazioni hanno la stessa data di fine di quella divisa.

 È possibile dividere una prenotazione in due prenotazioni tramite PowerShell, l'interfaccia della riga di comando oppure l'API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividere una prenotazione tramite PowerShell

1. Ottenere l'ID dell'ordine di prenotazione eseguendo il comando seguente:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Ottenere i dettagli di una prenotazione:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dividere la prenotazione in due e distribuire le istanze:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. È possibile aggiornare l'ambito con il comando seguente:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Modifica l'impostazione di ottimizzazione per le istanze di macchina virtuale riservate

 Quando si acquista un'istanza di macchina virtuale riservata, si sceglie la flessibilità delle dimensioni istanza o la priorità di capacità. La flessibilità delle dimensioni istanza applicherà lo sconto di prenotazione ad altre macchine virtuali dello stesso [gruppo di macchine virtuali](../../virtual-machines/reserved-vm-instance-size-flexibility.md). La priorità di capacità designa la capacità del data center più importante per le distribuzioni. Questa opzione aumenta la certezza di avere la possibilità di avviare le istanze di macchina virtuale quando servono.

Per impostazione predefinita, quando l'ambito della prenotazione è condiviso, la flessibilità delle dimensioni istanza è attiva. La capacità del data center non viene classificata per le distribuzioni di macchine virtuali.

Per le prenotazioni con ambito singolo, è possibile ottimizzare la prenotazione per la priorità di capacità anziché per la flessibilità delle dimensioni istanza di macchina virtuale.

Per aggiornare l'impostazione di ottimizzazione per la prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
  ![Esempio che mostra l'elemento di configurazione](./media/manage-reserved-vm-instance/add-product03.png)
5. Modificare l'impostazione **Ottimizza per**.
  ![Esempio che mostra l'impostazione Ottimizza per](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Ottimizzare l'uso delle prenotazioni

Il risparmio sulle prenotazioni di Azure deriva solo dall'uso prolungato delle risorse. Quando si acquista una prenotazione, si paga essenzialmente per un possibile uso delle risorse al 100% per un periodo di uno o tre anni. Provare a ottimizzare la prenotazione per usare tutte le risorse possibili e ottenere il massimo risparmio. Le sezioni seguenti illustrano come monitorare una prenotazione e ottimizzarne l'uso.

### <a name="view-reservation-use-in-the-azure-portal"></a>Visualizzare l'uso delle prenotazioni nel portale di Azure

Il portale di Azure è uno dei modi disponibili per visualizzare l'utilizzo delle prenotazioni.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > [**Prenotazioni**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e notare il valore di **Utilizzo (%)** associato a una prenotazione.  
  ![Immagine che mostra l'elenco delle prenotazioni](./media/manage-reserved-vm-instance/reservation-list.png)
3. Selezionare una prenotazione.
4. Esaminare la tendenza dell'uso della prenotazione nel corso del tempo.
  ![Immagine che mostra l'uso delle prenotazioni ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Visualizzare l'uso delle prenotazioni con l'API

I clienti con contratto Enterprise Agreement (EA) possono visualizzare a livello di codice come vengono usate le prenotazioni all'interno dell'organizzazione. Una prenotazione inutilizzata si rileva tramite i dati di utilizzo. Quando si esaminano gli addebiti delle prenotazione, tenere presente che i dati sono divisi tra costi effettivi e costi ammortizzati. I costi effettivi forniscono i dati per riconciliare la fattura mensile. Includono inoltre il costo di acquisto della prenotazione e i dettagli sulla relativa applicazione. I costi ammortizzati sono simili a quelli effettivi, con la differenza che il prezzo valido per l'utilizzo della prenotazione è ripartito in modo proporzionale. Le ore di prenotazioni inutilizzate vengono visualizzate nei dati dei costi ammortizzati. Per altre informazioni sui dati di utilizzo per i clienti con contratto EA, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](understand-reserved-instance-usage-ea.md).

Per altri tipi di sottoscrizione, usare l'API per i [riepiloghi delle prenotazioni](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation), con l'elenco per ordine di prenotazione e prenotazione.

### <a name="optimize-your-reservation"></a>Ottimizzare la prenotazione

Se si ritiene che le prenotazioni dell'organizzazione siano sottoutilizzate:

- Assicurarsi che le macchine virtuali create dall'organizzazione corrispondano alle dimensioni di VM riportate nella prenotazione.
- Assicurarsi che la flessibilità delle dimensioni istanza sia attiva. Per altre informazioni, vedere [Gestire le prenotazioni - Modificare l'impostazione di ottimizzazione per le istanze di macchina virtuale riservate](#change-optimize-setting-for-reserved-vm-instances).
- Cambiare l'ambito della prenotazione impostandolo su _condiviso_ in modo che venga applicato su scala più ampia. Per altre informazioni, vedere [Cambiare l'ambito di una prenotazione](#change-the-reservation-scope).
- Valutare se scambiare la quantità inutilizzata. Per altre informazioni, vedere [Annullamenti e scambi](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)

Acquistare un piano di servizio:
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Pagare in anticipo le risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Acquistare un piano software:
- [Pagare in anticipo i piani software Red Hat dalle prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Informazioni su sconti e utilizzo:
- [Informazioni su come viene applicato lo sconto sulla prenotazione di macchine virtuali](../manage/understand-vm-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software Red Hat Enterprise Linux](understand-rhel-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software SUSE Linux Enterprise](understand-suse-reservation-charges.md)
- [Informazioni su come vengono applicati gli altri sconti sulla prenotazione](understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](reserved-instance-windows-software-costs.md)
