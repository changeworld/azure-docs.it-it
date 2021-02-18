---
title: Configurare HTTPS per il dominio personalizzato in una configurazione dello SKU Standard/Premium di Azure front door
description: Questo articolo illustra come eseguire l'onboarding di un dominio personalizzato nello SKU Standard/Premium dello sportello front-end di Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: f992f7d4295f380e4b1f2b13cc5da33df89354ab
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099618"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Configurare HTTPS in un dominio personalizzato dello SKU Standard/Premium (anteprima) di front-end usando il portale di Azure

> [!NOTE]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Azure front door standard/Premium consente il recapito TLS sicuro alle applicazioni per impostazione predefinita quando viene aggiunto un dominio personalizzato. Usando il protocollo HTTPS nel dominio personalizzato, è possibile garantire che i dati sensibili vengano recapitati in modo sicuro con la crittografia TLS/SSL quando vengono inviati attraverso Internet. Quando il Web browser è connesso a un sito Web tramite HTTPS, convalida il certificato di sicurezza del sito Web e verifica che sia emesso da un'autorità di certificazione legittima. Questo processo offre sicurezza e protezione delle applicazioni Web da attacchi.

Azure front door standard/Premium supporta sia il certificato gestito di Azure che i certificati gestiti dal cliente. Per impostazione predefinita, l'opzione front-end di Azure Abilita automaticamente HTTPS per tutti i domini personalizzati usando i certificati gestiti di Azure. Non sono necessari passaggi aggiuntivi per ottenere un certificato gestito di Azure. Un certificato viene creato durante il processo di convalida del dominio. È anche possibile usare il proprio certificato integrando Azure front door standard/Premium con la Key Vault.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere le [**Condizioni supplementari per l'uso delle anteprime di Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

* Prima di poter configurare HTTPS per il dominio personalizzato, è necessario creare prima di tutto un profilo standard/Premium di Azure front door. Per altre informazioni, vedere [Guida introduttiva: creare un profilo standard o Premium di Azure front door](create-front-door-portal.md).

* Se non si dispone già di un dominio personalizzato, è necessario prima acquistarne uno con un provider di dominio. Ad esempio, vedere [Acquistare un nome di dominio personalizzato](../../app-service/manage-custom-dns-buy-domain.md).

* Se si usa Azure per ospitare i [domini DNS](../../dns/dns-overview.md), è necessario delegare il DNS (Domain Name System) del provider di dominio a un DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md). In caso contrario, se si usa un provider di dominio per gestire il dominio DNS, è necessario convalidare manualmente il dominio immettendo i record TXT DNS richiesti.

## <a name="azure-managed-certificates"></a>Certificati gestiti di Azure

1. In impostazioni per il profilo standard/Premium di Azure front door selezionare **domini** , quindi selezionare **+ Aggiungi** per aggiungere un nuovo dominio.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Screenshot della pagina di destinazione della configurazione del dominio.":::

1. Nella pagina **Aggiungi un dominio** selezionare l'opzione **DNS gestito di Azure** per *gestione DNS* . 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Screenshot della pagina Aggiungi un dominio con DNS gestito di Azure selezionato.":::

1. Convalidare e associare il dominio personalizzato a un endpoint attenendosi alla procedura descritta in Abilitazione del [dominio personalizzato](how-to-add-custom-domain.md).

1. Una volta che il dominio personalizzato viene associato all'endpoint con successo, un certificato gestito di Azure viene distribuito in uno sportello anteriore. Il completamento di questo processo potrebbe richiedere alcuni minuti.

## <a name="using-your-own-certificate"></a>Uso del proprio certificato

È anche possibile scegliere di usare il proprio certificato TLS. Questo certificato deve essere importato in un Azure Key Vault prima di poterlo usare con Azure front door standard/Premium. Vedere [importare un certificato](../../key-vault/certificates/tutorial-import-certificate.md) in Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparare l'account e il certificato di Azure Key Vault
 
1. È necessario disporre di un account Azure Key Vault in esecuzione nella stessa sottoscrizione di Azure front door standard/Premium che si vuole abilitare HTTPS personalizzato. Se non si ha un account Azure Key Vault, crearne uno.

    > [!WARNING]
    > Il servizio Frontdoor di Azure attualmente supporta solo account Key Vault nella stessa sottoscrizione della configurazione della frontdoor. La scelta di un Key Vault in una sottoscrizione diversa da quella di Azure front door standard/Premium genererà un errore.

1. Se si dispone già di un certificato, è possibile caricarlo direttamente nell'account Azure Key Vault. In caso contrario, creare un nuovo certificato direttamente tramite Azure Key Vault da una delle autorità di certificazione partner con cui Azure Key Vault si integra. Caricare il certificato come oggetto **certificato**, anziché come **segreto**.

    > [!NOTE]
    > Per il certificato TLS/SSL, il servizio Frontdoor non supporta i certificati con algoritmi basati su crittografia a curva ellittica.

#### <a name="register-azure-front-door"></a>Registrare il servizio Frontdoor di Azure

Registrare l'entità servizio per il servizio Frontdoor di Azure come app in Azure Active Directory tramite PowerShell.

> [!NOTE]
> Questa azione richiede autorizzazioni di amministratore globale e deve essere eseguita solo **una volta** per ogni tenant.

1. Se necessario, installare [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell nel computer locale.

1. In PowerShell eseguire questo comando:

     `New-AzADServicePrincipal -ApplicationId 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8""`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Concedere al servizio Frontdoor di Azure l'accesso all'insieme di credenziali delle chiavi
 
Concedere al servizio Frontdoor di Azure l'autorizzazione ad accedere ai certificati nell'account Azure Key Vault.

1. Nell'account dell'insieme di credenziali delle chiavi, in impostazioni, selezionare **criteri di accesso**. Quindi selezionare **Aggiungi nuovo** per creare un nuovo criterio.

1. In **Seleziona entità** cercare **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** e scegliere * * Microsoft. AzureFrontDoor-Cdn * *. Fare clic su **Seleziona**.

1. In **Autorizzazioni dei segreti** selezionare **Recupera** per consentire a Frontdoor di recuperare il certificato.

1. In **Autorizzazioni del certificato** selezionare **Recupera** per consentire a Frontdoor di recuperare il certificato.

1. Selezionare **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Selezionare il certificato per il servizio Frontdoor di Azure da distribuire
 
1. Tornare al livello standard o Premium di Azure front-end nel portale.

1. Passare a **Secrets** in *Settings* e selezionare **Add certificate**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Screenshot della pagina di destinazione del segreto della porta principale di Azure.":::

1. Nella pagina **Aggiungi certificato** selezionare la casella di controllo per il certificato che si vuole aggiungere a Azure front door standard/Premium. Lasciare la selezione della versione "Latest" e selezionare **Aggiungi**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Screenshot della pagina Aggiungi certificato.":::

1. Una volta eseguito correttamente il provisioning del certificato, è possibile utilizzarlo quando si aggiunge un nuovo dominio personalizzato.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Screenshot del certificato aggiunto correttamente ai segreti.":::

1. Passare a **domini** in *impostazione* e selezionare **+ Aggiungi** per aggiungere un nuovo dominio personalizzato. Nella pagina **Aggiungi un dominio** scegliere "Bring your own certificate (BYOC)" per *https*. Per *Secret* selezionare il certificato che si vuole usare dall'elenco a discesa. 

    > [!NOTE]
    > Il certificato selezionato deve avere un nome comune (CN) uguale al dominio personalizzato da aggiungere.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Screenshot della pagina Aggiungi dominio personalizzato con HTTPS.":::

1. Seguire i passaggi sullo schermo per convalidare il certificato. Associare quindi il dominio personalizzato appena creato a un endpoint, come descritto in creazione di una guida del [dominio personalizzato](how-to-add-custom-domain.md) .

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Passare da Azure gestito a bring your own certificate (BYOC)

1. È possibile modificare un certificato gestito di Azure esistente in un certificato gestito dall'utente selezionando lo stato del certificato per aprire la pagina dei **Dettagli del certificato** .

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Screenshot dello stato del certificato nella pagina di destinazione dei domini." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Nella pagina dei **Dettagli del certificato** è possibile passare dall'opzione "Azure Managed" a "Bring your own certificate (BYOC)". Seguire quindi gli stessi passaggi descritti in precedenza per scegliere un certificato. Selezionare **Aggiorna** per modificare il certificato associato con un dominio.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Screenshot della pagina dei dettagli del certificato.":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [memorizzazione nella cache con Azure front door standard/Premium](concept-caching.md).
