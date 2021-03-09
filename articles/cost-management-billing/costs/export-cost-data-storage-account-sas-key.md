---
title: Esportare i dati sui costi con una chiave SAS dell'account di archiviazione di Azure
description: Questo articolo consente ai partner di creare una chiave SAS e di configurare le esportazioni di gestione dei costi.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509669"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Esportare i dati sui costi con una chiave SAS dell'account di archiviazione di Azure

Le informazioni seguenti si applicano solo ai partner Microsoft.

Spesso i partner non dispongono di sottoscrizioni di Azure nel tenant associato al rispettivo contratto per i partner Microsoft. I partner con un piano di contratto partner Microsoft che sono amministratori globali del proprio account di fatturazione possono esportare e copiare i dati relativi ai costi in un account di archiviazione in un tenant diverso usando una chiave di servizio di accesso condiviso (SAS). In altre parole, un account di archiviazione con una chiave di firma di accesso condiviso consente al partner di usare un account di archiviazione che esula dal contratto partner per ricevere le informazioni esportate. Questo articolo consente ai partner di creare una chiave SAS e di configurare le esportazioni di gestione dei costi.

## <a name="requirements"></a>Requisiti

- È necessario essere un partner con un contratto per i partner Microsoft e avere clienti nel piano Azure.
- Per l'account di fatturazione dell'organizzazione partner, è necessario essere un amministratore globale.
- È necessario avere accesso per configurare un account di archiviazione che si trova in un tenant diverso dell'organizzazione partner. L'utente è responsabile della gestione delle autorizzazioni e dell'accesso ai dati quando i dati vengono esportati nell'account di archiviazione.

## <a name="configure-azure-storage-with-a-sas-key"></a>Configurare archiviazione di Azure con una chiave SAS

Ottenere un token SAS dell'account di archiviazione o crearne uno usando il portale di Azure. Per creare nel portale di Azure, attenersi alla procedura seguente. Per altre informazioni sulle chiavi SAS, vedere [concedere l'accesso limitato ai dati con le firme di accesso condiviso (SAS).](../../storage/common/storage-sas-overview.md)

1. Passare all'account di archiviazione nel portale di Azure.
    - Se l'account ha accesso a più tenant, passare alla directory per accedere all'account di archiviazione. Selezionare il proprio account nell'angolo in alto a destra del portale di Azure e quindi selezionare **Cambia directory**.
    - Per accedere all'account di archiviazione, potrebbe essere necessario accedere al portale di Azure con l'account tenant corrispondente.
1. Nel menu a sinistra selezionare **firma di accesso condiviso**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Screenshot che mostra una firma di accesso condiviso di archiviazione di Azure configurata." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Configurare il token con le stesse impostazioni indicate nell'immagine precedente.
    1. Selezionare **BLOB** per _Servizi consentiti_.
    1. Selezionare il **servizio**, il **contenitore** e l' **oggetto** per i _tipi di risorse consentiti_.
    1. Selezionare **lettura**, **scrittura**, **eliminazione**, **elenco**, **Aggiungi** e **Crea** per _autorizzazioni consentite_.
    1. Scegliere la scadenza e le date. Assicurarsi di aggiornare il token SAS di esportazione prima della scadenza. Più a lungo il periodo di tempo configurato prima della scadenza, più a lungo viene eseguita l'esportazione prima che sia necessario un nuovo token SAS.
1. Selezionare **solo HTTPS** per i _protocolli consentiti_.
1. Selezionare **Basic** per il _livello di routing preferito_.
1. Selezionare **Key1** per la _chiave di firma_. Se si ruota o si aggiorna la chiave usata per firmare il token SAS, sarà necessario rigenerare un nuovo token SAS per l'esportazione.
1. Selezionare **Genera firma di accesso condiviso e stringa di connessione**.
    Il valore del **token SAS** visualizzato è il token necessario quando si configurano le esportazioni.

## <a name="create-a-new-export-with-a-sas-token"></a>Creare una nuova esportazione con un token di firma di accesso condiviso

Passare a **esportazioni** nell'ambito dell'account di fatturazione e creare una nuova esportazione usando la procedura seguente.

1. Selezionare **Crea**.
1. Configurare i dettagli di esportazione come per un'esportazione normale. È possibile configurare l'esportazione per l'uso di una directory o di un contenitore esistente oppure è possibile specificare una nuova directory o un contenitore e le esportazioni li creeranno automaticamente.
1. Quando si configura l'archiviazione, selezionare **Usa un token di firma di** accesso condiviso.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Screenshot che mostra la nuova esportazione in cui si seleziona il token SAS." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Immettere il nome dell'account di archiviazione e incollare il token SAS.
1. Specificare un contenitore o una directory esistente o identificarne di nuovi da creare.
1. Selezionare **Crea**.

L'esportazione basata su token SAS funziona solo quando il token rimane valido. Reimpostare il token prima della scadenza di quello corrente oppure l'esportazione smetterà di funzionare. Poiché il token fornisce l'accesso all'account di archiviazione, proteggere il token con la stessa attenzione delle altre informazioni riservate. Si è responsabili della gestione delle autorizzazioni e dell'accesso ai dati quando i dati vengono esportati nell'account di archiviazione.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Risolvere i problemi relativi alle esportazioni usando i token SAS

Di seguito sono riportati alcuni problemi comuni che possono verificarsi quando si configurano o usano esportazioni basate su token SAS.

- L'opzione chiave SAS non è visibile nella portale di Azure.
  - Verificare di essere un partner che disponga di un contratto per i partner Microsoft e di disporre dell'autorizzazione di amministratore globale per l'account di fatturazione. Sono gli unici utenti che possono esportare con una chiave di firma di accesso condiviso.

- Quando si tenta di configurare l'esportazione, viene ricevuto il messaggio di errore seguente:

    **Verificare che il token di firma di accesso condiviso sia valido per il servizio BLOB, che sia valido per i tipi di risorse contenitore e oggetto e che disponga delle autorizzazioni: Aggiungi crea lettura scrittura Elimina. (Codice di errore del servizio di archiviazione: AuthorizationResourceTypeMismatch)**

    - Assicurarsi che la chiave SAS sia configurata e generata correttamente in archiviazione di Azure.

- Non è possibile visualizzare la chiave SAS completa dopo aver creato un'esportazione.
  - Il comportamento previsto della chiave non viene visualizzato. Una volta configurata l'esportazione SAS, la chiave viene nascosta per motivi di sicurezza.

- Non è possibile accedere all'account di archiviazione dal tenant in cui è configurata l'esportazione.
  - Si tratta di un comportamento previsto. Se l'account di archiviazione si trova in un altro tenant, è necessario passare prima al tenant nel portale di Azure per trovare l'account di archiviazione.

- L'esportazione non riesce a causa di un errore relativo al token SAS.
  - L'esportazione funziona solo quando il token SAS rimane valido. Creare una nuova chiave ed eseguire l'esportazione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'esportazione dei dati di gestione dei costi, vedere [creare ed esportare dati](tutorial-export-acm-data.md).
- Per informazioni sull'esportazione di grandi quantità di dati di utilizzo, vedere Recupero di set di dati di [grandi dimensioni con esportazioni](ingest-azure-usage-at-scale.md).
