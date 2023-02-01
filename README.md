# WatchConnectivity

```swift

//
//  PhoneToWatchDataController.swift
//  WeightReminder
//
//  Created by paige shin on 2023/02/02.
//

import Foundation
import WatchConnectivity

class PhoneToWatchDataController: NSObject, WCSessionDelegate {
    
    var session: WCSession?
    
    var connected: Bool {
        guard let session: WCSession = self.session else {
            return false
        }
        return session.isPaired
        && session.watchDirectoryURL != nil // watch directory should not be nil when connected, directory is all deleted when devices are impaired or watch app is removed.
        && session.isWatchAppInstalled
    }
    
    // is interactive messaging possible?
    var messagingEnabled: Bool {
        return self.session?.isReachable ?? false
    }
    
    override init() {
        super.init()
        self.setupSession()
    }
    
    func setupSession() {
        if WCSession.isSupported() {
            self.session = WCSession.default
            self.session?.delegate = self
            session?.activate()
        }
    }
    
 
}

// MARK: - SESSION STATES
extension PhoneToWatchDataController {
    
    /// Session State
    func session(_ session: WCSession, activationDidCompleteWith activationState: WCSessionActivationState, error: Error?) {
        switch activationState {
        case .activated:
            Log.info("IPhone-Watch Session Activated")
        case .inactive:
            Log.info("IPhone-Watch Session Inactive")
        case .notActivated:
            Log.info("IPhone-Watch Session Not ACTIVATED")
        @unknown default:
            Log.info("Unknown cases")
        }
    }
    
    /// Session State
    func sessionDidBecomeInactive(_ session: WCSession) {
        Log.info("func sessionDidBecomeInactive(_ session: WCSession)")
    }
    
    /// Session State
    func sessionDidDeactivate(_ session: WCSession) {
        Log.info("func sessionDidDeactivate(_ session: WCSession)")
    }
    
    /// Session State
    /// Whenver session.paired changes its values,  sessionWatchStateDidChange(_), delegate method is called
    func sessionWatchStateDidChange(_ session: WCSession) {
        Log.info("func sessionWatchStateDidChange(_ session: WCSession)")
    }
    
    /// Rechability State - Interactive Messaging
    func sessionReachabilityDidChange(_ session: WCSession) {
        Log.info("func sessionReachabilityDidChange(_ session: WCSession)")
    }
    
}

// MARK: - BACKGROUND TRANSFER
extension PhoneToWatchDataController {
    
    /// ApplicationContext - Sender
    /// Single set of data
    /// applicationContext can be overriden
    func sendApplicationContext(dictionary: [String: Any]) {
        try? self.session?.updateApplicationContext(dictionary)
    }
    
    /// ApplicationContext - Receiver
    /// Single set of data
    /// Single set of data
    /// applicationContext can be overriden
    func session(_ session: WCSession, didReceiveApplicationContext applicationContext: [String : Any]) {
        
    }
    
    /// userInfo - Sender
    /// Complex Information Queued.
    /// Used for `InMemory Contents` like game score
    func sendUserInfo(dictionary: [String: Any]) {
        self.session?.transferUserInfo(dictionary)
    }
    
    /// userInfo - Receiver
    /// Complex Information Queued.
    /// Used for `InMemory Contents` like game score
    func session(_ session: WCSession, didReceiveUserInfo userInfo: [String : Any] = [:]) {
        
    }
    
    /// userInfo - Monitor, Cancel Task
    /// Complex Information Queued.
    /// Used for `InMemory Contents` like game score
    /// `Use the created object to monitor or cancel the transfer as needed.`
    func session(_ session: WCSession, didFinish userInfoTransfer: WCSessionUserInfoTransfer, error: Error?) {
        
    }
    
    /// file - Sender
    /// Image Editing App
    /// `Use the created object to monitor or cancel the transfer as needed.`
    func sendFile(url: URL, dictionary: [String: Any]?) {
        self.session?.transferFile(url, metadata: dictionary)
    }
    
    /// file - Receiver
    /// Image Editing App
    func session(_ session: WCSession, didReceive file: WCSessionFile) {
        
    }

    /// file - Monitor, Cancel Task
    /// Image Editing App
    /// `Use the created object to monitor or cancel the transfer as needed.`
    func session(_ session: WCSession, didFinish fileTransfer: WCSessionFileTransfer, error: Error?) {
        
    }
    
    
}

// MARK: Interactive Messaging
// - Phone only can receive messages from background (Wifi, Bluetooth)
// - Watch can receive message only on foreground
// - Phone and Watch should be paired
// - Determines connectivity with `rechable`
// - ios app should be running
extension PhoneToWatchDataController {

    
    func send(message: [String: Any], completion: @escaping([String: Any]) -> Void) {
        self.session?.sendMessage(message, replyHandler: { diciontary in
            completion(diciontary)
        })
    }
    
    func send(data: Data, completion: @escaping(Data) -> Void) {
        self.session?.sendMessageData(data, replyHandler: { data in
            completion(data)
        })
    }
    
    // Interactive Messaging - Receiver
    // `DICTIONARY`
    func session(_ session: WCSession, didReceiveMessage message: [String : Any]) {
        
    }
    
    // Interactive Messaging - Receiver
    // `DATA`
    func session(_ session: WCSession, didReceiveMessageData messageData: Data) {
        
    }
    
    // Interactive Messaging - Receiver
    // `DICTIONARY` WITH `REPLY HANDLER`
    func session(_ session: WCSession, didReceiveMessage message: [String : Any], replyHandler: @escaping ([String : Any]) -> Void) {

    }
    
    // Interactive Messaging - Receiver
    // `DATA` WITH `REPLY HANDLER`
    func session(_ session: WCSession, didReceiveMessageData messageData: Data, replyHandler: @escaping (Data) -> Void) {
        
    }
    
}



```
