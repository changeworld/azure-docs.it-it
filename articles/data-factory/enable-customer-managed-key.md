---
title: Crittografare Azure Data Factory con la chiave gestita dal cliente
description: Migliorare la sicurezza di Data Factory con la modalità Bring Your Own Key (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: 4e30ac2e6836b40ab0c0c2915eae48266cb4845c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199212"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Crittografare Azure Data Factory con le chiavi gestite dal cliente

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory crittografa i dati inattivi, ad esempio le definizioni delle entità e i dati memorizzati nella cache mentre le esecuzioni sono in corso. Per impostazione predefinita, i dati vengono crittografati con una chiave gestita da Microsoft e generata in modo casuale, assegnata in modo univoco alla data factory. Per garantire garanzie di sicurezza aggiuntive, è ora possibile abilitare Bring Your Own Key (BYOK) con la funzionalità chiavi gestite dal cliente in Azure Data Factory. Quando si specifica una chiave gestita dal cliente, per crittografare i dati del cliente Data Factory usa __entrambe__ le chiavi, ovvero la chiave del sistema data factory e la chiave gestita dal cliente. La mancanza di una delle due chiavi può comportare la negazione dell'accesso ai dati e alla factory.

Per archiviare le chiavi gestite dal cliente, è necessario Azure Key Vault. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. L'insieme delle credenziali della chiavi e Data Factory devono essere presenti nello stesso tenant di Azure Active Directory (Azure AD) e nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Informazioni sulle chiavi gestite dal cliente

Il diagramma seguente illustra il modo in cui Data Factory usa Azure Active Directory e Azure Key Vault per effettuare richieste usando la chiave gestita dal cliente:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagramma che illustra il funzionamento delle chiavi gestite dal cliente in Azure Data Factory.":::

Nell'elenco seguente vengono illustrati i passaggi numerati nel diagramma:

1. Un amministratore di Azure Key Vault concede le autorizzazioni per le chiavi di crittografia all'identità gestita associata a Data Factory
1. Un amministratore di Data Factory abilita la funzionalità chiave gestita dal cliente nella factory
1. Data Factory usa l'identità gestita associata alla factory per autenticare l'accesso ad Azure Key Vault tramite Azure Active Directory
1. Data Factory esegue il wrapping della chiave di crittografia della factory con la chiave del cliente in Azure Key Vault
1. Per le operazioni di lettura/scrittura, Data Factory invia richieste ad Azure Key Vault per annullare il wrapping della chiave di crittografia dell'account per l'esecuzione di crittografia e decrittografia

Esistono due modi per aggiungere la crittografia delle chiavi gestite dal cliente alle data factory. Uno è durante la creazione del produttore in portale di Azure e l'altro è la creazione post-produzione nell'interfaccia utente Data Factory.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Prerequisiti - Configurare Azure Key Vault e generare chiavi

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Abilitare l'eliminazione temporanea e non eliminare Azure Key Vault

L'uso delle chiavi gestite dal cliente con Data Factory richiede l'impostazione di due proprietà in Key Vault, ovvero __Eliminazione temporanea__ e __Do Not Purge__ (Non eliminare). Queste proprietà possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente. Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere la pagina relativa alla [gestione del ripristino Azure Key Vault con eliminazione temporanea e ripulitura](../key-vault/general/key-vault-recovery.md)

Se si crea un nuovo Azure Key Vault tramite il portale di Azure, le proprietà __Eliminazione temporanea__ e __Do Not Purge__ (Non eliminare) possono essere abilitate come indicato di seguito:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Screenshot che illustra come abilitare l'eliminazione temporanea e ripulire la protezione al momento della creazione del Key Vault.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Concedere a Data Factory l'accesso ad Azure Key Vault

Assicurarsi che Azure Key Vault e Azure Data Factory si trovino nello stesso tenant Azure Active Directory (Azure AD) e nella _stessa area_. Dal controllo di accesso Azure Key Vault concedere data factory le autorizzazioni seguenti: _Get_, _Unwrap Key_ e _Wrap Key_. Queste autorizzazioni sono necessarie per abilitare le chiavi gestite dal cliente in Data Factory.

* Se si vuole aggiungere la crittografia della chiave gestita [dal cliente dopo la creazione della Factory nell'interfaccia utente di data factory](#post-factory-creation-in-data-factory-ui), assicurarsi che l'identità del servizio gestito Data Factory (MSI) disponga delle tre autorizzazioni per Key Vault
* Se si vuole aggiungere la crittografia della chiave gestita [dal cliente durante la creazione del produttore in portale di Azure](#during-factory-creation-in-azure-portal), assicurarsi che l'identità gestita assegnata dall'utente (UA-mi) disponga delle tre autorizzazioni per Key Vault

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Screenshot che illustra come abilitare l'accesso Data Factory Key Vault.":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Generare o caricare la chiave gestita dal cliente in Azure Key Vault

È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi. In alternativa, è possibile usare le API Azure Key Vault per generare chiavi. Con la crittografia di Data Factory sono supportate solo le chiavi RSA a 2048 bit. Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Screenshot che illustra come generare una chiave di Customer-Managed.":::

## <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente

### <a name="post-factory-creation-in-data-factory-ui"></a>Creazione post-produzione nell'interfaccia utente di Data Factory

Questa sezione illustra il processo di aggiunta della crittografia delle chiavi gestite dal cliente nell'interfaccia utente di Data Factory, _dopo_ la creazione della factory.

> [!NOTE]
> Una chiave gestita dal cliente può essere configurata solo in una data factory vuota. La data factory non può contenere risorse quali servizi collegati, pipeline e flussi di dati. È consigliabile abilitare la chiave gestita dal cliente subito dopo la creazione della factory.

> [!IMPORTANT]
> Questo approccio non funziona con le factory abilitate per la rete virtuale gestita. Prendere in considerazione la [route alternativa](#during-factory-creation-in-azure-portal), se si desidera crittografare tali Factory.

1. Verificare che l'identità del servizio gestita (MSI) di data factory disponga delle autorizzazioni _Get_, _Unwrap Key_ e _Wrap Key_ per Key Vault.

1. Verificare che la data factory sia vuota. Il data factory non può contenere risorse quali servizi collegati, pipeline e flussi di dati. Per il momento, la distribuzione della chiave gestita dal cliente in una factory non vuota comporterà un errore.

1. Per individuare l'URI della chiave nel portale di Azure, passare ad Azure Key Vault e selezionare l'impostazione Chiavi. Selezionare la chiave Wanted, quindi selezionare la chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni

1. Copiare il valore del campo identificatore chiave, che fornisce la schermata URI :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="del recupero dell'URI chiave da Key Vault.":::

1. Avviare il portale di Azure Data Factory e usare la barra di spostamento a sinistra per passare al portale di gestione di Data Factory

1. Fare clic sullo screenshot dell'icona della __chiave Customer manged__ :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="come abilitare la chiave gestita dal cliente nell'interfaccia utente di data factory.":::

1. Immettere l'URI per la chiave gestita dal cliente copiata in precedenza

1. Fare clic su __Salva__ per abilitare la crittografia delle chiavi gestite dal cliente in Data Factory

### <a name="during-factory-creation-in-azure-portal"></a>Durante la creazione della factory in portale di Azure

Questa sezione illustra i passaggi per aggiungere la crittografia delle chiavi gestite dal cliente in portale di Azure _durante_ la distribuzione di Factory.

Per crittografare la Factory, Data Factory necessario prima recuperare la chiave gestita dal cliente da Key Vault. Poiché la distribuzione della Factory è ancora in corso, identità del servizio gestita (MSI) non è ancora disponibile per l'autenticazione con Key Vault. Di conseguenza, per usare questo approccio, il cliente deve assegnare un'identità gestita assegnata dall'utente (UA-MI) a data factory. Si presuppone che i ruoli definiti in UA-MI e eseguano l'autenticazione con Key Vault.

Per altre informazioni sull'identità gestita assegnata dall'utente, vedere [tipi di identità gestiti](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) e [assegnazione di ruolo per l'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Assicurarsi che l'identità gestita assegnata dall'utente (UA-MI) disponga delle autorizzazioni _Get_, _Unwrap Key_ e _Wrap Key_ per Key Vault

1. Nella scheda __Avanzate__ selezionare la casella di controllo _Abilita crittografia utilizzando una chiave gestita_ 
   :::image type="content" source="media/enable-customer-managed-key/06-uami-cmk.png" alt-text="dal cliente schermata della scheda avanzate per data factory esperienza di creazione in portale di Azure.":::

1. Specificare l'URL per Key Vault

1. Selezionare un'identità gestita assegnata dall'utente appropriata per l'autenticazione con Key Vault

1. Continua con la distribuzione di Factory

## <a name="update-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, per usarla è necessario aggiornare la data factory. Seguire una procedura simile, come descritto nella sezione [Data Factory interfaccia utente](#post-factory-creation-in-data-factory-ui), tra cui:

1. Individuare l'URI per la nuova versione della chiave tramite il portale di Azure Key Vault

1. Passare all'impostazione __Chiave gestita dal cliente__

1. Sostituire il valore e incollarlo nell'URI per la nuova chiave

1. Fare clic su __Salva__ e Data Factory eseguirà ora la crittografia con la nuova versione della chiave

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia di Data Factory, è necessario aggiornare manualmente le impostazioni in Data Factory. Seguire una procedura simile, come descritto nella sezione [Data Factory interfaccia utente](#post-factory-creation-in-data-factory-ui), tra cui:

1. Individuare l'URI per la nuova chiave tramite il portale di Azure Key Vault

1. Passare all'impostazione __Chiave gestita dal cliente__

1. Sostituire il valore e incollarlo nell'URI per la nuova chiave

1. Fare clic su __Salva__ e Data Factory eseguirà ora la crittografia con la nuova chiave

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Per impostazione predefinita, dopo aver abilitato la funzionalità relativa alla chiave gestita dal cliente, non è possibile rimuovere il passaggio di sicurezza aggiuntivo. Si prevede sempre una chiave fornita dal cliente per crittografare i dati e la factory.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md).