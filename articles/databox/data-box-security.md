---
title: Panoramica sulla protezione dei dati di Microsoft Azure Data Box | Microsoft Docs
description: Descrive Azure Data Box funzionalità di sicurezza nel dispositivo, nel servizio e nei dati che si trovano su Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: alkohli
ms.openlocfilehash: 4d6c77b3e8920cabc397cdcbc235baefa031e5ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97655493"
---
# <a name="azure-data-box-security-and-data-protection"></a>Sicurezza e protezione dei dati di Azure Data Box

Data Box offre una soluzione sicura per la protezione dei dati, garantendo che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati. Questo articolo descrive le funzionalità di sicurezza di Azure Data Box che consentono di proteggere ciascuno dei componenti della soluzione Data Box e i dati archiviati su di essi.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Flusso dei dati nei vari componenti

La soluzione Microsoft Azure Data Box è costituita da quattro componenti principali che interagiscono tra loro:

- **Servizio Azure Data Box ospitato in Azure**: il servizio di gestione usato per creare l'ordine del dispositivo, configurare il dispositivo e quindi monitorare l'ordine fino al completamento.
- **Dispositivo di Data Box**: il dispositivo di trasferimento spedito all'utente per l'importazione dei dati locali Azure.
- **Client/host connessi al dispositivo**: i client nell'infrastruttura che si connettono al dispositivo Data Box tramite USB e che contengono i dati da proteggere.
- **Archiviazione cloud** Posizione nel cloud Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Azure Data Box creata.

Il diagramma seguente indica il flusso di dati tramite la soluzione Azure Data Box dall'ambiente locale ad Azure e le varie funzionalità di sicurezza sul posto mentre i dati passano attraverso la soluzione. Questo flusso è per un ordine di importazione per il Data Box.

![Sicurezza importazione Data Box](media/data-box-security/data-box-security-import.png)

Il diagramma seguente è per l'ordine di esportazione per la Data Box.

![Sicurezza esportazione Data Box](media/data-box-security/data-box-security-export.png)

Con il passaggio dei dati attraverso questa soluzione, vengono registrati eventi e vengono generati log. Per altre informazioni, vedere:

- [Rilevamento e registrazione degli eventi per gli ordini di importazione Azure Data Box](data-box-logs.md).
- [Rilevamento e registrazione degli eventi per gli ordini di esportazione Azure Data Box](data-box-export-logs.md)

## <a name="security-features"></a>Funzionalità di sicurezza

Data Box offre una soluzione sicura per la protezione dei dati, garantendo che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati. Le funzionalità di sicurezza per questa soluzione riguardano sia i dischi che il servizio associato a garantire la sicurezza dei dati archiviati.

### <a name="data-box-device-protection"></a>Protezione del dispositivo Data Box

Il dispositivo Data Box è protetto dalle funzionalità seguenti:

- Un involucro per dispositivi robusto che resiste a urti, trasporti difficili e condizioni ambientali. 
- Rilevamento di manomissione dell'hardware e del software che impedisce altre operazioni del dispositivo.
- Esecuzione solo del software specifico di Data Box.
- Avvio in stato bloccato.
- Controlla l'accesso del dispositivo tramite una passkey di sblocco del dispositivo. Questa passkey è protetta da una chiave di crittografia. Per proteggere la passkey, è possibile usare la propria chiave gestita dal cliente. Per altre informazioni, vedere [Usare chiavi gestite dal cliente in Azure Key Vault per Azure Data Box](data-box-customer-managed-encryption-key-portal.md).
- Credenziali di accesso per copiare dati da e nel dispositivo. Ogni accesso alla pagina delle **credenziali del dispositivo** nel portale di Azure viene registrato nei [log attività](data-box-logs.md#query-activity-logs-during-setup).
- È possibile usare le proprie password per l'accesso al dispositivo e alla condivisione. Per ulteriori informazioni, vedere [esercitazione: Order Azure Data Box](data-box-deploy-ordered.md).

### <a name="establish-trust-with-the-device-via-certificates"></a>Stabilire una relazione di trust con il dispositivo tramite certificati

Un dispositivo Data Box consente di importare i propri certificati e di installarli per l'uso per la connessione all'interfaccia utente Web locale e all'archiviazione BLOB. Per altre informazioni, vedere [usare i propri certificati con data box e dispositivi di data box Heavy](data-box-bring-your-own-certificates.md).

### <a name="data-box-data-protection"></a>Protezione dei dati di Data Box

I dati in ingresso e in uscita da Data Box sono protetti dalle funzionalità seguenti:

- Crittografia AES a 256 bit dei dati inattivi. In un ambiente a sicurezza elevata è possibile utilizzare la crittografia doppia basata su software. Per ulteriori informazioni, vedere [esercitazione: Order Azure Data Box](data-box-deploy-ordered.md).
- Per i dati in movimento è possibile usare i protocolli crittografati. Si consiglia di usare SMB 3,0 con la crittografia per proteggere i dati quando si esegue la copia da tali server.
- Cancellazione protetta dei dati dal dispositivo dopo il completamento del caricamento in Azure. La cancellazione dei dati è conforme alle linee guida indicate nell'[Appendice A per le unità disco rigido ATA negli standard NIST 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). L'evento di cancellazione dei dati viene registrato nella [cronologia ordini](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Protezione del servizio Data Box

Il servizio Data Box è protetto dalle funzionalità seguenti.

- Per accedere al servizio Data Box è necessario che l'organizzazione disponga di una sottoscrizione di Azure che includa Data Box. La sottoscrizione determina le funzionalità a cui si può accedere nel portale di Azure.
- Dato che il servizio Data Box è ospitato in Azure, viene protetto dalle funzionalità di sicurezza di Azure. Per altre informazioni sulle funzionalità di sicurezza fornite da Microsoft Azure, andare in [Centro protezione Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- È possibile controllare l'accesso all'ordine di Data Box tramite l'uso dei ruoli di Azure. Per altre informazioni, vedere [Impostare il controllo di accesso dell'ordine di Data Box](data-box-logs.md#set-up-access-control-on-the-order).
- Il servizio Data Box archivia la password di sblocco usata per sbloccare il dispositivo nel servizio.
- Il servizio Data Box archivia i dettagli e lo stato dell'ordine nel servizio. Queste informazioni vengono eliminate quando l'ordine viene eliminato.

## <a name="managing-personal-data"></a>Gestione dei dati personali

Azure Data Box raccoglie e visualizza informazioni personali nei principali casi seguenti nel servizio:

- **Impostazioni di notifica** - Quando si crea un ordine, si configura l'indirizzo di posta elettronica degli utenti nelle impostazioni di notifica. Queste informazioni possono essere visualizzate dell'amministratore. Queste informazioni vengono eliminate dal servizio quando il processo raggiunge lo stato terminale o quando si elimina l'ordine.

- **Dettagli dell'ordine** : una volta creato l'ordine, l'indirizzo di spedizione, la posta elettronica e le informazioni di contatto degli utenti vengono archiviati nella portale di Azure. Le informazioni salvate includono:

  - Nome contatto
  - Numero di telefono
  - E-mail
  - Indirizzo
  - City
  - CAP
  - State
  - Paese/provincia/area geografica
  - Numero di account del vettore
  - Numero di tracciabilità della spedizione

    Al termine del processo o quando si elimina l'ordine, i dettagli dell'ordine vengono eliminati dal servizio Data Box.

- **Indirizzo di spedizione**: dopo l'inserimento dell'ordine, il servizio Data Box fornisce l'indirizzo di spedizione ai vettori di terze parti, ad esempio UPS o DHL. 

Per altre informazioni, consultare l'Informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Riferimento delle linee guida sulla sicurezza

In Data Box sono implementate le seguenti linee guida sulla sicurezza:

|Linee guida   |Descrizione   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Per la protezione da acqua e polvere         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Per la resistenza a condizioni di trasporto avverse          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Per l'aggiornamento protetto del firmware         |
|[FIPS 140-2 livello 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Per la protezione dei dati         |
|Appendice A, per le unità disco rigido ATA in [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Per la bonifica dei dati         |

## <a name="next-steps"></a>Passaggi successivi

- Rivedere i [requisiti per Data Box](data-box-system-requirements.md).
- Comprendere i [limiti di Data Box](data-box-limits.md).
- Distribuire rapidamente [Azure Data Box](data-box-quickstart-portal.md) nel portale di Azure.
