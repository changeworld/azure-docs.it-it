---
title: Come analizzare i bucket di Amazon S3
description: Questa guida dettagliata descrive in dettaglio come analizzare i bucket di Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/21/2021
ms.custom: references_regions
ms.openlocfilehash: 75a7cba1e47509e3186ab519d0d8ca82dd315373
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815523"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Connettore Azure Purview per Amazon S3

Questa guida alle procedure illustra come usare Azure Purview per analizzare i dati non strutturati attualmente archiviati in bucket standard Amazon S3 e individuare i tipi di informazioni riservate presenti nei dati. Questa guida dettagliata descrive anche come identificare i bucket di Amazon S3 in cui sono attualmente archiviati i dati per una facile protezione delle informazioni e la conformità dei dati.

Per questo servizio, usare Purview per fornire un account Microsoft con accesso sicuro ad AWS, in cui verrà eseguito lo scanner Purview. Lo scanner Purview usa questo accesso ai bucket di Amazon S3 per leggere i dati e quindi segnala i risultati dell'analisi, inclusi solo i metadati e la classificazione, ad Azure. Usare i report di classificazione e etichettatura Purview per analizzare ed esaminare i risultati dell'analisi dei dati.

In questa guida si apprenderà come aggiungere bucket Amazon S3 come risorse Purview e creare un'analisi per i dati di Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Ambito di purview per Amazon S3

L'ambito seguente è specifico per la registrazione e l'analisi dei bucket Amazon S3 come origini dati Purview.

|Ambito  |Descrizione  |
|---------|---------|
|**Limiti dei dati**     |    Il servizio di analisi Purview supporta attualmente l'analisi di bucket Amazon S3 per un massimo di 100 GB di dati per ogni tenant.     |
|**Tipi di file**     | Il servizio di analisi Purview supporta attualmente i tipi di file seguenti: <br><br>avro, csv, .doc, .docm, .docx, .dot, .json, .odp, .ods, .odt, .orc, .parquet, .pdf, .pot, .ppsx, .ppsx, .ppt, .pptm, .pptx, .psv, .ssv, .tsv, .txt, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlt, .xml        |
|**Aree**     | Il connettore Purview per il servizio Amazon S3 è attualmente distribuito solo nelle aree AWS Stati Uniti orientali **(Asia)** **ed Europa (Germania).** <br><br>Per altre informazioni, vedere Aree [di archiviazione e analisi.](#storage-and-scanning-regions)   |
|     |         |

Per altre informazioni, vedere i limiti di Purview documentati all'indirizzo:

- [Gestire e aumentare le quote per le risorse con Azure Purview](how-to-manage-quotas.md)
- [Origini dati e tipi di file supportati in Azure Purview](sources-and-scans.md)
- [Usare endpoint privati per l'account Purview](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Aree di archiviazione e analisi

Nella tabella seguente vengono mappate le aree in cui i dati vengono archiviati nell'area in cui verranno analizzati da Azure Purview.

> [!IMPORTANT]
> Ai clienti verranno addebitati tutti gli addebiti per il trasferimento dei dati correlati in base all'area del bucket.
>

| Area di archiviazione | Area di analisi |
| ------------------------------- | ------------------------------------- |
| Stati Uniti orientali (Ohio)                  | Stati Uniti orientali (Ohio)                        |
| Stati Uniti orientali (N. Virginia)           | Stati Uniti orientali (N. Virginia)                       |
| Stati Uniti occidentali (N. California)         | Stati Uniti orientali (Ohio)                        |
| Stati Uniti occidentali (Oregon)                | Stati Uniti orientali (Ohio)                        |
| Africa (Città del Capo)              | Europa (Parigi)                    |
| Asia Pacifico (Hong Kong)        | Asia Pacifico (Sydney)                   |
| Asia Pacifico (Mumbai)           | Asia Pacifico (Sydney)                   |
| Asia Pacifico (Osaka-Local)      | Asia Pacifico (Sydney)                   |
| Asia Pacifico (Sydney)            | Asia Pacifico (Sydney)                   |
| Asia Pacifico (Singapore)        | Asia Pacifico (Sydney)                   |
| Asia Pacifico (Sydney)           | Asia Pacifico (Sydney)                  |
| Asia Pacifico (Tokyo)            | Asia Pacifico (Sydney)                 |
| Canada (centrale)                | Stati Uniti orientali (Asia)                        |
| Cina (Cina)                 | Non supportato                    |
| Cina (Ningxia)                 | Non supportato                   |
| Europa (Parigi)              | Europa (Parigi)                    |
| Europa (Irlanda)                | Europa (Irlanda)                   |
| Europa (Londra)                 | Europa (Irlanda)                   |
| Europa (Italia)                  | Europa (Parigi)                    |
| Europa (Parigi)                  | Europa (Parigi)                    |
| Europa (Italia)              | Europa (Parigi)                    |
| Medio Oriente (Brasile)           | Europa (Parigi)                    |
| America del Sud (San Paolo)       | Stati Uniti orientali (Ohio)                        |
| | |

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver eseguito i prerequisiti seguenti prima di aggiungere i bucket Amazon S3 come origini dati Purview ed eseguire l'analisi dei dati S3.

> [!div class="checklist"]
> * È necessario essere un amministratore dell'origine dati di Azure Purview.
> * [Creare un account Purview](#create-a-purview-account) se non è ancora disponibile
> * [Creare una credenziale Purview per l'analisi bucket AWS](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Creare un nuovo ruolo AWS da usare con Purview](#create-a-new-aws-role-for-purview)
> * [Configurare l'analisi per bucket Amazon S3 crittografati,](#configure-scanning-for-encrypted-amazon-s3-buckets)se pertinenti
> * Quando si aggiungono i bucket come risorse Purview, sono necessari i valori di [AWS ARN,](#retrieve-your-new-role-arn) [nome bucket](#retrieve-your-amazon-s3-bucket-name)e talvolta l'ID [dell'account AWS.](#locate-your-aws-account-id)

### <a name="create-a-purview-account"></a>Creare un account Purview

- **Se si ha già un account Purview,** è possibile continuare con le configurazioni necessarie per il supporto di AWS S3. Iniziare con [Creare una credenziale Purview per l'analisi bucket AWS.](#create-a-purview-credential-for-your-aws-bucket-scan)

- **Se è necessario creare un account Purview,** seguire le istruzioni in [Creare un'istanza dell'account Azure Purview](create-catalog-portal.md). Dopo aver creato l'account, tornare qui per completare la configurazione e iniziare a usare il connettore Purview per Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Creare una credenziale Purview per l'analisi bucket AWS

Questa procedura descrive come creare una nuova credenziale Purview da usare durante l'analisi dei bucket AWS.

> [!TIP]
> È anche possibile creare una nuova credenziale nel corso del processo, durante la [configurazione dell'analisi](#create-a-scan-for-one-or-more-amazon-s3-buckets). In tal caso, nel **campo Credenziali** selezionare **Nuovo**.
>

1. In Purview passare al **Centro** gestione e in **Sicurezza e accesso** selezionare **Credenziali**.

1. Selezionare **Nuovo** e nel **riquadro Nuove** credenziali visualizzato a destra usare i campi seguenti per creare le credenziali purview:

    |Campo |Descrizione  |
    |---------|---------|
    |**Nome**     |Immettere un nome significativo per questa credenziale oppure usare il valore predefinito.        |
    |**Descrizione**     |Immettere una descrizione facoltativa per questa credenziale, ad esempio `Used to scan the tutorial S3 buckets`         |
    |**Metodo di autenticazione**     |Selezionare **Role ARN (ARN** ruolo) perché si sta usando un ruolo ARN per accedere al bucket.         |
    |**Microsoft account ID**     |Fare clic per copiare questo valore negli Appunti. Usare questo valore come ID **account Microsoft quando** si [crea l'ARN del ruolo in AWS.](#create-a-new-aws-role-for-purview)           |
    |**ID esterno**     |Fare clic per copiare questo valore negli Appunti. Usare questo valore come **ID esterno durante la** creazione del ruolo [ARN in AWS.](#create-a-new-aws-role-for-purview)        |
    |**ARN del ruolo**     | Dopo aver creato [il ruolo Amazon IAM,](#create-a-new-aws-role-for-purview)passare al proprio ruolo nell'area IAM, copiare il valore **ARN** del ruolo e immetterlo qui. Ad esempio: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Per altre informazioni, vedere [Recuperare il nuovo ruolo ARN.](#retrieve-your-new-role-arn) |
    | | |

    Al **termine della** creazione delle credenziali, selezionare Crea.

1. Se non è ancora stato fatto, copiare e incollare i valori di **ID** account Microsoft e **ID** esterno da usare quando si crea un nuovo ruolo AWS per [Purview,](#create-a-new-aws-role-for-purview)che è il passaggio successivo.

Per altre informazioni sulle credenziali di Purview, vedere [Credenziali per l'autenticazione di origine in Azure Purview.](manage-credentials.md)

### <a name="create-a-new-aws-role-for-purview"></a>Creare un nuovo ruolo AWS per Purview

Questa procedura richiede l'immissione dei valori per l'ID account Azure e l'ID esterno durante la creazione del ruolo AWS.

Se questi valori non sono presenti, individuarli prima nelle credenziali [di Purview.](#create-a-purview-credential-for-your-aws-bucket-scan)

**Per individuare l'ID account Microsoft e l'ID esterno:**

1. In Purview passare a Management **Center**  >  **Security and access** Credentials (Credenziali di sicurezza e  >  **accesso del Centro gestione).**

1. Selezionare le credenziali create [per l'analisi bucket di AWS](#create-a-purview-credential-for-your-aws-bucket-scan)e quindi nella barra degli strumenti selezionare **Modifica.**

1. Nel riquadro Modifica **credenziali** visualizzato a destra copiare i valori di **ID account Microsoft** e **ID** esterno in un file separato oppure fare in modo che sia possibile incollarli nel campo pertinente in AWS.

    Ad esempio:

    [![Individuare l'ID account Microsoft e i valori dell'ID esterno. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Per creare il ruolo AWS per Purview:**

1.  Aprire la **console Amazon Web Services** e in **Sicurezza, identità e conformità** selezionare **IAM.**

1. Selezionare **Ruoli** e quindi **Crea ruolo**.

1. Selezionare **Un altro account AWS** e quindi immettere i valori seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**ID account**     |    Immettere l'ID account Microsoft. ad esempio `615019938638`     |
    |**ID esterno**     |   In opzioni selezionare **Richiedi ID esterno...** e quindi immettere l'ID esterno nel campo designato. <br>ad esempio `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Ad esempio:

    ![Aggiungere l'ID account Microsoft all'account AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Nell'area Create role > Attach permissions policies (Crea ruolo) **filtrare** le autorizzazioni visualizzate in **S3.** Selezionare **AmazonS3ReadOnlyAccess** e quindi **Selezionare Avanti: Tag**.

    ![Selezionare i criteri ReadOnlyAccess per il nuovo ruolo di analisi Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > I **criteri AmazonS3ReadOnlyAccess** forniscono le autorizzazioni minime necessarie per l'analisi dei bucket S3 e possono includere anche altre autorizzazioni.
    >
    >Per applicare solo le autorizzazioni minime necessarie per l'analisi dei bucket, creare un nuovo criterio con le autorizzazioni elencate in Autorizzazioni minime per i criteri [AWS,](#minimum-permissions-for-your-aws-policy)a seconda che si voglia analizzare un singolo bucket o tutti i bucket nell'account. 
    >
    >Applicare i nuovi criteri al ruolo invece **di AmazonS3ReadOnlyAccess.**

1. Nell'area **Aggiungi tag (facoltativo)** è possibile scegliere di creare un tag significativo per questo nuovo ruolo. I tag utili consentono di organizzare, tenere traccia e controllare l'accesso per ogni ruolo creato.

    Immettere una nuova chiave e un nuovo valore per il tag in base alle esigenze. Al termine o se si vuole ignorare questo passaggio, selezionare **Avanti:** Verifica per esaminare i dettagli del ruolo e completare la creazione del ruolo.

    ![Aggiungere un tag significativo per organizzare, tenere traccia o controllare l'accesso per il nuovo ruolo.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Nell'area **Revisione** eseguire le operazioni seguenti:

    - Nel campo **Nome ruolo** immettere un nome significativo per il ruolo
    - Nella casella **Descrizione** ruolo immettere una descrizione facoltativa per identificare lo scopo del ruolo
    - Nella sezione **Criteri** verificare che il criterio corretto (**AmazonS3ReadOnlyAccess**) sia collegato al ruolo.

    Selezionare quindi **Crea ruolo** per completare il processo.

    Ad esempio:

    ![Esaminare i dettagli prima di creare il ruolo.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Configurare l'analisi per i bucket crittografati di Amazon S3

I bucket AWS supportano più tipi di crittografia. Per i bucket che usano la **crittografia AWS-KMS,** è necessaria una configurazione speciale per abilitare l'analisi.

> [!NOTE]
> Per i bucket che non usano la crittografia AES-256 o la crittografia S3 aws-KMS, ignorare questa sezione e continuare con Recuperare il nome [del bucket Amazon S3.](#retrieve-your-amazon-s3-bucket-name)
>

**Per controllare il tipo di crittografia usato nei bucket di Amazon S3:**

1. In AWS passare a **Storage**  >  **S3 >** e selezionare Bucket **dal** menu a sinistra.

    ![Selezionare la scheda Bucket Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Selezionare il bucket da controllare. Nella pagina dei dettagli del bucket selezionare la scheda **Proprietà** e scorrere verso il basso fino all'area **Crittografia** predefinita.

    - Se il bucket selezionato è configurato solo per la crittografia **AWS-KMS,** anche se la crittografia predefinita per il bucket è **Disabilitata,** ignorare il resto di questa procedura e continuare con Recuperare il nome del [bucket Amazon S3.](#retrieve-your-amazon-s3-bucket-name)

    - Se il bucket selezionato è configurato per la crittografia **AWS-KMS,** continuare come descritto di seguito per aggiungere un nuovo criterio che consenta l'analisi di un bucket con la crittografia **AWS-KMS** personalizzata.

    Ad esempio:

    ![Visualizzare un bucket Amazon S3 configurato con la crittografia AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Per aggiungere un nuovo criterio per consentire l'analisi di un bucket con la crittografia AWS-KMS personalizzata:**

1. In AWS passare a **Services**  >   IAM Policies **(Criteri IAM**  >   **dei** servizi) e **selezionare Create policy (Crea criterio).**

1. Nella scheda **Crea editor oggetto**  >  **visivo** criteri definire i criteri con i valori seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**Servizio**     |  Immettere e selezionare **Servizio di gestione delle chiavi**.       |
    |**Actions**     | In **Livello di accesso** selezionare Scrittura **per** espandere la **sezione** Scrittura.<br>Dopo l'espansione, selezionare solo **l'opzione Decrittografa.**        |
    |**Risorse**     |Selezionare una risorsa specifica o **Tutte le risorse**.         |
    | | |

    Al termine, selezionare **Rivedi criteri** per continuare.

    ![Creare un criterio per l'analisi di un bucket con la crittografia AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Nella pagina **Rivedi** criteri immettere un nome significativo per i criteri e una descrizione facoltativa e quindi selezionare **Crea criterio**.

    I criteri appena creati vengono aggiunti all'elenco di criteri.

1. Collegare il nuovo criterio al ruolo aggiunto per l'analisi.

    1. Tornare alla pagina **Ruoli IAM**  >   e selezionare il ruolo aggiunto in [precedenza.](#create-a-new-aws-role-for-purview)

    1. Nella scheda **Autorizzazioni** selezionare **Associa criteri**.

        ![Nella scheda Autorizzazioni del ruolo selezionare Associa criteri.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Nella pagina **Collega autorizzazioni** cercare e selezionare il nuovo criterio creato in precedenza. Selezionare **Associa criterio** per associare i criteri al ruolo.

        La **pagina** Riepilogo viene aggiornata, con i nuovi criteri collegati al ruolo.

        ![Visualizzare una pagina riepilogo aggiornata con i nuovi criteri associati al ruolo.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Recuperare il nuovo ARN del ruolo

È necessario registrare l'ARN del ruolo AWS e copiarlo in Purview durante la creazione di un'analisi per [il bucket Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Per recuperare il ruolo ARN:**

1. Nell'area Ruoli di AWS **Identity and Access Management (IAM)** cercare e selezionare il nuovo ruolo creato per  >   [Purview.](#create-a-purview-credential-for-your-aws-bucket-scan)

1. Nella pagina Riepilogo **del** ruolo selezionare il pulsante Copia negli **Appunti** a destra del **valore ARN del** ruolo.

    ![Copiare il valore ARN del ruolo negli Appunti.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Incollare questo valore in una posizione sicura, pronta per l'uso durante la creazione [di un'analisi per il bucket di Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Recuperare il nome del bucket Amazon S3

È necessario il nome del bucket Amazon S3 per copiarlo in Purview durante la creazione di un'analisi per [il bucket Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Per recuperare il nome del bucket:**

1. In AWS passare a **Storage**  >  **S3 >** e selezionare Bucket **dal** menu a sinistra.

    ![Visualizzare la scheda Bucket di Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Cercare e selezionare il bucket per visualizzare la pagina dei dettagli del bucket e quindi copiare il nome del bucket negli Appunti.

    Ad esempio:

    ![Recuperare e copiare l'URL del bucket S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Incollare il nome del bucket in un file sicuro e aggiungervi un prefisso per creare il valore che sarà necessario immettere quando si configura il bucket come `s3://` risorsa Purview.

    ad esempio `s3://purview-tutorial-bucket`

> [!NOTE]
> Solo il livello radice del bucket è supportato come origine dati Purview. Ad esempio, l'URL seguente, che include una sottocartella, *non è* supportato: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Individuare l'ID account AWS

È necessario l'ID dell'account AWS per registrare l'account AWS come origine dati Purview, insieme a tutti i bucket.

L'ID dell'account AWS è l'ID che si usa per accedere alla console aws. È anche possibile trovarlo dopo aver eseguito l'accesso al dashboard IAM, a sinistra sotto le opzioni di spostamento e nella parte superiore, come parte numerica dell'URL di accesso:

Ad esempio:

![Recuperare l'ID dell'account AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Aggiungere un singolo bucket Amazon S3 come risorsa Purview

Usare questa procedura se si ha un solo bucket S3 che si vuole registrare in Purview come origine dati o se sono presenti più bucket nell'account AWS, ma non si vuole registrarli tutti in Purview.

**Per aggiungere il bucket:** 

1. Avviare il portale Purview usando il connettore Purview dedicato per l'URL Amazon S3. Questo URL è stato fornito dal team di gestione del prodotto del connettore Amazon S3 Purview.

    ![Avviare il portale purview.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Passare alla pagina Origini di Azure **Purview** e selezionare l'icona **Registra** ![ registro.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Continuare** con .

    ![Aggiungere un bucket AMAZON AWS come origine dati Purview.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Se si dispone di più [raccolte](manage-data-sources.md#manage-collections) e si vuole aggiungere Amazon S3 a una raccolta specifica, selezionare la visualizzazione Mappa **in** alto a destra e quindi selezionare l'icona Registra  ![ registro.](./media/register-scan-amazon-s3/register-button.png) all'interno della raccolta.
    >

1. Nel riquadro **Registra origini (Amazon S3)** visualizzato immettere i dettagli seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**Nome**     |Immettere un nome significativo o usare il valore predefinito specificato.         |
    |**Bucket URL**     | Immettere l'URL del bucket AWS usando la sintassi seguente:   `s3://<bucketName>`     <br><br>**Nota:** assicurarsi di usare solo il livello radice del bucket, senza sottocartelle. Per altre informazioni, vedere [Recuperare il nome del bucket Amazon S3.](#retrieve-your-amazon-s3-bucket-name) |
    |**Selezionare una raccolta** |Se si è scelto di registrare un'origine dati dall'interno di una raccolta, tale raccolta è già elencata. <br><br>Selezionare una raccolta diversa in base alle esigenze, Nessuno per non **assegnare** alcuna raccolta o **Nuovo** per creare una nuova raccolta ora. <br><br>Per altre informazioni sulle raccolte Purview, vedere [Gestire le origini dati in Azure Purview.](manage-data-sources.md#manage-collections)|
    | | |

    Al termine, selezionare Fine **per** completare la registrazione.

Continuare con [Creare un'analisi per uno o più bucket Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Aggiungere un account Amazon come risorsa Purview

Usare questa procedura se si dispone di più bucket S3 nell'account Amazon e si vuole registrarli tutti come origini dati Purview.

Quando [si configura l'analisi,](#create-a-scan-for-one-or-more-amazon-s3-buckets)sarà possibile selezionare i bucket specifici da analizzare, se non si vuole analizzarli tutti insieme.

**Per aggiungere l'account Amazon:**
1. Avviare il portale Purview usando il connettore Purview dedicato per l'URL Amazon S3. Questo URL è stato fornito dal team di gestione del prodotto del connettore Amazon S3 Purview.

    ![Avviare il connettore per il portale Purview dedicato di Amazon S3](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Passare alla pagina Azure Purview **Sources (Azure** Purview Sources) e selezionare **l'icona Register** Register ![ (Registra registrazione).](./media/register-scan-amazon-s3/register-button.png) > **Account Amazon**  >  **Continuare** con .

    ![Aggiungere un account Amazon come origine dati Purview.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Se si dispone [di](manage-data-sources.md#manage-collections) più raccolte e si vuole aggiungere Amazon S3 a una raccolta specifica, selezionare la visualizzazione Mappa **in** alto a destra e quindi selezionare l'icona Registra  ![ registrazione.](./media/register-scan-amazon-s3/register-button.png) all'interno della raccolta.
    >

1. Nel riquadro **Register sources (Amazon S3) (Registra origini - Amazon S3)** visualizzato immettere i dettagli seguenti:

    |Campo  |Descrizione  |
    |---------|---------|
    |**Nome**     |Immettere un nome significativo o usare il valore predefinito specificato.         |
    |**AWS account ID**     | Immettere l'ID account AWS. Per altre informazioni, vedere Individuare [l'ID account AWS](#locate-your-aws-account-id)|
    |**Selezionare una raccolta** |Se si è scelto di registrare un'origine dati dall'interno di una raccolta, tale raccolta è già elencata. <br><br>Selezionare una raccolta diversa in base alle esigenze, Nessuno per non **assegnare** alcuna raccolta o **Nuovo** per creare una nuova raccolta ora. <br><br>Per altre informazioni sulle raccolte Purview, vedere [Gestire le origini dati in Azure Purview.](manage-data-sources.md#manage-collections)|
    | | |

    Al termine, selezionare Fine **per** completare la registrazione.

Continuare con [Creare un'analisi per uno o più bucket Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Creare un'analisi per uno o più bucket Amazon S3

Dopo aver aggiunto i bucket come origini dati Purview, è possibile configurare un'analisi per l'esecuzione a intervalli pianificati o immediatamente.

1. Passare all'area  Origini di Azure Purview e quindi eseguire una delle operazioni seguenti:

    - Nella visualizzazione **Mappa selezionare** Nuova **analisi** Icona ![ Nuova analisi.](./media/register-scan-amazon-s3/new-scan-button.png) nella casella dell'origine dati.
    - Nella visualizzazione **Elenco passare il** puntatore del mouse sulla riga dell'origine dati e selezionare Nuova **analisi** Icona ![ Nuova ](./media/register-scan-amazon-s3/new-scan-button.png) analisi.

1. Nel riquadro **Analizza...** visualizzato a destra definire i campi seguenti e quindi selezionare **Continua:**

    |Campo  |Descrizione  |
    |---------|---------|
    |**Nome**     |  Immettere un nome significativo per l'analisi o usare il valore predefinito.       |
    |**Tipo** |Viene visualizzato solo se è stato aggiunto l'account AWS, con tutti i bucket inclusi. <br><br>Le opzioni correnti includono solo **All**  >  **Amazon S3**. Rimanere ottimizzati per ottenere altre opzioni da selezionare con l'espansione della matrice di supporto di Purview. |
    |**Credenziali**     |  Selezionare una credenziale Purview con il ruolo ARN. <br><br>**Suggerimento:** per creare una nuova credenziale in questo momento, selezionare **Nuova**. Per altre informazioni, vedere [Creare una credenziale Purview per l'analisi bucket AWS.](#create-a-purview-credential-for-your-aws-bucket-scan)     |
    | **Amazon S3**    |   Viene visualizzato solo se è stato aggiunto l'account AWS, con tutti i bucket inclusi. <br><br>Selezionare uno o più bucket da analizzare oppure **Selezionare tutti** per analizzare tutti i bucket nell'account.      |
    | | |

    Purview verifica automaticamente che il ruolo ARN sia valido e che i bucket e gli oggetti all'interno dei bucket siano accessibili e quindi continua se la connessione ha esito positivo.

    > [!TIP]
    > Per immettere valori diversi e testare manualmente la connessione prima di continuare, selezionare **Test connessione** in basso a destra prima di **selezionare Continua.**
    >

1. Nel riquadro **Selezionare un set di** regole di analisi selezionare il set di regole predefinito **AmazonS3** oppure selezionare Nuovo **set** di regole di analisi per creare un nuovo set di regole personalizzate. Dopo aver selezionato il set di regole, selezionare **Continua.**

    Se si sceglie di creare un nuovo set di regole di analisi personalizzato, usare la procedura guidata per definire le impostazioni seguenti:

    |Riquadro  |Descrizione  |
    |---------|---------|
    |**Nuovo set di regole di analisi** /<br>**Descrizione della regola di analisi**    |   Immettere un nome significativo e una descrizione facoltativa per il set di regole      |
    |**Selezionare i tipi di file**     | Selezionare tutti i tipi di file da includere nell'analisi e quindi selezionare **Continua.**<br><br>Per aggiungere un nuovo tipo di file, **selezionare Nuovo tipo di file** e definire quanto segue: <br>- L'estensione di file che si vuole aggiungere <br>- Descrizione facoltativa  <br>- Indica se il contenuto del file ha un delimitatore personalizzato o è un tipo di file di sistema. Immettere quindi il delimitatore personalizzato o selezionare il tipo di file di sistema. <br><br>Selezionare **Crea** per creare il tipo di file personalizzato.     |
    |**Selezionare le regole di classificazione**     |   Passare a e selezionare le regole di classificazione da eseguire nel set di dati.      |
    |     |         |

    Al **termine,** selezionare Crea per creare il set di regole.

1. Nel riquadro **Imposta un trigger di** analisi selezionare una delle opzioni seguenti e quindi continua: 

    - **Ricorrente** per configurare una pianificazione per un'analisi ricorrente
    - **Una volta** per configurare un'analisi che viene avviata immediatamente

1. Nel riquadro **Rivedi analisi** controllare i dettagli di analisi per verificare  che siano corretti e  quindi selezionare Salva o Salva **ed** esegui se è stato selezionato Una volta nel riquadro precedente.

    > [!NOTE]
    > Una volta avviata, il completamento dell'analisi può richiedere fino a 24 ore. Sarà possibile esaminare i  report di informazioni dettagliate ed eseguire ricerche nel catalogo 24 ore dopo l'avvio di ogni analisi.
    >

Per altre informazioni, vedere [Esplorare i risultati dell'analisi Purview.](#explore-purview-scanning-results)

## <a name="explore-purview-scanning-results"></a>Esplorare i risultati dell'analisi Purview

Al termine di un'analisi Purview nei bucket Amazon S3, eseguire il drill-down **nell'area** Origini purview per visualizzare la cronologia di analisi.

Selezionare un'origine dati per visualizzarne i dettagli e quindi selezionare la scheda **Scansioni** per visualizzare le analisi attualmente in esecuzione o completate.
Se è stato aggiunto un account AWS con più bucket, la cronologia di analisi per ogni bucket viene visualizzata sotto l'account.

Ad esempio:

![Visualizzare le analisi bucket AWS S3 nell'origine dell'account AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Usare le altre aree di Purview per ottenere informazioni dettagliate sul contenuto del data estate, inclusi i bucket Amazon S3:

- **Cercare nel catalogo dati Purview e** filtrare per un bucket specifico. Ad esempio:

    ![Cercare asset AWS S3 nel catalogo.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Visualizzare i report di Informazioni** dettagliate per visualizzare statistiche per la classificazione, le etichette di riservatezza, i tipi di file e altri dettagli sul contenuto.

    Tutti i report di Purview Insight includono i risultati dell'analisi di Amazon S3, insieme al resto dei risultati delle origini dati di Azure. Se pertinente, è stato aggiunto un tipo di asset **Amazon S3** aggiuntivo alle opzioni di filtro del report.

    Per altre informazioni, vedere Informazioni [dettagliate in Azure Purview.](concept-insights.md)

## <a name="minimum-permissions-for-your-aws-policy"></a>Autorizzazioni minime per i criteri AWS

La procedura predefinita per la creazione di un ruolo AWS da usare per [Purview](#create-a-new-aws-role-for-purview) durante l'analisi dei bucket S3 usa i criteri **AmazonS3ReadOnlyAccess.**

Il **criterio AmazonS3ReadOnlyAccess** fornisce le autorizzazioni minime necessarie per l'analisi dei bucket S3 e può includere anche altre autorizzazioni.

Per applicare solo le autorizzazioni minime necessarie per l'analisi dei bucket, creare un nuovo criterio con le autorizzazioni elencate nelle sezioni seguenti, a seconda che si voglia analizzare un singolo bucket o tutti i bucket nell'account.

Applicare i nuovi criteri al ruolo invece di **AmazonS3ReadOnlyAccess.**

### <a name="individual-buckets"></a>Singoli bucket

Quando si esegue l'analisi di singoli bucket S3, le autorizzazioni aws minime includono:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Assicurarsi di definire la risorsa con il nome del bucket specifico. Ad esempio:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Tutti i bucket nell'account

Quando si esegue l'analisi di tutti i bucket nell'account AWS, le autorizzazioni AWS minime includono:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Assicurarsi di definire la risorsa con un carattere jolly. Ad esempio:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui report di Azure Purview Insight:

> [!div class="nextstepaction"]
> [Informazioni dettagliate in Azure Purview](concept-insights.md)
