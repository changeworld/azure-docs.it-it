---
title: Aggiornare i dati di intelligence per le minacce
description: Il pacchetto di dati di intelligence per le minacce viene fornito con ogni nuova versione di Defender per IoT o, se necessario, tra una versione e l'altra.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750465"
---
# <a name="threat-intelligence-research-and-packages"></a>Ricerca e pacchetti di intelligence per le minacce #
## <a name="overview"></a>Panoramica ##

I team di sicurezza di Microsoft svolgono ricerche proprietarie sulle minacce e sulle vulnerabilità ICS. Questi team includono MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (Digital Crimes Unit) e Sezione 52 (esperti del dominio IoT/OT/ICS che tiene traccia di zero giorni specifici di ICS, malware di reverse engineering, campagne e avversari)

I team forniscono funzionalità di rilevamento della sicurezza, analisi e risposta a Microsoft:

- Infrastruttura e servizi cloud.
- Prodotti e dispositivi tradizionali.
- Risorse aziendali interne.

I team di sicurezza ottengono i vantaggi seguenti:

- Protezione da minacce note e rilevanti.
- Informazioni dettagliate che consentono di classificare e classificare in ordine di priorità.
- Conoscenza del contesto completo delle minacce prima che ne siano interessate.
- Dati più rilevanti, accurati e utilizzabili.

Questa intelligence fornisce informazioni contestuali per arricchire l'analisi della piattaforma Microsoft e supporta i servizi gestiti dell'azienda per la risposta agli eventi imprevisti e l'analisi delle violazioni. I pacchetti di intelligence per le minacce contengono firme (incluse le firme malware), CVA e altri contenuti sulla sicurezza.

## <a name="when-are-packages-delivered"></a>Quando vengono recapitati i pacchetti ##

I pacchetti di intelligence per le minacce vengono forniti circa una volta al mese o, se necessario, più frequentemente. Gli annunci sui nuovi pacchetti sono disponibili da: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Aggiornare i pacchetti di intelligence per le minacce ai sensori ##

Sono disponibili tre opzioni per l'aggiornamento dei pacchetti di intelligence per le minacce ai sensori:

- Eseguire automaticamente il push dei pacchetti ai sensori quando vengono recapitati da Defender per IoT.
- Eseguire manualmente il push del pacchetto di intelligence per le minacce ai sensori in base alle esigenze.
- Scaricare un pacchetto e quindi caricarlo in uno o più sensori.

Gli utenti con autorizzazioni defender per ioT Security Reader possono eseguire automaticamente e manualmente il push dei pacchetti ai sensori.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Eseguire automaticamente il push degli aggiornamenti dell'intelligence per le minacce ai sensori ###

I pacchetti di intelligence per le minacce possono essere aggiornati automaticamente ai sensori connessi al *cloud* quando vengono rilasciati da Defender per IoT. Assicurarsi l'aggiornamento automatico dei pacchetti tramite l'onboarding del sensore connesso al cloud con **l'opzione Aggiornamenti automatici di Threat Intelligence abilitata.** Per altre informazioni, vedere [Onboard di un sensore.](getting-started.md#onboard-a-sensor)

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Eseguire manualmente il push degli aggiornamenti dell'intelligence per le minacce ai sensori ###

I *sensori connessi al cloud* possono essere aggiornati automaticamente con i pacchetti di intelligence per le minacce. Tuttavia, se si vuole adottare un approccio più conservativo, è possibile eseguire il push dei pacchetti dal portale di Azure Defender per IoT ai sensori solo quando lo si desidera.
In questo modo è possibile controllare quando viene installato un pacchetto, senza la necessità di scaricarlo e quindi caricarlo nei sensori.

**Per eseguire manualmente il push dei pacchetti:**

1. Passare alla pagina Azure Defender per IoT **Siti e sensori.**
1. Selezionare i puntini di sospensione (...) per un sensore e quindi selezionare **Push Threat Intelligence update (Aggiornamento push di Threat Intelligence).** Nel **campo Stato aggiornamento di Threat Intelligence** viene visualizzato lo stato dell'aggiornamento.

#### <a name="change-the-threat-intelligence-update-mode"></a>Modificare la modalità di aggiornamento dell'intelligence per le minacce ####

È possibile modificare la modalità di aggiornamento dell'intelligence per le minacce del sensore dopo l'onboarding iniziale.

**Per modificare la modalità di aggiornamento:**

1. Selezionare i puntini di sospensione (...) per un sensore e quindi selezionare **Modifica.**
1. Abilitare o disabilitare **l'interruttore Aggiornamenti automatici di Intelligence per le** minacce.

### <a name="download-packages-and-upload-to-sensors"></a>Scaricare i pacchetti e caricarli nei sensori ###

I pacchetti possono essere scaricati dal portale di Defender per IoT e caricati manualmente nei singoli sensori. Se la console di gestione locale gestisce i sensori, è possibile scaricare i pacchetti di intelligence per le minacce nella console di gestione ed eseguire il push dei pacchetti in più sensori contemporaneamente.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Scaricare gli aggiornamenti tramite Azure Defender per IoT portale.":::

Questa opzione è disponibile sia per i *sensori connessi al cloud che* per i sensori gestiti *in* locale.

**Per caricare in un singolo sensore:**

1. Passare alla pagina Azure Defender per IoT **Aggiornamenti.**

2. Scaricare e salvare il **pacchetto di intelligence per le** minacce.

3. Accedere alla console del sensore.

4. Nel menu laterale selezionare Impostazioni **di sistema.**

5. Selezionare **Threat Intelligence Data (Dati di** intelligence sulle minacce) e quindi Update **(Aggiorna).**

6. Caricare il nuovo pacchetto.

**Per caricare più sensori contemporaneamente:**

1. Passare alla pagina Azure Defender per IoT **Aggiornamenti.**

2. Scaricare e salvare il **pacchetto threat intelligence.**

3. Accedere alla console di gestione.

4. Nel menu laterale selezionare Impostazioni **di sistema.**

5. Nella sezione **Sensor Engine Configuration (Configurazione** motore sensore) selezionare i sensori che devono ricevere i pacchetti aggiornati.  

6. Nella sezione **Selezione dati di Threat Intelligence** selezionare il segno più ( **+** ).

7. Caricare il pacchetto.

## <a name="review-package-update-status-on-the-sensor"></a>Esaminare lo stato di aggiornamento del pacchetto nel sensore ##

Le informazioni sullo stato e sulla versione dell'aggiornamento del pacchetto vengono visualizzate nella sezione **Impostazioni di** sistema del sensore , **Threat Intelligence.**  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Esaminare le informazioni sui pacchetti per i sensori connessi al cloud ##

Esaminare le informazioni seguenti sui pacchetti di intelligence per le minacce per i sensori connessi al cloud:

- Versione del pacchetto installata
- Modalità di aggiornamento di Threat Intelligence
- Stato dell'aggiornamento dell'intelligence per le minacce

Per esaminare le informazioni sull'intelligence per le minacce:

1. Passare alla pagina Azure Defender per IoT **Siti e sensori.**
1. Esaminare la **versione di Threat Intelligence** installata in ogni sensore. La denominazione delle versioni si basa sul giorno in cui il pacchetto è stato compilato da Defender per IoT.
1. Esaminare la **modalità Di intelligence per le minacce** . *Automatico* indica che i nuovi pacchetti disponibili verranno installati automaticamente nei sensori quando vengono rilasciati da Defender per IoT. *Manuale* indica che è possibile eseguire il push dei nuovi pacchetti disponibili direttamente ai sensori in base alle esigenze.
1. Esaminare lo stato **di aggiornamento di Threat Intelligence.** Possono essere visualizzati gli stati seguenti:

- Non riuscito
- In corso
- Aggiornamento disponibile
- OK

Se gli aggiornamenti di Intelligence per le minacce  connesse al cloud hanno esito negativo, esaminare le informazioni di connessione nelle colonne **Stato** sensore e Utc Ultima connessione **nella pagina Siti e** sensori. 

## <a name="see-also"></a>Vedi anche

[Eseguire l'onboarding di un sensore](getting-started.md#onboard-a-sensor)

[Gestire i sensori dalla console di gestione](how-to-manage-sensors-from-the-on-premises-management-console.md)