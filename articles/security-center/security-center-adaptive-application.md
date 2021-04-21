---
title: Controlli delle applicazioni adattivi nel Centro sicurezza di Azure
description: Questo documento consente di usare il controllo delle applicazioni adattive Centro sicurezza di Azure per consentire l'elenco di applicazioni in esecuzione in computer Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 9e2dcace673a1c7215634434f9e89ddc6b953a63
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834620"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Usare i controlli delle applicazioni adattivi per ridurre le superfici di attacco dei computer

Informazioni sui vantaggi dei controlli Centro sicurezza di Azure delle applicazioni adattive e su come migliorare la sicurezza con questa funzionalità intelligente e basata sui dati.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Che cosa sono i controlli delle applicazioni adattivi del Centro sicurezza?

I controlli delle applicazioni adattivi sono una soluzione intelligente e automatizzata per definire elenchi consentiti di applicazioni sicure note per i computer. 

Spesso le organizzazioni hanno raccolte di computer che eseguono regolarmente gli stessi processi. Il Centro sicurezza usa Machine Learning per analizzare le applicazioni in esecuzione nei computer e creare un elenco del software sicuro noto. Gli elenchi Consenti si basano sui carichi di lavoro di Azure specifici ed è possibile personalizzare ulteriormente le raccomandazioni usando le istruzioni seguenti.

Dopo l'abilitazione e la configurazione dei controlli applicazioni adattivi, si riceveranno avvisi di sicurezza se viene eseguita un'applicazione non inclusa nell'elenco di applicazioni definite come sicure.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Quali sono i vantaggi dei controlli delle applicazioni adattivi?

Definendo elenchi di applicazioni sicure note e generando avvisi quando si esegue qualsiasi altra operazione, è possibile raggiungere più obiettivi di protezione avanzata:

- Identificare il potenziale malware, anche quello che potrebbe essere perso dalle soluzioni antimalware
- Migliorare la conformità ai criteri di sicurezza locali che determinano l'uso solo di software concesso in licenza
- Evitare l'esecuzione di applicazioni vecchie o non supportate
- Impedire software specifico escluso dall'organizzazione
- Aumentare la supervisione delle app che accedono ai dati sensibili

Non sono attualmente disponibili opzioni di imposizione. I controlli delle applicazioni adattivi sono destinati a fornire avvisi di sicurezza se un'applicazione viene eseguita in modo diverso da quelli definiti come sicuri.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale|
|Prezzi:|Richiede [Azure Defender per server](defender-for-servers-introduction.md)|
|Computer supportati:|![Sì, ](./media/icons/yes-icon.png) i computer Azure e non Azure che eseguono Windows e Linux<br>![Sì ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) macchine virtuali|
|Autorizzazioni e ruoli obbligatori:|**I ruoli Ruolo** con **autorizzazioni di lettura** per la sicurezza e Lettore possono visualizzare sia i gruppi che gli elenchi di applicazioni sicure note<br>**I ruoli** **Collaboratore e Amministratore** della sicurezza possono modificare i gruppi e gli elenchi di applicazioni sicure note|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Abilitare i controlli delle applicazioni in un gruppo di computer

Se il Centro sicurezza ha identificato gruppi di computer nelle sottoscrizioni che eseguono in modo coerente un set simile di applicazioni, verrà visualizzata la raccomandazione seguente: I controlli delle applicazioni adattivi per la definizione di applicazioni sicure devono essere abilitati nei **computer.**

Selezionare la raccomandazione o aprire la pagina controlli applicazioni adattivi per visualizzare l'elenco delle applicazioni e dei gruppi di computer noti consigliati.

1. Aprire il dashboard Azure Defender e nell'area Protezione avanzata selezionare **Controlli applicazione adattivi.**

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Apertura di controlli applicazioni adattivi dal dashboard di Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Verrà **visualizzata la pagina Controlli applicazioni** adattivi con le macchine virtuali raggruppate nelle schede seguenti:

    - **Configurato:** gruppi di computer che dispongono già di un elenco di applicazioni consentite definito. Per ogni gruppo, la scheda configurata mostra:
        - il numero di computer nel gruppo
        - avvisi recenti

    - **Consigliato:** gruppi di computer che eseguono in modo coerente le stesse applicazioni e non dispongono di un elenco di computer consentiti configurato. È consigliabile abilitare i controlli applicazioni adattivi per questi gruppi.
    
      > [!TIP]
      > Se viene visualizzato un nome di gruppo con il prefisso "REVIEWGROUP", contiene i computer con un elenco parzialmente coerente di applicazioni. Il Centro sicurezza non può visualizzare un modello, ma  consiglia di esaminare questo gruppo per verificare se è possibile definire manualmente alcune regole di controlli applicazioni adattivi, come descritto in Modifica della regola dei controlli applicazioni adattivi [di un gruppo.](#edit-a-groups-adaptive-application-controls-rule)
      >
      > È anche possibile spostare i computer da questo gruppo ad altri gruppi, come descritto in [Spostare un computer da un gruppo a un altro.](#move-a-machine-from-one-group-to-another)

    - **Nessuna raccomandazione:** computer senza un elenco di applicazioni consentite definito e che non supportano la funzionalità. Il computer potrebbe essere in questa scheda per i motivi seguenti:
      - Manca un agente di Log Analytics
      - L'agente di Log Analytics non invia eventi
      - Si tratta di un computer Windows con un criterio [di AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) preesistnte abilitato da un oggetto Criteri di gruppo o da un criterio di sicurezza locale

      > [!TIP]
      > Il Centro sicurezza richiede almeno due settimane di dati per definire le raccomandazioni univoche per ogni gruppo di computer. I computer creati di recente o che appartengono a sottoscrizioni abilitate solo di recente con Azure Defender, verranno visualizzati nella **scheda Nessuna raccomandazione.**


1. Aprire la **scheda Consigliato.** Vengono visualizzati i gruppi di computer con elenchi consentiti consigliati.

   ![Scheda Consigliata](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Selezionare un gruppo. 

1. Per configurare la nuova regola, esaminare  le diverse sezioni di questa pagina Configura regole di controllo dell'applicazione e il contenuto, che sarà univoco per questo gruppo specifico di computer:

   ![Configurare una nuova regola](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Seleziona computer:** per impostazione predefinita, vengono selezionati tutti i computer nel gruppo identificato. Deselezionare qualsiasi per rimuovere i dati da questa regola.
   
   1. **Applicazioni consigliate:** esaminare questo elenco di applicazioni comuni ai computer all'interno di questo gruppo ed è consigliabile che sia consentita l'esecuzione.
   
   1. **Altre applicazioni:** esaminare questo elenco di applicazioni che vengono viste meno frequentemente nei computer all'interno di questo gruppo o sono note per essere sfruttabili. Un'icona di avviso indica che un'applicazione specifica potrebbe essere usata da un utente malintenzionato per ignorare un elenco di applicazioni consentite. È consigliabile esaminare attentamente queste applicazioni.

      > [!TIP]
      > Entrambi gli elenchi di applicazioni includono l'opzione per limitare un'applicazione specifica a determinati utenti. Adottare il principio dei privilegi minimi quando possibile.
      > 
      > Le applicazioni vengono definite dagli editori, se un'applicazione non dispone di informazioni sull'editore (non firmate), viene creata una regola di percorso per il percorso completo dell'applicazione specifica.

   1. Per applicare la regola, selezionare **Controlla**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Modificare la regola dei controlli applicazioni adattivi di un gruppo

È possibile decidere di modificare l'elenco di computer consentiti per un gruppo di computer a causa di modifiche note all'interno dell'organizzazione. 

Per modificare le regole per un gruppo di computer:

1. Aprire il dashboard Azure Defender e nell'area Protezione avanzata selezionare **Controlli applicazione adattivi.**

1. Nella scheda **Configurato** selezionare il gruppo con la regola da modificare.

1. Esaminare le varie sezioni della pagina Configurare **le regole di controllo delle** applicazioni come descritto in Abilitare i controlli applicazioni [adattivi in un gruppo di computer.](#enable-application-controls-on-a-group-of-machines)

1. Facoltativamente, aggiungere una o più regole personalizzate:

   1. Selezionare **Aggiungi regola.**

      ![Aggiungere una regola personalizzata](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Se si sta definendo un percorso sicuro noto, modificare **il** tipo di regola in "Percorso" e immettere un singolo percorso. È possibile includere caratteri jolly nel percorso.
   
      > [!TIP]
      > Alcuni scenari per cui i caratteri jolly in un percorso possono essere utili:
      > 
      > * Uso di un carattere jolly alla fine di un percorso per consentire tutti i file eseguibili all'interno di questa cartella e sottocartelle.
      > * Uso di un carattere jolly al centro di un percorso per abilitare un nome eseguibile noto con un nome di cartella che cambia (ad esempio, cartelle utente personali contenenti un eseguibile noto, nomi di cartella generati automaticamente e così via).
  
   1. Definire gli utenti consentiti e i tipi di file protetti.

   1. Al termine della definizione della regola, selezionare **Aggiungi**.

1. Per applicare le modifiche, selezionare **Salva.**


## <a name="review-and-edit-a-groups-settings"></a>Esaminare e modificare le impostazioni di un gruppo

1. Per visualizzare i dettagli e le impostazioni del gruppo, selezionare **Impostazioni gruppo**

    Questo riquadro mostra il nome del gruppo (che può essere modificato), il tipo di sistema operativo, la posizione e altri dettagli rilevanti.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Pagina delle impostazioni del gruppo per i controlli dell'applicazione adattivi" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Facoltativamente, modificare le modalità di protezione del nome o del tipo di file del gruppo.

1. Selezionare **Applica** e **salva**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Rispondere alla raccomandazione "Le regole dell'elenco consentiti nei criteri di controllo delle applicazioni adattive devono essere aggiornate"

Questa raccomandazione verrà visualizzata quando l'apprendimento automatico del Centro sicurezza identifica un comportamento potenzialmente legittimo che in precedenza non era consentito. La raccomandazione suggerisce nuove regole per le definizioni esistenti per ridurre il numero di avvisi falsi positivi.

Per risolvere i problemi:

1. Nella pagina raccomandazioni selezionare la raccomandazione **Allowlist rules in your adaptive application control policy should be updated** per visualizzare i gruppi con un comportamento appena identificato e potenzialmente legittimo.

1. Selezionare il gruppo con la regola da modificare.

1. Esaminare le varie sezioni della pagina **Configurare le regole di controllo** dell'applicazione come descritto in Abilitare i controlli delle applicazioni [adattivi in un gruppo di computer.](#enable-application-controls-on-a-group-of-machines)

1. Per applicare le modifiche, selezionare **Controlla**.




## <a name="audit-alerts-and-violations"></a>Controllare avvisi e violazioni

1. Aprire il dashboard Azure Defender e nell'area Protezione avanzata selezionare **Controlli applicazione adattivi**.

1. Per visualizzare i gruppi con computer con avvisi recenti, esaminare i gruppi elencati nella **scheda** Configurato.

1. Per approfondire la ricerca, selezionare un gruppo.

   ![Avvisi recenti](./media/security-center-adaptive-application/recent-alerts.png)

1. Per altri dettagli e per l'elenco dei computer interessati, selezionare un avviso.

    La pagina degli avvisi mostra più dettagli degli avvisi e fornisce un collegamento Azione **con** consigli su come attenuare la minaccia.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="L'ora di inizio degli avvisi dei controlli delle applicazioni adattivi è il ":::

    > [!NOTE]
    > I controlli dell'applicazione adattivi calcolano gli eventi una volta ogni dodici ore. L'"ora di inizio dell'attività" visualizzata nella pagina degli  avvisi è l'ora in cui i controlli applicazioni adattivi hanno creato l'avviso, non l'ora in cui il processo sospetto è stato attivo.


## <a name="move-a-machine-from-one-group-to-another"></a>Spostare un computer da un gruppo a un altro

Quando si sposta un computer da un gruppo a un altro, i criteri di controllo delle applicazioni applicati cambiano in base alle impostazioni del gruppo in cui è stato spostato. È anche possibile spostare un computer da un gruppo configurato a un gruppo non configurato, rimuovendo così tutte le regole di controllo delle applicazioni applicate al computer.

1. Aprire il dashboard Azure Defender e nell'area Protezione avanzata selezionare **Controlli applicazione adattivi.**

1. Nella pagina **Controlli applicazioni adattivi** selezionare **il** gruppo contenente il computer da spostare nella scheda Configurato.

1. Aprire l'elenco  **Computer configurati**.

1. Aprire il menu del computer da tre punti alla fine della riga e selezionare **Sposta.** Verrà **visualizzato il riquadro Sposta computer in un gruppo** diverso.

1. Selezionare il gruppo di destinazione e selezionare **Sposta computer.**

1. Per salvare le modifiche, selezionare **Salva**.





## <a name="manage-application-controls-via-the-rest-api"></a>Gestire i controlli delle applicazioni tramite l'API REST 

Per gestire i controlli applicazioni adattivi a livello di codice, usare l'API REST. 

La documentazione dell'API pertinente è [disponibile nella sezione Controlli applicazioni adattivi della](/rest/api/securitycenter/adaptiveapplicationcontrols)documentazione sulle API del Centro sicurezza.

Alcune delle funzioni disponibili dall'API REST:

* **List** recupera tutte le raccomandazioni del gruppo e fornisce un json con un oggetto per ogni gruppo.

* **Get** recupera il codice JSON con i dati di raccomandazione completi, ovvero l'elenco di computer, le regole di pubblicazione/percorso e così via.

* **Put** configura la regola (usare il codice JSON recuperato con **Get** come corpo per questa richiesta).
 
   > [!IMPORTANT]
   > La **funzione Put** prevede un numero di parametri inferiore a quello contenuto nel codice JSON restituito dal comando Get.
   >
   > Rimuovere le proprietà seguenti prima di usare il codice JSON nella richiesta Put: recommendationStatus, configurationStatus, issues, location e sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Domande frequenti - Controlli applicazioni adattivi

- [Sono disponibili opzioni per applicare i controlli dell'applicazione?](#are-there-any-options-to-enforce-the-application-controls)
- [Perché viene visualizzata un'app Qualys nelle applicazioni consigliate?](#why-do-i-see-a-qualys-app-in-my-recommendeded-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Sono disponibili opzioni per applicare i controlli dell'applicazione?
Non sono attualmente disponibili opzioni di imposizione. I controlli delle applicazioni adattivi sono destinati a fornire avvisi **di** sicurezza se un'applicazione viene eseguita in modo diverso da quelli definiti come sicuri. Hanno una gamma di vantaggi ( Quali sono i vantaggi dei controlli delle applicazioni[adattivi?](#what-are-the-benefits-of-adaptive-application-controls)) e sono estremamente personalizzabili come illustrato in questa pagina.

### <a name="why-do-i-see-a-qualys-app-in-my-recommendeded-applications"></a>Perché viene visualizzata un'app Qualys nelle applicazioni consigliate?
[Azure Defender server include l'analisi](defender-for-servers-introduction.md) delle vulnerabilità per i computer senza costi aggiuntivi. Non è necessaria una licenza Qualys, né un account Qualys: tutto viene gestito senza interruzioni all'interno del Centro sicurezza. Per informazioni dettagliate su questo scanner e per istruzioni su come distribuirlo, vedere la soluzione di valutazione integrata delle vulnerabilità [di Defender.](deploy-vulnerability-assessment-vm.md)

Per assicurarsi che non siano generati avvisi quando il Centro sicurezza distribuisce lo scanner, l'elenco dei controlli delle applicazioni adattivi consigliati include lo scanner per tutti i computer. 


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare il controllo delle applicazioni adattive in Centro sicurezza di Azure per definire elenchi consentiti di applicazioni in esecuzione nei computer Azure e non Azure. Per altre informazioni su alcune delle altre funzionalità di protezione del carico di lavoro cloud del Centro sicurezza, vedere:

* [Informazioni sull'accesso JIT alle macchine virtuali](just-in-time-explained.md)
* [Protezione dei cluster Azure Kubernetes](defender-for-kubernetes-introduction.md)