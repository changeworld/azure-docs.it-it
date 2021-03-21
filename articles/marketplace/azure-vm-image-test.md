---
title: Testare un'immagine di macchina virtuale di Azure per Azure Marketplace
description: Informazioni su come testare e inviare un'offerta di macchina virtuale di Azure in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 9ffba221625c57332cd695125651d92adc11cf60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200369"
---
# <a name="test-a-virtual-machine-image"></a>Testare un'immagine di macchina virtuale

In questo argomento vengono illustrati i passaggi per testare un'immagine di macchina virtuale (VM) per la distribuzione in Azure Marketplace.

## <a name="deploy-an-azure-vm"></a>Distribuire una macchina virtuale di Azure

Per distribuire una VM dall'immagine della raccolta di immagini condivise:

1. Passare alla versione dell'immagine della raccolta immagini condivisa
1. Fare clic su Crea macchina virtuale
1. Specificare un nome per la macchina virtuale e selezionare le dimensioni della VM
1. Fare clic su Verifica + crea. Una volta superata la convalida, fare clic su Crea.

> [!NOTE]
> Se è necessario creare una macchina virtuale da un file VHD, seguire le istruzioni riportate negli articoli seguenti, [preparare un modello di Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#prepare-an-azure-resource-manager-template) o [distribuire una VM di Azure con PowerShell](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#deploy-an-azure-vm-using-powershell).

Questo articolo descrive come testare e inviare un'immagine di macchina virtuale nel marketplace commerciale per verificare che soddisfi i requisiti di pubblicazione di Azure Marketplace più recenti.

Prima di inviare l'offerta di macchina virtuale, completare questi passaggi:

- Distribuire una macchina virtuale di Azure usando l'immagine generalizzata; vedere [creare una macchina virtuale usando una base approvata](azure-vm-create-using-approved-base.md) o [creare una macchina virtuale usando un'immagine personalizzata](azure-vm-create-using-own-image.md).
- Eseguire convalide.

## <a name="run-validations"></a>Eseguire convalide

Esistono due modi per eseguire le convalide nell'immagine distribuita.

### <a name="use-certification-test-tool-for-azure-certified"></a>Usare lo strumento di test della certificazione per Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Scaricare ed eseguire lo strumento di test di certificazione

Lo strumento di test di certificazione per Azure Certified viene eseguito in un computer Windows locale, ma esegue il test di una macchina virtuale Linux o Windows basata su Azure. Lo strumento verifica che l'immagine di macchina virtuale dell'utente possa essere usata con Microsoft Azure e che siano stati soddisfatti i requisiti e le linee guida per la preparazione del disco rigido virtuale.

1. Scaricare e installare lo [strumento di test di certificazione per Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Aprire lo strumento di certificazione, quindi selezionare **Avvia nuovo test**.
3. Nella schermata Test Information (Informazioni test) immettere un valore in **Test Name** (Nome test) per l'esecuzione del test.
4. Selezionare la piattaforma per la VM, ovvero **Windows Server** o **Linux**. La piattaforma scelta determina le opzioni rimanenti.
5. Se la macchina virtuale usa questo servizio di database, selezionare la casella di controllo **Test for Azure SQL Database** (Test per il database SQL di Azure).

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale

1. Selezionare la modalità Autenticazione SSH: Autenticazione della password o Autenticazione del file di chiave.
2. Se si usa l'autenticazione basata su password, immettere i valori per il **nome DNS della VM**, il **nome utente** e la **password**. Facoltativamente, è possibile modificare il valore predefinito per la Porta SSH.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Mostra la selezione delle informazioni di test della macchina virtuale.":::

3. Se si usa l'autenticazione basata su file di chiavi, immettere i valori nei campi VM DNS Name (Nome DNS macchina virtuale), User name (Nome utente) e Private key (Chiave privata). È anche possibile specificare un valore per Passphrase o modificare il numero predefinito per il campo Porta SSH.
4. Immettere il nome DNS completo della macchina virtuale, ad esempio MyVMName.Cloudapp.net.
5. Immettere **nome utente** e **password**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Mostra la selezione del nome utente e della password della macchina virtuale.":::

6. Selezionare **Avanti**.

#### <a name="run-a-certification-test"></a>Eseguire un test di certificazione

Dopo avere specificato i valori dei parametri per l'immagine di macchina virtuale nello strumento di certificazione, selezionare Test connessione per creare una connessione valida alla VM. Dopo che la connessone è verificata, selezionare **Next** (Avanti) per avviare il test. Al termine del test, i risultati vengono visualizzati in una tabella. La colonna Stato mostra (Pass/Fail/Warning) per ogni test. Se uno dei test non riesce, l'immagine non viene certificata. In questo caso, esaminare i requisiti e i messaggi di errore, apportare le modifiche indicate ed eseguire nuovamente il test.

Al termine del test automatizzato, fornire informazioni aggiuntive sull'immagine della macchina virtuale nelle due schede della schermata Questionario, Valutazione generale e Personalizzazione kernel, quindi selezionare Avanti.

L'ultima schermata consente di fornire altre informazioni, ad esempio le informazioni di accesso SSH per un'immagine di macchina virtuale Linux e una spiegazione per eventuali valutazioni non riuscite se si cercano eccezioni.

Selezionare infine Genera report per scaricare i risultati del test e i file di log per i casi di test eseguiti, oltre alle risposte al questionario.
> [!Note]
> Pochi autori hanno scenari in cui le macchine virtuali devono essere bloccate in quanto hanno un software, ad esempio i firewall installati nella macchina virtuale. In questo caso, scaricare lo [strumento di test certificato](https://aka.ms/AzureCertificationTestTool) e inviare il report al [supporto tecnico](https://aka.ms/marketplacepublishersupport)del partner.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Come usare PowerShell per utilizzare l'API Self-Test

### <a name="on-linux-os"></a>Nel sistema operativo Linux

Chiamare l'API in PowerShell:

1. Usare il comando Invoke-WebRequest per chiamare l'API.
2. Il metodo è POST e il tipo di contenuto è JSON, come illustrato nella schermata e nell'esempio di codice seguenti.
3. Specificare i parametri del corpo in formato JSON.

Nell'esempio seguente viene illustrata una chiamata a PowerShell per l'API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Di seguito è riportato un esempio di chiamata dell'API in PowerShell:

[![Esempio di schermata per chiamare l'API in PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Usando l'esempio precedente, è possibile recuperare il file JSON e analizzarlo in modo da ottenere i dettagli seguenti:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Questa schermata di esempio, che mostra `$res.Content` i dettagli dei risultati del test in formato JSON:

[![Esempio di schermata per chiamare l'API in PowerShell con i dettagli dei risultati del test.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Di seguito è riportato un esempio di risultati dei test JSON visualizzati in un visualizzatore JSON online, ad esempio l' [abbellimento del codice](https://codebeautify.org/jsonviewer) o il [Visualizzatore JSON](https://jsonformatter.org/json-viewer).

![Altri risultati dei test in un visualizzatore JSON online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Nel sistema operativo Windows

Chiamare l'API in PowerShell:

1. Usare il comando Invoke-WebRequest per chiamare l'API.
2. Il metodo è post e il tipo di contenuto è JSON, come illustrato nell'esempio di codice e nella schermata di esempio seguenti.
3. Creare i parametri del corpo in formato JSON.

Questo esempio di codice mostra una chiamata a PowerShell per l'API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

In queste schermate di esempio viene illustrato l'esempio per chiamare l'API in PowerShell:

**Con la chiave SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Chiamata dell'API in PowerShell con una chiave SSH.":::

**Con password**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Chiamata dell'API in PowerShell con una password.":::

<br>Usando l'esempio precedente, è possibile recuperare il file JSON e analizzarlo in modo da ottenere i dettagli seguenti:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Questa schermata, che mostra `$res.Content` , Mostra i dettagli dei risultati del test in formato JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Dettagli dei risultati del test in formato JSON.":::

<br>Di seguito è riportato un esempio di risultati dei test visualizzati in un visualizzatore JSON online, ad esempio l' [abbellimento del codice](https://codebeautify.org/jsonviewer) o il [Visualizzatore JSON](https://jsonformatter.org/json-viewer).

![Risultati dei test in un visualizzatore JSON online.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Come usare CURL per usare l'API Self-Test in un sistema operativo Linux

In questo esempio curl verrà usato per effettuare una chiamata POST API a Azure Active Directory e alla VM Self-Host.

1. Richiedere un token di Azure AD per l'autenticazione alla macchina virtuale Self-host

   Assicurarsi che i valori corretti vengano sostituiti nella richiesta curl.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Di seguito è riportato un esempio della risposta dalla richiesta:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Inviare una richiesta per la macchina virtuale con test automatico

   Verificare che i parametri e il token di porta siano sostituiti con i valori corretti.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Risposta di esempio dalla chiamata all'API della macchina virtuale di test automatico

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

- Accedere al [Centro per i partner](https://partner.microsoft.com/).
