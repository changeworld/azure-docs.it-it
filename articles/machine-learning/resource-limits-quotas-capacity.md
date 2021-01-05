---
title: Limiti del servizio
titleSuffix: Azure Machine Learning
description: Limiti di servizio usati per la pianificazione della capacità e limiti massimi per richieste e risposte per Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: 132c4b223b99f7110cd4553bb0dd93f1e58f4cdd
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733325"
---
# <a name="service-limits-in-azure-machine-learning"></a>Limiti del servizio in Azure Machine Learning

In questa sezione vengono elencate le quote di base e le soglie di limitazione in Azure Machine Learning.

## <a name="workspaces"></a>Aree di lavoro
| Limite | valore |
| --- | --- |
| Nome dell'area di lavoro | 2-32 caratteri |

## <a name="runs"></a>Esecuzioni
| Limite | valore |
| --- | --- |
| Esecuzioni per area di lavoro | 10 milioni |
| RunId/ParentRunId | 256 caratteri |
| DataContainerId | 261 caratteri |
| DisplayName |256 caratteri|
| Descrizione |5.000 caratteri|
| Numero di proprietà |50 |
| Lunghezza della chiave della proprietà |100 caratteri |
| Lunghezza del valore della proprietà |1.000 caratteri |
| Numero di tag |50 |
| Lunghezza della chiave del tag |100 |
| Lunghezza del valore del tag |1.000 caratteri |
| CancelUri / CompleteUri / DiagnosticsUri |1.000 caratteri |
| Lunghezza del messaggio di errore |3.000 caratteri |
| Lunghezza messaggio di avviso |300 caratteri |
| Numero di set di dati di input |200 |
| Numero di set di risultati di output |20 |


## <a name="metrics"></a>Metriche
| Limite | valore |
| --- | --- |
| Nomi delle metriche per esecuzione |50|
| Righe metrica per nome metrica |10 milioni|
| Colonne per riga metrica |15|
| Lunghezza del nome della colonna metrica |255 caratteri |
| Lunghezza del valore della colonna metrica |255 caratteri |
| Righe metriche per batch caricate | 250 |

> [!NOTE]
> Se si sta raggiungendo il limite di nomi di metrica per esecuzione perché si sta formattando le variabili nel nome della metrica, è consigliabile usare invece una metrica di riga in cui una colonna è il valore della variabile e la seconda colonna è il valore della metrica.

## <a name="artifacts"></a>Artifacts

| Limite | valore |
| --- | --- |
| Numero di artefatti per esecuzione |10 milioni|
| Lunghezza massima del percorso dell'artefatto |5.000 caratteri |

## <a name="limit-increases"></a>Limiti aumentati
È possibile aumentare i limiti per le singole aree di lavoro [contattando il supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'ambiente di Azure Machine Learning](how-to-configure-environment.md)
