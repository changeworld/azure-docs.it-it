---
title: Protezione dei dati in analisi di flusso di Azure
description: Questo articolo illustra come crittografare i dati privati usati da un processo di analisi di flusso di Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 102b03ad4fe247ae0abc4e2312d7027c6170333f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019466"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Protezione dei dati in analisi di flusso di Azure 

Analisi di flusso di Azure è una piattaforma distribuita come servizio completamente gestita che consente di compilare pipeline di analisi in tempo reale. Tutto il lavoro pesante, ad esempio il provisioning del cluster, il ridimensionamento dei nodi per adattarlo all'utilizzo e la gestione dei checkpoint interni, viene gestito in background.

## <a name="private-data-assets-that-are-stored"></a>Asset di dati privati archiviati

Analisi di flusso di Azure rende permanente i metadati e i dati seguenti per l'esecuzione: 

* Definizione della query e configurazione correlata  

* Funzioni o aggregazioni definite dall'utente  

* Checkpoint necessari per il runtime di analisi di flusso

* Snapshot dei dati di riferimento 

* Dettagli della connessione delle risorse usate dal processo di analisi di flusso

Per soddisfare gli obblighi di conformità in qualsiasi settore o ambiente regolamentato, è possibile leggere altre informazioni sulle [offerte di conformità di Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>In-Region residenza dei dati
Analisi di flusso di Azure archivia i dati dei clienti e altri metadati descritti in precedenza. Per impostazione predefinita, i dati dei clienti vengono archiviati da analisi di flusso di Azure in una singola area, in modo che questo servizio soddisfi automaticamente i requisiti di residenza dei dati dell'area, inclusi quelli specificati nel [Centro protezione](https://azuredatacentermap.azurewebsites.net/).
Inoltre, è possibile scegliere di archiviare tutti gli asset di dati (dati del cliente e altri metadati) correlati al processo di analisi di flusso in un'unica area crittografando i dati in un account di archiviazione di propria scelta.

## <a name="encrypt-your-data"></a>Crittografare i dati

Analisi di flusso usa automaticamente gli standard di crittografia migliori all'interno dell'infrastruttura per crittografare e proteggere i dati. È possibile considerare semplicemente attendibile analisi di flusso per archiviare in modo sicuro tutti i dati, in modo da non dover preoccuparsi di gestire l'infrastruttura.

Se si vuole usare chiavi gestite dal cliente per crittografare i dati, è possibile usare il proprio account di archiviazione (utilizzo generico V1 o V2) per archiviare gli asset di dati privati richiesti dal runtime di analisi di flusso. L'account di archiviazione può essere crittografato in base alle esigenze. Nessuno degli asset di dati privati viene archiviato in modo permanente dall'infrastruttura di analisi di flusso. 

Questa impostazione deve essere configurata al momento della creazione del processo di analisi di flusso e non può essere modificata nel corso del ciclo di vita del processo. La modifica o l'eliminazione dello spazio di archiviazione usato dall'analisi di flusso non è consigliata. Se si elimina l'account di archiviazione, verranno eliminati definitivamente tutti gli asset di dati privati, causando un errore nel processo. 

Non è possibile aggiornare o ruotare le chiavi nell'account di archiviazione usando il portale di analisi di flusso. È possibile aggiornare le chiavi usando le API REST.


### <a name="configure-storage-account-for-private-data"></a>Configurare l'account di archiviazione per i dati privati 

Crittografare l'account di archiviazione per proteggere tutti i dati e scegliere in modo esplicito il percorso dei dati privati. 

Per soddisfare gli obblighi di conformità in qualsiasi settore o ambiente regolamentato, è possibile leggere altre informazioni sulle [offerte di conformità di Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

Usare la procedura seguente per configurare l'account di archiviazione per gli asset di dati privati. Questa configurazione viene eseguita dal processo di analisi di flusso, non dall'account di archiviazione.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure. 

1. Selezionare **Analytics analisi**   > di **flusso processo**   dall'elenco risultati. 

1. Compilare la pagina del processo di analisi di flusso con i dettagli necessari, ad esempio il nome, l'area e la scala. 

1. Selezionare la casella di controllo per *proteggere tutti gli asset di dati privati necessari per questo processo nell'account di archiviazione*.

1. Selezionare un account di archiviazione dalla sottoscrizione. Si noti che questa impostazione non può essere modificata durante tutto il ciclo di vita del processo. Non è inoltre possibile aggiungere questa opzione dopo che il processo è stato creato.

1. Per eseguire l'autenticazione con una stringa di connessione, selezionare **stringa di connessione** nell'elenco a discesa modalità di autenticazione. La chiave dell'account di archiviazione viene popolata automaticamente dalla sottoscrizione.

   ![Impostazioni dell'account di archiviazione dati privato](./media/data-protection/storage-account-create.png)

1. Per eseguire l'autenticazione con identità gestita (anteprima), selezionare **identità gestita** dall'elenco a discesa modalità di autenticazione. Se si sceglie identità gestita, è necessario aggiungere il processo di analisi di flusso all'elenco di controllo di accesso dell'account di archiviazione con il ruolo di *collaboratore dei dati BLOB di archiviazione* . Se non si concede l'accesso al processo, il processo non sarà in grado di eseguire alcuna operazione. Per altre informazioni su come concedere l'accesso, vedere usare il controllo degli accessi in base al ruolo [di Azure per assegnare un'identità gestita a un'altra risorsa](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource).

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="Impostazioni dell'account di archiviazione dati privati con autenticazione identità gestita":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Asset di dati privati archiviati da analisi di flusso

Tutti i dati privati che devono essere salvati in modo permanente da analisi di flusso vengono archiviati nell'account di archiviazione. Esempi di asset di dati privati includono: 

* Query create e relative configurazioni  

* Funzioni definite dall'utente 

* Checkpoint necessari per il runtime di analisi di flusso

* Snapshot dei dati di riferimento 

Vengono archiviati anche i dettagli della connessione delle risorse, usati dal processo di analisi di flusso. Crittografare l'account di archiviazione per proteggere tutti i dati. 

Per soddisfare gli obblighi di conformità in qualsiasi settore o ambiente regolamentato, è possibile leggere altre informazioni sulle [offerte di conformità di Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="enables-data-residency"></a>Abilita la residenza dei dati 
Questa funzionalità può essere usata per applicare eventuali requisiti di residenza dei dati, fornendo un account di archiviazione di conseguenza.

## <a name="known-issues"></a>Problemi noti
Si verifica un problema noto in cui un processo che usa la chiave gestita dal cliente si verifica in caso di errori quando si usa l'identità gestita per l'autenticazione a qualsiasi input o output. 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md)
* [Informazioni sugli input per Analisi di flusso di Azure](stream-analytics-add-inputs.md)
* [Concetti di checkpoint e riproduzione nei processi di Analisi di flusso di Azure](stream-analytics-concepts-checkpoint-replay.md)
* [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md)
