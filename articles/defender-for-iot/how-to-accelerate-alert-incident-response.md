---
title: Accelerare i flussi di lavoro degli avvisi
description: Migliorare i flussi di lavoro di avvisi ed eventi imprevisti.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839677"
---
# <a name="accelerate-alert-workflows"></a>Accelerare i flussi di lavoro degli avvisi

Questo articolo descrive come accelerare i flussi di lavoro degli avvisi usando i commenti degli avvisi, i gruppi di avvisi e le regole di avviso personalizzate in Azure Defender per l'it.  Questi strumenti consentono di:

- Analizzare e gestire il volume elevato di eventi di avviso rilevati nella rete.

- Individuare e gestire un'attività di rete specifica.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Accelerare i flussi di lavoro di eventi imprevisti usando i commenti degli avvisi

Usare i commenti di avviso per migliorare la comunicazione tra singoli utenti e team durante l'analisi di un evento di avviso.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Screenshot che Mostra attività dannose.":::

Usare i commenti degli avvisi per migliorare:

- **Passaggi del flusso di lavoro**: fornire procedure di mitigazione degli avvisi.

- **Completamento del flusso di lavoro**: notificare che i passaggi sono stati eseguiti.

- **Linee guida** per il flusso di lavoro: fornire consigli, informazioni dettagliate o avvisi sull'evento.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Screenshot che mostra i commenti di avviso.":::

L'elenco delle opzioni disponibili viene visualizzato in ogni avviso. Gli utenti possono selezionare uno o più messaggi.

Per aggiungere commenti agli avvisi:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nella finestra **Impostazioni sistema** selezionare **Commenti avviso**.

3. Nella casella **Aggiungi commenti** immettere il testo del commento. Usare un massimo di 50 caratteri. Le virgole non sono consentite.

4. Selezionare **Aggiungi**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Accelerare i flussi di lavoro di eventi imprevisti usando gruppi di avvisi

I gruppi di avvisi consentono ai team SOC di visualizzare e filtrare gli avvisi nelle soluzioni SIEM e quindi di gestire questi avvisi in base ai criteri di sicurezza aziendali e alle priorità aziendali. Ad esempio, gli avvisi relativi a nuovi rilevamenti sono organizzati in un gruppo di individuazione. Questo gruppo include avvisi che gestiscono il rilevamento di nuovi dispositivi, nuove VLAN, nuovi account utente, nuovi indirizzi MAC e altro ancora.

I gruppi di avvisi vengono applicati quando si creano regole di invio per le soluzioni partner seguenti:

  - Server Syslog

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Screenshot della creazione di una regola di invio.":::

Il gruppo di avvisi pertinente viene visualizzato nelle soluzioni di output partner. 

### <a name="requirements"></a>Requisiti

Il gruppo di avvisi verrà visualizzato nelle soluzioni partner supportate con i prefissi seguenti:

  - **Cat** per QRadar, ArcSight, syslog CEF, syslog Leef

  - **Gruppo di avvisi** per i messaggi di testo syslog

  - **alert_group** per gli oggetti syslog

Questi campi devono essere configurati nella soluzione partner per visualizzare il nome del gruppo di avvisi. Se non è presente alcun avviso associato a un gruppo di avvisi, il campo nella soluzione partner visualizzerà **na**.

### <a name="default-alert-groups"></a>Gruppi di avvisi predefiniti

I gruppi di avvisi seguenti vengono definiti automaticamente:
|  |  |  |
|--|--|--|
| Comportamento anomalo della comunicazione | Avvisi personalizzati | Accesso remoto |
| Comportamento anomalo della comunicazione HTTP | Individuazione | Comandi di riavvio e arresto |
| Authentication | Modifica del firmware | Scansione |
| Comportamento di comunicazione non autorizzato | Comandi non validi | Traffico del sensore |
| Anomalie della larghezza di banda | Accesso a Internet | Sospetto di malware |
| Overflow del buffer | Errori delle operazioni | Sospetto di attività dannose |
| Errori di comando | Problemi operativi |  |
| Modifiche di configurazione | Programmazione |  |

I gruppi di avvisi sono predefiniti. Per informazioni dettagliate sugli avvisi associati ai gruppi di avvisi e sulla creazione di gruppi di avvisi personalizzati, contattare [supporto tecnico Microsoft](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Personalizzare le regole di avviso

È possibile aggiungere regole di avviso personalizzate in base alle informazioni rilevate dai singoli sensori. Ad esempio, definire una regola che indica a un sensore di attivare un avviso in base a un indirizzo IP di origine, indirizzo IP di destinazione o comando (all'interno di un protocollo). Quando il sensore rileva il traffico definito nella regola, viene generato un avviso o un evento.

Il messaggio di avviso indica che una regola definita dall'utente ha attivato l'avviso.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Screenshot che Mostra gli avvisi personalizzati.":::

Per creare una regola di avviso personalizzata:

1. Selezionare **avvisi personalizzati** dal menu laterale di un sensore.
1. Selezionare il segno più ( **+** ) per creare una regola.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Screenshot che mostra una regola definita dall'utente.":::

1. Definire un nome di regola.
1. Selezionare una categoria o un protocollo nel riquadro **categorie** .
1. Definire un indirizzo IP o un indirizzo MAC di origine e di destinazione specifico oppure scegliere qualsiasi indirizzo.
1. Aggiungere una condizione. Un elenco di condizioni e le relative proprietà è univoco per ogni categoria. È possibile selezionare più di una condizione per ogni avviso.
1. Indica se la regola attiva un **allarme** o un **evento**.
1. Assegnare un livello di gravità all'avviso.
1. Indica se l'avviso includerà un file PCAP.
1. Selezionare **Salva**.

La regola viene aggiunta all'elenco **delle regole di avviso personalizzate** , in cui è possibile esaminare i parametri della regola di base, l'ultima volta in cui è stata attivata la regola e altro ancora. È anche possibile abilitare e disabilitare la regola dall'elenco.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Screenshot di una regola personalizzata aggiunta dall'utente.":::

### <a name="see-also"></a>Vedere anche

[Visualizza le informazioni fornite negli avvisi](how-to-view-information-provided-in-alerts.md)

[Gestire l'evento di avviso](how-to-manage-the-alert-event.md)
