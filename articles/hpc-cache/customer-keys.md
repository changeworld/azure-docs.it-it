---
title: Usare chiavi usate dal cliente per crittografare i dati in Cache HPC di Azure
description: Come usare le Azure Key Vault con Cache HPC di Azure per controllare l'accesso alla chiave di crittografia invece di usare le chiavi di crittografia predefinite gestite da Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 36ce494c7fd51a1341834d5c231e32e60c5a32b9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751995"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Usare chiavi di crittografia gestite dal cliente per Cache HPC di Azure

È possibile usare Azure Key Vault per controllare la proprietà delle chiavi usate per crittografare i dati in Cache HPC di Azure. Questo articolo illustra come usare le chiavi gestite dal cliente per la crittografia dei dati della cache.

> [!NOTE]
> Per impostazione predefinita, tutti i dati archiviati in Azure, inclusi i dischi della cache, vengono crittografati quando sono in pausa usando chiavi gestite da Microsoft. È necessario seguire i passaggi descritti in questo articolo solo se si vogliono gestire le chiavi usate per crittografare i dati.

Cache HPC di Azure è protetto anche dalla crittografia [dell'host](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) della macchina virtuale nei dischi gestiti che contengono i dati memorizzati nella cache, anche se si aggiunge una chiave cliente per i dischi della cache. L'aggiunta di una chiave gestita dal cliente per la crittografia doppia offre un livello aggiuntivo di sicurezza per i clienti con esigenze di sicurezza elevate. Per informazioni [dettagliate, vedere Crittografia lato server dell'archiviazione su disco](../virtual-machines/disk-encryption.md) di Azure.

Esistono tre passaggi per abilitare la crittografia della chiave gestita dal cliente per Cache HPC di Azure:

1. Configurare un'Azure Key Vault per archiviare le chiavi.
1. Quando si crea il Cache HPC di Azure, scegliere la crittografia della chiave gestita dal cliente e specificare l'insieme di credenziali delle chiavi e la chiave da usare.
1. Dopo aver creato la cache, autorizzarla ad accedere all'insieme di credenziali delle chiavi.

La crittografia non è completamente impostata fino a quando non viene autorizzata dalla cache appena creata (passaggio 3). Questo perché è necessario passare l'identità della cache all'insieme di credenziali delle chiavi per renderla un utente autorizzato. Non è possibile eseguire questa operazione prima di creare la cache, perché l'identità non esiste fino a quando non viene creata la cache.

Dopo aver creato la cache, non è possibile passare da chiavi gestite dal cliente a chiavi gestite da Microsoft. Tuttavia, se la cache usa chiavi [](#update-key-settings) gestite dal cliente, è possibile modificare la chiave di crittografia, la versione della chiave e l'insieme di credenziali delle chiavi in base alle esigenze.

## <a name="understand-key-vault-and-key-requirements"></a>Informazioni su key vault e requisiti delle chiavi

L'insieme di credenziali delle chiavi e la chiave devono soddisfare questi requisiti per funzionare con Cache HPC di Azure.

Proprietà dell'insieme di credenziali delle chiavi:

* **Sottoscrizione:** usare la stessa sottoscrizione usata per la cache.
* **Area:** l'insieme di credenziali delle chiavi deve essere nella stessa area del Cache HPC di Azure.
* **Piano tariffario:** il livello Standard è sufficiente per l'uso con Cache HPC di Azure.
* **Eliminazione soft:** Cache HPC di Azure l'eliminazione soft se non è già configurata nell'insieme di credenziali delle chiavi.
* **Protezione dell'eliminazione:** è necessario che la protezione dell'eliminazione sia abilitata.
* **Criteri di accesso:** sono sufficienti le impostazioni predefinite.
* **Connettività di** rete: Cache HPC di Azure essere in grado di accedere all'insieme di credenziali delle chiavi, indipendentemente dalle impostazioni dell'endpoint scelto.

Proprietà chiave:

* **Tipo di chiave** - RSA
* **Dimensioni della chiave RSA** - 2048
* **Abilitato** - Sì

Autorizzazioni di accesso all'insieme di credenziali delle chiavi:

* L'utente che crea il Cache HPC di Azure deve disporre di autorizzazioni equivalenti al ruolo Key Vault [collaboratore](../role-based-access-control/built-in-roles.md#key-vault-contributor). Le stesse autorizzazioni sono necessarie per configurare e gestire i Azure Key Vault.

  Per [altre informazioni, vedere](../key-vault/general/security-overview.md) Accesso sicuro a un insieme di credenziali delle chiavi.

## <a name="1-set-up-azure-key-vault"></a>1. Configurare Azure Key Vault

È possibile configurare un insieme di credenziali delle chiavi e una chiave prima di creare la cache o come parte della creazione della cache. Assicurarsi che queste risorse soddisfino i requisiti descritti in [precedenza.](#understand-key-vault-and-key-requirements)

Al momento della creazione della cache è necessario specificare un insieme di credenziali, una chiave e una versione della chiave da usare per la crittografia della cache.

Leggere la [documentazione Azure Key Vault per](../key-vault/general/overview.md) informazioni dettagliate.

> [!NOTE]
> Il Azure Key Vault deve usare la stessa sottoscrizione e deve essere nella stessa area del Cache HPC di Azure. Assicurarsi che l'area scelta [supporti entrambi i prodotti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Creare la cache con le chiavi gestite dal cliente abilitate

È necessario specificare l'origine della chiave di crittografia quando si crea il Cache HPC di Azure. Seguire le istruzioni in [Creare un Cache HPC di Azure](hpc-cache-create.md)e specificare l'insieme di credenziali delle chiavi e la chiave nella pagina Chiavi di crittografia **del** disco. È possibile creare un nuovo insieme di credenziali delle chiavi e una nuova chiave durante la creazione della cache.

> [!TIP]
> Se la **pagina Chiavi di crittografia** del disco non viene visualizzata, assicurarsi che la cache si trova in una delle aree [supportate.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

L'utente che crea la cache deve avere privilegi uguali al ruolo [Key Vault collaboratore o](../role-based-access-control/built-in-roles.md#key-vault-contributor) superiore.

1. Fare clic sul pulsante per abilitare le chiavi gestite privatamente. Dopo aver modificato questa impostazione, vengono visualizzate le impostazioni dell'insieme di credenziali delle chiavi.

1. Fare clic **su Selezionare un insieme di credenziali delle** chiavi per aprire la pagina di selezione della chiave. Scegliere o creare l'insieme di credenziali delle chiavi e la chiave per crittografare i dati nei dischi della cache.

   Se il Azure Key Vault non viene visualizzato nell'elenco, verificare i requisiti seguenti:

   * La cache si trova nella stessa sottoscrizione dell'insieme di credenziali delle chiavi?
   * La cache si trova nella stessa area dell'insieme di credenziali delle chiavi?
   * Esiste connettività di rete tra il portale di Azure e l'insieme di credenziali delle chiavi?

1. Dopo aver selezionato un insieme di credenziali, selezionare la singola chiave tra le opzioni disponibili oppure creare una nuova chiave. La chiave deve essere una chiave RSA a 2048 bit.

1. Specificare la versione per la chiave selezionata. Altre informazioni sul controllo delle versioni sono [Azure Key Vault documentazione di](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Continuare con le altre specifiche e creare la cache come descritto in [Creare un](hpc-cache-create.md)Cache HPC di Azure .

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorizzare Azure Key Vault crittografia dalla cache
<!-- header is linked from create article, update if changed -->

Dopo alcuni minuti, il nuovo Cache HPC di Azure viene visualizzato nel portale di Azure. Passare alla pagina **Panoramica** per autorizzarlo ad accedere al Azure Key Vault e abilitare la crittografia della chiave gestita dal cliente.

> [!TIP]
> La cache potrebbe essere visualizzata nell'elenco delle risorse prima della cancellazione dei messaggi di "distribuzione in corso". Controllare l'elenco delle risorse dopo un minuto o due anziché attendere una notifica di esito positivo.

Questo processo in due passaggi è necessario perché l'istanza Cache HPC di Azure deve passare un'identità al Azure Key Vault per l'autorizzazione. L'identità della cache non esiste fino al completamento dei passaggi di creazione iniziali.

> [!NOTE]
> È necessario autorizzare la crittografia entro 90 minuti dalla creazione della cache. Se non si completa questo passaggio, si verifica il timeout della cache e si verifica un errore. Una cache non riuscita deve essere ri-creata e non può essere corretta.

Nella cache viene visualizzato lo stato **In attesa della chiave**. Fare clic **sul pulsante Abilita** crittografia nella parte superiore della pagina per autorizzare la cache ad accedere all'insieme di credenziali delle chiavi specificato.

![Screenshot della pagina di panoramica della cache nel portale, con l'evidenziazione sul pulsante Abilita crittografia (riga superiore) e su Stato: In attesa della chiave](media/waiting-for-key.png)

Fare **clic su Abilita** crittografia e quindi sul **pulsante** Sì per autorizzare la cache a usare la chiave di crittografia. Questa azione abilita anche la protezione da eliminazione e ripulitura (se non è già abilitata) nell'insieme di credenziali delle chiavi.

![Screenshot della pagina di panoramica della cache nel portale, con un messaggio banner nella parte superiore che chiede all'utente di abilitare la crittografia facendo clic su Sì](media/enable-keyvault.png)

Dopo che la cache richiede l'accesso all'insieme di credenziali delle chiavi, può creare e crittografare i dischi in cui sono archiviati i dati memorizzati nella cache.

Dopo aver autorizzato la crittografia, Cache HPC di Azure vengono evasi diversi minuti di configurazione per creare i dischi crittografati e l'infrastruttura correlata.

## <a name="update-key-settings"></a>Aggiornare le impostazioni della chiave

È possibile modificare l'insieme di credenziali delle chiavi, la chiave o la versione della chiave per la cache dal portale di Azure. Fare clic sul collegamento **Impostazioni crittografia** della cache per aprire la pagina **Impostazioni chiave** cliente.

Non è possibile modificare una cache tra chiavi gestite dal cliente e chiavi gestite dal sistema.

![screenshot della pagina "Impostazioni chiavi cliente", raggiungibile facendo clic su Impostazioni > crittografia dalla pagina della cache nel portale di Azure](media/change-key-click.png)

Fare clic **sul collegamento Cambia chiave** e quindi su Modifica **l'insieme di** credenziali delle chiavi, la chiave o la versione per aprire il selettore di chiavi.

![screenshot della pagina "Select key from Azure Key Vault" con tre selettori a discesa per scegliere l'insieme di credenziali delle chiavi, la chiave e la versione](media/select-new-key.png)

Gli insiemi di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area della cache vengono visualizzati nell'elenco.

Dopo aver scelto i nuovi valori della chiave di crittografia, fare clic su **Seleziona**. Viene visualizzata una pagina di conferma con i nuovi valori. Fare **clic su** Salva per finalizzare la selezione.

![screenshot della pagina di conferma con il pulsante Salva in alto a sinistra](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Altre informazioni sulle chiavi gestite dal cliente in Azure

Questi articoli illustrano altre informazioni sull'uso Azure Key Vault chiavi gestite dal cliente per crittografare i dati in Azure:

* [Panoramica della crittografia di archiviazione di Azure](../storage/common/storage-service-encryption.md)
* [Crittografia dischi con chiavi gestite dal cliente:](../virtual-machines/disk-encryption.md#customer-managed-keys) documentazione per l'uso di Azure Key Vault con dischi gestiti, che è uno scenario simile a quello Cache HPC di Azure

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il Cache HPC di Azure e la crittografia Key Vault basata su Key Vault, continuare a configurare la cache fornendo l'accesso alle origini dati.

* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md)
