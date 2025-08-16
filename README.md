# Telegramscripts

1. Message.swift

import Foundation

struct Message: Identifiable {
    let id = UUID()
    let text: String
    let isUser: Bool
    var isTyping: Bool = false
}


 2. TypingIndicator.swift



import SwiftUI

struct TypingIndicator: View {
    @State private var animate = false

    var body: some View {
        HStack(spacing: 4) {
            Circle().fill(Color.gray).frame(width: 8, height: 8)
                .opacity(animate ? 1 : 0.3)
                .animation(.easeInOut(duration: 0.6).repeatForever(), value: animate)
            Circle().fill(Color.gray).frame(width: 8, height: 8)
                .opacity(animate ? 1 : 0.3)
                .animation(.easeInOut(duration: 0.6).delay(0.2).repeatForever(), value: animate)
            Circle().fill(Color.gray).frame(width: 8, height: 8)
                .opacity(animate ? 1 : 0.3)
                .animation(.easeInOut(duration: 0.6).delay(0.4).repeatForever(), value: animate)
        }
        .onAppear { animate = true }
        .onDisappear { animate = false }
    }
}

3. ChatBubble.swift
import SwiftUI

struct ChatBubble: View {
    var message: Message

    var body: some View {
        HStack {
            if message.isUser {
                Spacer()
                Text(message.text)
                    .padding()
                    .background(Color.blue.opacity(0.8))
                    .foregroundColor(.white)
                    .cornerRadius(16)
                    .frame(maxWidth: 250, alignment: .trailing)
            } else {
                if message.isTyping {
                    TypingIndicator()
                        .padding()
                        .background(Color.gray.opacity(0.2))
                        .cornerRadius(16)
                } else {
                    Text(message.text)
                        .padding()
                        .background(Color.gray.opacity(0.2))
                        .foregroundColor(.black)
                        .cornerRadius(16)
                        .frame(maxWidth: 250, alignment: .leading)
                }
                Spacer()
            }
        }
        .padding(.horizontal)
        .padding(.vertical, 4)
    }
}

4. ContentView.swift

import SwiftUI

let GPT4ALL_URL = "http://127.0.0.1:4891/v1/chat/completions"  // GPT4All App local server

struct ContentView: View {
    @State private var userInput = ""
    @State private var messages: [Message] = []

    var body: some View {
        VStack {
            ScrollViewReader { proxy in
                ScrollView {
                    VStack {
                        ForEach(messages) { msg in
                            ChatBubble(message: msg)
                        }
                    }
                    .onChange(of: messages.count) { _ in
                        if let last = messages.last {
                            proxy.scrollTo(last.id, anchor: .bottom)
                        }
                    }
                }
            }

            HStack {
                TextField("Type a message...", text: $userInput)
                    .textFieldStyle(RoundedBorderTextFieldStyle())
                    .padding()

                Button("Send") {
                    sendMessage()
                }
                .padding()
            }
        }
        .padding()
    }

    func sendMessage() {
        // User message
        let userMsg = Message(text: userInput, isUser: true)
        messages.append(userMsg)

        // Typing indicator
        let typingMsg = Message(text: "", isUser: false, isTyping: true)
        messages.append(typingMsg)

        callGPT4All(prompt: userInput) { response in
            DispatchQueue.main.async {
                // Remove typing indicator
                messages.removeAll { $0.isTyping }

                // Add bot response
                let botMsg = Message(text: response, isUser: false)
                messages.append(botMsg)
                userInput = ""
            }
        }
    }

    func callGPT4All(prompt: String, completion: @escaping (String) -> Void) {
        guard let url = URL(string: GPT4ALL_URL) else { return }

        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")

        let body: [String: Any] = [
            "model": "gpt4all-lora-quantized",
            "messages": [["role": "user", "content": prompt]]
        ]

        request.httpBody = try? JSONSerialization.data(withJSONObject: body)

        URLSession.shared.dataTask(with: request) { data, _, error in
            guard let data = data, error == nil else {
                completion("⚠️ Error: \(error?.localizedDescription ?? "Unknown error")")
                return
            }

            if let json = try? JSONSerialization.jsonObject(with: data) as? [String: Any],
               let choices = json["choices"] as? [[String: Any]],
               let message = choices.first?["message"] as? [String: Any],
               let content = message["content"] as? String {
                completion(content.trimmingCharacters(in: .whitespacesAndNewlines))
            } else {
                completion("⚠️ Parsing error")
            }
        }.resume()
    }
}



**************+***+++++*****

import Foundation

struct Message: Identifiable {
    let id = UUID()
    let text: String
    let isUser: Bool
    var isTyping: Bool = false
}



import SwiftUI

struct TypingIndicator: View {
    @State private var animate = false

    var body: some View {
        HStack(spacing: 4) {
            Circle().fill(Color.gray).frame(width: 8, height: 8)
                .opacity(animate ? 1 : 0.3)
                .animation(.easeInOut(duration: 0.6).repeatForever(), value: animate)
            Circle().fill(Color.gray).frame(width: 8, height: 8)
                .opacity(animate ? 1 : 0.3)
                .animation(.easeInOut(duration: 0.6).delay(0.2).repeatForever(), value: animate)
            Circle().fill(Color.gray).frame(width: 8, height: 8)
                .opacity(animate ? 1 : 0.3)
                .animation(.easeInOut(duration: 0.6).delay(0.4).repeatForever(), value: animate)
        }
        .onAppear { animate = true }
        .onDisappear { animate = false }
    }
}




import SwiftUI

struct ChatBubble: View {
    var message: Message

    var body: some View {
        HStack {
            if message.isUser {
                Spacer()
                Text(message.text)
                    .padding()
                    .background(Color.blue.opacity(0.8))
                    .foregroundColor(.white)
                    .cornerRadius(16)
                    .frame(maxWidth: 250, alignment: .trailing)
            } else {
                if message.isTyping {
                    TypingIndicator()
                        .padding()
                        .background(Color.gray.opacity(0.2))
                        .cornerRadius(16)
                } else {
                    Text(message.text)
                        .padding()
                        .background(Color.gray.opacity(0.2))
                        .foregroundColor(.black)
                        .cornerRadius(16)
                        .frame(maxWidth: 250, alignment: .leading)
                }
                Spacer()
            }
        }
        .padding(.horizontal)
        .padding(.vertical, 4)
    }
}



import SwiftUI

let GPT4ALL_URL = "http://127.0.0.1:4891/v1/chat/completions"

struct ContentView: View {
    @State private var userInput = ""
    @State private var messages: [Message] = []

    var body: some View {
        VStack {
            ScrollViewReader { proxy in
                ScrollView {
                    VStack {
                        ForEach(messages) { msg in
                            ChatBubble(message: msg)
                        }
                    }
                    .onChange(of: messages.count) { _ in
                        if let last = messages.last {
                            proxy.scrollTo(last.id, anchor: .bottom)
                        }
                    }
                }
            }

            HStack {
                TextField("Type a message...", text: $userInput)
                    .textFieldStyle(RoundedBorderTextFieldStyle())
                    .padding()

                Button("Send") {
                    sendMessage()
                }
                .padding()
            }
        }
        .padding()
    }

    func sendMessage() {
        let userMsg = Message(text: userInput, isUser: true)
        messages.append(userMsg)

        let typingMsg = Message(text: "", isUser: false, isTyping: true)
        messages.append(typingMsg)

        callGPT4All(prompt: userInput) { response in
            DispatchQueue.main.async {
                messages.removeAll { $0.isTyping }
                let botMsg = Message(text: response, isUser: false)
                messages.append(botMsg)
                userInput = ""
            }
        }
    }

    func callGPT4All(prompt: String, completion: @escaping (String) -> Void) {
        guard let url = URL(string: GPT4ALL_URL) else { return }

        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.addValue("application/json", forHTTPHeaderField: "Content-Type")

        let body: [String: Any] = [
            "model": "gpt4all-lora-quantized",
            "messages": [["role": "user", "content": prompt]]
        ]

        request.httpBody = try? JSONSerialization.data(withJSONObject: body)

        URLSession.shared.dataTask(with: request) { data, _, error in
            if let error = error {
                completion("⚠️ Network error: \(error.localizedDescription)")
                return
            }

            guard let data = data else {
                completion("⚠️ No data received")
                return
            }

            // Safe JSON Parsing
            if let json = try? JSONSerialization.jsonObject(with: data) as? [String: Any],
               let choices = json["choices"] as? [[String: Any]],
               let message = choices.first?["message"] as? [String: Any],
               let content = message["content"] as? String {
                completion(content.trimmingCharacters(in: .whitespacesAndNewlines))
            } else {
                // Try to parse fallback message
                if let text = String(data: data, encoding: .utf8) {
                    completion(text)
                } else {
                    completion("⚠️ Unknown response")
                }
            }
        }.resume()
    }
}
