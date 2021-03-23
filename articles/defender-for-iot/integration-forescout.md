---
title: Informazioni sull'integrazione di ForeScout
description: Azure Defender per l'integrazione con la piattaforma ForeScout fornisce un nuovo livello di visibilità, monitoraggio e controllo centralizzato per le cose e il panorama.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784068"
---
# <a name="about-the-forescout-integration"></a>Informazioni sull'integrazione di ForeScout

Azure Defender per Internet delle cose offre una piattaforma ICS e Cybersecurity creata da esperti del team blu con una traccia di difesa dell'infrastruttura nazionale critica. Defender for Internet è l'unica piattaforma con l'analisi delle minacce e l'apprendimento automatico compatibili con ICS. Defender per l'it fornisce:

- Informazioni immediate su ICS il paesaggio del dispositivo con una vasta gamma di dettagli sugli attributi.
- Conoscenza incorporate approfondita con compatibilità con ICS di protocolli, dispositivi, applicazioni e comportamenti.
- Informazioni immediate sulle vulnerabilità e minacce note e zero-day.
- Una tecnologia di modellazione delle minacce ICS automatizzata per prevedere i percorsi più probabili degli attacchi ICS mirati tramite l'analisi proprietaria.

Il Defender per l'integrazione con la piattaforma ForeScout fornisce un nuovo livello di visibilità, monitoraggio e controllo centralizzato per le cose e il panorama.

Queste piattaforme con bridging consentono la visibilità e la gestione automatizzate dei dispositivi a dispositivi ICS precedentemente non raggiungibili e a flussi di lavoro silo.

L'integrazione fornisce analisti SOC con visibilità a più livelli nei protocolli OT distribuiti in ambienti industriali. Sono disponibili informazioni dettagliate per elementi quali firmware, tipi di dispositivi, sistemi operativi e punteggi di analisi dei rischi basati su Azure Defender proprietario per le tecnologie Internet.

> [!Note]
> I riferimenti a CyberX fanno riferimento ad Azure Defender per l'it.
## <a name="devices"></a>Dispositivi

### <a name="device-visibility-and-management"></a>Visibilità e gestione dei dispositivi

L'inventario del dispositivo è arricchito con gli attributi critici rilevati dal Defender per la piattaforma Internet. Ciò consente di:

- Ottieni visibilità completa e continua nel panorama del dispositivo OT da un singolo riquadro di vetro.
- Ottenere informazioni in tempo reale sui rischi di OT.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventario dei dispositivi":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Dettagli dispositivo":::

### <a name="device-control"></a>Controllo del dispositivo

L'integrazione di ForeScout consente di ridurre il tempo necessario per le organizzazioni di infrastruttura industriali e critiche per rilevare, analizzare e agire su minacce informatiche.

- Usare Azure Defender per le funzionalità di Business Intelligence per dispositivi per chiudere il ciclo di sicurezza attivando azioni dei criteri di ForeScout. Ad esempio, è possibile inviare automaticamente un messaggio di posta elettronica di avviso agli amministratori SOC quando vengono rilevati protocolli specifici o quando i dettagli del firmware cambiano.

- Correlare Defender per informazioni su Internet con altri moduli *ForeScout eyeExtended* che controllano il monitoraggio, la gestione degli eventi imprevisti e il controllo dei dispositivi.

## <a name="system-requirements"></a>Requisiti di sistema

- Azure Defender per la versione 2,4 o successiva
- ForeScout versione 8,0 o successiva
- Una licenza per il modulo *ForeScout eyeExtend* per Azure Defender per la piattaforma Internet.

### <a name="getting-more-forescout-information"></a>Ottenere altre informazioni su ForeScout

Per ulteriori informazioni sulla piattaforma ForeScout, vedere il [Centro risorse di ForeScout](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Installazione del sistema

Questo articolo descrive come configurare la comunicazione tra il Defender per la piattaforma Internet e la piattaforma ForeScout.

### <a name="set-up-the-defender-for-iot-platform"></a>Configurare il Defender per la piattaforma Internet

Per garantire la comunicazione da Defender for ForeScout, generare un token di accesso in Defender per l'intero.

I token di accesso consentono ai sistemi esterni di accedere ai dati individuati da Defender per l'IT e di eseguire azioni con tali dati usando l'API REST esterna, tramite le connessioni SSL. È possibile generare token di accesso per accedere ad Azure Defender per l'API REST di Azure.

Per generare un token:

1. Accedere alla pagina Defender per il sensore Internet delle cose che verrà sottoposta a query da ForeScout.

1. Selezionare **impostazioni di sistema** , quindi selezionare **token di accesso** dalla sezione **generale** . Verrà visualizzata la finestra di dialogo **token di accesso** .
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Token di accesso":::
1. Selezionare **genera nuovo token** dalla finestra di dialogo **token di accesso** .
1. Immettere una descrizione del token nella finestra di dialogo **nuovo token di accesso** .
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Nuovo token di accesso":::
1. Selezionare **Avanti**. Il token viene visualizzato nella finestra di dialogo. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Visualizza token":::
   > [!NOTE]
   > *Registrare il token in un luogo sicuro. Sarà necessario quando si configura la piattaforma ForeScout*.
1. Selezionare **Fine**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Fine aggiunta token":::

### <a name="set-up-the-forescout-platform"></a>Configurare la piattaforma ForeScout

È possibile configurare la piattaforma ForeScout per comunicare con un Defender per il sensore Internet.

Per configurare:

1. Installare *il modulo ForeScout eyeExtend per CyberX* nella piattaforma ForeScout.

1. Accedere alla console di contrasto e scegliere **Opzioni** dal menu **strumenti** . Verrà visualizzata la finestra di dialogo **Opzioni** .

1. Passare a e selezionare la cartella **moduli** .

1. Nel riquadro **moduli** selezionare **piattaforma CyberX**. Viene visualizzato il riquadro Defender for Internets Platform.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Impostazioni del modulo Azure Defender per le cose":::

   Immettere le seguenti informazioni:

   - **Indirizzo server** : immettere l'indirizzo IP di Defender per il sensore Internet che verrà sottoposto a query dal dispositivo ForeScout.
   - **Token di accesso** : immettere il token di accesso generato per il sensore Defender for Internet che si connetterà al dispositivo ForeScout. Per generare un token, vedere [configurare il Defender per la piattaforma](#set-up-the-defender-for-iot-platform)Internet.

1. Selezionare **Applica**.

Se si vuole che la piattaforma ForeScout comunichi con un altro sensore:

1. Creare un nuovo token di accesso nel Defender pertinente per il sensore Internet.

1. Nella finestra di dialogo **ForeScout Modules**  >  **CyberX Platform** :

   - Elimina le informazioni visualizzate.
   
   - Immettere il nuovo indirizzo IP del sensore e le nuove informazioni sul token di accesso.

### <a name="verify-communication"></a>Verifica comunicazione

Dopo aver configurato Defender per le cose e ForeScout, aprire la finestra di dialogo token di accesso del sensore in Defender per tutto.

Se **N/A** viene visualizzato nel campo **usato** per questo token, la connessione tra il sensore e l'appliance ForeScout non funziona.

**Usato** indica l'ultima volta che è stata ricevuta una chiamata esterna con questo token.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Verifica che il token sia stato ricevuto":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Visualizza il Defender per i rilevamenti delle cose in ForeScout

Per visualizzare gli attributi di un dispositivo:

1. Accedere alla piattaforma ForeScout e quindi passare all' **inventario asset**.

1. Passare alla **piattaforma CyberX**. Gli attributi del dispositivo seguenti vengono visualizzati per i dispositivi OT rilevati da Defender per l'it.

   | Elemento | Descrizione |
   |--|--|
   | Autorizzato da Azure Defender per l'it | Un dispositivo rilevato nella rete da Defender per tutto il periodo di apprendimento della rete. |
   | Firmware | Dettagli del firmware del dispositivo. Ad esempio, Model e Version details. |
   | Name | Nome del dispositivo. |
   | Sistema operativo | Sistema operativo del dispositivo. |
   | Tipo | Tipo di dispositivo. Ad esempio, un PLC, uno storico o una stazione di progettazione. |
   | Vendor | Fornitore del dispositivo. Ad esempio, Rockwell Automation. |
   | Livello di rischio | Livello di rischio calcolato da Defender per l'it. |
   | Protocolli | Protocolli rilevati nel traffico generato dal dispositivo. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Visualizzare gli attributi del firmware.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Visualizzare gli attributi dei fornitori.":::

### <a name="viewing-more-details"></a>Visualizzazione di altri dettagli

Visualizza informazioni aggiuntive sul dispositivo per i dispositivi indirizzati da Defender per l'it. Ad esempio, le informazioni sulla conformità e i criteri di ForeScout.

A tale scopo, fare clic con il pulsante destro del mouse su un dispositivo nella sezione **host inventario dispositivi** . Verrà visualizzata la finestra di dialogo Dettagli host con ulteriori informazioni.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Dettagli host":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Creare i criteri di Azure Defender per l'it in ForeScout

I criteri ForeScout possono essere usati per automatizzare il controllo e la gestione dei dispositivi rilevati da Defender per l'it. Ad esempio,

- Invia automaticamente gli amministratori SOC quando vengono rilevate versioni specifiche del firmware.

- Aggiungere un Defender specifico per i dispositivi rilevati in un gruppo di ForeScout per un'ulteriore gestione nei flussi di lavoro di eventi imprevisti e di sicurezza, ad esempio con altre integrazioni SIEM.

Creare un criterio personalizzato ForeScout con Defender per l'uso delle proprietà della condizione.

Per accedere a Defender per le proprietà delle cose:

1. Passare all' **albero delle proprietà** dalla finestra di dialogo **condizioni dei criteri** .

1. Espandere la cartella **CyberX Platform** nell' **albero proprietà**. Sono disponibili le proprietà Defender per l'it.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Proprietà":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [inviare le informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md).
