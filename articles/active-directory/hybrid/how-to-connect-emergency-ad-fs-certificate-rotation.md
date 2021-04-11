---
title: Rotazione di emergenza dei certificati di AD FS | Microsoft Docs
description: Questo articolo illustra come revocare e aggiornare immediatamente i certificati AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613050"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Rotazione di emergenza dei certificati AD FS
Nel caso in cui sia necessario ruotare immediatamente il AD FS certificati, è possibile seguire la procedura descritta di seguito in questa sezione.

> [!IMPORTANT]
> Eseguendo i passaggi seguenti nell'ambiente AD FS i certificati precedenti vengono revocati immediatamente.  Poiché questa operazione viene eseguita immediatamente, il tempo normale normalmente consentito ai partner federativi di utilizzare il nuovo certificato viene passato. Potrebbe verificarsi un'interruzione del servizio come aggiornamento dei trust per l'uso dei nuovi certificati.  Questa operazione dovrebbe risolversi dopo che tutti i partner federativi dispongono dei nuovi certificati.

> [!NOTE]
> Microsoft consiglia di usare un modulo di protezione hardware (HSM) per proteggere e proteggere i certificati.
> Per ulteriori informazioni, vedere [modulo di protezione hardware](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) in procedure consigliate per la protezione di ad FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Determinare l'identificazione personale del certificato per la firma di token
Per revocare il certificato di firma del token precedente, che AD FS attualmente in uso, è necessario determinare l'identificazione personale del certificato token-sigining.  A tale scopo, attenersi alla procedura seguente:

 1. Connettersi al servizio online Microsoft `PS C:\>Connect-MsolService`
 2. Documentare le date di scadenza e l'identificazione personale del certificato per la firma di token cloud e locale.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Copiare l'identificazione personale.  Verrà usato in un secondo momento per rimuovere i certificati esistenti.

È anche possibile ottenere l'identificazione personale usando Gestione AD FS, passando a servizio/certificati, facendo clic con il pulsante destro del mouse sul certificato, selezionando Visualizza certificato e quindi selezionando Dettagli. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Determinare se AD FS rinnova automaticamente i certificati
Per impostazione predefinita, AD FS è configurato per generare automaticamente i certificati per la firma di token e i certificati di decrittografia token, sia in fase di configurazione iniziale, sia quando i certificati hanno quasi raggiunto la data di scadenza.

È possibile eseguire il comando di Windows PowerShell seguente: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

La proprietà AutoCertificateRollover descrive se AD FS è configurato per rinnovare automaticamente i certificati di firma del token e di decrittografia dei token.  Se AutoCertificateRollover è impostato su TRUE, seguire le istruzioni descritte di seguito in [generazione di un nuovo certificato autofirmato se AutoCertificateRollover è impostato su TRUE].  Se AutoCertificateRollover è impostato su FALSE, seguire le istruzioni descritte di seguito in [generazione manuale di nuovi certificati se AutoCertificateRollover è impostato su FALSE]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Generazione di un nuovo certificato autofirmato se AutoCertificateRollover è impostato su TRUE
In questa sezione verranno creati **due** certificati per la firma di token.  Il primo utilizzerà il flag **-urgente** , che sostituisce immediatamente il certificato primario corrente.  Il secondo verrà usato per il certificato secondario.  

>[!IMPORTANT]
>Il motivo per cui si stanno creando due certificati è dato dal fatto che Azure utilizza le informazioni relative al certificato precedente.  Creando un secondo, viene forzato Azure a rilasciare le informazioni sul certificato precedente e sostituirlo con informazioni sul secondo certificato.
>
>Se non si crea il secondo certificato e si aggiorna Azure con esso, potrebbe essere possibile che il vecchio certificato per la firma di token esegua l'autenticazione degli utenti.

Per generare i nuovi certificati per la firma di token, è possibile usare i passaggi seguenti.

 1. Assicurarsi di essere connessi al server AD FS primario.
 2. Aprire Windows PowerShell come amministratore. 
 3. Assicurarsi che AutoCertificateRollover sia impostato su true.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Per generare un nuovo certificato per la firma di token: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Verificare l'aggiornamento eseguendo il comando seguente: `Get-ADFSCertificate –CertificateType token-signing`
 6. Generare ora il secondo certificato per la firma di token: `Update-ADFSCertificate –CertificateType token-signing` .
 7. È possibile verificare l'aggiornamento eseguendo di nuovo il comando seguente: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Generazione manuale di nuovi certificati se AutoCertificateRollover è impostato su FALSE
Se non si utilizzano i certificati di decrittografia token autofirmati e i certificati di decrittografia token autofirmati, è necessario rinnovarli e configurarli manualmente.  Ciò comporta la creazione di due nuovi certificati per la firma di token e l'importazione.  Viene quindi innalzato di livello uno a primario, viene revocato il certificato precedente e viene configurato il secondo certificato come certificato secondario.

In primo luogo, è necessario ottenere due nuovi certificati dall'autorità di certificazione e importarli nell'archivio certificati personale del computer locale in ogni server federativo. Per istruzioni, vedere l'articolo relativo all' [importazione di un certificato](https://technet.microsoft.com/library/cc754489.aspx) .

>[!IMPORTANT]
>Il motivo per cui si stanno creando due certificati è dato dal fatto che Azure utilizza le informazioni relative al certificato precedente.  Creando un secondo, viene forzato Azure a rilasciare le informazioni sul certificato precedente e sostituirlo con informazioni sul secondo certificato.
>
>Se non si crea il secondo certificato e si aggiorna Azure con esso, potrebbe essere possibile che il vecchio certificato per la firma di token esegua l'autenticazione degli utenti.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Per configurare un nuovo certificato come certificato secondario
Quindi, è necessario configurare un certificato come certificato di firma o di decrittografia del token di AD FS secondario e quindi innalzarlo di livello al database primario

1. Assicurarsi di aver importato il certificato. Aprire la console di **gestione di ad FS** .
2. Espandere **Servizio**, quindi selezionare **Certificati**.
3. Nel riquadro azioni fare clic su **aggiungi Token-Signing certificato**.
4. Selezionare il nuovo certificato dall'elenco dei certificati visualizzati e quindi fare clic su OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Per alzare di livello il nuovo certificato da secondario a principale
Ora che il nuovo certificato è stato importato e configurato in AD FS, è necessario impostare come certificato primario.
1. Aprire la console di **gestione di ad FS** .
2. Espandere **Servizio**, quindi selezionare **Certificati**.
3. Fare clic sul certificato secondario per la firma di token.
4. Nel riquadro **Azioni** fare clic su **Imposta come principale**. Fare clic su Sì quando viene richiesta la conferma.
5. Una volta innalzato di nuovo il certificato come certificato primario, è necessario rimuovere il certificato precedente perché può comunque essere utilizzato. Vedere la sezione [rimuovere i certificati obsoleti](#remove-your-old-certificates) di seguito.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Per configurare il secondo certificato come certificato secondario
Ora che è stato aggiunto il primo certificato ed è stato reso primario e rimosso quello precedente, importare il secondo certificato.  Quindi, è necessario configurare il certificato come certificato di firma del token di AD FS secondario

1. Assicurarsi di aver importato il certificato. Aprire la console di **gestione di ad FS** .
2. Espandere **Servizio**, quindi selezionare **Certificati**.
3. Nel riquadro azioni fare clic su **aggiungi Token-Signing certificato**.
4. Selezionare il nuovo certificato dall'elenco dei certificati visualizzati e quindi fare clic su OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Aggiornare Azure AD con il nuovo certificato per la firma di token
Aprire il modulo di Microsoft Azure Active Directory per Windows PowerShell. In alternativa, aprire Windows PowerShell ed eseguire il comando `Import-Module msonline`

Connettersi a Azure AD eseguendo il comando seguente: `Connect-MsolService` , quindi immettere le credenziali di amministratore globale.

>[!Note]
> Se si eseguono questi comandi in un computer che non è il server federativo primario, immettere prima il comando seguente: `Set-MsolADFSContext –Computer <servername>` . Sostituire <servername> con il nome del server ad FS. Quando richiesto, immettere le credenziali di amministratore per il server AD FS.

Facoltativamente, verificare se è necessario un aggiornamento controllando le informazioni correnti sul certificato in Azure AD. A tale scopo, eseguire il comando seguente: `Get-MsolFederationProperty` . Immettere il nome del dominio federato quando richiesto.

Per aggiornare le informazioni sul certificato in Azure AD, eseguire il comando seguente: `Update-MsolFederatedDomain` e quindi immettere il nome del dominio quando richiesto.

>[!Note]
> Se viene visualizzato un errore durante l'esecuzione di questo comando, eseguire il comando seguente: `Update-MsolFederatedDomain –SupportMultipleDomain` , quindi immettere il nome di dominio quando richiesto.

## <a name="replace-ssl-certificates"></a>Sostituisci certificati SSL
Nel caso in cui sia necessario sostituire il certificato per la firma di token a causa di una compromissione, è inoltre necessario revocare e sostituire i certificati SSL per AD FS e i server WAP.  

La revoca dei certificati SSL deve essere eseguita presso l'autorità di certificazione (CA) che ha emesso il certificato.  Questi certificati vengono spesso emessi da provider di terze parti, ad esempio GoDaddy.  Per un esempio, vedere (revocare un certificato | Certificati SSL-GoDaddy Help US.  Per ulteriori informazioni [, vedere come funziona la revoca dei certificati](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

Una volta che il certificato SSL precedente è stato revocato e ne è stato emesso uno nuovo, è possibile sostituire i certificati SSL. Per ulteriori informazioni, vedere [sostituzione del certificato SSL per ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Rimuovere i certificati obsoleti
Dopo aver sostituito i vecchi certificati, è necessario rimuovere il vecchio certificato perché può comunque essere usato. . A tale scopo, attenersi alla procedura seguente:.  A questo scopo, eseguire la procedura seguente.

1. Assicurarsi di essere connessi al server AD FS primario.
2. Aprire Windows PowerShell come amministratore. 
4. Per rimuovere il vecchio certificato per la firma di token: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Aggiornamento dei partner federativi che possono utilizzare i metadati federativi
Se è stato rinnovato e configurato un nuovo certificato per la firma di token o per la decrittografia di token, è necessario assicurarsi che tutti i partner federativi (organizzazioni di risorse o partner dell'organizzazione account che sono rappresentati nella AD FS da relying party attendibilità e attendibilità del provider di attestazioni) abbiano selezionato i nuovi certificati.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Aggiornamento dei partner federativi che non possono utilizzare i metadati federativi
Se i partner federativi non possono utilizzare i metadati federativi, è necessario inviare manualmente la chiave pubblica del nuovo certificato di firma del token/decrittografia di token. Inviare la nuova chiave pubblica del certificato (file con estensione cer o. p7b se si desidera includere l'intera catena) a tutti i partner dell'organizzazione delle risorse o dell'organizzazione account (rappresentati nel AD FS da relying party trust e trust del provider di attestazioni). Chiedere ai partner di implementare modifiche sul lato per considerare attendibili i nuovi certificati.



## <a name="revoke-refresh-tokens-via-powershell"></a>Revocare i token di aggiornamento tramite PowerShell
È ora necessario revocare i token di aggiornamento per gli utenti che li hanno e forzarli a riaccedere e ottenere nuovi token.  In questo modo gli utenti vengono disconnessi dal telefono, le sessioni di webmail correnti, insieme ad altri elementi che usano token e token di aggiornamento.  Le informazioni sono disponibili [qui](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) ed è anche possibile fare riferimento a come [revocare l'accesso utente in Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Passaggi successivi

- [Gestione dei certificati SSL in AD FS e WAP in Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Ottenere e configurare i certificati per la firma di token e la decrittografia di token per AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Rinnovare i certificati di federazione per Microsoft 365 e Azure Active Directory](how-to-connect-fed-o365-certs.md)



















