---
title: Confronto degli strumenti di migrazione per archiviazione di Azure-dati non strutturati
description: Funzionalità di base e confronto tra gli strumenti utilizzati per la migrazione di dati non strutturati
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231586"
---
# <a name="comparison-matrix"></a>Matrice di confronto

Nella matrice di confronto seguente sono illustrate le funzionalità di base dei diversi strumenti che possono essere utilizzati per la migrazione di dati non strutturati. 

## <a name="supported-azure-services"></a>Servizi di Azure supportati

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome soluzione**  | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione dati intelligente](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Supporto File di Azure (tutti i livelli)** | Sì                          | Sì                      | Sì            | Sì                            |
| **Supporto Azure NetApp Files**      | No                           | Sì                      | Sì            | Sì                            |
| **Supporto ad accesso frequente/sporadico BLOB di Azure**   | No                           | Sì (tramite NFS Preview)    | Sì            | Sì                            |
| **Supporto del livello archivio BLOB di Azure** | No                           | No                       | No             | Sì (come destinazione della migrazione) |
| **Supporto Azure Data Lake Storage** | No                           | No                       | No             | No                             |
| **Origini supportate**      | Windows Server 2012 R2 e su | File System & NAS cloud | Qualsiasi NAS e S3 | NAS, BLOB, S3                  |

## <a name="supported-protocols-source--destination"></a>Protocolli supportati (origine/destinazione)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome soluzione**   | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione dati intelligente](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Sì | Sì | Sì | Sì |
| **SMB 3.0**       | Sì | Sì | Sì | Sì |
| **SMB 3,1**       | Sì | Sì | Sì | Sì |
| **NFS v3**        | No  | Sì | Sì | Sì |
| **NFS v 4.1**      | No  | Sì | No  | Sì |
| **API REST BLOB** | No  | No  | Sì | Sì |
| **S3**            | No  | Sì | Sì | Sì |

## <a name="extended-features"></a>Funzionalità estese

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome soluzione**  | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione dati intelligente](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Rimapping di UID/SID**                   | No  | Sì                        | Sì | No                             |
| **Modifica mapping ACL protocollo**                | No  | No                         | No  | No                             |
| **Supporto DFS**                           | Sì | Sì                        | Sì | Sì                            |
| **Supporto della limitazione della larghezza di banda della rete**                    | Sì | Sì                        | Sì | Sì                            |
| **Esclusioni del modello di file**               | No  | Sì                        | Sì | Sì (usando la funzionalità di copia) |
| **Supporto per attributi di file selettivi** | Sì | Sì                        | Sì | Sì (per gli attributi estesi)  |
| **Elimina propagazioni**                   | Sì | Sì                        | Sì | Sì                            |
| **Segui giunzioni NTFS**                 | No  | Sì                        | No  | Sì                            |
| **Ignora proprietario SMB e proprietario gruppo**    | Sì | Sì                        | Sì | No                             |
| **Catena di report di custodia**            | No  | Sì                        | No  | Sì                            |
| **Supporto per flussi di dati alternativi**    | No  | Sì                        | Sì | No                             |
| **Pianificazione per la migrazione**              | No  | Sì                        | Sì | Sì                            |
| **Mantenimento dell'ACL**                        | No  | Sì                        | Sì | Sì                            |
| **Supporto per DACL**                          | Sì | Sì                        | Sì | Sì                            |
| **Supporto SACL**                          | Sì | Sì                        | Sì | No                             |
| **Mantenimento del tempo di accesso**                | Sì | Sì                        | Sì | Sì                            |
| **Mantenimento dell'ora modificata**              | Sì | Sì                        | Sì | Sì                            |
| **Conservazione dell'ora di creazione**              | No  | Sì                        | Sì | Sì                            |
| **Supporto Azure Data Box**       | Sì | Sì                        | No  | No                             |
| **Migrazione di snapshot**                | No  | Manuale                     | Sì | No                             |
| **Supporto del collegamento simbolico**                 | No  | Sì                        | No  | Sì                            |
| **Supporto per collegamenti reali**                     | No  | Migrazione eseguita come file separati | Sì | Sì                            |
| **Supporto per i file aperti/bloccati**       | Sì | Sì                        | Sì | Sì                            |
| **Migrazione incrementale**                 | Sì | Sì                        | Sì | Sì                            |
| **Supporto dello switchover**                    | No  | Sì                        | Sì | No (solo manuale)               |
| **[Altre funzionalità](#other-features)**         | [Collegamento](#azure-file-sync)| [Collegamento](#dobimigrate) | [Collegamento](#data-mobility-and-migration) | [Collegamento](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Valutazione e creazione di report

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome soluzione**   | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione dati intelligente](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capacity**                        | No      | Sì | Sì | Sì            |
| **file/cartelle**            | No      | Sì | Sì | Sì            |
| **Distribuzione Age nel tempo**      | No      | Sì | Sì | Sì            |
| **Tempo di accesso**                     | No      | Sì | Sì | Sì            |
| **Ora di modifica**                   | No      | Sì | Sì | Sì            |
| **Ora creazione**                   | No      | Sì | Sì | Sì (solo SMB) |
| **Stato rapporto per file/oggetto** | Parziale | Sì | Sì | Sì            |

## <a name="licensing"></a>Gestione delle licenze

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome soluzione**   | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione dati intelligente](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N/A | Sì | Sì | Sì |
| **Impegno di Azure** | Sì   | Sì | Sì | Sì |

## <a name="other-features"></a>Altre funzionalità

### <a name="azure-file-sync"></a>Sincronizzazione file di Azure

- Convalida hash interna

### <a name="dobimigrate"></a>DobiMigrate

- Verifiche preliminari della migrazione
- Pianificazione della migrazione
- Esecuzione a secco per il superamento del test
- Rilevamento e avviso sull'attività dell'utente sul lato di destinazione prima del trasferimento
- Migrazioni basate su criteri
- Iterazioni di copia pianificate
- Opzioni configurabili per la gestione della sicurezza della directory radice
- Esecuzioni della verifica su richiesta
- Verifica della rilettura dei dati nell'origine e nella destinazione
- Flusso di lavoro di gestione degli errori grafico e interattivo
- Possibilità di limitare determinate operazioni dalla propagazione come eliminazioni e aggiornamenti
- Possibilità di mantenere l'ora di accesso nell'origine (oltre alla destinazione)
- Possibilità di eseguire il rollback all'origine durante lo switchover della migrazione
- Possibilità di eseguire la migrazione degli attributi di file SMB selezionati
- Possibilità di pulire i descrittori di sicurezza NTFS
- Possibilità di eseguire l'override delle autorizzazioni NFSv3 e di scrivere nuovi bit in modalità di destinazione
- Possibilità di convertire ACL bozza NFSv3 POSIX in ACL NFSv4
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilità e migrazione dei dati

- Convalida hash

### <a name="intelligent-data-management"></a>Gestione dati intelligente

- Migrazioni basate su progetto/directory
- Tentativi automatici di errori
- Valutazione/creazione di report: tipi di file, dimensioni del file, basate su progetti
- Valutazione/creazione di report: ricerche personalizzate basate sui metadati
- Soluzione completa di gestione del ciclo di vita dei dati per archiviazione, replica e analisi
- Accedere a analisi basate sul tempo su BLOB, dati S3
- Assegnazione di tag
- Supporto 24 x 7 x 365
- Convalida hash

*L'ultimo elenco è stato verificato il 31 marzo 2021.*

## <a name="see-also"></a>Vedi anche

- [Panoramica della migrazione dell'archiviazione](../../../common/storage-migration-overview.md)
- [Scegliere una soluzione di Azure per il trasferimento dei dati](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Eseguire la migrazione a condivisioni file di Azure](/azure/storage/files/storage-files-migration-overview)
- [Eseguire la migrazione a Data Lake Storage con la piattaforma WANdisco LiveData per Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Copiare o spostare dati in archiviazione di Azure con AzCopy](https://aka.ms/azcopy)
- [Eseguire la migrazione di set di impostazioni di grandi dimensioni nell'archiviazione BLOB di Azure con AzReplicate (applicazione di esempio)](https://github.com/Azure/AzReplicate/tree/master/)
