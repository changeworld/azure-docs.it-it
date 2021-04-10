---
title: Configurare i criteri SSL specifici del listener nel gateway applicazione Azure tramite il portale
description: Informazioni su come configurare i criteri SSL specifici del listener nel gateway applicazione tramite il portale
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231577"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Configurare i criteri SSL specifici del listener nel gateway applicazione tramite il portale (anteprima)

Questo articolo descrive come usare la portale di Azure per configurare i criteri SSL specifici del listener nel gateway applicazione. I criteri SSL specifici del listener consentono di configurare listener specifici per l'uso di criteri SSL diversi tra loro. Sarà comunque possibile impostare un criterio SSL predefinito che tutti i listener utilizzeranno a meno che non vengano sovrascritti dal criterio SSL specifico del listener. 

> [!NOTE]
> Solo gli SKU Standard_v2 e WAF_v2 supportano criteri specifici del listener. i criteri specifici del listener sono parte dei profili SSL e i profili SSL sono supportati solo nei gateway V2. 



Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-new-application-gateway"></a>Creare un nuovo gateway applicazione

Creare innanzitutto un nuovo gateway applicazione come si farebbe in genere tramite il portale. non sono necessari passaggi aggiuntivi per configurare i criteri SSL specifici del listener. Per altre informazioni su come creare un gateway applicazione nel portale, vedere l' [esercitazione introduttiva sul portale](./quick-create-portal.md).

## <a name="set-up-a-listener-specific-ssl-policy"></a>Configurare criteri SSL specifici del listener

Per configurare i criteri SSL specifici del listener, è necessario passare prima alla scheda **Impostazioni SSL (anteprima)** nel portale e creare un nuovo profilo SSL. Quando si crea un profilo SSL, verranno visualizzate due schede: **autenticazione client** e **criteri SSL**. La scheda **criteri SSL** consente di configurare criteri SSL specifici del listener. La scheda **autenticazione client** è la posizione in cui caricare i certificati client per l'autenticazione reciproca. per ulteriori informazioni, vedere [configurazione di un'autenticazione reciproca](./mutual-authentication-portal.md).

> [!NOTE]
> È consigliabile usare TLS 1,2 perché TLS 1,2 verrà richiesto in futuro. 

1. Cercare il **gateway applicazione** nel portale, selezionare **gateway applicazione** e fare clic sul gateway applicazione esistente.

2. Selezionare **Impostazioni SSL (anteprima)** dal menu a sinistra.

3. Fare clic sul segno più accanto a **profili SSL** nella parte superiore per creare un nuovo profilo SSL.

4. Immettere un nome in **nome profilo SSL**. In questo esempio viene chiamato il profilo SSL *applicationGatewaySSLProfile*. 

5. Passare alla scheda **criteri SSL** e selezionare la casella **Abilita criteri SSL specifici del listener** . 

6. Configurare i criteri SSL specifici del listener in base ai requisiti. È possibile scegliere tra criteri SSL predefiniti e personalizzare i propri criteri SSL. Per altre informazioni sui criteri SSL, vedere [Panoramica dei criteri SSL](./application-gateway-ssl-policy-overview.md). Si consiglia di usare TLS 1,2

7. Selezionare **Aggiungi** per salvare.

    > [!NOTE]
    > Non è necessario configurare l'autenticazione client in un profilo SSL per associarlo a un listener. È possibile configurare solo l'autenticazione client o solo i criteri SSL specifici del listener configurati o entrambi configurati nel profilo SSL.  

    ![Aggiungere criteri SSL specifici del listener al profilo SSL](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Associare il profilo SSL a un listener

Ora che è stato creato un profilo SSL con criteri SSL specifici del listener, è necessario associare il profilo SSL al listener per inserire i criteri specifici del listener in azione. 

1. Passare al gateway applicazione esistente. Se la procedura precedente è stata completata, non è necessario eseguire alcuna operazione. 

2. Selezionare **listener** dal menu a sinistra. 

3. Fare clic su **Aggiungi listener** se non è già stato configurato un listener HTTPS. Se si dispone già di un listener HTTPS, fare clic su di esso nell'elenco. 

4. Compilare il **nome del listener**, l'IP front- **end**, la **porta**, il **protocollo** e altre **impostazioni HTTPS** per soddisfare i requisiti.

5. Selezionare la casella di controllo **Abilita profilo SSL** per poter selezionare il profilo SSL da associare al listener. 

6. Selezionare il profilo SSL creato dall'elenco a discesa. In questo esempio si sceglie il profilo SSL creato dai passaggi precedenti: *applicationGatewaySSLProfile*. 

7. Continuare a configurare il resto del listener in base ai requisiti. 

8. Fare clic su **Aggiungi** per salvare il nuovo listener con il profilo SSL associato. 

    ![Associare il profilo SSL al nuovo listener](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)