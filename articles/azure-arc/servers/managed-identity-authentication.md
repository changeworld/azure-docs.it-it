---
title: Eseguire l'autenticazione nelle risorse di Azure con i server abilitati per Arc
description: Questo articolo descrive il supporto del servizio metadati dell'istanza di Azure per i server abilitati per Arc e come è possibile eseguire l'autenticazione nelle risorse di Azure e in locale usando un segreto.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939159"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Eseguire l'autenticazione nelle risorse di Azure con i server abilitati per Arc

Le applicazioni o i processi in esecuzione direttamente su un server abilitato per Azure Arc possono sfruttare le identità gestite per accedere ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory. Un'applicazione può ottenere un [token di accesso](../../active-directory/develop/developer-glossary.md#access-token) che rappresenta la propria identità, che è assegnata dal sistema per i server abilitati per Arc e lo usa come token di connessione per autenticarsi a un altro servizio.

Per una descrizione dettagliata delle identità gestite, vedere la documentazione relativa alla [Panoramica dell'identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) , nonché la distinzione tra identità assegnate dal sistema e identità assegnate dall'utente.

Questo articolo illustra come un server può usare un'identità gestita assegnata dal sistema per accedere ad Azure [Key Vault](../../key-vault/general/overview.md). Fungendo da bootstrap, Key Vault consente quindi all'applicazione client di usare un segreto per accedere alle risorse non protette da Azure Active Directory (AD). Ad esempio, i certificati TLS/SSL usati dai server Web IIS possono essere archiviati in Azure Key Vault e distribuire in modo sicuro i certificati ai server Windows o Linux all'esterno di Azure.

## <a name="security-overview"></a>Panoramica della sicurezza

Durante il caricamento del server nei server abilitati per Azure Arc vengono eseguite diverse azioni per configurare usando un'identità gestita, analogamente a quanto avviene per una macchina virtuale di Azure:

- Azure Resource Manager riceve una richiesta di abilitazione dell'identità gestita assegnata dal sistema sul server abilitato per l'arco.

- Azure Resource Manager crea un'entità servizio in Azure AD per l'identità del server. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile dalla sottoscrizione.

- Azure Resource Manager configura l'identità sul server aggiornando l'endpoint di identità del servizio metadati dell'istanza di Azure (IMDS) per [Windows](../../virtual-machines/windows/instance-metadata-service.md) o [Linux](../../virtual-machines/linux/instance-metadata-service.md) con l'ID client e il certificato dell'entità servizio. L'endpoint è un endpoint REST accessibile solo dall'interno del server usando un indirizzo IP noto e non instradabile. Questo servizio fornisce un sottoinsieme di informazioni sui metadati sul server abilitato per l'arco che consente di gestirlo e configurarlo.

L'ambiente di un server abilitato per l'identità gestita verrà configurato con le variabili seguenti in un server abilitato per Windows Arc:

- **IMDS_ENDPOINT**: indirizzo IP dell'endpoint IMDS `http://localhost:40342` per i server abilitati per Arc.

- **IDENTITY_ENDPOINT**: l'endpoint localhost corrispondente all'identità gestita del servizio `http://localhost:40342/metadata/identity/oauth2/token` .

Il codice in esecuzione nel server può richiedere un token dall'endpoint del servizio metadati dell'istanza di Azure, accessibile solo dall'interno del server.

La variabile di ambiente di sistema **IDENTITY_ENDPOINT** viene utilizzata per individuare l'endpoint di identità dalle applicazioni. Le applicazioni devono tentare di recuperare **IDENTITY_ENDPOINT** e **IMDS_ENDPOINT** i valori e di usarli. Le applicazioni con un livello di accesso possono eseguire richieste agli endpoint. Le risposte ai metadati vengono gestite normalmente e fornite a qualsiasi processo nel computer. Tuttavia, quando viene effettuata una richiesta che espone un token, è necessario che il client fornisca un segreto per attestare che è in grado di accedere ai dati disponibili solo per gli utenti con privilegi elevati.

## <a name="prerequisites"></a>Prerequisiti

- Conoscenza delle identità gestite.
- Un server connesso e registrato con i server abilitati per Arc.
- L'utente è membro del [gruppo proprietario](../../role-based-access-control/built-in-roles.md#owner)* * nella sottoscrizione o nel gruppo di risorse per eseguire i passaggi necessari per la creazione delle risorse e la gestione dei ruoli.
- Azure Key Vault per archiviare e recuperare le credenziali. e assegnare l'accesso all'identità di Azure Arc a Vault.

    - Se non si dispone di un Key Vault creato, vedere [creare Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Per configurare l'accesso in base all'identità gestita usata dal server, vedere [concedere l'accesso per Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) o [concedere l'accesso per Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Per il passaggio numero 5 si immetterà il nome del server abilitato per l'arco. Per completare questa operazione usando PowerShell, vedere [assegnare un criterio di accesso usando PowerShell](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Acquisizione di un token di accesso tramite l'API REST

Il metodo per ottenere e usare un'identità gestita assegnata dal sistema per l'autenticazione con le risorse di Azure è simile al modo in cui viene eseguita con una macchina virtuale di Azure.

Per un server Windows abilitato per Arc, usando PowerShell, richiamare la richiesta Web per ottenere il token dall'host locale nella porta specifica. Specificare la richiesta utilizzando l'indirizzo IP o la variabile di ambiente **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

La risposta seguente è un esempio restituito:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Il recupero del token di accesso tramite PowerShell è riuscito.":::

Per un server Linux abilitato per Arc, usando bash, viene richiamata la richiesta Web per ottenere il token dall'host locale nella porta specifica. Specificare la richiesta seguente utilizzando l'indirizzo IP o la variabile di ambiente **IDENTITY_ENDPOINT**. Per completare questo passaggio, è necessario un client SSH.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

La risposta seguente è un esempio restituito:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Il recupero del token di accesso con bash è riuscito.":::

La risposta include il token di accesso necessario per accedere a qualsiasi risorsa in Azure. Per completare la configurazione per l'autenticazione Azure Key Vault, vedere [accedere Key Vault con Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) o [accedere Key Vault con Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle Azure Key Vault, vedere [Panoramica di Key Vault](../../key-vault/general/overview.md).

- Informazioni su come assegnare un accesso a identità gestite a una risorsa [tramite PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) o tramite [l'interfaccia della](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)riga di comando di Azure.