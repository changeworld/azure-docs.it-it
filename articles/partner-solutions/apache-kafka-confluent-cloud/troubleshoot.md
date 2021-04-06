---
title: Risoluzione dei problemi relativi a Apache Kafka per il cloud Confluent-soluzioni per partner Azure
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi e sulle domande frequenti per il cloud Confluent in Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709395"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Risoluzione dei problemi Apache Kafka per soluzioni cloud confluenti

Questo documento contiene informazioni sulla risoluzione dei problemi relativi alle soluzioni che usano Apache Kafka per il cloud Confluent.

Se non si trova una risposta o non si riesce a risolvere un problema, [creare una richiesta tramite il portale di Azure](manage.md#get-support) o contattare il [supporto tecnico](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Non è possibile trovare l'offerta nel Marketplace

Per trovare l'offerta in Azure Marketplace, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa**.
1. Cercare _Apache Kafka nel cloud Confluent_.
1. Selezionare il riquadro dell'applicazione.

Se l'offerta non è visualizzata, contattare il [supporto tecnico](https://support.confluent.io). L'ID tenant di Azure Active Directory deve essere nell'elenco dei tenant consentiti. Per informazioni su come trovare l'ID tenant, vedere [come trovare l'ID tenant di Azure Active Directory](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="purchase-errors"></a>Errori di acquisto

* L'acquisto non riesce perché una carta di credito valida non è connessa alla sottoscrizione di Azure oppure un metodo di pagamento non è associato alla sottoscrizione.

  Usare una sottoscrizione di Azure diversa. In alternativa, aggiungere o aggiornare la carta di credito o il metodo di pagamento per la sottoscrizione. Per ulteriori informazioni, vedere [aggiornamento del metodo di pagamento e di credito](../../cost-management-billing/manage/change-credit-card.md).

* La sottoscrizione EA non consente acquisti nel Marketplace.

  Usare una sottoscrizione diversa. In alternativa, controllare se la sottoscrizione EA è abilitata per l'acquisto del Marketplace. Per altre informazioni, vedere [Enable Marketplace purchases](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Se tali opzioni non risolvono il problema, contattare il [supporto tecnico](https://support.confluent.io).

## <a name="conflict-error"></a>errore di conflitto

Se in precedenza è stata eseguita la registrazione per il cloud Confluent, è necessario usare un nuovo indirizzo di posta elettronica per creare un'altra risorsa di organizzazione cloud Confluent. Quando si usa un indirizzo di posta elettronica registrato in precedenza, si ottiene un errore di **conflitto** . Ripetere la registrazione, ma questa volta con un nuovo indirizzo di posta elettronica.

## <a name="deploymentfailed-error"></a>Errore DeploymentFailed

Se viene ricevuto un errore **DeploymentFailed** , verificare lo stato della sottoscrizione di Azure. Assicurarsi che non sia sospeso e che non si verifichino problemi di fatturazione.

## <a name="resource-isnt-displayed"></a>La risorsa non è visualizzata

Se i pannelli **Panoramica** o **Elimina** per il cloud Confluent non vengono visualizzati nel portale, provare ad aggiornare la pagina. Questo errore potrebbe essere un problema intermittente con il portale. Se il servizio non funziona, contattare il [supporto tecnico](https://support.confluent.io).

Se la risorsa cloud Confluent non è disponibile nell'elenco **tutte le risorse** di Azure, contattare il [supporto tecnico](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>La creazione di risorse richiede molto tempo

Se il completamento del processo di distribuzione richiede più di tre ore, contattare il supporto tecnico.

Se la distribuzione ha esito negativo e la risorsa cloud Confluent ha lo stato `Failed` , eliminare la risorsa. Dopo l'eliminazione, riprovare a creare la risorsa.

## <a name="offer-plan-doesnt-load"></a>Il piano dell'offerta non viene caricato

Questo errore potrebbe essere un problema intermittente con il portale di Azure. Provare a distribuire di nuovo l'offerta.

## <a name="unable-to-delete"></a>Impossibile eliminare

Se non si è in grado di eliminare le risorse confluenti, verificare di disporre delle autorizzazioni per eliminare la risorsa. È necessario avere la possibilità di eseguire azioni Microsoft. Confluent/*/Delete. Per informazioni sulla visualizzazione delle autorizzazioni, vedere [elencare le assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-list-portal.md).

Se si dispone delle autorizzazioni corrette ma non è ancora possibile eliminare la risorsa, contattare il [supporto tecnico](https://support.confluent.io). Questa condizione potrebbe essere correlata ai criteri di conservazione di confluenti. Il supporto tecnico può eliminare l'organizzazione e l'indirizzo di posta elettronica per l'utente.

## <a name="unable-to-use-single-sign-on"></a>Impossibile utilizzare Single Sign-On

Se SSO non funziona per il portale SaaS cloud Confluent, verificare che si stia usando il messaggio di posta elettronica Azure Active Directory corretto. È necessario avere anche il consenso per consentire l'accesso al portale di Software as a Service Cloud confluenti (SaaS). Per ulteriori informazioni, vedere la [guida Single Sign-on](manage.md#single-sign-on).

Se il problema persiste, contattare il [supporto tecnico](https://support.confluent.io).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [gestione dell'istanza](manage.md) di Apache Kafka per il cloud Confluent.
