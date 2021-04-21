---
title: Domande frequenti - Importare Azure Key Vault certificato
description: Risposte alle domande frequenti sull'importazione di Azure Key Vault certificati.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 39fe4e77d701f8e9311ea343c88eb3b905496680
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749241"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Domande frequenti sull Azure Key Vault importazione di certificati

Questo articolo risponde alle domande frequenti sull'importazione Azure Key Vault certificati.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Come è possibile importare un certificato in Azure Key Vault?

Per un'operazione di importazione del certificato, Azure Key Vault due formati di file di certificato: PEM e PFX. Anche se sono presenti file PEM con solo la parte pubblica, Key Vault richiede e accetta solo un file PEM o PFX con una chiave privata. Per altre informazioni, vedere [Importare un certificato in Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Dopo aver importato un certificato protetto da password Key Vault e quindi scaricato, perché non è possibile visualizzare la password associata?
     
Dopo l'importazione e la protezione di un certificato Key Vault, la password associata non viene salvata. La password è necessaria una sola volta durante l'operazione di importazione. Si tratta di un'operazione di progettazione, ma è sempre possibile ottenere il certificato come segreto e convertirlo da Base64 a PFX aggiungendo la password [tramite](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)Azure PowerShell .

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Come è possibile risolvere un errore di "parametro non valido"? Quali sono i formati di certificato supportati per l'importazione Key Vault?

Quando si importa un certificato, è necessario assicurarsi che la chiave sia inclusa nel file. Se si dispone di una chiave privata archiviata separatamente in un formato diverso, è necessario combinare la chiave con il certificato. Alcune autorità di certificazione forniscono certificati in altri formati. Pertanto, prima di importare il certificato, assicurarsi che sia in formato di file PEM o PFX e che la chiave usi la crittografia Rivest-Shamir-Adleman (RSA) o ECC (elliptic-curve cryptography). 

Per altre informazioni, vedere Requisiti [dei certificati e](./certificate-scenarios.md#formats-of-import-we-support) requisiti delle chiavi del [certificato](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>È possibile importare un certificato usando un modello di Arm?

No, non è possibile eseguire operazioni sui certificati usando un modello di Azure Resource Manager (ARM). Una soluzione alternativa consigliata consiste nell'usare i metodi di importazione dei certificati nell'API di Azure, nell'interfaccia della riga di comando di Azure o in PowerShell. Se si dispone di un certificato esistente, è possibile importarlo come segreto.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Quando si importa un certificato tramite il portale di Azure, viene visualizzato l'errore "Si è verificato un errore". Come è possibile analizzare ulteriormente il problema?
     
Per visualizzare un errore più descrittivo, importare il file del certificato usando l'interfaccia della riga [di comando di Azure](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) o [PowerShell.](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate)

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Come è possibile risolvere il messaggio "Tipo di errore: Accesso negato o utente non autorizzato all'importazione del certificato"?
    
Per l'operazione di importazione è necessario concedere all'utente le autorizzazioni per importare il certificato in base ai criteri di accesso. A tale scopo, passare all'insieme di credenziali delle chiavi, selezionare Criteri di accesso Aggiungi criteri di accesso Selezionare l'entità delle autorizzazioni certificato, cercare l'utente e quindi aggiungere l'indirizzo di posta  >    >    >  elettronica dell'utente. 

Per altre informazioni sui criteri di accesso correlati ai certificati, vedere [Informazioni sui Azure Key Vault certificati.](./about-certificates.md#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Come è possibile risolvere "Tipo di errore: Conflitto durante la creazione di un certificato"?
    
Ogni nome di certificato deve essere univoco. Un certificato con lo stesso nome potrebbe essere in uno stato di eliminazione soft. Inoltre, in base alla composizione di un [certificato,](./about-certificates.md#composition-of-a-certificate)quando viene creato un nuovo certificato, crea un segreto indirizzabile con lo stesso nome, quindi se nell'insieme di credenziali delle chiavi è presente un'altra chiave o un altro segreto con lo stesso nome di quello che si sta tentando di specificare per il certificato, la creazione del certificato avrà esito negativo e sarà necessario rimuovere la chiave o il segreto o usare un nome diverso per il certificato. 

Per altre informazioni, vedere [Operazione Get Deleted Certificate](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Perché viene visualizzato il messaggio "Tipo di errore: lunghezza char troppo lunga"?
Questo errore può essere causato da uno dei due motivi seguenti:    
* Il nome soggetto del certificato è limitato a 200 caratteri.
* La password del certificato è limitata a 200 caratteri.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Errore "Il contenuto del certificato PEM X.509 specificato è in un formato imprevisto. Verificare che il certificato sia in formato PEM valido."
Verificare che il contenuto del file PEM usi separatori di riga in stile UNIX `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>È possibile importare un certificato scaduto Azure Key Vault?
    
No, i certificati PFX scaduti non possono essere importati in Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Come è possibile convertire il certificato nel formato corretto?

È possibile chiedere all'autorità di certificazione di fornire il certificato nel formato richiesto. Sono disponibili anche strumenti di terze parti che consentono di convertire il certificato nel formato corretto.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>È possibile importare certificati da CA non partner?
Sì, è possibile importare certificati da qualsiasi AUTORITÀ di certificazione, ma l'insieme di credenziali delle chiavi non sarà in grado di rinnovarli automaticamente. È possibile impostare promemoria per ricevere una notifica sulla scadenza del certificato.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Se si importa un certificato da una CA partner, la funzionalità di aggiornamento automatico continuerà a funzionare?
Sì. Dopo aver caricato il certificato, assicurarsi di specificare la registrazione automatica nei criteri di rilascio del certificato. Le impostazioni rimarranno effettive fino al rilascio del ciclo o della versione del certificato successivo.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Perché non è possibile visualizzare il certificato del servizio app importato in Key Vault? 
Se il certificato è stato importato correttamente, sarà possibile confermarlo nel **riquadro** Segreti.


## <a name="next-steps"></a>Passaggi successivi

- [Azure Key Vault certificati](./about-certificates.md)
