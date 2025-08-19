
import UIKit

extension UIViewController {
    func showAlert(title: String, message: String, buttonTitle: String = "OK") {
        let alert = UIAlertController(title: title, message: message, preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: buttonTitle, style: .default, handler: nil))
        self.present(alert, animated: true, completion: nil)
    }
}

how to use

self.showAlert(title: "alerrrt!", message: "this is a alert।")


fetcdata *****

import Foundation

class NetworkManager {
    
    static let shared = NetworkManager()  // Singleton pattern
    
    private init() {}
    
    // Generic GET request with parameters
    func fetchData(from urlString: String, parameters: [String: String], completion: @escaping (Result<Data, Error>) -> Void) {
        
        // URL और parameters को combine करना
        guard var urlComponents = URLComponents(string: urlString) else {
            completion(.failure(NSError(domain: "Invalid URL", code: 0)))
            return
        }
        
        urlComponents.queryItems = parameters.map { URLQueryItem(name: $0.key, value: $0.value) }
        
        guard let url = urlComponents.url else {
            completion(.failure(NSError(domain: "Invalid URL Components", code: 0)))
            return
        }

        let task = URLSession.shared.dataTask(with: url) { data, response, error in
            
            if let error = error {
                completion(.failure(error))
                return
            }
            
            guard let httpResponse = response as? HTTPURLResponse,
                  (200...299).contains(httpResponse.statusCode),
                  let data = data else {
                completion(.failure(NSError(domain: "Invalid Response", code: 0)))
                return
            }
            
            completion(.success(data))
        }
        
        task.resume()
    }
}




how to call

NetworkManager.shared.fetchData(from: url, parameters: params) { result in
    DispatchQueue.main.async {
        switch result {
        case .success(let data):
            // we received data here
            
            self.showAlert(title: "success", message: "fetch all data")
            
        case .failure(let error):
            self.showAlert(title: "erroe", message: error.localizedDescription)
        }
    }
}




