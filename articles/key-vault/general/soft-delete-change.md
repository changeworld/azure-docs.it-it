---
title: Abilitare l'eliminazione temporanea in tutte le istanze di Azure Key Vault | Microsoft Docs
description: Usare questo documento per adottare l'eliminazione temporanea per tutti gli insiemi di credenziali delle chiavi.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: e512cccdbfdc56500fa7c69372ca38f59d3195c2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590087"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>L'eliminazione temporanea verrà abilitata in tutti gli insiemi di credenziali delle chiavi

> [!WARNING]
> **Modifica di rilievo**: la possibilità di rifiutare esplicitamente l'eliminazione temporanea verrà presto deprecata. Utenti e amministratori di Azure Key Vault dovranno abilitare immediatamente l'eliminazione temporanea sugli insiemi di credenziali delle chiavi.
>
> Per il modulo di protezione hardware gestito, l'eliminazione temporanea è abilitata per impostazione predefinita e non può essere disabilitata.

Senza protezione dell'eliminazione temporanea, un segreto eliminato da un insieme di credenziali delle chiavi viene rimosso definitivamente. Gli utenti possono attualmente rifiutare esplicitamente l'eliminazione temporanea durante la creazione dell'insieme di credenziali delle chiavi, ma, per proteggere i segreti da eliminazioni accidentali o dannose da parte di un utente, Microsoft abiliterà la protezione dell'eliminazione temporanea in **tutti** insiemi di credenziali delle chiavi e gli utenti non avranno più la possibilità di rifiutarla o disattivarla.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<alt text>":::

Per informazioni dettagliate sulla funzionalità di eliminazione temporanea, vedere [Panoramica dell'eliminazione temporanea di Azure Key Vault](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>L'applicazione può funzionare con l'eliminazione temporanea abilitata?

> [!Important] 
> **Esaminare con attenzione le informazioni seguenti prima di attivare l'eliminazione temporanea per gli insiemi di credenziali delle chiavi**

I nomi degli insiemi di credenziali delle chiavi sono univoci a livello globale. Anche i nomi dei segreti archiviati in un insieme di credenziali delle chiavi sono univoci. Non sarò possibile riutilizzare il nome di un insieme di credenziali delle chiavi o di un oggetto al suo interno se è nello stato di eliminazione temporanea. 

**Esempio 1**  L'applicazione crea a livello di codice un insieme di credenziali delle chiavi denominato 'Insieme di credenziali delle chiavi A' e in seguito lo elimina. L'insieme di credenziali delle chiavi passerà nello stato di eliminazione temporanea. L'applicazione non potrà ricreare un altro insieme di credenziali delle chiavi denominato 'Insieme di credenziali delle chiavi A' finché quello eliminato temporaneamente non verrà rimosso definitivamente. 

**Esempio 2** L'applicazione crea un insieme di credenziali delle chiavi denominato `test key` nell'insieme di credenziali delle chiavi A e in seguito elimina la chiave da quest'ultimo. L'applicazione non potrà creare una nuova chiave denominata `test key` nell'insieme di credenziali delle chiavi A finché l'oggetto `test key` eliminato temporaneamente non verrà rimosso definitivamente. 

Di conseguenza, si possono verificare errori di conflitto se si prova a eliminare un oggetto dell'insieme di credenziali delle chiavi e a ricrearlo con lo stesso nome senza prima rimuoverlo definitivamente dallo stato di eliminazione temporanea. In questo caso si possono verificare errori per le applicazioni o l'automazione. Prima di apportare le modifiche richieste seguenti dell'applicazione e dell'amministrazione, consultare il team di sviluppo. 

### <a name="application-changes"></a>Modifiche dell'applicazione

Se l'applicazione presuppone che l'eliminazione temporanea non sia abilitata e prevede che i nomi del segreto o dell'insieme di credenziali delle chiavi eliminato siano disponibili per il riutilizzo immediato, sarà necessario apportare le modifiche seguenti alla logica dell'applicazione per adottare questa modifica.

1. Eliminare l'insieme di credenziali delle chiavi o il segreto originale
2. Rimuovere definitivamente l'insieme di credenziali delle chiavi o il segreto nello stato di eliminazione temporanea.
3. Attenzione: la ricreazione immediata può causare un conflitto.
4. Ricreare l'insieme di credenziali delle chiavi con lo stesso nome.
5. Implementare la ripetizione di tentativi se l'operazione di creazione continua a generare un errore di conflitto di nomi. Nello scenario peggiore, l'aggiornamento dei record DNS può richiedere fino a 10 minuti.

### <a name="administration-changes"></a>Modifiche dell'amministrazione

Alle entità di sicurezza che devono accedere per eliminare definitivamente i segreti è necessario concedere autorizzazioni aggiuntive dei criteri di accesso per rimuovere definitivamente questi segreti e l'insieme di credenziali delle chiavi.

Se gli insieme di credenziali delle chiavi prevedono un criterio di Azure che impone la disattivazione dell'eliminazione temporanea, è necessario disabilitare tale criterio.  Può essere necessario inoltrare questo problema a un amministratore che controlla i criteri di Azure applicati all'ambiente. Se questo criterio non viene disabilitato, è possibile che si perda la possibilità di creare nuovi insiemi di credenziali delle chiavi nell'ambito di applicazione del criterio.

Se l'organizzazione è soggetta a requisiti di conformità legali e non può consentire la conservazione in uno stato recuperabile di insiemi di credenziali delle chiavi e segreti eliminati per un periodo prolungato di tempo, sarà necessario modificare il periodo di conservazione dell'eliminazione temporanea, configurabile tra 7 e 90 giorni, per soddisfare gli standard dell'organizzazione.

## <a name="procedures"></a>Procedure

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Controllare gli insiemi di credenziali delle chiavi per verificare se l'eliminazione temporanea è abilitata

1. Accedere al portale di Azure.
2. Cercare "Criteri di Azure".
3. Selezionare "Definizioni".
4. In Categoria selezionare "Key Vault" nel filtro.
5. Selezionare il criterio "Nell'insieme di credenziali delle chiavi deve essere abilitata la funzionalità di eliminazione temporanea".
6. Fare clic su "Assegna".
7. Impostare l'ambito della sottoscrizione.
8. Assicurarsi che l'effetto del criterio sia impostato su "Controlla".
9. Selezionare "Rivedi e crea".
10. Possono essere necessarie fino a 24 ore prima che venga completata un'analisi completa dell'ambiente.
11. Nel pannello Criteri di Azure fare clic su "Conformità".
12. Selezionare il criterio applicato.

A questo punto dovrebbe essere possibile filtrare e verificare per quali insiemi di credenziali delle chiavi è abilitata l'eliminazione temporanea (risorse conformi) e per quali non è abilitata (risorse non conformi).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Attivare l'eliminazione temporanea per un insieme di credenziali delle chiavi esistente

1. Accedere al portale di Azure.
2. Cercare l'insieme di credenziali delle chiavi.
3. Selezionare "Proprietà" nelle impostazioni.
4. In Eliminazione temporanea selezionare il pulsante di opzione corrispondente a "Abilita il ripristino di questo insieme di credenziali e degli oggetti al suo interno".
5. Impostare il periodo di conservazione per l'eliminazione temporanea.
6. Selezionare "Salva".

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Concedere le autorizzazioni di rimozione dei criteri di accesso a un'entità di sicurezza

1. Accedere al portale di Azure.
2. Cercare l'insieme di credenziali delle chiavi.
3. Selezionare "Criteri di accesso" nelle impostazioni.
4. Selezionare l'entità servizio a cui si vuole concedere l'accesso.
5. Per ogni elenco a discesa nelle autorizzazioni per chiavi, segreti e certificati, scorrere in basso fino a "Operazioni con privilegi" e selezionare l'autorizzazione "Ripulisci".

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-this-change-affect-me"></a>Quali sono gli effetti di questa modifica?

Se l'eliminazione temporanea è già stata attivata o se non si eliminano e si ricreano oggetti dell'insieme di credenziali delle chiavi con lo stesso nome, è probabile che non si noterà alcuna modifica nel comportamento dell'insieme di credenziali delle chiavi.

Se un'applicazione elimina e ricrea spesso oggetti dell'insieme di credenziali delle chiavi con le stesse convenzioni di denominazione, sarà necessario apportare modifiche alla logica dell'applicazione per mantenere il comportamento previsto. Vedere la sezione "Come rispondere alle modifiche di rilievo" precedente.

### <a name="how-do-i-benefit-from-this-change"></a>Quali sono i vantaggi di questa modifica?

La protezione dell'eliminazione temporanea fornirà all'organizzazione un ulteriore livello di protezione da eliminazioni accidentali o dannose. L'amministratore dell'insieme di credenziali delle chiavi può limitare l'accesso alle autorizzazioni di ripristino e a quelle di rimozione definitiva.

Se un utente elimina accidentalmente un insieme di credenziali delle chiavi o un segreto, è possibile concedergli autorizzazione di accesso per ripristinarlo autonomamente senza rischiare che lo elimini definitivamente. Questo processo self-service ridurrà al minimo il tempo di inattività nell'ambiente e garantirà la disponibilità dei segreti.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Come si può sapere se è necessario intervenire?

Seguire i passaggi precedenti nella sezione "Procedura per controllare gli insiemi di credenziali delle chiavi e verificare se l'eliminazione temporanea è attivata". Questa modifica influirà su qualsiasi insieme di credenziali delle chiavi per cui l'eliminazione temporanea non è attivata. Saranno presto disponibili strumenti aggiuntivi per il facilitare questo controllo e in quell'occasione questo documento verrà aggiornato.

### <a name="what-action-do-i-need-to-take"></a>Quali azioni è necessario intraprendere?

Assicurarsi che non sia necessario apportare modifiche alla logica dell'applicazione. Una volta ottenuta questa conferma, attivare l'eliminazione temporanea in tutti gli insiemi di credenziali delle chiavi.

### <a name="by-when-do-i-need-to-take-action"></a>Entro quando è necessario intervenire?

Per assicurarsi che le applicazioni non siano interessate, attivare l'eliminazione temporanea negli insiemi di credenziali delle chiavi il prima possibile.

## <a name="next-steps"></a>Passaggi successivi

- Per eventuali domande su questa modifica, inviare un messaggio all'indirizzo [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com).
- Leggere la [panoramica dell'eliminazione temporanea](soft-delete-overview.md)
