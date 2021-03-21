---
title: Usare i criteri di accesso nella cache HPC di Azure
description: Come creare e applicare criteri di accesso personalizzati per limitare l'accesso client a destinazioni di archiviazione nella cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: eb9e71cc8ec463077e3b12b8738203a4945a2eab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471785"
---
# <a name="control-client-access"></a>Controllare l'accesso client

Questo articolo illustra come creare e applicare criteri di accesso client personalizzati per le destinazioni di archiviazione.

I criteri di accesso client controllano il modo in cui i client sono autorizzati a connettersi alle esportazioni di destinazione di archiviazione. È possibile controllare elementi come la squash radice e l'accesso in lettura/scrittura a livello di host client o di rete.

I criteri di accesso vengono applicati a un percorso dello spazio dei nomi, il che significa che è possibile utilizzare criteri di accesso diversi per due esportazioni diverse in un sistema di archiviazione NFS.

Questa funzionalità è destinata ai flussi di lavoro in cui è necessario controllare il modo in cui i diversi gruppi di client accedono alle destinazioni di archiviazione.

Se non è necessario un controllo con granularità fine sull'accesso alla destinazione di archiviazione, è possibile usare i criteri predefiniti oppure personalizzare i criteri predefiniti con regole aggiuntive. Se ad esempio si vuole abilitare la squash radice per tutti i client che si connettono tramite la cache, è possibile modificare il criterio denominato **default** per aggiungere l'impostazione di squash radice.

## <a name="create-a-client-access-policy"></a>Creare un criterio di accesso client

Utilizzare la pagina **criteri di accesso client** nella portale di Azure per creare e gestire i criteri. <!-- is there AZ CLI for this? -->

[![screenshot della pagina dei criteri di accesso client. Sono stati definiti diversi criteri e alcuni sono stati espansi per visualizzare le relative regole](media/policies-overview.png)](media/policies-overview.png#lightbox)

Ogni criterio è costituito da regole. Le regole vengono applicate agli host in ordine dall'ambito più piccolo (host) al più grande (impostazione predefinita). Viene applicata la prima regola che corrisponde a e le regole successive verranno ignorate.

Per creare nuovi criteri di accesso, fare clic sul pulsante **+ Aggiungi criteri di accesso** nella parte superiore dell'elenco. Assegnare un nome al nuovo criterio di accesso e immettere almeno una regola.

![screenshot del pannello di modifica dei criteri di accesso con più regole compilate. Fare clic su OK per salvare la regola.](media/add-policy.png)

Nella parte restante di questa sezione vengono illustrati i valori che è possibile utilizzare nelle regole.

### <a name="scope"></a>Ambito

Il termine ambito e il filtro degli indirizzi interagiscono per definire i client interessati dalla regola.

Utilizzarli per specificare se la regola si applica a un singolo client (host), a un intervallo di indirizzi IP (rete) o a tutti i client (impostazione predefinita).

Selezionare il valore di **ambito** appropriato per la regola:

* **Host** : la regola si applica a un singolo client
* **Rete** : la regola si applica ai client in un intervallo di indirizzi IP
* **Impostazione predefinita** : la regola si applica a tutti i client.

Le regole in un criterio vengono valutate in base a tale ordine. Quando una richiesta di montaggio client corrisponde a una regola, le altre vengono ignorate.

### <a name="address-filter"></a>Filtro indirizzi

Il valore del **filtro degli indirizzi** specifica quali client corrispondono alla regola.

Se si imposta l'ambito su **host**, è possibile specificare un solo indirizzo IP nel filtro. Per impostazione **predefinita** dell'ambito, non è possibile immettere indirizzi IP nel campo **filtro indirizzi** perché l'ambito predefinito corrisponde a tutti i client.

Specificare l'indirizzo IP o l'intervallo di indirizzi per questa regola. Usare la notazione CIDR, ad esempio: 0.1.0.0/16, per specificare un intervallo di indirizzi.

### <a name="access-level"></a>Livello di accesso

Impostare i privilegi per concedere ai client che corrispondono all'ambito e al filtro.

Le opzioni sono di **lettura/scrittura**, di sola **lettura** o **Nessun accesso**.

### <a name="suid"></a>SUID

Selezionare la casella **SUID** per consentire ai file nell'archivio di impostare gli ID utente al momento dell'accesso.

Il valore SUID viene in genere usato per aumentare temporaneamente i privilegi di un utente in modo che l'utente possa eseguire un'attività correlata a tale file.

### <a name="submount-access"></a>Accesso al sottomontaggio

Selezionare questa casella per consentire ai client specificati di montare direttamente le sottodirectory dell'esportazione.

### <a name="root-squash"></a>Squash radice

Scegliere se impostare la squash radice per i client che soddisfano questa regola.

Questa impostazione controlla il modo in cui la cache HPC di Azure considera le richieste provenienti dall'utente root nei computer client. Quando lo squash radice è abilitato, gli utenti root di un client vengono automaticamente mappati a un utente senza privilegi quando inviano richieste tramite la cache HPC di Azure. Impedisce inoltre alle richieste client di utilizzare i bit di autorizzazione set-UID.

Se la zucca radice è disabilitata, una richiesta proveniente dall'utente radice del client (UID 0) viene passata a un sistema di archiviazione NFS back-end come radice. Questa configurazione potrebbe consentire l'accesso ai file non appropriato.

L'impostazione di squash radice per le richieste client può compensare l' ``no_root_squash`` impostazione necessaria nei sistemi NAS usati come destinazioni di archiviazione. Per ulteriori informazioni sui [prerequisiti per l'archiviazione NFS](hpc-cache-prerequisites.md#nfs-storage-requirements), vedere. Può anche migliorare la sicurezza quando viene usata con le destinazioni di archiviazione BLOB di Azure.

Se si attiva lo squash radice, è necessario impostare anche il valore utente ID anonimo. Il portale accetta valori integer compresi tra 0 e 4294967295. I valori precedenti-2 e-1 sono supportati per la compatibilità con le versioni precedenti, ma non sono consigliati per le nuove configurazioni.

Questi valori vengono mappati a valori utente specifici:

* **-2** o **65534** (nessuno)
* **-1** o **65535** (nessun accesso)
* **0** (radice senza privilegi)

Il sistema di archiviazione potrebbe avere altri valori con significati speciali.

## <a name="update-access-policies"></a>Aggiornare i criteri di accesso

È possibile modificare o eliminare i criteri di accesso dalla tabella nella pagina **criteri di accesso client** .

Fare clic sul nome del criterio per aprirlo per la modifica.

Per eliminare un criterio, contrassegnare la casella di controllo accanto al relativo nome nell'elenco, quindi fare clic sul pulsante **Elimina** nella parte superiore dell'elenco. Non è possibile eliminare il criterio denominato "default".

> [!NOTE]
> Non è possibile eliminare un criterio di accesso in uso. Rimuovere i criteri da qualsiasi percorso dello spazio dei nomi che lo includa prima di tentare di eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

* Applicare i criteri di accesso nei percorsi dello spazio dei nomi per le destinazioni di archiviazione. Per informazioni su come, vedere [configurare lo spazio dei nomi aggregato](add-namespace-paths.md) .
