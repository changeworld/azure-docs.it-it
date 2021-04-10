---
title: Aggiornare i dati di intelligence per le minacce
description: Il pacchetto di dati di intelligence per le minacce viene fornito con ogni nuovo Defender per la versione di Internet delle cose o, se necessario, tra le versioni.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 386d59d33c4f9695b8fc5243dab345321cff4a5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784051"
---
# <a name="threat-intelligence-research-and-packages"></a>Ricerca e pacchetti di intelligence per le minacce

I team addetti alla sicurezza di Microsoft si occupano di intelligence per le minacce e di ricerca delle vulnerabilità. Questi team includono MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), UCD (Digital Crimes Unit) e la sezione 52 (gli esperti di dominio degli utenti/OT/ICS che tengono traccia di zero giorni specifici di ICS, malware inverso, campagne e avversari).

I team forniscono il rilevamento, l'analisi e la risposta alla sicurezza di Microsoft:

- Infrastruttura e servizi cloud.
- Prodotti e dispositivi tradizionali.
- Risorse aziendali interne.

I team addetti alla sicurezza traggono vantaggio da:

- Protezione da minacce note e rilevanti.
- Informazioni dettagliate che consentono di valutare e classificare in ordine di priorità.
- Comprensione del contesto completo delle minacce prima che siano interessate.
- Dati più rilevanti, accurati e interoperabili.

Questa Intelligence aggiunge informazioni contestuali per arricchire Microsoft Platform Analytics e supporta i servizi gestiti della società per la risposta agli eventi imprevisti e l'analisi delle violazioni. I pacchetti di intelligence per le minacce contengono firme (incluse le firme di malware), CVEs e altri contenuti di sicurezza.

È possibile scaricare i pacchetti dalla pagina **aggiornamenti** nel portale di Azure Defender per l'it.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Scaricare gli aggiornamenti tramite il portale di Azure Defender per l'it.":::

## <a name="update-threat-intelligence-data"></a>Aggiornare i dati di intelligence per le minacce

I pacchetti di intelligence per le minacce vengono forniti con ogni nuovo Defender per l'aggiornamento della versione di Internet delle cose o se necessario tra le versioni.

È possibile caricare manualmente i pacchetti scaricati da Defender per il portale per i singoli sensori. Se la console di gestione locale gestisce i sensori, è possibile scaricare i pacchetti di intelligence per le minacce nella console di gestione ed effettuarne il push a più sensori contemporaneamente.

Per aggiornare un pacchetto in un singolo sensore:

1. Passare alla pagina Azure Defender for Internet **Updates** .

2. Scaricare e salvare il pacchetto di intelligence per le **minacce** .

3. Accedere alla console del sensore.

4. Nel menu laterale selezionare impostazioni di **sistema**.

5. Selezionare **dati di intelligence** per le minacce, quindi selezionare **Aggiorna**.

6. Caricare il nuovo pacchetto.

Per aggiornare simultaneamente un pacchetto su più sensori:

1. Passare alla pagina Azure Defender for Internet **Updates** .

2. Scaricare e salvare il pacchetto di intelligence per le **minacce** .

3. Accedere alla console di gestione di.

4. Nel menu laterale selezionare impostazioni di **sistema**.

5. Nella sezione **configurazione del motore dei sensori** selezionare i sensori che devono ricevere i pacchetti aggiornati.  

6. Nella sezione **selezionare i dati di intelligence** per le minacce selezionare il segno più ( **+** ).

7. Caricare il pacchetto.

## <a name="see-also"></a>Vedi anche

[Versioni di aggiornamento](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
