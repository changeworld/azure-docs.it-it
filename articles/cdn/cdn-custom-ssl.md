---
title: 'Esercitazione: Configurare HTTPS in un dominio personalizzato della rete CDN di Azure'
description: In questa esercitazione viene illustrato come abilitare e disabilitare HTTPS nel dominio personalizzato dell'endpoint della rete CDN di Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c4ad270b989e0e212c1d362ae4bfafc91fe07f3e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943578"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Esercitazione: Configurare HTTPS in un dominio personalizzato della rete CDN di Azure

Questa esercitazione illustra come abilitare il protocollo HTTPS per un dominio personalizzato associato a un endpoint della rete CDN di Azure. 

Il protocollo HTTPS nel dominio personalizzato, ad esempio https: \/ /www.contoso.com, garantisce che i dati sensibili vengano recapitati in modo sicuro tramite TLS/SSL. Quando il Web browser è connesso tramite HTTPS, il browser convalida il certificato del sito Web. Il browser verifica che sia emesso da un'autorità di certificazione legittima. Questo processo offre sicurezza e protezione delle applicazioni Web da attacchi.

Per impostazione predefinita, la rete CDN di Azure supporta il protocollo HTTPS in un nome host di endpoint della rete CDN. Se si crea un endpoint CDN, ad esempio https:\//contoso.azureedge.net, il protocollo HTTPS è abilitato per impostazione predefinita.  

Di seguito sono riportati alcuni attributi chiave della funzionalità HTTPS personalizzato.

- Nessun costo aggiuntivo: non sono previsti costi per l'acquisizione o il rinnovo del certificato e nessun costo aggiuntivo per il traffico HTTPS. L'addebito è relativo solo ai GB in uscita dalla rete CDN.

- Abilitazione semplice: il provisioning è disponibile con un unico clic nel [portale di Azure](https://portal.azure.com). È possibile anche usare l'API REST o altri strumenti per sviluppatori per abilitare la funzionalità.

- Gestione completa dei certificati: 
    * tutta la fase di approvvigionamento e gestione di certificati viene gestita automaticamente. 
    * Il provisioning e il rinnovo dei certificati vengono automaticamente prima della scadenza.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Abilitare il protocollo HTTPS nel dominio personalizzato
> - Usare un certificato gestito dalla rete CDN 
> - Usare un certificato personale
> - Convalidare il dominio
> - Disabilitare il protocollo HTTPS nel dominio personalizzato

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Prima di poter completare i passaggi di questa esercitazione, è necessario creare un profilo di rete CDN e almeno un endpoint della rete CDN. Per altre informazioni, vedere [Avvio rapido: Creare un profilo e un endpoint della rete CDN di Azure](cdn-create-new-endpoint.md).

Associare un dominio personalizzato della rete CDN di Azure nell'endpoint della rete CDN. Per altre informazioni, vedere [Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> I certificati gestiti da rete CDN non sono disponibili per i domini radice o apex. Se il dominio personalizzato della rete CDN di Azure è un dominio radice o apex, è necessario usare un certificato personale. 
>

---

## <a name="tlsssl-certificates"></a>Certificati TLS/SSL

Per abilitare HTTPS in un dominio personalizzato della rete CDN di Azure, usare un certificato TLS/SSL. Si sceglie di usare un certificato gestito dalla rete CDN di Azure o usare il certificato.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opzione 1 (predefinita): abilitare HTTPS con un certificato gestito dalla rete CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

La rete CDN di Azure gestisce le attività di gestione dei certificati, ad esempio approvvigionamento e rinnovo. Dopo l'abilitazione della funzionalità, il processo viene avviato immediatamente. 

Se è già stato eseguito il mapping del dominio personalizzato all'endpoint della rete CDN, non è necessaria alcuna azione aggiuntiva. La rete CDN di Azure elaborerà i passaggi e completerà la richiesta automaticamente.

Se il dominio personalizzato è mappato altrove, usare la posta elettronica per convalidare la proprietà del dominio.

Per abilitare il protocollo HTTPS in un dominio personalizzato, seguire questa procedura:

1. Per trovare un certificato gestito dalla rete CDN di Azure, passare al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Profili CDN**. 

2. Scegliere il profilo:
    * **Rete CDN Standard di Azure offerta da Microsoft**
    * **Rete CDN Standard di Azure offerta da Akamai**
    * **Rete CDN Standard di Azure offerta da Verizon**
    * **Rete CDN Premium di Azure da Verizon**

3. Nell'elenco di endpoint della rete CDN selezionare l'endpoint contenente il dominio personalizzato.

    ![Elenco di endpoint](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Viene visualizzata la pagina **Endpoint**.

4. Nell'elenco di domini personalizzati selezionare il dominio personalizzato per cui si vuole abilitare il protocollo HTTPS.

    ![Screenshot che mostra la pagina Dominio personalizzato con l'opzione Usa certificato personale.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Viene visualizzata la pagina **Dominio personalizzato**.

5. In Tipo di gestione dei certificati selezionare **Gestito dalla rete CDN**.

6. Selezionare **Sì** per abilitare HTTPS.

    ![Stato HTTPS del dominio personalizzato](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Continuare a [convalidare il dominio](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Opzione 2: Abilitare HTTPS con un certificato personale](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Questa opzione è disponibile solo con i profili della **rete di distribuzione dei contenuti di Microsoft Azure** e con la **rete di distribuzione dei contenuti di Verizon Azure**. 
>
 
Per abilitare la funzionalità HTTPS, è possibile usare un certificato personale. Questo processo avviene tramite un'integrazione con Azure Key Vault, che consente di archiviare i certificati in modo sicuro. La rete CDN di Azure usa questo meccanismo sicuro per ottenere il certificato e richiede alcuni passaggi aggiuntivi. Quando si crea il certificato TLS/SSL, è necessario crearlo con un'autorità di certificazione consentita (CA). In caso contrario, se si usa una CA non consentita, la richiesta verrà rifiutata. Per un elenco delle autorità di certificazione consentite, vedere [Allowed certificate authorities for enabling custom HTTPS on Azure CDN](cdn-troubleshoot-allowed-ca.md) (Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nella rete CDN di Azure). Per la rete **CDN di Azure di Verizon** verrà accettata qualsiasi CA valida. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparare l'account e il certificato di Azure Key Vault
 
1. Azure Key Vault: è necessario un account Azure Key Vault in esecuzione nella stessa sottoscrizione del profilo di rete CDN di Azure e degli endpoint della rete CDN in cui si vuole abilitare la funzionalità HTTPS personalizzato. Se non si ha un account Azure Key Vault, crearne uno.
 
2. Azure Key Vault certificati: se si dispone di un certificato, caricarlo direttamente nell'account di Azure Key Vault. Se non si dispone di un certificato, creare un nuovo certificato direttamente tramite Azure Key Vault.

### <a name="register-azure-cdn"></a>Registrare la rete CDN di Azure

Registrare la rete CDN di Azure come app in Azure Active Directory tramite PowerShell.

1. Se necessario, installare [Azure PowerShell](/powershell/azure/install-az-ps) nel computer locale.

2. In PowerShell eseguire questo comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** è l'entità servizio per **Microsoft. AzureFrontDoor-Cdn**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Concedere alla rete CDN di Azure l'accesso all'insieme di credenziali delle chiavi
 
Concedere alla rete CDN di Azure l'autorizzazione ad accedere ai certificati (segreti) nell'account Azure Key Vault.

1. Nell'insieme di credenziali delle chiavi, nella sezione **Impostazioni** , selezionare **criteri di accesso**. Nel riquadro di destra selezionare **+ Aggiungi criteri di accesso**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Creare criteri di accesso all'insieme di credenziali delle app" border="true":::

2. Nella pagina **Aggiungi criteri di accesso** selezionare **Nessuno selezionato** accanto a **Seleziona entità**. Nella pagina **principale** immettere **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**. Selezionare **Microsoft. AzureFrontdoor-Cdn**.  Scegliere **Seleziona**:

2. In **Seleziona entità** cercare **205478C0-bd83-4e1b-a9d6-db63a3e1e1c8**, scegliere **Microsoft. AzureFrontDoor-Cdn**. Scegliere **Seleziona**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Selezionare l'entità servizio della rete CDN di Azure" border="true":::
    
3. Selezionare **autorizzazioni** per i certificati. Selezionare le caselle di controllo relative a **Get** e **List** per consentire alle autorizzazioni della rete CDN di ottenere ed elencare i certificati.

4. Selezionare **autorizzazioni segrete**. Selezionare le caselle di controllo relative a **Get** e **List** per consentire alle autorizzazioni della rete CDN di ottenere ed elencare i segreti:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Selezionare le autorizzazioni per la rete CDN in Vault" border="true":::

5. Selezionare **Aggiungi**. 

    La rete CDN di Azure può ora accedere a questo insieme di credenziali delle chiavi e ai certificati (segreti) in esso archiviati.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Selezionare il certificato da distribuire per la rete CDN di Azure
 
1. Tornare al portale della rete CDN di Azure e selezionare il profilo e l'endpoint della rete CDN in cui si vuole abilitare la funzionalità HTTPS personalizzato. 

2. Nell'elenco di domini personalizzati selezionare il dominio personalizzato per cui si vuole abilitare il protocollo HTTPS.

    Viene visualizzata la pagina **Dominio personalizzato**.

3. In Tipo di gestione dei certificati selezionare **Usa certificato personale**. 

    ![Configurare il certificato](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selezionare un insieme di credenziali delle chiavi, un certificato (segreto) e una versione del certificato.

    La rete CDN di Azure elenca le informazioni seguenti: 
    - Account Key Vault per l'ID sottoscrizione. 
    - Certificati (segreti) nell'insieme di credenziali delle chiavi selezionato. 
    - Versioni del certificato disponibili. 
 
5. Selezionare **Sì** per abilitare HTTPS.
  
6. Quando si usa il certificato, la convalida del dominio non è obbligatoria. Continua ad [attendere la propagazione](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Convalidare il dominio

Se si dispone di un dominio personalizzato in uso mappato all'endpoint personalizzato con un record CNAME o si usa il proprio certificato, continuare con il [dominio personalizzato mappato all'endpoint](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)della rete CDN. 

In caso contrario, se la voce di record CNAME per l'endpoint non esiste più o contiene il sottodominio cdnverify, continuare a eseguire il [mapping del dominio personalizzato all'endpoint](#custom-domain-isnt-mapped-to-your-cdn-endpoint)della rete CDN.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Il dominio personalizzato è mappato all'endpoint della rete CDN da un record CNAME

Quando è stato aggiunto un dominio personalizzato all'endpoint, è stato creato un record CNAME nel registrar del dominio DNS mappato al nome host dell'endpoint della rete CDN. 

Se il record CNAME esiste ancora e non contiene il sottodominio cdnverify, la CA DigiCert la usa per convalidare automaticamente la proprietà del dominio personalizzato. 

Se si usa il proprio certificato, la convalida del dominio non è obbligatoria.

Il record CNAME deve avere il formato seguente:

* *Nome* è il nome di dominio personalizzato.
* Il *valore* è il nome host dell'endpoint della rete CDN.

| Nome            | Type  | valore                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Per altre informazioni sui record CNAME, vedere [Create the CNAME DNS record](./cdn-map-content-to-custom-domain.md) (Creare un record DNS CNAME).

Se il record CNAME è nel formato corretto, DigiCert verifica automaticamente il nome di dominio personalizzato e crea un certificato per il dominio. DigitCert non invia alcun messaggio di verifica e non sarà necessario approvare la richiesta. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza. Continua ad [attendere la propagazione](#wait-for-propagation). 

La convalida automatica richiede in genere qualche ora. Se il dominio non viene convalidato entro 24 ore, aprire un ticket di supporto.

>[!NOTE]
>Se si ha un record di autorizzazione dell'autorità di certificazione (CAA, Certificate Authority Authorization) con il provider DNS, il record deve includere DigiCert come CA valida. Un record CAA consente ai proprietari di domini di indicare ai propri provider DNS le CA autorizzate a emettere certificati per i loro domini. Se una CA riceve l'ordine di un certificato per un dominio dotato di record CAA ma la CA non è citata come autorità emittente autorizzata in tale record, non deve emettere il certificato per il dominio o il sottodominio. Per informazioni sulla gestione dei record CAA, vedere l'argomento relativo alla [gestione dei record CAA](https://support.dnsimple.com/articles/manage-caa-record/). Per informazioni su uno strumento per i record CAA, vedere [Strumento di supporto per record CAA](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Il dominio personalizzato non è mappato all'endpoint della rete CDN

>[!NOTE]
>Se si usa la **rete CDN di Azure di Akamai**, è necessario configurare il record CNAME seguente per consentire la convalida automatica del dominio: "_acme-challenge.&lt;nome host del dominio personalizzato&gt; -> CNAME -> &lt;nome host del dominio personalizzato&gt;.ak-acme-challenge.azureedge.net"

Se la voce di record CNAME contiene il sottodominio cdnverify, seguire le istruzioni riportate in questo passaggio.

DigiCert invia un messaggio di verifica agli indirizzi di posta elettronica seguenti. Verificare di poter eseguire l'approvazione direttamente da uno degli indirizzi seguenti:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Si dovrebbe ricevere un messaggio di posta elettronica in pochi minuti per approvare la richiesta. Nel caso in cui si stia usando un filtro per la posta indesiderata, aggiungere verification@digicert.com all'elenco Consenti. Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.
    
![Messaggio di posta elettronica di convalida del dominio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando si seleziona il collegamento di approvazione, si viene indirizzati al modulo di approvazione online seguente: 
    
![Modulo di convalida del dominio](./media/cdn-custom-ssl/domain-validation-form.png)

Seguire le istruzioni nel modulo. Sono disponibili due opzioni di verifica:

- È possibile approvare tutti gli ordini futuri effettuati con lo stesso account per lo stesso dominio radice, ad esempio contoso.com. Questo approccio è consigliato se si prevede di aggiungere altri domini personalizzati per lo stesso dominio radice.

- È possibile approvare solo il nome host specifico usato in questa richiesta. Un'altra approvazione è necessaria per le richieste successive.

Dopo l'approvazione, DigiCert completa la creazione del certificato per il nome di dominio personalizzato. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza.

## <a name="wait-for-propagation"></a>Attendere la propagazione

Dopo la convalida del nome di dominio, per l'attivazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene modificato in **attivato**. I quattro passaggi dell'operazione nella finestra di dialogo dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato è ora pronto per l'uso di HTTPS.

![Finestra di dialogo per l'abilitazione di HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per l'abilitazione di HTTPS. Dopo l'abilitazione di HTTPS, i quattro passaggi dell'operazione vengono visualizzati nella finestra di dialogo del dominio personalizzato. Quando ogni passaggio diventa attivo, vengono visualizzati altri dettagli del sottopassaggio sotto il passaggio durante l'avanzamento. Non tutti i passaggi secondari verranno eseguiti. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Passaggio dell'operazione | Dettagli del passaggio secondario dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| | La richiesta HTTPS è in fase di invio. |
| | La richiesta HTTPS è stata inviata. |
| 2 Convalida del dominio | Il dominio viene automaticamente convalidato se CNAME è mappato all'endpoint della rete CDN. In caso contrario, una richiesta di verifica verrà inviata all'indirizzo di posta elettronica elencato nel record di registrazione del dominio (registrante WHOIS).|
| | La proprietà del dominio è stata convalidata. |
| | La richiesta di convalida della proprietà del dominio è scaduta (probabilmente perché il cliente non ha risposto entro 6 giorni). HTTPS non verrà abilitato nel dominio. * |
| | La richiesta di convalida della proprietà del dominio è stata rifiutata dal cliente. HTTPS non verrà abilitato nel dominio. * |
| 3 Provisioning del certificato | L'autorità di certificazione sta per rilasciare il certificato necessario per abilitare HTTPS nel dominio. |
| | Il certificato è stato rilasciato ed è in fase di distribuzione nella rete CDN. Questa operazione potrebbe richiedere fino a 6 ore. |
| | Il certificato è stato distribuito nella rete CDN. |
| 4 Operazione completata | HTTPS è stato abilitato nel dominio. |

\* Questo messaggio viene visualizzato solo se si è verificato un errore. 

Se si verifica un errore prima dell'invio della richiesta, viene visualizzato il messaggio di errore seguente:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Pulire le risorse - Disabilitare il protocollo HTTPS

In questa sezione si apprenderà come disabilitare HTTPS per il dominio personalizzato.

### <a name="disable-the-https-feature"></a>Disabilitare la funzionalità HTTPS 

1. Accedere al [portale di Azure](https://portal.azure.com) quindi cercare e selezionare **Profili CDN**. 

2. Scegliere il profilo **Rete CDN di Azure Standard fornita da Microsoft**, **Rete CDN di Azure Standard fornita da Verizon**, **Rete CDN di Azure Premium fornita da Verizon**.

3. Nell'elenco di endpoint selezionare l'endpoint contenente il dominio personalizzato.

4. Scegliere il dominio personalizzato per cui si vuole disabilitare HTTPS.

    ![Elenco dei domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Fare clic su **No** per disabilitare HTTPS e quindi su **Applica**.

    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Attendere la propagazione

Per rendere effettiva la disabilitazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene modificato in **disabilitato**. I tre passaggi dell'operazione nella finestra di dialogo dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato non può più usare HTTPS.

![Finestra di dialogo per la disabilitazione di HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per la disabilitazione di HTTPS. Dopo aver disattivato HTTPS, nella finestra di dialogo dominio personalizzato vengono visualizzati tre passaggi dell'operazione. Quando un passaggio diventa attivo, i dettagli vengono visualizzati sotto il passaggio. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Avanzamento dell'operazione | Dettagli dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| 2 Deprovisioning del certificato | Eliminazione del certificato |
| 3 Operazione completata | Il certificato è stato eliminato |

## <a name="frequently-asked-questions"></a>Domande frequenti

1. *Chi è il provider di certificati e quale tipo di certificato viene usato?*

    Un certificato dedicato fornito da DigiCert viene usato per il dominio personalizzato per:
    
    * **Rete CDN di Azure di Verizon**
    * **Rete CDN di Azure di Microsoft**

2. *Viene usata una configurazione TLS/SSL basata su IP o su SNI?*

    Entrambe le reti **CDN di Azure fornita da Verizon** e **CDN Standard di Azure fornita da Microsoft** utilizzano una configurazione TLS/SSL basata su SNI.

3. *Cosa accade se non si riceve il messaggio di verifica del dominio da DigiCert?*

    Se non si usa il sottodominio *cdnverify* e la voce CNAME è per il nome host dell'endpoint, non si riceverà un messaggio di posta elettronica di verifica del dominio.
     
    La convalida viene eseguita automaticamente. In caso contrario, se non è disponibile alcuna voce CNAME e non è stato ricevuto alcun messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

4. *L'uso di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN applica gli stessi standard di crittografia e sicurezza di un certificato dedicato. Tutti i certificati TLS/SSL emessi usano l'algoritmo SHA-256 per la protezione avanzata dei server.

5. *È necessario avere un record di autorizzazione dell'autorità di certificazione presso il provider DNS?*

    Il record di autorizzazione dell'autorità di certificazione non è attualmente obbligatorio. Se tuttavia se ne ha uno, deve includere DigiCert come CA valida.

6. *Il 20 giugno 2018, la rete CDN di Azure fornita da Verizon ha iniziato a utilizzare un certificato dedicato con configurazione TLS/SSL basata su SNI per impostazione predefinita. Cosa accade ai miei domini personalizzati che utilizzano un certificato SAN (nome alternativo del soggetto) e una configurazione TLS/SSL basata su IP?*

    Se Microsoft rileva che all'applicazione vengono effettuate esclusivamente richieste client SNI, i domini esistenti verranno gradualmente migrati al certificato unico nei prossimi mesi. 
    
    Se vengono rilevati client non SNI, i domini resteranno nel certificato SAN con TLS/SSL basato su IP. Le richieste al servizio o ai client non SNI non sono interessate.

7. *Come funzionano i rinnovi di certificati con il programma Bring Your Own Certificate?*

    Per assicurarsi che un certificato più recente venga distribuito nell'infrastruttura PoP, caricare il nuovo certificato nell'insieme di credenziali delle credenziali di Azure. In impostazioni TLS nella rete CDN di Azure scegliere la versione più recente del certificato e selezionare Salva. La rete CDN di Azure propagherà quindi il nuovo certificato aggiornato. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> - Abilitare il protocollo HTTPS nel dominio personalizzato
> - Usare un certificato gestito dalla rete CDN 
> - Usare un certificato personale
> - Convalidare il dominio
> - Disabilitare il protocollo HTTPS nel dominio personalizzato

Passare all'esercitazione successiva per informazioni su come configurare la memorizzazione nella cache nell'endpoint della rete CDN.

> [!div class="nextstepaction"]
> [Esercitazione: Impostare le regole di memorizzazione nella cache della rete CDN di Azure](cdn-caching-rules-tutorial.md)