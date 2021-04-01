---
title: Panoramica sul cloud con Apache Kafka per le soluzioni di Azure partner
description: Informazioni sull'uso di Apache Kafka nel cloud Confluent in Azure Marketplace.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253533"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Che cos'è Apache Kafka per il cloud Confluent?

Apache Kafka per il cloud Confluent è un'offerta di Azure Marketplace che fornisce Apache Kafka come servizio. È completamente gestito, in modo da potersi concentrare sulla creazione di applicazioni piuttosto che sulla gestione dei cluster.

Per ridurre il carico di gestione multipiattaforma, Microsoft ha collaborato con il cloud per la creazione di un livello di provisioning integrato da Azure al cloud Confluent. Offre un'esperienza consolidata per l'uso del cloud Confluent in Azure. Puoi integrare e gestire con facilità il cloud con le tue applicazioni Azure.

In precedenza era necessario acquistare l'offerta cloud Confluent nel Marketplace e configurare separatamente l'account in un cloud Confluent. Per gestire le configurazioni e le risorse, è necessario spostarsi tra i portali per Azure e il cloud Confluent.

È ora possibile effettuare il provisioning delle risorse cloud confluenti tramite un provider di risorse denominato **Microsoft. Confluent**. È possibile creare e gestire le risorse dell'organizzazione cloud confluenti tramite l' [portale di Azure](https://portal.azure.com/), l'interfaccia della riga di comando di [Azure](/cli/azure/)o [Azure SDK](/azure/#languages-and-tools). Il cloud Confluent è proprietario ed esegue l'applicazione Software as a Service (SaaS), inclusi gli ambienti, i cluster, gli argomenti, le chiavi API e i connettori gestiti.

## <a name="capabilities"></a>Funzionalità

L'integrazione completa tra cloud e Azure è in grado di abilitare le funzionalità seguenti:

- Effettuare il provisioning di una nuova risorsa dell'organizzazione cloud Confluent dalla portale di Azure con l'infrastruttura completamente gestita.
- Semplifica la Single Sign-On (SSO) da Azure al cloud confluenti con Azure Active Directory (Azure AD). Non è necessaria alcuna autenticazione separata dal portale cloud Confluent.
- Ottieni una fatturazione unificata del consumo di cloud Confluent tramite la fatturazione della sottoscrizione di Azure.
- Gestire risorse cloud confluenti dal portale di Azure e tenerne traccia nella pagina **tutte le risorse** con le altre risorse di Azure.

## <a name="confluent-organization"></a>Organizzazione Confluent

Un'organizzazione Confluent è una risorsa che fornisce il mapping tra le risorse cloud di Azure e le risorse cloud confluenti. Si tratta della risorsa padre per altre risorse cloud confluenti.

Ogni sottoscrizione di Azure può contenere più piani fluenti. Ogni piano Confluent viene mappato a un account utente e a un'organizzazione nel portale di Confluent. All'interno di ogni organizzazione Confluent è possibile creare più ambienti, cluster, argomenti e connettori.

Quando si esegue il provisioning di una risorsa cloud Confluent in Azure, si ottiene un ID organizzazione, un ambiente predefinito e un account utente confluenti. Per altre informazioni, vedere [Guida introduttiva: Introduzione al cloud Confluent in Azure](create.md).

Per la fatturazione, ogni offerta di cloud Confluent acquistata nel Marketplace è mappata a un'organizzazione confluente univoca.

## <a name="single-sign-on"></a>Single sign-on

Quando si accede al portale di Azure, le credenziali vengono usate anche per accedere al portale SaaS cloud Confluent. L'esperienza USA [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) e [Azure ad SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) per fornire un modo sicuro e pratico per eseguire l'accesso.

Per ulteriori informazioni, vedere [Single Sign-on](manage.md#single-sign-on).

## <a name="billing"></a>Fatturazione

Sono disponibili due opzioni di fatturazione: piano mensile con pagamento in base al consumo e piano di impegno.

- Con il piano tariffario con **pagamento in base** al consumo, si ricevono gli addebiti per l'utilizzo del cloud per la fatturazione mensile di Azure.
- Con un **piano di impegno** è possibile iscriversi per ottenere un importo minimo di spesa e ottenere uno sconto sull'utilizzo vincolato del cloud Confluent.

Decidere quale opzione di fatturazione usare quando si crea il servizio.

## <a name="confluent-links"></a>Collegamenti confluenti

Per ulteriori informazioni sull'utilizzo di Apache Kafka per il cloud Confluent, vedere i collegamenti seguenti al [sito Confluent](https://docs.confluent.io/home/overview.html).

Per ulteriori informazioni sulle opzioni di fatturazione, vedere:

* [Azure Marketplace con pagamento in base al consumo](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Azure Marketplace con impegni](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Per ulteriori informazioni sulla gestione delle soluzioni, vedere:

* [Creazione di un cluster in un cloud Confluent](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Ambienti cloud confluenti](https://docs.confluent.io/current/cloud/using/environments.html)
* [Nozioni di base sul cloud](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Per informazioni sul supporto e sulle condizioni, vedere:

* [Supporto per la conoscenza](https://support.confluent.io)
* [Condizioni](https://www.confluent.io/confluent-cloud-tos)per il servizio.

## <a name="next-steps"></a>Passaggi successivi

Per creare un'istanza di Apache Kafka per il cloud Confluent, vedere [Guida introduttiva: Introduzione al cloud Confluent in Azure](create.md).
