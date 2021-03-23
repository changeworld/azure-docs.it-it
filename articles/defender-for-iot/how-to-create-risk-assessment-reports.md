---
title: Creazione di report di valutazione dei rischi
description: Ottenere informazioni sui rischi di rete rilevati da singoli sensori o una visualizzazione aggregata dei rischi rilevati da tutti i sensori.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784255"
---
# <a name="risk-assessment-reporting"></a>Creazione di report di valutazione del rischio

## <a name="about-risk-assessment-reports"></a>Informazioni sui report di valutazione dei rischi

I report di valutazione dei rischi forniscono:

- Punteggio di sicurezza complessivo per i dispositivi rilevati dai sensori aziendali.

- Punteggio di sicurezza per ogni dispositivo di rete rilevato da un singolo sensore.

- Suddivisione del numero di dispositivi vulnerabili, dispositivi che richiedono miglioramenti e dispositivi sicuri.

-  Informazioni sulla sicurezza e sui problemi operativi:

    - Problemi di configurazione

    - Vulnerabilità del dispositivo classificata in base al livello di sicurezza

    - Problemi di sicurezza di rete

    - Problemi operativi della rete

    - Connessioni alle reti ICS

    - connessioni Internet

    - Indicatori di malware industriali

    - Problemi relativi al protocollo

    - Attack vectors (Vettori di attacco)

### <a name="risk-mitigation"></a>Mitigazione dei rischi

I report forniscono consigli che consentono di migliorare il Punteggio di sicurezza. Ad esempio, installare gli aggiornamenti della sicurezza più recenti, aggiornare il firmware alla versione più recente o completare gli avvisi.

## <a name="about-security-scores"></a>Informazioni sui punteggi di sicurezza

Il Punteggio di sicurezza di rete complessivo viene generato in ogni report. Il Punteggio rappresenta la percentuale di sicurezza del 100%. Ad esempio, un punteggio pari al 30% indicherà che la rete è protetta al 30%.

I punteggi della valutazione dei rischi sono basati su informazioni apprese dall'ispezione dei pacchetti, dai motori di modellazione comportamentale e da una progettazione di una macchina a stati specifica di SCADA.

I **dispositivi protetti** sono dispositivi con un punteggio di sicurezza superiore al 90%.

**Dispositivi che richiedono un miglioramento**: i dispositivi con un punteggio di sicurezza compreso tra il 70% e il 89%.

I **dispositivi vulnerabili** sono dispositivi con un punteggio di sicurezza inferiore al 70%.

## <a name="create-risk-assessment-reports"></a>Creazione di report di valutazione dei rischi

Creare un report di valutazione del rischio PDF. Il nome del report viene generato automaticamente come risk-assessment-report-1.pdf. Il numero viene aggiornato per ogni nuovo report creato.  Vengono visualizzati l'ora e il giorno della creazione.

### <a name="create-a-sensor-risk-assessment-report"></a>Creare un report di valutazione del rischio del sensore

Creare un report di valutazione dei rischi basato sui rilevamenti effettuati dal sensore a cui si è connessi.

Per creare un report:

1. Accedere alla console del sensore.
1. Selezionare **valutazione dei rischi** nel menu laterale.
1. Selezionare **Genera rapporto**. Il report viene visualizzato nella sezione report archiviati.
1. Selezionare il report nella sezione report archiviati per scaricarlo.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Visualizzazione della valutazione dei rischi.":::

Per importare un logo aziendale:

- Selezionare **Importa logo**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Creazione di un report di valutazione dei rischi per la console di gestione locale

Creare un report di valutazione dei rischi basato sui rilevamenti eseguiti da uno qualsiasi dei sensori gestiti dalla console di gestione locale. 

Per creare un report:

1. Selezionare **valutazione dei rischi** nel menu laterale.

2. Selezionare un sensore nell'elenco a discesa **Seleziona sensore** .

3. Selezionare **Genera rapporto**.

4. Selezionare **download** dalla sezione **report archiviati** .

Per importare un logo aziendale:

- Selezionare **Importa logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importare il logo tramite la vista valutazione dei rischi.":::

## <a name="see-also"></a>Vedi anche

[Segnalazione di vettori di attacco](how-to-create-attack-vector-reports.md)

