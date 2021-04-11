---
title: Configurare l'autenticazione reciproca sul gateway applicazione Azure tramite il portale
description: Informazioni su come configurare un gateway applicazione per l'autenticazione reciproca tramite il portale
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231514"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Configurare l'autenticazione reciproca con il gateway applicazione tramite il portale (anteprima)

Questo articolo descrive come usare la portale di Azure per configurare l'autenticazione reciproca nel gateway applicazione. L'autenticazione reciproca significa che il gateway applicazione autentica il client inviando la richiesta usando il certificato client caricato nel gateway applicazione. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare l'autenticazione reciproca con un gateway applicazione, è necessario un certificato client da caricare nel gateway. Il certificato client verrà usato per convalidare il certificato che il client presenterà al gateway applicazione. A scopo di test, è possibile usare un certificato autofirmato. Tuttavia, questa operazione non è consigliata per i carichi di lavoro di produzione, perché sono più difficili da gestire e non sono completamente protetti. 

Per altre informazioni, soprattutto sul tipo di certificati client che è possibile caricare, vedere [Panoramica dell'autenticazione reciproca con il gateway applicazione](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Creare un nuovo gateway applicazione

Creare prima di tutto un nuovo gateway applicazione come si farebbe normalmente tramite il portale. non sono necessari passaggi aggiuntivi per abilitare l'autenticazione reciproca. Per altre informazioni su come creare un gateway applicazione nel portale, vedere l' [esercitazione introduttiva sul portale](./quick-create-portal.md).

## <a name="configure-mutual-authentication"></a>Configurare l'autenticazione reciproca 

Per configurare un gateway applicazione esistente con l'autenticazione reciproca, è necessario passare prima alla scheda **Impostazioni SSL (anteprima)** nel portale e creare un nuovo profilo SSL. Quando si crea un profilo SSL, verranno visualizzate due schede: **autenticazione client** e **criteri SSL**. Nella scheda **autenticazione client** è possibile caricare i certificati client. La scheda **criteri SSL** consente di configurare criteri SSL specifici del listener. per ulteriori informazioni, vedere [configurazione di un criterio SSL specifico del listener](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Assicurarsi di caricare l'intera catena di certificati della CA client in un file e solo una catena per ogni file.

1. Cercare il **gateway applicazione** nel portale, selezionare **gateway applicazione** e fare clic sul gateway applicazione esistente.

2. Selezionare **Impostazioni SSL (anteprima)** dal menu a sinistra.

3. Fare clic sul segno più accanto a **profili SSL** nella parte superiore per creare un nuovo profilo SSL.

4. Immettere un nome in **nome profilo SSL**. In questo esempio viene chiamato il profilo SSL *applicationGatewaySSLProfile*. 

5. Rimanere nella scheda **autenticazione client** . Caricare il certificato PEM che si intende usare per l'autenticazione reciproca tra il client e il gateway applicazione usando il pulsante **carica un nuovo certificato** . 

    Per altre informazioni su come estrarre le catene di certificati CA client attendibili da caricare qui, vedere come estrarre le catene di [certificati CA client attendibili](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > Se questo non è il primo profilo SSL e sono stati caricati altri certificati client nel gateway applicazione, è possibile scegliere di riusare un certificato esistente sul gateway tramite il menu a discesa. 

6. Controllare la casella **DN dell'autorità emittente del certificato client** solo se si vuole che il gateway applicazione verifichi il nome distinto dell'autorità emittente immediata del certificato client. 

7. Prendere in considerazione l'aggiunta di un criterio specifico del listener. Vedere le istruzioni nella pagina [relativa alla configurazione di criteri SSL specifici del listener](./application-gateway-configure-listener-specific-ssl-policy.md).

8. Selezionare **Aggiungi** per salvare.
    > [!div class="mx-imgBorder"]
    > ![Aggiungere l'autenticazione client al profilo SSL](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Associare il profilo SSL a un listener

Ora che è stato creato un profilo SSL con autenticazione reciproca configurata, è necessario associare il profilo SSL al listener per completare la configurazione dell'autenticazione reciproca. 

1. Passare al gateway applicazione esistente. Se la procedura precedente è stata completata, non è necessario eseguire alcuna operazione. 

2. Selezionare **listener** dal menu a sinistra. 

3. Fare clic su **Aggiungi listener** se non è già stato configurato un listener HTTPS. Se si dispone già di un listener HTTPS, fare clic su di esso nell'elenco. 

4. Compilare il **nome del listener**, l'IP front- **end**, la **porta**, il **protocollo** e altre **impostazioni HTTPS** per soddisfare i requisiti.

5. Selezionare la casella di controllo **Abilita profilo SSL** per poter selezionare il profilo SSL da associare al listener. 

6. Selezionare il profilo SSL appena creato dall'elenco a discesa. In questo esempio si sceglie il profilo SSL creato dai passaggi precedenti: *applicationGatewaySSLProfile*. 

7. Continuare a configurare il resto del listener in base ai requisiti. 

8. Fare clic su **Aggiungi** per salvare il nuovo listener con il profilo SSL associato. 

    > [!div class="mx-imgBorder"]
    > ![Associare il profilo SSL al nuovo listener](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Rinnova i certificati CA client scaduti

Nel caso in cui il certificato della CA client sia scaduto, è possibile aggiornare il certificato nel gateway attenendosi alla procedura seguente: 

1. Passare al gateway applicazione e passare alla scheda **Impostazioni SSL (anteprima)** nel menu a sinistra. 
 
1. Selezionare i profili SSL esistenti con il certificato client scaduto. 
 
1. Selezionare **carica un nuovo certificato** nella scheda **autenticazione client** e caricare il nuovo certificato client. 
 
1. Selezionare l'icona del cestino accanto al certificato scaduto. Verrà rimossa l'associazione del certificato dal profilo SSL. 

1. Ripetere i passaggi 2-4 precedenti con qualsiasi altro profilo SSL che usa lo stesso certificato client scaduto. Sarà possibile scegliere il nuovo certificato caricato nel passaggio 3 dal menu a discesa in altri profili SSL.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)