---
title: App e distribuzione nel cloud Spring di Azure
description: In questo argomento viene illustrata la differenza tra l'applicazione e la distribuzione nel cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5473daedc8a7ad5a3b6ddffc65234160d4b3019d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878596"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>App e distribuzione nel cloud Spring di Azure

**Questo articolo si applica a:** ✔️ Java ✔️ C#

**App** e **distribuzione** sono i due concetti chiave del modello di risorse di Azure Spring cloud. Nel cloud Spring di Azure un' *app* è un'astrazione di un'app aziendale o di un microservizio.  Una versione di codice o binario distribuita quando l' *app* viene eseguita in una *distribuzione*.  Le app vengono eseguite in un' *istanza del servizio cloud di Azure Spring* o semplicemente in un' *istanza del servizio*, come illustrato di seguito.

 ![App e distribuzioni](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

È possibile avere più istanze del servizio all'interno di una singola sottoscrizione di Azure, ma il servizio cloud Spring è più semplice da usare quando tutte le app che costituiscono un'app aziendale o un microservizio risiedono all'interno di una singola istanza del servizio.

Il livello standard di Azure Spring cloud consente a un'app di disporre di una distribuzione di produzione e di una distribuzione di gestione temporanea, in modo che sia possibile eseguire facilmente la distribuzione Blue/Green.

## <a name="app"></a>App
Le seguenti funzionalità/proprietà sono definite a livello di app.

| Funzionalità | Descrizione |
|:--|:----------------|
| Pubblico</br>Endpoint | URL per accedere all'app |
| Personalizzato</br>Dominio | Record CNAME che protegge il dominio personalizzato |
| Servizio</br>Binding | Connessione predefinita con altri servizi di Azure |
| Gestita</br>Identità | L'identità gestita da Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da Azure AD, ad esempio Azure Key Vault |
| Persistente</br>Archiviazione | Impostazione che consente la permanenza dei dati oltre il riavvio dell'app |

## <a name="deployment"></a>Distribuzione

Le seguenti funzionalità/proprietà sono definite a livello di distribuzione e verranno scambiate durante lo scambio della distribuzione di produzione/gestione temporanea.

| Funzionalità | Descrizione |
|:--|:----------------|
| CPU | Numero di Vcore per ogni istanza dell'app |
| Memoria | GB di memoria per ogni istanza dell'app|
| Istanza</br>Conteggio | Il numero di istanze dell'app, impostate manualmente o automaticamente |
| Ridimensionamento automatico | Ridimensionare automaticamente il numero di istanze in base a regole e pianificazioni predefinite |
| JVM</br>Opzioni | Impostazione delle opzioni JVM  |
| Ambiente</br>Variabili | Impostazioni delle variabili di ambiente |
| Runtime</br>Versione | Java 8/Java 11|

## <a name="restrictions"></a>Restrizioni

* **Un'app deve avere una distribuzione di produzione**: l'eliminazione di una distribuzione di produzione è bloccata dall'API. Deve essere scambiata in staging prima dell'eliminazione.
* **Un'app può avere al massimo due distribuzioni**: la creazione di più di due distribuzioni è bloccata dall'API. Distribuire il nuovo file binario in una distribuzione di produzione o di gestione temporanea esistente.
* **Gestione della distribuzione non è disponibile nel livello Basic**: usare il livello standard per la funzionalità di distribuzione Blue-Green.

## <a name="see-also"></a>Vedi anche
* [Configurare un ambiente di staging nel cloud Spring di Azure](spring-cloud-howto-staging-environment.md)
