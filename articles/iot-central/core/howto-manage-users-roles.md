---
title: Gestire utenti e ruoli in Azure IoT Central'applicazione | Microsoft Docs
description: Come amministratore, come gestire utenti e ruoli nell'applicazione Azure IoT Central locale
author: lmasieri
ms.author: lmasieri
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: cff8830d180b0c234e54f7578ed9fafafeb598f0
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719170"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gestire utenti e ruoli nell'applicazione IoT Central

Questo articolo descrive come, in quanto amministratore, è possibile aggiungere, modificare ed eliminare utenti nell'applicazione Azure IoT Central locale. L'articolo descrive anche come gestire i ruoli nell'applicazione.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. Se si crea un'Azure IoT Central, si viene aggiunti automaticamente al ruolo **Amministratore** per tale applicazione.

## <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione e accedervi. IoT Central attualmente supporta account Microsoft e account Azure Active Directory, ma non Azure Active Directory gruppi.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.

  :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Gestire gli utenti":::

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

  :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Aggiungere un utente e selezionare un ruolo.":::

  > [!NOTE]
  > Un utente che ha un ruolo personalizzato che concede loro l'autorizzazione per aggiungere altri utenti può aggiungere utenti solo a un ruolo con le stesse autorizzazioni o un numero inferiore rispetto al proprio ruolo.

  > [!NOTE]
  > Se un utente viene eliminato da Azure Active Directory e quindi aggiunto di nuovo, non sarà in grado di accedere all'applicazione IoT Central dati. Per abilitare nuovamente l'accesso, l'amministratore dell'applicazione deve eliminare e aggiungere nuovamente l'utente anche nell'applicazione.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo l'assegnazione. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

> [!NOTE]
> I ruoli assegnati sono specifici dell'applicazione IoT Central e non possono essere gestiti dal portale di Azure.

## <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="manage-roles"></a>Gestisci ruoli

I ruoli consentono di controllare chi all'interno dell'organizzazione è autorizzato a eseguire varie attività IoT Central. Sono disponibili tre ruoli predefiniti che è possibile assegnare agli utenti dell'applicazione. È anche possibile [creare ruoli personalizzati](#create-a-custom-role) se è necessario un controllo con granularità più fine.

> [!div class="mx-imgBorder"]
> ![Gestire la selezione dei ruoli](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Amministratore

Gli utenti con **ruolo Amministratore** possono gestire e controllare ogni parte dell'applicazione, inclusa la fatturazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="builder"></a>Generatore

Gli utenti con **il ruolo Generatore** possono gestire ogni parte dell'app, ma non possono apportare modifiche nelle schede Amministrazione o Esportazione continua dati.

### <a name="operator"></a>Operatore

Gli utenti con il **ruolo Operatore** possono monitorare l'integrità e lo stato del dispositivo. Non è consentito apportare modifiche ai modelli di dispositivo o amministrare l'applicazione. Gli operatori possono aggiungere ed eliminare dispositivi, gestire set di dispositivi ed eseguire analisi e processi.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Se la soluzione richiede controlli di accesso con granularità più fine, è possibile creare ruoli con set personalizzati di autorizzazioni. Per creare un ruolo personalizzato, passare alla **pagina Ruoli** nella **sezione Amministrazione** dell'applicazione. Selezionare quindi **+ Nuovo ruolo** e aggiungere un nome e una descrizione per il ruolo. Selezionare le autorizzazioni richieste dal ruolo e quindi selezionare **Salva.**

È possibile aggiungere utenti al ruolo personalizzato nello stesso modo in cui si aggiungono utenti a un ruolo predefinito.

> [!div class="mx-imgBorder"]
> ![Creare un ruolo personalizzato](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opzioni del ruolo personalizzato

Quando si definisce un ruolo personalizzato, si sceglie il set di autorizzazioni concesse a un utente se è membro del ruolo. Alcune autorizzazioni dipendono da altre. Ad esempio, se si aggiunge **l'autorizzazione Aggiorna dashboard** personali a un ruolo, l'autorizzazione Visualizza **dashboard** personali viene aggiunta automaticamente. Le tabelle seguenti riepilogano le autorizzazioni disponibili e le relative dipendenze che è possibile usare durante la creazione di ruoli personalizzati.

#### <a name="managing-devices"></a>Gestione dei dispositivi

**Autorizzazioni del modello di dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Gestire | Visualizzazione <br/> Altre dipendenze: Visualizzare le istanze del dispositivo  |
| Controllo completo | Visualizzare, gestire <br/> Altre dipendenze: Visualizzare le istanze del dispositivo |

**Autorizzazioni dell'istanza del dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e i gruppi di dispositivi |
| Update | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Create | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Eseguire comandi | Aggiornamento, visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Visualizzare dati non elaborati | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Controllo completo | Comandi Visualizza, Aggiorna, Crea, Elimina, Esegui, Visualizza dati non elaborati <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |

**Autorizzazioni per i gruppi di dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo |
| Update | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Create | Visualizzare, aggiornare <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo  |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo |

**Autorizzazioni di gestione della connettività dei dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Leggere l'istanza | nessuno <br/> Altre dipendenze: Visualizzare modelli di dispositivo, gruppi di dispositivi, istanze del dispositivo |
| Gestire l'istanza | Leggere un'istanza <br /> Altre dipendenze: Visualizzare modelli di dispositivo, gruppi di dispositivi, istanze di dispositivo |
| Lettura globale | nessuno   |
| Gestisci globale | Lettura globale |
| Controllo completo | Istanza di lettura, Gestisci istanza, Lettura globale, Gestisci globale <br/> Altre dipendenze: Visualizzare modelli di dispositivo, gruppi di dispositivi, istanze di dispositivo |

**Autorizzazioni per i processi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo e i gruppi di dispositivi |
| Update | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo e i gruppi di dispositivi |
| Create | Visualizzare, aggiornare <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo e i gruppi di dispositivi |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo e i gruppi di dispositivi |
| Execute | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi nelle istanze del dispositivo |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare, eseguire <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi nelle istanze del dispositivo |

**Autorizzazioni per le regole**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: Visualizzare i modelli di dispositivo |
| Update | Visualizzazione <br/> Altre dipendenze: Visualizzare i modelli di dispositivo |
| Create | Visualizzare, aggiornare <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo |

#### <a name="managing-the-app"></a>Gestione dell'app

**Autorizzazioni per le impostazioni dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Copia | Visualizzazione <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivo, gruppi di dispositivi, dashboard, esportazione dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, copiare, eliminare <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, dashboard dell'applicazione, esportazione dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |

**Autorizzazioni di esportazione del modello di applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Esportazione | Visualizzazione <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivo, gruppi di dispositivi, dashboard, esportazione dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |
| Controllo completo | Visualizzare, esportare <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, dashboard dell'applicazione, esportazione dati, personalizzazione, collegamenti alla Guida, ruoli personalizzati, regole |

**Autorizzazioni per il caricamento di file del dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Gestire | Visualizzazione   |
| Controllo completo | Visualizzare, gestire |

**Autorizzazioni di fatturazione**

| Nome | Dependencies |
| ---- | -------- |
| Gestire | nessuno     |
| Controllo completo | Gestire |

#### <a name="managing-users-and-roles"></a>Gestione di utenti e ruoli

**Autorizzazioni per i ruoli personalizzati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno |
| Update | Visualizzazione |
| Create | Visualizzare, aggiornare |
| Delete | Visualizzazione |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione |

**Autorizzazioni di gestione dell'utente**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Add | Visualizzazione <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Delete | Visualizzazione <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |
| Controllo completo | Visualizzazione, aggiunta, eliminazione <br/> Altre dipendenze: Visualizzare i ruoli personalizzati |

> [!NOTE]
> Un utente che ha un ruolo personalizzato che concede l'autorizzazione per aggiungere altri utenti può aggiungere utenti solo a un ruolo con le stesse autorizzazioni o un numero inferiore rispetto al proprio ruolo.

#### <a name="customizing-the-app"></a>Personalizzazione dell'app

**Autorizzazioni del dashboard dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Create | Visualizzare, aggiornare |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni per i dashboard personali**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Create | Visualizzare, aggiornare   |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni per personalizzazione, favicon e colori**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare |

**Autorizzazioni per i collegamenti alla Guida**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare |

#### <a name="extending-the-app"></a>Estensione dell'app

**Autorizzazioni per l'esportazione dei dati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Update | Visualizzazione   |
| Create | Visualizzare, aggiornare  |
| Delete | Visualizzazione   |
| Controllo completo | Visualizzazione, aggiornamento, creazione, eliminazione |

**Autorizzazioni per i token API**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno  <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Create | Visualizzazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Delete | Visualizzazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Controllo completo | Visualizzazione, creazione, eliminazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come gestire utenti e ruoli nell'applicazione IoT Central, il passaggio successivo consigliato consiste nell'apprendere come gestire [la fattura.](howto-view-bill.md)