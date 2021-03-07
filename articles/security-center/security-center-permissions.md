---
title: Autorizzazioni nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come Centro sicurezza di Azure usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identificare le azioni consentite per ogni ruolo.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 01/03/2021
ms.author: memildin
ms.openlocfilehash: dcbb4977e1bfd17f0cbed61abf9ba335615b7799
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443761"
---
# <a name="permissions-in-azure-security-center"></a>Autorizzazioni nel Centro sicurezza di Azure

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md), che fornisce [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

* **Ruolo con autorizzazioni di lettura per la sicurezza**: un utente che appartiene a questo ruolo dispone dei diritti di visualizzazione nel Centro sicurezza. L'utente può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche.
* **Amministratore della sicurezza**: un utente che appartiene a questo ruolo ha gli stessi diritti del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni.

> [!NOTE]
> I ruoli di sicurezza Ruolo con autorizzazioni di lettura per la sicurezza e Amministratore della protezione hanno accesso solo al Centro sicurezza. I ruoli di sicurezza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.
>

## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente contiene i ruoli e le azioni consentite in Centro sicurezza.

| Operazione                                                                                                                                        | Ruolo con autorizzazioni di lettura per la sicurezza / <br> Reader | Amministrazione della protezione | Collaboratore del gruppo di risorse / <br> Proprietario del gruppo di risorse | Collaboratore alla sottoscrizione | Proprietario della sottoscrizione |
|:----------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
| Modificare i criteri di sicurezza                                                                                                                          | -                             | ✔             | -                                                      | -                        | ✔                 |
| Aggiungere/assegnare iniziative, inclusi gli standard di conformità alle normative                                                                           | -                             | -              | -                                                      | -                        | ✔                 |
| Abilitare/disabilitare Azure Defender                                                                                                               | -                             | ✔             | -                                                      | -                        | ✔                 |
| Abilitare/disabilitare il provisioning automatico                                                                                                            | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Applicare i suggerimenti per la sicurezza per una risorsa</br> (e usare la [correzione rapida](security-center-remediate-recommendations.md#quick-fix-remediation)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Ignorare gli avvisi                                                                                                                                | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Visualizzare gli avvisi e le raccomandazioni                                                                                                               | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Assegnare ad esempio il ruolo di lettore agli utenti che devono visualizzare solo le informazioni sull'integrità della sicurezza di una risorsa, ma non eseguono alcuna azione, come l'applicazione di consigli e la modifica di criteri.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come viene usato il Controllo degli accessi in base al ruolo di Azure nel Centro sicurezza per assegnare autorizzazioni agli utenti e sono state identificate le azioni consentite per ogni ruolo. Ora che è stata acquisita familiarità con le assegnazioni di ruolo necessari per monitorare lo stato di sicurezza della sottoscrizione, modificare i criteri di sicurezza e applicare i consigli, si apprenderà come eseguire queste operazioni:

- [Impostare i criteri di sicurezza nel Centro sicurezza](tutorial-security-policy.md)
- [Gestire i suggerimenti per la sicurezza in Centro sicurezza](security-center-recommendations.md)
- [Gestire e rispondere agli avvisi di sicurezza in Centro sicurezza](security-center-managing-and-responding-alerts.md)
- [Monitorare le soluzioni di sicurezza dei partner](./security-center-partner-integration.md)