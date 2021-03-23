---
title: Informazioni sull'integrazione di Cisco ISE pxGrid
description: Il bridging delle funzionalità di Defender per le cose con Cisco ISE pxGrid offre ai team di sicurezza senza precedenti la visibilità dei dispositivi nell'ecosistema aziendale.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784085"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Informazioni sull'integrazione di Cisco ISE pxGrid

Defender per le cose offre l'unica piattaforma ICS e Cybersecurity creata da esperti del team blu con un track record che difende l'infrastruttura nazionale critica e l'unica piattaforma con analisi delle minacce e Machine Learning brevettate compatibili con ICS. Defender per l'it fornisce:

- Informazioni immediate sui dispositivi ICS, sulle vulnerabilità e sulle minacce note e zero-day.

- Conoscenza incorporate approfondita con compatibilità con ICS di protocolli, dispositivi, applicazioni e comportamenti.

- Una tecnologia di modellazione delle minacce ICS automatizzata per prevedere i percorsi più probabili degli attacchi ICS mirati tramite l'analisi proprietaria.

## <a name="powerful-device-visibility-and-control"></a>Visibilità e controllo potenti del dispositivo

Il Defender per l'integrazione con Cisco ISE pxGrid fornisce un nuovo livello di visibilità centralizzata, monitoraggio e controllo per il paesaggio OT.

Queste piattaforme con bridging consentono la visibilità e la protezione automatica dei dispositivi per i dispositivi IIoT e ICS non raggiungibili in precedenza.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Visibilità del dispositivo ICS e IIoT: completo e approfondito

Il Defender brevettato per le tecnologie di Internet delle cose garantisce l'individuazione completa e approfondita di dispositivi IIoT e la gestione dell'inventario per i dati aziendali.

I tipi di dispositivi, i produttori, le porte aperte, i numeri di serie, le revisioni del firmware, gli indirizzi IP e i dati degli indirizzi MAC e altro ancora vengono aggiornati in tempo reale. Defender for Internet è in grado di migliorare ulteriormente la visibilità, l'individuazione e l'analisi da questa baseline integrando con origini dati aziendali critiche. Ad esempio, CMDBs, DNS, firewall e API Web.

Inoltre, la piattaforma Defender for Internet include il monitoraggio passivo e le tecniche di sondaggio selettivo facoltative per fornire l'inventario più accurato e dettagliato dei dispositivi nelle organizzazioni di infrastruttura industriali e critiche.

### <a name="bridged-capabilities"></a>Funzionalità con bridging

Il bridging di queste funzionalità con Cisco ISE pxGrid offre ai team di sicurezza la visibilità dei dispositivi senza precedenti per l'ecosistema aziendale.

Una facile integrazione con Cisco ISE pxGrid garantisce che non vengano individuati dispositivi OT e che non vengano perse informazioni sul dispositivo.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Esempio delle categorie di endpoint OUI.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Endpoint di esempio nel sistema":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Screenshot degli attributi presenti nel sistema.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Copertura del caso d'uso: criteri di ISE basati su Defender per gli attributi Internet

USA i potenti criteri di ISE basati su Defender per l'apprendimento approfondito per gestire i requisiti dei casi d'uso di ICS e Internet.

L'utilizzo dei criteri consente di chiudere il ciclo di sicurezza e di consentirne la conformità in tempo reale.

Ad esempio, i clienti possono usare Defender per gli elementi ICS e Internet degli elementi per creare criteri che gestiscono i casi d'uso seguenti, ad esempio:

- Creare un criterio di autorizzazione per consentire ai dispositivi noti e autorizzati di accedere a una zona sensibile basata sugli attributi consentiti, ad esempio una versione specifica del firmware per i PLC di automazione Rockwell.

- Invia una notifica ai team di sicurezza quando viene rilevato un dispositivo ICS in una zona non OT.

- Monitorare e aggiornare i computer con fornitori obsoleti o non conformi.

- Mettere in quarantena e bloccare i dispositivi secondo necessità.

- Generare report su PLC o RTU che eseguono firmware con vulnerabilità note (CVEs).

### <a name="about-this-article"></a>Informazioni sull'articolo

Questo articolo descrive come configurare pxGrid e il Defender per la piattaforma Internet per assicurarsi che Defender per l'it inserisca attributi OT per Cisco ISE.

### <a name="getting-more-information"></a>Ottenere altre informazioni

Per ulteriori informazioni sui requisiti di integrazione di Cisco ISE pxGrid, vedere <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Requisiti del sistema di integrazione

Questo articolo descrive i requisiti di sistema di integrazione:

Defender per i requisiti di Internet delle cose

- Defender per la versione 2,5

Requisiti Cisco

- pxGrid versione 2,0

- Cisco ISE versione 2,4

Requisiti di rete

- Verificare che la protezione per l'appliance per le cose sia in grado di accedere all'interfaccia di gestione di Cisco ISE.

- Verificare di disporre dell'accesso dell'interfaccia della riga di comando a tutte le applicazioni Defender per l'intera azienda.

> [!NOTE]
> Solo i dispositivi con indirizzi MAC sono sincronizzati con Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Installazione di Cisco ISE pxGrid

Questo articolo illustra come:

- Configurare la comunicazione con pxGrid

- Sottoscrivere l'argomento relativo al dispositivo endpoint

- Generare i certificati

- Definire le impostazioni di pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Configurare la comunicazione con pxGrid

Questo articolo descrive come configurare la comunicazione con pxGrid.

Per configurare la comunicazione:

1. Accedere a Cisco ISE.

1. Selezionare il > **sistema** di amministrazione e la **distribuzione**.

1. Selezionare il nodo obbligatorio. Nella scheda Impostazioni generali selezionare la casella di controllo **pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Verificare che sia selezionata la casella di controllo pxgrid.":::

1. Selezionare la scheda **configurazione della profilatura** .

1. Selezionare la **casella** di controllo pxGrid. aggiungere una descrizione

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Screenshot della descrizione Aggiungi":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Sottoscrivere l'argomento relativo al dispositivo endpoint

Verificare che il nodo pxGrid ISE abbia sottoscritto l'argomento relativo al dispositivo endpoint. Passare ad **Amministrazione** > **Servizi pxGrid** > **Web client**. Qui è possibile verificare che ISE abbia sottoscritto l'argomento relativo al dispositivo endpoint.

## <a name="generate-certificates"></a>Generare i certificati

Questo articolo descrive come generare i certificati.

Per generare:

1. Selezionare **Amministrazione**  >  **Servizi pxGrid**, quindi selezionare **certificati**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Selezionare la scheda certificati per generare un certificato.":::

1. Nel campo **desidero** selezionare **genera un singolo certificato (senza una richiesta di firma del certificato)**.

1. Compilare i campi rimanenti e selezionare **Crea**.

1. Creare la chiave del certificato client e il certificato del server, quindi convertirli nel formato di archivio chiavi Java. È necessario copiarli in ogni Defender per il sensore di Internet delle cose nella rete.

## <a name="define-pxgrid-settings"></a>Definire le impostazioni di pxGrid

Per definire le impostazioni:

1. Selezionare **Amministrazione**  >  **Servizi pxGrid** , quindi selezionare **Impostazioni**.

1. Consente di **approvare automaticamente nuovi account basati su certificati** e **consentire la creazione di account basati su password.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Verificare che siano selezionate entrambe le caselle di controllo.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Configurare il Defender per gli elettrodomestici

Questo articolo descrive come configurare Defender per le appliance di Internet delle cose per comunicare con pxGrid. La configurazione deve essere eseguita su tutti i Defender per le appliance Internet che inseriranno i dati in Cisco ISE.

Per configurare i dispositivi:

1. Accedere all'interfaccia della riga di comando del sensore.

1. Copiare `trust.jks` e che sono stati creati in precedenza nel sensore. Copiarli in: `/var/cyberx/properties/` .

1. Modificare `/var/cyberx/properties/pxgrid.properties`:

    1. Aggiungere una chiave e un trust, quindi archiviare i nomi file e le password.

    2. Aggiungere il nome host dell'istanza di pxGrid.

    3. `Enabled=true`

1. Riavviare l'appliance.

1. Ripetere questi passaggi per ogni appliance nell'azienda in cui inserire i dati.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Visualizzare e gestire i rilevamenti in Cisco ISE

1. I rilevamenti degli endpoint vengono visualizzati nella   >  scheda **endpoint** di visibilità del contesto ISE.

1. Selezionare la  >  **profilatura** dei criteri  >  **Aggiungi**  >  **regole**  >  **+ condizione**  >  **Crea nuova condizione**.

1. Selezionare l' **attributo** e usare i dizionari per i dispositivi per creare una regola di profilatura basata sugli attributi inseriti. Vengono inseriti gli attributi seguenti.

    - Tipo di dispositivo

    - Revisione hardware

    - Indirizzo IP

    - Indirizzo MAC

    - Nome

    - Product ID

    - Protocollo

    - Numero di serie

    - Revisione software

    - Vendor

Vengono sincronizzati solo i dispositivi con indirizzi MAC.

## <a name="troubleshooting-and-logs"></a>Risoluzione dei problemi e log

I log sono disponibili in:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [inviare le informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md).
