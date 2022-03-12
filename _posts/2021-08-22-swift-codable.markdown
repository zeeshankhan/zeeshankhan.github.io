---
title: Codable
date: 2021-08-22 13:41:10 +0400
categories: [Swift]
tags: [writing, swift]
---

## Decoding enum

Parsing a `String` value to an `enum` case with undefined case value to an unknown case using `singleValueContainer`

```swift
enum Fruits: String, Decodable {
    case mango, banana, unknown

    /// If we don't implement init decoder then decoding will fail for unknown values
    init(from decoder: Decoder) throws {
        let rawValue = try decoder.singleValueContainer().decode(String.self)
        self = Fruits(rawValue: rawValue) ?? Fruits.unknown
    }
}

let json = """
    [ "mango", "banana", "apple", "other", "unknown", ""]
    """.data(using: .utf8)!
do {
    let fruits = try JSONDecoder().decode(Array<Fruits>.self, from: json)
    dump(fruits)
} catch {
    dump(error)
}
// it will print two fruits cases and rest with unknown cases.
```

## Different data type in response
When we have two API return the same model with different data type values.

```swift
struct IDE: Decodable {
    let name: String
    let build: String
    
    private enum CodingKeys: String, CodingKey {
        case name, build
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        name = try container.decode(String.self, forKey: .name)

        do {
            build = try container.decode(String.self, forKey: .build)
        } catch {
            let buildInt = try container.decode(Int64.self, forKey: .build)
            build = String(buildInt)
        }
    }
}

let json1 =
    """
    {
        "name": "Xcode Beta",
        "build": 1234,
    }
    """
    .data(using: .utf8)!

let json2 =
    """
    {
        "name": "Xcode",
        "build": "1234"
    }
    """
    .data(using: .utf8)!

do {
    let ideBeta = try JSONDecoder().decode(IDE.self, from: json1)
    dump(ideBeta)
    
    let ide = try JSONDecoder().decode(IDE.self, from: json2)
    dump(ide)
} catch {
    dump(error)
}
```

## Decoding optional key

```swift
struct IDE: Decodable {
    let isBeta: Bool?
    
    private enum CodingKeys: String, CodingKey {
        case isBeta
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        isBeta = try container.decodeIfPresent(Bool.self, forKey: .isBeta)
    }
}

let json1 =
    """
    {
        "isBeta": true
    }
    """
    .data(using: .utf8)!

let json2 = "{}".data(using: .utf8)!

do {
    let ideBeta = try JSONDecoder().decode(IDE.self, from: json1)
    dump(ideBeta)
    
    let ide = try JSONDecoder().decode(IDE.self, from: json2)
    dump(ide)

} catch {
    dump(error)
}
```

## Enum with associated value (before Swift 5.5)
```swift
struct Square: Decodable {
    let sideLength: Int
    
    enum CodingKeys: String, CodingKey {
        case sideLength = "side_length"
    }
}

struct Sphere: Decodable {
    let diameter: Int
}

struct Cylinder: Decodable {
    let diameter: Int
    let height: Int
}

enum ObjectType: String, Decodable {
    case square
    case sphere
    case cylinder
    case unknown
    
    init(from decoder: Decoder) throws {
        let container = try decoder.singleValueContainer()
        let type = try container.decode(String.self)
        self = ObjectType(rawValue: type) ?? .unknown
    }
}

enum Object: Decodable {
    case square(Square)
    case sphere(Sphere)
    case cylinder(Cylinder)
    case unknown
    
    enum CodingKeys: String, CodingKey {
        case type
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        guard let type = try? container.decode(ObjectType.self, forKey: .type) else {
            self = .unknown
            return
        }
        
        let objectContainer = try decoder.singleValueContainer()
        
        switch type {
        case .square:
            let square = try objectContainer.decode(Square.self)
            self = .square(square)
        case .sphere:
            let sphere = try objectContainer.decode(Sphere.self)
            self = .sphere(sphere)
        case .cylinder:
            let cylinder = try objectContainer.decode(Cylinder.self)
            self = .cylinder(cylinder)
        case .unknown:
            self = .unknown
        }
    }
}

struct GeometricShapes: Decodable {
    let items: [Object]
}

let JSON = """
    {
        "items": [
            {
                "type": "square",
                "side_length": 12
            },
            {
                "type": "sphere",
                "diameter": 4
            },
            {
                "type": "cylinder",
                "diameter": 4,
                "height": 10
            },
            {
                "type": "lalala",
                "unknown_key": 0
            }
        ]
    }
""".data(using: .utf8)!

let geometricObjects = try JSONDecoder().decode(GeometricShapes.self, from: JSON)
dump(geometricObjects)
```

## Decoding dynamic keys

```swift
let json =
    """
    {
      "S001": {
        "firstName": "Tony",
        "lastName": "Stark"
      },
      "S002": {
        "firstName": "Peter",
        "lastName": "Parker"
      },
      "S003": {
        "firstName": "Bruce",
        "lastName": "Wayne"
      }
    }
    """.data(using: .utf8)!

struct Student: Decodable {
    let firstName: String
    let lastName: String
    
    let studentId: String

    enum CodingKeys: CodingKey {
        case firstName
        case lastName
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        firstName = try container.decode(String.self, forKey: CodingKeys.firstName)
        lastName = try container.decode(String.self, forKey: CodingKeys.lastName)
        studentId = container.codingPath.first!.stringValue
    }
}

struct ClassX: Decodable {
    var students: [Student]
    
    private struct DynamicCodingKeys: CodingKey {

        // Use for string-keyed dictionary
        var stringValue: String
        init?(stringValue: String) {
            self.stringValue = stringValue
        }

        // Use for integer-keyed dictionary
        var intValue: Int?
        init?(intValue: Int) {
            // We are not using this, thus just return nil
            return nil
        }
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: DynamicCodingKeys.self)

        var tempArray = [Student]()
        for key in container.allKeys {
            let decodedObject = try container.decode(Student.self, forKey: key)
            tempArray.append(decodedObject)
        }

        students = tempArray
    }
}

let result = try! JSONDecoder().decode(ClassX.self, from: json)
dump(result)
```

## Decoding key as value
```swift
let json =
    """
    {
        "Xcode": "123xb",
        "AndroidStudio": "345JK",
        "VSCode": "89sdfu89",
        "Slack": "uojakls89"
    }
    """.data(using: .utf8)!

struct Data: Decodable {
    
    let softwares: [Software]
    
    private struct DynamicCodingKeys: CodingKey {

        var stringValue: String
        init?(stringValue: String) {
            self.stringValue = stringValue
        }

        var intValue: Int?
        init?(intValue: Int) {
            return nil
        }
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: DynamicCodingKeys.self)
        var tempArray = [Software]()
        for key in container.allKeys {
            let name = key.stringValue
            let build = try container.decode(String.self, forKey: key)
            tempArray.append(Software(name: name, build: build))
        }
        softwares = tempArray
    }
}

struct Software: Decodable {
    let name: String
    let build: String
}

do {
    let result = try JSONDecoder().decode(Data.self, from: json)
    dump(result.softwares)
} catch {
    dump(error)
}

```

## Decoding single key value

```swift
let json =
    """
    {
        "Xcode": "123xb"
    }
    """.data(using: .utf8)!

struct Software: Decodable {
    let name: String
    let build: String

    private struct DynamicCodingKeys: CodingKey {

        var stringValue: String
        init?(stringValue: String) {
            self.stringValue = stringValue
        }

        var intValue: Int?
        init?(intValue: Int) {
            return nil
        }
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: DynamicCodingKeys.self)
        if let key = container.allKeys.first {
            name = key.stringValue
            build = try container.decode(String.self, forKey: key)
        } else {
            throw DecodingError.dataCorruptedError(forKey: DynamicCodingKeys(stringValue: "")!,
                                                   in: container,
                                                   debugDescription: "Some error")
        }
    }
}

do {
    let result = try JSONDecoder().decode(Software.self, from: json)
    dump(result)
} catch {
    dump(error)
}
```

## Decoding different types in a collection

```swift
let json =
    """
    {
        "todo": {
            "banana": 6,
            "apple": 3,
            "rice": "200g",
            "beans": "500g"
        }
    }
    """
    .data(using: .utf8)!


class WrappedString: Codable {
    let value: String
    
    required init(from decoder: Decoder) throws {
        let container = try decoder.singleValueContainer()
        do {
            // Try to parse it as a String
            value = try container.decode(String.self)
        } catch {
            
            if let tempValue = try? container.decode(Int.self) {
                value = String(tempValue)
            } else {
                throw error
            }
        }
    }
}

class TODO: Codable {
    let items : [String: String]
    private enum CodingKeys: String, CodingKey {
        case items = "todo"
    }
    required init(from decoder: Decoder) throws {
        let decoder = try decoder.container(keyedBy: CodingKeys.self)
        let values = try decoder.decode([String: WrappedString].self, forKey: .items)
        items = values.mapValues { $0.value }
    }
}

do {
    let result = try JSONDecoder().decode(TODO.self, from: json)
    dump(result)
} catch {
    dump(error)
}
```

## Decoding Date URL formatter

```swift
let json =
    """
        {
            "name": "Apple",
            "urlAddress": "www.apple.com",
            "isoFormattedDate": "1975-01-24T21:30:31Z"
        }
    """
    .data(using: .utf8)!

struct Company: Decodable {
    let name: String
    let urlAddress: URL
    let isoFormattedDate: Date
}

var dateFormatter: DateFormatter = {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss'Z'"
    return dateFormatter
}()

do {
    let decoder = JSONDecoder()
    decoder.dateDecodingStrategy = .formatted(dateFormatter)
    let company = try decoder.decode(Company.self, from: json)
    print(company)
} catch {
    print(error.localizedDescription)
}

do {
    let decoder = JSONDecoder()
    decoder.dateDecodingStrategy = .iso8601
    let company = try decoder.decode(Company.self, from: json)
    print(company)
} catch {
    print(error.localizedDescription)
}

```

## Decoding different key for same structure

```swift
let serviceOneJSON = """
{
    "user_name": "Mukesh",
    "user_tagline": "Experience is the name everyone gives to their mistakes",
    "id": 1
}
""".data(using: .utf8)!

let serviceTwoJSON = """
{
    "full_name": "Mukesh Mandora",
    "status": "In order to be irreplaceable, one must always be different",
    "user_id": 12
}
""".data(using: .utf8)!

struct UserOfProduct: Decodable {
    var name: String?
    var userId: Int?
    var tagline: String?
    
    enum ServiceOneCodingKeys: String, CodingKey {
        case name = "user_name"
        case userId = "id"
        case tagline = "user_tagline"
    }
    
    enum ServiceTwoCodingKeys: String, CodingKey {
        case name = "full_name"
        case userId = "user_id"
        case tagline = "status"
    }
    
    init(from decoder: Decoder) throws {
        // Service One JSON Container
        let containerForServiceOne = try decoder.container(keyedBy: ServiceOneCodingKeys.self)
        userId = try containerForServiceOne.decodeIfPresent(Int.self,
                                                            forKey: .userId)
        name = try containerForServiceOne.decodeIfPresent(String.self,
                                                          forKey: .name)
        tagline = try containerForServiceOne.decodeIfPresent(String.self,
                                                             forKey: .tagline)
        
        // Check any one param which is required to not be nil in this case
        // userId, based on that we will take decision to decode from another container
        guard userId == nil else {
            return
        }
        
        // Service Two JSON Container
        let containerForServiceTwo = try decoder.container(keyedBy: ServiceTwoCodingKeys.self)
        userId = try containerForServiceTwo.decodeIfPresent(Int.self,
                                                            forKey: .userId)
        name = try containerForServiceTwo.decodeIfPresent(String.self,
                                                          forKey: .name)
        tagline = try containerForServiceTwo.decodeIfPresent(String.self,
                                                             forKey: .tagline)
        
        // Failure send crash report to find this unknown keys
        guard userId != nil else {
            fatalError("Decooding error")
        }
    }
}
let userObjectFromServiceOne = try! JSONDecoder().decode(UserOfProduct.self, from: serviceOneJSON)
print("User Name from Service One, \(userObjectFromServiceOne.name ?? "")")
print("User Tagline from Service One, \(userObjectFromServiceOne.tagline ?? "")")

let userObjectFromServiceTwo = try! JSONDecoder().decode(UserOfProduct.self, from: serviceTwoJSON)
print("User Name from Service Two, \(userObjectFromServiceTwo.name ?? "")")
print("User Tagline from Service Two, \(userObjectFromServiceTwo.tagline ?? "")")

```
or

```swift
let serviceOneJSON = """
{
    "user_name": "Mukesh",
    "user_tagline": "Experience is the name everyone gives to their mistakes",
    "id": 1
}
""".data(using: .utf8)!

let serviceTwoJSON = """
{
    "full_name": "Mukesh Mandora",
    "status": "In order to be irreplaceable, one must always be different",
    "user_id": 12
}
""".data(using: .utf8)!

struct UserOfProduct: Decodable {
    var name: String
    var userId: Int
    var tagline: String
    
    enum ServiceOneCodingKeys: String, CodingKey {
        case name = "user_name"
        case userId = "id"
        case tagline = "user_tagline"
    }
    
    enum ServiceTwoCodingKeys: String, CodingKey {
        case name = "full_name"
        case userId = "user_id"
        case tagline = "status"
    }
    
    init(from decoder: Decoder) throws {
        if
            let containerForServiceOne = try? decoder.container(keyedBy: ServiceOneCodingKeys.self),
            let userId = try? containerForServiceOne.decode(Int.self, forKey: .userId),
            let name = try? containerForServiceOne.decode(String.self, forKey: .name),
            let tagline = try? containerForServiceOne.decode(String.self, forKey: .tagline) {
            self.userId = userId
            self.name = name
            self.tagline = tagline
        } else if
            let containerForServiceTwo = try? decoder.container(keyedBy: ServiceTwoCodingKeys.self),
            let userId = try? containerForServiceTwo.decode(Int.self, forKey: .userId),
            let name = try? containerForServiceTwo.decode(String.self, forKey: .name),
            let tagline = try? containerForServiceTwo.decode(String.self, forKey: .tagline) {
            self.userId = userId
            self.name = name
            self.tagline = tagline
        } else {
            fatalError("Decooding error")
        }
    }
}

let userObjectFromServiceOne = try! JSONDecoder().decode(UserOfProduct.self, from: serviceOneJSON)
print("User from Service One, \(userObjectFromServiceOne)")

let userObjectFromServiceTwo = try! JSONDecoder().decode(UserOfProduct.self, from: serviceTwoJSON)
print("User from Service Two, \(userObjectFromServiceTwo)")

```

## Throwing custom error

```swift
/// When we want to throw a custom error message
enum Fruits: String, Decodable {
    case mango, banana, unknown

    private enum CodingKeys: CodingKey {
        case type
    }
    
    init(from decoder: Decoder) throws {
        var container = try decoder.unkeyedContainer()
        let type = try container.decode(String.self)
        
        if let val = Fruits(rawValue: type) {
            self = val
        } else {
            throw DecodingError.dataCorruptedError(in: container, debugDescription: "Wrong value")
        }
    }
}

let json =
    """
        [ "mangooo" ]
    """
    .data(using: .utf8)!

do {
    let fruits = try JSONDecoder().decode(Fruits.self, from: json)
    print(fruits)
} catch {
    print(error)
}

```

## References
- [Flight school guide to Codable](https://flight.school/books/codable/)
