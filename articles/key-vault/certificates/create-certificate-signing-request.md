---
title: Creazione e unione di una richiesta CSR in Azure Key Vault
description: Informazioni su come creare e unire una richiesta CSR in Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752139"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Creare e unire una richiesta CSR in Key Vault

Azure Key Vault supporta l'archiviazione di certificati digitali emessi da un'autorità di certificazione (CA). Supporta la creazione di una richiesta di firma del certificato (CSR) con una coppia di chiavi pubblica/privata. La richiesta CSR può essere firmata da qualsiasi CA (una CA aziendale interna oppure una pubblica esterna). Una richiesta CSR è un messaggio inviato a una CA allo scopo di richiedere un certificato digitale.

Per informazioni più generali sui certificati, vedere [Certificati di Azure Key Vault](./about-certificates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Aggiungere in Key Vault i certificati emessi da CA partner

Key Vault collabora con le autorità di certificazione seguenti per semplificare la creazione di certificati.

|Provider|Tipo di certificato|Eseguire la configurazione  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault offre certificati SSL OV o EV con DigiCert| [Guida all'integrazione](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault offre certificati SSL OV o EV con GlobalSign| [Guida all'integrazione](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Aggiungere in Key Vault i certificati emessi da CA non partner

Seguire questi passaggi per aggiungere un certificato emesso da CA che non sono partner di Key Vault. Ad esempio, GoDaddy non è una CA considerata attendibile in Key Vault.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'insieme di credenziali delle chiavi in cui aggiungere il certificato.
1. Nella pagina delle proprietà selezionare **Certificati**.
1. Selezionare la scheda **Genera/Importa**.
1. Nella schermata che consente di **creare un certificato** scegliere i seguenti valori:
    - **Metodo di creazione del certificato**: Genera.
    - **Nome del certificato**: ContosoManualCSRCertificate.
    - **Tipo di Autorità di certificazione:** certificato emesso da una CA non integrata.
    - **Soggetto**: `"CN=www.contosoHRApp.com"`.
     > [!NOTE]
     > Se si usa un nome distinto relativo il cui valore contiene una virgola (,), racchiudere tra virgolette doppie il valore che contiene il carattere speciale. 
     >
     > Voce di esempio per **Soggetto**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > In questo esempio il nome distinto relativo `OU` contiene un valore con una virgola nel nome. L'output risultante per `OU` è **Docs, Contoso**.
1. Selezionare gli altri valori desiderati, quindi selezionare **Crea** per aggiungere il certificato all'elenco **Certificati**.

    ![Screenshot delle proprietà del certificato](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Nell'elenco **Certificati** selezionare il nuovo certificato. Lo stato corrente del certificato sarà **disabilitato** perché non è ancora stato emesso dall'autorità di certificazione.
1. Nella scheda **Operazione relativa al certificato** selezionare **Scarica file CSR**.

   ![Screenshot con il pulsante Scarica file CSR evidenziato.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Chiedere alla CA di firmare la richiesta CSR (con estensione csr).
1. Dopo la firma della richiesta, selezionare **Unisci la richiesta firmata** nella scheda **Operazione relativa al certificato** per aggiungere il certificato firmato in Key Vault.

La richiesta di certificato è stata ora unita correttamente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Creare criteri dei certificati. Poiché la CA scelta in questo scenario non è partner, **IssuerName** è impostato su **Unknown** e Key Vault non registra né rinnova il certificato.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Se si usa un nome distinto relativo il cui valore contiene una virgola (,), usare le virgolette singole per il valore o il set di valori completo e racchiudere tra virgolette doppie il valore che contiene il carattere speciale. 
     >
     >Voce di esempio per **SubjectName**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. In questo esempio, il valore `OU` viene letto come **Docs, Contoso**. Questo formato funziona per tutti i valori che contengono una virgola.
     > 
     > In questo esempio il nome distinto relativo `OU` contiene un valore con una virgola nel nome. L'output risultante per `OU` è **Docs, Contoso**.

1. Creare la richiesta CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Chiedere alla CA di firmare la richiesta CSR. `$csr.CertificateSigningRequest` corrisponde alla richiesta CSR con codifica di base per il certificato. È possibile eseguire il dump di questo BLOB nel sito Web della richiesta di certificato dell'autorità di certificazione. Questo passaggio varia da una CA all'altra. Per informazioni su come eseguirlo, vedere le linee guida della CA in uso. È anche possibile usare strumenti come certreq o openssl per ottenere la richiesta CSR firmata e completare il processo di generazione di un certificato.

1. Unire la richiesta firmata in Key Vault. Una volta firmata, è possibile unire la richiesta di certificato con la coppia di chiavi pubblica/privata creata in Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

La richiesta di certificato è stata ora unita correttamente.

---

## <a name="add-more-information-to-the-csr"></a>Aggiungere altre informazioni alla richiesta CSR

Se si vogliono aggiungere altre informazioni durante la creazione della CSR, definirle in **SubjectName**. Potrebbe essere necessario aggiungere informazioni come:
- Paese
- Città/località
- Provincia
- Organization
- Unità organizzativa

Esempio

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Se si richiede un certificato di convalida del dominio con informazioni aggiuntive, la CA potrebbe rifiutare la richiesta se non è in grado di convalidare tutte le informazioni al suo interno. Le informazioni aggiuntive potrebbero essere più appropriate se si richiede un certificato di convalida dell'organizzazione.

## <a name="faqs"></a>Domande frequenti

- Come è possibile monitorare o gestire le richieste CSR?

     Vedere [Monitorare e gestire la creazione di certificati](./create-certificate-scenarios.md).

- Perché viene visualizzato il messaggio **Tipo di errore: 'La chiave pubblica del certificato di entità finale nel contenuto del certificato X. 509 specificato non corrisponde alla parte pubblica della chiave privata specificata. Assicurarsi che il certificato sia valido'** ?

     Questo errore si verifica se la richiesta CSR firmata non viene unita alla stessa richiesta CSR avviata. Ogni nuova richiesta CSR creata include una chiave privata, che deve corrispondere quando si unisce la richiesta firmata.

- Con l'unione di una richiesta CSR, viene unita l'intera catena?

     Sì, verrà unita l'intera catena, purché l'utente abbia recuperato un file p7b da unire.

- Cosa succede se il certificato emesso risulta disabilitato nel portale di Azure?

     Per esaminare il messaggio di errore relativo al certificato, visualizzare la scheda **Operazione relativa al certificato**.

- Perché viene visualizzato un messaggio analogo a **Tipo di errore: 'Il nome soggetto specificato non è un nome X500 valido'** ?

     Questo errore può verificarsi se **SubjectName** include caratteri speciali. Vedere le note nel portale di Azure e le istruzioni di PowerShell.

---

## <a name="next-steps"></a>Passaggi successivi

- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
- [Informazioni di riferimento sull'API REST di Azure Key Vault](/rest/api/keyvault)
- [Insiemi di credenziali delle chiavi - Creare o aggiornare](/rest/api/keyvault/vaults/createorupdate)
- [Insiemi di credenziali - Aggiornare i criteri di accesso](/rest/api/keyvault/vaults/updateaccesspolicy)