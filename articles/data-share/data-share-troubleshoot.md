---
title: Risolvere i problemi relativi a Condivisione dati di Azure
description: Informazioni su come risolvere i problemi relativi agli inviti e agli errori quando si creano o si ricevono condivisioni dati nella condivisione dati di Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97964508"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Risolvere i problemi comuni nella condivisione di dati di Azure 

Questo articolo illustra come risolvere i problemi comuni nella condivisione di dati di Azure. 

## <a name="azure-data-share-invitations"></a>Inviti a Condivisione dati di Azure 

In alcuni casi, quando i nuovi utenti selezionano **accetta invito** in un messaggio di posta elettronica, potrebbero visualizzare un elenco vuoto di inviti. 

:::image type="content" source="media/no-invites.png" alt-text="Screenshot che mostra un elenco vuoto di inviti.":::

Questo problema potrebbe avere una delle cause seguenti:

* **Il servizio di condivisione dati di Azure non è registrato come provider di risorse di una sottoscrizione di Azure nel tenant di Azure.** Questo problema si verifica quando il tenant di Azure non dispone di una risorsa di condivisione dati. 

    Quando si crea una risorsa di Condivisione dati di Azure, il provider di risorse viene registrato automaticamente nella sottoscrizione di Azure. È possibile registrare manualmente il servizio di condivisione dati attenendosi alla procedura seguente. Per completare questi passaggi, è necessario il [ruolo Collaboratore](../role-based-access-control/built-in-roles.md#contributor) per la sottoscrizione di Azure. 

    1. Passare a **Sottoscrizioni** nel portale di Azure.
    1. Selezionare la sottoscrizione che si vuole usare per creare la risorsa di condivisione dati di Azure.
    1. Selezionare **provider di risorse**.
    1. Cercare **Microsoft. DataShare**.
    1. Selezionare **Registra**.

* **L'invito viene inviato all'alias di posta elettronica anziché all'indirizzo di posta elettronica di accesso di Azure.** Se il servizio Condivisione dati di Azure è già stato registrato o è stata creata una risorsa di condivisione dati nel tenant di Azure, ma non è ancora possibile visualizzare l'invito, l'alias di posta elettronica potrebbe essere elencato come destinatario. Contattare il provider di dati e assicurarsi che l'invito venga inviato all'indirizzo di posta elettronica di accesso di Azure e non all'alias di posta elettronica.

* **L'invito è già accettato.** Il collegamento nel messaggio di posta elettronica consente di portarsi alla pagina **inviti di condivisione dati** nella portale di Azure. In questa pagina sono elencati solo gli inviti in sospeso. Gli inviti accettati non vengono visualizzati nella pagina. Per visualizzare le condivisioni ricevute e configurare l'impostazione del cluster di Azure Esplora dati di destinazione, passare alla risorsa di condivisione dati usata per accettare l'invito.

## <a name="creating-and-receiving-shares"></a>Creazione e ricezione di condivisioni

Quando si crea una nuova condivisione, si aggiungono set di impostazioni o si mappano i set di elementi, è possibile che vengano visualizzati gli errori seguenti:

* Non è stato possibile aggiungere i set di impostazioni.
* Non è stato possibile eseguire il mapping di DataSet.
* Impossibile concedere l'accesso alla risorsa di condivisione dati x a y.
* Non si dispone delle autorizzazioni appropriate per x.
* Non è stato possibile aggiungere le autorizzazioni di scrittura per l'account di condivisione dati di Azure a una o più delle risorse selezionate.

Se non si dispone di autorizzazioni sufficienti per l'archivio dati di Azure, potrebbe essere visualizzato uno di questi errori. Per altre informazioni, vedere [Ruoli e requisiti](concepts-roles-permissions.md). 

Per condividere o ricevere dati da un archivio dati di Azure, è necessaria l'autorizzazione di scrittura. Questa autorizzazione è in genere parte del ruolo Collaboratore. 

Se si condividono dati o si ricevono dati dall'archivio dati di Azure per la prima volta, è necessaria anche l'autorizzazione *Microsoft. Authorization/Role assegnations/Write* . Questa autorizzazione è in genere parte del ruolo proprietario. Anche se è stata creata la risorsa Archivio dati di Azure, non si è necessariamente il proprietario della risorsa. 

Quando si hanno le autorizzazioni appropriate, il servizio Condivisione dati di Azure consente automaticamente all'identità gestita della risorsa di condivisione dati di accedere all'archivio dati. Questo processo può richiedere alcuni minuti. Se si verifica un errore a causa di questo ritardo, riprovare tra qualche minuto.

La condivisione basata su SQL richiede autorizzazioni aggiuntive. Per informazioni sui prerequisiti, vedere [condividere da origini SQL](how-to-share-from-sql.md).

## <a name="snapshots"></a>Snapshot
Uno snapshot può non riuscire per vari motivi. Aprire un messaggio di errore dettagliato selezionando l'ora di inizio dello snapshot e quindi lo stato di ogni set di dati. 

Gli snapshot in genere hanno esito negativo per questi motivi:

* La condivisione dati non dispone delle autorizzazioni necessarie per leggere dall'archivio dati di origine o per scrivere nell'archivio dati di destinazione. Per altre informazioni, vedere [Ruoli e requisiti](concepts-roles-permissions.md). Se si sta eseguendo uno snapshot per la prima volta, la risorsa di condivisione dati potrebbe richiedere alcuni minuti per ottenere l'accesso all'archivio dati di Azure. Dopo alcuni minuti, riprovare.
* La connessione di condivisione dati all'archivio dati di origine o all'archivio dati di destinazione è bloccata da un firewall.
* Un set di dati condiviso, un archivio dati di origine o un archivio dati di destinazione è stato eliminato.

Per gli account di archiviazione, uno snapshot può non riuscire perché è in corso l'aggiornamento di un file nell'origine mentre è in corso lo snapshot. Di conseguenza, è possibile che venga visualizzato un file di 0 byte nella destinazione. Dopo l'aggiornamento nell'origine, gli snapshot dovranno avere esito positivo.

Per le origini SQL, uno snapshot può non riuscire per questi altri motivi:

* Lo script SQL di origine o lo script SQL di destinazione che concede l'autorizzazione di condivisione dati non è stato eseguito. In alternativa, per il database SQL di Azure o Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse), lo script viene eseguito con l'autenticazione SQL invece che con Azure Active Directory autenticazione.  
* L'archivio dati di origine o l'archivio dati SQL di destinazione è sospeso.
* Il processo snapshot o l'archivio dati di destinazione non supporta i tipi di dati SQL. Per altre informazioni, vedere [condividere da origini SQL](how-to-share-from-sql.md#supported-data-types).
* L'archivio dati di origine o l'archivio dati SQL di destinazione è bloccato da altri processi. La condivisione di dati di Azure non blocca questi archivi dati. Tuttavia, i blocchi esistenti in questi archivi dati possono generare un errore di snapshot.
* Un vincolo FOREIGN KEY fa riferimento alla tabella SQL di destinazione. Durante uno snapshot, se una tabella di destinazione ha lo stesso nome di una tabella nei dati di origine, la condivisione di dati di Azure rilascia la tabella e crea una nuova tabella. Se un vincolo FOREIGN KEY fa riferimento alla tabella SQL di destinazione, la tabella non può essere eliminata.
* Viene generato un file CSV di destinazione, ma i dati non possono essere letti in Excel. Questo problema può essere visualizzato quando la tabella SQL di origine contiene dati che includono caratteri non inglesi. In Excel selezionare la scheda **recuperare i dati** e scegliere il file CSV. Selezionare il file Origin **65001: Unicode (UTF-8)**, quindi caricare i dati.

## <a name="updated-snapshot-schedules"></a>Pianificazioni snapshot aggiornate
Quando il provider di dati aggiorna la pianificazione dello snapshot per la condivisione inviata, il consumer di dati deve disabilitare la pianificazione dello snapshot precedente. Abilitare quindi la pianificazione dello snapshot aggiornata per la condivisione ricevuta. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, continuare con l'esercitazione [condividere i dati](share-your-data.md) . 

Per informazioni su come ricevere dati, continuare con l'esercitazione [accettare e ricevere dati](subscribe-to-data-share.md) .