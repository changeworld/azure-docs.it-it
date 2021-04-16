---
title: Creare certificati usando lo Microsoft Azure strumento Stack Hub Readiness Checker | Microsoft Docs
description: Viene descritto come creare richieste di certificato e quindi ottenere e installare certificati nel dispositivo GPU Azure Stack Edge Pro usando lo strumento hub di Azure Stack Readiness Checker.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 8316dd0abfa437d4bf88e8268dfe034344c6614c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389333"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Creare certificati per l'Azure Stack Edge Pro usando lo hub di Azure Stack Readiness Checker 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come creare certificati per l'Azure Stack Edge Pro usando lo strumento hub di Azure Stack Readiness Checker. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Uso dello hub di Azure Stack Readiness Checker

Usare lo strumento hub di Azure Stack Readiness Checker per creare richieste di firma del certificato (CSR) per una distribuzione Azure Stack Edge Pro dispositivo. È possibile creare queste richieste dopo aver posto un ordine per il Azure Stack Edge Pro e attendere l'arrivo del dispositivo.

> [!NOTE]
> Usare questo strumento solo a scopo di test o sviluppo e non per i dispositivi di produzione. 

È possibile usare lo hub di Azure Stack Readiness Checker (AzsReadinessChecker) per richiedere i certificati seguenti:

- Azure Resource Manager certificato
- Certificato dell'interfaccia utente locale
- Certificato del nodo
- Certificato BLOB
- Certificato VPN


## <a name="prerequisites"></a>Prerequisiti

Per creare csr per la Azure Stack Edge Pro dei dispositivi, assicurarsi che: 

- Si ha un client che esegue Windows 10 o Windows Server 2016 o versione successiva. 
- È stato scaricato lo strumento Microsoft Azure stack hub Readiness Checker [dal](https://aka.ms/AzsReadinessChecker) PowerShell Gallery in questo sistema.
- Per i certificati sono disponibili le informazioni seguenti:
  - Nome del dispositivo
  - Numero di serie del nodo
  - Nome di dominio completo esterno (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generare richieste di firma del certificato

Usare questa procedura per preparare i certificati Azure Stack Edge Pro dispositivo:

1. Eseguire PowerShell come amministratore (5.1 o versione successiva).
2. Installare lo strumento hub di Azure Stack Readiness Checker. Al prompt di PowerShell digitare: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Per ottenere la versione installata, digitare:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Creare una directory per tutti i certificati, se non ne è già presente uno. Digitare: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Per creare una richiesta di certificato, specificare le informazioni seguenti. Se si genera un certificato VPN, alcuni di questi input non si applicano.
    
    |Input |Descrizione  |
    |---------|---------|
    |`OutputRequestPath`|Percorso del file nel client locale in cui si desidera creare le richieste di certificato.        |
    |`DeviceName`|Nome del dispositivo nella **pagina** Dispositivi dell'interfaccia utente Web locale del dispositivo. <br> Questo campo non è obbligatorio per un certificato VPN.         |
    |`NodeSerialNumber`|Numero di serie del nodo del dispositivo nella **pagina Rete** nell'interfaccia utente Web locale del dispositivo. <br> Questo campo non è obbligatorio per un certificato VPN.       |
    |`ExternalFQDN`|Valore DNSDomain nella pagina **Dispositivi** nell'interfaccia utente Web locale del dispositivo.         |
    |`RequestType`|Il tipo di richiesta può essere per - certificati diversi per i vari endpoint o `MultipleCSR` - un singolo certificato per tutti gli `SingleCSR` endpoint. <br> Questo campo non è obbligatorio per un certificato VPN.     |

    Per tutti i certificati tranne il certificato VPN, digitare: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Se si crea un certificato VPN, digitare: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. I file di richiesta del certificato sono presenti nella directory specificata nel parametro OutputRequestPath precedente. Quando si `MultipleCSR` usa il parametro , verranno visualizzati i quattro file seguenti con l'estensione `.req` :

    
    |Nomi di file  |Tipo di richiesta di certificato  |
    |---------|---------|
    |A partire da `DeviceName`     |Richiesta di certificato dell'interfaccia utente Web locale      |
    |A partire da `NodeSerialNumber`     |Richiesta di certificato del nodo         |
    |A partire da `login`     |Azure Resource Manager di certificato dell'endpoint       |
    |A partire da `wildcard`     |Richiesta di certificato di archiviazione BLOB. Contiene un carattere jolly perché copre tutti gli account di archiviazione che è possibile creare nel dispositivo.          |
    |A partire da `AzureStackEdgeVPNCertificate`     |Richiesta di certificato client VPN.         |

    Verrà visualizzata anche una cartella INF. Contiene un file di informazioni management.<edge-devicename> in testo non crittografato che spiega i dettagli del certificato.  


6. Inviare questi file all'autorità di certificazione (interna o pubblica). Assicurarsi che la CA generi certificati, usando la richiesta generata, che soddisfino i requisiti Azure Stack Edge Pro certificati per i certificati [nodo,](azure-stack-edge-gpu-manage-certificates.md#node-certificates)i certificati [endpoint](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)e i certificati dell'interfaccia [utente locali.](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)

## <a name="prepare-certificates-for-deployment"></a>Preparare i certificati per la distribuzione

I file di certificato che si ottengono dall'autorità di certificazione (CA) devono essere importati ed esportati con proprietà che soddisfano i requisiti del certificato del Azure Stack Edge Pro dispositivo. Completare i passaggi seguenti nello stesso sistema in cui sono stati generati le richieste di firma del certificato.

- Per importare i certificati, seguire la procedura descritta in [Importare](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)certificati nei client che accedono Azure Stack Edge Pro dispositivo .

- Per esportare i certificati, seguire la procedura descritta in [Esportare](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)certificati dal client che accede Azure Stack Edge Pro dispositivo .


## <a name="validate-certificates"></a>Convalidare i certificati

Prima di tutto, verrà generata una struttura di cartelle appropriata e i certificati verranno posizionati nelle cartelle corrispondenti. Solo a questo punto si convaliderà i certificati usando lo strumento .

1. Eseguire PowerShell come amministratore.

2. Per generare la struttura di cartelle appropriata, al prompt digitare:

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Convertire la password PFX in una stringa sicura. Digitare:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Convalidare quindi i certificati. Digitare:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
