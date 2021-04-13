---
title: Gestione automatica di Azure per le macchine virtuali
description: Informazioni sulle Gestione automatica di Azure per le macchine virtuali.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 514f1af2a1b120254840986fc5ceb803dfc24345
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363377"
---
# <a name="azure-automanage-for-virtual-machines"></a>Gestione automatica di Azure per le macchine virtuali

Questo articolo illustra le Gestione automatica di Azure per le macchine virtuali, che hanno i vantaggi seguenti:

- Onboard di macchine virtuali in modo intelligente per selezionare le procedure consigliate per i servizi di Azure
- Configura automaticamente ogni servizio in base alle procedure consigliate di Azure
- Monitora la deriva e corregge la deriva quando viene rilevata
- Offre un'esperienza semplice (punto, clic, impostazione, dimentica)


## <a name="overview"></a>Panoramica

Gestione automatica di Azure per le macchine virtuali è un servizio che elimina la necessità di individuare, sapere come eseguire l'onboard e come configurare determinati servizi in Azure che potrebbero trarre vantaggio dalla macchina virtuale. Questi servizi sono considerati servizi di procedure consigliate di Azure e consentono di migliorare l'affidabilità, la sicurezza e la gestione per le macchine virtuali. I servizi di esempio [includono Azure Gestione aggiornamenti](../automation/update-management/overview.md) e [Backup di Azure](../backup/backup-overview.md).

Dopo l'onboarding delle macchine virtuali Gestione automatica di Azure, ogni servizio di procedure consigliate viene configurato in base alle impostazioni consigliate. Le procedure consigliate sono diverse per ognuno dei servizi. Un esempio potrebbe essere Backup di Azure, in cui la procedura consigliata potrebbe essere eseguire il backup della macchina virtuale una volta al giorno e avere un periodo di conservazione di sei mesi.

Gestione automatica di Azure monitora automaticamente anche la deriva e corregge la deriva quando viene rilevata. Ciò significa che se la macchina virtuale viene onboarded in Gestione automatica di Azure, non solo verrà configurata in base alle procedure consigliate di Azure, ma verrà monitorata per assicurarsi che continui a essere conforme a queste procedure consigliate per l'intero ciclo di vita. Se la macchina virtuale si discosta o devia da queste procedure (ad esempio, se un servizio è offboarded), verrà corretto e il computer verrà riportato nello stato desiderato.

## <a name="prerequisites"></a>Prerequisiti

Esistono diversi prerequisiti da considerare prima di provare a abilitare Gestione automatica di Azure nelle macchine virtuali.

- Versioni [di Windows Server e](automanage-windows-server.md#supported-windows-server-versions) [distribuzioni Linux supportate](automanage-linux.md#supported-linux-distributions-and-versions)
- Le macchine virtuali devono essere in un'area supportata (vedere di seguito)
- L'utente deve avere le autorizzazioni corrette (vedere di seguito)
- La gestione automatica al momento non supporta le sottoscrizioni sandbox

### <a name="supported-regions"></a>Aree supportate
Gestione automatica supporta solo le macchine virtuali che si trovano nelle aree seguenti:
* Europa occidentale
* Europa settentrionale
* Stati Uniti centrali
* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti occidentali
* Stati Uniti occidentali 2
* Canada centrale
* Stati Uniti centro-occidentali
* Stati Uniti centro-meridionali
* Giappone orientale
* Regno Unito meridionale
* Australia orientale
* Australia sud-orientale
* Asia sud-orientale

### <a name="required-rbac-permissions"></a>Autorizzazioni di controllo degli accessi in base al ruolo necessarie
L'account richiederà ruoli controllo degli accessi in base al ruolo leggermente diversi a seconda che si abilitazione di Gestione automatica con un nuovo account di Gestione automatica.

Se si abilita Gestione automatica con un nuovo account di Gestione automatica:
* **Ruolo** proprietario nelle sottoscrizioni contenenti le macchine virtuali _**oppure**_
* **Ruoli** **Collaboratore e Amministratore Accesso** utenti nelle sottoscrizioni contenenti le macchine virtuali

Se si abilita Gestione automatica con un account di Gestione automatica esistente:
* **Ruolo Collaboratore** nel gruppo di risorse contenente le macchine virtuali

All'account Gestione automatica verranno  concesse le **autorizzazioni Collaboratore** e Collaboratore Ai criteri delle risorse per eseguire azioni nei computer con gestione automatica.

> [!NOTE]
> Se si vuole usare Gestione automatica in una macchina virtuale connessa a un'area di lavoro in una sottoscrizione diversa, è necessario disporre delle autorizzazioni descritte in precedenza in ogni sottoscrizione.

## <a name="participating-services"></a>Servizi partecipanti

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Onboard di servizi in modo intelligente.":::

Per l'elenco completo dei servizi di Azure partecipanti e del relativo ambiente supportato, vedere gli argomenti seguenti:
- [Gestione automatica per Linux](automanage-linux.md)
- [Gestione automatica per Windows Server](automanage-windows-server.md)

 L'utente verrà automaticamente aggiunto a questi servizi partecipanti. Sono essenziali per le procedure consigliate white paper, disponibili nell'Cloud Adoption Framework [.](/azure/cloud-adoption-framework/manage/azure-server-management)

Per tutti questi servizi, verrà automaticamente onboard, configurata automaticamente, monitorata la deriva e mediata se viene rilevata la deriva.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Abilitazione della gestione automatica per le macchine virtuali in portale di Azure

Nel portale di Azure è possibile abilitare la gestione automatica in una macchina virtuale esistente o quando si crea una nuova macchina virtuale. Per i passaggi concisi di questo processo, vedere l'avvio rapido Gestione automatica per [le macchine virtuali](quick-create-virtual-machines-portal.md).

Se è la prima volta che si abilita La gestione automatica per la macchina virtuale, è possibile cercare nel portale di Azure le procedure consigliate per la gestione automatica delle macchine **virtuali di Azure.** Fare **clic su Abilita** nella macchina virtuale esistente, selezionare le macchine virtuali di cui si vuole eseguire l'onboard, fare clic su Seleziona, fare clic su **Abilita** e al termine. 

L'unica volta che potrebbe essere necessario interagire con questa macchina virtuale per gestire questi servizi è nel caso in cui si sia tentato di correggere la macchina virtuale, ma non è stato possibile farlo. Se la macchina virtuale viene corretta, la macchina virtuale verrà nuovamente conforme senza avvisare l'utente. Per altre informazioni, vedere [Stato delle macchine virtuali.](#status-of-vms)

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Abilitazione della gestione automatica per le macchine virtuali tramite Criteri di Azure
È anche possibile abilitare la gestione automatica nelle macchine virtuali su larga scala usando il modello Criteri di Azure. Il criterio ha un effetto DeployIfNotExists, il che significa che tutte le macchine virtuali idonee che si trovano nell'ambito del criterio verranno automaticamente onboarded in Procedure consigliate per la gestione automatica delle macchine virtuali.

Un collegamento diretto ai criteri è [disponibile qui.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)

### <a name="how-to-apply-the-policy"></a>Come applicare i criteri
1. Fare clic **sul pulsante Assegna** quando si visualizza la definizione dei criteri
1. Selezionare l'ambito a cui si desidera applicare i criteri (può essere gruppo di gestione, sottoscrizione o gruppo di risorse)
1. In **Parametri** specificare i parametri per l'account di gestione automatica, il profilo di configurazione e l'effetto (l'effetto deve in genere essere DeployIfNotExists)
    1. Se non si ha un account di gestione automatica, sarà necessario [crearne uno.](./automanage-account.md)
1. In **Correzione selezionare la** casella di controllo "Fare clic su un'attività di correzione". Verrà eseguito l'onboarding in Automanage.
1. Fare **clic su Rivedi e** crea e verificare che tutte le impostazioni siano buone.
1. Fare clic su **Crea**.

## <a name="environment-configuration"></a>Configurazione dell'ambiente

Quando si abilita Gestione automatica per la macchina virtuale, è necessario un ambiente. Gli ambienti sono alla base di questo servizio. Definiscono i servizi in cui vengono onboarder i computer e, in una certa misura, la configurazione di tali servizi.

### <a name="default-environments"></a>Ambienti predefiniti

Sono attualmente disponibili due ambienti.

- **L'ambiente di sviluppo/test** è progettato per computer di sviluppo/test.
- **L'ambiente** di produzione è per la produzione.

Il motivo di questo differenziatore è che alcuni servizi sono consigliati in base al carico di lavoro in esecuzione. Ad esempio, in un computer di produzione l'utente verrà automaticamente Backup di Azure. Tuttavia, per un computer di sviluppo/test, un servizio di backup sarebbe un costo non necessario, poiché i computer di sviluppo/test hanno in genere un impatto aziendale inferiore.

### <a name="customizing-an-environment-using-preferences"></a>Personalizzazione di un ambiente usando le preferenze

Oltre ai servizi standard su cui si è stati emersi, è possibile configurare un determinato subset di preferenze. Queste preferenze sono consentite all'interno di una gamma di opzioni di configurazione. Ad esempio, nel caso di Backup di Azure sarà possibile definire la frequenza del backup e il giorno della settimana in cui si verifica.

> [!NOTE]
> Nell'ambiente di sviluppo/test non verrà affatto backup della macchina virtuale.

È possibile modificare le impostazioni di un ambiente predefinito tramite le preferenze. Per informazioni su come creare una [preferenza, vedere](virtual-machines-custom-preferences.md)qui .

> [!NOTE]
> Non è possibile modificare la configurazione di registrazione nella macchina virtuale quando è abilitata la gestione automatica. Sarà necessario disabilitare La gestione automatica per la macchina virtuale e quindi riattivarla con l'ambiente e le preferenze desiderati.

Per l'elenco completo dei servizi di Azure partecipanti e se supportano le preferenze, vedere qui:
- [Gestione automatica per Linux](automanage-windows-server.md)
- [Gestione automatica per Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Gestione automatica dell'account

L'account di gestione automatica è il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche. In genere, l'opzione Automanage Account non è necessaria per l'utente, ma in caso di scenario di delega in cui si vuole dividere la gestione automatica delle risorse (ad esempio tra due amministratori di sistema), l'opzione Automanage Account nel flusso di abilitazione consente di definire un'identità di Azure per ognuno di questi amministratori.

Per altre informazioni sull'account di gestione automatica e su come crearne uno, visitare il documento [Automanage Account](./automanage-account.md).

## <a name="status-of-vms"></a>Stato delle macchine virtuali

Nel portale di Azure passare alla pagina **Automanage - Azure virtual machine best practices (Procedure** consigliate per la gestione automatica delle macchine virtuali di Azure) in cui sono elencate tutte le macchine virtuali gestite automaticamente. Qui verrà visualizzato lo stato complessivo di ogni macchina virtuale.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Elenco di macchine virtuali configurate.":::

Per ogni macchina virtuale elencata vengono visualizzati i dettagli seguenti: Nome, Ambiente, Preferenza di configurazione, Stato, Sistema operativo, Account, Sottoscrizione e Gruppo di risorse.

La **colonna Stato** può visualizzare gli stati seguenti:
- *In corso:* la macchina virtuale è stata appena abilitata ed è in fase di configurazione
- *Configurato:* la macchina virtuale è configurata e non viene rilevata alcuna deriva
- *Operazione* non riuscita: la macchina virtuale è stata spostata e non è stato possibile correggere
- *In* sospeso: la macchina virtuale non è attualmente in esecuzione e Gestione automatica tenterà di eseguire l'onboardaggio o la correzione della macchina virtuale alla successiva esecuzione

Se lo stato è **Non** *riuscito,* è possibile risolvere i problemi relativi alla distribuzione tramite il gruppo di risorse in cui si trova la macchina virtuale. Passare a **Gruppi di risorse,** selezionare il gruppo di risorse, fare clic su **Distribuzioni** e visualizzare lo *stato* Non riuscito insieme ai dettagli dell'errore.


## <a name="disabling-automanage-for-vms"></a>Disabilitazione della gestione automatica per le macchine virtuali

È possibile decidere un giorno di disabilitare la gestione automatica in determinate macchine virtuali. Ad esempio, il computer esegue un carico di lavoro sicuro estremamente sensibile ed è necessario bloccarlo ulteriormente rispetto a quanto avrebbe fatto Naturalmente Azure, quindi è necessario configurare il computer al di fuori delle procedure consigliate di Azure.

A tale scopo, nella portale di Azure, passare alla pagina **Automanage – Azure virtual machine best practices** (Gestione automatica - Procedure consigliate per le macchine virtuali di Azure) in cui sono elencate tutte le macchine virtuali gestite automaticamente. Selezionare la casella di controllo accanto alla macchina virtuale che si vuole disabilitare da Gestione automatica, quindi fare clic sul pulsante **Disabilita** gestione automatica.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Disabilitazione di Gestione automatica in una macchina virtuale.":::

Leggere attentamente i messaggi nella finestra popup visualizzata prima di fare clic su **Disabilita**.

> [!NOTE]
> La disabilitazione della gestione automatica in una macchina virtuale comporta il comportamento seguente:
>
> - La configurazione della macchina virtuale e i servizi di cui è stato onboarded non cambiano.
> - Gli eventuali addebiti sostenuti da tali servizi rimangono fatturabili e continuano a essere addebitati.
> - Il monitoraggio della deriva della gestione automatica si arresta immediatamente.


Prima di tutto, la macchina virtuale non verrà disassociata da nessuno dei servizi in cui è stato onboarded e configurato. Pertanto, gli eventuali addebiti sostenuti da tali servizi continueranno a rimanere fatturabili. Se necessario, è necessario eseguire l'off-board. Qualsiasi comportamento di automanage verrà immediatamente interrotta. Ad esempio, non verrà più monitorata la macchina virtuale per la deriva.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso che Gestione automatica per le macchine virtuali offre un mezzo per eliminare la necessità di conoscere, eseguire l'onboard e configurare le procedure consigliate per i servizi di Azure. Inoltre, se un computer di cui è stato onboarded in Gestione automatica per le macchine virtuali si allontana dalla configurazione dell'ambiente, verrà automaticamente riemandato alla conformità.

Provare ad abilitare Gestione automatica per le macchine virtuali nel portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare La gestione automatica per le macchine virtuali nel portale di Azure](quick-create-virtual-machines-portal.md)