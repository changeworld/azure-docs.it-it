---
title: Guida alla configurazione dell'account Lab accelerato per Azure Lab Services
description: Questa guida consente agli amministratori di configurare rapidamente un account Lab da usare all'interno della scuola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669390"
---
# <a name="lab-account-setup-guide"></a>Guida all'installazione dell'account Lab
Se si è un amministratore, prima di configurare l'ambiente di Azure Lab Services, è prima di tutto necessario creare un *account Lab* nella sottoscrizione di Azure. Un account Lab è un contenitore per uno o più Lab e richiede solo pochi minuti per la configurazione.

Questa guida include tre sezioni:
-  Prerequisiti
-  Pianificare le impostazioni dell'account Lab
-  Configurare l'account Lab

## <a name="prerequisites"></a>Prerequisiti
Le sezioni seguenti descrivono le operazioni necessarie prima di poter configurare un account Lab.


### <a name="access-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure
Per creare un account Lab, è necessario accedere a una sottoscrizione di Azure già configurata per la propria scuola. La scuola potrebbe avere una o più sottoscrizioni. Si usa una sottoscrizione per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure, inclusi gli account Lab.  Le sottoscrizioni di Azure sono in genere gestite dal reparto IT.  Per ulteriori informazioni, vedere la sezione "sottoscrizione" della [guida Azure Lab Services-amministratore](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Stimare il numero di macchine virtuali e dimensioni di VM necessarie
È importante comprendere il numero di [macchine virtuali (VM) e le dimensioni delle VM](./administrator-guide.md#vm-sizing) richieste dal Lab School. 

Per informazioni aggiuntive sulla strutturazione dei Lab e delle immagini, vedere il post di Blog relativo al [passaggio da un Lab fisico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Per altre istruzioni su come strutturare i Lab, vedere la sezione "lab" della [guida Azure Lab Services-Administrator](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Informazioni sui limiti delle VM di sottoscrizione e sulla capacità delle macchine virtuali regionali
Dopo aver stimato il numero di macchine virtuali e le dimensioni delle VM per i Lab, è necessario:

- Verificare che il limite di capacità della sottoscrizione di Azure consenta il numero di VM e le dimensioni della macchina virtuale che si intende usare nei Lab.
- Creare l'account Lab all'interno di un'area con una capacità di macchina virtuale disponibile sufficiente.

Per altre informazioni, vedere [limiti delle sottoscrizioni VM e capacità a livello](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)di area.

### <a name="decide-how-many-lab-accounts-to-create"></a>Decidere il numero di account Lab da creare

Per iniziare rapidamente, creare un singolo account Lab all'interno del proprio gruppo di risorse.  Successivamente, è possibile creare altri account Lab e gruppi di risorse, in base alle esigenze. Ad esempio, si potrebbe avere un account Lab e un gruppo di risorse per reparto come un modo per separare chiaramente i costi. 

Per ulteriori informazioni sugli account Lab, i gruppi di risorse e i costi di separazione, vedere:
- Sezione "gruppo di risorse" della [guida Azure Lab Services-amministratore](./administrator-guide.md#resource-group)
- Sezione "account Lab" della [guida Azure Lab Services-amministratore](./administrator-guide.md#lab-account) 
- [Gestione dei costi per Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Pianificare le impostazioni dell'account Lab

Per pianificare le impostazioni dell'account Lab, prendere in considerazione le domande seguenti.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Chi deve essere proprietari e collaboratori dell'account Lab?

Gli amministratori IT della scuola accettano in genere i ruoli proprietario e collaboratore per un account Lab. Questi ruoli sono responsabili della gestione dei criteri che si applicano a tutti i Lab nell'account Lab. La persona che crea l'account Lab è automaticamente un proprietario. È possibile aggiungere altri proprietari e collaboratori dal tenant di Azure Active Directory (Azure AD) associato alla sottoscrizione. 

Per ulteriori informazioni sul proprietario dell'account Lab e sui ruoli collaboratore, vedere la sezione relativa alla gestione dell'identità nella [guida Azure Lab Services-amministratore](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Gli utenti del Lab visualizzano solo un singolo elenco delle macchine virtuali a cui hanno accesso tra Azure AD tenant in Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Chi sarà autorizzato a creare Lab?

È possibile scegliere di creare Lab per il team IT o i membri del corpo docente. Per creare i Lab, è quindi necessario assegnare tali utenti al ruolo di autore del Lab nell'account Lab. Questo ruolo viene normalmente assegnato dal tenant di Azure AD associato alla sottoscrizione della scuola. Chi crea un Lab viene assegnato automaticamente come proprietario del Lab.  

Per ulteriori informazioni sul ruolo di autore del Lab, vedere la sezione relativa alla gestione dell'identità nella [guida Azure Lab Services-amministratore](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Chi sarà autorizzato a gestire i Lab?

È anche possibile scegliere di fare in modo che i membri del personale IT e dei docenti own\manage Labs *senza* la possibilità di creare Lab.  In questo caso, gli utenti del tenant Azure AD della sottoscrizione vengono assegnati al proprietario o al collaboratore dei Lab esistenti.  

Per ulteriori informazioni sui ruoli proprietario Lab e collaboratore, vedere la sezione relativa alla gestione dell'identità nella [guida Azure Lab Services-amministratore](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Si desidera salvare le immagini e condividerle tra laboratori?

Raccolta immagini condivise è un servizio che è possibile usare per salvare e condividere le immagini. Per le classi che devono usare la stessa immagine, i creatori di Lab possono creare l'immagine e quindi esportarla in una raccolta di immagini condivise.  Una volta esportata un'immagine nella raccolta immagini condivise, è possibile usarla per creare nuovi Lab.

Potrebbe essere necessario creare le immagini nell'ambiente fisico e quindi importarle in una raccolta di immagini condivise. Per altre informazioni, vedere il post [di Blog importare un'immagine personalizzata in una raccolta di immagini condivise](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Se si decide di usare il servizio raccolta immagini condivise, è necessario creare o alleghi una raccolta di immagini condivise all'account Lab. È possibile rimandare questa decisione per ora, perché una raccolta di immagini condivise può essere collegata a un account Lab in qualsiasi momento.  

Per altre informazioni, vedere:
- Sezione "raccolta di immagini condivise" della [guida Azure Lab Services-amministratore](./administrator-guide.md#shared-image-gallery)
- Sezione "prezzi" della [guida Azure Lab Services-amministratore](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Quali immagini in Azure Marketplace useranno i laboratori?

Azure Marketplace offre centinaia di immagini che è possibile abilitare affinché gli autori del Lab possano usarle per creare i propri laboratori. Alcune immagini possono includere tutti gli elementi necessari per un Lab. In altri casi, è possibile utilizzare un'immagine come punto di partenza, quindi l'autore del Lab può personalizzarlo installando applicazioni o strumenti aggiuntivi.

Se non si conoscono le immagini necessarie, è possibile tornare in seguito per abilitarle. Il modo migliore per visualizzare le immagini disponibili è creare prima di tutto un account Lab. In questo modo è possibile accedere a per esaminare l'elenco di immagini disponibili e il relativo contenuto.  

Per altre informazioni, vedere [specificare le immagini di Azure Marketplace disponibili per gli autori di Lab](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Le macchine virtuali del lab devono accedere ad altre risorse di Azure o locali?

Quando si configura un account Lab, è anche possibile fare il peering dell'account Lab con una rete virtuale.  Tenere presente che la rete virtuale e l'account lab devono trovarsi nella stessa area.  Per decidere se è necessario eseguire il peering con una rete virtuale, considerare gli scenari seguenti:

- **Accesso a un server licenze**
  
   Quando si usano le immagini di Azure Marketplace, il costo della licenza del sistema operativo viene aggregato ai prezzi per Lab Services. Tuttavia, non è necessario fornire licenze per il sistema operativo stesso. Per ulteriori software e applicazioni installate, è necessario fornire una licenza, a seconda dei casi.  Per accedere a un server licenze:
   - È possibile scegliere di connettersi a un server licenze locale.  Per la connessione a un server licenze locale è necessaria un'installazione aggiuntiva.
   - Un'altra opzione, che è più veloce da configurare, consiste nel creare un server licenze che si ospita in una macchina virtuale di Azure.  La VM di Azure si trova all'interno di una rete virtuale di cui si esegue il peering con l'account Lab.

- **Accesso ad altre risorse locali, ad esempio una condivisione file o un database**

   Normalmente si crea una rete virtuale per fornire l'accesso alle risorse locali usando un gateway di rete virtuale da sito a sito. La configurazione di questo tipo di ambiente richiederà ulteriore tempo.

- **Accesso ad altre risorse di Azure situate all'esterno di una rete virtuale**

   Se è necessario accedere alle risorse di Azure *non* protette all'interno di una rete virtuale, è possibile accedervi tramite Internet pubblico senza dover eseguire alcun peering.

   Per ulteriori informazioni sulle reti virtuali, vedere:
   - La sezione "rete virtuale" di [nozioni fondamentali sull'architettura in Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Connettere la rete lab con una rete virtuale peer in Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Creare un Lab con una risorsa condivisa in Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Configurare l'account Lab

Al termine della pianificazione, si è pronti per configurare l'account Lab. È possibile applicare gli stessi passaggi per configurare [Azure Lab Services in teams](./lab-services-within-teams-overview.md).

1. **Creare l'account Lab**. Per istruzioni, vedere [creare un account Lab](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Per informazioni sulle convenzioni di denominazione, vedere la sezione "denominazione" della [guida Azure Lab Services-amministratore](./administrator-guide.md#naming).

1. **Aggiungere utenti al ruolo Lab Creator**. Per istruzioni, vedere [aggiungere utenti al ruolo di autore del Lab](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Connettersi a una rete virtuale peer**. Per istruzioni, vedere [connettere la rete lab con una rete virtuale peer](./how-to-connect-peer-virtual-network.md).

   Potrebbe anche essere necessario fare riferimento alle istruzioni per [la configurazione dell'intervallo di indirizzi delle VM Lab](./how-to-configure-lab-accounts.md).

1. **Abilitare ed esaminare le immagini**. Per istruzioni, vedere [specificare quali immagini di Azure Marketplace sono disponibili per gli autori di Lab](./specify-marketplace-images.md).

   Per esaminare il contenuto di ogni immagine di Azure Marketplace, selezionare il nome dell'immagine. Ad esempio, lo screenshot seguente mostra i dettagli dell'immagine di Ubuntu Data Science VM:

   ![Screenshot di un elenco di immagini disponibili per la revisione in Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Se una raccolta di immagini condivise è associata all'account Lab e si vuole abilitare la condivisione delle immagini personalizzate da parte degli autori del Lab, completare passaggi simili, come illustrato nello screenshot seguente:

   ![Screenshot di un elenco di immagini personalizzate abilitate in una raccolta di immagini condivise.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione e la gestione dei Lab, vedere:

- [Gestire gli account Lab](how-to-manage-lab-accounts.md)  
- [Guida all'installazione di Lab](setup-guide.md)
