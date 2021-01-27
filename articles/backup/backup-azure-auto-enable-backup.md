---
title: Abilitare automaticamente il backup durante la creazione di macchine virtuali con Criteri di Azure
description: Articolo che descrive come usare i criteri di Azure per abilitare automaticamente il backup per tutte le macchine virtuali create in un determinato ambito
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896868"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Abilitare automaticamente il backup durante la creazione di macchine virtuali con Criteri di Azure

Una delle responsabilità principali di un amministratore di backup o di conformità in un'organizzazione è garantire che tutti i computer cruciali per l'azienda vengano sottoposti a backup con il periodo di conservazione appropriato.

Attualmente, backup di Azure offre un'ampia gamma di criteri predefiniti, usando [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview), che consentono di verificare automaticamente che le macchine virtuali di Azure siano configurate per il backup. A seconda di come sono organizzati i team e le risorse di backup, è possibile usare uno dei criteri seguenti:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Criteri 1: configurare il backup nelle VM senza un determinato tag in un insieme di credenziali di servizi di ripristino esistente nello stesso percorso

Se l'organizzazione dispone di un team di backup centrale che gestisce i backup tra i team di applicazioni, è possibile usare questo criterio per configurare il backup in un insieme di credenziali di servizi di ripristino centrale esistente nella stessa sottoscrizione e nella stessa posizione delle macchine virtuali da governare. È possibile scegliere di **escludere** le VM che contengono un determinato tag, dall'ambito di questi criteri.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Criterio 2-[Anteprima] configurare il backup nelle VM con un determinato tag in un insieme di credenziali di servizi di ripristino esistente nello stesso percorso
Questo criterio funziona come il criterio 1 precedente, con la sola differenza che è possibile usare questo criterio per **includere** VM che contengono un determinato tag nell'ambito di questi criteri. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Criterio 3-[Anteprima] configurare il backup nelle VM senza un tag specificato in un nuovo insieme di credenziali di servizi di ripristino con criteri predefiniti
Se si organizzano applicazioni in gruppi di risorse dedicati e si vuole eseguirne il backup dallo stesso insieme di credenziali, questo criterio consente di gestire automaticamente l'azione. È possibile scegliere di **escludere** le VM che contengono un determinato tag, dall'ambito di questi criteri.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Criteri 4-[Anteprima] configurare il backup nelle VM con un determinato tag in un nuovo insieme di credenziali di servizi di ripristino con un criterio predefinito
Questo criterio funziona allo stesso modo del criterio 3, con la sola differenza che è possibile usare questo criterio per **includere** le macchine virtuali contenenti un determinato tag nell'ambito di questi criteri. 

Oltre a quanto sopra, backup di Azure fornisce anche un criterio di [solo controllo](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) : **backup di Azure deve essere abilitato per le macchine virtuali**. Questo criterio identifica le macchine virtuali in cui non è abilitato il backup, ma non configura automaticamente i backup per queste macchine virtuali. Questa operazione è utile quando si desidera valutare la conformità complessiva delle macchine virtuali, ma non si desidera eseguire immediatamente un'azione.

## <a name="supported-scenarios"></a>Scenari Supportati

* I criteri predefiniti sono attualmente supportati solo per le macchine virtuali di Azure. Gli utenti devono prestare attenzione per assicurarsi che i criteri di conservazione specificati durante l'assegnazione siano criteri di conservazione delle macchine virtuali. Fare riferimento a [questo](./backup-azure-policy-supported-skus.md) documento per visualizzare tutti gli SKU di VM supportati da questo criterio.

* I criteri 1 e 2 possono essere assegnati a una singola posizione e a una sottoscrizione alla volta. Per abilitare il backup per le macchine virtuali tra percorsi e sottoscrizioni, è necessario creare più istanze dell'assegnazione dei criteri, una per ogni combinazione di percorso e sottoscrizione.

* Per i criteri 1 e 2, l'ambito del gruppo di gestione non è al momento supportato.

* Per i criteri 1 e 2, l'insieme di credenziali specificato e le macchine virtuali configurate per il backup possono trovarsi in gruppi di risorse diversi.

* I criteri 1, 2, 3 e 4 non sono attualmente disponibili nei cloud nazionali.

* I criteri 3 e 4 possono essere assegnati a una singola sottoscrizione alla volta (o a un gruppo di risorse all'interno di una sottoscrizione).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Uso dei criteri predefiniti

I passaggi seguenti descrivono il processo end-to-end di assegnazione dei criteri 1: **configurare il backup nelle macchine virtuali senza un tag specificato in un insieme di credenziali di servizi di ripristino esistente nello stesso percorso** a un ambito specifico. Per gli altri criteri si applicano istruzioni analoghe. Una volta assegnato, qualsiasi nuova macchina virtuale creata nell'ambito viene configurata automaticamente per il backup.

1. Accedere al portale di Azure e passare al dashboard dei **criteri** .
2. Selezionare **definizioni** nel menu a sinistra per ottenere un elenco di tutti i criteri predefiniti tra le risorse di Azure.
3. Filtrare l'elenco per **Category = backup** e selezionare il criterio denominato ' Configure backup on VMS of a location to an existing Central Vault nello stesso percorso '.
![Dashboard dei criteri](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Selezionare il nome del criterio. Si verrà reindirizzati alla definizione dettagliata per questo criterio.
![Riquadro definizione criteri](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Selezionare il pulsante **assegna** nella parte superiore del riquadro. Viene quindi reindirizzati al riquadro **assegna criterio** .
6. In **nozioni di base** selezionare i tre puntini di sospensione accanto al campo **ambito** . Verrà aperto un riquadro di contesto a destra in cui è possibile selezionare la sottoscrizione per il criterio da applicare. Facoltativamente, è anche possibile selezionare un gruppo di risorse, in modo che i criteri vengano applicati solo per le macchine virtuali in un determinato gruppo di risorse.
![Nozioni fondamentali sull'assegnazione di criteri](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Nella scheda **parametri** scegliere un percorso dall'elenco a discesa e selezionare l'insieme di credenziali e i criteri di backup a cui è necessario associare le VM nell'ambito. È anche possibile scegliere di specificare un nome di tag e una matrice di valori di tag. Una macchina virtuale che contiene i valori specificati per il tag specificato verrà esclusa dall'ambito dell'assegnazione dei criteri.
![Parametri di assegnazione dei criteri](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Verificare che l' **effetto** sia impostato su deployIfNotExists.
9. Passare a **Verifica + crea** e selezionare **Crea**.

> [!NOTE]
>
> Criteri di Azure può essere usato anche nelle macchine virtuali esistenti, usando la [correzione](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Si consiglia di non assegnare questo criterio a più di 200 di macchine virtuali alla volta. Se il criterio è assegnato a più di 200 macchine virtuali, il backup potrebbe essere attivato alcune ore dopo il valore specificato dalla pianificazione.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su criteri di Azure](../governance/policy/overview.md)
