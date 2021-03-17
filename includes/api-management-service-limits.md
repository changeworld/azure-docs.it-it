---
title: includere file
description: File di inclusione
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 02/19/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: ebaca1f39b16e4a06b5dcaa4e5f1de07122c6c89
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103621982"
---
| Risorsa | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Numero massimo di unità di scala | 10 per area<sup>1</sup> |
| Dimensioni cache | 5 GiB per unità<sup>2</sup> |
| Connessioni back-end simultanee<sup>3</sup> per autorità HTTP | 2\.048 per unità<sup>4</sup> |
| Dimensione massima della risposta memorizzata nella cache | 2 MiB |
| Dimensioni massime del documento dei criteri | 256 KiB<sup>5</sup> |
| Numero massimo di domini gateway personalizzati per istanza del servizio<sup>6</sup> | 20 |
| Numero massimo di certificati CA per istanza del servizio<sup>7</sup> | 10 |
| Numero massimo di istanze del servizio per sottoscrizione<sup>8</sup> | 20 |
| Numero massimo di sottoscrizioni per istanza del servizio<sup>8</sup> | 500 |
| Numero massimo di certificati client per istanza del servizio<sup>8</sup> | 50 |
| Numero massimo di API per istanza del servizio<sup>8</sup> | 50 |
| Numero massimo di operazioni API per istanza del servizio<sup>8</sup> | 1\.000 |
| Durata totale massima delle richieste<sup>8</sup> | 30 secondi |
| Dimensioni massime del payload memorizzato nel buffer<sup>8</sup> | 2 MiB |
| Dimensioni massime dell'URL delle richieste<sup>9</sup> | 4096 byte |
| Lunghezza massima del segmento di percorso URL<sup>10</sup> | 260 caratteri |
| Dimensioni massime dello schema API usato dai [criteri di convalida](../articles/api-management/validation-policies.md)<sup>10</sup> | 4 MB |
| Dimensione massima del corpo della richiesta o della risposta nei [criteri di convalida del contenuto](../articles/api-management/validation-policies.md#validate-content) | 100 kB |
| Numero massimo di gateway self-hosted<sup>11</sup> | 25 |

<sup>1</sup>I limiti di dimensionamento dipendono dal piano tariffario. Per informazioni sui piani tariffari e sui relativi limiti di dimensionamento, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>Le dimensioni della cache per unità dipendono dal piano tariffario. Per vedere i piani tariffari e i relativi limiti di dimensionamento, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>Le connessioni vengono raggruppate in pool e riutilizzate, a meno che non vengano chiuse esplicitamente dal back-end.<br/>
<sup>4</sup>Questo limite è per unità dei livelli Basic, Standard e Premium. Per il livello Developer il limite è 1.024. Questo limite non si applica al livello A consumo.<br/>
<sup>5</sup>Questo limite si applica ai livelli Basic, Standard e Premium. Nei criteri per il livello A consumo le dimensioni dei documenti sono limitate a 16 KiB.<br/>
<sup>6</sup>Più domini personalizzati sono supportati solo nei livelli Developer e Premium.<br/>
<sup>7</sup>I certificati CA non sono supportati nel livello A consumo.<br/>
<sup>8</sup>Questo limite si applica solo al livello A consumo. Per gli altri livelli non sono previsti limiti in queste categorie.<br/>
<sup>9</sup>Si applica solo al livello A consumo. Include fino a 2048 byte di lunghezza per la stringa di query.<br/>
<sup>10</sup> per aumentare questo limite, contattare il [supporto tecnico](https://azure.microsoft.com/support/options/).<br/>
<sup>11</sup>I gateway self-hosted sono supportati solo nei livelli Developer e Premium. Il limite si applica al numero di [risorse gateway self-hosted](/rest/api/apimanagement/2019-12-01/gateway). Per aumentare questo limite, contattare il [supporto](https://azure.microsoft.com/support/options/). Si noti che il numero di nodi (o repliche) associati a una risorsa gateway self-hosted è illimitato nel livello Premium e limitato a un singolo nodo nel livello Developer.