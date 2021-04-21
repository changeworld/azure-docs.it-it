---
title: Crittografare l'origine dell'applicazione in stato di inquieto
description: Informazioni su come crittografare i dati dell'applicazione Archiviazione di Azure e distribuirlo come file di pacchetto.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: a26660723fbe96a9b765401af1f0c9cfc80dbc3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779435"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia dei dati in pausa con chiavi gestite dal cliente

Per crittografare i dati dell'applicazione dell'app Web in Archiviazione di Azure sono necessari un account Azure Key Vault. Questi servizi vengono usati quando si esegue l'app da un pacchetto di distribuzione.

  - [Archiviazione di Azure la crittografia dei dati in stato di inalter.](../storage/common/storage-service-encryption.md) È possibile usare chiavi fornite dal sistema o chiavi gestite dal cliente. Qui vengono archiviati i dati dell'applicazione quando non sono in esecuzione in un'app Web in Azure.
  - [L'esecuzione da un pacchetto di](deploy-run-package.md) distribuzione è una funzionalità di distribuzione del servizio app. Consente di distribuire il contenuto del sito da un account Archiviazione di Azure usando un URL di firma di accesso condiviso ( SAS).
  - [Key Vault informazioni di](app-service-key-vault-references.md) riferimento sono una funzionalità di sicurezza del servizio app. Consente di importare segreti in fase di esecuzione come impostazioni dell'applicazione. Usare questa opzione per crittografare l'URL di firma di accesso condiviso dell Archiviazione di Azure appliato.

## <a name="set-up-encryption-at-rest"></a>Configurare la crittografia dei dati in stato di inquieto

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Creare prima [di tutto un account Archiviazione di Azure e](../storage/common/storage-account-create.md) [crittografarlo con chiavi gestite dal cliente.](../storage/common/customer-managed-keys-overview.md) Dopo aver creato l'account di archiviazione, usare il [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file del pacchetto.

Successivamente, usare il Storage Explorer per [generare una firma di accesso condiviso.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer) 

> [!NOTE]
> Salvare questo URL di firma di accesso condiviso, che verrà usato in un secondo momento per abilitare l'accesso sicuro al pacchetto di distribuzione in fase di esecuzione.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configurare l'esecuzione da un pacchetto dall'account di archiviazione
  
Dopo aver caricato il file nell'archivio BLOB e avere un URL di firma di accesso condiviso per il file, impostare l'impostazione dell'applicazione `WEBSITE_RUN_FROM_PACKAGE` sull'URL di firma di accesso condiviso. L'esempio seguente esegue questa procedura usando l'interfaccia della riga di comando di Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Se si aggiunge questa impostazione dell'applicazione, l'app Web verrà riavviata. Dopo il riavvio dell'app, individuarla e verificare che l'app sia stata avviata correttamente usando il pacchetto di distribuzione. Se l'applicazione non è stata avviata correttamente, vedere la guida alla risoluzione dei [problemi esegui dal pacchetto](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Crittografare l'impostazione dell'applicazione Key Vault riferimenti

È ora possibile sostituire il valore dell'impostazione dell'applicazione con Key Vault `WEBSITE_RUN_FROM_PACKAGE` riferimento all'URL con codifica SAS. In questo modo l'URL di firma di accesso condiviso viene crittografato Key Vault, che offre un ulteriore livello di sicurezza.

1. Usare il comando [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) seguente per creare un'Key Vault predefinita.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Seguire [queste istruzioni per concedere all'app l'accesso](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) all'insieme di credenziali delle chiavi:

1. Usare il comando [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) seguente per aggiungere l'URL esterno come segreto nell'insieme di credenziali delle chiavi:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Usare il comando seguente per creare l'impostazione dell'applicazione con il valore come Key Vault [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) `WEBSITE_RUN_FROM_PACKAGE` riferimento all'URL esterno:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`L'oggetto sarà nell'output del comando `az keyvault secret set` precedente.

L'aggiornamento di questa impostazione dell'applicazione causa il riavvio dell'app Web. Dopo il riavvio dell'app, passare ad essa per assicurarsi che sia stata avviata correttamente usando il Key Vault riferimento.

## <a name="how-to-rotate-the-access-token"></a>Come ruotare il token di accesso

È consigliabile ruotare periodicamente la chiave di firma di accesso condiviso dell'account di archiviazione. Per assicurarsi che l'app Web non eserciti inavvertitamente l'accesso libero, è necessario aggiornare anche l'URL di firma di accesso condiviso in Key Vault.

1. Ruotare la chiave di firma di accesso condiviso passando all'account di archiviazione nel portale di Azure. In **Impostazioni Chiavi** di  >  **accesso** fare clic sull'icona per ruotare la chiave di firma di accesso condiviso.

1. Copiare il nuovo URL di firma di accesso condiviso e usare il comando seguente per impostare l'URL di firma di accesso condiviso aggiornato nell'insieme di credenziali delle chiavi:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Aggiornare il riferimento all'insieme di credenziali delle chiavi nell'impostazione dell'applicazione alla nuova versione del segreto:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    sarà `<secret-version>` nell'output del comando `az keyvault secret set` precedente.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Come revocare l'accesso ai dati dell'app Web

Esistono due metodi per revocare l'accesso dell'app Web all'account di archiviazione. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Ruotare la chiave di firma di accesso condiviso per Archiviazione di Azure account

Se la chiave di firma di accesso condiviso per l'account di archiviazione viene ruotata, l'app Web non avrà più accesso all'account di archiviazione, ma continuerà a essere eseguita con l'ultima versione scaricata del file del pacchetto. Riavviare l'app Web per cancellare l'ultima versione scaricata.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Rimuovere l'accesso dell'app Web a Key Vault

È possibile revocare l'accesso dell'app Web ai dati del sito disabilitando l'accesso dell'app Web Key Vault. A tale scopo, rimuovere i criteri di accesso per l'identità dell'app Web. Si tratta della stessa identità creata in precedenza durante la configurazione dei riferimenti all'insieme di credenziali delle chiavi.

## <a name="summary"></a>Riepilogo

I file dell'applicazione sono ora crittografati quando sono in pausa nell'account di archiviazione. All'avvio, l'app Web recupera l'URL di firma di accesso condiviso dall'insieme di credenziali delle chiavi. Infine, l'app Web carica i file dell'applicazione dall'account di archiviazione. 

Se è necessario revocare l'accesso dell'app Web all'account di archiviazione, è possibile revocare l'accesso all'insieme di credenziali delle chiavi o ruotare le chiavi dell'account di archiviazione, invalidando così l'URL di firma di accesso condiviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Sono disponibili costi aggiuntivi per l'esecuzione dell'app Web dal pacchetto di distribuzione?

Solo il costo associato all'account Archiviazione di Azure ed eventuali addebiti in uscita applicabili.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>In che modo l'esecuzione dal pacchetto di distribuzione influisce sull'app Web?

- L'esecuzione dell'app dal pacchetto di distribuzione `wwwroot/` rende di sola lettura. L'app riceve un errore quando tenta di scrivere in questa directory.
- I formati TAR e GZIP non sono supportati.
- Questa funzionalità non è compatibile con la cache locale.

## <a name="next-steps"></a>Passaggi successivi

- [Key Vault per il servizio app](app-service-key-vault-references.md)
- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)