---
title: Gestire una richiesta di supporto in Azure
description: Viene descritto come visualizzare le richieste di supporto, inviare messaggi, modificare il livello di gravità della richiesta, condividere le informazioni di diagnostica con il supporto di Azure, riaprire una richiesta di supporto chiusa e caricare i file.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 8110f87401da1352309fb55615093d49981c754d
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504815"
---
# <a name="manage-an-azure-support-request"></a>Gestire una richiesta di supporto in Azure

Dopo aver [creato una richiesta di supporto tecnico di Azure](how-to-create-azure-support-request.md), è possibile gestirla nel [portale di Azure](https://portal.azure.com), come spiegato in questo articolo. È anche possibile creare e gestire le richieste a livello programmatico, mediante l'[API REST per i ticket di supporto tecnico di Azure](/rest/api/support).

## <a name="view-support-requests"></a>Visualizzare le richieste di supporto

Per visualizzare i dettagli e lo stato delle richieste di supporto, passare a **Guida e supporto** per  >   **tutte le richieste di supporto**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Tutte le richieste di supporto":::

In questa pagina è possibile cercare, filtrare e ordinare le richieste di supporto. Selezionare una richiesta di supporto per visualizzare i dettagli, inclusi la gravità e i messaggi associati alla richiesta.

## <a name="send-a-message"></a>Inviare un messaggio

1. Nella pagina **tutte le richieste di supporto** selezionare la richiesta di supporto.

1. Nella pagina **richiesta di supporto** selezionare **nuovo messaggio**.

1. Immettere il messaggio e selezionare **Invia**.

## <a name="change-the-severity-level"></a>Modificare il livello di gravità

> [!NOTE]
> Il livello di gravità massimo dipende dal [piano di supporto](https://azure.microsoft.com/support/plans).
>

1. Nella pagina **tutte le richieste di supporto** selezionare la richiesta di supporto.

1. Nella pagina **richiesta di supporto** selezionare **Cambia**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Modificare la gravità della richiesta di supporto":::

1. Il portale di Azure Mostra una delle due schermate, a seconda se la richiesta è già stata assegnata a un tecnico del supporto:

    - Se la richiesta non è stata assegnata, viene visualizzata una schermata simile alla seguente. Selezionare un nuovo livello di gravità, quindi selezionare **Cambia**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Selezionare un nuovo livello di gravità":::

    - Se la richiesta è stata assegnata, viene visualizzata una schermata simile alla seguente. Selezionare **OK**, quindi crea un [nuovo messaggio](#send-a-message) per richiedere una modifica a livello di gravità.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Non è possibile selezionare un nuovo livello di gravità":::

## <a name="share-diagnostic-information-with-azure-support"></a>Condividi le informazioni di diagnostica con il supporto di Azure

Quando si crea una richiesta di supporto, per impostazione predefinita viene selezionata l'opzione **Condividi informazioni di diagnostica** . Questo consente al supporto di Azure di raccogliere [informazioni di diagnostica](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) dalle risorse di Azure:

* Non è possibile deselezionare questa opzione dopo la creazione di una richiesta.

* Se l'opzione è stata deselezionata durante la creazione di una richiesta, è possibile selezionarla dopo la creazione della richiesta.

    1. Nella pagina **tutte le richieste di supporto** selezionare la richiesta di supporto.
    
    1. Nella pagina **richiesta di supporto** selezionare **Concedi autorizzazione**, quindi selezionare **Sì** e **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Concedere le autorizzazioni per le informazioni di diagnostica":::

## <a name="upload-files"></a>Caricare i file

È possibile usare l'opzione di caricamento dei file per caricare i file di diagnostica o qualsiasi altro file ritenuto pertinente per una richiesta di supporto.

1. Nella pagina **tutte le richieste di supporto** selezionare la richiesta di supporto.

1. Nella pagina **richiesta di supporto** cercare il file e quindi selezionare **carica**. Ripetere il processo se si dispone di più file.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Caricare un file":::

### <a name="file-upload-guidelines"></a>Linee guida per il caricamento di file

Quando si usa l'opzione di caricamento file, seguire queste linee guida:

* Per proteggere la privacy degli utenti, non includere informazioni personali nel caricamento.
* Il nome del file non deve superare i 110 caratteri.
* Non è possibile caricare più di un file.
* I file non possono essere superiori a 4 MB.
* Tutti i file devono avere un'estensione del nome file, ad esempio *docx* o *xlsx*. Nella tabella seguente vengono illustrate le estensioni del nome file consentite per il caricamento.

| 0-9, A-C     | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| 7z         | dat  | . har        | odx  | rar     | tdb       | xlam   |
| .a          | db   | hwl        | oft  | rdl     | tdf       | xlr    |
| abc        | DMP  | .ics        | old  | rdlc    | text      | xls    |
| adm        | do_  | .ini        | one  | re_     | thmx      | xlsb   |
| .aspx       | doc  | .java       | osd  | . reg     | tif       | xlsm   |
| ATF        | docm | jpg        | OUT  | remove  | trc       | xlsx   |
| b          | docx | LDF        | p1   | ren     | TTD       | xlt    |
| ba_        | dotm | letterhead | pcap | rename  | tx_       | xltx   |
| bak        | dotx | lnk        | pdb  | rft     | .txt       | xml    |
| .bat        | dtsx | lo_        | pdf  | rpt     | uccapilog | xmla   |
| blg        | eds  | .log        | piz  | rte     | uccplog   | xps    |
| CA_        | emf  | lpk        | pmls | .rtf     | udcx      | xsd    |
| CAB        | eml  | manifest   | png  | run     | vb_       | xsn    |
| cap        | emz  | master     | potx | saz     | vbs_      | xxx    |
| catx       | err  | mdmp       | ppt  | sql     | vcf       | z_     |
| CFG        | etl  | mof        | pptm | sqlplan | vsd       | z01    |
| compressed | evt  | mp3        | pptx | stp     | wdb       | z02    |
| Config     | evtx | mpg        | prn  | svclog  | wks       | zi     |
| cpk        | EX   | ms_        | psf  | -        | wma       | zi_    |
| .cpp        | ex_  | msg        | pst  | -        | wmv       | zip    |
| .cs         | ex0  | msi        | pub  | -        | wmz       | zip_   |
| CSV        | FRD  | mso        | -     | -        | wps       | zipp   |
| cvr        | gif  | msu        | -     | -        | wpt       | zipped |
| -           | guid | nfo        | -     | -        | wsdl      | zippy  |
| -           | gz   | -           | -     | -        | wsp       | zipx   |
| -           | -     | -           | -     | -        | wtl       | zit    |
| -           | -     | -           | -     | -        | -          | zix    |
| -           | -     | -           | -     | -        | -          | zzz    |

## <a name="close-a-support-request"></a>Chiudere una richiesta di supporto

Se è necessario chiudere una richiesta di supporto, [inviare un messaggio](#send-a-message) che richiede la chiusura della richiesta.

## <a name="reopen-a-closed-request"></a>Riaprire una richiesta chiusa

Se è necessario riaprire una richiesta di supporto chiusa, creare un [nuovo messaggio](#send-a-message)che riapra automaticamente la richiesta.

## <a name="cancel-a-support-plan"></a>Annulla un piano di supporto

Se è necessario annullare un piano di supporto, vedere [annullare un piano di supporto](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Passaggi successivi

[Come creare una richiesta di supporto in Azure](how-to-create-azure-support-request.md)

[API REST dei ticket di supporto di Azure](/rest/api/support)
