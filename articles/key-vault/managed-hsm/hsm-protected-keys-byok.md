---
title: Come generare e trasferire chiavi protette tramite HSM per Azure Key Vault HSM gestito - Azure Key Vault | Microsoft Docs
description: Usare questo articolo per pianificare, generare e trasferire le proprie chiavi protette da HSM da usare con il servizio HSM gestito. Questo approccio è noto anche come BYOK o Bring Your Own Key.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: cc9037db3289d7fb3287a8994a8ff6a68fc0583a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790582"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importare le chiavi con protezione HSM in HSM gestito (BYOK)

 Azure Key Vault modulo di protezione hardware gestito supporta l'importazione di chiavi generate nel modulo di protezione hardware locale; Le chiavi non lasciano mai il limite di protezione HSM. Questo scenario viene spesso definito *BYOK* (Bring Your Own Key). Il modulo di protezione HSM gestito usa gli adattatori HSM DisassluLue (convalidati fiPS 140-2 Livello 3) per proteggere le chiavi.

Usare le informazioni contenute in questo articolo per pianificare, generare e trasferire le proprie chiavi protette da HSM da usare con il servizio HSM gestito.

> [!NOTE]
> Questa funzionalità non è disponibile per Azure Cina 21Vianet. Questo metodo di importazione è disponibile solo per i [moduli di protezione hardware supportati](#supported-hsms). 

Per altre informazioni e per un'esercitazione introduttiva sull'uso del servizio HSM gestito, vedere [Che cos'è un HSM gestito?](overview.md).

## <a name="overview"></a>Panoramica

Ecco una panoramica del processo. I passaggi specifici da completare sono illustrati più avanti nell'articolo.

* Nel modulo di protezione HSM gestito generare una chiave (denominata chiave di *scambio* delle chiavi). La chiave KEK deve essere una chiave RSA-HSM che ha solo l'operazione `import` della chiave. 
* Scaricare la chiave pubblica KEK come file PEM.
* Trasferire la chiave pubblica KEK in un computer offline connesso a un modulo di protezione hardware locale.
* Nel computer offline usare lo strumento BYOK fornito dal fornitore del modulo di protezione hardware per creare un file BYOK. 
* La chiave di destinazione viene crittografata con una chiave di crittografia della chiave, che rimane crittografata fino a quando non viene trasferita al servizio HSM gestito. Solo la versione crittografata della chiave esce dal modulo di protezione hardware locale.
* Una chiave kek generata all'interno di un HSM gestito non è esportabile. I HSM applicano la regola che non esiste una versione non crittografata di una chiave di protezione dall'esterno di un HSM gestito.
* La chiave KEK deve essere nello stesso HSM gestito in cui verrà importata la chiave di destinazione.
* Quando il file BYOK viene caricato nel servizio HSM gestito, un HSM gestito usa la chiave privata KEK per decrittografare il materiale della chiave di destinazione e importarlo come chiave HSM. Questa operazione viene eseguita interamente all'interno del HSM. La chiave di destinazione rimane sempre entro i limiti di protezione del modulo di protezione hardware.


## <a name="prerequisites"></a>Prerequisiti

Per usare i comandi dell'interfaccia della riga di comando di Azure in questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando di Azure versione 2.12.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
* Un HSM gestito, [l'elenco di HSM](#supported-hsms) supportati nella sottoscrizione. Vedere [Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure](quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli)

## <a name="supported-hsms"></a>Moduli di protezione hardware supportati

|Nome del fornitore|Tipo di fornitore|Modelli di moduli di protezione hardware supportati|Ulteriori informazioni|
|---|---|---|---|
|nCipher|Produttore,<br/>modulo di protezione hardware come servizio|<ul><li>Famiglia di moduli di protezione hardware nShield</li><li>nShield come servizio</ul>|[Nuovo strumento BYOK e documentazione di nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Produttore|<ul><li>Famiglia di prodotti Luna HSM 7 con firmware con versione 7.3 o successiva</li></ul>| [Strumento BYOK e documentazione di Luna](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Produttore,<br/>modulo di protezione hardware come servizio|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Esportazione di chiavi SDKMS nei provider di servizi cloud per BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Produttore|Moduli di protezione hardware di All LiquidSecurity con<ul><li>Firmware con versione 2.0.4 o successiva</li><li>Firmware con versione 3.2 o successiva</li></ul>|[Strumento BYOK e documentazione di Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Più marchi e modelli di moduli di protezione hardware, tra cui<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Per dettagli, vedere il [sito di Cryptomathic](https://www.cryptomathic.com/azurebyok)|[Strumento BYOK e documentazione di Cryptomathic](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Produttore, modulo di protezione hardware come servizio|Famiglia di moduli di protezione hardware Primus, Securosys Clouds HSM|[Strumento BYOK e documentazione di Primus](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Più marchi e modelli di moduli di protezione hardware, tra cui<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Per dettagli, vedere il [sito di StorMagic](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS e BYOK di Azure Key Vault](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Produttore|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Produttore,<br/>modulo di protezione hardware come servizio|u.trust Anchor, CryptoServer|[Guida all'integrazione e strumento Utimaco BYOK](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Tipi di chiave supportati

|Nome della chiave|Tipo di chiave|Dimensioni chiave/curva|Origine|Descrizione|
|---|---|---|---|---|
|Chiave KEK (Key Exchange Key)|RSA| A 2.048 bit<br />A 3.072 bit<br />A 4.096 bit|Modulo di protezione hardware gestito|Una coppia di chiavi RSA supportata da HSM generata in un HSM gestito|
|Chiave di destinazione|
||RSA|A 2.048 bit<br />A 3.072 bit<br />A 4.096 bit|Modulo di protezione hardware del fornitore|Chiave da trasferire al servizio HSM gestito|
||EC|P-256<br />P-384<br />P-521|Modulo di protezione hardware del fornitore|Chiave da trasferire al servizio HSM gestito|
||Chiave simmetrica (oct-HSM)|128 bit<br />192 bit<br />256 bit|Modulo di protezione hardware del fornitore|Chiave da trasferire al servizio HSM gestito|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Generare e trasferire la chiave al servizio HSM gestito

Per generare e trasferire la chiave in un HSM gestito:

* [Passaggio 1: Generare una chiave KEK](#step-1-generate-a-kek)
* [Passaggio 2: Scaricare la chiave pubblica KEK](#step-2-download-the-kek-public-key)
* [Passaggio 3: Generare e preparare la chiave per il trasferimento](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passaggio 4: Trasferire la chiave in HSM gestito](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Passaggio 1: Generare una chiave KEK

Una chiave KEK è una chiave RSA generata in un HSM gestito. La chiave KEK viene usata per crittografare la chiave da importare, ovvero la chiave di *destinazione*.

La chiave KEK deve essere:
- Una chiave RSA-HSM (a 2.048 bit, a 3.072 bit o a 4.096 bit)
- Generato nello stesso HSM gestito in cui si intende importare la chiave di destinazione
- Creata con operazioni di chiave consentite impostate su `import`

> [!NOTE]
> La chiave KEK deve avere 'import' come unica operazione di chiave consentita. L'operazione 'import' e tutte le altre operazioni delle chiavi si escludono a vicenda.

Usare il comando [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) per creare una chiave KEK con operazioni della chiave impostate su `import`. Registrare l'identificatore della chiave (`kid`) restituito dal comando seguente. Il valore `kid` verrà usato nel [Passaggio 3](#step-3-generate-and-prepare-your-key-for-transfer).

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Passaggio 2: Scaricare la chiave pubblica KEK

Usare [az keyvault key download](/cli/azure/keyvault/key#az_keyvault_key_download) per scaricare la chiave pubblica KEK in un file PEM. La chiave di destinazione importata viene crittografata mediante la chiave pubblica KEK.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Trasferire il file KEKforBYOK.publickey.pem nel computer offline. Questo file sarà necessario nel passaggio successivo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passaggio 3: Generare e preparare la chiave per il trasferimento

Vedere la documentazione del fornitore del modulo di protezione hardware per scaricare e installare lo strumento BYOK. Seguire le istruzioni del fornitore del modulo di protezione hardware per generare una chiave di destinazione e quindi creare il pacchetto di trasferimento della chiave, ovvero un file BYOK. Lo strumento BYOK userà il valore `kid` dal [Passaggio 1](#step-1-generate-a-kek) e il file KEKforBYOK.publickey.pem scaricato nel [Passaggio 2](#step-2-download-the-kek-public-key) per generare una chiave di destinazione crittografata in un file BYOK.

Trasferire il file BYOK nel computer connesso.

> [!NOTE] 
> L'importazione delle chiavi RSA a 1.024 bit non è supportata. Non è attualmente supportata l'importazione di una chiave a curva ellittica (EC).
>
> **Problema noto**: l'importazione di una chiave di destinazione RSA 4K da moduli di protezione hardware di Luna è supportata solo con firmware 7.4.0 o versioni successive.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Passaggio 4: Trasferire la chiave in HSM gestito

Per completare l'importazione della chiave, trasferire il pacchetto di trasferimento della chiave, ovvero un file BYOK, dal computer disconnesso al computer connesso a Internet. Usare il [comando az keyvault key import](/cli/azure/keyvault/key#az_keyvault_key_import) per caricare il file BYOK nel servizio HSM gestito.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se il caricamento ha esito positivo, l'interfaccia della riga di comando di Azure mostrerà le proprietà della chiave importata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave protetta da HSM nel sistema HSM gestito. Per altre informazioni, vedere [questo confronto tra prezzi e funzionalità](https://azure.microsoft.com/pricing/details/key-vault/).
