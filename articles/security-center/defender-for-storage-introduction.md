---
title: Azure Defender per Archiviazione - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Archiviazione.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8979e315f188a5c21cce206c24f195f72096d438
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516517"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduzione ad Azure Defender per Archiviazione


**Azure Defender per Archiviazione** offre un livello di intelligence di sicurezza nativo di Azure che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Sfrutta le funzionalità avanzate di intelligenza artificiale e di [intelligence sulle minacce Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) per fornire avvisi e raccomandazioni contestuali sulla sicurezza.

Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi sono integrati con il Centro sicurezza di Azure e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sull'attività sospetta e le raccomandazioni su come analizzare e correggere le minacce.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|**Azure Defender per Archiviazione** è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Tipi di archiviazione protetti:|[Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/)<br>[File di Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quali tipi di avvisi vengono forniti da Azure Defender per Archiviazione?

Gli avvisi di sicurezza vengono attivati in caso di:

- **Modelli di accesso sospetti**, ad esempio l'accesso riuscito da un nodo di uscita Tor o da un indirizzo IP considerato sospetto dall'intelligence sulle minacce Microsoft
- **Attività sospette**, ad esempio l'estrazione anomala di dati o una modifica insolita delle autorizzazioni di accesso
- **Upload di contenuto dannoso**, ad esempio potenziali file di malware (in base all'analisi della reputazione hash) o hosting di contenuto di phishing

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati e raccomandazioni su come analizzare e risolvere le minacce. Gli avvisi possono essere esportati in Azure Sentinel o in qualsiasi altro sistema SIEM di terze parti o strumento esterno.

> [!TIP]
> Come procedura consigliata, [configurare Azure Defender per Archiviazione](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) a livello di sottoscrizione, sebbene sia possibile [configurarlo anche nei singoli account di archiviazione](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Cos'è l'analisi della reputazione hash per il malware?

Per determinare se un file caricato è sospetto, Azure Defender per Archiviazione usa l'analisi della reputazione hash supportata dalla funzionalità di [intelligence sulle minacce Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). Gli strumenti di protezione dalle minacce non analizzano i file caricati, bensì esaminano i log di archiviazione e confrontano gli hash dei nuovi file caricati con quelli di virus, trojan, spyware e ransomware noti. 

Se sospetta che un file contenga malware, il Centro sicurezza visualizza un avviso e può facoltativamente inviare un messaggio di posta elettronica al proprietario dell'archiviazione per chiedere l'approvazione per eliminare il file sospetto. Per configurare questa rimozione automatica dei file che l'analisi della reputazione hash indica come contenenti malware, distribuire un'[automazione del flusso di lavoro che venga attivata da avvisi che contengono "Potenziale malware caricato in un account di archiviazione"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario abilitare Azure Defender nella sottoscrizione contenente i carichi di lavoro applicabili.
>
> È possibile abilitare **Azure Defender per Archiviazione** a livello di sottoscrizione o di risorsa.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per Archiviazione.

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).
- [Come abilitare Azure Defender per Archiviazione](../storage/common/azure-defender-storage-configure.md)
- [Elenco degli avvisi di Azure Defender per Archiviazione](alerts-reference.md#alerts-azurestorage)
- [Funzionalità dell'intelligence sulle minacce di Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)
