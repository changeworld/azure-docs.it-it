---
title: Risoluzione dei problemi per Datadog-soluzioni partner di Azure
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi per Datadog in Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563709"
---
# <a name="troubleshooting-datadog-on-azure"></a>Risoluzione dei problemi relativi a Datadog in Azure

Questo documento contiene informazioni sulla risoluzione dei problemi relativi alle soluzioni che usano Datadog.

## <a name="purchase-errors"></a>Errori di acquisto

* L'acquisto non riesce perché una carta di credito valida non è connessa alla sottoscrizione di Azure oppure un metodo di pagamento non è associato alla sottoscrizione.

  Usare una sottoscrizione di Azure diversa. In alternativa, aggiungere o aggiornare la carta di credito o il metodo di pagamento per la sottoscrizione. Per ulteriori informazioni, vedere [aggiornamento del metodo di pagamento e di credito](../../cost-management-billing/manage/change-credit-card.md).

* La sottoscrizione EA non consente acquisti nel Marketplace.

  Usare una sottoscrizione diversa. In alternativa, controllare se la sottoscrizione EA è abilitata per l'acquisto del Marketplace. Per altre informazioni, vedere [Enable Marketplace purchases](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Se tali opzioni non risolvono il problema, contattare il [supporto di Datadog](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Non è possibile creare la risorsa Datadog

Per configurare l'integrazione di Azure Datadog, è necessario avere l'accesso al **proprietario** nella sottoscrizione di Azure. Assicurarsi di disporre dell'accesso appropriato prima di avviare l'installazione.

## <a name="single-sign-on-errors"></a>Errori di Single Sign-on

Non è **possibile salvare le impostazioni di Single Sign-on** . questo errore si verifica quando è presente un'altra app aziendale che usa l'identificatore SAML Datadog. Per trovare l'app che lo usa, selezionare **modifica** nella sezione configurazione SAML di base.

Per risolvere questo problema, disabilitare l'altra app o usare l'altra app come app aziendale per configurare l'accesso SSO SAML con Datadog. Se si decide di usare l'altra app, assicurarsi che l'app abbia le [impostazioni necessarie](create.md#configure-single-sign-on).

L' **app non viene visualizzata nella pagina di impostazione dell'accesso Single Sign-on** , innanzitutto cercare l'ID applicazione. Se non viene visualizzato alcun risultato, controllare le impostazioni SAML dell'app. La griglia Mostra solo le app con impostazioni SAML corrette. 

L'URL dell'identificatore deve essere `https://us3.datadoghq.com/account/saml/metadata.xml` .

L'URL di risposta deve essere `https://us3.datadoghq.com/account/saml/assertion` .

Nell'immagine seguente vengono illustrati i valori corretti.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Controllare le impostazioni SAML per l'applicazione Datadog in AAD." border="true":::

**Gli utenti Guest invitati al tenant non sono in grado di accedere al Single Sign-on** . alcuni utenti hanno due indirizzi di posta elettronica in portale di Azure. In genere, un messaggio di posta elettronica è il nome dell'entità utente (UPN) e l'altro è un indirizzo di posta elettronica alternativo.

Quando si invita l'utente Guest, usare l'UPN del tenant Home. Utilizzando il nome UPN, l'indirizzo di posta elettronica viene mantenuto sincronizzato durante il processo Single Sign-on. È possibile trovare l'UPN cercando l'indirizzo di posta elettronica nell'angolo superiore destro del portale di Azure dell'utente.
  
## <a name="logs-not-being-emitted"></a>Log non emessi

Solo le risorse elencate nelle categorie di log delle risorse di monitoraggio di Azure emettono log in Datadog. Per verificare se la risorsa emette log in Datadog, passare a impostazioni di diagnostica di Azure per la risorsa specifica. Verificare che sia presente un'impostazione di diagnostica Datadog.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Impostazione di diagnostica Datadog nella risorsa di Azure" border="true":::

## <a name="metrics-not-being-emitted"></a>Metriche non emesse

Alla risorsa Datadog viene assegnato un ruolo di **lettore di monitoraggio** nella sottoscrizione di Azure appropriata. Questo ruolo consente alla risorsa Datadog di raccogliere le metriche e inviare tali metriche a Datadog.

Per verificare che la risorsa abbia l'assegnazione di ruolo corretta, aprire il portale di Azure e selezionare la sottoscrizione. Nel riquadro a sinistra selezionare **controllo di accesso (IAM)**. Cercare il nome della risorsa Datadog. Verificare che la risorsa Datadog abbia l'assegnazione del ruolo di **lettore di monitoraggio** , come illustrato di seguito.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Assegnazione di ruolo Datadog nella sottoscrizione di Azure" border="true":::

## <a name="datadog-agent-installation-fails"></a>L'installazione dell'agente Datadog non riesce

L'integrazione di Azure Datadog consente di installare l'agente Datadog in una macchina virtuale o in un servizio app. Per la configurazione dell'agente Datadog, viene usata la chiave API selezionata come **chiave predefinita** nella schermata delle chiavi API. Se non si seleziona una chiave predefinita, l'installazione dell'agente Datadog non riuscirà.

Se l'agente Datadog è stato configurato con una chiave non corretta, passare alla schermata chiavi API e modificare la **chiave predefinita**. È necessario disinstallare l'agente Datadog e reinstallarlo per configurare la macchina virtuale con le nuove chiavi API.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [gestione dell'istanza](manage.md) di Datadog.
