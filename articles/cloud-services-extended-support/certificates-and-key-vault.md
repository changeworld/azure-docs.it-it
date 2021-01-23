---
title: Archiviare e usare i certificati in Servizi cloud di Azure (supporto "Extended")
description: Processi per l'archiviazione e l'uso di certificati in servizi cloud di Azure (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744476"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Usare i certificati con servizi cloud di Azure (supporto esteso)

Key Vault viene utilizzato per archiviare i certificati associati ai servizi cloud (supporto esteso). Gli insiemi di credenziali delle chiavi possono essere creati tramite [portale di Azure](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) e [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). Aggiungere i certificati a Key Vault, quindi fare riferimento alle identificazioni personali del certificato nel file di configurazione del servizio. È anche necessario abilitare Key Vault per le autorizzazioni appropriate in modo che la risorsa servizi cloud (supporto esteso) possa recuperare il certificato archiviato come segreto da Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Caricare un certificato in Key Vault 

1.  Accedere al portale di Azure e passare al Key Vault. Se non si dispone di un Key Vault configurato, è possibile scegliere di crearne uno nella stessa finestra.

2. Selezionare i **criteri di accesso**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Immagine mostra la selezione dei criteri di accesso nel pannello dell'insieme di credenziali delle chiavi.":::

3. Verificare che i criteri di accesso includano le proprietà seguenti:
    - **Abilitare l'accesso alle macchine virtuali di Azure per la distribuzione**
    - **Abilitare l'accesso alla Azure Resource Manager per la distribuzione dei modelli** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Image Mostra la finestra criteri di accesso nella portale di Azure.":::
 
4.  Selezione **certificati** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Immagine mostra la selezione dell'opzione certificati dalla finestra dei criteri del pannello dell'insieme di credenziali delle chiavi nella portale di Azure.":::

5. Selezionare **genera/importa**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Immagine che mostra la selezione dell'opzione di generazione/importazione":::

4.  Completare le informazioni necessarie per completare il caricamento del certificato. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Image Mostra la finestra di importazione nel portale di Azure.":::

5.  Aggiungere i dettagli del certificato al ruolo nel file di configurazione del servizio (. cscfg). Verificare che l'identificazione personale del certificato nella portale di Azure corrisponda all'identificazione digitale nel file di configurazione del servizio (. cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).