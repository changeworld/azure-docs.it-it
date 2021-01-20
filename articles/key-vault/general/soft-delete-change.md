---
title: Abilitare l'eliminazione temporanea in tutti gli oggetti dell'insieme di credenziali delle chiavi - Azure Key Vault | Microsoft Docs
description: Questo documento descrive come adottare l'eliminazione temporanea per tutti gli insiemi di credenziali delle chiavi e come apportare modifiche all'applicazione e all'amministrazione per evitare errori di conflitto.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572868"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>L'eliminazione temporanea verrà abilitata in tutti gli insiemi di credenziali delle chiavi

> [!WARNING]
> Modifica di rilievo: la possibilità di rifiutare esplicitamente l'eliminazione temporanea verrà presto deprecata. Utenti e amministratori di Azure Key Vault dovranno abilitare immediatamente l'eliminazione temporanea sugli insiemi di credenziali delle chiavi.
>
> Per il modulo di protezione hardware gestito di Azure Key Vault, l'eliminazione temporanea è abilitata per impostazione predefinita e non può essere disabilitata.

Senza protezione dell'eliminazione temporanea, un segreto eliminato da un insieme di credenziali delle chiavi viene rimosso definitivamente. Gli utenti possono attualmente rifiutare esplicitamente l'eliminazione temporanea durante la creazione dell'insieme di credenziali delle chiavi. Microsoft, tuttavia, abiliterà presto la protezione dell'eliminazione temporanea in tutti gli insiemi di credenziali delle chiavi per proteggere i segreti dall'eliminazione accidentale o dolosa da parte di un utente. Gli utenti non saranno più in grado di rifiutare esplicitamente o disattivare l'eliminazione temporanea.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagramma che illustra l'eliminazione di un insieme di credenziali delle chiavi con o senza protezione dell'eliminazione temporanea.":::

Per informazioni dettagliate sulla funzionalità di eliminazione temporanea, vedere [Panoramica dell'eliminazione temporanea di Azure Key Vault](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>L'applicazione può funzionare con l'eliminazione temporanea abilitata?

> [!Important] 
> Esaminare con attenzione le informazioni seguenti prima di attivare l'eliminazione temporanea per gli insiemi di credenziali delle chiavi.

I nomi degli insiemi di credenziali delle chiavi sono univoci a livello globale. Anche i nomi dei segreti archiviati in un insieme di credenziali delle chiavi sono univoci. Non sarà possibile riutilizzare il nome di un insieme di credenziali delle chiavi o di un oggetto al suo interno se è nello stato di eliminazione temporanea. 

Se ad esempio l'applicazione crea a livello di codice un insieme di credenziali delle chiavi denominato "Insieme di credenziali delle chiavi A" e in seguito lo elimina, l'insieme di credenziali delle chiavi passa nello stato di eliminazione temporanea. L'applicazione non potrà ricreare un altro insieme di credenziali delle chiavi denominato "Insieme di credenziali delle chiavi A" finché quello eliminato temporaneamente non verrà rimosso definitivamente. 

Inoltre, se l'applicazione crea una chiave denominata `test key` nell'"Insieme di credenziali delle chiavi A" e in seguito la elimina, non potrà creare una nuova chiave denominata `test key` nell'"Insieme di credenziali delle chiavi A" finché l'oggetto `test key` eliminato temporaneamente non verrà rimosso definitivamente. 

Il tentativo di eliminare un oggetto dell'insieme di credenziali delle chiavi e di ricrearlo con lo stesso nome senza prima rimuoverlo definitivamente dallo stato di eliminazione temporanea può generare errori di conflitto. Questi errori possono creare problemi nell'applicazione o nell'automazione. Prima di apportare le seguenti modifiche necessarie nell'applicazione e nell'amministrazione, consultare il team di sviluppo. 

### <a name="application-changes"></a>Modifiche dell'applicazione

Se l'applicazione presuppone che l'eliminazione temporanea non sia abilitata e prevede che i nomi di segreto o di insiemi di credenziali delle chiavi eliminati siano disponibili per il riutilizzo immediato, sarà necessario apportare le modifiche seguenti alla logica dell'applicazione.

1. Eliminare l'insieme di credenziali delle chiavi o il segreto originale.
1. Rimuovere definitivamente l'insieme di credenziali delle chiavi o il segreto nello stato di eliminazione temporanea.
1. Attendere che venga completata la rimozione definitiva. La ricreazione immediata potrebbe causare un conflitto.
1. Ricreare l'insieme di credenziali delle chiavi con lo stesso nome.
1. Se l'operazione di creazione genera comunque un errore di conflitto di nomi, provare a ricreare nuovamente l'insieme di credenziali delle chiavi. L'aggiornamento dei record DNS di Azure potrebbe richiedere fino a 10 minuti nello scenario peggiore.

### <a name="administration-changes"></a>Modifiche dell'amministrazione

Alle entità di sicurezza che devono accedere per eliminare permanentemente i segreti è necessario concedere autorizzazioni aggiuntive dei criteri di accesso per rimuovere definitivamente questi segreti e l'insieme di credenziali delle chiavi.

Disabilitare eventuali criteri di Azure negli insiemi di credenziali delle chiavi che impongono la disattivazione dell'eliminazione temporanea. Può essere necessario inoltrare questo problema a un amministratore che controlla i criteri di Azure applicati all'ambiente. Se questi criteri non vengono disabilitati, potrebbe non essere possibile creare nuovi insiemi di credenziali delle chiavi nell'ambito in cui sono applicati.

Se l'organizzazione è soggetta a requisiti di conformità legali e non può consentire la conservazione di insiemi di credenziali delle chiavi e segreti eliminati in uno stato recuperabile per un periodo prolungato di tempo, sarà necessario modificare il periodo di conservazione dell'eliminazione temporanea per soddisfare gli standard dell'organizzazione. È possibile configurare la durata del periodo di conservazione da 7 a 90 giorni.

## <a name="procedures"></a>Procedure

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Controllare gli insiemi di credenziali delle chiavi per verificare se l'eliminazione temporanea è abilitata

1. Accedere al portale di Azure.
1. Cercare **Criteri di Azure**.
1. Selezionare **Definizioni**.
1. Nel filtro di **Categoria** selezionare **Key Vault**.
1. Selezionare il criterio **Nell'insieme di credenziali delle chiavi deve essere abilitata la funzionalità di eliminazione temporanea**.
1. Selezionare **Assegna**.
1. Impostare l'ambito della sottoscrizione.
1. Assicurarsi che l'effetto del criterio sia impostato su **Controllo**.
1. Selezionare **Rivedi e crea**. Possono essere necessarie fino a 24 ore prima che venga completata un'analisi completa dell'ambiente.
1. Nel riquadro **Criteri di Azure** fare clic su **Conformità**.
1. Selezionare il criterio applicato.

A questo punto è possibile filtrare e verificare per quali insiemi di credenziali delle chiavi è abilitata l'eliminazione temporanea (risorse conformi) e per quali non è abilitata (risorse non conformi).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Attivare l'eliminazione temporanea per un insieme di credenziali delle chiavi esistente

1. Accedere al portale di Azure.
1. Cercare l'insieme di credenziali delle chiavi.
1. Selezionare **Proprietà** in **Impostazioni**.
1. In **Eliminazione temporanea** selezionare l'opzione **Abilita il ripristino di questo insieme di credenziali e dei rispettivi oggetti**.
1. Impostare il periodo di conservazione per l'eliminazione temporanea.
1. Selezionare **Salva**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Concedere le autorizzazioni di rimozione dei criteri di accesso a un'entità di sicurezza

1. Accedere al portale di Azure.
1. Cercare l'insieme di credenziali delle chiavi.
1. Selezionare **Criteri di accesso** in **Impostazioni**.
1. Selezionare l'entità servizio a cui concedere l'accesso.
1. Spostarsi tra i menu a discesa **Chiave**, **Segreto** e **Autorizzazioni del certificato** fino a visualizzare **Operazioni con privilegi**. Selezionare l'autorizzazione **Rimuovi definitivamente**.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-this-change-affect-me"></a>Quali sono gli effetti di questa modifica?

Se l'eliminazione temporanea è già stata attivata o se non si eliminano e si ricreano oggetti dell'insieme di credenziali delle chiavi con lo stesso nome, è probabile che non si noterà alcuna modifica nel comportamento dell'insieme di credenziali delle chiavi.

Se un'applicazione elimina e ricrea spesso oggetti dell'insieme di credenziali delle chiavi con le stesse convenzioni di denominazione, sarà necessario apportare modifiche alla logica dell'applicazione per mantenere il comportamento previsto. Vedere la sezione [Modifiche dell'applicazione](#application-changes) in questo articolo.

### <a name="how-do-i-benefit-from-this-change"></a>Quali sono i vantaggi di questa modifica?

La protezione dell'eliminazione temporanea fornisce all'organizzazione un ulteriore livello di protezione da eliminazioni accidentali o dolose. L'amministratore dell'insieme di credenziali delle chiavi può limitare l'accesso alle autorizzazioni di ripristino e a quelle di rimozione definitiva.

Se un utente elimina accidentalmente un insieme di credenziali delle chiavi o un segreto, è possibile concedergli autorizzazione di accesso per ripristinarlo autonomamente senza rischiare che lo elimini definitivamente. Questo processo self-service ridurrà al minimo il tempo di inattività nell'ambiente e garantirà la disponibilità dei segreti.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Come si può sapere se è necessario intervenire?

Seguire i passaggi descritti nella sezione [Controllare gli insiemi di credenziali delle chiavi per verificare se l'eliminazione temporanea è abilitata](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) in questo articolo. Questa modifica influirà su qualsiasi insieme di credenziali delle chiavi per cui l'eliminazione temporanea non è attivata.

### <a name="what-action-do-i-need-to-take"></a>Quali azioni è necessario intraprendere?

Dopo aver verificato che non è necessario apportare modifiche alla logica dell'applicazione, attivare l'eliminazione temporanea in tutti gli insiemi di credenziali delle chiavi.

### <a name="when-do-i-need-to-take-action"></a>Quando è necessario intervenire?

Per assicurarsi che le applicazioni non siano interessate, attivare l'eliminazione temporanea negli insiemi di credenziali delle chiavi il prima possibile.

## <a name="next-steps"></a>Passaggi successivi

- Per eventuali domande su questa modifica, inviare un messaggio all'indirizzo [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com).
- Leggere la [panoramica dell'eliminazione temporanea](soft-delete-overview.md).
