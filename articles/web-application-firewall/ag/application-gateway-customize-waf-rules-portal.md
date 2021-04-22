---
title: Personalizzare le regole tramite il portale - Web application firewall di Azure
description: Questo articolo fornisce informazioni su come personalizzare le regole del web application firewall nel gateway applicazione con il portale di Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878327"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalizzare le regole di Web Application Firewall usando il portale di Azure

Il gateway applicazione di Azure Web Application Firewall (WAF) fornisce protezione per le applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per altre informazioni su regole e gruppi di regole specifici, vedere Elenco di regole e gruppi di regole CRS di [Web Application Firewall.](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Se il gateway applicazione non sta usando il livello WAF, l'opzione per aggiornare il gateway applicazione al livello WAF, come visualizzato nel riquadro a destra. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="Abilitare WAF"::: 

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

**Visualizzare le regole e i gruppi di regole**
1. Esplorare il gateway applicazione, e quindi selezionare **Web application firewall**.  
2. Selezionare il **criterio WAF.**
2. Selezionare **Regole gestite.**

   Questa visualizzazione mostra una tabella nella pagina di tutti i gruppi di regole forniti con il set di regole selezionato. Vengono selezionate tutte le caselle di controllo della regola.

## <a name="disable-rule-groups-and-rules"></a>Disabilitare le regole e i gruppi di regole

> [!IMPORTANT]
> Prestare attenzione quando si disabilitano tutti i gruppi di regole o le regole. Ciò può esporre a maggiori rischi per la sicurezza.

**Disabilitare gruppi di regole o regole specifiche**

   1. Cercare le regole o i gruppi di regole che si desidera disabilitare.
   2. Selezionare le caselle di controllo per le regole da disabilitare. 
   3. Selezionare l'azione nella parte superiore della pagina (abilita/disabilita) per le regole selezionate.
   2. Selezionare **Salva**.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Salvare le regole disabilitate"::: 

## <a name="mandatory-rules"></a>Regole obbligatorie

L'elenco seguente contiene le condizioni che causano il blocco della richiesta da parte del WAF durante la modalità di prevenzione. In modalità di rilevamento vengono registrati come eccezioni.

Questi elementi non possono essere configurati o disabilitati:

* Se non si analizza il corpo della richiesta, la richiesta viene bloccata, a meno che l'ispezione del corpo non sia disattivata (XML, JSON, dati del modulo)
* La lunghezza dei dati del corpo della richiesta (senza file) è superiore al limite configurato
* Il corpo della richiesta (inclusi i file) è superiore al limite
* Si è verificato un errore interno nel motore waf

Specifico di CRS 3.x:

* Il punteggio di anomalie in ingresso ha superato la soglia

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).