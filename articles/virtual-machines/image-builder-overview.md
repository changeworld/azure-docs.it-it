---
title: Informazioni su Generatore immagini di Azure (anteprima)
description: Scopri di più su Azure Image Builder per macchine virtuali in Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604067"
---
# <a name="preview-azure-image-builder-overview"></a>Anteprima: Panoramica di Azure Image Builder

Le immagini di macchine virtuali (VM) standardizzate consentono alle organizzazioni di eseguire la migrazione al cloud e garantire la coerenza nelle distribuzioni. Le immagini includono in genere impostazioni predefinite di configurazione e sicurezza, oltre al software necessario. Per configurare la pipeline di imaging, è necessario disporre di tempo, infrastruttura e configurazione, ma con il generatore di immagini di VM di Azure è sufficiente fornire una configurazione che descriva l'immagine, la invii al servizio e l'immagine venga compilata e distribuita.
 
Il generatore di immagini di macchine virtuali di Azure (Generatore di immagini di Azure) consente di iniziare con un'immagine di Azure Marketplace basata su Windows o Linux, immagini personalizzate esistenti e iniziare ad aggiungere personalizzazioni personalizzate. Poiché il generatore di immagini si basa su [HashiCorp Packer](https://packer.io/) , si vedranno alcune analogie, ma si ha il vantaggio di un servizio gestito. È inoltre possibile specificare dove ospitare le immagini, ossia nella [raccolta immagini condivise](shared-image-galleries.md), come immagine gestita o come disco rigido virtuale.

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funzionalità di anteprima

Per l'anteprima, sono supportate queste funzionalità:

- Creazione di immagini di base, che include la sicurezza e le configurazioni aziendali minime, e consentire ai reparti di personalizzarla ulteriormente.
- Integrazione di applicazioni di base, in modo che la macchina virtuale possa assumere i carichi di lavoro dopo la creazione o aggiungere configurazioni per supportare le immagini del desktop virtuale di Windows.
- Applicazione di patch alle immagini esistenti: Image Builder consentirà di applicare continuamente patch alle immagini personalizzate esistenti.
- Connessione di Image Builder alle reti virtuali esistenti, per connettersi a server di configurazione (DSC, Chef, Puppet e così via), condivisioni file o altri server/servizi instradabili esistenti.
- Integrazione con la raccolta immagini condivise di Azure per distribuire, controllare le versioni e dimensionare le immagini a livello globale, con un sistema di gestione delle immagini.
- Integrazione con le pipeline di compilazione di immagini esistenti: è sufficiente chiamare Image Builder dalla pipeline oppure usare la semplice attività Azure DevOps di Image Builder disponibile in anteprima.
- Migrazione di una pipeline di personalizzazione di immagini esistente ad Azure. Uso di script, comandi e processi esistenti per personalizzare le immagini.
- Creazione di immagini in formato VHD per supportare Azure Stack.
 

## <a name="regions"></a>Regioni
Il servizio Azure Image Builder sarà disponibile per l'anteprima in queste aree. Le immagini possono essere distribuite al di fuori di queste aree.
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale

## <a name="os-support"></a>Sistemi operativi supportati
Image Builder supporta immagini dei sistemi operativi di base di Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-session/Professional
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>Funzionamento

Il generatore di immagini di macchine virtuali di Azure è un servizio di Azure completamente gestito accessibile da un provider di risorse di Azure. Fornire una configurazione al servizio che specifichi l'immagine di origine, la personalizzazione da eseguire e la posizione in cui la nuova immagine deve essere distribuita, il diagramma seguente mostra un flusso di lavoro di alto livello:

![Disegno concettuale del processo di generatore di immagini di Azure che mostra le origini (Windows/Linux), le personalizzazioni (Shell, PowerShell, riavvio di Windows & aggiornamento, l'aggiunta di file) e la distribuzione globale con la raccolta di immagini condivise di Azure](./media/image-builder-overview/image-builder-flow.png)

Le configurazioni dei modelli possono essere passate usando PowerShell, AZ CLI, ARM Templates e usando l'attività DevOps di Azure VM Image Builder, quando lo si invia al servizio, verrà creata una risorsa modello di immagine. Quando viene creata la risorsa modello di immagine, verrà visualizzato un gruppo di risorse di staging creato nella sottoscrizione, nel formato: it_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). Il gruppo di risorse di staging contiene file e script a cui si fa riferimento nel file, Shell, personalizzazione di PowerShell nella proprietà ScriptURI.

Per eseguire la compilazione che verrà richiamata `Run` sulla risorsa modello di immagine, il servizio distribuirà quindi risorse aggiuntive per la compilazione, ad esempio una macchina virtuale, una rete, un disco, una scheda di rete e così via. Se si compila un'immagine senza usare un generatore di immagini VNET esistente, distribuirà anche un IP pubblico e un NSG, il servizio si connette alla macchina virtuale di compilazione usando SSH o WinRM. Se si seleziona un VNET esistente, il servizio verrà distribuito usando il collegamento privato di Azure e non è necessario un indirizzo IP pubblico. per altri dettagli su Image Builder networking, vedere i [Dettagli](./linux/image-builder-networking.md).

Al termine della compilazione, tutte le risorse verranno eliminate, ad eccezione del gruppo di risorse di staging e dell'account di archiviazione, per rimuoverle verrà eliminata la risorsa modello di immagine oppure è possibile lasciarle per eseguire nuovamente la compilazione.

Questa documentazione include più esempi e guide dettagliate, che fanno riferimento a modelli e soluzioni di configurazione nel [repository GitHub di Azure Image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Sposta supporto
La risorsa modello di immagine non è modificabile e contiene collegamenti alle risorse e al gruppo di risorse di staging, pertanto il tipo di risorsa non supporta lo spostamento. Se si vuole spostare la risorsa modello di immagine, assicurarsi di avere una copia del modello di configurazione (estrarre la configurazione esistente dalla risorsa, se non è già presente), creare una nuova risorsa modello di immagine nel nuovo gruppo di risorse con un nuovo nome ed eliminare la risorsa modello immagine precedente. 

## <a name="permissions"></a>Autorizzazioni
Quando si esegue la registrazione in Azure Image Builder, si concede al servizio l'autorizzazione per creare, gestire ed eliminare un gruppo di risorse di staging (IT_*) e si acquisiscono i diritti per aggiungervi le risorse necessarie per la compilazione dell'immagine. Questo risultato si ottiene tramite un nome dell'entità servizio (SPN) di Azure Image Builder, reso disponibile nella sottoscrizione durante una registrazione completata.

Per consentire a Image Builder per macchine virtuali di Azure di distribuire immagini nelle immagini gestite o in una raccolta immagini condivise, sarà necessario creare un'identità assegnata dall'utente di Azure con le autorizzazioni di lettura e scrittura di immagini. Se si accede ad archiviazione di Azure, sarà necessario disporre delle autorizzazioni per leggere i contenitori privati e pubblici.

Le autorizzazioni sono illustrate in modo più dettagliato per [PowerShell](./linux/image-builder-permissions-powershell.md)e [AZ CLI](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Costi
Durante la creazione, la compilazione e l'archiviazione di immagini con Azure Image Builder si incorrerà in alcuni costi di calcolo, rete e archiviazione. Questi costi sono simili a quelli sostenuti per la creazione manuale di immagini personalizzate. Per le risorse, verranno addebitate le tariffe di Azure. 

Durante il processo di creazione dell'immagine, i file vengono scaricati e archiviati nel gruppo di risorse `IT_<DestinationResourceGroup>_<TemplateName>`, con un piccolo costo di archiviazione. Se non si vogliono mantenere questi file, eliminare il **modello di immagine** dopo la compilazione dell'immagine.
 
Image Builder crea una VM di dimensioni D1v2, oltre alle risorse di archiviazione e di rete necessarie. Queste risorse rimarranno disponibili per l'intero processo di compilazione e verranno eliminate quando Image Builder completa la creazione dell'immagine. 
 
Azure Image Builder distribuirà l'immagine nelle aree scelte, che potrebbero comportare addebiti in uscita dalla rete.

## <a name="hyper-v-generation"></a>Generazione di Hyper-V
Il generatore di immagini attualmente supporta solo in modo nativo la creazione di immagini di generazione Hyper-V (Gen1) 1 nella raccolta di immagini condivise (SIG) o nell'immagine gestita di Azure. 
 
## <a name="next-steps"></a>Passaggi successivi 
 
Per provare Azure Image Builder, vedere gli articoli per la compilazione di immagini [Linux](./linux/image-builder.md) o [Windows](./windows/image-builder.md).