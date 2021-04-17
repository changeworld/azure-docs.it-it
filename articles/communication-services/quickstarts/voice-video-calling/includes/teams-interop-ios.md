---
title: Guida introduttiva - Partecipare a una riunione di Teams da un'app iOS
description: Questa esercitazione illustra come partecipare a una riunione di Teams usando Servizi di comunicazione di Azure Calling SDK per iOS
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 363799cee5d66b718bb8ba06f4afd442add15148
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564535"
---
In questa guida introduttiva si apprenderà come partecipare a una riunione di Teams usando Servizi di comunicazione di Azure Calling SDK per iOS.

## <a name="prerequisites"></a>Prerequisiti

- Un'app [di Servizi di comunicazione funzionante che chiama l'app iOS](../getting-started-with-calling.md).
- Una [distribuzione di Teams](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Aggiungere i controlli dell'interfaccia utente di Teams e Abilitare i controlli dell'interfaccia utente di Teams

Sostituire il codice in ContentView.swift con il frammento di codice seguente. La casella di testo verrà usata per accedere al contesto della riunione di Teams e il pulsante per partecipare alla riunione specificata:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Ottenere il collegamento alla riunione di Teams

Il collegamento alla riunione di Teams può essere recuperato usando le API Graph. Questa operazione è descritta in dettaglio nella [documentazione di Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
L'SDK per le chiamate di Servizi di comunicazione accetta un collegamento alla riunione di Teams completo. Questo collegamento viene restituito come parte della `onlineMeeting` risorsa, accessibile tramite la [ `joinWebUrl` proprietà](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). È anche possibile ottenere le informazioni necessarie sulla riunione dall'URL di partecipazione **alla** riunione nell'invito alla riunione di Teams stesso.

## <a name="launch-the-app-and-join-teams-meeting"></a>Avviare l'app e partecipare alla riunione di Teams

È possibile creare ed eseguire un'app nel simulatore iOS selezionando **Product** > **Run** (Prodotto > Esegui) o premendo i tasti di scelta rapida &#8984;+R.

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Screenshot che mostra l'applicazione completata.":::

Inserire il contesto di Teams nella casella di testo e premere il pulsante *Partecipa alla riunione di Teams* per partecipare alla riunione di Teams dall'interno dell'applicazione di Servizi di comunicazione.
