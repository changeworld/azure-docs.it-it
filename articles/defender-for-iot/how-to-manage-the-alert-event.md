---
title: Gestione degli eventi di avviso
description: Gestire gli eventi di avviso rilevati nella rete.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: c0670f37da0cead5e3bd05a1d69e17191e8c0ccf
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508744"
---
# <a name="manage-alert-events"></a>Gestione degli eventi di avviso

Per la gestione degli eventi di avviso sono disponibili le opzioni seguenti:

 | Azione | Descrizione |
 |--|--|
 | **Learn** | Autorizzare l'evento rilevato. Per ulteriori informazioni, vedere [informazioni sull'apprendimento e](#about-learning-and-unlearning-events)la disapprendimento degli eventi. |
 | **Riconoscere** | Nascondere l'avviso una volta per l'evento rilevato. L'avviso verrà nuovamente attivato se l'evento viene nuovamente rilevato. Per ulteriori informazioni, vedere [informazioni su come riconoscere e non riconoscere gli eventi](#about-acknowledging-and-unacknowledging-events). |
 | **Disattiva audio** | Ignorare continuamente l'attività con dispositivi identici e traffico analogo. Per ulteriori informazioni, vedere [informazioni sugli eventi di muting e di](#about-muting-and-unmuting-events)esclusione. |

## <a name="about-learning-and-unlearning-events"></a>Informazioni sugli eventi e sull'apprendimento

Gli eventi che indicano deviazioni della rete acquisita possono riflettere modifiche di rete valide. Esempi possono includere un nuovo dispositivo autorizzato che è stato aggiunto alla rete o un aggiornamento del firmware autorizzato.

Quando si seleziona **Learn**, il sensore considera il traffico, le configurazioni o l'attività valida. Questo significa che gli avvisi non verranno più attivati per l'evento. Significa anche che l'evento non viene calcolato quando il sensore genera una valutazione dei rischi, un vettore di attacco e altri report.

Si riceve ad esempio un avviso che indica l'attività di analisi degli indirizzi su un dispositivo che non è stato definito in precedenza da uno scanner di rete. Se il dispositivo è stato aggiunto alla rete ai fini dell'analisi, è possibile indicare al sensore di apprendere il dispositivo come dispositivo di analisi.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="La finestra di analisi rilevata dall'indirizzo.":::

Gli eventi appresi possono essere impresi. Quando il sensore non apprende gli eventi, riattiverà gli avvisi correlati a questo evento.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Informazioni su come riconoscere e non riconoscere gli eventi

In alcune situazioni, potrebbe non essere necessario che un sensore apprenda un evento rilevato o l'opzione potrebbe non essere disponibile. Al contrario, l'evento imprevisto potrebbe richiedere la mitigazione. Ad esempio:

- **Attenuare una configurazione di rete o un dispositivo**: viene visualizzato un avviso che indica che è stato rilevato un nuovo dispositivo sulla rete. Quando si esamina, si scopre che il dispositivo è un dispositivo di rete non autorizzato. Per gestire l'evento imprevisto, disconnettere il dispositivo dalla rete.
- **Aggiornare una configurazione del sensore**: viene visualizzato un avviso che indica che un server ha avviato un numero eccessivo di connessioni remote. Questo avviso è stato generato perché sono state definite soglie di anomalie dei sensori per attivare gli avvisi sopra un determinato numero di sessioni entro un minuto. Per gestire l'evento imprevisto, aggiornare le soglie.

Dopo aver eseguito la mitigazione o l'analisi, è possibile indicare al sensore di nascondere l'avviso selezionando **conferma**. Se l'evento viene nuovamente rilevato, l'avviso verrà riattivato.

Per cancellare l'avviso:

  - Selezionare **conferma**.

Per visualizzare di nuovo l'avviso:

  - Accedere all'avviso e selezionare non **confermare**.

Non riconoscere avvisi se sono necessarie ulteriori indagini.

## <a name="about-muting-and-unmuting-events"></a>Informazioni sugli eventi di muting e di silenziamento

In determinate circostanze, potrebbe essere necessario indicare al sensore di ignorare uno scenario specifico sulla rete. Ad esempio:

  - Il motore delle **anomalie** attiva un avviso su un picco della larghezza di banda tra due dispositivi, ma il picco è valido per questi dispositivi.

  - Il motore di **violazione del protocollo** attiva un avviso sulla deviazione di un protocollo rilevato tra due dispositivi, ma la deviazione è valida tra i dispositivi.

In queste situazioni, l'apprendimento non è disponibile. Quando non è possibile eseguire l'apprendimento e si vuole eliminare l'avviso e rimuovere il dispositivo quando si calcolano i rischi e i vettori di attacco, è invece possibile disattivare l'evento di avviso.

> [!NOTE] 
> Non è possibile disattivare gli eventi in cui un dispositivo Internet è definito come origine o destinazione.

### <a name="what-traffic-is-muted"></a>Quale traffico è disattivato?

Uno scenario disattivato include i dispositivi di rete e il traffico rilevato per un evento. Il titolo dell'avviso descrive il traffico che viene disattivato.

Il dispositivo o i dispositivi da disattivare verranno visualizzati come immagine nell'avviso. Se vengono visualizzati due dispositivi, il traffico di avviso specifico tra di essi verrà disattivato.

**Esempio 1**

Quando un evento viene disattivato, viene ignorato ogni volta che il database primario (origine) Invia il codice di funzione secondario (destinazione) come definito dal fornitore.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Il dispositivo secondario è stato ricevuto.":::

**Esempio 2**

Quando un evento viene disattivato, viene ignorato ogni volta che l'origine invia un'intestazione HTTP con contenuto non valido, indipendentemente dalla destinazione.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Screenshot del contenuto di intestazione HTTP non valido.":::

**Dopo la disattivazione di un evento:**

- L'avviso sarà accessibile nella visualizzazione avvisi **riconosciuti** fino a quando non viene disattivato.

- L'azione di silenziamento verrà visualizzata nella **sequenza temporale dell'evento**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Evento rilevato e disattivato.":::

- Il sensore Ricalcola i dispositivi durante la generazione della valutazione dei rischi, del vettore di attacco e di altri report. Se, ad esempio, è stato disattivato un avviso che ha rilevato traffico dannoso in un dispositivo, il dispositivo non verrà calcolato nel report di valutazione dei rischi.

**Per disattivare e riattivare un avviso:**

- Selezionare l'icona di **silenziamento** nell'avviso.

**Per visualizzare gli avvisi in sordina:**

1. Selezionare l'opzione **riconosciuta** dalla schermata principale **avvisi** .

2. Passare il puntatore del mouse su un avviso per verificare se è disattivato.  

## <a name="see-also"></a>Vedi anche

[Controllare quale traffico viene monitorato](how-to-control-what-traffic-is-monitored.md)
