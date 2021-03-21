---
title: Raccolta di cartelle di lavoro nel centro sicurezza di Azure
description: Informazioni su come creare report interattivi avanzati dei dati del Centro sicurezza di Azure con la raccolta di cartelle di lavoro integrata di monitoraggio di Azure
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107711"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Creazione di report avanzati e interattivi dei dati del Centro sicurezza

Le [cartelle di lavoro di monitoraggio di Azure](../azure-monitor/visualize/workbooks-overview.md) forniscono un'area di disegno flessibile per l'analisi dei dati e la creazione di report visivi avanzati all'interno del portale di Azure. Consentono di sfruttare più origini dati in tutto Azure e di combinarle in esperienze interattive unificate.

Le cartelle di lavoro offrono un set completo di funzionalità per la visualizzazione dei dati di Azure. Per esempi dettagliati di ogni tipo di visualizzazione, vedere gli [esempi di visualizzazioni e la documentazione](../azure-monitor/visualize/workbooks-text-visualizations.md). 

Nel centro sicurezza di Azure è possibile accedere ai report predefiniti per tenere traccia del comportamento di sicurezza dell'organizzazione. È anche possibile creare report personalizzati per visualizzare un'ampia gamma di dati dal centro sicurezza o da altre origini dati supportate.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapporto di Punteggio sicuro nel tempo":::

## <a name="availability"></a>Disponibilità

| Aspetto                          | Dettagli                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Stato della versione:                  | Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Prezzi:                        | Livello gratuito                                                                                                                                         |
| Autorizzazioni e ruoli obbligatori: | Per salvare le cartelle di lavoro, è necessario disporre almeno delle autorizzazioni di collaboratore cartella di lavoro nel gruppo di risorse di destinazione                                      |
| Cloud:                         | ![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Raccolta di cartelle di lavoro nel centro sicurezza di Azure

Grazie alla funzionalità integrata di cartelle di lavoro di Azure, il Centro sicurezza di Azure semplifica la creazione di report personalizzati e interattivi. Il Centro sicurezza include anche una raccolta di cartelle di lavoro con i report seguenti pronti per la personalizzazione:

- **Punteggio sicuro nel tempo** : tenere traccia dei punteggi delle sottoscrizioni e delle modifiche apportate alle raccomandazioni per le risorse
- **Aggiornamenti del sistema** -visualizzare gli aggiornamenti di sistema mancanti per risorse, sistema operativo, gravità e altro ancora
- **Risultati della valutazione della vulnerabilità** : visualizzare i risultati delle analisi delle vulnerabilità delle risorse di Azure

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Raccolta di cartelle di lavoro predefinite nel centro sicurezza di Azure":::

Scegliere uno dei report forniti o crearne uno personalizzato.

> [!TIP]
> Utilizzare il pulsante **modifica** per personalizzare i report forniti in modo da soddisfare le proprie esigenze. Al termine della modifica, selezionare **Salva per salvare** le modifiche in una nuova cartella di lavoro.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Modificare le cartelle di lavoro fornite per personalizzarle in base alle specifiche esigenze":::

### <a name="use-the-secure-score-over-time-report"></a>Usa il report "Punteggio sicuro nel tempo"

Questo report USA i dati di Punteggio sicuro dall'area di lavoro Log Analytics. I dati devono essere esportati dallo strumento di esportazione continua, come descritto in [configurare l'esportazione continua dalle pagine del Centro sicurezza in portale di Azure](continuous-export.md?tabs=azure-portal).

Quando si configura l'esportazione continua, impostare la frequenza di esportazione sia per **gli aggiornamenti di streaming** che per gli **snapshot**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Per la cartella di lavoro del Punteggio sicuro nel tempo è necessario selezionare entrambe le opzioni dalle impostazioni della frequenza di esportazione nella configurazione dell'esportazione continua.":::

> [!NOTE]
> Gli snapshot vengono esportati settimanalmente, quindi è necessario attendere almeno una settimana prima che il primo snapshot venga esportato prima di poter visualizzare i dati nel report.

> [!TIP]
> Per configurare l'esportazione continua nell'organizzazione, usare i criteri di Azure ' DeployIfNotExist ' specificati in [configurare l'esportazione continua su larga scala](continuous-export.md?tabs=azure-policy).

Il report del Punteggio sicuro nel tempo ha cinque grafici per le sottoscrizioni che inviano report alle aree di lavoro selezionate:


|Grafico  |Esempio  |
|---------|---------|
|**Tendenze del punteggio per l'ultima settimana e il mese**<br>Usare questa sezione per monitorare il punteggio corrente e le tendenze generali dei punteggi per le sottoscrizioni.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Tendenze per il Punteggio sicuro nel report incorporato":::|
|**Punteggio aggregato per tutte le sottoscrizioni selezionate**<br>Posizionare il puntatore del mouse su un punto qualsiasi della linea di tendenza per visualizzare il punteggio aggregato in qualsiasi data nell'intervallo di tempo selezionato.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Punteggio aggregato per tutte le sottoscrizioni selezionate":::|
|**Raccomandazioni con le risorse più problematiche**<br>Questa tabella consente di valutare le raccomandazioni per le quali il maggior numero di risorse è stato modificato in non integro nel periodo selezionato.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Raccomandazioni con le risorse più problematiche":::|
|**Punteggi per controlli di sicurezza specifici**<br>I controlli di sicurezza del Centro sicurezza sono raggruppamenti logici di raccomandazioni. Questo grafico mostra a colpo d'occhio i punteggi settimanali per tutti i controlli.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Punteggi per i controlli di sicurezza nel periodo di tempo selezionato":::|
|**Modifiche alle risorse**<br>Le raccomandazioni con la maggior parte delle risorse che hanno modificato lo stato (integro, non integro o non applicabile) durante il periodo selezionato sono elencate qui. Selezionare una raccomandazione nell'elenco per aprire una nuova tabella che elenca le risorse specifiche.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Raccomandazioni con la maggior parte delle risorse che hanno modificato lo stato di integrità":::|

### <a name="use-the-system-updates-report"></a>Usa il report ' aggiornamenti del sistema '

Questo report è basato sulla raccomandazione di sicurezza "gli aggiornamenti del sistema devono essere installati nei computer".

Il report consente di identificare i computer con aggiornamenti in attesa.

È possibile visualizzare la situazione per le sottoscrizioni selezionate in base a:

- Elenco di risorse con aggiornamenti in attesa
- Elenco degli aggiornamenti mancanti dalle risorse

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Report degli aggiornamenti del sistema del Centro sicurezza basato sulla raccomandazione per la sicurezza degli aggiornamenti mancanti":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Usa il report ' risultati valutazione vulnerabilità'

Il Centro sicurezza include scanner di vulnerabilità per computer, contenitori in registri contenitori e server SQL.

Altre informazioni sull'uso di questi scanner:

- [Eseguire la scansione dei computer con lo scanner VA integrato](deploy-vulnerability-assessment-vm.md)
- [Analizzare le immagini del registro per individuare vulnerabilità](defender-for-container-registries-usage.md)
- [Analizza le risorse SQL per individuare le vulnerabilità](defender-for-sql-on-machines-vulnerability-assessment.md)

I risultati per ognuno di questi scanner vengono segnalati in consigli distinti:

- È consigliabile correggere le vulnerabilità nelle macchine virtuali
- È consigliabile correggere le vulnerabilità delle immagini del Registro Azure Container (con tecnologia Qualys)
- È consigliabile correggere i risultati di Valutazione della vulnerabilità nei database SQL
- È consigliabile correggere i risultati di Valutazione della vulnerabilità nei server SQL nei computer

Questo report raccoglie questi risultati e li organizza in base alla gravità, al tipo di risorsa e alla categoria.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Report sui risultati della valutazione della vulnerabilità del Centro sicurezza":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importa cartelle di lavoro da altre raccolte di cartelle di lavoro

Se sono state compilate cartelle di lavoro in altri servizi di Azure e si vuole spostarle nella raccolta di cartelle di lavoro del Centro sicurezza di Azure:

1. Aprire la cartella di lavoro di destinazione.

1. Scegliere **modifica** dalla barra degli strumenti.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Modifica di una cartella di lavoro di monitoraggio di Azure":::

1. Dalla barra degli strumenti selezionare **</>** per immettere il Editor avanzato.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Avvio dell'editor avanzato per ottenere il codice JSON del modello della raccolta":::

1. Copiare il file JSON del modello di raccolta della cartella di lavoro.

1. Aprire la raccolta di cartelle di lavoro nel centro sicurezza e dalla barra dei menu selezionare **nuovo**.
1. Selezionare la **</>** per immettere il Editor avanzato.
1. Incollare l'intero modello di raccolta JSON.
1. Selezionare **Applica**.
1. Dalla barra degli strumenti selezionare **Salva con nome**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Salvataggio della cartella di lavoro nella raccolta nel centro sicurezza":::

1. Immettere i dettagli necessari per salvare la cartella di lavoro:
   1. Nome della cartella di lavoro
   2. Area desiderata
   3. Sottoscrizione, gruppo di risorse e condivisione nel modo appropriato.

La cartella di lavoro salvata è presente nella categoria **cartelle di lavoro modificate di recente** .


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive la pagina cartelle di lavoro di monitoraggio integrato di Azure del Centro sicurezza con report predefiniti e l'opzione per creare report personalizzati e interattivi.

- Altre informazioni sulle [cartelle di lavoro di monitoraggio di Azure](../azure-monitor/visualize/workbooks-overview.md)
- I report predefiniti estraggono i dati dalle raccomandazioni del Centro sicurezza. Informazioni sui numerosi consigli di sicurezza nelle [raccomandazioni sulla sicurezza-Guida di riferimento](recommendations-reference.md)