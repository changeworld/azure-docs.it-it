---
title: Usare le chiavi usate dal cliente per crittografare i dati Cache HPC di Azure
description: Come usare Azure Key Vault con Cache HPC di Azure per controllare l'accesso alla chiave di crittografia anziché usare le chiavi di crittografia predefinite gestite da Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: ae4c52ec1390166eccb0e73d6f81a8553c445b2e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813291"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Usare chiavi di crittografia gestite dal cliente per Cache HPC di Azure

È possibile usare Azure Key Vault per controllare la proprietà delle chiavi usate per crittografare i dati Cache HPC di Azure. Questo articolo illustra come usare le chiavi gestite dal cliente per la crittografia dei dati della cache.

> [!NOTE]
> Tutti i dati archiviati in Azure, inclusi i dischi della cache, vengono crittografati in stato di inquieto usando le chiavi gestite da Microsoft per impostazione predefinita. È necessario seguire i passaggi descritti in questo articolo solo se si vogliono gestire le chiavi usate per crittografare i dati.

Cache HPC di Azure è protetto anche dalla crittografia [dell'host](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) di macchine virtuali nei dischi gestiti che contengono i dati memorizzati nella cache, anche se si aggiunge una chiave del cliente per i dischi della cache. L'aggiunta di una chiave gestita dal cliente per la doppia crittografia offre un ulteriore livello di sicurezza per i clienti con esigenze di sicurezza elevate. Per informazioni [dettagliate, vedere](../virtual-machines/disk-encryption.md) Crittografia lato server di Archiviazione dischi di Azure.

Esistono tre passaggi per abilitare la crittografia delle chiavi gestita dal cliente per Cache HPC di Azure:

1. Configurare un'Azure Key Vault per archiviare le chiavi.
1. Quando si crea la Cache HPC di Azure, scegliere la crittografia della chiave gestita dal cliente e specificare l'insieme di credenziali delle chiavi e la chiave da usare.
1. Dopo aver creato la cache, autorizzarla ad accedere all'insieme di credenziali delle chiavi.

La crittografia non è completamente impostata fino a quando non si autorizza dalla cache appena creata (passaggio 3). Questo perché è necessario passare l'identità della cache all'insieme di credenziali delle chiavi per renderla un utente autorizzato. Non è possibile eseguire questa operazione prima di creare la cache, perché l'identità non esiste fino a quando non viene creata la cache.

Dopo aver creato la cache, non è possibile passare da chiavi gestite dal cliente a chiavi gestite da Microsoft. Tuttavia, se la cache usa chiavi gestite dal cliente, è possibile [modificare](#update-key-settings) la chiave di crittografia, la versione della chiave e l'insieme di credenziali delle chiavi in base alle esigenze.

## <a name="understand-key-vault-and-key-requirements"></a>Comprendere i requisiti dell'insieme di credenziali delle chiavi e delle chiavi

L'insieme di credenziali delle chiavi e la chiave devono soddisfare questi requisiti per poter essere Cache HPC di Azure.

Proprietà dell'insieme di credenziali delle chiavi:

* **Sottoscrizione:** usare la stessa sottoscrizione usata per la cache.
* **Area:** l'insieme di credenziali delle chiavi deve essere nella stessa area del Cache HPC di Azure.
* **Piano tariffario:** il livello Standard è sufficiente per l'uso con Cache HPC di Azure.
* **Eliminazione soft:** Cache HPC di Azure abiliterà l'eliminazione se non è già configurata nell'insieme di credenziali delle chiavi.
* **Protezione dall'eliminazione:** la protezione dall'eliminazione deve essere abilitata.
* **Criteri di accesso:** le impostazioni predefinite sono sufficienti.
* **Connettività di** rete: Cache HPC di Azure essere in grado di accedere all'insieme di credenziali delle chiavi, indipendentemente dalle impostazioni dell'endpoint scelto.

Proprietà chiave:

* **Tipo di chiave** - RSA
* **Dimensioni della chiave RSA** - 2048
* **Abilitato** - Sì

Autorizzazioni di accesso dell'insieme di credenziali delle chiavi:

* L'utente che crea il Cache HPC di Azure deve avere autorizzazioni equivalenti al ruolo [Key Vault collaboratore](../role-based-access-control/built-in-roles.md#key-vault-contributor). Le stesse autorizzazioni sono necessarie per configurare e gestire i Azure Key Vault.

  Per [altre informazioni, vedere Proteggere l'accesso a](../key-vault/general/security-features.md) un insieme di credenziali delle chiavi.

## <a name="1-set-up-azure-key-vault"></a>1. Configurare Azure Key Vault

È possibile configurare un insieme di credenziali delle chiavi e una chiave prima di creare la cache o come parte della creazione della cache. Assicurarsi che queste risorse soddisfino i requisiti descritti in [precedenza.](#understand-key-vault-and-key-requirements)

Al momento della creazione della cache è necessario specificare un insieme di credenziali, una chiave e una versione della chiave da usare per la crittografia della cache.

Per informazioni [dettagliate, Azure Key Vault documentazione](../key-vault/general/overview.md) dettagliata.

> [!NOTE]
> Il Azure Key Vault deve usare la stessa sottoscrizione e deve essere nella stessa area del Cache HPC di Azure. Assicurarsi che l'area scelta [supporti entrambi i prodotti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Creare la cache con le chiavi gestite dal cliente abilitate

È necessario specificare l'origine della chiave di crittografia quando si crea il Cache HPC di Azure. Seguire le istruzioni in [Creare un Cache HPC di Azure](hpc-cache-create.md)e specificare l'insieme di credenziali delle chiavi e la chiave nella pagina Chiavi di crittografia **del** disco. È possibile creare un nuovo insieme di credenziali delle chiavi e una nuova chiave durante la creazione della cache.

> [!TIP]
> Se la **pagina Chiavi di crittografia** del disco non viene visualizzata, assicurarsi che la cache si trova in una delle aree [supportate](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

L'utente che crea la cache deve avere privilegi uguali al ruolo Key Vault [collaboratore o](../role-based-access-control/built-in-roles.md#key-vault-contributor) superiore.

1. Fare clic sul pulsante per abilitare le chiavi gestite privatamente. Dopo aver modificato questa impostazione, vengono visualizzate le impostazioni dell'insieme di credenziali delle chiavi.

1. Fare **clic su Seleziona un insieme di credenziali** delle chiavi per aprire la pagina di selezione delle chiavi. Scegliere o creare l'insieme di credenziali delle chiavi e la chiave per crittografare i dati nei dischi della cache.

   Se il Azure Key Vault non viene visualizzato nell'elenco, verificare i requisiti seguenti:

   * La cache si trova nella stessa sottoscrizione dell'insieme di credenziali delle chiavi?
   * La cache si trova nella stessa area dell'insieme di credenziali delle chiavi?
   * Esiste connettività di rete tra il portale di Azure e l'insieme di credenziali delle chiavi?

1. Dopo aver selezionato un insieme di credenziali, selezionare la singola chiave tra le opzioni disponibili o creare una nuova chiave. La chiave deve essere una chiave RSA a 2048 bit.

1. Specificare la versione per la chiave selezionata. Per altre informazioni sul controllo delle versioni, [Azure Key Vault documentazione di](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Continuare con le altre specifiche e creare la cache come descritto in [Creare un](hpc-cache-create.md)Cache HPC di Azure .

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorizzare Azure Key Vault crittografia dalla cache
<!-- header is linked from create article, update if changed -->

Dopo alcuni minuti, il nuovo Cache HPC di Azure visualizzato nella portale di Azure. Passare alla pagina **Panoramica** per autorizzarlo ad accedere al Azure Key Vault e abilitare la crittografia della chiave gestita dal cliente.

> [!TIP]
> La cache potrebbe essere visualizzata nell'elenco delle risorse prima della cancellazione dei messaggi di "distribuzione in corso". Controllare l'elenco delle risorse dopo un minuto o due anziché attendere una notifica di esito positivo.

Questo processo in due passaggi è necessario perché l'istanza Cache HPC di Azure richiede un'identità da passare al Azure Key Vault per l'autorizzazione. L'identità della cache non esiste fino al completamento dei passaggi di creazione iniziali.

> [!NOTE]
> È necessario autorizzare la crittografia entro 90 minuti dalla creazione della cache. Se non si completa questo passaggio, si verifica il timeout e l'esito negativo della cache. Una cache non riuscita deve essere ri-creata e non può essere corretta.

La cache mostra lo stato **In attesa della chiave**. Fare clic **sul pulsante Abilita** crittografia nella parte superiore della pagina per autorizzare la cache ad accedere all'insieme di credenziali delle chiavi specificato.

![screenshot della pagina di panoramica della cache nel portale, con evidenziato il pulsante Abilita crittografia (riga superiore) e lo stato: In attesa della chiave](media/waiting-for-key.png)

Fare **clic su Abilita** crittografia e quindi sul **pulsante** Sì per autorizzare la cache a usare la chiave di crittografia. Questa azione abilita anche l'eliminazione e la protezione dall'eliminazione (se non è già abilitata) nell'insieme di credenziali delle chiavi.

![screenshot della pagina di panoramica della cache nel portale, con un messaggio di intestazione nella parte superiore che chiede all'utente di abilitare la crittografia facendo clic su Sì](media/enable-keyvault.png)

Dopo che la cache richiede l'accesso all'insieme di credenziali delle chiavi, può creare e crittografare i dischi in cui sono archiviati i dati memorizzati nella cache.

Dopo aver autorizzato la crittografia, Cache HPC di Azure altri minuti di configurazione per creare i dischi crittografati e l'infrastruttura correlata.

## <a name="update-key-settings"></a>Aggiornare le impostazioni delle chiavi

È possibile modificare l'insieme di credenziali delle chiavi, la chiave o la versione della chiave per la cache dal portale di Azure. Fare clic sul collegamento **Impostazioni** crittografia della cache per aprire la **pagina Impostazioni chiave** cliente.

Non è possibile modificare una cache tra chiavi gestite dal cliente e chiavi gestite dal sistema.

![screenshot della pagina "Impostazioni chiavi cliente", raggiungibile facendo clic su Impostazioni > crittografia dalla pagina della cache nel portale di Azure](media/change-key-click.png)

Fare clic **sul collegamento Cambia chiave,** quindi fare clic **su Cambia l'insieme** di credenziali delle chiavi, la chiave o la versione per aprire il selettore di chiavi.

![Screenshot della pagina "Select key from Azure Key Vault" con tre selettori a discesa per scegliere l'insieme di credenziali delle chiavi, la chiave e la versione](media/select-new-key.png)

Gli insiemi di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area della cache vengono visualizzati nell'elenco.

Dopo aver scelto i nuovi valori della chiave di crittografia, fare clic su **Seleziona**. Verrà visualizzata una pagina di conferma con i nuovi valori. Fare **clic su** Salva per finalizzare la selezione.

![Screenshot della pagina di conferma con il pulsante Salva in alto a sinistra](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Altre informazioni sulle chiavi gestite dal cliente in Azure

Questi articoli illustrano di più sull'uso Azure Key Vault chiavi gestite dal cliente per crittografare i dati in Azure:

* [Panoramica della crittografia di archiviazione di Azure](../storage/common/storage-service-encryption.md)
* [Crittografia del disco con chiavi gestite dal cliente:](../virtual-machines/disk-encryption.md#customer-managed-keys) documentazione per l'Azure Key Vault con i dischi gestiti, che è uno scenario simile a quello Cache HPC di Azure

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la Cache HPC di Azure e la crittografia Key Vault basata su Key Vault, continuare a configurare la cache fornendogli l'accesso alle origini dati.

* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md)
