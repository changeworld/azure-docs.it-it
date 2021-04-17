---
title: Archiviazione di Azure confronto degli strumenti di migrazione - Dati non strutturati
description: Funzionalità di base e confronto tra gli strumenti usati per la migrazione di dati non strutturati
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576534"
---
# <a name="comparison-matrix"></a>Matrice di confronto

La matrice di confronto seguente illustra le funzionalità di base di diversi strumenti che possono essere usati per la migrazione di dati non strutturati. 

## <a name="supported-azure-services"></a>Servizi di Azure supportati

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome soluzione**  | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione Gestione dati](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **File di Azure (tutti i livelli)** | Sì                          | Sì                      | Sì            | Sì                            |
| **Azure NetApp Files supporto**      | No                           | Sì                      | Sì            | Sì                            |
| **Supporto per blob di Azure ad accesso hot/cool**   | No                           | Sì (tramite anteprima NFS)    | Sì            | Sì                            |
| **Supporto del livello archivio BLOB di Azure** | No                           | No                       | No             | Sì (come destinazione della migrazione) |
| **Azure Data Lake Storage supporto** | No                           | No                       | No             | No                             |
| **Origini supportate**      | Windows Server 2012 R2 e sistemi | Nas & file system cloud | Qualsiasi NAS e S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Protocolli supportati (origine/destinazione)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome soluzione**   | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione Gestione dati](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Sì | Sì | Sì | Sì |
| **SMB 3.0**       | Sì | Sì | Sì | Sì |
| **SMB 3.1**       | Sì | Sì | Sì | Sì |
| **NFS v3**        | No  | Sì | Sì | Sì |
| **NFS v4.1**      | No  | Sì | No  | Sì |
| **Blob REST API** | No  | No  | Sì | Sì |
| **S3**            | No  | Sì | Sì | Sì |

## <a name="extended-features"></a>Funzionalità estese

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nome soluzione**  | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione Gestione dati](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Modifica mapping UID/SID**                   | No  | Sì                        | Sì | No                             |
| **Nuovo mapping ACL del protocollo**                | No  | No                         | No  | No                             |
| **Supporto DFS**                           | Sì | Sì                        | Sì | Sì                            |
| **Supporto della limitazione della larghezza di banda della rete**                    | Sì | Sì                        | Sì | Sì                            |
| **Esclusioni di modelli di file**               | No  | Sì                        | Sì | Sì (usando la funzionalità di copia) |
| **Supporto per gli attributi di file selettivi** | Sì | Sì                        | Sì | Sì (per gli attributi estesi)  |
| **Elimina propagazioni**                   | Sì | Sì                        | Sì | Sì                            |
| **Seguire le giunzioni NTFS**                 | No  | Sì                        | No  | Sì                            |
| **Eseguire l'override del proprietario SMB e del proprietario del gruppo**    | Sì | Sì                        | Sì | No                             |
| **Segnalazione della catena di custodia**            | No  | Sì                        | No  | Sì                            |
| **Supporto per flussi di dati alternativi**    | No  | Sì                        | Sì | No                             |
| **Pianificazione per la migrazione**              | No  | Sì                        | Sì | Sì                            |
| **Mantenimento dell'ACL**                        | No  | Sì                        | Sì | Sì                            |
| **Supporto DACL**                          | Sì | Sì                        | Sì | Sì                            |
| **Supporto SACL**                          | Sì | Sì                        | Sì | No                             |
| **Mantenimento del tempo di accesso**                | Sì | Sì                        | Sì | Sì                            |
| **Mantenimento dell'ora modificata**              | Sì | Sì                        | Sì | Sì                            |
| **Mantenimento dell'ora di creazione**              | No  | Sì                        | Sì | Sì                            |
| **Azure Data Box supporto**       | Sì | Sì                        | No  | No                             |
| **Migrazione di snapshot**                | No  | Manuale                     | Sì | No                             |
| **Supporto dei collegamenti simbolici**                 | No  | Sì                        | No  | Sì                            |
| **Supporto dei collegamenti rigidi**                     | No  | Di cui è stata eseguita la migrazione come file separati | Sì | Sì                            |
| **Supporto per i file aperti/bloccati**       | Sì | Sì                        | Sì | Sì                            |
| **Migrazione incrementale**                 | Sì | Sì                        | Sì | Sì                            |
| **Supporto per il passaggio**                    | No  | Sì                        | Sì | No (solo manuale)               |
| **[Altre funzionalità](#other-features)**         | [Collegamento](#azure-file-sync)| [Collegamento](#dobimigrate) | [Collegamento](#data-mobility-and-migration) | [Collegamento](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Valutazione e creazione di report

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome soluzione**   | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione Gestione dati](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capacity**                        | No      | Sì | Sì | Sì            |
| **N. di file/cartelle**            | No      | Sì | Sì | Sì            |
| **Distribuzione dell'età nel tempo**      | No      | Sì | Sì | Sì            |
| **Ora di accesso**                     | No      | Sì | Sì | Sì            |
| **Ora di modifica**                   | No      | Sì | Sì | Sì            |
| **Ora di creazione**                   | No      | Sì | Sì | Sì            |
| **Stato del report per file/oggetto** | Partial | Sì | Sì | Sì            |

## <a name="licensing"></a>Gestione delle licenze

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Dynamics dei dati](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nome soluzione**   | [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilità e migrazione dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestione Gestione dati](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N / A | Sì | Sì | Sì |
| **Impegno di Azure** | Sì   | Sì | Sì | Sì |

## <a name="other-features"></a>Altre funzionalità

### <a name="azure-file-sync"></a>Sincronizzazione file di Azure

- Convalida hash interna

### <a name="dobimigrate"></a>DobiMigrate

- Controlli preliminari della migrazione
- Pianificazione della migrazione
- Prova di prova per il test cut over
- Rilevare e avvisare sull'attività dell'utente sul lato destinazione prima del cut over
- Migrazioni guidate da criteri
- Iterazioni di copia pianificate
- Opzioni configurabili per la gestione della sicurezza della directory radice
- Esecuzioni di verifica su richiesta
- Verifica della lettura dei dati nell'origine e nella destinazione
- Flusso di lavoro di gestione degli errori interattivo e grafico
- Possibilità di limitare la propagazione di determinate operazioni, ad esempio eliminazioni e aggiornamenti
- Possibilità di mantenere il tempo di accesso nell'origine (oltre alla destinazione)
- Possibilità di eseguire il rollback all'origine durante il passaggio alla migrazione
- Possibilità di eseguire la migrazione degli attributi di file SMB selezionati
- Possibilità di pulire i descrittori di sicurezza NTFS
- Possibilità di eseguire l'override delle autorizzazioni NFSv3 e scrivere nuovi bit in modalità di destinazione
- Possibilità di convertire NFSv3 POSIX draft ACLS in ACLS NFSv4
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilità e migrazione dei dati

- Convalida dell'hash

### <a name="intelligent-data-management"></a>Gestione Gestione dati

- Migrazioni basate su progetto/directory
- Ripetizione automatica degli errori
- Valutazione/creazione di report: tipi di file, dimensioni del file, basate su progetto
- Valutazione/creazione di report: ricerche personalizzate basate su metadati
- Soluzione di gestione completa del ciclo di vita dei dati per archiviazione, replica, analisi
- Accedere all'analisi basata sul tempo su DATI BLOB e S3
- Assegnazione di tag
- Supporto 24 x 7 x 365
- Convalida hash

*L'elenco è stato verificato l'ultima volta il 31 marzo 2021.*

## <a name="see-also"></a>Vedi anche

- [Panoramica della migrazione dell'archiviazione](../../../common/storage-migration-overview.md)
- [Scegliere una soluzione di Azure per il trasferimento dei dati](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Eseguire la migrazione a condivisioni file di Azure](/azure/storage/files/storage-files-migration-overview)
- [Eseguire la migrazione Data Lake Storage con WANdisco LiveData Platform per Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Copiare o spostare dati in Archiviazione di Azure con AzCopy](https://aka.ms/azcopy)
- [Eseguire la migrazione di set di dati di Archiviazione BLOB di Azure con AzReplicate (applicazione di esempio)](https://github.com/Azure/AzReplicate/tree/master/)
