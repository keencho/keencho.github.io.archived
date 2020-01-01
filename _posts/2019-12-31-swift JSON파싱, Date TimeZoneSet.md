---
layout: post
title: Swift 스터디
categories: [iOS]
tags: [Swift]
description: Alamofire을 이용한 JSON 파싱 및 milliseconds 변환, Timezone Set
comments: true
---

~~~swift
import UIKit
import Alamofire
import Foundation

class ListViewController: UITableViewController {
    var startVar = 0
    var limitVar = 10
    var typeVar = "ALL"
    var result: AFDataResponse<Any>?
    
    let header: HTTPHeaders = ["Cookie" :  "토큰값", "X-Requested-With" : "XMLHttpRequest"]
    
    let url = "API 주소"
    
    let params = param(start: 0, limit: 10, type: "ALL")
    
    
    override func viewDidLoad() {
        AF.request(url, parameters: params, headers: header).responseJSON { response in
            switch response.result {
                case .success:
                    if let data = response.data {
                        do {
                            let result = try JSONDecoder().decode(Root.self, from: data)
                            let resultDecode = result.data.transList.rows
                            
                        } catch {
                            print(error.localizedDescription)
                        }
                    }
                
                case .failure(let error):
                    print(error)
            }
        
        }
    }
}

public struct Root: Codable {
    let data: Data
}

public struct Data: Codable {
    let transList: TransList
}

public struct TransList: Codable {
    let rows: [rows]
}

public struct rows: Codable {
    let id: String
    let mallName: String
    let sheetNo: String
    let unread: Bool
    let created: Int
    let frName: String
    let stateOrder: Int
    let toName: String
    var arrivalTime: String?
    let mallCode: String
    let tcCode: String
    let optionInfo: String?
    let tag: String
    let currentState: String
    let stateChanged: Int
    let send: Bool
}

func changeDate(date: Int) -> String {
    var newDate = Date(timeIntervalSince1970: TimeInterval(date / 1000))
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy-MM-dd HH:mm"
    dateFormatter.timeZone = TimeZone(abbreviation: "KST")
    
    return dateFormatter.string(from: newDate)
}

struct param: Encodable {
    let start: Int
    let limit: Int
    let type: String
}

~~~

SwiftyJSON을 사용하지 않은 JSON 파싱, Alamofire을 이용한 HTTP 통신, KST로 맞춰져있지 않은 TimeZone 세팅
