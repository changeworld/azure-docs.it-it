---
title: Limiti delle risorse per Azure NetApp Files | Microsoft Docs
description: Descrive i limiti per le Azure NetApp Files e come richiedere l'aumento del limite di risorse.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: f023bfa2b3941f7d667f4be34a8ee8dc1ed9a9c3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750195"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files

Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

## <a name="resource-limits"></a>Limiti delle risorse

La tabella seguente descrive i limiti delle risorse per Azure NetApp Files:

|  Risorsa  |  Limite predefinito  |  Regolabile tramite richiesta di supporto  |
|----------------|---------------------|--------------------------------------|
|  Numero di account NetApp per area di Azure per sottoscrizione  |  10    |  Sì   |
|  Numero di pool di capacità per account NetApp   |    25     |   Sì   |
|  Numero di volumi per sottoscrizione   |    500     |   Sì   |
|  Numero di volumi per pool di capacità     |    500   |    Sì     |
|  Numero di snapshot per volume       |    255     |    No        |
|  Numero di subnet delegate ai Azure NetApp Files (Microsoft.NetApp/volumes) per ogni rete virtuale di Azure    |   1   |    No    |
|  Numero di IP usati in una rete virtuale (incluse le reti virtuali con peer immediato) con Azure NetApp Files   |    1000   |    No   |
|  Dimensioni minime di un singolo pool di capacità   |  4 TiB     |    No  |
|  Dimensioni massime di un singolo pool di capacità    |  500 TiB   |   No   |
|  Dimensioni minime di un singolo volume    |    100 GiB    |    No    |
|  Dimensioni massime di un singolo volume     |    100 TiB    |    No    |
|  Dimensioni massime di un singolo file     |    16 TiB    |    No    |    
|  Dimensioni massime dei metadati della directory in una singola directory      |    320 MB    |    No    |    
|  Numero massimo di file ([maxfiles](#maxfiles)) per volume     |    100 milioni    |    Sì    |    
|  Numero massimo di regole dei criteri di esportazione per volume     |    5  |    No    | 
|  Velocità effettiva minima assegnata per un volume QoS manuale     |    1 MiB/s   |    No    |    
|  Velocità effettiva massima assegnata per un volume QoS manuale     |    4.500 MiB/s    |    No    |    
|  Numero di volumi di protezione dei dati di replica tra aree (volumi di destinazione)     |    5    |    Sì    |     

Per verificare se una directory sta per essere avvicinata al limite di dimensioni massime per i metadati della directory (320 MB), vedere Ricerca per categorie determinare se una directory sta per essere avvicinata alla dimensione [massima.](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)   

Per altre informazioni, vedere Domande frequenti sulla [gestione della capacità](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Limiti del numero massimo di file <a name="maxfiles"></a> 

Azure NetApp Files volumi hanno un limite denominato *maxfiles*. Il limite maxfiles è il numero di file che un volume può contenere. I file system Linux fanno riferimento al limite *come inodes*. Il limite maxfiles per un volume Azure NetApp Files viene indicizzato in base alle dimensioni (quota) del volume. Il limite maxfiles per un volume aumenta o diminuisce alla velocità di 20 milioni di file per TiB delle dimensioni del volume di cui è stato effettuato il provisioning. 

Il servizio regola dinamicamente il limite maxfiles per un volume in base alle dimensioni di cui è stato effettuato il provisioning. Ad esempio, un volume configurato inizialmente con dimensioni di 1 TiB avrebbe un limite massimo di 20 milioni di file. Le successive modifiche alle dimensioni del volume comportano una modifica automatica del limite maxfiles in base alle regole seguenti: 

|    Dimensioni volume (quota)     |  Correzione automatica del limite maxfiles    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 milioni     |
|    > 1 TiB ma <= 2 TiB    |    40 milioni     |
|    > 2 TiB ma <= 3 TiB    |    60 milioni     |
|    > 3 TiB ma <= 4 TiB    |    80 milioni     |
|    > 4 TiB                 |    100 milioni    |

Se sono già stati allocati almeno 4 TiB di [](#limit_increase) quota per un volume, è possibile avviare una richiesta di supporto per aumentare il limite maxfiles (inodes) oltre 100 milioni. Per ogni 100 milioni di file aumentati (o una frazione), è necessario aumentare la quota di volume corrispondente di 4 TiB.  Ad esempio, se si aumenta il limite maxfiles da 100 milioni di file a 200 milioni di file (o qualsiasi numero compreso tra), è necessario aumentare la quota del volume da 4 TiB a 8 TiB.

È possibile aumentare il limite di maxfiles a 500 milioni se la quota del volume è di almeno 20 TiB. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Aumento del limite di richieste <a name="limit_increase"></a> 

È possibile creare una supporto tecnico di Azure richiesta per aumentare i limiti modificabili dalla tabella precedente. 

Dal portale di Azure di navigazione: 

1. Fare **clic su Guida e supporto.**
2. Fare **clic su + Nuova richiesta di supporto.**
3. Nella scheda Nozioni di base specificare le informazioni seguenti: 
    1. Tipo di problema: selezionare **Limiti del servizio e della sottoscrizione (quote).**
    2. Sottoscrizioni: selezionare la sottoscrizione per la risorsa per cui è necessario aumentare la quota.
    3. Tipo di quota: selezionare **Archiviazione: Azure NetApp Files limiti**.
    4. Fare **clic su Avanti: Soluzioni**.
4. Nella scheda Dettagli:
    1. Nella casella Descrizione specificare le informazioni seguenti per il tipo di risorsa corrispondente:

        |  Risorsa  |    Risorse padre      |    Nuovi limiti richiesti     |    Motivo dell'aumento della quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *ID sottoscrizione*   |  *Nuovo numero massimo **di account** richiesto*    |  *Quale scenario o caso d'uso ha richiesto la richiesta?*  |
        |  Pool    |  *ID sottoscrizione, URI dell'account NetApp*  |  *Nuovo numero massimo **di pool** richiesto*   |  *Quale scenario o caso d'uso ha richiesto la richiesta?*  |
        |  Volume  |  *ID sottoscrizione, URI dell'account NetApp, URI pool di capacità*   |  *Nuovo numero massimo **di volume** richiesto*     |  *Quale scenario o caso d'uso ha richiesto la richiesta?*  |
        |  Maxfiles  |  *ID sottoscrizione, URI dell'account NetApp, URI pool di capacità, URI del volume*   |  *Nuovo numero **massimo di file** richiesto*     |  *Quale scenario o caso d'uso ha richiesto la richiesta?*  |    
        |  Volumi di protezione dei dati di replica tra aree  |  *ID sottoscrizione, URI dell'account NetApp di destinazione, URI del pool di capacità di destinazione, URI dell'account NetApp di origine, URI del pool di capacità di origine, URI del volume di origine*   |  *Richiesto nuovo numero massimo di volumi di protezione dei dati di replica tra aree **(volumi di destinazione)** _     |  _What scenario o caso d'uso ha richiesto la richiesta?*  |    

    2. Specificare il metodo di supporto appropriato e specificare le informazioni sul contratto.

    3. Fare **clic su Avanti: Rivedi e crea** per creare la richiesta. 


## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modello di costi per Azure NetApp Files](azure-netapp-files-cost-model.md)
