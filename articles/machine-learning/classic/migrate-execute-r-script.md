---
title: 'ML Studio (classico): eseguire la migrazione a Azure Machine Learning eseguire uno script R'
description: Ricompilare Studio (classico) eseguire i moduli di script R da eseguire su Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565033"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Eseguire la migrazione di moduli Execute R script in studio (versione classica)

In questo articolo si apprenderà come ricompilare un modulo **Execute R script** (classico) in Azure Machine Learning.

Per altre informazioni sulla migrazione da studio (classico), vedere l' [articolo Panoramica della migrazione](migrate-overview.md).

## <a name="execute-r-script"></a>Execute R Script

Azure Machine Learning Designer viene ora eseguito in Linux. Studio (classico) viene eseguito in Windows. A causa della modifica della piattaforma, è necessario modificare lo **script Execute R** durante la migrazione. in caso contrario, la pipeline avrà esito negativo.

Per eseguire la migrazione di un modulo **Execute R script** da studio (classico), è necessario sostituire le `maml.mapInputPort` `maml.mapOutputPort` interfacce e con le funzioni standard.

La tabella seguente riepiloga le modifiche apportate al modulo di script R:

|Funzionalità|Studio (versione classica)|Finestra di progettazione di Azure Machine Learning|
|---|---|---|
|Interfaccia script|`maml.mapInputPort` e `maml.mapOutputPort`|Interfaccia Function|
|Piattaforma|Windows|Linux|
|Internet accessibile |No|Sì|
|Memoria|14 GB|Dipendente dallo SKU di calcolo|

### <a name="how-to-update-the-r-script-interface"></a>Come aggiornare l'interfaccia di script R

Ecco il contenuto di un modulo **Execute R script** di esempio in studio (classico):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Ecco il contenuto aggiornato nella finestra di progettazione. Si noti che `maml.mapInputPort` e `maml.mapOutputPort` sono stati sostituiti con l'interfaccia della funzione standard `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Per altre informazioni, vedere il [riferimento al modulo Execute R script](../algorithm-module-reference/execute-r-script.md)di progettazione.

### <a name="install-r-packages-from-the-internet"></a>Installare i pacchetti R da Internet

Azure Machine Learning Designer consente di installare i pacchetti direttamente da CRAN.

Si tratta di un miglioramento rispetto a Studio (classico). Poiché Studio (classico) viene eseguito in un ambiente sandbox senza accesso a Internet, è necessario caricare gli script in un bundle zip per installare altri pacchetti. 

Usare il codice seguente per installare i pacchetti CRAN nel modulo **Execute R script** della finestra di progettazione:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire la migrazione di moduli Execute R script a Azure Machine Learning.

Vedere gli altri articoli della serie di migrazione Studio (classica):

1. [Panoramica sulla migrazione](migrate-overview.md).
1. [Eseguire la migrazione del set di dati](migrate-register-dataset.md).
1. [Ricompilare una pipeline di training di studio (classica)](migrate-rebuild-experiment.md).
1. [Ricompilare un servizio Web di studio (classico)](migrate-rebuild-web-service.md).
1. [Integrare un servizio web Azure Machine Learning con le app client](migrate-rebuild-integrate-with-client-app.md).
1. Eseguire la **migrazione dei moduli Execute R script**.