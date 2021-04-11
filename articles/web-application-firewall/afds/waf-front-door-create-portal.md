---
title: 'Esercitazione: Creare un criterio di web application firewall per Frontdoor di Azure - Portale di Azure'
description: Questa esercitazione illustra come creare un criterio di web application firewall (WAF) tramite il portale di Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122257"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Esercitazione: Creare un criterio di web application firewall in Frontdoor di Azure usando il portale di Azure

Questa esercitazione illustra come creare un criterio di base di Web application firewall (WAF) di Azure e applicarlo a un host front-end in Frontdoor di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un criterio WAF
> * Associarlo a un host front-end
> * Configurare le regole per web application firewall

## <a name="prerequisites"></a>Prerequisiti

Creare una [porta anteriore](../../frontdoor/quickstart-create-front-door.md) o un profilo [Standard/Premium per la porta anteriore](../../frontdoor/standard-premium/create-front-door-portal.md) . 

## <a name="create-a-web-application-firewall-policy"></a>Creare un criterio di web application firewall

Come prima cosa, creare un criterio WAF di base con il set di regole predefinite gestito usando il portale. 

1. Nella parte superiore sinistra della schermata selezionare **Crea una risorsa** > cercare **WAF** > selezionare **Web Application Firewall (WAF)** > selezionare **Crea**.

1. Nella scheda **Generale** della pagina **Crea un criterio WAF** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Criteri per              | Selezionare **WAF globale (porta anteriore)**. |
    | SKU sportello anteriore          | Selezionare uno SKU Basic, standard e Premium. |
    | Subscription            | Selezionare il nome della sottoscrizione di Frontdoor.|
    | Resource group          | Selezionare il nome del gruppo di risorse di Frontdoor.|
    | Nome criteri             | Immettere un nome univoco per il criterio WAF.|
    | Stato criteri            | Imposta come **abilitato**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Screenshot della pagina di creazione di un criterio WAF, con il pulsante Rivedi e crea e le caselle di riepilogo relative a sottoscrizione, gruppo di risorse e nome del criterio.":::

1. Nella scheda **associazione** della pagina **creare un criterio WAF** Selezionare **+ associa un profilo di sportello anteriore**, immettere le impostazioni seguenti e quindi selezionare **Aggiungi**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Profilo sportello anteriore              | Selezionare il nome del profilo Frontdoor. |
    | Domains          | Selezionare i domini a cui si vuole associare il criterio WAF, quindi selezionare **Aggiungi**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Screenshot della pagina associa un profilo di sportello anteriore.":::
    
    > [!NOTE]
    > Se il dominio è associato a un criterio WAF, viene visualizzato come grigio. È necessario prima rimuovere il dominio dal criterio associato, quindi riassociarlo a un nuovo criterio WAF.

1. Selezionare **Rivedi e crea** e quindi **Crea**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurare le regole di web application firewall (facoltativo)

### <a name="change-mode"></a>Cambiare modalità

Quando si crea un criterio WAF, per impostazione predefinita questo è in modalità **Rilevamento**. In modalità **Rilevamento** WAF non blocca alcuna richiesta e le richieste che soddisfano le regole WAF vengono registrate nei log di WAF.
Per vedere WAF in azione, è possibile cambiare le impostazioni della modalità da **Rilevamento** a **Prevenzione**. In modalità **Prevenzione** le richieste che soddisfano le regole definite nel set di regole predefinite vengono bloccate e registrate nei log WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Screenshot della sezione di impostazioni del criterio. L'interruttore Modalità è impostato su Prevenzione.":::

### <a name="custom-rules"></a>Regole personalizzate

È possibile creare una regola personalizzata selezionando **Aggiungi regola personalizzata** nella sezione **Regole personalizzate**. Viene avviata la pagina di configurazione delle regole personalizzate. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Screenshot della pagina delle regole personalizzate.":::

Di seguito è riportato un esempio di configurazione di una regola personalizzata per bloccare una richiesta se la stringa di query contiene **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Screenshot della pagina di configurazione di regole personalizzate, che mostra l'impostazione per una regola che verifica se la variabile QueryString contiene il valore blockme.":::

### <a name="default-rule-set-drs"></a>Set di regole predefinite

Il set di regole predefinite gestito da Azure è abilitato per impostazione predefinita. La versione predefinita corrente è DefaultRuleSet_1.0. Da **regole gestite** di WAF, **assegnare**, il set di regole di Microsoft_DefaultRuleSet_1 1 disponibile di recente è disponibile nell'elenco a discesa.

Per disabilitare una singola regola, selezionare la **casella di controllo** davanti al numero della regola e selezionare **Disabilita** nella parte superiore della pagina. Per modificare i tipi di azioni per le singole regole all'interno del set di regole, selezionare la casella di controllo davanti al numero della regola, quindi selezionare l' **azione modifica** nella parte superiore della pagina.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Screenshot della pagina Regole gestite che mostra un set di regole, i gruppi di regole, le regole e i pulsanti Abilita, Disabilita e modifica azione." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse e tutte le risorse correlate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su Azure front door](../../frontdoor/front-door-overview.md) 
>  [Scopri di più su Azure front door standard/Premium](../../frontdoor/standard-premium/overview.md)
