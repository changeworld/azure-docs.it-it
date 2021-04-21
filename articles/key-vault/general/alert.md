---
title: Azure Key Vault monitoraggio e avvisi | Microsoft Docs
description: Creare un dashboard per monitorare l'integrità dell'insieme di credenziali delle chiavi e configurare gli avvisi.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: f8f9dd6d51b974ebd31804daf0402ca5535ffc92
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751581"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitoraggio e avvisi per Azure Key Vault

## <a name="overview"></a>Panoramica

Dopo aver iniziato a usare l'insieme di credenziali delle chiavi per archiviare i segreti di produzione, è importante monitorare l'integrità dell'insieme di credenziali delle chiavi per assicurarsi che il servizio funzioni come previsto. Quando si inizia a ridimensionare il servizio, il numero di richieste inviate all'insieme di credenziali delle chiavi aumenta. Di conseguenza, può aumentare anche la latenza delle richieste e, in casi estremi, le richieste possono venire limitate, con un impatto sulle prestazioni del servizio. È anche necessario ricevere un avviso se l'insieme di credenziali delle chiavi invia un numero insolito di codici di errore, in modo da ricevere rapidamente una notifica di eventuali problemi di configurazione del firewall o dei criteri di accesso. Questo documento tratta gli argomenti seguenti:

+ Metriche Key Vault di base da monitorare
+ Come configurare le metriche e creare un dashboard
+ Come creare avvisi in base alle soglie specificate

Monitoraggio di Azure per Key Vault combina log e metriche per offrire una soluzione di monitoraggio globale. [Altre informazioni sulle Monitoraggio di Azure per Key Vault qui](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>Metriche Key Vault di base da monitorare

+ Disponibilità dell'insieme di credenziali  
+ Saturazione dell'insieme di credenziali 
+ Latenza DELL'API del servizio 
+ Totale riscontri API del servizio (filtro per tipo di attività) 
+ Codici di errore (filtro in base al codice di stato) 

**Disponibilità dell'insieme** di credenziali: questa metrica deve essere sempre al 100%, si tratta di una metrica importante da monitorare, perché può mostrare rapidamente se si è verificata un'interruzione dell'insieme di credenziali delle chiavi. 

**Saturazione dell'insieme** di credenziali: il numero di richieste al secondo che un insieme di credenziali delle chiavi può gestire dipende dal tipo di operazione eseguita. Alcune operazioni dell'insieme di credenziali hanno una soglia di richieste al secondo inferiore. Questa metrica aggrega l'utilizzo totale dell'insieme di credenziali delle chiavi in tutti i tipi di operazione per ottenere un valore percentuale che indica l'utilizzo corrente dell'insieme di credenziali delle chiavi. Per un elenco completo dei limiti del servizio dell'insieme di credenziali delle chiavi, vedere il documento seguente. [Limiti dei servizi Azure Key Vault ](service-limits.md)

**Latenza API del servizio:** questa metrica mostra la latenza media delle chiamate all'insieme di credenziali delle chiavi, misurata nel servizio. Non include il tempo utilizzato dal client o dalla rete tra client e servizio.

**Totale riscontri API:** questa metrica mostra tutte le chiamate effettuate all'insieme di credenziali delle chiavi. Ciò consente di identificare le applicazioni che chiamano l'insieme di credenziali delle chiavi. 

**Codici di errore:** questa metrica indica se l'insieme di credenziali delle chiavi presenta una quantità insolita di errori. Per un elenco completo dei codici di errore e delle linee guida per la risoluzione dei problemi, vedere il documento seguente. [Azure Key Vault di errore dell'API REST](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Come configurare le metriche e creare un dashboard

1. Accedere al portale di Azure
2. Passare al Key Vault
3. Selezionare **Metriche** in **Monitoraggio** 

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia l'opzione Metriche nella sezione Monitoraggio.](../media/alert-1.png)

4. Aggiornare il titolo del grafico in base a quello che si vuole visualizzare nel dashboard. 
5. Selezionare l'ambito. In questo esempio si selezionerà un singolo insieme di credenziali delle chiavi. 
6. Selezionare la metrica **Overall Vault Availability** and Aggregation Avg (Media aggregazione e disponibilità complessiva **dell'insieme di credenziali della metrica)** 
7. Aggiornare l'intervallo di tempo alle ultime 24 ore e aggiornare la granularità temporale a 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la metrica Overall Vault Availablility.](../media/alert-2.png)

8. Ripetere i passaggi precedenti per le metriche Saturazione dell'insieme di credenziali e Latenza API del servizio. Selezionare **Aggiungi al dashboard** per salvare le metriche in un dashboard. 

> [!IMPORTANT]
> Selezionare "Aggiungi al dashboard" e salvare tutte le metriche configurate. Se si esce dalla pagina e si torna alla pagina senza salvarla, le modifiche alla configurazione andranno perse. 

9. Per monitorare tutti i tipi di operazioni nell'insieme di credenziali delle chiavi, usare la metrica **Total Service API Hits** (Totale riscontri API servizio) e selezionare Apply Splitting by Activity Type (Applica suddivisione per tipo di **attività)**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il pulsante Applica suddivisione.](../media/alert-3.png)

10. Per monitorare i codici di errore nell'insieme di credenziali delle chiavi, usare la metrica **Total Service API Results** (Totale risultati API del servizio) e selezionare Apply Splitting by Activity Type (Applica suddivisione per tipo di **attività)**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la metrica Total Service API Results selezionata.](../media/alert-4.png)

A questo punto si avrà un dashboard simile al seguente. È possibile fare clic sui 3 puntini in alto a destra di ogni riquadro ed è possibile ridisporre e ridimensionare i riquadri in base alle necessità. 

Dopo aver salvato e pubblicato il dashboard, verrà creata una nuova risorsa nella sottoscrizione di Azure. Sarà possibile visualizzare il dashboard in qualsiasi momento cercando "dashboard condiviso". 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il dashboard pubblicato.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Come configurare gli avvisi nel Key Vault 

Questa sezione illustra come configurare gli avvisi per l'insieme di credenziali delle chiavi in modo che sia possibile avvisare il team di intervenire immediatamente se l'insieme di credenziali delle chiavi si trova in uno stato non integro. È possibile configurare avvisi che inviano un messaggio di posta elettronica, preferibilmente a una DL del team, generano una notifica di Griglia di eventi o chiamano o inviano un SMS a un numero di telefono. È anche possibile scegliere avvisi statici in base a un valore fisso o un avviso dinamico che avvisa se una metrica monitorata supera il limite medio dell'insieme di credenziali delle chiavi per un determinato numero di volte in un intervallo di tempo definito. 

> [!IMPORTANT]
> Si noti che l'avvio dell'invio delle notifiche da parte degli avvisi appena configurati può richiedere fino a 10 minuti. 

### <a name="configure-an-action-group"></a>Configurare un gruppo di azioni 

Un gruppo di azioni è un elenco configurabile di notifiche e proprietà.

1. Accedere al portale di Azure
2. Cercare Avvisi **nella** casella di ricerca
3. Selezionare **Gestisci azioni**

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia il pulsante Gestisci azioni.](../media/alert-6.png)

4. Selezionare **+ Aggiungi gruppo di azioni**

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia il pulsante + Aggiungi gruppo di azioni.](../media/alert-7.png)

5. Scegliere il **tipo di azione** per il gruppo di azioni. In questo esempio verrà creato un avviso di posta elettronica.

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia i campi necessari per aggiungere un gruppo di azioni.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra gli elementi necessari per aggiungere un messaggio di posta elettronica o un avviso sms.](../media/alert-9.png)

6. Fare clic su **OK** nella parte inferiore della pagina. È stato creato un gruppo di azioni. 

Dopo aver configurato un gruppo di azioni, verranno configurate le soglie di avviso dell'insieme di credenziali delle chiavi. 

### <a name="configure-alert-thresholds"></a>Configurare le soglie di avviso 

1. Selezionare la risorsa dell'insieme di credenziali delle chiavi nel portale di Azure e **selezionare Avvisi** in **Monitoraggio**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra l'opzione di menu Avvisi nella sezione Monitoraggio.](../media/alert-10.png)

2. Selezionare **nuova regola di avviso**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il pulsante + Nuova regola di avviso.](../media/alert-11.png)

3. Selezionare l'ambito della regola di avviso. È possibile selezionare uno o più insiemi di credenziali. 

> [!IMPORTANT]
> Si noti che quando si selezionano più insiemi di credenziali per l'ambito degli avvisi, tutti gli insiemi di credenziali selezionati devono essere nella stessa area. Sarà necessario configurare regole di avviso separate per gli insiemi di credenziali in aree diverse. 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra come selezionare un insieme di credenziali.](../media/alert-12.png)

4. Selezionare le condizioni per gli avvisi. È possibile scegliere uno dei segnali seguenti e definire la logica per gli avvisi. Il team Key Vault consiglia di configurare le soglie di avviso seguenti. 

    + Key Vault disponibilità scende sotto il 100% (soglia statica)
    + Key Vault latenza è maggiore di 500 ms (soglia statica) 
    + La saturazione complessiva dell'insieme di credenziali è maggiore del 75% (soglia statica) 
    + La saturazione complessiva dell'insieme di credenziali supera la media (soglia dinamica)
    + Codici di errore totali superiori alla media (soglia dinamica) 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra dove si selezionano le condizioni per gli avvisi.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Esempio 1: Configurazione di una soglia di avviso statica per la latenza

Selezionare **Overall Service API Latency (Latenza COMPLESSIVA API del** servizio) come nome del segnale
> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il nome del segnale overall service api latency.](../media/alert-14.png)

Vedere i parametri di configurazione seguenti.

+ Impostare Soglia su **Statico** 
+ Impostare Operatore su **Maggiore di**
+ Impostare Tipo di aggregazione su **Media**
+ Impostare il valore soglia su **500**
+ Impostare Periodo di aggregazione **su 5 minuti**
+ Impostare Frequenza di valutazione **su 1 minuto**
+ Selezionare **Operazione completata**  

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia la logica di avviso configurata.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Esempio 2: Configurazione di una soglia di avviso dinamica per la saturazione dell'insieme di credenziali 

Quando si usa un avviso dinamico, sarà possibile visualizzare i dati cronologici dell'insieme di credenziali delle chiavi selezionato. L'area blu rappresenta l'utilizzo medio dell'insieme di credenziali delle chiavi. L'area rossa mostra i picchi che avrebbero attivato un avviso purché siano soddisfatti altri criteri nella configurazione dell'avviso. I punti rossi mostrano le istanze di violazioni in cui sono stati soddisfatti i criteri per l'avviso durante l'intervallo di tempo aggregato. È possibile impostare un avviso in modo che sia attivato dopo un determinato numero di violazioni entro un periodo di tempo impostato. Se non si vogliono includere dati precedenti, è possibile escludere i dati precedenti di seguito nelle impostazioni avanzate. 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra un grafico della saturazione complessiva dell'insieme di credenziali.](../media/alert-16.png)

Vedere i parametri di configurazione seguenti.

+ Impostare Soglia su **Dinamico** 
+ Impostare l'operatore **su Maggiore di**
+ Impostare Tipo di aggregazione su **Media**
+ Impostare La sensibilità della soglia su **Media**
+ Impostare Periodo di aggregazione su **5 minuti**
+ Impostare Frequenza di valutazione su **1 minuto**
+ **Facoltativo** Configurare le impostazioni avanzate 
+ Selezionare **Operazione completata**

> [!div class="mx-imgBorder"]
> ![Screenshot della portale di Azure](../media/alert-17.png)

5. Aggiungere il gruppo di azioni configurato

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra come aggiungere un gruppo di azioni.](../media/alert-18.png)

6. Abilitare l'avviso e assegnare una gravità

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra dove abilitare l'avviso e assegnare una gravità.](../media/alert-19.png)

7. Creare l'avviso 

### <a name="example-email-alert"></a>Avviso di posta elettronica di esempio 

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia le informazioni necessarie per configurare un avviso di posta elettronica.](../media/alert-20.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato creato un dashboard di monitoraggio e sono stati configurati avvisi per l'insieme di credenziali delle chiavi. Dopo aver seguito tutti i passaggi precedenti, è necessario ricevere avvisi tramite posta elettronica quando l'insieme di credenziali delle chiavi soddisfa i criteri di avviso configurati. Di seguito è illustrato un esempio. Usare gli strumenti impostati in questo articolo per monitorare attivamente l'integrità dell'insieme di credenziali delle chiavi. 


