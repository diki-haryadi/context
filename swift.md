/== README.md
# RAWG API Documentation

## Base URLs
- Games List: `https://api.rawg.io/api/games`
- Game Details: `https://api.rawg.io/api/games/{id}`

## Authentication
API Key: `fe9d7ddef6394a068e8f6aa7675aacd6`

### List Games
### With Search Query
```bash
curl "https://api.rawg.io/api/games?key=fe9d7ddef6394a068e8f6aa7675aacd6&search=minecraft&page=1&page_size=10" | jq '.'
```

## Sample Games List Response

```json
{
  "count": 471,
  "next": "https://api.rawg.io/api/games?key=fe9d7ddef6394a068e8f6aa7675aacd6&page=2&page_size=10&search=minecraft",
  "previous": null,
  "results": [
    {
      "id": 22509,
      "slug": "minecraft",
      "name": "Minecraft",
      "released": "2011-11-18",
      "background_image": "https://media.rawg.io/media/games/b4e/b4e4c73d5aa4ec66bbf75375c4847a2b.jpg",
      "rating": 4.43,
      "rating_top": 5,
      "ratings_count": 2231,
      "metacritic": 83,
      "playtime": 27,
      "updated": "2023-11-01T11:44:21"
    }
  ]
}

### Get Game Details
```bash
curl -s "https://api.rawg.io/api/games/3498?key=fe9d7ddef6394a068e8f6aa7675aacd6" | jq '.'
```

## Sample Game Details Response

```json
{
  "id": 3498,
  "slug": "grand-theft-auto-v",
  "name": "Grand Theft Auto V",
  "name_original": "Grand Theft Auto V",
  "description": "<p>Rockstar Games went bigger, since their previous installment of the series. You get the complicated and realistic world-building from Liberty City of GTA4 in the setting of lively and diverse Los Santos, from an old fan favorite GTA San Andreas...</p>",
  "metacritic": 92,
  "released": "2013-09-17",
  "tba": false,
  "background_image": "https://media.rawg.io/media/games/20a/20aa03a10cda45239fe22d035c0ebe64.jpg",
  "rating": 4.47,
  "rating_top": 5,
  "ratings": {
    "id": 5,
    "title": "exceptional",
    "count": 4233,
    "percent": 59.01
  },
  "playtime": 74,
  "screenshots_count": 58,
  "movies_count": 8,
  "creators_count": 11,
  "achievements_count": 539,
  "parent_achievements_count": 75,
  "reddit_url": "https://www.reddit.com/r/GrandTheftAutoV/",
  "reddit_name": "",
  "website": "http://www.rockstargames.com/V/",
  "metacritic_url": "https://www.metacritic.com/game/pc/grand-theft-auto-v"
}
```

## Notes
- Always include the API key in your requests
- Use URL encoding for special characters in the search query
- The API returns results in JSON format
- For game details, replace {id} with the actual game ID in the URL

/== TheMealsApp/App/ContentView.swift
//
//  ContentView.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI

struct ContentView: View {
  @EnvironmentObject var homePresenter: HomePresenter
  @EnvironmentObject var favoritePresenter: FavoritePresenter
  @EnvironmentObject var searchPresenter: SearchPresenter

  var body: some View {
    TabView {
      NavigationStack {
        HomeView(presenter: homePresenter)
      }.tabItem {
        TabItem(imageName: "house", title: "Home")
      }

      NavigationStack {
        SearchView(presenter: searchPresenter)
      }.tabItem {
        TabItem(imageName: "magnifyingglass", title: "Search")
      }

      NavigationStack {
        FavoriteView(presenter: favoritePresenter)
      }.tabItem {
        TabItem(imageName: "heart", title: "Favorite")
      }
    }
  }
}


/== TheMealsApp/App/TheMealsAppApp.swift
//
//  TheMealsAppApp.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI

@main
struct TheMealsAppApp: App {
  let homePresenter = HomePresenter(homeUseCase: Injection.init().provideHome())
  let favoritePresenter = FavoritePresenter(favoriteUseCase: Injection.init().provideFavorite())
  let searchPresenter = SearchPresenter(searchUseCase: Injection.init().provideSearch())

  var body: some Scene {
    WindowGroup {
      ContentView()
        .environmentObject(homePresenter)
        .environmentObject(favoritePresenter)
        .environmentObject(searchPresenter)
    }
  }
}


/== TheMealsApp/Assets.xcassets/AccentColor.colorset/Contents.json
{
  "colors" : [
    {
      "idiom" : "universal"
    }
  ],
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp/Assets.xcassets/AppIcon.appiconset/Contents.json
{
  "images" : [
    {
      "idiom" : "universal",
      "platform" : "ios",
      "size" : "1024x1024"
    }
  ],
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp/Assets.xcassets/Contents.json
{
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp/Assets.xcassets/assetNoFavorite.imageset/Contents.json
{
  "images" : [
    {
      "filename" : "salad.pdf",
      "idiom" : "universal",
      "scale" : "1x"
    },
    {
      "idiom" : "universal",
      "scale" : "2x"
    },
    {
      "idiom" : "universal",
      "scale" : "3x"
    }
  ],
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp/Assets.xcassets/assetSearchMeal.imageset/Contents.json
{
  "images" : [
    {
      "filename" : "search_meal.png",
      "idiom" : "universal",
      "scale" : "1x"
    },
    {
      "idiom" : "universal",
      "scale" : "2x"
    },
    {
      "idiom" : "universal",
      "scale" : "3x"
    }
  ],
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp/Assets.xcassets/assetSearchNotFound.imageset/Contents.json
{
  "images" : [
    {
      "filename" : "drinking-table.pdf",
      "idiom" : "universal",
      "scale" : "1x"
    },
    {
      "idiom" : "universal",
      "scale" : "2x"
    },
    {
      "idiom" : "universal",
      "scale" : "3x"
    }
  ],
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp/Core/DI/Injection.swift
//
//  Injection.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import RealmSwift

final class Injection: NSObject {

  private func provideRepository() -> MealRepositoryProtocol {
    let realm = try? Realm()

    let locale: LocaleDataSource = LocaleDataSource.sharedInstance(realm)
    let remote: RemoteDataSource = RemoteDataSource.sharedInstance

    return MealRepository.sharedInstance(locale, remote)
  }

  func provideHome() -> HomeUseCase {
    let repository = provideRepository()
    return HomeInteractor(repository: repository)
  }

  func provideDetail(category: CategoryModel) -> DetailUseCase {
    let repository = provideRepository()
    return DetailInteractor(repository: repository, category: category)
  }

    func provideMeal(meal: MealModel, game: GameModel? = nil) -> MealUseCase {
    let repository = provideRepository()
        return MealInteractor(repository: repository, meal: meal, game: game ?? <#default value#>)
  }
  
  

  func provideFavorite() -> FavoriteUseCase {
    let repository = provideRepository()
    return FavoriteInteractor(repository: repository)
  }

  func provideSearch() -> SearchUseCase {
    let repository = provideRepository()
    return SearchInteractor(repository: repository)
  }

}


/== TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift
//
//  CategoryEntity.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import RealmSwift

class CategoryEntity: Object {

  @objc dynamic var id: String = ""
  @objc dynamic var title: String = ""
  @objc dynamic var image: String = ""
  @objc dynamic var desc: String = ""

  override static func primaryKey() -> String? {
    return "id"
  }
}


/== TheMealsApp/Core/Data/Locale/Entity/GameEntity.swift
//
//  GameEntity.swift
//  TheMealsApp
//
//  Created by Ben on 29/11/22.
//

import Foundation
import RealmSwift

class GameEntity: Object {
  @objc dynamic var id = 0
  @objc dynamic var slug = ""
  @objc dynamic var name = ""
  @objc dynamic var released = ""
  @objc dynamic var backgroundImage = ""
  @objc dynamic var rating = 0.0
  @objc dynamic var ratingTop = 0
  @objc dynamic var ratingsCount = 0
  @objc dynamic var metacritic = 0
  @objc dynamic var playtime = 0
  @objc dynamic var updated = ""
  @objc dynamic var gameDescription = ""
  @objc dynamic var nameOriginal = ""
  @objc dynamic var tba = false
  @objc dynamic var screenshotsCount = 0
  @objc dynamic var moviesCount = 0
  @objc dynamic var creatorsCount = 0
  @objc dynamic var achievementsCount = 0
  @objc dynamic var parentAchievementsCount = 0
  @objc dynamic var redditUrl = ""
  @objc dynamic var redditName = ""
  @objc dynamic var website = ""
  @objc dynamic var metacriticUrl = ""
  @objc dynamic var favorite = false

  override static func primaryKey() -> String {
    return "id"
  }
}


/== TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift
//
//  IngredientEntity.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import RealmSwift

class IngredientEntity: Object {

  @objc dynamic var id: String = ""
  @objc dynamic var title: String = ""
  @objc dynamic var idMeal: String = ""

}


/== TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift
//
//  MealEntity.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import RealmSwift

class MealEntity: Object {

  @objc dynamic var id = ""
  @objc dynamic var title = ""
  @objc dynamic var image = ""
  @objc dynamic var category = ""
  @objc dynamic var area = ""
  @objc dynamic var instructions = ""
  @objc dynamic var tag = ""
  @objc dynamic var youtube = ""
  @objc dynamic var source = ""
  @objc dynamic var favorite = false

  var ingredients = List<IngredientEntity>()

  override static func primaryKey() -> String? {
    return "id"
  }
}


/== TheMealsApp/Core/Data/Locale/LocaleDataSource.swift
//
//  LocaleDataStore.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import RealmSwift
import Combine

protocol LocaleDataSourceProtocol: AnyObject {

  func getCategories() -> AnyPublisher<[CategoryEntity], Error>
  func addCategories(from categories: [CategoryEntity]) -> AnyPublisher<Bool, Error>

  func getMeal(by idMeal: String) -> AnyPublisher<MealEntity, Error>
  func getMeals(by category: String) -> AnyPublisher<[MealEntity], Error>
  func getMealsBy(_ title: String) -> AnyPublisher<[MealEntity], Error>
  func addMeals(by category: String, from meals: [MealEntity]) -> AnyPublisher<Bool, Error>
  func addMealsBy(_ title: String, from meals: [MealEntity]) -> AnyPublisher<Bool, Error>
  func updateMeal(by idMeal: String, meal: MealEntity) -> AnyPublisher<Bool, Error>

  func addIngredients(from ingredients: [IngredientEntity]) -> AnyPublisher<Bool, Error>

  func getFavoriteMeals() -> AnyPublisher<[MealEntity], Error>
  func updateFavoriteMeal(by idMeal: String) -> AnyPublisher<MealEntity, Error>

  func getGame(by id: Int) -> AnyPublisher<GameEntity, Error>
  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<[GameEntity], Error>
  func addGames(from games: [GameEntity]) -> AnyPublisher<Bool, Error>
  func updateGame(by id: Int, game: GameEntity) -> AnyPublisher<Bool, Error>
  func getFavoriteGames() -> AnyPublisher<[GameEntity], Error>
  func updateFavoriteGame(by id: Int) -> AnyPublisher<GameEntity, Error>

}

final class LocaleDataSource: NSObject {

  private let realm: Realm?

  private init(realm: Realm?) {
    self.realm = realm
  }

  static let sharedInstance: (Realm?) -> LocaleDataSource = { realmDatabase in
    return LocaleDataSource(realm: realmDatabase)
  }

}

extension LocaleDataSource: LocaleDataSourceProtocol {

  func getGame(by id: Int) -> AnyPublisher<GameEntity, Error> {
    return Future<GameEntity, Error> { completion in
      if let realm = self.realm {
        let games: Results<GameEntity> = {
          realm.objects(GameEntity.self)
            .filter("id = %@", id)
        }()

        guard let game = games.first else {
          completion(.failure(DatabaseError.requestFailed))
          return
        }

        completion(.success(game))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func getGames(page: Int = 1, pageSize: Int = 10, search: String? = nil) -> AnyPublisher<[GameEntity], Error> {
    return Future<[GameEntity], Error> { completion in
      if let realm = self.realm {
        var games: Results<GameEntity>
        if let searchQuery = search {
          games = realm.objects(GameEntity.self)
            .filter("title contains[c] %@", searchQuery)
            .sorted(byKeyPath: "title", ascending: true)
        } else {
          games = realm.objects(GameEntity.self)
            .sorted(byKeyPath: "title", ascending: true)
        }
        
        let startIndex = (page - 1) * pageSize
        let endIndex = min(startIndex + pageSize, games.count)
        let paginatedGames = Array(games[startIndex..<endIndex])
        completion(.success(paginatedGames))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func addGames(from games: [GameEntity]) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          try realm.write {
            for game in games {
              realm.add(game, update: .modified)
            }
            completion(.success(true))
          }
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func updateGame(by id: Int, game: GameEntity) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm, let gameEntity = {
        realm.objects(GameEntity.self).filter("id = %@", id)
      }().first {
        do {
          try realm.write {
            gameEntity.setValue(game.name, forKey: "title")
            gameEntity.setValue(game.released, forKey: "released")
            gameEntity.setValue(game.backgroundImage, forKey: "backgroundImage")
            gameEntity.setValue(game.rating, forKey: "rating")
            gameEntity.setValue(game.ratingTop, forKey: "ratingTop")
            gameEntity.setValue(game.favorite, forKey: "favorite")
          }
          completion(.success(true))
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func getFavoriteGames() -> AnyPublisher<[GameEntity], Error> {
    return Future<[GameEntity], Error> { completion in
      if let realm = self.realm {
        let gameEntities = {
          realm.objects(GameEntity.self)
            .filter("favorite = %@", true)
            .sorted(byKeyPath: "title", ascending: true)
        }()
        completion(.success(gameEntities.toArray(ofType: GameEntity.self)))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func updateFavoriteGame(by id: Int) -> AnyPublisher<GameEntity, Error> {
    return Future<GameEntity, Error> { completion in
      if let realm = self.realm, let gameEntity = {
        realm.objects(GameEntity.self).filter("id = %@", id)
      }().first {
        do {
          try realm.write {
            gameEntity.setValue(!gameEntity.favorite, forKey: "favorite")
          }
          completion(.success(gameEntity))
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }


  func getCategories() -> AnyPublisher<[CategoryEntity], Error> {
    return Future<[CategoryEntity], Error> { completion in
      if let realm = self.realm {
        let categories: Results<CategoryEntity> = {
          realm.objects(CategoryEntity.self)
            .sorted(byKeyPath: "title", ascending: true)
        }()
        completion(.success(categories.toArray(ofType: CategoryEntity.self)))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func addCategories(
    from categories: [CategoryEntity]
  ) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          try realm.write {
            for category in categories {
              realm.add(category, update: .all)
            }
            completion(.success(true))
          }
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func getMeal(
    by idMeal: String
  ) -> AnyPublisher<MealEntity, Error> {
    return Future<MealEntity, Error> { completion in
      if let realm = self.realm {
        let meals: Results<MealEntity> = {
          realm.objects(MealEntity.self)
            .filter("id = '\(idMeal)'")
        }()

        guard let meal = meals.first else {
          completion(.failure(DatabaseError.requestFailed))
          return
        }

        completion(.success(meal))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func getMeals(
    by category: String
  ) -> AnyPublisher<[MealEntity], Error> {
    return Future<[MealEntity], Error> { completion in
      if let realm = self.realm {
        let meals: Results<MealEntity> = {
          realm.objects(MealEntity.self)
            .filter("category = '\(category)'")
            .sorted(byKeyPath: "title", ascending: true)
        }()
        completion(.success(meals.toArray(ofType: MealEntity.self)))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func getMealsBy(
    _ title: String
  ) -> AnyPublisher<[MealEntity], Error> {
    return Future<[MealEntity], Error> { completion in
      if let realm = self.realm {
        let meals: Results<MealEntity> = {
          realm.objects(MealEntity.self)
            .filter("title contains[c] %@", title)
            .sorted(byKeyPath: "title", ascending: true)
        }()
        completion(.success(meals.toArray(ofType: MealEntity.self)))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func addMeals(
    by category: String,
    from meals: [MealEntity]
  ) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          try realm.write {
            for meal in meals {
              realm.add(meal, update: .all)
            }
            completion(.success(true))
          }
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func addMealsBy(
    _ title: String,
    from meals: [MealEntity]
  ) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          try realm.write {
            for meal in meals {
              if let mealEntity = realm.object(ofType: MealEntity.self, forPrimaryKey: meal.id) {
                if mealEntity.title == meal.title {
                  meal.favorite = mealEntity.favorite
                  realm.add(meal, update: .all)
                } else {
                  realm.add(meal)
                }
              } else {
                realm.add(meal)
              }
            }
          }
          completion(.success(true))
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func updateMeal(
    by idMeal: String,
    meal: MealEntity
  ) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm, let mealEntity = {
        realm.objects(MealEntity.self).filter("id = '\(idMeal)'")
      }().first {
        do {
          try realm.write {
            mealEntity.setValue(meal.area, forKey: "area")
            mealEntity.setValue(meal.instructions, forKey: "instructions")
            mealEntity.setValue(meal.tag, forKey: "tag")
            mealEntity.setValue(meal.youtube, forKey: "youtube")
            mealEntity.setValue(meal.source, forKey: "source")
            mealEntity.setValue(meal.favorite, forKey: "favorite")
            mealEntity.setValue(meal.ingredients, forKey: "ingredients")
          }
          completion(.success(true))

        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func addIngredients(
    from ingredients: [IngredientEntity]
  ) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          try realm.write {
            for ingredient in ingredients {
              realm.add(ingredient)
            }
            completion(.success(true))
          }
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func getFavoriteMeals() -> AnyPublisher<[MealEntity], Error> {
    return Future<[MealEntity], Error> { completion in
      if let realm = self.realm {
        let mealEntities = {
          realm.objects(MealEntity.self)
            .filter("favorite = \(true)")
            .sorted(byKeyPath: "title", ascending: true)
        }()
        completion(.success(mealEntities.toArray(ofType: MealEntity.self)))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

  func updateFavoriteMeal(
    by idMeal: String
  ) -> AnyPublisher<MealEntity, Error> {
    return Future<MealEntity, Error> { completion in
      if let realm = self.realm, let mealEntity = {
        realm.objects(MealEntity.self).filter("id = '\(idMeal)'")
      }().first {
        do {
          try realm.write {
            mealEntity.setValue(!mealEntity.favorite, forKey: "favorite")
          }
          completion(.success(mealEntity))
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }

}

extension Results {

  func toArray<T>(ofType: T.Type) -> [T] {
    var array = [T]()
    for index in 0 ..< count {
      if let result = self[index] as? T {
        array.append(result)
      }
    }
    return array
  }

}


/== TheMealsApp/Core/Data/MealRepository.swift
//
//  MealRepository.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import Combine

protocol MealRepositoryProtocol {

  func getCategories() -> AnyPublisher<[CategoryModel], Error>
  func getMeal(by idMeal: String) -> AnyPublisher<MealModel, Error>
  func getMeals(by category: String) -> AnyPublisher<[MealModel], Error>
  func searchMeal(by title: String) -> AnyPublisher<[MealModel], Error>
  func getFavoriteMeals() -> AnyPublisher<[MealModel], Error>
  func updateFavoriteMeal(by idMeal: String) -> AnyPublisher<MealModel, Error>
  
  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<[GameModel], Error>
  func getGameDetail(by id: Int) -> AnyPublisher<GameDetailModel, Error>
  func searchGame(by title: String) -> AnyPublisher<[GameModel], Error>
}

final class MealRepository: NSObject {

  typealias MealInstance = (LocaleDataSource, RemoteDataSource) -> MealRepository

  fileprivate let remote: RemoteDataSource
  fileprivate let locale: LocaleDataSource

  private init(locale: LocaleDataSource, remote: RemoteDataSource) {
    self.locale = locale
    self.remote = remote
  }

  static let sharedInstance: MealInstance = { localeRepo, remoteRepo in
    return MealRepository(locale: localeRepo, remote: remoteRepo)
  }

}

extension MealRepository: MealRepositoryProtocol {
  
  func searchGame(
    by title: String
  ) -> AnyPublisher<[GameModel], Error> {
    return self.getGames(search: title)
      .eraseToAnyPublisher()
  }
  
  func getGames(
    page: Int = 1,
    pageSize: Int = 10,
    search: String? = nil
  ) -> AnyPublisher<[GameModel], Error> {
    return self.remote.getGames(page: page, pageSize: pageSize, search: search)
      .map { GameMapper.mapGamesResponseToModels(input: $0) }
      .eraseToAnyPublisher()
  }
  
  func getGameDetail(
    by id: Int
  ) -> AnyPublisher<GameDetailModel, Error> {
    return self.remote.getGameDetail(by: id)
      .map { GameMapper.mapGameDetailResponseToModel(input: $0) }
      .eraseToAnyPublisher()
  }

  func getCategories() -> AnyPublisher<[CategoryModel], Error> {
    return self.locale.getCategories()
      .flatMap { result -> AnyPublisher<[CategoryModel], Error> in
        if result.isEmpty {
          return self.remote.getCategories()
            .map { CategoryMapper.mapCategoryResponsesToEntities(input: $0) }
            .catch { _ in self.locale.getCategories() }
            .flatMap { self.locale.addCategories(from: $0) }
            .filter { $0 }
            .flatMap { _ in self.locale.getCategories()
              .map { CategoryMapper.mapCategoryEntitiesToDomains(input: $0) }
            }
            .eraseToAnyPublisher()
        } else {
          return self.locale.getCategories()
            .map { CategoryMapper.mapCategoryEntitiesToDomains(input: $0) }
            .eraseToAnyPublisher()
        }
      }.eraseToAnyPublisher()
  }

  func getMeal(
    by idMeal: String
  ) -> AnyPublisher<MealModel, Error> {
    return self.locale.getMeal(by: idMeal)
      .flatMap { result -> AnyPublisher<MealModel, Error> in
        if result.ingredients.isEmpty {
          return self.remote.getMeal(by: idMeal)
            .map { MealMapper.mapDetailMealResponseToEntity(by: idMeal, input: $0) }
            .catch { _ in self.locale.getMeal(by: idMeal) }
            .flatMap { self.locale.updateMeal(by: idMeal, meal: $0) }
            .filter { $0 }
            .flatMap { _ in self.locale.getMeal(by: idMeal)
              .map { MealMapper.mapDetailMealEntityToDomain(input: $0) }
            }.eraseToAnyPublisher()
        } else {
          return self.locale.getMeal(by: idMeal)
            .map { MealMapper.mapDetailMealEntityToDomain(input: $0) }
            .eraseToAnyPublisher()
        }
      }.eraseToAnyPublisher()
  }

  func getMeals(
    by category: String
  ) -> AnyPublisher<[MealModel], Error> {
    return self.locale.getMeals(by: category)
      .flatMap { result -> AnyPublisher<[MealModel], Error> in
        if result.isEmpty {
          return self.remote.getMeals(by: category)
            .map { MealMapper.mapMealResponsesToEntities(by: category, input: $0) }
            .catch { _ in self.locale.getMeals(by: category) }
            .flatMap { self.locale.addMeals(by: category, from: $0) }
            .filter { $0 }
            .flatMap { _ in self.locale.getMeals(by: category)
              .map {  MealMapper.mapMealEntitiesToDomains(input: $0) }
            }.eraseToAnyPublisher()
        } else {
          return self.locale.getMeals(by: category)
            .map { MealMapper.mapMealEntitiesToDomains(input: $0) }
            .eraseToAnyPublisher()
        }
      }.eraseToAnyPublisher()
  }

  func searchMeal(
    by title: String
  ) -> AnyPublisher<[MealModel], Error> {
    return self.remote.searchMeal(by: title)
      .map { MealMapper.mapDetailMealResponseToEntity(input: $0) }
      .catch { _ in self.locale.getMealsBy(title) }
      .flatMap { responses  in
        self.locale.getMealsBy(title)
          .flatMap { locale -> AnyPublisher<[MealModel], Error> in
            if responses.count > locale.count {
              return self.locale.addMealsBy(title, from: responses)
                .filter { $0 }
                .flatMap { _ in self.locale.getMealsBy(title)
                  .map { MealMapper.mapDetailMealEntityToDomains(input: $0) }
                }.eraseToAnyPublisher()
            } else {
              return self.locale.getMealsBy(title)
                .map { MealMapper.mapDetailMealEntityToDomains(input: $0) }
                .eraseToAnyPublisher()
            }
          }
      }.eraseToAnyPublisher()
  }

  func getFavoriteMeals() -> AnyPublisher<[MealModel], Error> {
    return self.locale.getFavoriteMeals()
      .map { MealMapper.mapMealEntitiesToDomains(input: $0) }
      .eraseToAnyPublisher()
  }

  func updateFavoriteMeal(
    by idMeal: String
  ) -> AnyPublisher<MealModel, Error> {
    return self.locale.updateFavoriteMeal(by: idMeal)
      .map { MealMapper.mapDetailMealEntityToDomain(input: $0) }
      .eraseToAnyPublisher()
  }

}


/== TheMealsApp/Core/Data/Remote/RemoteDataSource.swift
//
//  RemoteDataSource.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import Alamofire
import Combine

protocol RemoteDataSourceProtocol: AnyObject {

  func getCategories() -> AnyPublisher<[CategoryResponse], Error>
  func getMeal(by id: String) -> AnyPublisher<MealResponse, Error>
  func getMeals(by category: String) -> AnyPublisher<[MealResponse], Error>
  func searchMeal(by title: String) -> AnyPublisher<[MealResponse], Error>
  
  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<GamesResponse, Error>
  func getGameDetail(by id: Int) -> AnyPublisher<GameDetailResponse, Error>

}

final class RemoteDataSource: NSObject {

  private override init() { }

  static let sharedInstance: RemoteDataSource =  RemoteDataSource()

}

extension RemoteDataSource: RemoteDataSourceProtocol {

  func getGames(
    page: Int = 1,
    pageSize: Int = 10,
    search: String? = nil
  ) -> AnyPublisher<GamesResponse, Error> {
    return Future<GamesResponse, Error> { completion in
      var urlString = Endpoints.Gets.games.url
      urlString += "&page=\(page)&page_size=\(pageSize)"
      if let searchQuery = search {
        urlString += "&search=\(searchQuery)"
      }
      
      if let url = URL(string: urlString) {
        AF.request(url)
          .validate()
          .responseDecodable(of: GamesResponse.self) { response in
            switch response.result {
            case .success(let value):
              completion(.success(value))
            case .failure:
              completion(.failure(URLError.invalidResponse))
            }
          }
      }
    }.eraseToAnyPublisher()
  }
  
  func getGameDetail(
    by id: Int
  ) -> AnyPublisher<GameDetailResponse, Error> {
    return Future<GameDetailResponse, Error> { completion in
      if let url = URL(string: Endpoints.Gets.gameDetail(id: id).url) {
        AF.request(url)
          .validate()
          .responseDecodable(of: GameDetailResponse.self) { response in
            switch response.result {
            case .success(let value):
              completion(.success(value))
            case .failure:
              completion(.failure(URLError.invalidResponse))
            }
          }
      }
    }.eraseToAnyPublisher()
  }


  func getCategories() -> AnyPublisher<[CategoryResponse], Error> {
    return Future<[CategoryResponse], Error> { completion in
      if let url = URL(string: Endpoints.Gets.categories.url) {
        AF.request(url)
          .validate()
          .responseDecodable(of: CategoriesResponse.self) { response in
            switch response.result {
            case .success(let value):
              completion(.success(value.categories))
            case .failure:
              completion(.failure(URLError.invalidResponse))
            }
          }
      }
    }.eraseToAnyPublisher()
  }

  func getMeal(
    by id: String
  ) -> AnyPublisher<MealResponse, Error> {
    return Future<MealResponse, Error> { completion in
      if let url = URL(string: Endpoints.Gets.meal.url + id) {
        AF.request(url)
          .validate()
          .responseDecodable(of: MealsResponse.self) { response in
            switch response.result {
            case .success(let value):
              completion(.success(value.meals[0]))
            case .failure:
              completion(.failure(URLError.invalidResponse))
            }
          }
      }
    }.eraseToAnyPublisher()
  }

  func getMeals(
    by category: String
  ) -> AnyPublisher<[MealResponse], Error> {
    return Future<[MealResponse], Error> { completion in
      if let url = URL(string: Endpoints.Gets.meals.url + category) {
        AF.request(url)
          .validate()
          .responseDecodable(of: MealsResponse.self) { response in
            switch response.result {
            case .success(let value):
              completion(.success(value.meals))
            case .failure:
              completion(.failure(URLError.invalidResponse))
            }
          }
      }
    }.eraseToAnyPublisher()
  }

  func searchMeal(
    by title: String
  ) -> AnyPublisher<[MealResponse], Error> {
    return Future<[MealResponse], Error> { completion in
      if let url = URL(string: Endpoints.Gets.search.url + title) {
        AF.request(url)
          .validate()
          .responseDecodable(of: MealsResponse.self) { response in
            switch response.result {
            case .success(let value):
              completion(.success(value.meals))
            case .failure:
              completion(.failure(URLError.invalidResponse))
            }
          }
      }
    }.eraseToAnyPublisher()
  }
}


/== TheMealsApp/Core/Data/Remote/Response/CategoriesResponse.swift
//
//  CategoryResponse.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation

struct CategoriesResponse: Decodable {

  let categories: [CategoryResponse]

}

struct CategoryResponse: Decodable {

  private enum CodingKeys: String, CodingKey {
    case id = "idCategory"
    case title = "strCategory"
    case image = "strCategoryThumb"
    case description = "strCategoryDescription"
  }

  let id: String?
  let title: String?
  let image: String?
  let description: String?

}


/== TheMealsApp/Core/Data/Remote/Response/GamesResponse.swift
//
//  GamesResponse.swift
//  TheMealsApp
//

import Foundation

struct GamesResponse: Codable {
    let count: Int
    let next: String?
    let previous: String?
    let results: [GameResponse]
}

struct GameResponse: Codable {
    let id: Int
    let slug: String
    let name: String
    let released: String?
    let backgroundImage: String?
    let rating: Double
    let ratingTop: Int
    let ratingsCount: Int
    let metacritic: Int?
    let playtime: Int
    let updated: String
    
    enum CodingKeys: String, CodingKey {
        case id, slug, name, released
        case backgroundImage = "background_image"
        case rating
        case ratingTop = "rating_top"
        case ratingsCount = "ratings_count"
        case metacritic, playtime, updated
    }
}

struct GameDetailResponse: Codable {
    let id: Int
    let slug: String
    let name: String
    let nameOriginal: String
    let description: String
    let metacritic: Int?
    let released: String?
    let tba: Bool
    let backgroundImage: String?
    let rating: Double
    let ratingTop: Int
    let playtime: Int
    let screenshotsCount: Int
    let moviesCount: Int
    let creatorsCount: Int
    let achievementsCount: Int
    let parentAchievementsCount: Int
    let redditUrl: String?
    let redditName: String?
    let website: String?
    let metacriticUrl: String?
    
    enum CodingKeys: String, CodingKey {
        case id, slug, name
        case nameOriginal = "name_original"
        case description, metacritic, released, tba
        case backgroundImage = "background_image"
        case rating
        case ratingTop = "rating_top"
        case playtime
        case screenshotsCount = "screenshots_count"
        case moviesCount = "movies_count"
        case creatorsCount = "creators_count"
        case achievementsCount = "achievements_count"
        case parentAchievementsCount = "parent_achievements_count"
        case redditUrl = "reddit_url"
        case redditName = "reddit_name"
        case website
        case metacriticUrl = "metacritic_url"
    }
}

/== TheMealsApp/Core/Data/Remote/Response/MealsResponse.swift
//
//  MealsResponse.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation

struct MealsResponse: Decodable {
  let meals: [MealResponse]
}

struct MealResponse: Decodable {

  private enum CodingKeys: String, CodingKey {
    case id = "idMeal"
    case title = "strMeal"
    case image = "strMealThumb"
    case category = "strCategory"
    case area = "strArea"
    case instructions = "strInstructions"
    case tag = "strTags"
    case youtube = "strYoutube"
    case source = "strSource"

    case ingredient1 = "strIngredient1"
    case ingredient2 = "strIngredient2"
    case ingredient3 = "strIngredient3"
    case ingredient4 = "strIngredient4"
    case ingredient5 = "strIngredient5"
    case ingredient6 = "strIngredient6"
    case ingredient7 = "strIngredient7"
    case ingredient8 = "strIngredient8"
    case ingredient9 = "strIngredient9"
    case ingredient10 = "strIngredient10"
    case ingredient11 = "strIngredient11"
    case ingredient12 = "strIngredient12"
    case ingredient13 = "strIngredient13"
    case ingredient14 = "strIngredient14"
    case ingredient15 = "strIngredient15"
    case ingredient16 = "strIngredient16"
    case ingredient17 = "strIngredient17"
    case ingredient18 = "strIngredient18"
    case ingredient19 = "strIngredient19"
    case ingredient20 = "strIngredient20"

    case measure1 = "strMeasure1"
    case measure2 = "strMeasure2"
    case measure3 = "strMeasure3"
    case measure4 = "strMeasure4"
    case measure5 = "strMeasure5"
    case measure6 = "strMeasure6"
    case measure7 = "strMeasure7"
    case measure8 = "strMeasure8"
    case measure9 = "strMeasure9"
    case measure10 = "strMeasure10"
    case measure11 = "strMeasure11"
    case measure12 = "strMeasure12"
    case measure13 = "strMeasure13"
    case measure14 = "strMeasure14"
    case measure15 = "strMeasure15"
    case measure16 = "strMeasure16"
    case measure17 = "strMeasure17"
    case measure18 = "strMeasure18"
    case measure19 = "strMeasure19"
    case measure20 = "strMeasure20"
  }

  let id: String?
  let title: String?
  let image: String?
  let category: String?
  let area: String?
  let instructions: String?
  let tag: String?
  let youtube: String?
  let source: String?

  let ingredient1: String?
  let ingredient2: String?
  let ingredient3: String?
  let ingredient4: String?
  let ingredient5: String?
  let ingredient6: String?
  let ingredient7: String?
  let ingredient8: String?
  let ingredient9: String?
  let ingredient10: String?
  let ingredient11: String?
  let ingredient12: String?
  let ingredient13: String?
  let ingredient14: String?
  let ingredient15: String?
  let ingredient16: String?
  let ingredient17: String?
  let ingredient18: String?
  let ingredient19: String?
  let ingredient20: String?

  let measure1: String?
  let measure2: String?
  let measure3: String?
  let measure4: String?
  let measure5: String?
  let measure6: String?
  let measure7: String?
  let measure8: String?
  let measure9: String?
  let measure10: String?
  let measure11: String?
  let measure12: String?
  let measure13: String?
  let measure14: String?
  let measure15: String?
  let measure16: String?
  let measure17: String?
  let measure18: String?
  let measure19: String?
  let measure20: String?

}


/== TheMealsApp/Core/Domain/Model/CategoryModel.swift
//
//  CategoryModel.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation

struct CategoryModel: Equatable, Identifiable {

  let id: String
  let title: String
  let image: String
  let description: String

}


/== TheMealsApp/Core/Domain/Model/GameDetailModel.swift
//
//  GameDetailModel.swift
//  TheMealsApp
//
//  Created by Ben on 2024
//

import Foundation

struct GameDetailModel: Equatable, Identifiable {
  let id: Int
  let slug: String
  let name: String
  let nameOriginal: String
  let description: String
  let metacritic: Int
  let released: String
  let tba: Bool
  let backgroundImage: String
  let rating: Double
  let ratingTop: Int
  let playtime: Int
  let screenshotsCount: Int
  let moviesCount: Int
  let creatorsCount: Int
  let achievementsCount: Int
  let parentAchievementsCount: Int
  let redditUrl: String
  let redditName: String
  let website: String
  let metacriticUrl: String
  
  static func == (lhs: GameDetailModel, rhs: GameDetailModel) -> Bool {
    return lhs.id == rhs.id
  }
}

/== TheMealsApp/Core/Domain/Model/GameModel.swift
//
//  GameModel.swift
//  TheMealsApp
//
//  Created by Ben on 11/01/24.
//

import Foundation

struct GameModel: Equatable, Identifiable {
  let id: Int
  let slug: String
  let name: String
  let released: String
  let backgroundImage: String
  let rating: Double
  let ratingTop: Int
  let ratingsCount: Int
  let metacritic: Int
  let playtime: Int
  let updated: String
  var description: String = ""
  var nameOriginal: String = ""
  var tba: Bool = false
  var screenshotsCount: Int = 0
  var moviesCount: Int = 0
  var creatorsCount: Int = 0
  var achievementsCount: Int = 0
  var parentAchievementsCount: Int = 0
  var redditUrl: String = ""
  var redditName: String = ""
  var website: String = ""
  var metacriticUrl: String = ""
  var favorite: Bool = false
}

/== TheMealsApp/Core/Domain/Model/IngredientModel.swift
//
//  IngredientModel.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation

struct IngredientModel: Equatable, Identifiable {

  let id: String
  let title: String
  let idMeal: String

}


/== TheMealsApp/Core/Domain/Model/MealModel.swift
//
//  MealModel.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation

struct MealModel: Equatable, Identifiable {

  let id: String
  let title: String
  let image: String
  var category: String = ""
  var area: String = ""
  var instructions: String = ""
  var tag: String = ""
  var youtube: String = ""
  var source: String = ""
  var ingredients: [IngredientModel] = []
  var favorite: Bool = false

}


/== TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift
//
//  DetailInteractor.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import Combine

protocol DetailUseCase {

  func getCategory() -> CategoryModel
  func getMeals() -> AnyPublisher<[MealModel], Error>
  func getGameDetail() -> AnyPublisher<GameDetailModel, Error>

}

class DetailInteractor: DetailUseCase {

  private let repository: MealRepositoryProtocol
  private let category: CategoryModel

  required init(
    repository: MealRepositoryProtocol,
    category: CategoryModel
  ) {
    self.repository = repository
    self.category = category
  }

  func getCategory() -> CategoryModel {
    return category
  }

  func getMeals() -> AnyPublisher<[MealModel], Error> {
    return repository.getMeals(by: category.title)
  }
  
  func getGameDetail() -> AnyPublisher<GameDetailModel, Error> {
      return repository.getGameDetail(by: Int(category.id) ?? 0)
  }

}


/== TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift
//
//  FavoriteInteractor.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import Combine

protocol FavoriteUseCase {

  func getFavoriteMeals() -> AnyPublisher<[MealModel], Error>

}

class FavoriteInteractor: FavoriteUseCase {

  private let repository: MealRepositoryProtocol

  required init(repository: MealRepositoryProtocol) {
    self.repository = repository
  }

  func getFavoriteMeals() -> AnyPublisher<[MealModel], Error> {
    return repository.getFavoriteMeals()
  }

}


/== TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift
//
//  HomeInteractor.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation
import Combine

protocol HomeUseCase {

  func getCategories() -> AnyPublisher<[CategoryModel], Error>
  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<[GameModel], Error>

}

class HomeInteractor: HomeUseCase {

  private let repository: MealRepositoryProtocol

  required init(repository: MealRepositoryProtocol) {
    self.repository = repository
  }

  func getCategories() -> AnyPublisher<[CategoryModel], Error> {
    return repository.getCategories()
  }

  func getGames(page: Int = 1, pageSize: Int = 10, search: String? = nil) -> AnyPublisher<[GameModel], Error> {
    return repository.getGames(page: page, pageSize: pageSize, search: search)
  }

}


/== TheMealsApp/Core/Domain/UseCase/MealInteractor.swift
//
//  MealInteractor.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import Combine

protocol MealUseCase {

  func getMeal() -> AnyPublisher<MealModel, Error>
  func getMeal() -> MealModel
  func updateFavoriteMeal() -> AnyPublisher<MealModel, Error>
  func getGame() -> AnyPublisher<GameDetailModel, Error>

}

class MealInteractor: MealUseCase {

  private let repository: MealRepositoryProtocol
  private let meal: MealModel
  private let game: GameModel

  required init(
    repository: MealRepositoryProtocol,
    meal: MealModel,
    game: GameModel
  ) {
    self.repository = repository
    self.meal = meal
    self.game = game
  }

  func getMeal() -> AnyPublisher<MealModel, Error> {
    return repository.getMeal(by: meal.id)
  }

  func getMeal() -> MealModel {
    return meal
  }

  func updateFavoriteMeal() -> AnyPublisher<MealModel, Error> {
    return repository.updateFavoriteMeal(by: meal.id)
  }
    

  func getGame() -> AnyPublisher<GameDetailModel, Error> {
      return repository.getGameDetail(by: game.id)
  }
    
  func getGame() -> GameModel {
        return game
  }
}


/== TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift
//
//  SearchInteractor.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import Combine

protocol SearchUseCase {

  func searchMeal(by title: String) -> AnyPublisher<[MealModel], Error>
  func searchGames(by title: String, page: Int) -> AnyPublisher<[GameModel], Error>

}

class SearchInteractor: SearchUseCase {

  private let repository: MealRepositoryProtocol

  required init(repository: MealRepositoryProtocol) {
    self.repository = repository
  }

  func searchMeal(by title: String) -> AnyPublisher<[MealModel], Error> {
    return repository.searchMeal(by: title)
  }

  func searchGames(by title: String, page: Int) -> AnyPublisher<[GameModel], Error> {
      return repository.getGames(page: page, pageSize: 10, search: title)
  }

}


/== TheMealsApp/Core/Utils/Extensions/Color+Ext.swift
//
//  Color+Ext.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI

extension Color {

  static var random: Color {
    return Color(
      red: .random(in: 0...1),
      green: .random(in: 0...1),
      blue: .random(in: 0...1)
    )
  }

}


/== TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift
//
//  CustomeError+Ext.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation

enum URLError: LocalizedError {

  case invalidResponse
  case addressUnreachable(URL)

  var errorDescription: String? {
    switch self {
    case .invalidResponse: return "The server responded with garbage."
    case .addressUnreachable(let url): return "\(url.absoluteString) is unreachable."
    }
  }

}

enum DatabaseError: LocalizedError {

  case invalidInstance
  case requestFailed

  var errorDescription: String? {
    switch self {
    case .invalidInstance: return "Database can't instance."
    case .requestFailed: return "Your request failed."
    }
  }

}


/== TheMealsApp/Core/Utils/Mapper/CategoryMapper.swift
//
//  CategoryMapper.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation

final class CategoryMapper {

  static func mapCategoryResponsesToEntities(
    input categoryResponses: [CategoryResponse]
  ) -> [CategoryEntity] {
    return categoryResponses.map { result in
      let newCategory = CategoryEntity()
      newCategory.id = result.id ?? ""
      newCategory.title = result.title ?? "Unknow"
      newCategory.image = result.image ?? "Unknow"
      newCategory.desc = result.description ?? "Unknow"
      return newCategory
    }
  }

  static func mapCategoryEntitiesToDomains(
    input categoryEntities: [CategoryEntity]
  ) -> [CategoryModel] {
    return categoryEntities.map { result in
      return CategoryModel(
        id: result.id,
        title: result.title,
        image: result.image,
        description: result.desc
      )
    }
  }

  static func mapCategoryResponsesToDomains(
    input categoryResponses: [CategoryResponse]
  ) -> [CategoryModel] {

    return categoryResponses.map { result in
      return CategoryModel(
        id: result.id ?? "",
        title: result.title ?? "Unknow",
        image: result.image ?? "Unknow",
        description: result.description ?? "Unknow"
      )
    }
  }

}


/== TheMealsApp/Core/Utils/Mapper/GameMapper.swift
//
//  GameMapper.swift
//  TheMealsApp
//
//  Created by Ben on 29/11/22.
//

import Foundation

final class GameMapper {
  static func mapGameResponsesToEntities(
    input gameResponses: [GameResponse]
  ) -> [GameEntity] {
    return gameResponses.map { result in
      let newGame = GameEntity()
      newGame.id = result.id
      newGame.slug = result.slug
      newGame.name = result.name
      newGame.released = result.released ?? "Unknown"
      newGame.backgroundImage = result.backgroundImage ?? "Unknown"
      newGame.rating = result.rating
      newGame.ratingTop = result.ratingTop
      newGame.ratingsCount = result.ratingsCount
      newGame.metacritic = result.metacritic ?? 0
      newGame.playtime = result.playtime
      newGame.updated = result.updated
      return newGame
    }
  }

  static func mapGameResponsesToDomains(
    input gameResponses: [GameResponse]
  ) -> [GameModel] {
    return gameResponses.map { result in
      return GameModel(
        id: result.id,
        slug: result.slug,
        name: result.name,
        released: result.released ?? "Unknown",
        backgroundImage: result.backgroundImage ?? "Unknown",
        rating: result.rating,
        ratingTop: result.ratingTop,
        ratingsCount: result.ratingsCount,
        metacritic: result.metacritic ?? 0,
        playtime: result.playtime,
        updated: result.updated
      )
    }
  }

  static func mapGameEntitiesToDomains(
    input gameEntities: [GameEntity]
  ) -> [GameModel] {
    return gameEntities.map { result in
      return GameModel(
        id: result.id,
        slug: result.slug,
        name: result.name,
        released: result.released,
        backgroundImage: result.backgroundImage,
        rating: result.rating,
        ratingTop: result.ratingTop,
        ratingsCount: result.ratingsCount,
        metacritic: result.metacritic,
        playtime: result.playtime,
        updated: result.updated,
        description: result.gameDescription,
        nameOriginal: result.nameOriginal,
        tba: result.tba,
        screenshotsCount: result.screenshotsCount,
        moviesCount: result.moviesCount,
        creatorsCount: result.creatorsCount,
        achievementsCount: result.achievementsCount,
        parentAchievementsCount: result.parentAchievementsCount,
        redditUrl: result.redditUrl,
        redditName: result.redditName,
        website: result.website,
        metacriticUrl: result.metacriticUrl,
        favorite: result.favorite
      )
    }
  }

  static func mapDetailGameEntityToDomain(
    input gameEntity: GameEntity
  ) -> GameModel {
    return GameModel(
      id: gameEntity.id,
      slug: gameEntity.slug,
      name: gameEntity.name,
      released: gameEntity.released,
      backgroundImage: gameEntity.backgroundImage,
      rating: gameEntity.rating,
      ratingTop: gameEntity.ratingTop,
      ratingsCount: gameEntity.ratingsCount,
      metacritic: gameEntity.metacritic,
      playtime: gameEntity.playtime,
      updated: gameEntity.updated,
      description: gameEntity.gameDescription,
      nameOriginal: gameEntity.nameOriginal,
      tba: gameEntity.tba,
      screenshotsCount: gameEntity.screenshotsCount,
      moviesCount: gameEntity.moviesCount,
      creatorsCount: gameEntity.creatorsCount,
      achievementsCount: gameEntity.achievementsCount,
      parentAchievementsCount: gameEntity.parentAchievementsCount,
      redditUrl: gameEntity.redditUrl,
      redditName: gameEntity.redditName,
      website: gameEntity.website,
      metacriticUrl: gameEntity.metacriticUrl,
      favorite: gameEntity.favorite
    )
  }

  static func mapDetailGameResponseToEntity(
    input gameResponse: GameDetailResponse
  ) -> GameEntity {
    let gameEntity = GameEntity()
    gameEntity.id = gameResponse.id
    gameEntity.slug = gameResponse.slug
    gameEntity.name = gameResponse.name
    gameEntity.nameOriginal = gameResponse.nameOriginal
    gameEntity.gameDescription = gameResponse.description
    gameEntity.metacritic = gameResponse.metacritic ?? 0
    gameEntity.released = gameResponse.released ?? "Unknown"
    gameEntity.tba = gameResponse.tba
    gameEntity.backgroundImage = gameResponse.backgroundImage ?? "Unknown"
    gameEntity.rating = gameResponse.rating
    gameEntity.ratingTop = gameResponse.ratingTop
    gameEntity.playtime = gameResponse.playtime
    gameEntity.screenshotsCount = gameResponse.screenshotsCount
    gameEntity.moviesCount = gameResponse.moviesCount
    gameEntity.creatorsCount = gameResponse.creatorsCount
    gameEntity.achievementsCount = gameResponse.achievementsCount
    gameEntity.parentAchievementsCount = gameResponse.parentAchievementsCount
    gameEntity.redditUrl = gameResponse.redditUrl ?? ""
    gameEntity.redditName = gameResponse.redditName ?? ""
    gameEntity.website = gameResponse.website ?? ""
    gameEntity.metacriticUrl = gameResponse.metacriticUrl ?? ""
    return gameEntity
  }

  static func mapGamesResponseToModels(
    input gamesResponse: GamesResponse
  ) -> [GameModel] {
    return mapGameResponsesToDomains(input: gamesResponse.results)
  }

  static func mapGameDetailResponseToModel(
    input gameDetailResponse: GameDetailResponse
  ) -> GameDetailModel {
    let gameEntity = mapDetailGameResponseToEntity(input: gameDetailResponse)
    return GameDetailModel(
      id: gameEntity.id,
      slug: gameEntity.slug,
      name: gameEntity.name,
      nameOriginal: gameEntity.nameOriginal,
      description: gameEntity.gameDescription,
      metacritic: gameEntity.metacritic,
      released: gameEntity.released,
      tba: gameEntity.tba,
      backgroundImage: gameEntity.backgroundImage,
      rating: gameEntity.rating,
      ratingTop: gameEntity.ratingTop,
      playtime: gameEntity.playtime,
      screenshotsCount: gameEntity.screenshotsCount,
      moviesCount: gameEntity.moviesCount,
      creatorsCount: gameEntity.creatorsCount,
      achievementsCount: gameEntity.achievementsCount,
      parentAchievementsCount: gameEntity.parentAchievementsCount,
      redditUrl: gameEntity.redditUrl,
      redditName: gameEntity.redditName,
      website: gameEntity.website,
      metacriticUrl: gameEntity.metacriticUrl
    )
  }
}


/== TheMealsApp/Core/Utils/Mapper/IngredientMapper.swift
//
//  IngredientMapper.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import RealmSwift

final class IngredientMapper {

  static func mapIngredientEntitiesToDomains(
    input ingredientEntities: [IngredientEntity]
  ) -> [IngredientModel] {
    return ingredientEntities.map { result in
      return IngredientModel(
        id: result.id,
        title: result.title,
        idMeal: result.idMeal
      )
    }
  }

  static func mapIngredientResponseToEntities(
     by idMeal: String,
     input mealResponse: MealResponse
   ) -> List<IngredientEntity> {
     let ingredientEntities = List<IngredientEntity>()
     var ingredients = [
       mealResponse.ingredient1, mealResponse.ingredient2,
       mealResponse.ingredient3, mealResponse.ingredient4,
       mealResponse.ingredient5, mealResponse.ingredient6,
       mealResponse.ingredient7, mealResponse.ingredient8,
       mealResponse.ingredient9, mealResponse.ingredient10,
       mealResponse.ingredient11, mealResponse.ingredient12,
       mealResponse.ingredient13, mealResponse.ingredient14,
       mealResponse.ingredient15, mealResponse.ingredient16,
       mealResponse.ingredient17, mealResponse.ingredient18,
       mealResponse.ingredient19, mealResponse.ingredient20
     ].compactMap { $0 }
     ingredients = ingredients.filter({ $0 != ""})

     var measures = [
       mealResponse.measure1, mealResponse.measure2,
       mealResponse.measure3, mealResponse.measure4,
       mealResponse.measure5, mealResponse.measure6,
       mealResponse.measure7, mealResponse.measure8,
       mealResponse.measure9, mealResponse.measure10,
       mealResponse.measure11, mealResponse.measure12,
       mealResponse.measure13, mealResponse.measure14,
       mealResponse.measure15, mealResponse.measure16,
       mealResponse.measure17, mealResponse.measure18,
       mealResponse.measure19, mealResponse.measure20
     ].compactMap { $0 }
     measures = measures.filter({ $0 != ""})

     let ingredientStrings = zip(ingredients, measures)
       .map { "\($0) \($1)" }

     for (index, ingredient) in ingredientStrings.enumerated() {
       let ingredientEntity = IngredientEntity()
       ingredientEntity.id = "\(index+1)"
       ingredientEntity.title = "\(index+1). \(ingredient)"
       ingredientEntity.idMeal = idMeal
       ingredientEntities.append(ingredientEntity)
     }

     return ingredientEntities
   }

   static func mapIngredientResponseToDomains(
     by idMeal: String,
     input mealResponse: MealResponse
   ) -> [IngredientModel] {
     var ingredientDomains: [IngredientModel] = []
     var ingredients = [
       mealResponse.ingredient1, mealResponse.ingredient2,
       mealResponse.ingredient3, mealResponse.ingredient4,
       mealResponse.ingredient5, mealResponse.ingredient6,
       mealResponse.ingredient7, mealResponse.ingredient8,
       mealResponse.ingredient9, mealResponse.ingredient10,
       mealResponse.ingredient11, mealResponse.ingredient12,
       mealResponse.ingredient13, mealResponse.ingredient14,
       mealResponse.ingredient15, mealResponse.ingredient16,
       mealResponse.ingredient17, mealResponse.ingredient18,
       mealResponse.ingredient19, mealResponse.ingredient20
     ].compactMap { $0 }
     ingredients = ingredients.filter({ $0 != ""})

     var measures = [
       mealResponse.measure1, mealResponse.measure2,
       mealResponse.measure3, mealResponse.measure4,
       mealResponse.measure5, mealResponse.measure6,
       mealResponse.measure7, mealResponse.measure8,
       mealResponse.measure9, mealResponse.measure10,
       mealResponse.measure11, mealResponse.measure12,
       mealResponse.measure13, mealResponse.measure14,
       mealResponse.measure15, mealResponse.measure16,
       mealResponse.measure17, mealResponse.measure18,
       mealResponse.measure19, mealResponse.measure20
     ].compactMap { $0 }
     measures = measures.filter({ $0 != ""})

     let ingredientStrings = zip(ingredients, measures)
       .map { "\($0) \($1)" }

     for (index, ingredient) in ingredientStrings.enumerated() {
       let ingredientDomain = IngredientModel(
         id: "\(index+1)",
         title: "\(index+1). \(ingredient)",
         idMeal: idMeal
       )
       ingredientDomains.append(ingredientDomain)
     }
     return ingredientDomains
   }

}


/== TheMealsApp/Core/Utils/Mapper/MealMapper.swift
//
//  MealMapper.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation

final class MealMapper {
  static func mapMealResponsesToEntities(
    by category: String,
    input mealResponses: [MealResponse]
  ) -> [MealEntity] {
    return mealResponses.map { result in
      let newMeal = MealEntity()
      newMeal.id = result.id ?? ""
      newMeal.title = result.title ?? "Unknow"
      newMeal.image = result.image ?? "Unknow"
      newMeal.category = category
      return newMeal
    }
  }

  static func mapMealResponsesToDomains(
    by category: String,
    input mealResponses: [MealResponse]
  ) -> [MealModel] {
    return mealResponses.map { result in
      var newMeal = MealModel(
        id: result.id ?? "",
        title: result.title ?? "Unknow",
        image: result.image ?? "Unknow"
      )
      newMeal.category = category
      return newMeal
    }
  }

  static func mapMealResponsesToDomains(
    input mealResponses: [MealResponse]
  ) -> [MealModel] {
    return mealResponses.map { result in
      let ingredients = IngredientMapper.mapIngredientResponseToDomains(
        by: result.id ?? "",
        input: result
      )
      return MealModel(
        id: result.id ?? "",
        title: result.title ?? "Unknow",
        image: result.image ?? "Unknow",
        category: result.category ?? "Unknow",
        area: result.area ?? "Unknow",
        instructions: result.instructions ?? "Unknow",
        tag: result.tag ?? "Unknow",
        youtube: result.youtube ?? "Unknow",
        source: result.source ?? "Unknow",
        ingredients: ingredients
      )
    }
  }

  static func mapMealEntitiesToDomains(
    input mealEntities: [MealEntity]
  ) -> [MealModel] {
    return mealEntities.map { result in
      let ingredients = IngredientMapper.mapIngredientEntitiesToDomains(
        input: Array(result.ingredients)
      )
      return MealModel(
        id: result.id ,
        title: result.title,
        image: result.image,
        category: result.category,
        area: result.area,
        instructions: result.instructions,
        tag: result.tag,
        youtube: result.youtube,
        source: result.source,
        ingredients: ingredients,
        favorite: result.favorite
      )
    }
  }

  static func mapDetailMealEntityToDomain(
    input mealEntity: MealEntity
  ) -> MealModel {
    let ingredients = IngredientMapper.mapIngredientEntitiesToDomains(
      input: Array(mealEntity.ingredients)
    )
    return MealModel(
      id: mealEntity.id ,
      title: mealEntity.title,
      image: mealEntity.image,
      category: mealEntity.category,
      area: mealEntity.area,
      instructions: mealEntity.instructions,
      tag: mealEntity.tag,
      youtube: mealEntity.youtube,
      source: mealEntity.source,
      ingredients: ingredients,
      favorite: mealEntity.favorite
    )
  }

  static func mapDetailMealEntityToDomains(
    input mealEntities: [MealEntity]
  ) -> [MealModel] {
    return mealEntities.map { result in
      let ingredients = IngredientMapper.mapIngredientEntitiesToDomains(
        input: Array(result.ingredients)
      )
      return MealModel(
        id: result.id ,
        title: result.title,
        image: result.image,
        category: result.category,
        area: result.area,
        instructions: result.instructions,
        tag: result.tag,
        youtube: result.youtube,
        source: result.source,
        ingredients: ingredients,
        favorite: result.favorite
      )
    }
  }

  static func mapDetailMealResponseToEntity(
    by idMeal: String,
    input mealResponse: MealResponse
  ) -> MealEntity {
    let ingredients = IngredientMapper.mapIngredientResponseToEntities(
      by: idMeal,
      input: mealResponse
    )
    let mealEntity = MealEntity()
    mealEntity.id = mealResponse.id ?? ""
    mealEntity.title = mealResponse.title ?? "Unknow"
    mealEntity.image = mealResponse.image ?? "Unknow"
    mealEntity.category = mealResponse.category ?? "Unknow"
    mealEntity.area = mealResponse.area ?? "Unknow"
    mealEntity.instructions = mealResponse.instructions ?? "Unknow"
    mealEntity.tag = mealResponse.tag ?? "Unknow"
    mealEntity.youtube = mealResponse.youtube ?? "Unknow"
    mealEntity.source = mealResponse.source ?? "Unknow"
    mealEntity.ingredients = ingredients
    return mealEntity
  }

  static func mapDetailMealResponseToEntity(
    input mealResponse: [MealResponse]
  ) -> [MealEntity] {
    return mealResponse.map { result in
      let ingredients = IngredientMapper.mapIngredientResponseToEntities(
        by: result.id ?? "",
        input: result
      )
      let mealEntity = MealEntity()
      mealEntity.id = result.id ?? ""
      mealEntity.title = result.title ?? "Unknow"
      mealEntity.image = result.image ?? "Unknow"
      mealEntity.category = result.category ?? "Unknow"
      mealEntity.area = result.area ?? "Unknow"
      mealEntity.instructions = result.instructions ?? "Unknow"
      mealEntity.tag = result.tag ?? "Unknow"
      mealEntity.youtube = result.youtube ?? "Unknow"
      mealEntity.source = result.source ?? "Unknow"
      mealEntity.ingredients = ingredients
      return mealEntity
    }
  }

}


/== TheMealsApp/Core/Utils/Network/APICall.swift
//
//  APICall.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import Foundation

struct API {

  static let mealBaseUrl = "https://www.themealdb.com/api/json/v1/1/"
  static let gameBaseUrl = "https://api.rawg.io/api/"
  static let gameApiKey = "fe9d7ddef6394a068e8f6aa7675aacd6"

}

protocol Endpoint {

  var url: String { get }

}

enum Endpoints {

  enum Gets: Endpoint {
    case categories
    case meals
    case meal
    case search
    case games
    case gameDetail(id: Int)

    public var url: String {
      switch self {
      case .categories: return "\(API.mealBaseUrl)categories.php"
      case .meals: return "\(API.mealBaseUrl)filter.php?c="
      case .meal: return "\(API.mealBaseUrl)lookup.php?i="
      case .search: return "\(API.mealBaseUrl)search.php?s="
      case .games: return "\(API.gameBaseUrl)games?key=\(API.gameApiKey)"
      case .gameDetail(let id): return "\(API.gameBaseUrl)games/\(id)?key=\(API.gameApiKey)"
      }
    }
  }

}


/== TheMealsApp/Core/Utils/View/CustomEmptyView.swift
//
//  CustomEmptyView.swift
//  TheMealsApp
//
//  Created by Ari Supriatna on 08/09/20.
//  Copyright Â© 2020 Dicoding Indonesia. All rights reserved.
//

import SwiftUI

struct CustomEmptyView: View {
  var image: String
  var title: String

  var body: some View {
    VStack {
      Image(image)
        .resizable()
        .renderingMode(.original)
        .scaledToFit()
        .frame(width: 250)

      Text(title)
        .font(.system(.body, design: .rounded))
    }
  }
}


/== TheMealsApp/Core/Utils/View/CustomIcon.swift
//
//  CustomIcon.swift
//  TheMealsApp
//
//  Created by Ari Supriatna on 19/08/20.
//  Copyright Â© 2020 Dicoding Indonesia. All rights reserved.
//

import SwiftUI

struct CustomIcon: View {

  var imageName: String
  var title: String

  var body: some View {
    VStack {
      Image(systemName: imageName)
        .font(.system(size: 28))
        .foregroundColor(.orange)

      Text(title)
        .font(.caption)
        .padding(.top, 8)
    }
  }

}


/== TheMealsApp/Core/Utils/View/TabItem.swift
//
//  TabItem.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI

struct TabItem: View {

  var imageName: String
  var title: String
  var body: some View {
    VStack {
      Image(systemName: imageName)
      Text(title)
    }
  }

}


/== TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift
//
//  DetailPresenter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI
import Combine

class DetailPresenter: ObservableObject {

  private var cancellables: Set<AnyCancellable> = []
  private let router = DetailRouter()
  private let detailUseCase: DetailUseCase

  @Published var meals: [MealModel] = []
  @Published var category: CategoryModel
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false

  init(detailUseCase: DetailUseCase) {
    self.detailUseCase = detailUseCase
    category = detailUseCase.getCategory()
  }

  func getMeals() {
    isLoading = true
    detailUseCase.getMeals()
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { completion in
        switch completion {
        case .failure(let error):
          self.errorMessage = error.localizedDescription
          self.isError = true
          self.isLoading = false
        case .finished:
          self.isLoading = false
        }
      }, receiveValue: { meals in
        self.meals = meals
      })
      .store(in: &cancellables)
  }

  func linkBuilder<Content: View>(
    for meal: MealModel,
    game: GameModel,
    @ViewBuilder content: () -> Content
  ) -> some View {
      NavigationLink(destination: router.makeMealView(for: meal, game: game)) { content() }
  }

}


/== TheMealsApp/Module/Detail/Router/DetailRouter.swift
//
//  DetailRouter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI

class DetailRouter {

func makeMealView(for meal: MealModel, game: GameModel? = nil) -> some View {
  let mealUseCase = Injection.init().provideMeal(meal: meal, game: game)
  let presenter = MealPresenter(mealUseCase: mealUseCase)
  return MealView(presenter: presenter)
}

}


/== TheMealsApp/Module/Detail/View/DetailView.swift
//
//  File.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI
import CachedAsyncImage

struct DetailView: View {
  @ObservedObject var presenter: DetailPresenter

  var body: some View {
    ZStack {
      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isLoading {
        errorIndicator
      } else {
        ScrollView(.vertical) {
          VStack {
            imageCategory
            spacer
            content
            spacer
          }.padding()
        }
      }
    }.onAppear {
      if self.presenter.meals.count == 0 {
        self.presenter.getMeals()
      }
    }.navigationBarTitle(Text(self.presenter.category.title), displayMode: .large)
  }
}

extension DetailView {
  var spacer: some View {
    Spacer()
  }

  var loadingIndicator: some View {
    VStack {
      Text("Loading...")
      ProgressView()
    }
  }

  var errorIndicator: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: presenter.errorMessage
    ).offset(y: 80)
  }

  var imageCategory: some View {
    CachedAsyncImage(url: URL(string: self.presenter.category.image)) { image in
      image.resizable()
    } placeholder: {
      ProgressView()
    }.scaledToFit().frame(width: 250.0, height: 250.0, alignment: .center)
  }

  var mealsHorizontal: some View {
    ScrollView(.horizontal) {
      HStack {
        ForEach(self.presenter.meals, id: \.id) { meal in
          ZStack {
            self.presenter.linkBuilder(for: meal) {
              MealRow(meal: meal)
                .frame(width: 150, height: 150)
            }.buttonStyle(PlainButtonStyle())
          }
        }
      }
    }
  }

  var description: some View {
    Text(self.presenter.category.description)
      .font(.system(size: 15))
  }

  func headerTitle(_ title: String) -> some View {
    return Text(title)
      .font(.headline)
  }

  var content: some View {
    VStack(alignment: .leading, spacing: 0) {
      if !presenter.meals.isEmpty {
        headerTitle("Meals from \(self.presenter.category.title)")
          .padding(.bottom)
        mealsHorizontal
      }
      spacer
      headerTitle("Description")
        .padding([.top, .bottom])
      description
    }
  }
}


/== TheMealsApp/Module/Detail/View/MealRow.swift
//
//  MealRow.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import SwiftUI
import CachedAsyncImage

struct MealRow: View {
  var meal: MealModel

  var body: some View {
    GeometryReader { geometry in
      ZStack(alignment: .bottomLeading) {
        self.imageMeal
          .frame(
            width: geometry.size.width,
            height: geometry.size.height,
            alignment: .center
        )
        EmptyView().frame(
          width: geometry.size.width,
          height: 32
        ).blur(radius: 20)

        self.titleMeal
      }
    }.cornerRadius(12)
  }
}

extension MealRow {

  var imageMeal: some View {
    CachedAsyncImage(url: URL(string: self.meal.image)) { image in
      image.resizable()
    } placeholder: {
      ProgressView()
    }.scaledToFit()
  }

  var titleMeal: some View {
    Text(self.meal.title)
      .font(.system(size: 14))
      .lineLimit(1)
      .foregroundColor(.white)
      .padding(EdgeInsets(top: 0, leading: 8, bottom: 8, trailing: 8))
  }

}


/== TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift
//
//  FavoritePresenter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI
import Combine

class FavoritePresenter: ObservableObject {

  private var cancellables: Set<AnyCancellable> = []
  private let router = FavoriteRouter()
  private let favoriteUseCase: FavoriteUseCase

  @Published var meals: [MealModel] = []
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false

  init(favoriteUseCase: FavoriteUseCase) {
    self.favoriteUseCase = favoriteUseCase
  }

  func getFavoriteMeals() {
    isLoading = true
    favoriteUseCase.getFavoriteMeals()
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { completion in
          switch completion {
          case .failure(let error):
            self.errorMessage = error.localizedDescription
            self.isError = true
          case .finished:
            self.isLoading = false
          }
        }, receiveValue: { meals in
          self.meals = meals
        })
        .store(in: &cancellables)
  }

  func linkBuilder<Content: View>(
    for meal: MealModel,
    game: GameModel,
    @ViewBuilder content: () -> Content
  ) -> some View {
      NavigationLink(destination: router.makeMealView(for: meal, game: game)) { content() }
  }

}


/== TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift
//
//  FavoriteRouter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI

class FavoriteRouter {

    func makeMealView(for meal: MealModel, game: GameModel) -> some View {
        let mealUseCase = Injection.init().provideMeal(meal: meal, game: game)
        let presenter = MealPresenter(mealUseCase: mealUseCase)
        return MealView(presenter: presenter)
  }

}


/== TheMealsApp/Module/Favorite/View/FavoriteRow.swift
//
//  FavoriteRow.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI
import CachedAsyncImage

struct FavoriteRow: View {

  var meal: MealModel

  var body: some View {
    VStack {
      HStack(alignment: .top) {
        imageCategory
        content
        Spacer()
      }
      .padding(.horizontal, 16)
      .padding(.vertical, 8)

      Divider()
        .padding(.leading)
    }
  }

}

extension FavoriteRow {

  var imageCategory: some View {
    CachedAsyncImage(url: URL(string: meal.image)) { image in
      image.resizable()
    } placeholder: {
      ProgressView()
    }.cornerRadius(20).scaledToFit().frame(width: 120)
  }

  var content: some View {
    VStack(alignment: .leading, spacing: 10) {
      Text(meal.title)
        .font(.system(size: 20, weight: .semibold, design: .rounded))
        .lineLimit(3)

      Text(meal.category)
        .font(.system(size: 16))
        .lineLimit(2)

      if !meal.area.isEmpty {
        Text("From \(meal.area)")
          .font(.system(size: 14))
          .lineLimit(2)
      }

    }.padding(
      EdgeInsets(
        top: 0,
        leading: 16,
        bottom: 16,
        trailing: 16
      )
    )
  }

}


/== TheMealsApp/Module/Favorite/View/FavoriteView.swift
//
//  FavoriteView.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI

struct FavoriteView: View {

  @ObservedObject var presenter: FavoritePresenter

  var body: some View {
    ZStack {

      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isError {
        errorIndicator
      } else if presenter.meals.count == 0 {
        emptyFavorites
      } else {
        content
      }
    }.onAppear {
      self.presenter.getFavoriteMeals()
    }.navigationBarTitle(
      Text("Favorite Meals"),
      displayMode: .automatic
    )
  }

}

extension FavoriteView {
  var loadingIndicator: some View {
    VStack {
      Text("Loading...")
      ProgressView()
    }
  }

  var errorIndicator: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: presenter.errorMessage
    ).offset(y: 80)
  }

  var emptyFavorites: some View {
    CustomEmptyView(
      image: "assetNoFavorite",
      title: "Your favorite is empty"
    ).offset(y: 80)
  }

  var content: some View {
    ScrollView(
      .vertical,
      showsIndicators: false
    ) {
      ForEach(
        self.presenter.meals,
        id: \.id
      ) { meal in
        ZStack {
            self.presenter.linkBuilder(for: meal, game: <#GameModel#>) {
            FavoriteRow(meal: meal)
          }.buttonStyle(PlainButtonStyle())
        }

      }
    }
  }
}


/== TheMealsApp/Module/Home/Presenter/HomePresenter.swift
//
//  HomePresenter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI
import Combine

class HomePresenter: ObservableObject {

  private var cancellables: Set<AnyCancellable> = []
  private let router = HomeRouter()
  private let homeUseCase: HomeUseCase

  @Published var categories: [CategoryModel] = []
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false

  init(homeUseCase: HomeUseCase) {
    self.homeUseCase = homeUseCase
  }

  func getCategories() {
    isLoading = true
    homeUseCase.getCategories()
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { completion in
        switch completion {
        case .failure(let error):
          self.errorMessage = error.localizedDescription
          self.isError = true
          self.isLoading = false
        case .finished:
          self.isLoading = false
        }
      }, receiveValue: { categories in
        self.categories = categories
      })
      .store(in: &cancellables)
  }

  func linkBuilder<Content: View>(
    for category: CategoryModel,
    @ViewBuilder content: () -> Content
  ) -> some View {
    NavigationLink(destination: router.makeDetailView(for: category)) { content() }
  }

}


/== TheMealsApp/Module/Home/Router/HomeRouter.swift
//
//  HomeRouter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI

class HomeRouter {

  func makeDetailView(for category: CategoryModel) -> some View {
    let detailUseCase = Injection.init().provideDetail(category: category)
    let presenter = DetailPresenter(detailUseCase: detailUseCase)
    return DetailView(presenter: presenter)
  }

}


/== TheMealsApp/Module/Home/View/CategoryRow.swift
//
//  CategoryRow.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI
import CachedAsyncImage

struct CategoryRow: View {

  var category: CategoryModel
  var body: some View {
    VStack {
      imageCategory
      content
    }
    .frame(width: UIScreen.main.bounds.width - 32, height: 250)
    .background(Color.random.opacity(0.3))
    .cornerRadius(30)
  }

}

extension CategoryRow {

  var imageCategory: some View {
    CachedAsyncImage(url: URL(string: category.image)) { image in
      image.resizable()
    } placeholder: {
      ProgressView()
    }.cornerRadius(30).scaledToFit().frame(width: 200).padding(.top)
  }

  var content: some View {
    VStack(alignment: .leading, spacing: 10) {
      Text(category.title)
        .font(.title)
        .bold()

      Text(category.description)
        .font(.system(size: 14))
        .lineLimit(2)
    }.padding(
      EdgeInsets(
        top: 0,
        leading: 16,
        bottom: 16,
        trailing: 16
      )
    )
  }

}

struct CategoryRow_Previews: PreviewProvider {

  static var previews: some View {
    let meal = CategoryModel(
      id: "1",
      title: "Beef",
      image: "https://www.themealdb.com/images/category/beef.png",
      description: "Beef is the culinary name for meat from cattle, particularly skeletal muscle."
    )
    return CategoryRow(category: meal)
  }

}


/== TheMealsApp/Module/Home/View/HomeView.swift
//
//  HomeView.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI

struct HomeView: View {

  @ObservedObject var presenter: HomePresenter

  var body: some View {
    ZStack {
      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isError {
        errorIndicator
      } else if presenter.categories.isEmpty {
        emptyCategories
      } else {
        content
      }
    }.onAppear {
      if self.presenter.categories.count == 0 {
        self.presenter.getCategories()
      }
    }.navigationBarTitle(
      Text("Meals Apps"),
      displayMode: .automatic
    )
  }

}

extension HomeView {

  var loadingIndicator: some View {
    VStack {
      Text("Loading...")
      ProgressView()
    }
  }

  var errorIndicator: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: presenter.errorMessage
    ).offset(y: 80)
  }

  var emptyCategories: some View {
    CustomEmptyView(
      image: "assetNoFavorite",
      title: "The meal category is empty"
    ).offset(y: 80)
  }

  var content: some View {
    ScrollView(.vertical, showsIndicators: false) {
      ForEach(
        self.presenter.categories,
        id: \.id
      ) { category in
        ZStack {
          self.presenter.linkBuilder(for: category) {
            CategoryRow(category: category)
          }.buttonStyle(PlainButtonStyle())
        }.padding(8)
      }
    }
  }

}


/== TheMealsApp/Module/Meal/Presenter/MealPresenter.swift
//
//  MealPresenter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import Foundation
import Combine

class MealPresenter: ObservableObject {

  private var cancellables: Set<AnyCancellable> = []
  private let mealUseCase: MealUseCase

  @Published var meal: MealModel
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false

  init(mealUseCase: MealUseCase) {
    self.mealUseCase = mealUseCase
    meal = mealUseCase.getMeal()
  }

  func getMeal() {
    isLoading = true
    mealUseCase.getMeal()
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { completion in
          switch completion {
          case .failure (let error):
            self.errorMessage = error.localizedDescription
            self.isError = true
            self.isLoading = false
          case .finished:
            self.isLoading = false
          }
        }, receiveValue: { meal in
          self.meal = meal
        })
        .store(in: &cancellables)
  }

  func updateFavoriteMeal() {
    mealUseCase.updateFavoriteMeal()
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { completion in
          switch completion {
          case .failure:
            self.errorMessage = String(describing: completion)
          case .finished:
            self.isLoading = false
          }
        }, receiveValue: { meal in
          self.meal = meal
        })
        .store(in: &cancellables)
  }

}


/== TheMealsApp/Module/Meal/View/MealView.swift
//
//  MealView.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI
import CachedAsyncImage

struct MealView: View {

  @State private var showingAlert = false
  @ObservedObject var presenter: MealPresenter

  var body: some View {
    ZStack {
      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isError {
        errorIndicator
      } else {
        ScrollView(.vertical) {
          VStack {
            imageMeal
            menuButtonMeal
            content
          }.padding()
        }
      }
    }.onAppear {
      self.presenter.getMeal()
    }.alert(isPresented: $showingAlert) {
      Alert(
        title: Text("Oops!"),
        message: Text("Something wrong!"),
        dismissButton: .default(Text("OK"))
      )
    }.navigationBarTitle(
      Text(presenter.meal.title),
      displayMode: .automatic
    )
  }

}

extension MealView {

  var loadingIndicator: some View {
    VStack {
      Text("Loading...")
      ProgressView()
    }
  }

  var errorIndicator: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: presenter.errorMessage
    ).offset(y: 80)
  }

  var menuButtonMeal: some View {
    HStack(alignment: .center) {
      Spacer()
      CustomIcon(
        imageName: "link.circle",
        title: "Source"
      ).onTapGesture {
        self.openUrl(self.presenter.meal.source)
      }
      Spacer()
      CustomIcon(
        imageName: "video",
        title: "Video"
      ).onTapGesture {
        self.openUrl(self.presenter.meal.youtube)
      }
      Spacer()
      if presenter.meal.favorite {
        CustomIcon(
          imageName: "heart.fill",
          title: "Favorited"
        ).onTapGesture { self.presenter.updateFavoriteMeal() }
      } else {
        CustomIcon(
          imageName: "heart",
          title: "Favorite"
        ).onTapGesture { self.presenter.updateFavoriteMeal() }
      }
      Spacer()
    }.padding()
  }

  var imageMeal: some View {
    CachedAsyncImage(url: URL(string: self.presenter.meal.image)) { image in
      image.resizable()
    } placeholder: {
      ProgressView()
    }.scaledToFill()
      .frame(width: UIScreen.main.bounds.width - 32, height: 250.0, alignment: .center)
      .cornerRadius(30)
  }

  var content: some View {
    VStack(alignment: .leading, spacing: 8) {
      if !presenter.meal.ingredients.isEmpty {
        Text("Ingredient")
          .font(.headline)

        ForEach(self.presenter.meal.ingredients, id: \.id) { ingredient in
          ZStack {
            Text(ingredient.title)
              .font(.system(size: 16))
          }
        }
      }

      Divider()
        .padding(.vertical)

      Text("Instructions")
        .font(.headline)

      Text(self.presenter.meal.instructions)
        .font(.system(size: 16))
    }.padding(.top)
  }

}

extension MealView {

  func openUrl(_ linkUrl: String) {
    if let link = URL(string: linkUrl) {
      UIApplication.shared.open(link)
    } else {
      showingAlert = true
    }
  }

}


/== TheMealsApp/Module/Search/Presenter/SearchPresenter.swift
//
//  SearchPresenter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI
import Combine

class SearchPresenter: ObservableObject {

  private var cancellables: Set<AnyCancellable> = []
  private let router = SearchRouter()
  private let searchUseCase: SearchUseCase

  @Published var meals: [MealModel] = []
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false

  var title = ""

  init(searchUseCase: SearchUseCase) {
    self.searchUseCase = searchUseCase
  }

  func searchMeal() {
    isLoading = true
    searchUseCase.searchMeal(by: title)
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { completion in
        switch completion {
        case .failure(let error):
          self.errorMessage = error.localizedDescription
          self.isError = true
          self.isLoading = false
        case .finished:
          self.isLoading = false
        }
      }, receiveValue: { meals in
        self.meals = meals
      })
      .store(in: &cancellables)
  }

  func linkBuilder<Content: View>(
    for meal: MealModel,
    game: GameModel,
    @ViewBuilder content: () -> Content
  ) -> some View {
      NavigationLink(destination: router.makeMealView(for: meal, game: game)) { content() }
  }

}


/== TheMealsApp/Module/Search/Router/SearchRouter.swift
//
//  SearchRouter.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI

class SearchRouter {

    func makeMealView(for meal: MealModel, game: GameModel) -> some View {
        let mealUseCase = Injection.init().provideMeal(meal: meal, game: game)
        let presenter = MealPresenter(mealUseCase: mealUseCase)
        return MealView(presenter: presenter)
  }

}


/== TheMealsApp/Module/Search/View/SearchRow.swift
//
//  SearchRow.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI
import CachedAsyncImage

struct SearchRow: View {

  var meal: MealModel

  var body: some View {
    VStack {
      HStack(alignment: .top) {
        imageCategory
        content
        Spacer()
      }
      .padding(.horizontal, 16)
      .padding(.vertical, 8)

      Divider()
        .padding(.leading)
    }
  }

}

extension SearchRow {

  var imageCategory: some View {
    CachedAsyncImage(url: URL(string: meal.image)) { image in
      image.resizable()
    } placeholder: {
      ProgressView()
    }.cornerRadius(20).scaledToFit().frame(width: 120)
  }

  var content: some View {
    VStack(alignment: .leading, spacing: 10) {
      Text(meal.title)
        .font(.system(size: 20, weight: .semibold, design: .rounded))
        .lineLimit(3)

      Text(meal.category)
        .font(.system(size: 16))
        .lineLimit(2)

      if !meal.area.isEmpty {
        Text("From \(meal.area)")
          .font(.system(size: 14))
          .lineLimit(2)
      }

    }.padding(
      EdgeInsets(
        top: 0,
        leading: 16,
        bottom: 16,
        trailing: 16
      )
    )
  }

}


/== TheMealsApp/Module/Search/View/SearchView.swift
//
//  SearchView.swift
//  TheMealsApp
//
//  Created by Gilang Ramadhan on 29/11/22.
//

import SwiftUI

struct SearchView: View {

  @ObservedObject var presenter: SearchPresenter

  var body: some View {
    VStack {
      Spacer()
      ZStack {
        if presenter.isLoading {
          loadingIndicator
        } else if presenter.title.isEmpty {
          emptyTitle
        } else if presenter.meals.isEmpty {
          emptyMeals
        } else if presenter.isError {
          errorIndicator
        } else {
          ScrollView(.vertical, showsIndicators: false) {
            ForEach(
              self.presenter.meals,
              id: \.id
            ) { meal in
              ZStack {
                self.presenter.linkBuilder(for: meal) {
                  SearchRow(meal: meal)
                }.buttonStyle(PlainButtonStyle())
              }.padding(8)
            }
          }
        }
      }.searchable(text: $presenter.title)
        .onSubmit(of: .search, presenter.searchMeal)
      Spacer()
    }.navigationBarTitle(
      Text("Search Meals"),
      displayMode: .automatic
    )
  }
}

extension SearchView {

  var loadingIndicator: some View {
    VStack {
      Text("Loading...")
      ProgressView()
    }
  }

  var errorIndicator: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: presenter.errorMessage
    ).offset(y: 80)
  }

  var emptyTitle: some View {
    CustomEmptyView(
      image: "assetSearchMeal",
      title: "Come on, find your favorite food!"
    ).offset(y: 50)
  }
  var emptyMeals: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: "Data not found"
    ).offset(y: 80)
  }

}


/== TheMealsApp/Preview Content/Preview Assets.xcassets/Contents.json
{
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== TheMealsApp.xcodeproj/project.pbxproj
// !$*UTF8*$!
{
	archiveVersion = 1;
	classes = {
	};
	objectVersion = 56;
	objects = {

/* Begin PBXBuildFile section */
		2A91C76E2935DB11005A9155 /* MealEntity.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C76D2935DB11005A9155 /* MealEntity.swift */; };
		2A91C7702935DB1D005A9155 /* IngredientEntity.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C76F2935DB1D005A9155 /* IngredientEntity.swift */; };
		2A91C7722935DB41005A9155 /* MealsResponse.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7712935DB41005A9155 /* MealsResponse.swift */; };
		2A91C7742935DC3E005A9155 /* MealModel.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7732935DC3E005A9155 /* MealModel.swift */; };
		2A91C7762935DC57005A9155 /* IngredientModel.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7752935DC57005A9155 /* IngredientModel.swift */; };
		2A91C7782935DC8A005A9155 /* FavoriteInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7772935DC8A005A9155 /* FavoriteInteractor.swift */; };
		2A91C77A2935DC9F005A9155 /* MealInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7792935DC9F005A9155 /* MealInteractor.swift */; };
		2A91C77C2935DCB6005A9155 /* SearchInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C77B2935DCB6005A9155 /* SearchInteractor.swift */; };
		2A91C77E2935DCFA005A9155 /* MealMapper.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C77D2935DCFA005A9155 /* MealMapper.swift */; };
		2A91C7802935DD18005A9155 /* IngredientMapper.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C77F2935DD18005A9155 /* IngredientMapper.swift */; };
		2A91C7882935DD8B005A9155 /* FavoritePresenter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7872935DD8B005A9155 /* FavoritePresenter.swift */; };
		2A91C78A2935DDA3005A9155 /* FavoriteRouter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7892935DDA3005A9155 /* FavoriteRouter.swift */; };
		2A91C78C2935DDC0005A9155 /* FavoriteView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C78B2935DDC0005A9155 /* FavoriteView.swift */; };
		2A91C78E2935DDDB005A9155 /* FavoriteRow.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C78D2935DDDB005A9155 /* FavoriteRow.swift */; };
		2A91C7912935DE32005A9155 /* CustomEmptyView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7902935DE32005A9155 /* CustomEmptyView.swift */; };
		2A91C7952935DEDB005A9155 /* MealPresenter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7942935DEDB005A9155 /* MealPresenter.swift */; };
		2A91C7972935DEF3005A9155 /* MealView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7962935DEF3005A9155 /* MealView.swift */; };
		2A91C7992935DF79005A9155 /* CustomIcon.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7982935DF79005A9155 /* CustomIcon.swift */; };
		2A91C79B2935E052005A9155 /* MealRow.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C79A2935E052005A9155 /* MealRow.swift */; };
		2A91C79E2935E061005A9155 /* DetailRouter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C79D2935E061005A9155 /* DetailRouter.swift */; };
		2A91C7A42935E251005A9155 /* SearchView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7A32935E251005A9155 /* SearchView.swift */; };
		2A91C7A62935E274005A9155 /* SearchRow.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7A52935E274005A9155 /* SearchRow.swift */; };
		2A91C7A92935E2CC005A9155 /* SearchRouter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7A82935E2CC005A9155 /* SearchRouter.swift */; };
		2A91C7AB2935E2E5005A9155 /* SearchPresenter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7AA2935E2E5005A9155 /* SearchPresenter.swift */; };
		2A91C7AD2935FE79005A9155 /* TabItem.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7AC2935FE79005A9155 /* TabItem.swift */; };
		2A9276B0292CAA7B00C30767 /* Alamofire in Frameworks */ = {isa = PBXBuildFile; productRef = 2A9276AF292CAA7B00C30767 /* Alamofire */; };
		2A9D452F292C805D008FABEB /* TheMealsAppApp.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D452E292C805D008FABEB /* TheMealsAppApp.swift */; };
		2A9D4531292C805D008FABEB /* ContentView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4530292C805D008FABEB /* ContentView.swift */; };
		2A9D4533292C805F008FABEB /* Assets.xcassets in Resources */ = {isa = PBXBuildFile; fileRef = 2A9D4532292C805F008FABEB /* Assets.xcassets */; };
		2A9D4536292C805F008FABEB /* Preview Assets.xcassets in Resources */ = {isa = PBXBuildFile; fileRef = 2A9D4535292C805F008FABEB /* Preview Assets.xcassets */; };
		2A9D4548292C8162008FABEB /* CategoriesResponse.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4547292C8162008FABEB /* CategoriesResponse.swift */; };
		2A9D454A292C817B008FABEB /* RemoteDataSource.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4549292C817B008FABEB /* RemoteDataSource.swift */; };
		2A9D454C292C819A008FABEB /* MealRepository.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D454B292C819A008FABEB /* MealRepository.swift */; };
		2A9D454E292C81B1008FABEB /* Injection.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D454D292C81B1008FABEB /* Injection.swift */; };
		2A9D4551292C81DD008FABEB /* CategoryModel.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4550292C81DD008FABEB /* CategoryModel.swift */; };
		2A9D4554292C8202008FABEB /* HomeInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4553292C8202008FABEB /* HomeInteractor.swift */; };
		2A9D4556292C8225008FABEB /* DetailInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4555292C8225008FABEB /* DetailInteractor.swift */; };
		2A9D455B292C8262008FABEB /* APICall.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D455A292C8262008FABEB /* APICall.swift */; };
		2A9D455D292C8272008FABEB /* CategoryMapper.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D455C292C8272008FABEB /* CategoryMapper.swift */; };
		2A9D455F292C828E008FABEB /* CustomeError+Ext.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D455E292C828E008FABEB /* CustomeError+Ext.swift */; };
		2A9D4561292C82A2008FABEB /* Color+Ext.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4560292C82A2008FABEB /* Color+Ext.swift */; };
		2A9D4566292C840B008FABEB /* HomePresenter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4565292C840B008FABEB /* HomePresenter.swift */; };
		2A9D4568292C84B2008FABEB /* HomeView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4567292C84B2008FABEB /* HomeView.swift */; };
		2A9D456A292C84C2008FABEB /* CategoryRow.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4569292C84C2008FABEB /* CategoryRow.swift */; };
		2A9D456C292C8577008FABEB /* HomeRouter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D456B292C8577008FABEB /* HomeRouter.swift */; };
		2A9D4570292C8604008FABEB /* DetailPresenter.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D456F292C8604008FABEB /* DetailPresenter.swift */; };
		2A9D4572292C8620008FABEB /* DetailView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4571292C8620008FABEB /* DetailView.swift */; };
		2AC96394292CB18A0061B65A /* Realm in Frameworks */ = {isa = PBXBuildFile; productRef = 2AC96393292CB18A0061B65A /* Realm */; };
		2AC96396292CB18A0061B65A /* RealmSwift in Frameworks */ = {isa = PBXBuildFile; productRef = 2AC96395292CB18A0061B65A /* RealmSwift */; };
		2AC96399292CB25E0061B65A /* LocaleDataSource.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2AC96398292CB25E0061B65A /* LocaleDataSource.swift */; };
		2AC9639B292CB2770061B65A /* CategoryEntity.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2AC9639A292CB2770061B65A /* CategoryEntity.swift */; };
		2AC9639F292CB8270061B65A /* CachedAsyncImage in Frameworks */ = {isa = PBXBuildFile; productRef = 2AC9639E292CB8270061B65A /* CachedAsyncImage */; };
		B5FD19312D9D3AF400DC56F7 /* GameEntity.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19302D9D3AF400DC56F7 /* GameEntity.swift */; };
		B5FD19332D9D54DA00DC56F7 /* GamesResponse.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19322D9D54DA00DC56F7 /* GamesResponse.swift */; };
		B5FD193A2D9D554000DC56F7 /* GameModel.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19392D9D554000DC56F7 /* GameModel.swift */; };
		B5FD193B2D9D554000DC56F7 /* GameDetailModel.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19382D9D554000DC56F7 /* GameDetailModel.swift */; };
		B5FD193D2D9D556F00DC56F7 /* GameMapper.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD193C2D9D556F00DC56F7 /* GameMapper.swift */; };
/* End PBXBuildFile section */

/* Begin PBXFileReference section */
		2A91C76D2935DB11005A9155 /* MealEntity.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealEntity.swift; sourceTree = "<group>"; };
		2A91C76F2935DB1D005A9155 /* IngredientEntity.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = IngredientEntity.swift; sourceTree = "<group>"; };
		2A91C7712935DB41005A9155 /* MealsResponse.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealsResponse.swift; sourceTree = "<group>"; };
		2A91C7732935DC3E005A9155 /* MealModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealModel.swift; sourceTree = "<group>"; };
		2A91C7752935DC57005A9155 /* IngredientModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = IngredientModel.swift; sourceTree = "<group>"; };
		2A91C7772935DC8A005A9155 /* FavoriteInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = FavoriteInteractor.swift; sourceTree = "<group>"; };
		2A91C7792935DC9F005A9155 /* MealInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealInteractor.swift; sourceTree = "<group>"; };
		2A91C77B2935DCB6005A9155 /* SearchInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = SearchInteractor.swift; sourceTree = "<group>"; };
		2A91C77D2935DCFA005A9155 /* MealMapper.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealMapper.swift; sourceTree = "<group>"; };
		2A91C77F2935DD18005A9155 /* IngredientMapper.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = IngredientMapper.swift; sourceTree = "<group>"; };
		2A91C7872935DD8B005A9155 /* FavoritePresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = FavoritePresenter.swift; sourceTree = "<group>"; };
		2A91C7892935DDA3005A9155 /* FavoriteRouter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = FavoriteRouter.swift; sourceTree = "<group>"; };
		2A91C78B2935DDC0005A9155 /* FavoriteView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = FavoriteView.swift; sourceTree = "<group>"; };
		2A91C78D2935DDDB005A9155 /* FavoriteRow.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = FavoriteRow.swift; sourceTree = "<group>"; };
		2A91C7902935DE32005A9155 /* CustomEmptyView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CustomEmptyView.swift; sourceTree = "<group>"; };
		2A91C7942935DEDB005A9155 /* MealPresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealPresenter.swift; sourceTree = "<group>"; };
		2A91C7962935DEF3005A9155 /* MealView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealView.swift; sourceTree = "<group>"; };
		2A91C7982935DF79005A9155 /* CustomIcon.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CustomIcon.swift; sourceTree = "<group>"; };
		2A91C79A2935E052005A9155 /* MealRow.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealRow.swift; sourceTree = "<group>"; };
		2A91C79D2935E061005A9155 /* DetailRouter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailRouter.swift; sourceTree = "<group>"; };
		2A91C7A32935E251005A9155 /* SearchView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = SearchView.swift; sourceTree = "<group>"; };
		2A91C7A52935E274005A9155 /* SearchRow.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = SearchRow.swift; sourceTree = "<group>"; };
		2A91C7A82935E2CC005A9155 /* SearchRouter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = SearchRouter.swift; sourceTree = "<group>"; };
		2A91C7AA2935E2E5005A9155 /* SearchPresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = SearchPresenter.swift; sourceTree = "<group>"; };
		2A91C7AC2935FE79005A9155 /* TabItem.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = TabItem.swift; sourceTree = "<group>"; };
		2A9D452B292C805D008FABEB /* TheMealsApp.app */ = {isa = PBXFileReference; explicitFileType = wrapper.application; includeInIndex = 0; path = TheMealsApp.app; sourceTree = BUILT_PRODUCTS_DIR; };
		2A9D452E292C805D008FABEB /* TheMealsAppApp.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = TheMealsAppApp.swift; sourceTree = "<group>"; };
		2A9D4530292C805D008FABEB /* ContentView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = ContentView.swift; sourceTree = "<group>"; };
		2A9D4532292C805F008FABEB /* Assets.xcassets */ = {isa = PBXFileReference; lastKnownFileType = folder.assetcatalog; path = Assets.xcassets; sourceTree = "<group>"; };
		2A9D4535292C805F008FABEB /* Preview Assets.xcassets */ = {isa = PBXFileReference; lastKnownFileType = folder.assetcatalog; path = "Preview Assets.xcassets"; sourceTree = "<group>"; };
		2A9D4547292C8162008FABEB /* CategoriesResponse.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CategoriesResponse.swift; sourceTree = "<group>"; };
		2A9D4549292C817B008FABEB /* RemoteDataSource.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = RemoteDataSource.swift; sourceTree = "<group>"; };
		2A9D454B292C819A008FABEB /* MealRepository.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealRepository.swift; sourceTree = "<group>"; };
		2A9D454D292C81B1008FABEB /* Injection.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = Injection.swift; sourceTree = "<group>"; };
		2A9D4550292C81DD008FABEB /* CategoryModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CategoryModel.swift; sourceTree = "<group>"; };
		2A9D4553292C8202008FABEB /* HomeInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomeInteractor.swift; sourceTree = "<group>"; };
		2A9D4555292C8225008FABEB /* DetailInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailInteractor.swift; sourceTree = "<group>"; };
		2A9D455A292C8262008FABEB /* APICall.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = APICall.swift; sourceTree = "<group>"; };
		2A9D455C292C8272008FABEB /* CategoryMapper.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CategoryMapper.swift; sourceTree = "<group>"; };
		2A9D455E292C828E008FABEB /* CustomeError+Ext.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = "CustomeError+Ext.swift"; sourceTree = "<group>"; };
		2A9D4560292C82A2008FABEB /* Color+Ext.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = "Color+Ext.swift"; sourceTree = "<group>"; };
		2A9D4565292C840B008FABEB /* HomePresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomePresenter.swift; sourceTree = "<group>"; };
		2A9D4567292C84B2008FABEB /* HomeView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomeView.swift; sourceTree = "<group>"; };
		2A9D4569292C84C2008FABEB /* CategoryRow.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CategoryRow.swift; sourceTree = "<group>"; };
		2A9D456B292C8577008FABEB /* HomeRouter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomeRouter.swift; sourceTree = "<group>"; };
		2A9D456F292C8604008FABEB /* DetailPresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailPresenter.swift; sourceTree = "<group>"; };
		2A9D4571292C8620008FABEB /* DetailView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailView.swift; sourceTree = "<group>"; };
		2AC96398292CB25E0061B65A /* LocaleDataSource.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = LocaleDataSource.swift; sourceTree = "<group>"; };
		2AC9639A292CB2770061B65A /* CategoryEntity.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CategoryEntity.swift; sourceTree = "<group>"; };
		B5FD19302D9D3AF400DC56F7 /* GameEntity.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameEntity.swift; sourceTree = "<group>"; };
		B5FD19322D9D54DA00DC56F7 /* GamesResponse.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GamesResponse.swift; sourceTree = "<group>"; };
		B5FD19382D9D554000DC56F7 /* GameDetailModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameDetailModel.swift; sourceTree = "<group>"; };
		B5FD19392D9D554000DC56F7 /* GameModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameModel.swift; sourceTree = "<group>"; };
		B5FD193C2D9D556F00DC56F7 /* GameMapper.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameMapper.swift; sourceTree = "<group>"; };
/* End PBXFileReference section */

/* Begin PBXFrameworksBuildPhase section */
		2A9D4528292C805D008FABEB /* Frameworks */ = {
			isa = PBXFrameworksBuildPhase;
			buildActionMask = 2147483647;
			files = (
				2AC96396292CB18A0061B65A /* RealmSwift in Frameworks */,
				2AC9639F292CB8270061B65A /* CachedAsyncImage in Frameworks */,
				2AC96394292CB18A0061B65A /* Realm in Frameworks */,
				2A9276B0292CAA7B00C30767 /* Alamofire in Frameworks */,
			);
			runOnlyForDeploymentPostprocessing = 0;
		};
/* End PBXFrameworksBuildPhase section */

/* Begin PBXGroup section */
		2A91C7812935DD33005A9155 /* Favorite */ = {
			isa = PBXGroup;
			children = (
				2A91C7842935DD61005A9155 /* Presenter */,
				2A91C7852935DD67005A9155 /* Router */,
				2A91C7862935DD6C005A9155 /* View */,
			);
			path = Favorite;
			sourceTree = "<group>";
		};
		2A91C7822935DD3D005A9155 /* Meal */ = {
			isa = PBXGroup;
			children = (
				2A91C7922935DEB9005A9155 /* Presenter */,
				2A91C7932935DEBF005A9155 /* View */,
			);
			path = Meal;
			sourceTree = "<group>";
		};
		2A91C7832935DD43005A9155 /* Search */ = {
			isa = PBXGroup;
			children = (
				2A91C7A12935E23C005A9155 /* Presenter */,
				2A91C7A72935E2C4005A9155 /* Router */,
				2A91C7A22935E241005A9155 /* View */,
			);
			path = Search;
			sourceTree = "<group>";
		};
		2A91C7842935DD61005A9155 /* Presenter */ = {
			isa = PBXGroup;
			children = (
				2A91C7872935DD8B005A9155 /* FavoritePresenter.swift */,
			);
			path = Presenter;
			sourceTree = "<group>";
		};
		2A91C7852935DD67005A9155 /* Router */ = {
			isa = PBXGroup;
			children = (
				2A91C7892935DDA3005A9155 /* FavoriteRouter.swift */,
			);
			path = Router;
			sourceTree = "<group>";
		};
		2A91C7862935DD6C005A9155 /* View */ = {
			isa = PBXGroup;
			children = (
				2A91C78B2935DDC0005A9155 /* FavoriteView.swift */,
				2A91C78D2935DDDB005A9155 /* FavoriteRow.swift */,
			);
			path = View;
			sourceTree = "<group>";
		};
		2A91C78F2935DE27005A9155 /* View */ = {
			isa = PBXGroup;
			children = (
				2A91C7902935DE32005A9155 /* CustomEmptyView.swift */,
				2A91C7982935DF79005A9155 /* CustomIcon.swift */,
				2A91C7AC2935FE79005A9155 /* TabItem.swift */,
			);
			path = View;
			sourceTree = "<group>";
		};
		2A91C7922935DEB9005A9155 /* Presenter */ = {
			isa = PBXGroup;
			children = (
				2A91C7942935DEDB005A9155 /* MealPresenter.swift */,
			);
			path = Presenter;
			sourceTree = "<group>";
		};
		2A91C7932935DEBF005A9155 /* View */ = {
			isa = PBXGroup;
			children = (
				2A91C7962935DEF3005A9155 /* MealView.swift */,
			);
			path = View;
			sourceTree = "<group>";
		};
		2A91C79C2935E055005A9155 /* Router */ = {
			isa = PBXGroup;
			children = (
				2A91C79D2935E061005A9155 /* DetailRouter.swift */,
			);
			path = Router;
			sourceTree = "<group>";
		};
		2A91C7A12935E23C005A9155 /* Presenter */ = {
			isa = PBXGroup;
			children = (
				2A91C7AA2935E2E5005A9155 /* SearchPresenter.swift */,
			);
			path = Presenter;
			sourceTree = "<group>";
		};
		2A91C7A22935E241005A9155 /* View */ = {
			isa = PBXGroup;
			children = (
				2A91C7A32935E251005A9155 /* SearchView.swift */,
				2A91C7A52935E274005A9155 /* SearchRow.swift */,
			);
			path = View;
			sourceTree = "<group>";
		};
		2A91C7A72935E2C4005A9155 /* Router */ = {
			isa = PBXGroup;
			children = (
				2A91C7A82935E2CC005A9155 /* SearchRouter.swift */,
			);
			path = Router;
			sourceTree = "<group>";
		};
		2A9D4522292C805D008FABEB = {
			isa = PBXGroup;
			children = (
				2A9D452D292C805D008FABEB /* TheMealsApp */,
				2A9D452C292C805D008FABEB /* Products */,
			);
			sourceTree = "<group>";
		};
		2A9D452C292C805D008FABEB /* Products */ = {
			isa = PBXGroup;
			children = (
				2A9D452B292C805D008FABEB /* TheMealsApp.app */,
			);
			name = Products;
			sourceTree = "<group>";
		};
		2A9D452D292C805D008FABEB /* TheMealsApp */ = {
			isa = PBXGroup;
			children = (
				2A9D453C292C80E6008FABEB /* App */,
				2A9D453D292C80EA008FABEB /* Core */,
				2A9D453E292C80F2008FABEB /* Module */,
				2A9D4532292C805F008FABEB /* Assets.xcassets */,
				2A9D4534292C805F008FABEB /* Preview Content */,
			);
			path = TheMealsApp;
			sourceTree = "<group>";
		};
		2A9D4534292C805F008FABEB /* Preview Content */ = {
			isa = PBXGroup;
			children = (
				2A9D4535292C805F008FABEB /* Preview Assets.xcassets */,
			);
			path = "Preview Content";
			sourceTree = "<group>";
		};
		2A9D453C292C80E6008FABEB /* App */ = {
			isa = PBXGroup;
			children = (
				2A9D452E292C805D008FABEB /* TheMealsAppApp.swift */,
				2A9D4530292C805D008FABEB /* ContentView.swift */,
			);
			path = App;
			sourceTree = "<group>";
		};
		2A9D453D292C80EA008FABEB /* Core */ = {
			isa = PBXGroup;
			children = (
				2A9D4541292C8118008FABEB /* Data */,
				2A9D4542292C811C008FABEB /* DI */,
				2A9D4543292C8129008FABEB /* Domain */,
				2A9D4544292C8135008FABEB /* Utils */,
			);
			path = Core;
			sourceTree = "<group>";
		};
		2A9D453E292C80F2008FABEB /* Module */ = {
			isa = PBXGroup;
			children = (
				2A9D4540292C8105008FABEB /* Detail */,
				2A91C7812935DD33005A9155 /* Favorite */,
				2A9D453F292C80FD008FABEB /* Home */,
				2A91C7822935DD3D005A9155 /* Meal */,
				2A91C7832935DD43005A9155 /* Search */,
			);
			path = Module;
			sourceTree = "<group>";
		};
		2A9D453F292C80FD008FABEB /* Home */ = {
			isa = PBXGroup;
			children = (
				2A9D4562292C83EA008FABEB /* Presenter */,
				2A9D4564292C83F8008FABEB /* Router */,
				2A9D4563292C83F2008FABEB /* View */,
			);
			path = Home;
			sourceTree = "<group>";
		};
		2A9D4540292C8105008FABEB /* Detail */ = {
			isa = PBXGroup;
			children = (
				2A9D456E292C85E7008FABEB /* Presenter */,
				2A91C79C2935E055005A9155 /* Router */,
				2A9D456D292C85E2008FABEB /* View */,
			);
			path = Detail;
			sourceTree = "<group>";
		};
		2A9D4541292C8118008FABEB /* Data */ = {
			isa = PBXGroup;
			children = (
				2AC96397292CB24F0061B65A /* Locale */,
				2A9D4545292C8144008FABEB /* Remote */,
				2A9D454B292C819A008FABEB /* MealRepository.swift */,
			);
			path = Data;
			sourceTree = "<group>";
		};
		2A9D4542292C811C008FABEB /* DI */ = {
			isa = PBXGroup;
			children = (
				2A9D454D292C81B1008FABEB /* Injection.swift */,
			);
			path = DI;
			sourceTree = "<group>";
		};
		2A9D4543292C8129008FABEB /* Domain */ = {
			isa = PBXGroup;
			children = (
				2A9D454F292C81C2008FABEB /* Model */,
				2A9D4552292C81E5008FABEB /* UseCase */,
			);
			path = Domain;
			sourceTree = "<group>";
		};
		2A9D4544292C8135008FABEB /* Utils */ = {
			isa = PBXGroup;
			children = (
				2A91C78F2935DE27005A9155 /* View */,
				2A9D4559292C824F008FABEB /* Extensions */,
				2A9D4558292C824C008FABEB /* Mapper */,
				2A9D4557292C8247008FABEB /* Network */,
			);
			path = Utils;
			sourceTree = "<group>";
		};
		2A9D4545292C8144008FABEB /* Remote */ = {
			isa = PBXGroup;
			children = (
				2A9D4546292C814E008FABEB /* Response */,
				2A9D4549292C817B008FABEB /* RemoteDataSource.swift */,
			);
			path = Remote;
			sourceTree = "<group>";
		};
		2A9D4546292C814E008FABEB /* Response */ = {
			isa = PBXGroup;
			children = (
				2A9D4547292C8162008FABEB /* CategoriesResponse.swift */,
				2A91C7712935DB41005A9155 /* MealsResponse.swift */,
				B5FD19322D9D54DA00DC56F7 /* GamesResponse.swift */,
			);
			path = Response;
			sourceTree = "<group>";
		};
		2A9D454F292C81C2008FABEB /* Model */ = {
			isa = PBXGroup;
			children = (
				2A9D4550292C81DD008FABEB /* CategoryModel.swift */,
				2A91C7732935DC3E005A9155 /* MealModel.swift */,
				2A91C7752935DC57005A9155 /* IngredientModel.swift */,
				B5FD19382D9D554000DC56F7 /* GameDetailModel.swift */,
				B5FD19392D9D554000DC56F7 /* GameModel.swift */,
			);
			path = Model;
			sourceTree = "<group>";
		};
		2A9D4552292C81E5008FABEB /* UseCase */ = {
			isa = PBXGroup;
			children = (
				2A9D4553292C8202008FABEB /* HomeInteractor.swift */,
				2A9D4555292C8225008FABEB /* DetailInteractor.swift */,
				2A91C7772935DC8A005A9155 /* FavoriteInteractor.swift */,
				2A91C7792935DC9F005A9155 /* MealInteractor.swift */,
				2A91C77B2935DCB6005A9155 /* SearchInteractor.swift */,
			);
			path = UseCase;
			sourceTree = "<group>";
		};
		2A9D4557292C8247008FABEB /* Network */ = {
			isa = PBXGroup;
			children = (
				2A9D455A292C8262008FABEB /* APICall.swift */,
			);
			path = Network;
			sourceTree = "<group>";
		};
		2A9D4558292C824C008FABEB /* Mapper */ = {
			isa = PBXGroup;
			children = (
				2A9D455C292C8272008FABEB /* CategoryMapper.swift */,
				2A91C77F2935DD18005A9155 /* IngredientMapper.swift */,
				2A91C77D2935DCFA005A9155 /* MealMapper.swift */,
				B5FD193C2D9D556F00DC56F7 /* GameMapper.swift */,
			);
			path = Mapper;
			sourceTree = "<group>";
		};
		2A9D4559292C824F008FABEB /* Extensions */ = {
			isa = PBXGroup;
			children = (
				2A9D455E292C828E008FABEB /* CustomeError+Ext.swift */,
				2A9D4560292C82A2008FABEB /* Color+Ext.swift */,
			);
			path = Extensions;
			sourceTree = "<group>";
		};
		2A9D4562292C83EA008FABEB /* Presenter */ = {
			isa = PBXGroup;
			children = (
				2A9D4565292C840B008FABEB /* HomePresenter.swift */,
			);
			path = Presenter;
			sourceTree = "<group>";
		};
		2A9D4563292C83F2008FABEB /* View */ = {
			isa = PBXGroup;
			children = (
				2A9D4567292C84B2008FABEB /* HomeView.swift */,
				2A9D4569292C84C2008FABEB /* CategoryRow.swift */,
			);
			path = View;
			sourceTree = "<group>";
		};
		2A9D4564292C83F8008FABEB /* Router */ = {
			isa = PBXGroup;
			children = (
				2A9D456B292C8577008FABEB /* HomeRouter.swift */,
			);
			path = Router;
			sourceTree = "<group>";
		};
		2A9D456D292C85E2008FABEB /* View */ = {
			isa = PBXGroup;
			children = (
				2A9D4571292C8620008FABEB /* DetailView.swift */,
				2A91C79A2935E052005A9155 /* MealRow.swift */,
			);
			path = View;
			sourceTree = "<group>";
		};
		2A9D456E292C85E7008FABEB /* Presenter */ = {
			isa = PBXGroup;
			children = (
				2A9D456F292C8604008FABEB /* DetailPresenter.swift */,
			);
			path = Presenter;
			sourceTree = "<group>";
		};
		2AC96397292CB24F0061B65A /* Locale */ = {
			isa = PBXGroup;
			children = (
				2AC9639C292CB27B0061B65A /* Entity */,
				2AC96398292CB25E0061B65A /* LocaleDataSource.swift */,
			);
			path = Locale;
			sourceTree = "<group>";
		};
		2AC9639C292CB27B0061B65A /* Entity */ = {
			isa = PBXGroup;
			children = (
				2AC9639A292CB2770061B65A /* CategoryEntity.swift */,
				2A91C76D2935DB11005A9155 /* MealEntity.swift */,
				2A91C76F2935DB1D005A9155 /* IngredientEntity.swift */,
				B5FD19302D9D3AF400DC56F7 /* GameEntity.swift */,
			);
			path = Entity;
			sourceTree = "<group>";
		};
/* End PBXGroup section */

/* Begin PBXNativeTarget section */
		2A9D452A292C805D008FABEB /* TheMealsApp */ = {
			isa = PBXNativeTarget;
			buildConfigurationList = 2A9D4539292C805F008FABEB /* Build configuration list for PBXNativeTarget "TheMealsApp" */;
			buildPhases = (
				2A9D4527292C805D008FABEB /* Sources */,
				2A9D4528292C805D008FABEB /* Frameworks */,
				2A9D4529292C805D008FABEB /* Resources */,
			);
			buildRules = (
			);
			dependencies = (
			);
			name = TheMealsApp;
			packageProductDependencies = (
				2A9276AF292CAA7B00C30767 /* Alamofire */,
				2AC96393292CB18A0061B65A /* Realm */,
				2AC96395292CB18A0061B65A /* RealmSwift */,
				2AC9639E292CB8270061B65A /* CachedAsyncImage */,
			);
			productName = TheMealsApp;
			productReference = 2A9D452B292C805D008FABEB /* TheMealsApp.app */;
			productType = "com.apple.product-type.application";
		};
/* End PBXNativeTarget section */

/* Begin PBXProject section */
		2A9D4523292C805D008FABEB /* Project object */ = {
			isa = PBXProject;
			attributes = {
				BuildIndependentTargetsInParallel = 1;
				LastSwiftUpdateCheck = 1410;
				LastUpgradeCheck = 1620;
				TargetAttributes = {
					2A9D452A292C805D008FABEB = {
						CreatedOnToolsVersion = 14.1;
					};
				};
			};
			buildConfigurationList = 2A9D4526292C805D008FABEB /* Build configuration list for PBXProject "TheMealsApp" */;
			compatibilityVersion = "Xcode 14.0";
			developmentRegion = en;
			hasScannedForEncodings = 0;
			knownRegions = (
				en,
				Base,
			);
			mainGroup = 2A9D4522292C805D008FABEB;
			packageReferences = (
				2A9276AE292CAA7B00C30767 /* XCRemoteSwiftPackageReference "Alamofire" */,
				2AC96392292CB18A0061B65A /* XCRemoteSwiftPackageReference "realm-swift" */,
				2AC9639D292CB8270061B65A /* XCRemoteSwiftPackageReference "swiftui-cached-async-image" */,
			);
			productRefGroup = 2A9D452C292C805D008FABEB /* Products */;
			projectDirPath = "";
			projectRoot = "";
			targets = (
				2A9D452A292C805D008FABEB /* TheMealsApp */,
			);
		};
/* End PBXProject section */

/* Begin PBXResourcesBuildPhase section */
		2A9D4529292C805D008FABEB /* Resources */ = {
			isa = PBXResourcesBuildPhase;
			buildActionMask = 2147483647;
			files = (
				2A9D4536292C805F008FABEB /* Preview Assets.xcassets in Resources */,
				2A9D4533292C805F008FABEB /* Assets.xcassets in Resources */,
			);
			runOnlyForDeploymentPostprocessing = 0;
		};
/* End PBXResourcesBuildPhase section */

/* Begin PBXSourcesBuildPhase section */
		2A9D4527292C805D008FABEB /* Sources */ = {
			isa = PBXSourcesBuildPhase;
			buildActionMask = 2147483647;
			files = (
				2A91C7762935DC57005A9155 /* IngredientModel.swift in Sources */,
				2A91C77A2935DC9F005A9155 /* MealInteractor.swift in Sources */,
				2A91C7AB2935E2E5005A9155 /* SearchPresenter.swift in Sources */,
				2A91C7802935DD18005A9155 /* IngredientMapper.swift in Sources */,
				2A9D454A292C817B008FABEB /* RemoteDataSource.swift in Sources */,
				2A91C7952935DEDB005A9155 /* MealPresenter.swift in Sources */,
				2A9D4531292C805D008FABEB /* ContentView.swift in Sources */,
				2A9D456A292C84C2008FABEB /* CategoryRow.swift in Sources */,
				2A91C79E2935E061005A9155 /* DetailRouter.swift in Sources */,
				2A91C76E2935DB11005A9155 /* MealEntity.swift in Sources */,
				B5FD19332D9D54DA00DC56F7 /* GamesResponse.swift in Sources */,
				2A91C7782935DC8A005A9155 /* FavoriteInteractor.swift in Sources */,
				2A91C7A42935E251005A9155 /* SearchView.swift in Sources */,
				2A9D455B292C8262008FABEB /* APICall.swift in Sources */,
				2A91C7702935DB1D005A9155 /* IngredientEntity.swift in Sources */,
				2A91C7882935DD8B005A9155 /* FavoritePresenter.swift in Sources */,
				2A9D4568292C84B2008FABEB /* HomeView.swift in Sources */,
				2A9D452F292C805D008FABEB /* TheMealsAppApp.swift in Sources */,
				2A9D4561292C82A2008FABEB /* Color+Ext.swift in Sources */,
				B5FD193A2D9D554000DC56F7 /* GameModel.swift in Sources */,
				B5FD193B2D9D554000DC56F7 /* GameDetailModel.swift in Sources */,
				2A9D4556292C8225008FABEB /* DetailInteractor.swift in Sources */,
				2A9D456C292C8577008FABEB /* HomeRouter.swift in Sources */,
				2A91C77C2935DCB6005A9155 /* SearchInteractor.swift in Sources */,
				2A9D4554292C8202008FABEB /* HomeInteractor.swift in Sources */,
				2A91C78C2935DDC0005A9155 /* FavoriteView.swift in Sources */,
				2A9D4566292C840B008FABEB /* HomePresenter.swift in Sources */,
				B5FD193D2D9D556F00DC56F7 /* GameMapper.swift in Sources */,
				2A9D4570292C8604008FABEB /* DetailPresenter.swift in Sources */,
				2A91C7A92935E2CC005A9155 /* SearchRouter.swift in Sources */,
				B5FD19312D9D3AF400DC56F7 /* GameEntity.swift in Sources */,
				2A9D454E292C81B1008FABEB /* Injection.swift in Sources */,
				2A91C79B2935E052005A9155 /* MealRow.swift in Sources */,
				2A91C7972935DEF3005A9155 /* MealView.swift in Sources */,
				2A91C77E2935DCFA005A9155 /* MealMapper.swift in Sources */,
				2A91C7AD2935FE79005A9155 /* TabItem.swift in Sources */,
				2A91C7742935DC3E005A9155 /* MealModel.swift in Sources */,
				2A9D455F292C828E008FABEB /* CustomeError+Ext.swift in Sources */,
				2A9D4551292C81DD008FABEB /* CategoryModel.swift in Sources */,
				2AC96399292CB25E0061B65A /* LocaleDataSource.swift in Sources */,
				2A91C78A2935DDA3005A9155 /* FavoriteRouter.swift in Sources */,
				2AC9639B292CB2770061B65A /* CategoryEntity.swift in Sources */,
				2A91C7912935DE32005A9155 /* CustomEmptyView.swift in Sources */,
				2A91C78E2935DDDB005A9155 /* FavoriteRow.swift in Sources */,
				2A9D455D292C8272008FABEB /* CategoryMapper.swift in Sources */,
				2A9D454C292C819A008FABEB /* MealRepository.swift in Sources */,
				2A9D4572292C8620008FABEB /* DetailView.swift in Sources */,
				2A91C7A62935E274005A9155 /* SearchRow.swift in Sources */,
				2A91C7992935DF79005A9155 /* CustomIcon.swift in Sources */,
				2A9D4548292C8162008FABEB /* CategoriesResponse.swift in Sources */,
				2A91C7722935DB41005A9155 /* MealsResponse.swift in Sources */,
			);
			runOnlyForDeploymentPostprocessing = 0;
		};
/* End PBXSourcesBuildPhase section */

/* Begin XCBuildConfiguration section */
		2A9D4537292C805F008FABEB /* Debug */ = {
			isa = XCBuildConfiguration;
			buildSettings = {
				ALWAYS_SEARCH_USER_PATHS = NO;
				CLANG_ANALYZER_NONNULL = YES;
				CLANG_ANALYZER_NUMBER_OBJECT_CONVERSION = YES_AGGRESSIVE;
				CLANG_CXX_LANGUAGE_STANDARD = "gnu++20";
				CLANG_ENABLE_MODULES = YES;
				CLANG_ENABLE_OBJC_ARC = YES;
				CLANG_ENABLE_OBJC_WEAK = YES;
				CLANG_WARN_BLOCK_CAPTURE_AUTORELEASING = YES;
				CLANG_WARN_BOOL_CONVERSION = YES;
				CLANG_WARN_COMMA = YES;
				CLANG_WARN_CONSTANT_CONVERSION = YES;
				CLANG_WARN_DEPRECATED_OBJC_IMPLEMENTATIONS = YES;
				CLANG_WARN_DIRECT_OBJC_ISA_USAGE = YES_ERROR;
				CLANG_WARN_DOCUMENTATION_COMMENTS = YES;
				CLANG_WARN_EMPTY_BODY = YES;
				CLANG_WARN_ENUM_CONVERSION = YES;
				CLANG_WARN_INFINITE_RECURSION = YES;
				CLANG_WARN_INT_CONVERSION = YES;
				CLANG_WARN_NON_LITERAL_NULL_CONVERSION = YES;
				CLANG_WARN_OBJC_IMPLICIT_RETAIN_SELF = YES;
				CLANG_WARN_OBJC_LITERAL_CONVERSION = YES;
				CLANG_WARN_OBJC_ROOT_CLASS = YES_ERROR;
				CLANG_WARN_QUOTED_INCLUDE_IN_FRAMEWORK_HEADER = YES;
				CLANG_WARN_RANGE_LOOP_ANALYSIS = YES;
				CLANG_WARN_STRICT_PROTOTYPES = YES;
				CLANG_WARN_SUSPICIOUS_MOVE = YES;
				CLANG_WARN_UNGUARDED_AVAILABILITY = YES_AGGRESSIVE;
				CLANG_WARN_UNREACHABLE_CODE = YES;
				CLANG_WARN__DUPLICATE_METHOD_MATCH = YES;
				COPY_PHASE_STRIP = NO;
				DEBUG_INFORMATION_FORMAT = dwarf;
				ENABLE_STRICT_OBJC_MSGSEND = YES;
				ENABLE_TESTABILITY = YES;
				ENABLE_USER_SCRIPT_SANDBOXING = YES;
				GCC_C_LANGUAGE_STANDARD = gnu11;
				GCC_DYNAMIC_NO_PIC = NO;
				GCC_NO_COMMON_BLOCKS = YES;
				GCC_OPTIMIZATION_LEVEL = 0;
				GCC_PREPROCESSOR_DEFINITIONS = (
					"DEBUG=1",
					"$(inherited)",
				);
				GCC_WARN_64_TO_32_BIT_CONVERSION = YES;
				GCC_WARN_ABOUT_RETURN_TYPE = YES_ERROR;
				GCC_WARN_UNDECLARED_SELECTOR = YES;
				GCC_WARN_UNINITIALIZED_AUTOS = YES_AGGRESSIVE;
				GCC_WARN_UNUSED_FUNCTION = YES;
				GCC_WARN_UNUSED_VARIABLE = YES;
				IPHONEOS_DEPLOYMENT_TARGET = 16.1;
				MTL_ENABLE_DEBUG_INFO = INCLUDE_SOURCE;
				MTL_FAST_MATH = YES;
				ONLY_ACTIVE_ARCH = YES;
				SDKROOT = iphoneos;
				SWIFT_ACTIVE_COMPILATION_CONDITIONS = DEBUG;
				SWIFT_OPTIMIZATION_LEVEL = "-Onone";
			};
			name = Debug;
		};
		2A9D4538292C805F008FABEB /* Release */ = {
			isa = XCBuildConfiguration;
			buildSettings = {
				ALWAYS_SEARCH_USER_PATHS = NO;
				CLANG_ANALYZER_NONNULL = YES;
				CLANG_ANALYZER_NUMBER_OBJECT_CONVERSION = YES_AGGRESSIVE;
				CLANG_CXX_LANGUAGE_STANDARD = "gnu++20";
				CLANG_ENABLE_MODULES = YES;
				CLANG_ENABLE_OBJC_ARC = YES;
				CLANG_ENABLE_OBJC_WEAK = YES;
				CLANG_WARN_BLOCK_CAPTURE_AUTORELEASING = YES;
				CLANG_WARN_BOOL_CONVERSION = YES;
				CLANG_WARN_COMMA = YES;
				CLANG_WARN_CONSTANT_CONVERSION = YES;
				CLANG_WARN_DEPRECATED_OBJC_IMPLEMENTATIONS = YES;
				CLANG_WARN_DIRECT_OBJC_ISA_USAGE = YES_ERROR;
				CLANG_WARN_DOCUMENTATION_COMMENTS = YES;
				CLANG_WARN_EMPTY_BODY = YES;
				CLANG_WARN_ENUM_CONVERSION = YES;
				CLANG_WARN_INFINITE_RECURSION = YES;
				CLANG_WARN_INT_CONVERSION = YES;
				CLANG_WARN_NON_LITERAL_NULL_CONVERSION = YES;
				CLANG_WARN_OBJC_IMPLICIT_RETAIN_SELF = YES;
				CLANG_WARN_OBJC_LITERAL_CONVERSION = YES;
				CLANG_WARN_OBJC_ROOT_CLASS = YES_ERROR;
				CLANG_WARN_QUOTED_INCLUDE_IN_FRAMEWORK_HEADER = YES;
				CLANG_WARN_RANGE_LOOP_ANALYSIS = YES;
				CLANG_WARN_STRICT_PROTOTYPES = YES;
				CLANG_WARN_SUSPICIOUS_MOVE = YES;
				CLANG_WARN_UNGUARDED_AVAILABILITY = YES_AGGRESSIVE;
				CLANG_WARN_UNREACHABLE_CODE = YES;
				CLANG_WARN__DUPLICATE_METHOD_MATCH = YES;
				COPY_PHASE_STRIP = NO;
				DEBUG_INFORMATION_FORMAT = "dwarf-with-dsym";
				ENABLE_NS_ASSERTIONS = NO;
				ENABLE_STRICT_OBJC_MSGSEND = YES;
				ENABLE_USER_SCRIPT_SANDBOXING = YES;
				GCC_C_LANGUAGE_STANDARD = gnu11;
				GCC_NO_COMMON_BLOCKS = YES;
				GCC_WARN_64_TO_32_BIT_CONVERSION = YES;
				GCC_WARN_ABOUT_RETURN_TYPE = YES_ERROR;
				GCC_WARN_UNDECLARED_SELECTOR = YES;
				GCC_WARN_UNINITIALIZED_AUTOS = YES_AGGRESSIVE;
				GCC_WARN_UNUSED_FUNCTION = YES;
				GCC_WARN_UNUSED_VARIABLE = YES;
				IPHONEOS_DEPLOYMENT_TARGET = 16.1;
				MTL_ENABLE_DEBUG_INFO = NO;
				MTL_FAST_MATH = YES;
				SDKROOT = iphoneos;
				SWIFT_COMPILATION_MODE = wholemodule;
				SWIFT_OPTIMIZATION_LEVEL = "-O";
				VALIDATE_PRODUCT = YES;
			};
			name = Release;
		};
		2A9D453A292C805F008FABEB /* Debug */ = {
			isa = XCBuildConfiguration;
			buildSettings = {
				ASSETCATALOG_COMPILER_APPICON_NAME = AppIcon;
				ASSETCATALOG_COMPILER_GLOBAL_ACCENT_COLOR_NAME = AccentColor;
				CODE_SIGN_STYLE = Automatic;
				CURRENT_PROJECT_VERSION = 1;
				DEVELOPMENT_ASSET_PATHS = "\"TheMealsApp/Preview Content\"";
				ENABLE_PREVIEWS = YES;
				GENERATE_INFOPLIST_FILE = YES;
				INFOPLIST_KEY_UIApplicationSceneManifest_Generation = YES;
				INFOPLIST_KEY_UIApplicationSupportsIndirectInputEvents = YES;
				INFOPLIST_KEY_UILaunchScreen_Generation = YES;
				INFOPLIST_KEY_UISupportedInterfaceOrientations_iPad = "UIInterfaceOrientationPortrait UIInterfaceOrientationPortraitUpsideDown UIInterfaceOrientationLandscapeLeft UIInterfaceOrientationLandscapeRight";
				INFOPLIST_KEY_UISupportedInterfaceOrientations_iPhone = "UIInterfaceOrientationPortrait UIInterfaceOrientationLandscapeLeft UIInterfaceOrientationLandscapeRight";
				LD_RUNPATH_SEARCH_PATHS = (
					"$(inherited)",
					"@executable_path/Frameworks",
				);
				MARKETING_VERSION = 1.0;
				PRODUCT_BUNDLE_IDENTIFIER = com.dicoding.academy.TheMealsApp;
				PRODUCT_NAME = "$(TARGET_NAME)";
				SWIFT_EMIT_LOC_STRINGS = YES;
				SWIFT_VERSION = 5.0;
				TARGETED_DEVICE_FAMILY = "1,2";
			};
			name = Debug;
		};
		2A9D453B292C805F008FABEB /* Release */ = {
			isa = XCBuildConfiguration;
			buildSettings = {
				ASSETCATALOG_COMPILER_APPICON_NAME = AppIcon;
				ASSETCATALOG_COMPILER_GLOBAL_ACCENT_COLOR_NAME = AccentColor;
				CODE_SIGN_STYLE = Automatic;
				CURRENT_PROJECT_VERSION = 1;
				DEVELOPMENT_ASSET_PATHS = "\"TheMealsApp/Preview Content\"";
				ENABLE_PREVIEWS = YES;
				GENERATE_INFOPLIST_FILE = YES;
				INFOPLIST_KEY_UIApplicationSceneManifest_Generation = YES;
				INFOPLIST_KEY_UIApplicationSupportsIndirectInputEvents = YES;
				INFOPLIST_KEY_UILaunchScreen_Generation = YES;
				INFOPLIST_KEY_UISupportedInterfaceOrientations_iPad = "UIInterfaceOrientationPortrait UIInterfaceOrientationPortraitUpsideDown UIInterfaceOrientationLandscapeLeft UIInterfaceOrientationLandscapeRight";
				INFOPLIST_KEY_UISupportedInterfaceOrientations_iPhone = "UIInterfaceOrientationPortrait UIInterfaceOrientationLandscapeLeft UIInterfaceOrientationLandscapeRight";
				LD_RUNPATH_SEARCH_PATHS = (
					"$(inherited)",
					"@executable_path/Frameworks",
				);
				MARKETING_VERSION = 1.0;
				PRODUCT_BUNDLE_IDENTIFIER = com.dicoding.academy.TheMealsApp;
				PRODUCT_NAME = "$(TARGET_NAME)";
				SWIFT_EMIT_LOC_STRINGS = YES;
				SWIFT_VERSION = 5.0;
				TARGETED_DEVICE_FAMILY = "1,2";
			};
			name = Release;
		};
/* End XCBuildConfiguration section */

/* Begin XCConfigurationList section */
		2A9D4526292C805D008FABEB /* Build configuration list for PBXProject "TheMealsApp" */ = {
			isa = XCConfigurationList;
			buildConfigurations = (
				2A9D4537292C805F008FABEB /* Debug */,
				2A9D4538292C805F008FABEB /* Release */,
			);
			defaultConfigurationIsVisible = 0;
			defaultConfigurationName = Release;
		};
		2A9D4539292C805F008FABEB /* Build configuration list for PBXNativeTarget "TheMealsApp" */ = {
			isa = XCConfigurationList;
			buildConfigurations = (
				2A9D453A292C805F008FABEB /* Debug */,
				2A9D453B292C805F008FABEB /* Release */,
			);
			defaultConfigurationIsVisible = 0;
			defaultConfigurationName = Release;
		};
/* End XCConfigurationList section */

/* Begin XCRemoteSwiftPackageReference section */
		2A9276AE292CAA7B00C30767 /* XCRemoteSwiftPackageReference "Alamofire" */ = {
			isa = XCRemoteSwiftPackageReference;
			repositoryURL = "https://github.com/Alamofire/Alamofire";
			requirement = {
				kind = upToNextMajorVersion;
				minimumVersion = 5.0.0;
			};
		};
		2AC96392292CB18A0061B65A /* XCRemoteSwiftPackageReference "realm-swift" */ = {
			isa = XCRemoteSwiftPackageReference;
			repositoryURL = "https://github.com/realm/realm-swift";
			requirement = {
				branch = master;
				kind = branch;
			};
		};
		2AC9639D292CB8270061B65A /* XCRemoteSwiftPackageReference "swiftui-cached-async-image" */ = {
			isa = XCRemoteSwiftPackageReference;
			repositoryURL = "https://github.com/lorenzofiamingo/swiftui-cached-async-image";
			requirement = {
				kind = upToNextMajorVersion;
				minimumVersion = 2.0.0;
			};
		};
/* End XCRemoteSwiftPackageReference section */

/* Begin XCSwiftPackageProductDependency section */
		2A9276AF292CAA7B00C30767 /* Alamofire */ = {
			isa = XCSwiftPackageProductDependency;
			package = 2A9276AE292CAA7B00C30767 /* XCRemoteSwiftPackageReference "Alamofire" */;
			productName = Alamofire;
		};
		2AC96393292CB18A0061B65A /* Realm */ = {
			isa = XCSwiftPackageProductDependency;
			package = 2AC96392292CB18A0061B65A /* XCRemoteSwiftPackageReference "realm-swift" */;
			productName = Realm;
		};
		2AC96395292CB18A0061B65A /* RealmSwift */ = {
			isa = XCSwiftPackageProductDependency;
			package = 2AC96392292CB18A0061B65A /* XCRemoteSwiftPackageReference "realm-swift" */;
			productName = RealmSwift;
		};
		2AC9639E292CB8270061B65A /* CachedAsyncImage */ = {
			isa = XCSwiftPackageProductDependency;
			package = 2AC9639D292CB8270061B65A /* XCRemoteSwiftPackageReference "swiftui-cached-async-image" */;
			productName = CachedAsyncImage;
		};
/* End XCSwiftPackageProductDependency section */
	};
	rootObject = 2A9D4523292C805D008FABEB /* Project object */;
}


/== TheMealsApp.xcodeproj/project.xcworkspace/contents.xcworkspacedata
<?xml version="1.0" encoding="UTF-8"?>
<Workspace
   version = "1.0">
   <FileRef
      location = "self:">
   </FileRef>
</Workspace>


/== TheMealsApp.xcodeproj/project.xcworkspace/xcshareddata/IDEWorkspaceChecks.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>IDEDidComputeMac32BitWarning</key>
	<true/>
</dict>
</plist>


/== TheMealsApp.xcodeproj/project.xcworkspace/xcshareddata/swiftpm/Package.resolved
{
  "pins" : [
    {
      "identity" : "alamofire",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/Alamofire/Alamofire",
      "state" : {
        "revision" : "78424be314842833c04bc3bef5b72e85fff99204",
        "version" : "5.6.4"
      }
    },
    {
      "identity" : "realm-core",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/realm/realm-core.git",
      "state" : {
        "revision" : "a4fbfd3eb3d39fd4115ec2ae341e950357e170d2",
        "version" : "12.12.0"
      }
    },
    {
      "identity" : "realm-swift",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/realm/realm-swift",
      "state" : {
        "branch" : "master",
        "revision" : "e0fdb803ef8d62caa97c2e8ca4f3bd09f5550737"
      }
    },
    {
      "identity" : "swiftui-cached-async-image",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/lorenzofiamingo/swiftui-cached-async-image",
      "state" : {
        "revision" : "467a3d17479887943ab917a379e62bbaff60ac8a",
        "version" : "2.1.1"
      }
    }
  ],
  "version" : 2
}


/== TheMealsApp.xcodeproj/project.xcworkspace/xcuserdata/ben.xcuserdatad/UserInterfaceState.xcuserstate
bplist00Ô        
X$versionY$archiverT$topX$objects † _NSKeyedArchiverÑ  	UState€¯]      9 : ; < = > ? @ A B C D E F G T Z [ a e f j k o p t u y z ~  ƒ „ ˆ ‰   ‘ › œ   ¦ ª « ± ² ¶ À Æ Ê Î Ò × Û á â æ ì ï ò.2378<=ABFGKLPQUVZ[_`deijnostxy}~‚ƒ‡ˆŒ¥¦§¨©ª«¬­®¯³º»¼ ½¾ÆÇÈĞÑÒÕÖØâãäåæêíñòóûüı#*+,456>?@JKLMQUVW_`xyz{|}~€‚†•–—¡¢£¤¨¬´µ¶¹ÁÂÚÛÜİŞßàáâãäèïğñùúû"#;<=>?@ABCDEIPQRZ[\fghimqyz{ƒ„œŸ ¡¢£¤¥¦ª±¹º»ÅÆÇÈÌĞØÙÚÛãäüışÿ 
%&'12348<DE]^_`abcdefgkrst|}~ˆ‰Š‹“›œŸ§¨ÀÁÂÃÄÅÆÇÈÉÊÎÕÖŞßàêëìíñõışÿ !"#$%&'()*.56>?@JKLMQU]^_gh€‚ƒ„…†‡ˆ‰Š•–—Ÿ ¡«¬­®²¶¾¿ÀÈÉáâãäåæçèéêëïö÷ø  !)*BCDEFGHIJKLPW_`aklmnrv~‡ˆ ¡¢£¤¥¦§¨©ª®µ½¾¿ÉÊËÌĞÔÜİŞæçÿ 	'()3456:>FG_`abcdefghimtuv~€ˆ‰Š”•–—›Ÿ ¡©ªÂÃÄÅÆÇÈÉÊËÌĞ×ßàáéêëõö÷øü 	!"#$%&'()*+/6>?@HIJTUVW[_gh€‚ƒ„…†‡ˆ‰Š•Ÿ§¨©³´µ¶º¾ÆÇßàáâãäåæçèéíôõışÿ												 	(	)	A	B	C	D	E	F	G	H	I	J	K	O	V	^	_	`	h	i	j	t	u	v	w	{		‡	ˆ	 	¡	¢	£	¤	¥	¦	§	¨	©	ª	®	µ	½	¾	¿	Ç	È	É	Ó	Ô	Õ	Ö	Ú	Ş	æ	ç	ÿ
 








	






'
(
)
3
4
5
6
:
>
?
@
H
I
a
b
c
d
e
f
g
h
i
j
k
o
v
~

€
ˆ
‰
Š
”
•
–
—
›
Ÿ
§
¨
À
Á
Â
Ã
Ä
Å
Æ
Ç
È
É
Ê
Î
Õ
İ
Ş
ß
é
ê
ë
ì
ğ
ô
ü
ı
ş
ÿ !"#$%&'()*.56>?@JKLMQU]^_gh€‚ƒ„…†‡ˆ‰Š•Ÿ©ª«¬°´¼½¾¿ÇÈàáâãäåæçèéêîõöşÿ 
'(.23?@ABCDEIOPQYZ[\]bflmnpsw}„‘—˜œŸ¥¦ª®´µ¸ĞÑÒÓÔÕÖ×ØÙÚÛÜİŞßàáäğñòóôõö÷ı	
*+,-./23GHIJKLMNOPbcdefghiwxyz{|ˆ‰Š‹Œ‘—˜œ¢®¯°±²¶·½¾ÂÃÄÎÏĞÑÒŞßàáâãéêîïğøùúış !"#$%&,-2<=>?@AKLMQabcdefghpqr€œ¦¬­±µ¶º Ã»¿ÀÄÈÉÍÎÒÖ×Üàáâúûüışÿ !",-./37?@AENU^bcghlmqrvz{€˜™š›œŸ ¡¢¦­µ¶·ÁÂÃÄÈÌÔÕİŞßèïøüı123456789:;?FNOPZ[\]aemnvw€‡”•™š¢¦ª«¯°ÈÉÊËÌÍÎÏĞÑÒÖİåæçïğúûüı(,-159=ABFG_`abcdefghimt|}~ˆ‰Š‹“›œ¤¥¦¯¶·¼ÀÁÂÆÇÈÌÍÎÔÕÙßàáêñúşÿ23456789:;<@GOPQ[\]^bfnowxy‚‰’–—›Ÿ£§«¬°±ÉÊËÌÍÎÏĞÑÒÓ×Şæçèòóôõùı )-.237;?CDHIabcdefghijkov~€Š‹Œ‘•¦§¨±¸ÀÄÅÉÊÎÒÖ×ÛÜôõö÷øùúûüış	
()*+/3;<=FMUYZ^_cgklpq‰Š‹Œ‘’“—¦§¨²³´µ¹½ÅÆÎÏĞÑÕÙâéñõùı
"#$%&'()*+,07?@AKLMNRV^_ghlmt|€„ˆŒ‘•¡¢£¤¥±²³´µ»¼ÁËÌÍ×ØìíîïğñòóôõøU$nullÓ      WNS.keysZNS.objectsV$class¢  €€¢  €æ€_IDEWorkspaceDocument_$9229D32F-C4FF-43F2-A95F-816FAA71C582Ó     ) 8®        ! " # $ % & ' (€€€€€	€
€€€€€€€€® * + , - . / 0 1 2 3 - 5 6 0€€*¡¤€‹¦€w§ª¹¤ËÌ€w€:_RecentEditorDocumentURLs_DefaultEditorStatesForURLs\ActiveScheme_ActiveProjectSetIdentifierKey_$RunDestinationArchitectureVisibility_DocumentWindows_EnableThreadGallery_WindowArrangementDebugInfo_RunContextRecents_ActiveRunDestination_ActiveProjectSetNameKey_SelectedWindows_0LastCompletedPersistentSchemeBasedActivityReport_BreakpointsActivatedÒ   H Sª I J K L M N O P Q R€€€€€€€!€#€%€'€)Ó U  V W X YWNS.base[NS.relative€ €€_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/DI/Injection.swiftÒ \ ] ^ _Z$classnameX$classesUNSURL¢ ^ `XNSObjectÓ U  V W X d€ €€_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swiftÓ U  V W X i€ €€_|file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swiftÓ U  V W X n€ €€_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swiftÓ U  V W X s€ €€_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swiftÓ U  V W X x€ €€ _rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swiftÓ U  V W X }€ €€"_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swiftÓ U  V W X ‚€ €€$_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swiftÓ U  V W X ‡€ €€&_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swiftÓ U  V W X Œ€ €€(_2x-xcode-log://DE4259B5-7817-44E7-8091-BC59E4CDE608Ò \ ]  WNSArray¢  `Ó    ’ – 8£ “ ” •€+€,€-£ — ˜ ™€.€AŒ€:_'Xcode.IDEKit.EditorDocument.LogDocument_-Xcode.IDEKit.EditorDocument.PegasusSourceCode_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3ProjectÓ    Ÿ ¢ 8¢   R€/€'¢ £ ¤€1€;€:Ó U  V W X ©€ €€0_2x-xcode-log://55CEB2A9-122B-4C7C-8B4C-1B6CFB5F6402Ó    ¬ ® 8¡ ­€2¡ ¯€3€:_SelectedDocumentLocationsÒ   ³ S¡ ´€4€)Õ  · ¸ ¹ º » W ½ © ¿Ytimestamp_expandTranscript[documentURLYindexPath€9€ €0€5Ó Á Â  Ã Ä Å_NSIndexPathLength_NSIndexPathData€6€8Ò Ç  È ÉWNS.dataB €7Ò \ ] Ë Ì]NSMutableData£ Ë Í `VNSDataÒ \ ] Ï Ğ[NSIndexPath¢ Ñ `[NSIndexPathÒ \ ] Ó Ô_IDELogDocumentLocation£ Õ Ö `_IDELogDocumentLocation_DVTDocumentLocationÒ \ ] Ø Ù_NSMutableDictionary£ Ø Ú `\NSDictionaryÓ    Ü Ş 8¡ İ€<¡ ß€=€:_SelectedDocumentLocationsÒ   ã S¡ ä€>€)Õ  · ¸ ¹ º » W ½ Œ ë€9€ €(€?Ó Á Â  Ã í Å€@€8Ò Ç  ğ ÉB€7Ó    ó 8¯ ô õ ö K ø ù ú û ü ı N L  M P O	
 I J Q€B€D€F€€H€J€L€N€P€R€€€T€V€X€€Z€\€^€#€!€`€b€€€d€f€%¯ !"#$%&'()*+,€h€€®€Ê€ç =\x”±Îè>Yt­Èã8Tp€:Ó U  V W X1€ €€C_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity.swiftÓ U  V W X6€ €€E_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/GameMapper.swiftÓ U  V W X;€ €€G_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swiftÓ U  V W X@€ €€I_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity%202.swiftÓ U  V W XE€ €€K_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameDetailModel.swiftÓ U  V W XJ€ €€M_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel.swiftÓ U  V W XO€ €€O_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swiftÓ U  V W XT€ €€Q_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swiftÓ U  V W XY€ €€S_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GamesResponse.swiftÓ U  V W X^€ €€U_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/Untitled.swiftÓ U  V W Xc€ €€W_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swiftÓ U  V W Xh€ €€Y_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swiftÓ U  V W Xm€ €€[_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swiftÓ U  V W Xr€ €€]_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swiftÓ U  V W Xw€ €€__ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swiftÓ U  V W X|€ €€a_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel%202.swiftÓ U  V W X€ €€c_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swiftÓ U  V W X†€ €€e_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swiftÓ U  V W X‹€ €€g_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swiftÓ   ™ 8ª‘’“”•–—˜€i€j€k€l€m€n€o€p€q€rªš 0œœŸ .œ¢ 0€s€w€x€x€y€Š€‹€x€Œ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ° S¡±€t€)Ò  ´ S¤µ¶µ¶€u€v€u€v€)	Ó   ¿Â ¢ÀÁ€z€{¢ÃÄ€|€‚€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   ÉÌ ¢ÊË€}€~¢ÍÎ€€€€[lineIndexes^documentLengthÒ  Ó S €)jÒ \ ] Ú×¢ Ú `Ó   Ùİ £ÚÛÜ€ƒ€„€…£Şßà€†€‡€‰€_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore#@d      Òç èé\NS.uuidbytesO„äŞÌ<$D!˜
N68„€ˆÒ \ ]ëìVNSUUID¢ë `Òî ïé\NS.uuidbytesOØÌàoßE~ gßmĞ€ˆ#         Ó   ô÷ ¢õö€€¢ÍÎ€€€€Ufolds^documentLengthÓ   ş	 8ªÿ €€‘€’€“€”€•€–€—€˜€™ª
 0œœœ 0€š€w€x€x€€ª€«€x€¬€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ    S¡!€›€)Ò  $ S¤%&%&€œ€€œ€€)ƒÓ   -0 ¢./€Ÿ€ ¢12€¡€¤€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   7: ¢8Ê€¢€}¢;Í€£€€^documentLengthSÓ   AE £BCD€¥€¦€§£FŞH€¨€†€©€_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStoreÒN Oé\NS.uuidbytesO³&~ªïJŒgßlSÍ„?€ˆÒR Sé\NS.uuidbytesO®ØA‚¢L ±Î!58ú€ˆ#@      }Ó   X[ ¢õZ€€­¢Í;€€£€^documentLengthÓ   al 8ªbcdefghijk€¯€°€±€²€³€´€µ€¶€·€¸ªm 0œœqŸ .œu 0€¹€w€x€x€»€Š€‹€x€È€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ƒ S¡„€º€)Ò  ‡ S¤ . . . .€‹€‹€‹€‹€)Ó   ‘ ¢€¼€½¢’“€¾€Ä€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ˜œ £™š›€¿€À€Á£Ş€Â€Ã€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ¥ ¦é\NS.uuidbytesO·bæ›W=D=¿‹‡øİhƒ€ˆÒ© ªé\NS.uuidbytesO‘v¯E¤¶ìnÌN€ˆÓ   ­° ¢®Ê€Å€}¢±²€Æ€Ç€^documentLengthÒ  · S €)Ó   º½ ¢õ¼€€É¢²±€Ç€Æ€^documentLengthÓ   ÃÎ 8ªÄÅÆÇÈÉÊËÌÍ€Ë€Ì€Í€Î€Ï€Ğ€Ñ€Ò€Ó€ÔªÏ 0œœÓŸ .œ× 0€Õ€w€x€x€Ù€Š€‹€x€å€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  å S¡æ€Ö€)Ò  é S¤êëêë€×€Ø€×€Ø€)0KÓ   òõ ¢óô€Ú€Û¢ö÷€Ü€â€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ü  £ışÿ€İ€Ş€ß£Ş€†€à€á€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒ	 
é\NS.uuidbytesO$‘çCû…îLğAñ`ë€ˆÒ é\NS.uuidbytesOİ;Øz"C0œ ·.5o€ˆÓ    ¢Ê€}€ã¢Í€€ä€^documentLength+Ó    ¢õ€€æ¢Í€€ä€^documentLengthÓ   $/ 8ª%&'()*+,-.€è€é€ê€ë€ì€í€î€ï€ğ€ñª0 0œœ4Ÿ .œ8 0€ò€w€x€x€ö€Š€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  F S¡G€ó€)Ò  J S¤KLKL€ô€õ€ô€õ€)
Ó   SV ¢TU€÷€ø¢WX€ù€ÿ€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ]a £^_`€ú€û€ü£Şcd€†€ı€ş€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒj ké\NS.uuidbytesOèU^q3[Bºˆ	.iiÊ×€ˆÒn oé\NS.uuidbytesO£ƒÔbˆOp¯áóÙüa²D€ˆÓ   ru ¢sÊ €}¢vÍ€€^documentLength$Ó   | ¢õ~€¢Ív€€^documentLengthÓ   … 8ª†‡ˆ‰Š‹Œ	
ª‘ 0œœ•– .œ™ 0€w€x€x€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  § S¡¨€)Ò  « S¤ . . . .€‹€‹€‹€‹€)Ó   ²µ ¢³´¢¶·€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ¼À £½¾¿£ÁŞÃ€†€_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayControllerÒÉ Êé\NS.uuidbytesOV´	MDşw ÷)^Z9€ˆÒÍ Îé\NS.uuidbytesOáA½^$lC´€ŒIã|aİ€ˆÓ   ÑÔ ¢ÒÊ€}¢ÕÍ€€^documentLengthò#¿ğ      Ó   Üß ¢õŞ€¢ÍÕ€€^documentLengthÓ   åğ 8ªæçèéêëìíîï!"#$%&'()*ªñ 0œœõ– .œù 0+€w€x€x/€‹€x;€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   S¡,€)Ò   S¤-.-.€)Ó    ¢01¢25€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   ! ¢Ê €}3¢Í#€4€^documentLengthÓ   (, £)*+678£-.Ş9:€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ5 6é\NS.uuidbytesOHïÑßB2²5{n61p€ˆÒ9 :é\NS.uuidbytesO¶vštK³‰ço;h€ˆÓ   =@ ¢õ?€<¢Í#€4€^documentLengthÓ   FQ 8ªGHIJKLMNOP>?@ABCDEFGªR 0œœVWXœZ 0H€w€x€xLXY€xZ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  h S¡iI€)Ò  l S¤mnmnJKJK€)=Ó   ux ¢vwMN¢yzOU€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ƒ £€‚PQR£„…ŞST€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒŒ é\NS.uuidbytesOò ±ŠøIî“dl«À—¶€ˆÒ ‘é\NS.uuidbytesO"êøZiI…ˆ”q-l(×€ˆÓ   ”— ¢•ÊV€}¢˜ÍW€€^documentLengthí#@       8Ó    £ ¢õ¢€[¢Í˜€W€^documentLengthÓ   ©´ 8ªª«¬­®¯°±²³]^_`abcdefªµ 0œœ¹Ÿ .œ½ 0g€w€x€xj€Š€‹€xv€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ë S¡Ìh€)Ò  Ï S¤ĞëĞëi€Øi€Ø€)Ó   ×Ú ¢ØÙkl¢ÛÜms€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   áå £âãänop£Şçè€†qr€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒî ïé\NS.uuidbytesO?sVêINÍj”G=ã`€ˆÒò óé\NS.uuidbytesO6†ÜÌ'N'¯¥ìe¡ÃÖ€ˆÓ   öù ¢÷Êt€}¢úÍu€€^documentLengthFÓ     ¢õ€w¢Íú€u€^documentLengthÓ   	 8ª
yz{|}~€‚ª 0œœ– .œ 0ƒ€w€x€x†€‹€x’€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  + S¡,„€)Ò  / S¤0000…………€)Ó   7: ¢89‡ˆ¢;<‰€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   AE £BCDŠ‹Œ£FŞH€†€_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayControllerÒN Oé\NS.uuidbytesOƒo–¸K‹œ·‚ÍŠÔX€ˆÒR Sé\NS.uuidbytesO3·|<ÛÚB‚µ„×ºªïkä€ˆÓ   VY ¢ÊX€}¢Í[€‘€^documentLengthÓ   `c ¢õb€“¢Í[€‘€^documentLengthÓ   it 8ªjklmnopqrs•–—˜™š›œªu 0œœy– .œ} 0Ÿ€w€x€x£€‹€x¯€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ‹ S¡Œ €)Ò   S¤‘‘¡¢¡¢€)CÓ   ˜› ¢™š¤¥¢œ¦¬€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ¢¦ ££¤¥§¨©£§Ş©ª€†«€_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStoreÒ¯ °é\NS.uuidbytesOY”×ËpC†„ŞÇgXä9©€ˆÒ³ ´é\NS.uuidbytesOa;¼Æ ²Hİ‚‘=ãŸœß€ˆÓ   ·º ¢Ê¹€}­¢Í¼€®€^documentLengthÓ   ÁÄ ¢õÃ€°¢Í¼€®€^documentLengthÓ   ÊÕ 8ªËÌÍÎÏĞÑÒÓÔ²³´µ¶·¸¹º»ªÖ 0œœÚœŞ 0¼€w€x€xÀ€ª¡€xÌ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ì S¡í½€)Ò  ğ S¤ñòñò¾¿¾¿€)/Ó   ùü ¢úûÁÂ¢ışÃÉ€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ    £ÄÅÆ£Ş	
€†ÇÈ€_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStoreÒ é\NS.uuidbytesOwÙ0•"}F~™êf5ít€ˆÒ é\NS.uuidbytesO“ºKÿÎvJ8£æâ‘/½6€ˆÓ    ¢ÊÊ€}¢ÍË€€^documentLength1Ó   "% ¢õ$€Í¢Í€Ë€^documentLengthÓ   +6 8ª,-./012345ÏĞÑÒÓÔÕÖ×Øª7 0œœ;– .œ? 0Ù€w€x€xÛ€‹€xæ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  M S¡NÚ€)Ò  Q S¤ . . . .€‹€‹€‹€‹€)Ó   X[ ¢YZÜİ¢\]Şä€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   bf £cdeßàá£ghŞâã€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒo pé\NS.uuidbytesO*ExVåE …¨>û D€ˆÒs té\NS.uuidbytesOo{Q'¹ÄOóŸ4Z V9°€ˆÓ   wz ¢xÊå€}¢¶²€v€Ç€^documentLengthÓ   €ƒ ¢õ‚€ç¢²¶€Ç€v€^documentLengthÓ   ‰” 8ªŠ‹Œ‘’“éêëìíîïğñòª• 0œœ™Ÿ .œ 0ó€w€x€xõ€Š€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  « S¡¬ô€)Ò  ¯ S¤ . . . .€‹€‹€‹€‹€)Ó   ¶¹ ¢·¸ö÷¢º»øş€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ÀÄ £ÁÂÃùúû£ŞÆÇ€†üı€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒÍ Îé\NS.uuidbytesO!víÛ<Iº–ñxİŞ4U€ˆÒÑ Òé\NS.uuidbytesO¥¤8‰ÎI¾àc¼:Ö4€ˆÓ   ÕØ ¢Ê×€}ÿ¢ÍÚ€ €^documentLengthUÓ   ßâ ¢õá€¢ÍÚ€ €^documentLengthÓ   èó 8ªéêëìíîïğñò	
ªô 0œœø– .œü 0€w€x€x€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
 S¡€)Ò   S¤KK€ô€ô€) Ó    ¢¢€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ    # ¢!Ê€}¢$Í€€^documentLengthÓ   *. £+,-£Ş01€†€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒ7 8é\NS.uuidbytesOœpS<¦K¬©L¥wh­€ˆÒ; <é\NS.uuidbytesOÅåB«:YGı§ï ŸàÇ€ˆÓ   ?B ¢õA€¢Í$€€^documentLengthÓ   HS 8ªIJKLMNOPQR !"#$%&'()ªT 0œœXYZœ\ 0*€w€x€x.:;€x<€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  j S¡k+€)Ò  n S¤opop,-,-€)4Ó   wz ¢xy/0¢{|14€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   „ ¢Êƒ€}2¢Í†€3€^documentLengthfÓ   ‹ £Œ567£‘Ş89€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ˜ ™é\NS.uuidbytesOQÙ°ÊT@U¾_8(ÌÙ`€ˆÒœ é\NS.uuidbytesO¥Æ;÷@8—ü.íG>ò€ˆ#@*      Ó   ¢¥ ¢õ¤€=¢Í†€3€^documentLengthÓ   «¶ 8ª¬­®¯°±²³´µ?@ABCDEFGHª· 0œœ»– .œ¿ 0I€w€x€xK€‹€xW€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Í S¡ÎJ€)Ò  Ñ S¤ . . . .€‹€‹€‹€‹€)Ó   ØÛ ¢ÙÚLM¢ÜİNQ€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   âå ¢ãÊO€}¢æÍP€€^documentLength„Ó   ìğ £íîïRST£ñòŞUV€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒù úé\NS.uuidbytesO|RõqïÖBî ³Vøcî€ˆÒı şé\NS.uuidbytesOÏİé5Ö#B$˜n¿Oò¾Ú€ˆÓ    ¢õ€X¢Íæ€P€^documentLengthÓ   
 8ªZ[\]^_`abcª 0œœŸ .œ 0d€w€x€xf€Š€‹€xr€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  , S¡-e€)Ò  0 S¤ . . . .€‹€‹€‹€‹€)Ó   7: ¢89gh¢;<il€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   AD ¢ÊC€}j¢²F€Çk€^documentLength Ó   KO £LMNmno£PQŞpq€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒX Yé\NS.uuidbytesO{Ö;¡îF±¹çÊ]É¾€ˆÒ\ ]é\NS.uuidbytesOè2•'"^D‰71—ÆË%€ˆÓ   `c ¢õb€s¢²F€Çk€^documentLengthÓ   it 8ªjklmnopqrsuvwxyz{|}~ªu 0œœyŸ .œ} 0€w€x€x€Š€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ‹ S¡Œ€€)Ò   S¤ . .€‹€‹€)Ó   –™ ¢—˜‚ƒ¢š›„‡€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ    £ ¢Ê¢€}…¢Í¥€†€^documentLengthÓ   ª® £«¬­ˆ‰Š£Ş°±€†‹Œ€_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStoreÒ· ¸é\NS.uuidbytesOÅR9³ı¡C0 2b?ï€ˆÒ» ¼é\NS.uuidbytesOº‰—5J±½(“Ó}Q7€ˆÓ   ¿Â ¢õÁ€¢Í¥€†€^documentLengthÓ   ÈÓ 8ªÉÊËÌÍÎÏĞÑÒ‘’“”•–—˜™ªÔ 0œœØÙÚœÜ 0š€w€x€x©ª€x«€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ê S¡ë›€)Ò  î S¤ï .ï .œ€‹œ€‹€)&Ó   öù ¢÷øŸ¢úû £€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   	 	 ¢Ê	€}¡¢Í	€¢€^documentLength{Ó   	
	 £			¤¥¦£	Ş	§€†¨€_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayControllerÒ	 	é\NS.uuidbytesOé&‡ı©ÛD¨µ&Bu×æ€ˆÒ	 	é\NS.uuidbytesO>ä”™KüMğ²åÛíñ€³€ˆ#@,      Ó   	!	$ ¢õ	#€¬¢Í	€¢€^documentLengthÓ   	*	5 8ª	+	,	-	.	/	0	1	2	3	4®¯°±²³´µ¶·ª	6 0œœ	:– .œ	> 0¸€w€x€xº€‹€xÆ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  	L S¡	M¹€)Ò  	P S¤Z¶Z¶;€v;€v€)Ó   	W	Z ¢	X	Y»¼¢	[	\½À€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   	a	d ¢Ê	c€}¾¢Í	f€¿€^documentLength’Ó   	k	o £	l	m	nÁÂÃ£Ş	q	r€†ÄÅ€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒ	x 	yé\NS.uuidbytesOQ´Ğ$nO““C7Œé«€ˆÒ	| 	}é\NS.uuidbytesOö='£%SAÛ»Ú<Ñçİ€ˆÓ   	€	ƒ ¢õ	‚€Ç¢Í	f€¿€^documentLengthÓ   	‰	” 8ª	Š	‹	Œ					‘	’	“ÉÊËÌÍÎÏĞÑÒª	• 0œœ	™– .œ	 0Ó€w€x€xÕ€‹€xá€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  	« S¡	¬Ô€)Ò  	¯ S¤ . . . .€‹€‹€‹€‹€)Ó   	¶	¹ ¢	·	¸Ö×¢	º	»ØÛ€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   	À	Ã ¢Ê	Â€}Ù¢Í	Å€Ú€^documentLength±Ó   	Ê	Î £	Ë	Ì	ÍÜİŞ£	ÏŞ	Ñß€†à€_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStoreÒ	× 	Øé\NS.uuidbytesOœÆ#ï?Jƒ¢óøè‚Ñğ,€ˆÒ	Û 	Üé\NS.uuidbytesO&ş,•”‹KÌ¡ÓşMT¡Eï€ˆÓ   	ß	â ¢õ	á€â¢Í	Å€Ú€^documentLengthÓ   	è	ó 8ª	é	ê	ë	ì	í	î	ï	ğ	ñ	òäåæçèéêëìíª	ô 0œœ	ø	ù	úœ	ü 0î€w€x€xñış€xÿ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  

 S¡
ï€)Ò  
 S¤
µ
µğ€uğ€u€)dÓ   

 ¢

òó¢

ô÷€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   
 
# ¢
!Êõ€}¢
$Íö€€^documentLength3îÓ   
*
. £
+
,
-øùú£
/Ş
1û€†ü€_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStoreÒ
7 
8é\NS.uuidbytesO
ÜŠ3N)HG¾súY~€ˆÒ
; 
<é\NS.uuidbytesO@¡ŞÌÔF6–ÃWK9ô^ €ˆ#@.      AÓ   
A
D ¢õ
C€ ¢Í
$€ö€^documentLengthÓ   
J
U 8ª
K
L
M
N
O
P
Q
R
S
T	
ª
V 0œœ
ZŸ .œ
^ 0€w€x€x€Š€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
l S¡
m€)Ò  
p S¤nZnZK;K;€)Ó   
w
z ¢
x
y¢
{
|€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   

„ ¢Ê
ƒ€}¢Í
†€€^documentLengthÓ   
‹
 £
Œ

£

‘Ş€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ
˜ 
™é\NS.uuidbytesOn
WIEDŸŸ^ôé4€ˆÒ
œ 
é\NS.uuidbytesO	rhûííK§³ÑËıIÊØ€ˆÓ   
 
£ ¢õ
¢€¢Í
†€€^documentLengthÓ   
©
´ 8ª
ª
«
¬
­
®
¯
°
±
²
³ !"#$%&ª
µ 0œœ
¹
»œ
½ 0'€w€x€x)€ª5€x6€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
Ë S¡
Ì(€)Ò  
Ï S¤‘X‘X¢Y¢Y€)Ó   
Ö
Ù ¢
×
Ø*+¢
Ú
Û,2€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   
à
ä £
á
â
ã-./£
å
æŞ01€†€_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.widthÒ
í 
îé\NS.uuidbytesOc%¾»IWµ[P­ {€ˆÒ
ñ 
òé\NS.uuidbytesOlZ»T+Ly£Dì]“ü9€ˆÓ   
õ
ø ¢Ê
÷€}3¢Í
ú€4€^documentLengthŠ(Ó     ¢õ€7¢Í
ú€4€^documentLengthÓ   	 8ª
9:;<=>?@ABª 0œœŸ .œ 0C€w€x€xF€Š€‹€xR€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  + S¡,D€)Ò  / S¤
»1
»15E5E€)@Ó   7: ¢89GH¢;<IO€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   AE £BCDJKL£ŞGH€†MN€_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayControllerÒN Oé\NS.uuidbytesOö;vfSjDï¹ÆğÑ÷êÕü€ˆÒR Sé\NS.uuidbytesO<H;	¢öBÁŠÙq.©?€ˆÓ   VY ¢WÊP€}¢ZÍQ€€^documentLength—Ó   `c ¢õb€S¢ÍZ€Q€^documentLengthÓ   it 8ªjklmnopqrsUVWXYZ[\]^ªu 0œœyÙ{œ} 0_€w€x€xa©m€xn€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ‹ S¡Œ`€)Ò   S¤ . . . .€‹€‹€‹€‹€)Ó   –™ ¢—˜bc¢š›dj€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ    ¤ £¡¢£efg£¥¦Şhi€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ­ ®é\NS.uuidbytesOßIŸ0>éKÜ­(ğ›d¹sk€ˆÒ± ²é\NS.uuidbytesOÜ~·BO›ITÒ\W‚€ˆÓ   µ¸ ¢¶Êk€}¢¹²l€Ç€^documentLength€”Ó   ÀÃ ¢õÂ€o¢²¹€Çl€^documentLengthÓ   ÉÔ 8ªÊËÌÍÎÏĞÑÒÓqrstuvwxyzªÕ 0œœÙ– .œİ 0{€w€x€x~€‹€xŠ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ë S¡ì|€)Ò  ï S¤&ñ&ñ€}€}€)Ó   ÷ú ¢øù€¢ûü‡€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ    £‚ƒ„£Ş…€††€_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayControllerÒ é\NS.uuidbytesOmÚ3ßÒAB¹Ùså®öÇ€ˆÒ é\NS.uuidbytesO-ØG«÷ğJ®[0r¡=İ€ˆÓ    ¢Êˆ€}¢Í‰€€^documentLength–Ó    # ¢õ"€‹¢Í€‰€^documentLengthÓ   )+ 8¡*¡,€:Ó U  V W X1€ €_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeprojÓ   49 8¤5678‘’“¤:;<=”•– €:_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_&Xcode3ProjectEditor_Xcode3TargetEditor_Xcode3ProjectInfoEditor_Xcode3TargetEditorÒ  F S¡G—€)ÔJ · ¹ KLMNYselectionš™˜Ÿ_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj#AÆÎªº¤©Ó   RU 8¢ST›œ¢VW€:VEditorVTarget_Xcode3TargetEditor[TheMealsAppÒ \ ]^__Xcode3ProjectDocumentLocation£`a `_Xcode3ProjectDocumentLocation_DVTDocumentLocationÓ   cd 8  €:Ó   gi ¡h¢¡j£€]IDENameString[TheMealsAppÑ o¥Ò \ ]qrVNSNull¢q `Ò  t S¡ €€)Ó   xz 8¡ €¡{¨€:Ò  ~€¡ €©Ò \ ]‚ƒ^NSMutableArray£‚  `Ó   …‰ £†‡ˆ«¬­£Š‹Œ®²¶€_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKeyÓ   ’” 8¡“¯¡•°€:[TheMealsAppÒ™ š›WNS.time#AÆÎ¹›üÒ±Ò \ ]VNSDate¢ `Ó    ¢ 8¡¡³¡£´€:[TheMealsAppÒ §¨©YNS.stringµ_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64Ò \ ]«¬_NSMutableString£«­ `XNSStringÓ   ¯± 8¡°·¡²¸€:_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64Ò™ ¶›#AÆÎº!»¶±Ó   ¹Ä ªº»¼½¾¿ÀÁÂÃº»¼½¾¿ÀÁÂÃª 0Æ 0œÉÊËÌÍÎ€wÄ€w€xÅÆÇÈÉÊ€ZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:7A60B588-4926-4A88-9679-4FA36F2ECDD0Vx86_64_iphonesimulator18.2ViPhoneZiPhone17,1Ò  â€ ©Ó   åê 8¤æçèéÍÎÏĞ¤ëìíîÑÒÓÔ€:_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildX16C5032aêÒ  ø€£ùúûÕÚŞ©Ó   ş 8£ÿ Ö×Ø£WëXÙÑ€:_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ Ó    8£ÿ Ö×Ø£ÛÜİ€:#?ğ      c  %  Ò Ç  ÉObplist00Ô
X$versionY$archiverT$topX$objects † _NSKeyedArchiverÑ	Troot€¯)*0:;<#=AIJKLMSWX\_U$nullÓXNSStringV$class\NSAttributes€€€VFailedÓWNS.keysZNS.objects¡€¡€€VNSFontÖ !"#$%&'(VNSSizeXNSfFlags\NSDescriptorZNSHasWidthVNSName#@&      ˆ€€€_.AppleSystemUIFontBoldÓ+,-./_NSFontDescriptorOptions_NSFontDescriptorAttributes€€„€Ó15£234€	€
€£678€€€€_NSFontSizeAttribute_ NSCTFontFeatureSettingsAttribute_NSCTFontUIUsageAttributeÒ>@¡?€€ÓBE¢CD€€¢FG€€€_CTFeatureSelectorIdentifier_CTFeatureTypeIdentifier ÒNOPQZ$classnameX$classes\NSDictionary¢PRXNSObjectÒNOTU^NSMutableArray£TVRWNSArray_CTFontBoldUsageÒNOYZ_NSFontDescriptor¢[R_NSFontDescriptorÒNO]^VNSFont¢]RÒNO`a_NSAttributedString¢bR_NSAttributedString    $ ) 2 7 I L Q S o u | … Œ ™ ›  Ÿ ¦ ­ µ À Â Ä Æ È Ê Ñ Ş å î û 9@Zwy~€‡‹‘•—™›³Öñöøúü
1KMOT_hux†•™¡³¸ËÎáæíğõ
             c              "€7Ó   " 8¦ÿ !ÖßàØáâ¦#¶%&¶¶ã€väå€v€v€:_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle#@      Ò™ 0›#AÆÎâz‹$±o T o d a y   a t   2 . 1 8 / A MÓ   4= 8¨56789:;<çèéêëìíî¨ 0? 0œœ< 0E€wï€w€x€xî€wğ€:_IDEWindowIsFullScreen^IDEWindowFrame_-IDEHasMigratedValuesFromNSRestorableStateData_IDEWindowTabBarIsVisible_&IDEWindowTabBarWasVisibleWithSingleTab_IDEActiveWorkspaceTabController_IDEWindowToolbarIsVisible_>IDEWorkspaceTabController_75DACF4F-9477-44F2-97BD-12677AFE5FB7_209 109 1400 900 0 0 1680 1025 Ó   QY 8§RSTUVWXñòóôõö÷§ 0[\]^œ`€wø%&*€x+€:_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth_IDEShowUtilities]IDEEditorAreaÓ   jp 8¥klmnoùúûüı¥qrstuş
€:_ Xcode.IDEKit.Navigator.Workspace_"Xcode.IDEKit.Navigator.Test.Modern_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigatorÓ   }‚ 8¤~€ÿ ¤ƒ„…ƒ€:_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKeyÓ      €_IDENavigatorModeSolitaryÓ   ’” ¡“¡•€_IDENavigatorModeSolitaryÒ ™š›_codablePlistRepresentation	OÙbplist00Ô]a_lastAccessedDateYitemState]selectedItems^scrollPosition3AÆÎµˆÅßô¯ "$&(+-1368<>@BFHJLNPSUWY[Ò	
TpathYindexHint¥[TheMealsAppTCoreVDomainUModel Ò^expansionState3AÆÎ´œ)Q/Ò	¥TDataVLocaleÒ3AÆÎ´œ)	lÒ	¤Ò3AÆÎ´œ)AtÒ	!¥ WUseCaseÒ#3AÆÎ´œ)`kÒ	%¢Ò'3AÆÎ´œ(ÛÒ	)¦*VEntityÒ,3AÆÎ´œ)?Ò	.¥/0UUtilsVMapperÒ23AÆÎ´œ)‚Ò	4£5SAppÒ7!3AÆÎ´œIÜHÒ	9¦:;VRemoteXResponseÒ=3AÆÎ´œ)7ÊÒ	?¤ÒA3AÆÎ´œ(øŒÒ	CE¥/DWNetworkÒG3AÆÎ´œ)†MÒ	I!¥:ÒK3AÆÎ´œ)'_Ò	M!£ÒO3AÆÎ´œ(çXÒ	Q!¡R_Package DependenciesÒT3AÆÎ´œ)—‰Ò	VE¤/ÒX3AÆÎ´œ)müÒ	Z¡Ò\3AÆÎ´œ(Ç¡^Ò	_§*`_CategoryEntity.swift¢be¢cd#        #À$      ¢fg#@q      #@Ø        $ . < K T w |  ‹ ‘  ¢ © ¯ ± ¶ Å Î Ó Ù Ş å ê ó ø ı ÿ "'058=FKRY^glrx„’–šŸ¨­´»ÄÉÒ×Üáêïõıÿ&+/4=BD[`insx†ˆ–˜¥¼¿ÂËÔ×à             h              éÒ \ ]Ÿ_&ExplorableOutlineViewArchivableUIState£ ¡ `_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier_Ó   £¨ 8¤¤¥¦§¤©ª«©€:_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKeyÓ   ³´   €_IDENavigatorModeSolitaryÓ   ¸º ¡¹¡»€_IDENavigatorModeSolitaryÒ ¿šÁ_codablePlistRepresentation	O²bplist00Ô_lastAccessedDateYitemState]selectedItems^scrollPosition3AÆÎ¬Õ…¾  ¢	¢
#        #À$      ¢#@q      #@Œø     $.<KTUVY\enqz                            ƒ_#Xcode.IDENoticesKit.NoticeNavigatorÓ   ÅÉ 8£ÆÇÈ£Êslú€:_%Xcode.IDEKit.NavigatorGroup.Structure_"Xcode.IDEKit.NavigatorGroup.Issues_ Xcode.IDEKit.NavigatorGroup.Test_ Xcode.IDEKit.Navigator.WorkspaceÓ   ÓØ 8¤ÔÕÖ×¤ƒÚÛƒ !€:_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey_IDENavigatorModeSolitaryÓ   äæ ¡å"¡ç#€_IDENavigatorModeSolitaryÒ ëší_codablePlistRepresentation	$O_bplist00Ô_lastAccessedDateYitemState]selectedItems^scrollPosition3AÆÎâßòì}¤Ò	
TpathYindexHint¢[TheMealsApp_$Missing package product 'RealmSwift' Ò^expansionState3AÆÎµˆĞùºÒ	¡Ò3AÆÎây‘pX ¢¢#        #À$      ¢#@q      #@Ø        $ . < K T Y ^ c m p | £ ¥ ª ¹ Â Ç É Î × Ø Û Ş ç ğ ó ü                           #@p@     Ó   ñô 8¢òó'(¢²ö€Ç)€:_'userPreferredInspectorGroupExtensionIDs_!userPreferredCategoryExtensionIDsÒ  û€ ©#@q      Ó   ÿ 8« 	
,-./0123456« .7€‹89:F6?Z[\€:^MaximizedState_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_DebuggerSplitView_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_IDEDefaultDebugArea_ EditorMultipleSplitPrimaryLayout_ SelectedEditorAreaSplitIndexPath_ DefaultPersistentRepresentations ZOpenInTabs Ó   ') 8¡(;¡*<€:_DVTSplitViewItemsÒ  .€¢/0=C©Ó   37 £456>?@£8 0:A€wB€]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeYIDEEditor#@i`     Ó   BF £456>?@£GœID€xE€_IDEDebuggerArea#@\À     Ò  N€¡OG©Ó   RY 8¦STUVWXHIJKLM¦Z[\Z^œNON5€x€:ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestCommentsÓ   il 8¢jkPQ¢mnR€:_TabsAsHistoryItems_SelectedTabIndexÒ  s S«tuvwxyz{|}~S‰´İ.Aj’»å€)Ø‚ƒ„ …†‡ˆ‰Š‹Œ P_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifierXkmTˆ‡€#lÖ‘’“ ”•–—˜™š ._DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifierdVgjU€‹_/Xcode.IDENavigableItemDomain.WorkspaceStructureÒ   S¦Ÿ ¡¢£¤WZ\_ab€)Ó§¨ ˆp«ZIdentifierUIndexX-Y_MealInteractor.swiftÒ \ ]®¯_IDEArchivableStringIndexPair¢° `_IDEArchivableStringIndexPairÓ§¨ ²¶«[€vYWUseCaseÓ§¨ ·¸«]^YVDomainÓ§¨ ¼¶«`€vYTCoreÓ§¨ W .«€‹YÓ§¨ Å .«c€‹Y[TheMealsAppÓ ¹  ·ÊË Wef€ _sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swiftÒ \ ]ÏĞ_DVTDocumentLocation¢Ñ `_DVTDocumentLocationÒ ÓÔÕZpathStringih_M/Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeprojÒ \ ]ØÙ[DVTFilePath£ÚÛ `[DVTFilePath_PackedPathEntryÒ \ ]İŞ_(IDENavigableItemArchivableRepresentation¢ß `_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCodeÓ   ãî 8ªäåæçèéêëìínopqrstuvwªï 0œœóÙÚœ÷ 0x€w€x€xz©ª€x…€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   S¡y€)Ò  	 S¤ï .ï .œ€‹œ€‹€)Ó    ¢{|¢}€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ    ¢Ê~€}¢	²¢€Ç€^documentLengthÓ   #' £$%&€‚£()Şƒ„€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ0 1é\NS.uuidbytesOé&‡ı©ÛD¨µ&Bu×æ€ˆÒ4 5é\NS.uuidbytesO>ä”™KüMğ²åÛíñ€³€ˆÓ   8; ¢õ:€†¢²	€Ç¢€^documentLength^MealInteractorÒ \ ]BC_IDEEditorHistoryItem¢D `_IDEEditorHistoryItemØ‚ƒ„ …†‡F‰HIŒK Nk™Šˆ³€lÖ‘’“ ”•OP˜™š .—‹gjU€‹Ò  V S¦WXYZ[\Œ’”•€)Ó§¨ F .«€‹Y_DetailRouter.swiftÓ§¨ d¶«€vYVRouterÓ§¨ i .«‘€‹YVDetailÓ§¨ n¸«“^YVModuleÓ§¨ W .«€‹YÓ§¨ w .«–€‹Y[TheMealsAppÓ ¹  ·|Ë W˜f€ _rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swiftÓ   Œ 8ª‚ƒ„…†‡ˆ‰Š‹š›œŸ ¡¢£ª 0œœ‘– .œ• 0¤€w€x€x¦€‹€x±€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  £ S¡¤¥€)Ò  § S¤‘‘¡¢¡¢€)Ó   ®± ¢¯°§¨¢²³©¯€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ¸¼ £¹º»ª«¬£½¾Ş­®€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒÅ Æé\NS.uuidbytesOa;¼Æ ²Hİ‚‘=ãŸœß€ˆÒÉ Êé\NS.uuidbytesOY”×ËpC†„ŞÇgXä9©€ˆÓ   ÍĞ ¢ÎÊ°€}¢¼²®€Ç€^documentLengthÓ   ÖÙ ¢õØ€²¢²¼€Ç®€^documentLength_makeMealView(for:game:)Ø‚ƒ„ …†‡à‰âãŒà O¸kÃµˆ¸€!lÖ‘’“ ”•éê˜™š .Á¶gjU€‹Ò  ğ S¦ñòóôõö·¹º½¾¿€)Ó§¨ à .«¸€‹Y_SearchRouter.swiftÓ§¨ d¶«€vYÓ§¨ «»¼YVSearchÓ§¨ n¸«“^YÓ§¨ W .«€‹YÓ§¨  .«À€‹Y[TheMealsAppÓ ¹  ·Ë WÂf€ _rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swiftÓ   % 8ª !"#$ÄÅÆÇÈÉÊËÌÍª& 0œœ*– .œ. 0Î€w€x€xĞ€‹€xÛ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  < S¡=Ï€)Ò  @ S¤Z¶Z¶;€v;€v€)Ó   GJ ¢HIÑÒ¢KLÓÙ€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   QU £RSTÔÕÖ£VWŞ×Ø€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ^ _é\NS.uuidbytesOQ´Ğ$nO““C7Œé«€ˆÒb cé\NS.uuidbytesOö='£%SAÛ»Ú<Ñçİ€ˆÓ   fi ¢gÊÚ€}¢	f²¿€Ç€^documentLengthÓ   or ¢õq€Ü¢²	f€Ç¿€^documentLengthØ‚ƒ„ …†‡x‰z{Œ} MákëŞˆ€lÖ‘’“ ”•‚˜™š .éßgjU€‹Ò  ˆ S¦‰Š‹Œàâäåæç€)Ó§¨ x .«á€‹Y_DetailPresenter.swiftÓ§¨ – .«ã€‹YYPresenterÓ§¨ i .«‘€‹YÓ§¨ n¸«“^YÓ§¨ W .«€‹YÓ§¨ § .«è€‹Y[TheMealsAppÓ ¹  ·¬Ë Wêf€ _xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swiftÓ   ±¼ 8ª²³´µ¶·¸¹º»ìíîïğñòóôõª½ 0œœÁYZœÅ 0ö€w€x€xø:;€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ó S¡Ô÷€)Ò  × S¤opop,-,-€)Ó   Şá ¢ßàùú¢âãûı€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ   èë ¢Êê€}ü¢²†€Ç3€^documentLengthÓ   ñõ £òóôşÿ £ö÷Ş€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒş ÿé\NS.uuidbytesOQÙ°ÊT@U¾_8(ÌÙ`€ˆÒ é\NS.uuidbytesO¥Æ;÷@8—ü.íG>ò€ˆÓ   	 ¢õ€¢²†€Ç3€^documentLength_DetailPresenterØ‚ƒ„ …†‡‰Œ L
kˆ-€lÖ‘’“ ”•˜™š .gjU€‹Ò    S¦!"#$%&	€)Ó§¨  .«
€‹Y_SearchPresenter.swiftÓ§¨ – .«ã€‹YÓ§¨ «»¼YÓ§¨ n¸«“^YÓ§¨ W .«€‹YÓ§¨ > .«€‹Y[TheMealsAppÓ ¹  ·CË Wf€ _xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swiftÓ   HS 8ªIJKLMNOPQRªT 0œœXœ\ 0€w€x€x €ª¡€x+€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  j S¡k€)Ò  n S¤ñòñò¾¿¾¿€)Ó   ux ¢vw!"¢yz#)€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ƒ £€‚$%&£„…Ş'(€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒŒ é\NS.uuidbytesO“ºKÿÎvJ8£æâ‘/½6€ˆÒ ‘é\NS.uuidbytesOwÙ0•"}F~™êf5ít€ˆÓ   ”— ¢Ê–€}*¢²€ÇË€^documentLengthÓ     ¢õŸ€,¢²€ÇË€^documentLength_linkBuilder(for:game:content:)Ø‚ƒ„ …†‡§¨©ªŒ¬ R®39;/ˆ@€':Ö‘’“ ”•°±˜™´µ81gj07_1Xcode.IDENavigableItem.WorkspaceGroupedLogsDomainÒ  ¸ S¢¹º25€)Ó§¨ §¾«34YWAnalyzeÓ§¨ Ã .«6€‹Y[TheMealsAppÿÿÿÿÿÿÿÓ ¹  · ŒË W€(f€ _$com.apple.dt.IDE.BuildLogContentType_'Xcode.IDEKit.EditorDocument.LogDocumentÓ   ÏÑ 8¡Ğ<¡Ò=€:_SelectedDocumentLocationsÒ  Ö S¡×>€)Õ  · ¸ ¹ º » W ½İ ë€9€ ?€?_2x-xcode-log://DE4259B5-7817-44E7-8091-BC59E4CDE608_Analyze target RealmSwiftØ‚ƒ„ …†‡â‰äåŒç QEkOBˆi€%lÖ‘’“ ”•ëì˜™š .MCgjU€‹Ò  ò S¦óôõö÷øDFGIJK€)Ó§¨ â .«E€‹Y_FavoriteRouter.swiftÓ§¨ d¶«€vYÓ§¨ ¶«H€vYXFavoriteÓ§¨ n¸«“^YÓ§¨ W .«€‹YÓ§¨  .«L€‹Y[TheMealsAppÓ ¹  ·Ë WNf€ _vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swiftÓ   & 8ª !"#$%PQRSTUVWXYª' 0œœ+– .œ/ 0Z€w€x€x\€‹€xg€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  = S¡>[€)Ò  A S¤&ñ&ñ€}€}€)Ó   HK ¢IJ]^¢LM_e€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   RV £STU`ab£WXŞcd€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ_ `é\NS.uuidbytesOmÚ3ßÒAB¹Ùså®öÇ€ˆÒc dé\NS.uuidbytesO-ØG«÷ğJ®[0r¡=İ€ˆÓ   gj ¢hÊf€}¢²‰€Ç€^documentLengthÓ   ps ¢õr€h¢²€Ç‰€^documentLength_makeMealView(for:game:)Ø‚ƒ„ …†‡z‰|}Œ Knkwkˆ‘€lÖ‘’“ ”•ƒ„˜™š .ulgjU€‹Ò  Š S¦‹Œmopqrs€)Ó§¨ z .«n€‹Y_FavoritePresenter.swiftÓ§¨ – .«ã€‹YÓ§¨ ¶«H€vYÓ§¨ n¸«“^YÓ§¨ W .«€‹YÓ§¨ ¨ .«t€‹Y[TheMealsAppÓ ¹  ·­Ë Wvf€ _|file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swiftÓ   ²½ 8ª³´µ¶·¸¹º»¼xyz{|}~€ª¾ 0œœÂŸ .œÆ 0‚€w€x€x„€Š€‹€x€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ô S¡Õƒ€)Ò  Ø S¤êëêë€×€Ø€×€Ø€)Ó   ßâ ¢àá…†¢ãä‡€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   éí £êëìˆ‰Š£îïŞ‹Œ€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒö ÷é\NS.uuidbytesO$‘çCû…îLğAñ`ë€ˆÒú ûé\NS.uuidbytesOİ;Øz"C0œ ·.5o€ˆÓ   ş ¢Ê €}¢²€Ç€ä€^documentLengthÓ   
 ¢õ	€¢²€Ç€ä€^documentLength_linkBuilder(for:game:content:)Ø‚ƒ„ …†‡‰Œ J–k “ˆº€lÖ‘’“ ”•˜™š .”gjU€‹Ò  ! S¦"#$%&'•—™š›œ€)Ó§¨  .«–€‹Y_FavoriteView.swiftÓ§¨ /¸«˜^YTViewÓ§¨ ¶«H€vYÓ§¨ n¸«“^YÓ§¨ W .«€‹YÓ§¨ @ .«€‹Y[TheMealsAppÓ ¹  ·EË WŸf€ _rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swiftÓ   JU 8ªKLMNOPQRST¡¢£¤¥¦§¨©ªªV 0œœZ
»œ^ 0«€w€x€x­€ª5€x¸€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  l S¡m¬€)Ò  p S¤‘X‘X¢Y¢Y€)Ó   wz ¢xy®¯¢{|°¶€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   … £‚ƒ„±²³£†‡Ş´µ€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ é\NS.uuidbytesOlZ»T+Ly£Dì]“ü9€ˆÒ’ “é\NS.uuidbytesOc%¾»IWµ[P­ {€ˆÓ   –™ ¢Ê˜€}·¢²
ú€Ç4€^documentLengthÓ   Ÿ¢ ¢õ¡€¹¢²
ú€Ç4€^documentLengthWcontentØ‚ƒ„ …†‡©‰«¬Œ® I¿kÈ¼ˆä€lÖ‘’“ ”•²³˜™š .Æ½gjU€‹Ò  ¹ S¥º»¼½¾¾ÀÂÃÄ€)Ó§¨ © .«¿€‹Y_Injection.swiftÓ§¨ Æ¶«Á€vYRDIÓ§¨ ¼¶«`€vYÓ§¨ W .«€‹YÓ§¨ Ó .«Å€‹Y[TheMealsAppÓ ¹  ·ØË WÇf€ _bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/DI/Injection.swiftÓ   İè 8ªŞßàáâãäåæçÉÊËÌÍÎÏĞÑÒªé 0œœíŸ .œñ 0Ó€w€x€x×€Š€‹€xâ€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ÿ S¡ Ô€)Ò   S¤ÕÖÕÖ€)!PÓ    ¢ØÙ¢ÚÜ€_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtensionÓ    ¢Ê€}Û¢²
†€Ç€^documentLengthÓ   # £ !"İŞß£$%Şàá€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ, -é\NS.uuidbytesOn
WIEDŸŸ^ôé4€ˆÒ0 1é\NS.uuidbytesO	rhûííK§³ÑËıIÊØ€ˆÓ   47 ¢õ6€ã¢²
†€Ç€^documentLength_provideMeal(meal:game:)Ø‚ƒ„ …†‡>‰@AŒC ûékòæˆ€NlÖ‘’“ ”•GH˜™š .ğçgjU€‹Ò  N S¥OPQRSèêìíî€)Ó§¨ >¸«é^Y_MealRepository.swiftÓ§¨ [ .«ë€‹YTDataÓ§¨ ¼¶«`€vYÓ§¨ W .«€‹YÓ§¨ h .«ï€‹Y[TheMealsAppÓ ¹  ·mË Wñf€ _ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swiftÓ   r} 8ªstuvwxyz{|óôõö÷øùúûüª~ 0œœ‚WXœ† 0ı€w€x€xÿXY€x
€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ” S¡•ş€)Ò  ˜ S¤mnmnJKJK€)Ó   Ÿ¢ ¢ ¡ ¢£¤€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   ©­ £ª«¬£®¯Ş€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒ¶ ·é\NS.uuidbytesOò ±ŠøIî“dl«À—¶€ˆÒº »é\NS.uuidbytesO"êøZiI…ˆ”q-l(×€ˆÓ   ¾Á ¢¿Ê	€}¢˜²W€Ç€^documentLengthÓ   ÇÊ ¢õÉ€¢²˜€ÇW€^documentLength_getGameDetail(by:)	Ò  Ò€¡Ó©ÒÖ ×Ø_currentEditorHistoryItem4Ø‚ƒ„ …†‡©‰ÜİŒ© I¿kˆ¿€lÖ‘’“ ”•ãä˜™š .gjU€‹Ò  ê S¥ëìíîï€)Ó§¨ © .«¿€‹YÓ§¨ Æ¶«Á€vYÓ§¨ ¼¶«`€vYÓ§¨ W .«€‹YÓ§¨  .«€‹Y[TheMealsAppÓ ¹  ·ØË WÇf€ Ó    8ª !"#$ª 0œœŸ .œ 0%€w€x€x'€Š€‹€x2€w€:_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  - S¡.&€)Ò  1 S¤nZnZK;K;€)Ó   8; ¢9:()¢<=*0€_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtensionÓ   BF £CDE+,-£GHŞ./€†€_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.widthÒO Pé\NS.uuidbytesOn
WIEDŸŸ^ôé4€ˆÒS Té\NS.uuidbytesO	rhûííK§³ÑËıIÊØ€ˆÓ   WZ ¢ÊY€}1¢²
†€Ç€^documentLengthÓ   `c ¢õb€3¢²
†€Ç€^documentLengthÒ \ ]ij_IDEEditorHistoryStack¢k `_IDEEditorHistoryStack_ItemKind_EditorÖ‘’“ ”•no˜™š .>7gjU€‹Ò  u S¥vwxyz89:;<€)Ó§¨ © .«¿€‹YÓ§¨ Æ¶«Á€vYÓ§¨ ¼¶«`€vYÓ§¨ W .«€‹YÓ§¨  .«=€‹Y[TheMealsAppÓ ¹  ·ØË WÇf€ Ó   –› 8¤—˜™š@ABC¤œ¶ŸDI€vQ€:XLeftView_IDESplitViewDebugAreaZLayoutModeYRightViewÓ   ¦« 8¤§¨©ªEFGH¤œ .² 0€x€‹€Ç€w€:_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsTypeÓ   ¶¸ 8¡·J¡¹K€:_DVTSplitViewItemsÒ  ½€¢¾¿LO©Ó   ÂÆ £456>?@£Ç 0ÉM€wN€XLeftView#@…ø     Ó   ÎÒ £456>?@£Ó 0ÉP€wN€YRightViewÓ   Ùâ 8¨ÚÛÜİŞßàáRSTUVWXY¨œœœ 0œœœœ€x€x€x€w€x€x€x€x€:_+IDEStructuredConsoleAreaLibraryEnabledState_-IDEStructuredConsoleAreaTimestampEnabledState_*IDEStructuredConsoleAreaPIDTIDEnabledState_,IDEStructuredConsoleAreaMetadataEnabledState_(IDEStructuredConsoleAreaTypeEnabledState_-IDEStructuredConsoleAreaSubsystemEnabledState_/IDEStructuredConsoleAreaProcessNameEnabledState_,IDEStructuredConsoleAreaCategoryEnabledState_Layout_LeftToRightÓ Áö »ò Å_NSIndexPathValue€8Ó   ùú 8  €:   " , 1 : ? Q V \ ^"/7BINPRWY\^uœ©ÆÈÊÌÎĞÒÔÖØÚÜŞàâÿ	"$&A^k‹²ÄÚ÷"<N˜¡¶¸º¼¾ÀÂÄÆÈÊÌÙáíïñóXalu{€‰–˜šœ "$£°²´¶1>@BD¿ÌÎĞÒGTVXZÏÜŞàâXegikäñóõ÷,5=BOVXZ\cegjl–Æ !.024ivy{~€‚§ª¬®ÃÍàìöøúûıÿ 2468AILNWels|ˆ™¢»ÂÛñú$1469;=Ybegi~€‚ƒ…‡”–˜¡¤¦³îğòôöøúüş 
 "$&acegiknqtwz}€ƒ†‰Œ’•˜›¡¤§ª­°²¿ÁÃÅ;HJLN¾ËÍÏÑKXZ\^Øåçéë`moqsâïñóõanprtéöøúüw„†ˆŠş‡”–˜š  # % ' ) £ ° ² ´ ¶!,!9!;!=!?!·!Ä!Æ!È!Ê"="J"L"N"P"Å"Ò"Ô"Ö"Ø#P#]#_#a#c#Ó#à#õ#÷#ù#û#ı#ÿ$$$$$	$$ $"$$$&$($*$,$.$0$2$4$Q$p$”$¶$Ú% %$%C%b%%ˆ%‹%%%˜%¡%£%¥%§%©%«%­%¯%°%±%¾%Ã%Å%Ç%Ì%Î%Ğ%Ò%ï&&&$&&&(&-&/&1&3&?&N&W&X&Z&]&f&k&x&&&ƒ&…&Œ&&&’&”&µ&ç'''"'/'B'D'M'T'Y'b'o'‚'„'''œ'¡'£'¥'ª'¬'®'°'¶'Å'Ò'ç'é'ë'í'ï'ñ'ó'õ'÷'ù'û((((((((( ("($(&(C(b(†(¨(Ì(ò))5)T)q)z)})))Š)“)•)—)™)›))Ÿ)¡)®)³)µ)·)¼)¾)À)Â)ß********!*#*2*5*B*I*K*M*O*V*X*Z*\*^**±*Ú*ã*ğ+++++.+0+9+;+H+M+O+Q+V+X+Z+\+k+x+++‘+“+•+—+™+›++Ÿ+¡+¶+¸+º+¼+¾+À+Â+Ä+Æ+È+Ê+Ì+é,,,,N,r,˜,¼,Û,ú-- -#-%-'-0-9-;-=-?-A-C-P-U-W-Y-^-`-b-d-‡-¤-±-¸-º-¼-¾-Å-Ç-É-Ë-Í-ö.(.I.R._.r.t.}.Š..Ÿ.¬.±.³.µ.º.¼.¾.À.Ï.Ò.Û.Ü.Ş.ë.ğ.ò.ô.ù.û.ı.ÿ///0/2/4/6/8/:/</>/@/B/D/Y/[/]/_/a/c/e/g/i/k/m/o/Œ/«/Ï/ñ00;0_0~00º0Ã0Æ0È0Ê0Ó0Ü0Ş0à0â0ä0æ0è0ê0÷0ü0ş1 111	11.1K1X1_1a1c1e1l1n1p1r1t1•1¾1ğ1ù2222$212D2F2S2X2Z2\2a2c2e2g2v2y2†2‹222”2–2˜2š2©2¶2Ë2Í2Ï2Ñ2Ó2Õ2×2Ù2Û2İ2ß2ô2ö2ø2ú2ü2ş3 3333	33(3G3k33±3×3û4494V4_4b4d4f4o4x4z4|4~4€4‚4„4†4“4˜4š4œ4¡4£4¥4§4Ê4ç4ô4û4ı4ÿ555
555515Z5Œ5•5¢5µ5·5À5Í5à5â5ï5ô5÷5ù5ş666666$6)6+6.6365686:6I6V6k6n6q6t6w6z6}6€6ƒ6†6‰66¡6£6¥6§6ª6­6¯6±6´6¶6¸6Õ6ô77:7^7„7¨7Ç7æ8888888&8(8*8,8.808=8B8E8H8M8P8S8U8x8•8¢8©8¬8¯8²8¹8¼8¾8Á8Ã8ì99?9H9U9h9j9s9€9“9•9¢9§9ª9¬9±9´9¶9¸9Ç9Ê9Ó9à9å9ç9ê9ï9ñ9ô9ö:::':*:-:0:3:6:9:<:?:B:E:Z:]:_:a:c:f:i:k:m:p:r:t:‘:°:Ô:ö;;@;d;ƒ;¢;¿;È;Ë;Î;Ğ;Ù;â;å;è;ë;î;ğ;ò;ô<<<	<<<<<<6<Y<f<k<m<p<u<w<z<|<‹<<›<¢<¥<¨<«<²<µ<¸<º<¼<å==8=A=N=a=c=l=y=Œ==›= =¢=¥=ª=¬=¯=±=À=Í=â=å=è=ë=î=ñ=ô=÷=ú=ı> >>>>>>!>$>'>)>,>.>0>M>l>>²>Ö>ü? ???^?{?„?‡?Š?Œ?•??¡?¤?§?ª?¬?®?°?½?Â?Å?È?Í?Ğ?Ó?Õ?ø@@"@)@,@/@2@9@<@?@A@C@l@@¿@È@Õ@è@ê@óA AAA"A'A*A,A1A4A6A8AGAJASAUAbAgAiAlAqAsAvAxA‡A”A©A¬A¯A²AµA¸A»A¾AÁAÄAÇAÜAßAáAãAåAèAêAìAîAñAóAõBB1BUBwB›BÁBåCC#C@CICLCOCQCZCcCfChCkCmCoCqC~CƒC†C‰CC‘C”C–C¹CÖCãCêCíCğCóCúCüCÿDDD%DND€D‰D–D©D«D´DÁDÔDÖDãDèDëDíDòDõD÷DùEEEEEE"E'E)E,E.E=EJE_EbEeEhEkEnEqEtEwEzE}E’E•E—E™E›EE¡E£E¥E¨EªE¬EÉEèFF.FRFxFœF»FÚF÷G GGGGGGG G#G&G(G*G7G<G?GBGGGJGMGOGrGGœG£G¦G©G¬G³G¶G¸G»G½GæHH9HBHOHbHdHmHzHHHœH¡H£H¦H«H­H°H²HÁHÄHÑHÖHØHÛHàHâHåHçHöIIIII!I$I'I*I-I0I3I6IKINIPIRITIWIZI\I^IaIcIeI‚I¡IÅIçJJ1JUJtJ“J°J¹J¼J¿JÁJÊJÓJÖJÙJÜJßJáJãJåJòJ÷JúJıKKKK
K-KJKWK^KaKdKgKnKqKsKvKxKªKËKôKıL
LLL(L5LHLJLWL\L^LaLfLhLkLmL|LLŒL‘L“L–L›LL L¢L±L¾LÓLÖLÙLÜLßLâLåLèLëLîLñMM	MMMMMMMMMM M=M\M€M¢MÆMìNN/NNNkNtNwNzN|N…NN‘N”N—NšNœNN N­N²NµN¸N½NÀNÃNÅNèOOOOOO"O)O+O.O1O3OTO†O¯O¸OÅOØOÚOãOğPPPPPPP!P$P&P(P7P:PGPLPNPQPVPXP[P]PlPyPP‘P”P—PšPP P£P¦P©P¬PÁPÄPÆPÈPÊPÍPĞPÒPÔP×PÙPÛPøQQ;Q]QQ§QËQêR	R&R/R2R5R7R@RIRKRMRORQRSR`ReRhRkRpRsRvRxR›R¸RÅRÌRÏRÒRÕRÜRßRâRäRæSSASbSkSxS‹SS–S£S¶S¸SÅSÊSÍSÏSÔSÖSØSÚSéSöSûSıT TTT	TTT'T<T?TBTETHTKTNTQTTTWTZToTrTtTvTxT{T}TTT„T†TˆT¥TÄTèU
U.UTUxU—U¶UÓUÜUßUâUäUíUöUøUúUüUşV VVVVVV V#V%VHVeVrVyV|VV‚V‰V‹VV‘V“V´VİWWW%W8W:WCWPWcWeWrWwWyW|WWƒW†WˆW—WšW§W¬W®W±W¶W¸W»W½WÌWÙWîWñWôW÷WúWıX XXX	XX!X$X&X(X*X-X0X2X4X7X9X;XXXwX›X½XáYY+YJYiY†YY’Y•Y—Y Y©Y«Y®Y°Y³YµY·YÄYÉYÌYÏYÔY×YÚYÜYùZZ)Z.Z1Z3Z8Z;Z=Z?ZNZQZ^ZeZhZkZnZuZwZzZ}ZZ ZÉZû[[[$[&[/[<[O[Q[^[c[e[h[m[o[r[t[ƒ[[¥[¨[«[®[±[´[·[º[½[À[Ã[Ø[Û[İ[ß[á[ä[ç[ê[ì[ï[ñ[ó\\/\S\u\™\¿\ã]]!]>]G]J]M]O]X]a]d]g]j]m]o]q]s]€]…]ˆ]‹]]“]–]˜]µ]Ø]å]ê]ì]ï]ô]ö]ù]û^
^^^!^$^'^*^1^4^7^9^;^d^–^·^À^Í^à^â^ë^ø_____%_*_,_/_4_6_9_;_J_W_l_o_r_u_x_{_~__„_‡_Š_Ÿ_¢_¤_¦_¨_«_®_°_²_µ_·_¹_Ö_õ``;`_`…`©`È`çaaaaaaa'a)a+a-a/a1a>aCaFaIaNaQaTaVasa–a£a¨a«a­a²aµa·a¹aÈaËaØaßaâaåaèaïaòaõa÷aùb"bTbub~b‹bb b©b¶bÉbËbØbİbßbâbçbébìbîbıc
cc"c%c(c+c.c1c4c7c:c=cRcUcWcYc[c^c`cbcdcgcickcˆc§cËcídd7d[dzd™d¶d¿dÂdÅdÇdĞdÙdÛdİdßdádãdğdõdødûe eeee%eHeUeZe\e_edefeiekeze}eŠe‘e”e—eše¡e¤e§e©e«eÔff'f0f=fPfRf[fhf{f}fŠff‘f”f™f›ff f¯f¼fÑfÔf×fÚfİfàfãfæféfìfïggg	gggggggggg:gYg}gŸgÃgéhh,hKhhhqhthwhyh‚h‹hhh“h•h—h¤h©h¬h¯h´h·hºh¼hÙhüi	iiiiiiii.i1i>iEiHiKiNiUiWiZi]i_i€i²iÛiäiñjjjjj/j1j>jCjEjHjMjOjRjTjcjpj…jˆj‹jj‘j”j—jšjj j£j¸j»j½j¿jÁjÄjÇjÊjÌjÏjÑjÓjğkk3kUkykŸkÃkâlll'l*l-l/l8lAlDlFlIlKlMlOl\laldlglllolrltl‘l´lÁlÆlÈlËlĞlÒlÕl×lælélölım mmmmmmmm@mam“mœm©m¼m¾mÇmÔmçmémòmônnnnnnnnn&n3nHnKnNnQnTnWnZn]n`ncnfn{n~n€n‚n„n‡nŠnŒnn‘n“n•n²nÑnõoo;oao…o¤oÃoàoéoìoïoñoúpppppppp!p$p'p,p/p2p4pQptpp†pˆp‹pp’p•p—p¦p©p¶p½pÀpÃpÆpÍpÏpÒpÕp×pøq!qSq\qiq|q~q‡q”q§q©q¶q»q½qÀqÅqÇqÊqÌqÛqèqır rrr	rrrrrrr0r3r5r7r9r<r?rArCrFrHrJrgr†rªrÌrğss:sYsxs•ss¡s¤s¦s¯s¸sºs¼s¾sÀsÂsÏsÔs×sÚsßsâsåsçtt't4t9t;t>tCtEtHtJtYt\titptstvtyt€tƒt…tˆtŠt¼tİuuuu/u1u:uGuZu\uiunupusuxuzu}uuu›u°u³u¶u¹u¼u¿uÂuÅuÈuËuÎuãuæuèuêuìuïuòuõu÷uúuüuşvv:v^v€v¤vÊvîww,wIwRwUwXwZwcwlwowqwtwvwxwzw‡wŒww’w—wšwwŸw¼wßwìwñwôwöwûwşx xxxx!x(x+x.x1x8x;x=x@xBxtx•x¾xÇxÔxçxéxòxÿyyyyy,y1y3y6y;y=y@yByQy^ysyvyyy|yy‚y…yˆy‹yy‘y¦y©y«y­y¯y²y´y¶y¸y»y½y¿yÜyûzzAzez‹z¯zÎzí{
{{{{{${-{0{3{6{9{;{H{M{P{S{X{[{^{`{}{ {­{²{´{·{¼{¾{Á{Ã{Ò{Õ{â{é{ì{ï{ò{ù{ü{ÿ|||,|^||ˆ|•|¨|ª|³|À|Ó|Õ|â|ç|é|ì|ñ|ó|ö|ø}}})},}/}2}5}8};}>}A}D}G}\}_}a}c}e}h}j}m}o}r}t}v}“}²}Ö}ø~~B~f~…~¤~Á~Ê~Í~Ğ~Ò~Û~ä~ç~ê~í~ğ~ò~ÿ
:Wdknqt{~ƒ…·à€€
€€*€,€5€B€U€W€d€i€k€n€s€u€x€z€‰€Œ€€›€ €¢€¥€ª€¬€¯€±€À€Í€â€å€è€ë€î€ñ€ô€÷€ú€ı !#%'*,.Kj°Ôú‚‚=‚\‚y‚‚‚…‚ˆ‚Š‚“‚œ‚Ÿ‚¢‚¥‚¨‚ª‚¬‚¹‚¾‚Á‚Ä‚É‚Ì‚Ï‚Ñ‚ôƒƒƒ%ƒ(ƒ+ƒ.ƒ5ƒ7ƒ:ƒ=ƒ?ƒ`ƒ‰ƒ»ƒÄƒÑƒäƒæƒïƒü„„„„#„&„(„-„0„2„4„C„F„S„X„Z„]„b„d„g„i„x„…„š„„ „£„¦„©„¬„¯„²„µ„¸„Í„Ğ„Ò„Ô„Ö„Ù„Ü„ß„á„ä„æ„è……$…H…j……´…Ø…÷††3†<†?†B†D†M†V†X†Z†\†^†`†m†r†u†x†}†€†ƒ†…†¨†Å†Ò†Ù†Ü†ß†â†é†ì†ï†ñ†ó‡‡N‡o‡x‡…‡˜‡š‡£‡°‡Ã‡Å‡Ò‡×‡Ú‡Ü‡á‡ä‡æ‡è‡÷‡ú‡üˆ	ˆˆˆˆˆˆˆˆ.ˆ;ˆPˆSˆVˆYˆ\ˆ_ˆbˆeˆhˆkˆnˆƒˆ†ˆˆˆŠˆŒˆˆ’ˆ”ˆ–ˆ™ˆ›ˆˆºˆÙˆı‰‰C‰i‰‰¬‰Ë‰è‰ñ‰ô‰÷‰ùŠŠŠŠŠŠŠŠŠ&Š+Š.Š1Š6Š9Š<Š>ŠaŠ~Š‹Š’Š•Š˜Š›Š¢Š¥Š§ŠªŠ¬ŠÕŠö‹(‹1‹>‹Q‹S‹\‹i‹|‹~‹‹‹‹“‹•‹š‹‹Ÿ‹¡‹°‹³‹À‹Å‹Ç‹Ê‹Ï‹Ñ‹Ô‹Ö‹å‹ò‹õ‹ø‹û‹şŒ ŒŒŒŒŒkŒxŒŒ„Œ‡ŒŠŒŒ–Œ™ŒœŒŸŒ¢Œ¤ŒÔ2[uŠ“–™›¬¶¹¼¿Â"/47:?BEGNUjvŸ¦ÆÜéêëíúı "'*3:?HKMO\_adgiruwzƒ’™¦­°³¶½ÀÃÆÈé!JWZ]`ceqz‚‹—£°³¶¹¼¾ÊÓİà‘‘'‘9‘@‘I‘V‘Y‘\‘_‘b‘d‘¢‘«‘´‘·‘Ä‘Ù‘Ü‘ß‘â‘å‘è‘ë‘î‘ñ‘ô‘÷’’’’’’’’’!’$’'’)’4’K’d’}’’§’¼’Æ’Û’ó“““X“_“u“|“‡““‘“”“¡“ª“­“°“³“¶“¿“Â“Å“È“Ë“Í“æ”””O”U”^”`”i”p”s”v”y”|”‰””“”–”™” ”£”¦”©”«”Ô••.•0•=•D•G•J•M•T•W•Z•]•_•h•o•x™„™†™“™ ™£™¦™©™¬™¯™²™¿™Â™Ä™Ç™Ê™Ì™Î™Ğ™õššDšnšwš€š‰šŒš¯š¼šÍšĞšÓšÖšÙšÜšßšâšåšöšøšûšıšÿ››››	››#›2›b›}›¦›È›äœ%œGœTœcœfœiœlœoœrœuœxœ‡œ‰œŒœœ’œ•œ—œšœœœ¯œÂœÖœéœı+69<?BEPSVY\_a„©½Ïõ #&),.EYrš›œ¹ÆÉÌÏÒÔïøŸŸŸ¢ø££*£1£Z£¿£Ì£Õ£Ø£Û£Ş£á£ê£í£ğ£ó£ö£ø¤¤#¤<¤W¤d¤e¤f¤h¤ƒ¤¤“¤–¤™¤œ¤¤¹¤Â¤ß¤â¤å¥š¥À¥Í¥Ô¥×¥Ú¥İ¥ä¥ç¥ê¥í¥ï¦¦<¦_¦‚¦¦˜¦›¦¦¡¦¤¦­¦°¦³¦¶¦¹¦»¦Ò¦æ¦ÿ§§5§B§E§H§K§N§P§k§t§‘§”§—¨ú©©©©©© ©"©%©'©Q©u©~©©‚©‹©˜©¯©²©µ©¸©»©¾©Á©Ä©Ç©Ê©Í©Ğ©ç©ê©ì©ï©ò©õ©ø©û©şªªªª	ªªEªWªbªvªª´ªÊªí««3«5«@«B«O«R«U«X«[«]«q«z««‚«…«ˆ«•«œ«Ÿ«¢«¥«¬«¯«±«´«¶«Ä«Ñ«ä«î«÷¬¬¬¬¬¬¬¬ ¬#¬%¬7¬@¬I¬L¬O¬R¬_¬l¬o¬r¬u¬x¬{¬~¬‹¬¬‘¬”¬—¬š¬œ¬¬©¬½¬Ó¬á¬í­­­­!­$­'­,­/­2­4­I­\­e­|­­‚­…­ˆ­‹­­‘­”­—­š­­Ÿ­À­Ü® ®®0®D®P®n®q®t®w®z®}®€®‚®…®®±®À®Ø®ë¯¯
¯¯¯¯¯¯J¯S¯`¯c¯f¯i¯l¯o¯r¯t¯¯Œ¯’¯•¯˜¯›¯²¯»¯Ú¯ß¯ş°°°°°°(°+°.°1°8°E°H°J°M°R°_°b°d°g°t°w°y°|°ˆ°•°˜°›°±±±2±7±M±V±a±d±g±·±À±Ì±Ó±ß±ñ±ú²%²*²U²k²›²¨²½²À²Ã²Æ²É²Ì²Ï²Ò²Õ²Ø²Û²ğ²ó²õ²÷²ù²ü²ÿ³³³³	³³(³G³k³³±³×³û´´9´V´_´b´e´g´p´y´|´~´´ƒ´…´’´—´š´´¢´¥´¨´ª´Ç´ê´÷´ü´ÿµµµ	µµµµ)µ0µ3µ6µ9µ@µCµFµHµJµsµ¥µÆµÏµÜµïµñµú¶¶¶¶)¶.¶0¶3¶8¶:¶=¶?¶N¶]¶f¶}¶‚¶™¶º¶½¶À¶Ã¶Æ¶É¶Ì¶Î¶Ñ¶ê¶í¶ğ¶ó¶ö¶ù¶û······· ·#·%·2·5·7·:·O·\·_·a·d·k·x·{·}·€·‡·”·—·š··¤·±·´·¶·¹·Æ·É·Ë·Î·Ú·ç·ê·í·ï¸d¸q¸†¸‰¸Œ¸¸’¸•¸˜¸›¸¸¡¸¤¸¹¸¼¸¾¸À¸Â¸Å¸È¸Ê¸Ì¸Ï¸Ñ¸Ó¸ğ¹¹3¹U¹y¹Ÿ¹Ã¹âººº'º*º-º/º8ºAºDºGºJºMºOº\ºaºdºgºlºoºrºtº—º´ºÁºÈºËºÎºÑºØºÛºŞºàºâ»»=»^»g»t»‡»‰»’»Ÿ»²»´»Á»Æ»É»Ë»Ğ»Ó»Õ»×»æ»ó»ø»ú»ı¼¼¼¼	¼¼2¼S¼V¼Y¼\¼_¼b¼e¼g¼j¼ƒ¼†¼‰¼Œ¼¼’¼”¼¼ª¼­¼°¼³¼¶¼¹¼¼¼¾¼Ë¼Î¼Ğ¼Ó¼è¼õ¼ø¼ú¼ı½
½½½½½½)½,½/½2½?½B½D½G½T½W½Y½\½h½u½x½{½}½ò½ÿ¾¾¾¾¾ ¾#¾&¾)¾,¾/¾2¾G¾J¾L¾N¾P¾S¾V¾X¾Z¾]¾_¾a¾~¾¾Á¾ã¿¿-¿Q¿p¿¿¬¿µ¿¸¿»¿½¿Æ¿Ï¿Ò¿Ô¿×¿Ù¿Û¿è¿í¿ğ¿ó¿ø¿û¿şÀ À#À@ÀMÀTÀWÀZÀ]ÀdÀgÀjÀlÀnÀ—ÀÉÀêÀóÁ ÁÁÁÁ+Á>Á@ÁMÁRÁUÁWÁ\Á_ÁaÁcÁrÁÁ„Á†Á‰ÁÁÁ“Á•Á¤ÁÅÁÈÁËÁÎÁÑÁÔÁ×ÁÙÁÜÁõÁøÁûÁşÂÂÂÂÂÂÂ"Â%Â(Â+Â.Â0Â=Â@ÂBÂEÂ]ÂjÂmÂoÂrÂ|Â‰ÂŒÂÂ‘ÂÂ¡Â¤Â§Â´Â·Â¹Â¼ÂÉÂÌÂÎÂÑÂİÂêÂíÂğÂòÃmÃzÃÃ’Ã•Ã˜Ã›ÃÃ¡Ã¤Ã§ÃªÃ­ÃÂÃÅÃÇÃÉÃËÃÎÃÑÃÔÃÖÃÙÃÛÃİÃúÄÄ=Ä_ÄƒÄ©ÄÍÄìÅÅ(Å1Å4Å7Å9ÅBÅKÅNÅQÅTÅWÅYÅfÅkÅnÅqÅvÅyÅ|Å~Å›Å¾ÅËÅĞÅÒÅÕÅÚÅÜÅßÅáÅğÅıÆÆÆ
ÆÆÆÆÆÆÆGÆyÆšÆ£Æ°ÆÃÆÅÆÎÆÛÆîÆğÆıÇÇÇÇÇÇÇÇ"Ç4ÇUÇXÇ[Ç^ÇaÇdÇgÇiÇlÇ…ÇˆÇ‹ÇÇ‘Ç”Ç–ÇŸÇ¬Ç¯Ç²ÇµÇ¸Ç»Ç¾ÇÀÇÍÇĞÇÒÇÕÇíÇúÇıÇÿÈÈÈÈÈÈ%È(È+È.È;È>È@ÈCÈPÈSÈUÈXÈdÈqÈtÈwÈyÈôÉÉÉÉÉÉ"É%É(É+É.É1É4ÉIÉLÉNÉPÉRÉUÉWÉZÉ\É_ÉaÉcÉ€ÉŸÉÃÉåÊ	Ê/ÊSÊrÊ‘Ê®Ê·ÊºÊ½Ê¿ÊÈÊÑÊÔÊ×ÊÚÊİÊßÊìÊñÊôÊ÷ÊüÊÿËËË'ËDËQËXË[Ë^ËaËhËkËnËpËrË›ËÍËîË÷ÌÌÌÌ"Ì/ÌBÌDÌQÌVÌXÌ[Ì`ÌbÌeÌgÌvÌƒÌˆÌŠÌÌ’Ì”Ì—Ì™Ì¨ÌÉÌêÌíÌğÌóÌöÌùÌüÌşÍÍÍÍ Í#Í&Í)Í,Í`ÍiÍnÍqÍtÍvÍƒÍ†Í‰ÍŒÍ”Í–Í£Í¦Í¨Í«Í·ÍÀÍÍÍÏÍÒÍÔÍûÎ%Î2Î5Î8Î;Î>Î@Î\ÎeÎhÎkÎmÎ‚Î„Î†Î‡ÎŠÎŒÎÁÎİÎşÏÏÏÏ
ÏÏÏÏÏ.Ï1Ï4Ï7Ï:Ï=Ï?ÏHÏUÏXÏ[Ï^ÏaÏdÏgÏiÏvÏyÏ{Ï~Ï•Ï¢Ï¥Ï§ÏªÏ·ÏºÏ¼Ï¿ÏÈÏÕÏØÏÛÏŞÏëÏîÏğÏóĞ ĞĞĞĞĞ!Ğ$Ğ'Ğ)Ğ¢Ğ¯ĞÄĞÇĞÊĞÍĞĞĞÓĞÖĞÙĞÜĞßĞâĞ÷ĞúĞüĞşÑ ÑÑÑÑ
ÑÑÑÑ.ÑMÑqÑ“Ñ·ÑİÒÒ Ò?Ò\ÒeÒhÒkÒmÒvÒÒÒ„Ò†Ò‰Ò‹Ò˜ÒÒ Ò£Ò¨Ò«Ò®Ò°ÒÓÒğÒıÓÓÓ
ÓÓÓÓÓÓÓGÓyÓšÓ£Ó°ÓÃÓÅÓÎÓÛÓîÓğÓıÔÔÔÔÔÔÔÔ"Ô/Ô4Ô6Ô9Ô>Ô@ÔCÔEÔTÔnÔÔ’Ô•Ô˜Ô›ÔÔ¡Ô£Ô¦Ô¿ÔÂÔÅÔÈÔËÔÎÔĞÔÙÔæÔéÔìÔïÔòÔõÔøÔúÕÕ
ÕÕÕ)Õ6Õ9Õ;Õ>ÕKÕNÕPÕSÕ`ÕcÕfÕiÕvÕyÕ{Õ~Õ‹ÕÕÕ“ÕŸÕ¬Õ¯Õ²Õ´Ö3Ö@ÖUÖXÖ[Ö^ÖaÖdÖgÖjÖmÖpÖsÖˆÖ‹ÖÖÖ‘Ö”Ö–Ö˜ÖšÖÖŸÖ¡Ö¾Öİ××#×G×m×‘×°×Ï×ì×õ×ø×û×ıØØØØØØØØ&Ø+Ø.Ø1Ø6Ø9Ø<Ø>ØaØ~Ø‹Ø’Ø•Ø˜Ø›Ø¢Ø¥Ø¨ØªØ¬ØÕÙÙ(Ù1Ù>ÙQÙSÙ\ÙiÙ|Ù~Ù‹ÙÙ’Ù•ÙšÙœÙÙ Ù¯Ù¼ÙÁÙÃÙÆÙËÙÍÙÏÙÑÙàÚÚ"Ú%Ú(Ú+Ú.Ú1Ú4Ú6Ú9ÚRÚUÚXÚ[Ú^ÚaÚcÚlÚyÚ|ÚÚ‚Ú…ÚˆÚ‹ÚÚšÚÚŸÚ¢Ú·ÚÄÚÇÚÊÚÍÚÒÚßÚâÚäÚçÚôÚ÷ÚúÚıÛ
ÛÛÛÛÛ"Û$Û'Û3Û@ÛCÛFÛHÛ½ÛÊÛßÛâÛåÛèÛëÛîÛñÛôÛ÷ÛúÛıÜÜÜÜÜÜÜ Ü#Ü%Ü(Ü*Ü,ÜIÜhÜŒÜ®ÜÒÜøİİ;İZİwİ€İƒİ†İˆİ‘İšİİ İ£İ¦İ¨İµİºİ½İÀİÅİÈİËİÍİğŞŞŞ!Ş$Ş'Ş*Ş1Ş4Ş7Ş9Ş;ŞdŞ–Ş·ŞÀŞÍŞàŞâŞëŞøßßßßß!ß$ß)ß+ß.ß0ß?ßLßQßSßVß[ß]ß`ßbßqßyßšßß ß£ß¦ß©ß¬ß®ß±ßÊßÍßĞßÓßÖßÙßÛßäßïßòßõßøßûßşà ààààà'à4à7à9à<à?àLàOàQàTàaàdàfàiàvàyà{à~àŠà—àšààŸááá&á)á,á/á2á5á8á;á>áAáDáYá\á^á`ábáeágáiákánápáráá®áÒáôââ>âbââ â½âÆâÉâÌâÎâ×âàâãâæâéâìâîâğâòâÿããã
ããããã4ãWãdãiãkãnãsãuãxãzã‰ã–ãã ã£ã¦ã­ã°ã³ãµã·ãàää3ä<äIä\ä^ägätä‡ä‰ä–ä›ää ä¥ä§äªä¬ä»äÕäöäùäüäÿåååå
åå&å)å,å/å2å5å7å@åKåNåQåTåWåZå\åiålåoårå‰å–å™å›åå£å°å³åµå¸åÅåÈåÊåÍåÚåİåßåâåîåûåşæææoæ|æ‘æ”æ—æšææ æ£æ¦æ©æ¬æ¯æÄæÇæÉæËæÍæĞæÓæÖæØæÛæİæßæüçç?çaç…ç«çÏçîèè*è3è6è9è;èDèMèPèSèVèYè[èhèmèpèsèxè{è~è€è£èÀèÍèÔè×èÚèİèäèçèêèìèîééIéjésé€é“é•éé«é¾éÀéÍéÒéÕé×éÜéßéáéãéòéÿêêê	êêêêê$ê9ê;êDêGêJêMêVêqêtêwê˜ê›êê¡ê¤ê§êªê¬ê¯êÈêËêÎêÑêÔê×êÙêâêíêğêóêöêùêüêşëëëëë ë#ë%ë(ë5ë8ë:ë=ëJëMëOëRë_ëbëdëgësë€ëƒë†ëˆë•ëªë­ë°ë³ë¶ë¹ë¼ë¿ëÂëÅëÈëİëàëâëäëæëéëëëíëïëòëôëöìì2ìVìxìœìÂìæíí$íAíJíMíPíRí[ídígíjímípíríí„í‡íŠíí’í•í—íºí×íäíëíîíñíôíûíşîîîî.î`îîŠî—îªî¬îµîÂîÕî×îäîéîëîîîóîõîøîúï	ïïïï ï%ï'ï*ï,ï;ïDï\ïaïyï‹ï¤ï§ïªï­ï°ï³ïµï¾ïÉïÌïÏïÒïÕïØïÚïçïêïìïïïüïÿğğğğğğğ&ğ)ğ+ğ.ğ;ğ>ğ@ğCğOğ\ğ_ğbğdğqğzğ}ğ€ğƒğ†ğğ’ğ•ğ—ğšğœğ¥ğ½ğÈğÒğßğèğëğîğñğôğığÿñññññ%ñBñeñ~ñ‹ññ‘ñ”ñ—ñ™ñ­ñ¶ñ»ñ¾ñÁñÄñÑñØñÛñŞñáñèñëñíñğñòñûòòòòòò!ò(ò+ò-ò0ò2ò<òIòZò]ò`òcòfòiòlòoòròƒò…ò‡ò‰ò‹òòò‘ò“ò•òÃòóó óOózóªóÜôô ô-ô@ôBôOôPôQ            ü              ôS

/== TheMealsApp.xcodeproj/project.xcworkspace/xcuserdata/gilangramadhan.xcuserdatad/UserInterfaceState.xcuserstate
bplist00Ô        
X$versionY$archiverT$topX$objects † _NSKeyedArchiverÑ  	UState€¯€      1 2 3 4 5 6 7 8 9 : ; < J K L M N O U [ \ b v w x y z { | } ~ ‚ ƒ ‡ ‹ Œ   ’ – œ  ¡ ¢ ¤ ¥ © ¯ ³ ´ À Á Â Ã Ä Å Æ Ê Ò Ó Ô Ü İ Ş ß à å è ì ô ø ù ı ş $+,-.45=>?BCEFNOPhijklmnopqrv}ƒ„Œ•–¢£§¨®¯²¸0459:>?CDHIMNRSWX\]abfgklpquvz{€„…‰Š“”˜™¢£§¨¬­±²¶·»¼ÀÁÅÆÊËÏĞÔÕÙÚŞßãäèéíîòó÷øüı $%)*./3489=>BCGHLMQRjklmnopqrstx€‡ˆ‘’š›³´µ¶·¸¹º»¼½ÁÈÉÊĞÑÙÚÛãäüışÿ 
 !"*+CDEFGHIJKLMQXY_`hijrs‹Œ‘’“”•™ ¡¢¨©±²³»¼ÔÕÖ×ØÙÚÛÜİŞâéïğøùú !"#$%)01289ABCKLdefghijklmnryz{‚Š‹Œ–—¯°±²³´µ¶·¸¹½ÄÅËÌÔÕÖŞß÷øùúûüışÿ &'?@ABCDEFGHIMTU[\defghpq‰Š‹Œ‘’“—Ÿ¥¦®¯°¸¹ÑÒÓÔÕÖ×ØÙÚÛßæçíîö÷øù !"#$%&'+234:;CDEFPQRSTUV^_wxyz{|}~€…Œ“”œŸ ¨©ÁÂÃÄÅÆÇÈÉÊËÏÖ×İŞæçèğñ	
$%-./089QRSTUVWXYZ[_fgmnvwxy‚š›œŸ ¡¢£¤¨¯°±·¸ÀÁÂÊËãäåæçèéêëìíñøşÿ							*	+	,	-	.	/	0	1	2	3	4	8	?	@	F	G	O	P	Q	Y	Z	r	s	t	u	v	w	x	y	z	{	|	€	‡	ˆ			—	˜	™	¡	¢	º	»	¼	½	¾	¿	À	Á	Â	Ã	Ä	È	Ï	Õ	Ö	Ş	ß	ç	è
 








	






$
%
&
.
/
G
H
I
J
K
L
M
N
O
P
Q
U
\
b
c
k
l
m
u
v



‘
’
“
”
•
–
—
˜
œ
£
¤
ª
«
³
´
µ
½
¾
Ö
×
Ø
Ù
Ú
Û
Ü
İ
Ş
ß
à
ä
ë
ì
ò
ó
û
ü
ı
ş
ÿ !"#$%&'()*.56<=EFGHIQRjklmnopqrstx€†‡‘™š²³´µ¶·¸¹º»¼ÀÇÍÎÖ×Øàáùúûüışÿ  !)*BCDEFGHIJKLPW]^fghpq‰Š‹Œ‘’“—¤¥­®¶·ÏĞÑÒÓÔÕÖ×ØÙİäåëìôõö÷ÿ  !"&-34<=>FGabcdefghijklpw}~†‡ˆ’“”•¶·¸¹º»¼½¾¿ÀÄËÑÒÚÛÜäåışÿ "#$,-EFGHIJKLMNOSZ`aijkstŒ‘’“”•–š¡¢¨©±²³´¼½×ØÙÚÛÜİŞßàáâæíóôüı	
+,-./0123459@FGOPQYZrstuvwxyz{|€‡–—˜ ¡¹º»¼½¾¿ÀÁÂÃÇÎÏÕÖŞßàèé	
%&'/0HIJKLMNOPQRV]cdlmnvw‘’“”•–—˜™¤¥«¬´µ¶¾¿×ØÙÚÛÜİŞßàáåìòóûüı !"#$%&'(,39:BCDLMefghijklmnosz€‰Š’“«¬­®¯°±²³´µ¹ÀÁÇÈĞÑÒÓÛÜôõö÷øùúûüış	
#$<=>?@ABCDEFJQRXYabckl„…†‡ˆ‰Š‹Œ’™Ÿ ¨©ª²³ËÌÍÎÏĞÑÒÓÔÕÙàáçèğñòúû!(./789:BC[\]^_`abcdeipvw€‰Š¢£¤¥¦§¨©ª«¬°·½¾ÆÇÈĞÑÛÜİŞäåéìòó÷û%&'()*+,-./0123456BCDEFGHIOYZ[\]ghlp€‚ƒ„‡ˆ‹•™œ©»¼½¾¿ÀÁÂÃÆÜİŞßàáâãäåõö÷øùúû	
!)*+789:;<=>JQTXYZ^nopqrstu‚ƒ„…‰Š‹œŸ ¡¢®¯°±²¸¹º»ÁÂÆÇÍÎÒÓÔÚÛßàáçèğñò !"&6789:;<=GHIJRbnov|}…†Š‹”˜œ¡¥¦§¿ÀÁÂÃÄÅÆÇÈÉÍÔÚÛãäìíîòû#'(,DEFGHIJKLMNRY_`hiqrs|ƒŒ‘•–š¢¦§«ÃÄÅÆÇÈÉÊËÌÍÑØŞßçèéñòû%,378<@DEIabcdefghijkov|}…†“— §­±µ¹º¾ÒÓÔÕÖ×ØÙÚŞßãäåéïğôõùúşÿ!"'-.3=>?@ABLMNR^_`abnopqrxy€…‘›œU$nullÓ      WNS.keysZNS.objectsV$class¢  €€¢  €€e_IDEWorkspaceDocument_$F98B5DD0-830B-4F1E-8A69-9F65C20AC7EEÓ     % 0ª        ! " # $€€€€€	€
€€€€ª & ' ( ) * + , - . /€€ËÚİï	
€+_BreakpointsActivated_DefaultEditorStatesForURLs_RunContextRecents\ActiveScheme_ActiveRunDestination_0LastCompletedPersistentSchemeBasedActivityReport_DocumentWindows_WindowArrangementDebugInfo_SelectedWindows_RecentEditorDocumentURLs	Ó    = C 0¥ > ? @ A B€€€€€¥ D E F G H€€3€I€}€†€+_(Xcode.IDEKit.EditorDocument.AssetCatalog_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project_:Xcode.IDEKit.EditorDocument.PegasusNonFileSystemSourceCode_'Xcode.IDEKit.EditorDocument.LogDocument_-Xcode.IDEKit.EditorDocument.PegasusSourceCodeÓ    P R 0¡ Q€¡ S€€+Ó V  W X Y ZWNS.base[NS.relative€ €€_ofile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Assets.xcassetsÒ ] ^ _ `Z$classnameX$classesUNSURL¢ _ aXNSObjectÓ    c l 0¨ d e f g h i j k€€€€€€ €!€"¨ m n o p q r s t€#€&€(€)€*€,€1€2€+_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-areaÒ    ¡ €€$€%_./assetSearchMeal.imagesetÒ ] ^ „ …\NSMutableSet£ „ † aUNSSetÒ   ˆ ¡ ‰€'€%_./assetSearchMeal.imageset_IBICCatalogOverviewController#@lÀ     Ó      0  €+Ò ] ^ “ ”_NSMutableDictionary£ “ • a\NSDictionaryÓ    — ™ 0¡ ˜€-¡ š€.€+_expandedItemIDsÒ     ¡ Ÿ€/€0Q.Ò ] ^ † £¢ † a^sourceListAreaÓ    ¦ § 0  €+Ó    ª ¬ 0¡ «€4¡ ­€6€+Ó V  W X Y ²€ €€5_ifile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeprojÓ    µ º 0¤ ¶ · ¸ ¹€7€8€9€:¤ » ¼ ½ ¾€;€<€=€H€+_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_(Xcode3ProjectEditor_Xcode3PackagesEditor_Xcode3PackagesEditor_Xcode3TargetEditorÒ   Ç É¡ È€>€GÔ Ë Ì Í  Î Ï Ğ ÑYselectionYtimestamp[documentURL€A€@€?€F_ifile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj#AÄ–\İo;Ó    Õ Ø 0¢ Ö ×€B€C¢ Ù Ú€D€E€+WProjectVEditor[TheMealsApp_Xcode3PackagesEditorÒ ] ^ á â_Xcode3ProjectDocumentLocation£ ã ä a_Xcode3ProjectDocumentLocation_DVTDocumentLocationÒ ] ^ æ çWNSArray¢ æ aÓ    é ê 0  €+Ó    í ğ 0¢ î ï€J€L¢ ñ ò€N€j€+Ó V  W X Y ÷€ €€K_}x-xcode-disassembly://stack_frame?launchSessionRef=7fc6d80ce450&stackFrameHash=3526088571801097364&stackFrameRef=7fc6e3be1200Ó V  W X Y ü€ €€M_}x-xcode-disassembly://stack_frame?launchSessionRef=7fc6d80d1430&stackFrameHash=3526088571801097364&stackFrameRef=7fc6c4876b10Ó    ÿ
 0ª 	€O€P€Q€R€S€T€U€V€W€Xª &€Y€]€]€]€^€f€\€]€g€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ! É¡"€Z€GÒ  % É¤&&€[€\€[€\€G Ó   /1 ¡0€_¡2€`€e_DeltaEditorLayoutExtensionÓ   69 ¢78€a€b¢:;€c€d€e[lineIndexes^documentLengthÒ  @ É €GÒ ] ^ •D¢ • a#¿ğ      Ó   GJ ¢HI€h€i¢:;€c€d€eUfolds^documentLengthÓ   Q\ 0ªRSTUVWXYZ[€k€l€m€n€o€p€q€r€s€tª]ae &€u€]€]€]€w€f€\€]€{€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  s É¡t€v€GÒ  w É¤&&€[€\€[€\€GÓ   ~€ ¡€x¡€y€e_DeltaEditorLayoutExtensionÓ   …ˆ ¢†7€z€a¢;:€d€c€e^documentLengthÓ   ‘ ¢H€h€|¢:;€c€d€e^documentLengthÓ   —š 0¢˜™€~€€¢›œ€‚€…€+Ó V  W X Y¡€ €€_2x-xcode-log://70021531-02B3-4560-BC7E-8EEBD4B2389CÓ V  W X Y¦€ €€_2x-xcode-log://ACC57B49-B56F-4A2A-BF4F-6A5CD2C698B1Ó   ©« 0¡ª€ƒ¡¬€„€+_SelectedDocumentLocationsÒ  ° É €GÓ   ³µ 0¡ª€ƒ¡¬€„€+Ó   ¹ô 0¯:º»¼½¾¿ÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏĞÑÒÓÔÕÖ×ØÙÚÛÜİŞßàáâãäåæçèéêëìíîïğñòó€‡€‰€‹€€€‘€“€•€—€™€›€€Ÿ€¡€£€¥€§€©€«€­€¯€±€³€µ€·€¹€»€½€¿€Á€Ã€Å€Ç€É€Ë€Í€Ï€Ñ€Ó€Õ€×€Ù€Û€İ€ß€á€ã€å€ç€é€ë€í€ï€ñ€ó€õ€÷€ù¯:õö÷øùúûüışÿ 	
 !"#$%&'()*+,-.€û';Pfz¨½Òéş3J_tŠ ´ÉŞñ.E\q…›¯ÂØì.BXp„˜­ÁÕêş%;Pey£·€+Ó V  W X Y3€ €€ˆ_‚file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/View/SearchRow.swiftÓ V  W X Y8€ €€Š_…file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swiftÓ V  W X Y=€ €€Œ_…file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/CategoryEntity.swiftÓ V  W X YB€ €€_Šfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swiftÓ V  W X YG€ €€_Šfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swiftÓ V  W X YL€ €€’_}file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/File.swiftÓ V  W X YQ€ €€”_xfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swiftÓ V  W X YV€ €€–_†file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteRow.swiftÓ V  W X Y[€ €€˜_†file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/CategoryMapper.swiftÓ V  W X Y`€ €€š_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swiftÓ V  W X Ye€ €€œ_‰file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swiftÓ V  W X Yj€ €€_‹file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extension/CustomeError+Ext.swiftÓ V  W X Yo€ €€ _‚file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swiftÓ V  W X Yt€ €€¢_‚file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/View/CategoryRow.swiftÓ V  W X Yy€ €€¤_wfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/DI/Injection.swiftÓ V  W X Y~€ €€¦_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swiftÓ V  W X Yƒ€ €€¨_~file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swiftÓ V  W X Yˆ€ €€ª_€file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/MealRow.swiftÓ V  W X Y€ €€¬_ƒfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swiftÓ V  W X Y’€ €€®_„file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extension/Color+Ext.swiftÓ V  W X Y—€ €€°_‡file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swiftÓ V  W X Yœ€ €€²_}file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/TabItem.swiftÓ V  W X Y¡€ €€´_‡file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swiftÓ V  W X Y¦€ €€¶_†file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataStore.swiftÓ V  W X Y«€ €€¸_ƒfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swiftÓ V  W X Y°€ €€º_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/LocaleDataStore.swiftÓ V  W X Yµ€ €€¼_‘file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swiftÓ V  W X Yº€ €€¾_€file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swiftÓ V  W X Y¿€ €€À_‰file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Meal/Presenter/MealPresenter.swiftÓ V  W X YÄ€ €€Â_‘file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/DetailMeal/Presenter/DetailPresenter.swiftÓ V  W X YÉ€ €€Ä_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoryResponse.swiftÓ V  W X YÎ€ €€Æ_‹file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swiftÓ V  W X YÓ€ €€È_Œfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swiftÓ V  W X YØ€ €€Ê_‡file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swiftÓ V  W X Yİ€ €€Ì_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swiftÓ V  W X Yâ€ €€Î_qfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/ContentView.swiftÓ V  W X Yç€ €€Ğ_ˆfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/IngredientMapper.swiftÓ V  W X Yì€ €€Ò_€file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/CustomIcon.swiftÓ V  W X Yñ€ €€Ô_ˆfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swiftÓ V  W X Yö€ €€Ö_ufile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/App/ContentView.swiftÓ V  W X Yû€ €€Ø_ƒfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/a209-ios-expert-modularization/TheMealsApp/TheMealsApp/ContentView.swiftÓ V  W X Y € €€Ú_ˆfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swiftÓ V  W X Y€ €€Ü_~file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/BlurView.swiftÓ V  W X Y
€ €€Ş_ˆfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swiftÓ V  W X Y€ €€à_ƒfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swiftÓ V  W X Y€ €€â_–file:///Users/gilangramadhan/Library/Developer/Xcode/DerivedData/TheMealsApp-dxiufbyyxhculcgugbkseletdnte/SourcePackages/checkouts/Alamofire/README.mdÓ V  W X Y€ €€ä_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/MealsResponse.swiftÓ V  W X Y€ €€æ_tfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/TheMealsAppApp.swiftÓ V  W X Y#€ €€è_…file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swiftÓ V  W X Y(€ €€ê_’file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoriesResponse.swiftÓ V  W X Y-€ €€ì_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Meal/View/MealView.swiftÓ V  W X Y2€ €€î_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swiftÓ V  W X Y7€ €€ğ_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swiftÓ V  W X Y<€ €€ò_‡file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swiftÓ V  W X YA€ €€ô_Œfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swiftÓ V  W X YF€ €€ö_‡file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swiftÓ V  W X YK€ €€ø_‡file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swiftÓ V  W X YP€ €€ú_Œfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swiftÓ   S^ 0ªTUVWXYZ[\]€ü€ı€ş€ÿ ª_cg &€]€]€]
€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  u É¡v€GÒ  y É¤z{z{		€G"0Ó   ‚„ ¡ƒ¡…€e_DeltaEditorLayoutExtensionÓ   ‰Œ ¢Š7€a¢:€c€e^documentLengthºÓ   “– ¢H•€h¢:€c€e^documentLengthÓ   œ§ 0ªŸ ¡¢£¤¥¦ª¨¬° &€]€]€] €f€\€]%€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ¾ É¡¿€GÒ  Â É¤ÃÄÃÄ€GÓ   ËÍ ¡Ì!¡Î"€e_DeltaEditorLayoutExtensionÓ   ÒÕ ¢Ó7#€a¢Ö¬$€„€e^documentLengthøÓ   Üß ¢HŞ€h&¢¬Ö€„$€e^documentLengthÓ   åğ 0ªæçèéêëìíîï()*+,-./01ªñõù &2€]€]€]4€f€\€]9€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡3€GÒ   É¤€\€\€\€\€GÓ    ¡5¡6€e_DeltaEditorLayoutExtensionÓ    ¢7€a7¢¬€„8€e^documentLength'Ó   #& ¢H%€h:¢¬€„8€e^documentLengthÓ   ,7 0ª-./0123456<=>?@ABCDEª8<@ &F€]€]€]I€f€\€]N€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  N É¡OG€GÒ  R É¤SÄSÄHH€GÓ   Z\ ¡[J¡]K€e_DeltaEditorLayoutExtensionÓ   ad ¢7c€aL¢¬f€„M€e^documentLengthÓ   kn ¢Hm€hO¢¬f€„M€e^documentLengthÓ   t 0ªuvwxyz{|}~QRSTUVWXYZª€„ˆ &[€]€]€]_€f€\€]d€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  – É¡—\€GÒ  š É¤›œ›œ]^]^€GÓ   £¥ ¡¤`¡¦a€e_DeltaEditorLayoutExtensionÓ   ª­ ¢7¬€ab¢¬¯€„c€e^documentLengthÏÓ   ´· ¢H¶€he¢¬¯€„c€e^documentLengthÓ   ½È 0ª¾¿ÀÁÂÃÄÅÆÇghijklmnopªÉÍÑ &q€]€]€]s€f€\€]x€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ß É¡àr€GÒ  ã É¤€\€\€\€\€GÓ   êì ¡ët¡íu€e_DeltaEditorLayoutExtensionÓ   ñô ¢ò7v€a¢õ¬w€„€e^documentLengthgÓ   ûş ¢Hı€hy¢¬õ€„w€e^documentLengthÓ    0ª	
{|}~€‚ƒ„ª &…€]€]€]‰€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  & É¡'†€GÒ  * É¤+,+,‡ˆ‡ˆ€GÓ   35 ¡4Š¡6‹€e_DeltaEditorLayoutExtensionÓ   := ¢7<€aŒ¢:?€c€e^documentLengthmÓ   DG ¢HF€h¢:?€c€e^documentLengthÓ   MX 0ªNOPQRSTUVW‘’“”•–—˜™šªY]^_a &›€]€]€]Ÿ¤¥€]¦€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  o É¡pœ€GÒ  s É¤tv€\€\€G%&Ó   |~ ¡} ¡¡€e_DeltaEditorLayoutExtensionÓ   ƒ† ¢7…€a¢¢¬ˆ€„£€e^documentLengthÀ#@      +Ó   ’ ¢H‘€h§¢¬ˆ€„£€e^documentLengthÓ   ˜£ 0ª™š›œŸ ¡¢©ª«¬­®¯°±²ª¤¨¬ &³€]€]€]¶€f€\€]»€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  º É¡»´€GÒ  ¾ É¤¿›µ€\]€\€GÓ   ÆÈ ¡Ç·¡É¸€e_DeltaEditorLayoutExtensionÓ   ÍĞ ¢7Ï€a¹¢¬Ò€„º€e^documentLengthşÓ   ×Ú ¢HÙ€h¼¢¬Ò€„º€e^documentLengthÓ   àë 0ªáâãäåæçèéê¾¿ÀÁÂÃÄÅÆÇªìğô &È€]€]€]Ë€f€\€]Ğ€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡É€GÒ   É¤ÊÊÊÊ€GÓ    ¡Ì¡Í€e_DeltaEditorLayoutExtensionÓ    ¢7Î€a¢¬Ï€„€e^documentLengthÓ   " ¢H!€hÑ¢¬€„Ï€e^documentLengthÓ   (3 0ª)*+,-./012ÓÔÕÖ×ØÙÚÛÜª489:< &İ€]€]€]àåæ€]ç€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  J É¡KŞ€GÒ  N É¤{P{P	ß	ß€GÓ   VX ¡Wá¡Yâ€e_DeltaEditorLayoutExtensionÓ   ]` ¢7_€aã¢¬b€„ä€e^documentLength#@*      Ó   il ¢Hk€hè¢¬b€„ä€e^documentLengthÓ   r} 0ªstuvwxyz{|êëìíîïğñòóª~‚† &ô€]€]€]÷€f€\€]ü€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ” É¡•õ€GÒ  ˜ É¤™Ä™Äöö€G#Ó    ¢ ¡¡ø¡£ù€e_DeltaEditorLayoutExtensionÓ   §ª ¢7©€aú¢¬¬€„û€e^documentLength³Ó   ±´ ¢H³€hı¢¬¬€„û€e^documentLengthÓ   ºÅ 0ª»¼½¾¿ÀÁÂÃÄÿ ªÆÊ9ÌÎ &	€]€]€]å€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ü É¡İ
€GÒ  à É¤áá€\€\€G¯Ó   èê ¡é¡ë€e_DeltaEditorLayoutExtensionÓ   ïò ¢7ñ€a¢¬ô€„€e^documentLength€jÓ   úı ¢Hü€h¢¬ô€„€e^documentLengthÓ    0«	
« & €]€]€]$)*0€]1€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ( É¡)!€GÒ  , É¤S/0H€\"#€G DÓ   57 ¡6%¡8&€e_DeltaEditorLayoutExtensionÓ   <? ¢=7'€a¢@¬(€„€e^documentLengtht#@&      Ó   GK 0£HIJ+,-£LN.€]/€+_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_XcodePreviews.PinnedPreview#?ğ      PÓ   WZ ¢HY€h2¢¬@€„(€e^documentLengthÓ   `k 0ªabcdefghij456789:;<=ªlpqrt &>€]€]€]AFG€]H€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ‚ É¡ƒ?€GÒ  † É¤‡‡@€\@€\€GÓ    ¡B¡‘C€e_DeltaEditorLayoutExtensionÓ   •˜ ¢7—€aD¢¬š€„E€e^documentLengthÉ#@      Ó   ¡¤ ¢H£€hI¢¬š€„E€e^documentLengthÓ   ªµ 0ª«¬­®¯°±²³´KLMNOPQRSTª¶º¾ &U€]€]€]X€f€\€]]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ì É¡ÍV€GÒ  Ğ É¤Ñ¿ÑÃWµW€G	Ó   ØÚ ¡ÙY¡ÛZ€e_DeltaEditorLayoutExtensionÓ   ßâ ¢à7[€a¢ã¬\€„€e^documentLength½Ó   éì ¢Hë€h^¢¬ã€„\€e^documentLengthÓ   òı 0ªóôõö÷øùúûü`abcdefghiªş &j€]€]€]lq€\€]r€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡k€GÒ   É¤Ã_Ã_¥¥€GÓ   ! ¡ m¡"n€e_DeltaEditorLayoutExtensionÓ   &) ¢'7o€a¢*¬p€„€e^documentLengthD#        Ó   14 ¢H3€hs¢¬*€„p€e^documentLengthÓ   :E 0ª;<=>?@ABCDuvwxyz{|}~ªFJKÄN &€]€]€]‚‡€]ˆ€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  \ É¡]€€GÒ  ` É¤aa€\€\€GÓ   hj ¡iƒ¡k„€e_DeltaEditorLayoutExtensionÓ   or ¢p7…€a¢s¬†€„€e^documentLength#@      Ó   z} ¢H|€h‰¢¬s€„†€e^documentLengthÓ   ƒ 0ª„…†‡ˆ‰Š‹Œ‹Œ‘’“”ª“— &•€]€]€]™€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ¥ É¡¦–€GÒ  © É¤ª¬r—€\˜G€G8>Ó   ²´ ¡³š¡µ›€e_DeltaEditorLayoutExtensionÓ   ¹¼ ¢7»€aœ¢¬¾€„€e^documentLengthÕÓ   ÃÆ ¢HÅ€hŸ¢¬¾€„€e^documentLengthÓ   Ì× 0ªÍÎÏĞÑÒÓÔÕÖ¡¢£¤¥¦§¨©ªªØÜà &«€]€]€]­€f€\€]²€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  î É¡ï¬€GÒ  ò É¤¿œ¿œµ^µ^€GÓ   ùû ¡ú®¡ü¯€e_DeltaEditorLayoutExtensionÓ   	 	 ¢7	€a°¢¬	€„±€e^documentLengthÓ   	
	 ¢H	€h³¢¬	€„±€e^documentLengthÓ   		 0ª										µ¶·¸¹º»¼½¾ª		#	' &¿€]€]€]Â€f€\€]Ç€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  	5 É¡	6À€GÒ  	9 É¤	:	:Á€\Á€\€GÓ   	A	C ¡	BÃ¡	DÄ€e_DeltaEditorLayoutExtensionÓ   	H	K ¢	I7Å€a¢	L¬Æ€„€e^documentLengthgÓ   	R	U ¢H	T€hÈ¢¬	L€„Æ€e^documentLengthÓ   	[	f 0ª	\	]	^	_	`	a	b	c	d	eÊËÌÍÎÏĞÑÒÓª	g	k	o &Ô€]€]€]×€f€\€]Ü€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  	} É¡	~Õ€GÒ  	 É¤œ	ƒœ	ƒ^Ö^Ö€GÓ   	‰	‹ ¡	ŠØ¡	ŒÙ€e_DeltaEditorLayoutExtensionÓ   		“ ¢	‘7Ú€a¢	”:Û€c€e^documentLengthÓ   	š	 ¢H	œ€hİ¢:	”€cÛ€e^documentLengthÓ   	£	® 0ª	¤	¥	¦	§	¨	©	ª	«	¬	­ßàáâãäåæçèª	¯	³	· &é€]€]€]ë€f€\€]ï€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  	Å É¡	Æê€GÒ  	É É¤	:	:Á€\Á€\€GÓ   	Ğ	Ò ¡	Ñì¡	Óí€e_DeltaEditorLayoutExtensionÓ   	×	Ú ¢	Ø7î€a¢	L¬Æ€„€e^documentLengthÓ   	à	ã ¢H	â€hğ¢¬	L€„Æ€e^documentLengthÓ   	é	ô 0ª	ê	ë	ì	í	î	ï	ğ	ñ	ò	óòóôõö÷øùúûª	õ	ù	ı &ü€]€]€]ş€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
 É¡
ı€GÒ  
 É¤›:›:]æ]æ€GÓ   

 ¡
ÿ¡
 €e_DeltaEditorLayoutExtensionÓ   

  ¢
7€a¢
!¬€„€e^documentLengthóÓ   
'
* ¢H
)€h¢¬
!€„€e^documentLengthÓ   
0
; 0ª
1
2
3
4
5
6
7
8
9
:	
ª
<
@
D &€]€]€]€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
R É¡
S€GÒ  
V É¤	:	:Á€\Á€\€GÓ   
]
_ ¡
^¡
`€e_DeltaEditorLayoutExtensionÓ   
d
g ¢
e7€a¢
h¬€„€e^documentLengthÓ   
n
q ¢H
p€h¢¬
h€„€e^documentLengthÓ   
w
‚ 0ª
x
y
z
{
|
}
~

€
 !"#ª
ƒ
‡
‹ &$€]€]€]'€f€\€],€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
™ É¡
š%€GÒ  
 É¤
Ä
Ä&&€GÓ   
¥
§ ¡
¦(¡
¨)€e_DeltaEditorLayoutExtensionÓ   
¬
¯ ¢
­7*€a¢
°¬+€„€e^documentLength…Ó   
¶
¹ ¢H
¸€h-¢¬
°€„+€e^documentLengthÓ   
¿
Ê 0ª
À
Á
Â
Ã
Ä
Å
Æ
Ç
È
É/012345678ª
Ë
Ï
Ğ
Ñ
Ó &9€]€]€]<AB€]C€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  
á É¡
â:€GÒ  
å É¤
æP
æP;ß;ß€G/Ó   
í
ï ¡
î=¡
ğ>€e_DeltaEditorLayoutExtensionÓ   
ô
÷ ¢
õ7?€a¢
ø¬@€„€e^documentLength#@       Ó     ¢H€hD¢¬
ø€„@€e^documentLengthÓ   	 0ª
FGHIJKLMNOª &P€]€]€]SXY€]Z€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  + É¡,Q€GÒ  / É¤Ñ2ÄW€\R€G'Ó   79 ¡8T¡:U€e_DeltaEditorLayoutExtensionÓ   >A ¢?7V€a¢B¬W€„€e^documentLengthŠ#@      Ó   JM ¢HL€h[¢¬B€„W€e^documentLengthÓ   S^ 0ªTUVWXYZ[\]]^_`abcdefª_cLrg &g€]€]€]j.G€]o€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  u É¡vh€GÒ  y É¤Ñ|W€\i€\€G
Ó   ƒ ¡‚k¡„l€e_DeltaEditorLayoutExtensionÓ   ˆ‹ ¢‰7m€a¢Œ¬n€„€e^documentLengthßÓ   ’• ¢H”€hp¢¬Œ€„n€e^documentLengthÓ   ›¦ 0ªœŸ ¡¢£¤¥rstuvwxyz{ª§«¯ &|€]€]€]~€f€\€]ƒ€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ½ É¡¾}€GÒ  Á É¤¿¿¿:µµµæ€GÓ   ÈÊ ¡É¡Ë€€e_DeltaEditorLayoutExtensionÓ   ÏÒ ¢Ğ7€a¢Ó¬‚€„€e^documentLengthËÓ   ÙÜ ¢HÛ€h„¢¬Ó€„‚€e^documentLengthÓ   âí 0ªãäåæçèéêëì†‡ˆ‰Š‹Œªîòôö &€]€]€]“X˜€]™€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡‘€GÒ   É¤	Ä	Ä’’€GÓ    ¡”¡•€e_DeltaEditorLayoutExtensionÓ    ¢7–€a¢¬—€„€e^documentLengthÓ   "% ¢H$€hš¢¬€„—€e^documentLengthÓ   +6 0ª,-./012345œŸ ¡¢£¤¥ª7;? &¦€]€]€]¨€f€\€]­€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  M É¡N§€GÒ  Q É¤

Ê&Ê&€GÓ   XZ ¡Y©¡[ª€e_DeltaEditorLayoutExtensionÓ   _b ¢7a€a«¢¬d€„¬€e^documentLengthkÓ   il ¢Hk€h®¢¬d€„¬€e^documentLengthÓ   r} 0ªstuvwxyz{|°±²³´µ¶·¸¹ª~‚† &º€]€]€]¼q€\€]À€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ” É¡•»€GÒ  ˜ É¤€\€\€\€\€GÓ   Ÿ¡ ¡ ½¡¢¾€e_DeltaEditorLayoutExtensionÓ   ¦© ¢§7¿€a¢¬¬û€„€e^documentLengthÓ   ¯² ¢H±€hÁ¢¬¬€„û€e^documentLengthÓ   ¸Ã 0ª¹º»¼½¾¿ÀÁÂÃÄÅÆÇÈÉÊËÌªÄÈÉ¿Ì &Í€]€]€]ĞÕµ€]Ö€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ú É¡ÛÎ€GÒ  Ş É¤ßßÏ€\Ï€\€G1Ó   æè ¡çÑ¡éÒ€e_DeltaEditorLayoutExtensionÓ   íğ ¢î7Ó€a¢ñ¬Ô€„€e^documentLengths#@"      Ó   øû ¢Hú€h×¢¬ñ€„Ô€e^documentLengthÓ    0ª	
ÙÚÛÜİŞßàáâªL, &ã€]€]€]å.ˆ€]ê€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  # É¡$ä€GÒ  ' É¤vav/"€GÓ   .0 ¡/æ¡1ç€e_DeltaEditorLayoutExtensionÓ   58 ¢67è€a¢9:é€c€e^documentLengthÓ   ?B ¢HA€hë¢:9€cé€e^documentLengthÓ   HT 0«IJKLMNOPQRSíîïğñòóôõö÷«UY[^ &ø€]€]€]úqÿ€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  m É¡nù€GÒ  q É¤€\€\€\€\€GÓ   xz ¡yû¡{ü€e_DeltaEditorLayoutExtensionÓ   ‚ ¢€7ı€a¢ƒ¬ş€„€e^documentLengthæÓ   ‰ 0£Š‹Œ £LN.€]/€+_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_XcodePreviews.PinnedPreviewÓ   –™ ¢H˜€h¢¬ƒ€„ş€e^documentLengthÓ   Ÿª 0ª ¡¢£¤¥¦§¨©	
ª«¯³ &€]€]€]€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Á É¡Â€GÒ  Å É¤
Ñ›
Ñ›B]B]€GÓ   ÌÎ ¡Í¡Ï€e_DeltaEditorLayoutExtensionÓ   ÓÖ ¢Ô7€a¢×¬€„€e^documentLengthôÓ   İà ¢Hß€h¢¬×€„€e^documentLengthÓ   æñ 0ªçèéêëìíîïğ !"#ªòöú &$€]€]€]'€f€\€],€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡	%€GÒ   É¤rr&G&G€GÓ    ¡(¡)€e_DeltaEditorLayoutExtensionÓ    ¢7€a*¢¬ €„+€e^documentLengthÌÓ   %( ¢H'€h-¢¬ €„+€e^documentLengthÓ   .9 0ª/012345678/012345678ª:>
Ğ&B &9€]€]€];A€[€]@€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  P É¡Q:€GÒ  T É¤Ã›Ã›]]€GÓ   [] ¡\<¡^=€e_DeltaEditorLayoutExtensionÓ   be ¢7d€a>¢¬g€„?€e^documentLengthıÓ   lo ¢Hn€hA¢¬g€„?€e^documentLengthÓ   u€ 0ªvwxyz{|}~CDEFGHIJKLª…†‰ &M€]€]€]PU€\€]V€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  — É¡˜N€GÒ  › É¤SSHOHO€GÓ   £¥ ¡¤Q¡¦R€e_DeltaEditorLayoutExtensionÓ   ª­ ¢«7S€a¢®:T€c€e^documentLength6#@       Ó   µ¸ ¢H·€hW¢:®€cT€e^documentLengthÓ   ¾Ê 0«¿ÀÁÂÃÄÅÆÇÈÉYZ[\]^_`abc«ËÏÑÔ &d€]€]€]fqj€\€]n€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ã É¡äe€GÒ  ç É¤€\€\€\€\€GÓ   îğ ¡ïg¡ñh€e_DeltaEditorLayoutExtensionÓ   õø ¢ö7i€a¢ƒ¬ş€„€e^documentLengthÓ   ş 0£ÿ klm£NL/€].€+_XcodePreviews.PinnedPreview_XcodePreviews.ShowViewBounds_XcodePreviews.ZoomÓ    ¢H€ho¢¬ƒ€„ş€e^documentLengthÓ    0ªqrstuvwxyzª $( &{€]€]€]}€f€\€]‚€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  6 É¡7|€GÒ  : É¤¿:¿:µæµæ€GÓ   AC ¡B~¡D€e_DeltaEditorLayoutExtensionÓ   HK ¢I7€€a¢L¬€„€e^documentLengthUÓ   RU ¢HT€hƒ¢¬L€„€e^documentLengthÓ   [f 0ª\]^_`abcde…†‡ˆ‰Š‹Œªgko &€]€]€]‘€f€\€]–€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  } É¡~€GÒ   É¤€\€\€\€\€GÓ   ˆŠ ¡‰’¡‹“€e_DeltaEditorLayoutExtensionÓ   ’ ¢7‘€a”¢¬”€„•€e^documentLengthkÓ   ™œ ¢H›€h—¢¬”€„•€e^documentLengthÓ   ¢­ 0ª£¤¥¦§¨©ª«¬™š›œŸ ¡¢ª®²¶ &£€]€]€]¦€f€\€]«€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ä É¡Å¤€GÒ  È É¤ÉÉ¥€\¥€\€G,Ó   ĞÒ ¡Ñ§¡Ó¨€e_DeltaEditorLayoutExtensionÓ   ×Ú ¢Ø7©€a¢Û¬ª€„€e^documentLengthFÓ   áä ¢Hã€h¬¢¬Û€„ª€e^documentLengthÓ   êõ 0ªëìíîïğñòóô®¯°±²³´µ¶·ªöú
Ğş &¸€]€]€]ºA0€]¿€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡¹€GÒ   É¤+œ+œ‡^‡^€GÓ    ¡»¡¼€e_DeltaEditorLayoutExtensionÓ   ! ¢7 €a½¢:#€c¾€e^documentLength©Ó   (+ ¢H*€hÀ¢:#€c¾€e^documentLengthÓ   1< 0ª23456789:;ÂÃÄÅÆÇÈÉÊËª=AE &Ì€]€]€]Îq€\€]Ó€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  S É¡TÍ€GÒ  W É¤€\€\€\€\€GÓ   ^` ¡_Ï¡aĞ€e_DeltaEditorLayoutExtensionÓ   eh ¢7g€aÑ¢¬j€„Ò€e^documentLength>óÓ   or ¢Hq€hÔ¢¬j€„Ò€e^documentLengthÓ   xƒ 0ªyz{|}~€‚Ö×ØÙÚÛÜİŞßª„ˆŒ &à€]€]€]ã€f€\€]è€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  š É¡›á€GÒ   É¤ŸŸâ€\â€\€GzÓ   ¦¨ ¡§ä¡©å€e_DeltaEditorLayoutExtensionÓ   ­° ¢7¯€aæ¢¬²€„ç€e^documentLengthèÓ   ·º ¢H¹€hé¢¬²€„ç€e^documentLengthÓ   ÀË 0ªÁÂÃÄÅÆÇÈÉÊëìíîïğñòóôªÌĞÔ &õ€]€]€]÷€f€\€]ü€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  â É¡ãö€GÒ  æ É¤€\€\€\€\€GÓ   íï ¡îø¡ğù€e_DeltaEditorLayoutExtensionÓ   ô÷ ¢õ7ú€a¢ø¬û€„€e^documentLengthïÓ   ş ¢H €hı¢¬ø€„û€e^documentLengthÓ    0ª	
ÿ ª &	€]€]€]€f€\€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ) É¡*
€GÒ  - É¤Y€\Y€\€GÓ   46 ¡5¡7€e_DeltaEditorLayoutExtensionÓ   ;> ¢<7€a¢?¬€„€e^documentLengthäÓ   EH ¢HG€h¢¬?€„€e^documentLengthÓ   NY 0ªOPQRSTUVWXªZ^
Ğôb &€]€]€]A˜€]#€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  p É¡q€GÒ  t É¤	Ä	Ä’’€GÓ   {} ¡| ¡~!€e_DeltaEditorLayoutExtensionÓ   ‚… ¢ƒ7"€a¢¬—€„€e^documentLengthÓ   ‹ ¢H€h$¢¬€„—€e^documentLengthÓ   ”Ÿ 0ª•–—˜™š›œ&'()*+,-./ª ¤^¦¨ &0€]€]€]3¤8€]9€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ¶ É¡·1€GÒ  º É¤»	:»	:2Á2Á€GyÓ   ÂÄ ¡Ã4¡Å5€e_DeltaEditorLayoutExtensionÓ   ÉÌ ¢7Ë€a6¢¬Î€„7€e^documentLengthôAÓ   Ô× ¢HÖ€h:¢¬Î€„7€e^documentLengthÓ   İè 0ªŞßàáâãäåæç<=>?@ABCDEªéíñ &F€]€]€]I€f€\€]N€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ÿ É¡ G€GÒ   É¤	:	:HÁHÁ€G)Ó    ¡J¡K€e_DeltaEditorLayoutExtensionÓ    ¢7€aL¢:€cM€e^documentLength¬Ó    ¢H€hO¢:€cM€e^documentLengthÓ   %0 0ª&'()*+,-./QRSTUVWXYZª15
Ğ	9 &[€]€]€]^A’€]c€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  G É¡H\€GÒ  K É¤LPLP]ß]ß€G2Ó   SU ¡T_¡V`€e_DeltaEditorLayoutExtensionÓ   Z] ¢7\€aa¢¬_€„b€e^documentLengthCÓ   dg ¢Hf€hd¢¬_€„b€e^documentLengthÓ   mx 0ªnopqrstuvwfghijklmnoªy} &p€]€]€]r€f€\€]w€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡q€GÒ  “ É¤
‡
‡&@&@€GÓ   šœ ¡›s¡t€e_DeltaEditorLayoutExtensionÓ   ¡¤ ¢¢7u€a¢¥¬v€„€e^documentLengthÓ   «® ¢H­€hx¢¬¥€„v€e^documentLengthÓ   ´¿ 0ªµ¶·¸¹º»¼½¾z{|}~€‚ƒªÀÄÈ &„€]€]€]‡€f€\€]Œ€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ö É¡×…€GÒ  Ú É¤|Ü|Üi†i†€GÓ   âä ¡ãˆ¡å‰€e_DeltaEditorLayoutExtensionÓ   éì ¢7ë€aŠ¢¬î€„‹€e^documentLength„Ó   óö ¢Hõ€h¢¬î€„‹€e^documentLengthÓ   ü 0ªışÿ ‘’“”•–—˜ª^ &™€]€]€]›¤ €]¡€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ   É¡š€GÒ  " É¤ÉÉ0¥0¥€GÓ   )+ ¡*œ¡,€e_DeltaEditorLayoutExtensionÓ   03 ¢17€a¢4¬Ÿ€„€e^documentLength!ü
Ó   ;> ¢H=€h¢¢¬4€„Ÿ€e^documentLengthÓ   DO 0ªEFGHIJKLMN¤¥¦§¨©ª«¬­ªPTX &®€]€]€]°€f€\€]µ€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  f É¡g¯€GÒ  j É¤ÃÃ€\€\€GÓ   qs ¡r±¡t²€e_DeltaEditorLayoutExtensionÓ   x{ ¢7z€a³¢¬}€„´€e^documentLengthãÓ   ‚… ¢H„€h¶¢¬}€„´€e^documentLengthÓ   ‹– 0ªŒ‘’“”•¸¹º»¼½¾¿ÀÁª—›Ÿ &Â€]€]€]Ä€f€\€]É€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  ­ É¡®Ã€GÒ  ± É¤SÄSÄHH€GÓ   ¸º ¡¹Å¡»Æ€e_DeltaEditorLayoutExtensionÓ   ¿Â ¢7Á€aÇ¢¬Ä€„È€e^documentLengthÓ   ÉÌ ¢HË€hÊ¢¬Ä€„È€e^documentLengthÓ   ÒÖ £ÓÔÕÌÍÎ£×ØÙÏÓ×€e_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKeyÓ   ßá 0¡àĞ¡âÑ€+[TheMealsAppÒæ çèWNS.time#AÄšşG"6ÒÒ ] ^êëVNSDate¢ê aÓ   íï 0¡îÔ¡ğÕ€+[TheMealsAppÒ ôõöYNS.stringÖ_;76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9_iphonesimulator_x86_64Ò ] ^øù_NSMutableString£øú aXNSStringÓ   üş 0¡ıØ¡ÿÙ€+_;76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9_iphonesimulator_x86_64Òæ è#AÄšşG[-<ÒÓ    ¡Û¡	Ü€e]IDENameString[TheMealsAppÓ    ªŞßàáâãäåæçª & & !"#€è€€]éêëìíî€eZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9Vx86_64_iphonesimulator16.1ViPhoneZiPhone15,2Ó   7< 0¤89:;ğñòó¤=>?@ôõö÷€+_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildV14B47bêÒ  JN£KLMøıÓ   PT 0£QRSùúû£
ĞV=Aüô€+_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ Ó   ^b 0£QRSùúû£Lde.şÿ€+c  %  Òi jkWNS.dataObplist00Ô
X$versionY$archiverT$topX$objects † _NSKeyedArchiverÑ	Troot€¯)*0:;<#=AIJKLMSWX\_U$nullÓXNSStringV$class\NSAttributes€€€YSucceededÓWNS.keysZNS.objects¡€¡€€VNSFontÖ !"#$%&'(VNSSizeXNSfFlags\NSDescriptorZNSHasWidthVNSName#@&      ˆ€€€_.AppleSystemUIFontBoldÓ+,-./_NSFontDescriptorOptions_NSFontDescriptorAttributes€€€€Ó15£234€	€
€£678€€€€_NSFontSizeAttribute_ NSCTFontFeatureSettingsAttribute_NSCTFontUIUsageAttributeÒ>@¡?€€ÓBE¢CD€€¢FG€€€_CTFeatureSelectorIdentifier_CTFeatureTypeIdentifier ÒNOPQZ$classnameX$classes\NSDictionary¢PRXNSObjectÒNOTU^NSMutableArray£TVRWNSArray_CTFontBoldUsageÒNOYZ_NSFontDescriptor¢[R_NSFontDescriptorÒNO]^VNSFont¢]RÒNO`a_NSAttributedString¢bR_NSAttributedString    $ ) 2 7 I L Q S o u | … Œ ™ ›  Ÿ © ° ¸ Ã Å Ç É Ë Í Ô á è ñ ş	!#<C]z|ƒŠ’”˜šœ ¶Ùôùûıÿ	4NPRWbkx{„‰˜œ¤¶»ÎÑäéğóø             c              % Ò ] ^mn]NSMutableData£mo aVNSDataÓ   qx 0¦QstSvwùû¦^Ä{|ÄÄ¤€+_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyleÒæ …è#AÄ›¼ö¬Ò^Today at 16.08Ò ] ^‰Š^NSMutableArray£‰ æ aÒ  Œ É¡ €€GÓ   ’ 0¡ €¡“€+Ò  –N¡ €Ò  šN Ò   Éª QæáÜ ïÀ îºÏí€€ß€Õ€Ë€L€“€J€‡€±€í€GÓ   ª² 0§«¬­®¯°±§ &´¶°¸€€]€]€+_IDEWindowToolbarIsVisible^IDEWindowFrame_&IDEWindowTabBarWasVisibleWithSingleTab_(IDEWorkspaceWindow_NSRestorableStateData_IDEActiveWorkspaceTabController_>IDEWorkspaceTabController_933AE80D-5978-481C-9637-73C7D8929EFE_IDEWindowTabBarIsVisible_{{1679, 30}, {1322, 1025}}Òi ÄkO4bplist00Ô0X$versionY$archiverT$topX$objects † _NSKeyedArchiverß	
 !"#$%'()+,-./[NSStyleMask]NSWindowFrame_NSFirstResponder_NSManagedFrameIsUserDefined_&NSWindowManagementPersistentIdentifier__NSWindowLayouts^NSWindowNumber__NSWindowRestorer_NSUnmanagedWindowFrame2WNSTitleWNSIsKey__NSWindowResizeGenerationXNSTabIdx_NSWindowWorkspaceIDXNSIsMain[NSClassNameVNSUIID_NSUIPreviousFrameZNSTabBrVisZNSTabGrpID€€€	€€y€€ €	 €	€€€(€'¯)123456789@AGU[\]`defjklpqryz{~ƒ„…Š‹Œ‘’“–—U$null_$B24954C0-1B4E-4D32-BD8B-EF32C5F4CAA9_#1679 30 1322 1025 1680 0 1920 1055 U_NS:8o T h e M e a l s A p p      A s s e t s . x c a s s e t s_IDEWorkspaceWindow_$A370BEB6-8D1B-4CD5-83E3-A4BB8517370B_IDEDocumentControllerÓ:;<=>?V$classXwindowID_persistentIdentifier€
´€	V_NS:92ÒBCDEZ$classnameX$classes_NSPersistentUIEncodedReference¢DFXNSObjectÓHI:JOTWNS.keysZNS.objects¤KLMN€€€€¤PQRS€€€ €#€&ÓVW:XYZ_NSScreenLayoutUUIDString_NSScreenLayoutSize€€€_$F2B5A86A-9901-8E02-AF53-20B8D78B142E\{1920, 1080}ÒBC^_^NSScreenLayout¢^FÓVW:abZ€€€_$85A2805F-F0DD-0306-8C8C-4F1AD0C2EEC5\{1920, 1080}ÓVW:ghZ€€€_$6FAF45A8-9789-0F06-22FA-018734C0F4CF\{1680, 1050}ÓVW:mnZ€€€_$42591195-F398-F010-A125-104918E150C1\{1920, 1080}Ôst:uv'wx_NSWindowLayoutScreenLayoutFrame_NSWindowLayoutResizeGeneration_NSWindowLayoutWindowFrame€€€_{{1679, 30}, {1322, 1025}}_{{1680, 0}, {1920, 1055}}ÒBC|}^NSWindowLayout¢|FÔst:u€w‚€€€_{{1680, -62}, {1920, 1055}}_{{1680, -62}, {1920, 1055}}Ôst:u†‡w‰€"€€!_{{0, 0}, {1680, 1025}}_{{0, 0}, {1680, 1025}}Ôst:uw€%€€$_{{2505, 163}, {1078, 635}}_{{1680, -62}, {1920, 1055}}ÒBC”•\NSDictionary¢”F^0x60000184f2a0_0 0 1680 1025 0 0 1680 1025     $ ) 2 7 I t €  ¡ ¿ è û
8@HdmƒŒ˜Ÿ³¾ÉÌÎĞÑÓÕØÚÜŞßáãåæèêìíï!Hnt±Æí3579@EPYz}†• ¥§©«­²´¶¸º¼ÃŞóõ÷ù -2ADKMOQx…Œ’¹ÆÍÏÑÓú2Soqsu’®³ÂÅÎĞÒÔÖô!#<U^`bdfƒ¡¦³¶Å             ˜              ä Ó   ÇÑ 0©ÈÉÊËÌÍÎÏĞ !© &ÓÔÕÖ×Ø &Ú€"rswxz€}€+_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorAreaÓ   æí 0¦çèéêëì#$%&'(¦îïğñòó)5GYZa€+_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Find_Xcode.IDEKit.Navigator.Debug_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigatorÓ   ü 0¤ışÿ *+,-¤./0.€+_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKeyÓ      €e_IDENavigatorModeSolitaryÓ    ¡1¡2€e_IDENavigatorModeSolitaryÒ _codablePlistRepresentation43OÍbplist00Ô     ”•œYitemState_lastAccessedDate^scrollPosition]selectedItems¯h      # ' * . 3 7 ; ? B F L P T W [ _ d h k o r v z ~ ‚ † ‰  ‘ • ˜ œ   ¤ ¨ ¬ ¯ ³ ¶ º ¿ Ã Æ Ê Î Ò Õ Ù İ á ä è ë ï ò ö ù ı "%),037:>AEIMPTX\`dgknruy|€ƒ†‰ŒÒ   	 TpathYindexHint¤ 
   [TheMealsApp[TheMealsAppTCoreUUtilsÒ    _lastAccessedDate^expansionState3AÄ›±]WÍ Ò    ¥     [TheMealsAppVMapperÒ    _lastAccessedDate^expansionState3AÄ›±K´Ò    ¥     ! "[TheMealsApp[TheMealsAppTDataVRemoteÒ $ % & _lastAccessedDate^expansionState3AÄ›±]-ÓÒ   ( ¥ )   ! "[TheMealsAppÒ + , - _lastAccessedDate^expansionState3AÄ›±K´Ò   / ¤ 0  1 2[TheMealsAppVModuleZDetailMealÒ 4 5 6 _lastAccessedDate^expansionState3AÄ›±K´Ò   8 ¢ 9 :[TheMealsApp[TheMealsAppÒ < = > _lastAccessedDate^expansionState3AÄ›±]	JÒ   @ ¡ A[TheMealsAppÒ C D E _lastAccessedDate^expansionState3AÄ›±\úÒ   G ¥ H I 1 J K[TheMealsApp[TheMealsAppVDetailVRouterÒ M N O _lastAccessedDate^expansionState3AÄ›±K´-Ò   Q ¥ R I 1 S K[TheMealsAppVSearchÒ U V O _lastAccessedDate^expansionStateÒ   X ¥ Y    Z[TheMealsAppYExtensionÒ \ ] ^ _lastAccessedDate^expansionState3AÄ›±K´>Ò   ` ¥ a   b c[TheMealsAppVDomainUModelÒ e f g _lastAccessedDate^expansionState3AÄ›±]B>Ò   i £ j  [TheMealsAppÒ l m n _lastAccessedDate^expansionState3AÄ›±]Ò   p ¥ q   b c[TheMealsAppÒ s t u _lastAccessedDate^expansionState3AÄ›±K´NÒ   w ¤ x I 1 y[TheMealsAppTMealÒ { | } _lastAccessedDate^expansionState3AÄ›±]£wÒ    £ €  1[TheMealsAppÒ ƒ „ … _lastAccessedDate^expansionState3AÄ›±]h”Ò   ‡ ¤ ˆ  1 J[TheMealsAppÒ Š ‹ Œ _lastAccessedDate^expansionState3AÄ›±K´WÒ    ¥  I   [TheMealsAppTViewÒ ’ “ ” _lastAccessedDate^expansionState3AÄ›±]^Ò   – ¥ — I 1 S [TheMealsAppÒ ™ š › _lastAccessedDate^expansionState3AÄ›±K´_Ò    ¥   1 Ÿ [TheMealsAppTHomeÒ ¡ ¢ £ _lastAccessedDate^expansionState3AÄ›±]Ò   ¥ ¥ ¦ I 1 § [TheMealsAppXFavoriteÒ © ª « _lastAccessedDate^expansionState3AÄ›±]?Ò   ­ ¥ ® I 1 J [TheMealsAppÒ ° ± ² _lastAccessedDate^expansionState3AÄ›±]wâÒ   ´ ¥ µ I 1 S [TheMealsAppÒ · ¸ ¹ _lastAccessedDate^expansionState3AÄ›±]»®Ò   » ¥ ¼ ½  ! ¾[TheMealsApp[TheMealsAppVLocaleÒ À Á Â _lastAccessedDate^expansionState3AÄ›±]!«Ò   Ä ¤ Å I 1 §[TheMealsAppÒ Ç È É _lastAccessedDate^expansionState3AÄ›±]~³Ò   Ë ¥ Ì  1 Ÿ Í[TheMealsAppYPresenterÒ Ï Ğ Ñ _lastAccessedDate^expansionState3AÄ›±]–Ò   Ó ¤ Ô I 1 J[TheMealsAppÒ Ö × Ø _lastAccessedDate^expansionState3AÄ›±]o Ò   Ú ¤ Û   Ü[TheMealsAppRDIÒ Ş ß à _lastAccessedDate^expansionState3AÄ›±]53Ò   â ¥ ã  1 2 Í[TheMealsAppÒ å æ ç _lastAccessedDate^expansionState3AÄ›±K´‘Ò   é ¥ ê   1 J Í[TheMealsAppÒ ì í î _lastAccessedDate^expansionState3AÄ›±]pçÒ   ğ ¥ ñ I 1 § Í[TheMealsAppÒ ó ô õ _lastAccessedDate^expansionState3AÄ›±]€¡Ò   ÷ ¥ ø I 1 y Í[TheMealsAppÒ ú û ü _lastAccessedDate^expansionState3AÄ›±]¥Ò   ş ¥ ÿ I 1 S Í[TheMealsAppÒ _lastAccessedDate^expansionState3AÄ›±]´pÒ   ¤ I 1 Ÿ[TheMealsAppÒ	
 _lastAccessedDate^expansionState3AÄ›±]”Ò   ¦ ½  ! ¾[TheMealsAppVEntityÒ _lastAccessedDate^expansionState3AÄ›±K´«Ò   ¥ ½  [TheMealsAppZExtensionsÒ _lastAccessedDate^expansionStateÒ   £ [TheMealsAppSAppÒ ! _lastAccessedDate^expansionState3AÄ›±]oÒ  # ¦$ ½  ! ¾[TheMealsAppÒ&'( _lastAccessedDate^expansionState3AÄ›±](RÒ  * ¤+   ![TheMealsAppÒ-./ _lastAccessedDate^expansionState3AÄ›±]ÉÒ  1 ¤2   b[TheMealsAppÒ456 _lastAccessedDate^expansionState3AÄ›±];ÉÒ  8 ¥9 I 1 § K[TheMealsAppÒ;<= _lastAccessedDate^expansionState3AÄ›±]†]Ò  ? ¥@  1 Ÿ K[TheMealsAppÒBCD _lastAccessedDate^expansionState3AÄ›±	ĞÒ  FH¤G I 1 S[TheMealsAppÒJKL _lastAccessedDate^expansionState3AÄ›±]²‰Ò  N ¤O  1 Ÿ[TheMealsAppÒQRS _lastAccessedDate^expansionState3AÄ›±K´ÌÒ  U ¦V   ! "W[TheMealsAppXResponseÒYZ[ _lastAccessedDate^expansionState3AÄ›±]2ğÒ  ] ¥^   b_[TheMealsAppWUseCaseÒabc _lastAccessedDate^expansionState3AÄ›±]KùÒ  e ¥f  1 J [TheMealsAppÒhij _lastAccessedDate^expansionState3AÄ›±K´ÕÒ  l ¥m I 1 y [TheMealsAppÒopq _lastAccessedDate^expansionState3AÄ›±]ª7Ò  s ¤t   ![TheMealsAppÒvwx _lastAccessedDate^expansionState3AÄ›±K´İÒ  z ¥{ I 1 § Í[TheMealsAppÒ}~ _lastAccessedDate^expansionState3AÄ›±K´åÒ   ¥‚ I 1 S Í[TheMealsAppÒ„… _lastAccessedDate^expansionStateÒ  ‡ ¥ˆ I 1 J Í[TheMealsAppÒŠ‹ _lastAccessedDate^expansionStateÒ   ¥   [TheMealsAppWNetworkÒ‘’“ _lastAccessedDate^expansionState3AÄ›±K´î3AÄ›%Ôé¢–™¢—˜#        #@€     ¢š›#@p     #@     ¡Ò   £Ÿ ¡[TheMealsApp[TheMealsApp_Assets.xcassets   # 6 E S&/4>GS_djluˆ— ¢«¶ÂÉËÔçöÿ+07@Sbkt‹”§¶¿ÈÑİäïø#,1=IRet}†‰•±ÀÉÒİéõü.7@KW^gz‰’©³¼ÏŞçğû0?HQXdm€˜¡¬¸ÁÔãìõş
+:CLS_aj}Œ•§³¼ÏŞçğû(7@IT`i|‹”¨´¹ÂÕäíö2AJS^js†•§²¾ÇÚéòû				%	.	A	P	Y	b	k	w	€	“	¢	«	´	¿	Ë	Õ	Ş	ñ
 
	


'
0
C
R
[
d
m
y
|
…
˜
§
°
¹
Ä
Ğ
Ù
ì
û$-@OXalx”£¬µÀÌÕè÷ 	 )<KT]fr{¦¯¼ÈÏØëú#.7JYbiuy‚•¤­¶ÃÏØëú!*=LU^gs|§°»ÇĞãòû$7FOXamox‹š£¬µÁÊİìõş )<KT]ht|…˜§°¹ÄĞÙìû$-@OXajv’¡ª³¾ÊÓæõş':IR]ir…”¨´¼ÅØçğùş#,/8?KW            ¢              iÒ ] ^_&ExplorableOutlineViewArchivableUIState£  a_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier_Ó   "% 0¢#$67¢&N8/€+_queryParametersController]filterPatternÓ   ,1 0¤-./09:;<¤2345=>?@€+_presentingScopeChooser_lastEasyToInitiateQueryClass[queryAction_querySpecification _IDEBatchFindTextQueryTfindÖ?@ABC DE- XHIYanchoringYqueryTermYmatchCaseZnamedScopeZqueryClassEA€ DFÔ KLMN X,P_explicitDisplayStringXtermTypeTtextC€ BÒ ôõSÖPÒ ] ^UV_IDEBatchFindQueryTerm¢W a_IDEBatchFindQueryTerm_IDEBatchFindTextQueryTnoneÒ ] ^[\_IDEBatchFindQuerySpecification¢] a_IDEBatchFindQuerySpecificationÓ   _f 0¦`abcdeHIJKLM¦gh &kNO€\€X€]€+_IDEStackCompressionValue_IDEDebugTransientStates_DBGNavigatorContentMode_IDEShowOnlyRunningBlocks^IDEVisibleRect_IDEShowOnlyInterestingContentÓ   v{ 0¤wxyzPQRS¤|} &TV€W€+_IDEDebugExpandedItems_#IDECurrentLaunchSessionReferenceKey_1IDEHaveInitiallyExpandedCPUDebuggingChildrenState_IDEDebugSelectedNavigableItemsÒ  † ¡‡U€%[TheMealsApp\7fc6c48ac7f0Ò  ŒN _{{0, 0}, {257, 920}}_ Xcode.IDEKit.Navigator.WorkspaceÓ   ‘– 0¤’“”•[\]^¤—éñš_%Y`€+_"Xcode.IDEKit.NavigatorGroup.Issues_!Xcode.IDEKit.NavigatorGroup.Debug_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_#Xcode.IDENoticesKit.NoticeNavigator_Xcode.IDEKit.Navigator.FindÓ   £¨ 0¤¤¥¦§bcde¤©ª«¬fijn€+_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKeyÓ   ³µ ¡´g¡¶h€e_IDENavigatorModeSolitaryOnbplist00Ô
X$versionY$archiverT$topX$objects † _NSKeyedArchiverÑ	Troot€©!$'+,U$nullÕV$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiers€ €€€ÒZNS.objects €ÒZ$classnameX$classesWNSArray¢ XNSObjectÒ"# €Ò%&^NSMutableArray£% Ò(#¡)€€_IDEFilterIdentifier_NoticeErrorÒ-.__DVTFilterExpressionStateValue¤/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u ‚ š ® Ï Ñ Ó Õ × Ù Ş é ê ì ñ ü!&59>@BDfkŒ‘²Î             2              ê_IDENavigatorModeSolitaryÓ   ¼¾ ¡½k¡¿l€e_IDENavigatorModeSolitaryÒ ÃÅ_codablePlistRepresentation4mO¨bplist00ÔYitemState_lastAccessedDate^scrollPosition]selectedItems 3AÄ› ğZ|[¢
¢		#        ¢#@‚ğ     #@°      .=KLUX[dgpy                            zÓ   ÈÊ ¡Éo¡Ëp€e_IDENavigatorModeSolitaryÒ ÏÑ_codablePlistRepresentation4qO¨bplist00ÔYitemState_lastAccessedDate^scrollPosition]selectedItems 3AÄ› ğZ||¢
¢		#        ¢#@p     #@ŒÀ      .=KLUX[dgpy                            z#@p@     Ó   Õ× 0¡Öt¡Øu€+_!userPreferredCategoryExtensionIDsÒ  ÜN¡İv_#Xcode.IDEKit.InspectorCategory.File#@p     Ó   âä 0¡ãy¡ &€€+_ShowsOnlyVisibleViewObjectsÓ   éì 0¢êë{|¢€]€]€+_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspaceÓ   óÿ 0«ôõö÷øùúûüış~€‚ƒ„…†‡ˆ« 	
‰€\‹ŒS]ij€+_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ EditorMultipleSplitPrimaryLayout_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_DebuggerSplitView_ DefaultPersistentRepresentations_IDEDefaultDebugArea^MaximizedStateÓ É,_NSIndexPathLength_NSIndexPathValueŠÒ ] ^[NSIndexPath¢ a[NSIndexPathZOpenInTabs _Layout_LeftToRightÒ  #N¡$Ó   '. 0¦()*+,-‘’“”•¦/01/3–—2–R€]€+ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments Ó   >B 0£?@A˜™š£Crr›GG€+_TabsAsHistoryItems_SelectedTabIndex_DynamicTabIndexÒ  K É¥LMNOPœÆé
€GØSTUV WXYZ[\]^_áa_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifier¡¯±ÅÄ€Õ°Öcde fghijkl_DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifier©Ÿ«®€\_/Xcode.IDENavigableItemDomain.WorkspaceStructureÒ  p É¤qrst £¥§€GÓwx ZÄ{ZIdentifierUIndex¡¢_ContentView.swiftÒ ] ^~_IDEArchivableStringIndexPair¢€ a_IDEArchivableStringIndexPairÓwx ‚{¤€\¢SAppÓwx ‡{¦€\¢[TheMealsAppÓwx Œ{¨€\¢[TheMealsAppÓ Í  Ìö’ X€Öª€ Ò ] ^•–_DVTDocumentLocation¢— a_DVTDocumentLocationÒ ™š›ZpathString­¬_b/Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeprojÒ ] ^Ÿ[DVTFilePath¢  a[DVTFilePathÒ ] ^¢£_(IDENavigableItemArchivableRepresentation¢¤ a_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCodeÓ   ¨³ 0ª©ª«¬­®¯°±²²³´µ¶·¸¹º»ª´¸†¼ &¼€]€]€]¾U€\€]Â€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Ê É¡Ë½€GÒ  Î É¤SSHOHO€GÓ   Õ× ¡Ö¿¡ØÀ€e_DeltaEditorLayoutExtensionÓ   Üß ¢İ7Á€a¢®¬T€„€e^documentLengthÓ   åè ¢Hç€hÃ¢¬®€„T€e^documentLengthTbodyÒ ] ^ïğ_IDEEditorHistoryItem¢ñ a_IDEEditorHistoryItemØSTUV WXYó[õö^øÜaÊ¯ÕÇÅè€Ë°Öcde fgüıjklÔÈ«®€\Ò   É¦	ÉËÍÏÑÒ€GÓwx ó{Ê€\¢_SearchPresenter.swiftÓwx {Ì€\¢YPresenterÓwx &{Î€[¢VSearchÓwx g{ĞN¢VModuleÓwx ‡{¦€\¢Ówx ${Ó€\¢[TheMealsAppÓ Í  Ìİ’ X€Ìª€ Ó   -8 0ª./01234567Ö×ØÙÚÛÜİŞßª9=L,A &à€]€]€]â.ˆ€]æ€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  O É¡Pá€GÒ  S É¤vav/"€GÓ   Z\ ¡[ã¡]ä€e_DeltaEditorLayoutExtensionÓ   ad ¢b7å€a¢9¬é€„€e^documentLengthÓ   jm ¢Hl€hç¢¬9€„é€e^documentLength\searchMeal()ØSTUV WXYt[vw^_æaí¯öêÅÄ€ß°Öcde fg}~jklõë«®€\Ò  „ É¦…†‡ˆ‰Šìîğñòó€GÓwx t{í€\¢_SearchView.swiftÓwx ’g{ïN¢TViewÓwx &{Î€[¢Ówx g{ĞN¢Ówx ‡{¦€\¢Ówx £{ô€\¢[TheMealsAppÓ Í  Ì’ X€àª€ Ó   ¬· 0ª­®¯°±²³´µ¶÷øùúûüışÿ ª¸¼½rÀ &€]€]€]G€]€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  Î É¡Ï€GÒ  Ò É¤+œ+œ‡^‡^€GÓ   ÙÛ ¡Ú¡Ü€e_DeltaEditorLayoutExtensionÓ   àã ¢á7€a¢#¬¾€„€e^documentLength#@(      Ó   êí ¢Hì€h	¢¬#€„¾€e^documentLengthØSTUV WXYóô Xö^t Qú€ Åí€Öcde fgüıjkl«®€\Ò   É£€GÓwx ór{G¢_Assets.xcassetsÓwx ‡{¦€\¢Ówx {€\¢[TheMealsAppÓ Í  Ì Z’ X€ª€ _com.apple.dt.assetcatalog_(Xcode.IDEKit.EditorDocument.AssetCatalogØSTUV WXY[ ^_Àa¯ÅÄ€“°Öcde fg&'jkl«®€\Ò  - É¤./01€GÓwx {€\¢_TheMealsAppApp.swiftÓwx ‚{¤€\¢Ówx ‡{¦€\¢Ówx A{€\¢[TheMealsAppÓ Í  ÌQ’ X€”ª€ Ó   JU 0ªKLMNOPQRST !"#$%&'()ªVZ^ &*€]€]€],€f€\€]0€€+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLinesÒ  l É¡m+€GÒ  p É¤+,+,‡ˆ‡ˆ€GÓ   wy ¡x-¡z.€e_DeltaEditorLayoutExtensionÓ   ~ ¢7€€a/¢¬?€„€e^documentLengthÓ   ‡Š ¢H‰€h1¢¬?€„€e^documentLengthÒ  N¡‘3Ò” •–_currentEditorHistoryItem4QØSTUV WXYóôš›^ Qú<5ÅP€Öcde fg¡¢jkl;6«®€\Ò  ¨ É£©ª«789€GÓwx ór{G¢Ówx ‡{¦€\¢Ówx ¶{:€\¢[TheMealsAppÓ Í  Ì Z’ X€ª€ Ó   ¿È 0¨ÀÁÂÃÄÅÆÇ=>?@ABCD¨ÉÊË pÍÎÏĞEGI€)JKNO€+_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-areaÒ  Û ¡ÜF€%_./assetSearchMeal.imagesetÒ  à ¡áH€%_./assetSearchMeal.imageset_IBICCatalogOverviewControllerÓ   æç 0  €+Ó   êì 0¡ëL¡íM€+_expandedItemIDsÒ  ñ  ¡ Ÿ€/€0^sourceListAreaÓ   ö÷ 0  €+_assetSearchMealÒ ] ^ûü_IDEEditorHistoryStack¢ı a_IDEEditorHistoryStack_ItemKind_EditorÖcde fg jklÄZT«®Ò   É¤	
UVWX€G_assetSearchMeal.imagesetÓwx ór{G¢Ówx ‡{¦€\¢Ówx {Y€\¢[TheMealsAppÔ  Ì Í X Z \relativePath\€ €[_./assetSearchMeal.imagesetÒ ] ^#$_IBICCatalogDocumentLocation£%& a_IBICCatalogDocumentLocation_DVTDocumentLocationÓ   (* 0¡)^¡+_€+_DVTSplitViewItemsÒ  /N¢01`fÓ   48 £567abc£9 &;d€e€e]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeYIDEEditor#@…h     Ó   CG £567abc£H &Jg€h€e_IDEDebuggerArea#@u@     Ó   OP 0  €+Ó   SX 0¤TUVWklmn¤YZÄ\otv€+XLeftViewYRightViewZLayoutMode_IDESplitViewDebugAreaÓ   ch 0¤defgpqrs¤¬ &€]€\€„€€+_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsTypeÓ   su 0¡tu¡€\€+_ConsoleFilterModeÓ   z| 0¡{w¡}x€+_DVTSplitViewItemsÒ  N¢‚ƒy|Ó   †Š £567abc£‹ &z€{€eXLeftView#@{p     Ó   ’– £567abc£— &™}€~€eYRightView#@v°               "   ,   1   :   ?   Q   V   \   ^  b  h  u  }  ˆ    ”  –  ˜    Ÿ  ¢  ¤  »  â  ï        
                -  /  1  4  7  :  =  @  C  F  I  K  b    “     ·  ê  ü    +  F  G  T  _  a  c  e  g  i  t  v  x  z  |  ~  €  «  å  "  L  |  ‰  Œ    ‘  “  •  ¢  ª  ¶  ¸  º  ¼  .  7  B  K  Q  V  _  l  }      ƒ  …  ‡  ‰  ‹         ¢  ¤  ¦  ¨  ª  ¬  ®  °  Ê  Ö  é      %  7  E  N  Q  S  U  r  {  ˆ    •    ¡  £  ¥  Â  â  ë  ø  ù  ú  ü      "  /  <  ?  A  D  F  H  Z  c  f  h  j  l  u  z  ‰  –  —  ˜  š  §  ª  ¬  ¯  ±  ³  À  Â  Ä  Æ  2  ?  H  J  L  N  P  Y  [  ]  _  a  c  “  Â  ñ    3  H  Q  T  V  X  i  s  }  ‰  ‹      ‘  ı            !  #  %  '  /  6  B  Y  b  ‚  ‰  ©  ¿  È  Ğ  Õ  â  ã  ä  æ  ó  ø  ú  ü                  š  §  ©  «  ­  -  :  O  Q  S  U  W  Y  [  ]  _  a  c  x  z  |  ~  €  ‚  „  †  ˆ  Š  Œ    «  Ê  î    4  Z  ~    ¼  Ù  â  å  ç  é  ò  û  ı  ÿ          	  
          !  #  @  M  R  T  V  [  ]  _  a  m  |  …  †  ˆ  ‹  ”  ™  ¢  ¯  ´  ¶  ¸  ½  ¿  Á  Ã  É  Ø  å  ú  ü  ş             
      #  %  '  )  +  -  /  1  3  5  7  9  V  u  ™  »  ß    )  H  g  „      ’  ”    ¦  ¨  ª  ¬  ®  °  ½  À  Â  Å  Ç  É  æ  ó  ø  ú  ü            #  (  *  ,  1  3  5  7  F  S  X  Z  \  a  c  e  g  t  v  x  z  ¯  ¼  ¾  À  Â  ÷         	            ,   5   6   8   E   H   J   M   O   Q   ^   Õ   ×   Ù   Û   İ   ß   á   ã   å   ç   é   ë   í   ï   ñ   ó   õ   ÷   ù   û   ı   ÿ  !  !  !  !  !	  !  !  !  !  !  !  !  !  !  !  !  !!  !#  !%  !'  !)  !+  !-  !/  !1  !3  !5  !7  !9  !;  !=  !?  !A  !C  !E  !G  !I  !À  !Â  !Å  !È  !Ë  !Î  !Ñ  !Ô  !×  !Ú  !İ  !à  !ã  !æ  !é  !ì  !ï  !ò  !õ  !ø  !û  !ş  "  "  "  "
  "  "  "  "  "  "  "  ""  "%  "(  "+  ".  "1  "4  "7  ":  "=  "@  "C  "F  "I  "L  "O  "R  "U  "X  "[  "^  "a  "d  "g  "j  "m  "o  "|  "~  "€  "‚  #  #  #  #  #  #¢  #¯  #±  #³  #µ  $=  $J  $L  $N  $P  $İ  $ê  $ì  $î  $ğ  %}  %Š  %Œ  %  %  &  &  &  &!  &#  &  &«  &­  &¯  &±  ':  'G  'I  'K  'M  'Ö  'ã  'å  'ç  'é  (z  (‡  (‰  (‹  (  )  )&  )(  )*  ),  )º  )Ç  )É  )Ë  )Í  *R  *_  *a  *c  *e  *ê  *÷  *ù  *û  *ı  +w  +„  +†  +ˆ  +Š  ,  ,  ,  ,  ,!  ,¢  ,¯  ,±  ,³  ,µ  -8  -E  -G  -I  -K  -Ñ  -Ş  -à  -â  -ä  .k  .x  .z  .|  .~  /  /  /  /  /  /›  /¨  /ª  /¬  /®  08  0E  0G  0I  0K  0Ô  0á  0ã  0å  0ç  1m  1z  1|  1~  1€  2  2  2  2!  2#  2·  2Ä  2Æ  2È  2Ê  3M  3Z  3\  3^  3`  3ì  3ù  3û  3ı  3ÿ  4“  4   4¢  4¤  4¦  59  5F  5H  5J  5L  5Ú  5ç  5é  5ë  5í  6|  6‰  6‹  6  6  7  7&  7(  7*  7,  7¼  7É  7Ë  7Í  7Ï  8C  8P  8R  8T  8V  8á  8î  8ğ  8ò  8ô  9w  9„  9†  9ˆ  9Š  :  :"  :$  :&  :(  :   :­  :¯  :±  :³  ;9  ;F  ;H  ;J  ;L  ;×  ;ä  ;æ  ;è  ;ê  <k  <x  <z  <|  <~  =	  =  =  =  =  =¢  =¯  =±  =³  =µ  >N  >[  >]  >_  >a  >ñ  >ş  ?   ?  ?  ?{  ?ˆ  ?Š  ?Œ  ?  @  @#  @%  @'  @)  @¾  @Ë  @Í  @Ï  @Ñ  AS  A`  Ab  Ad  Af  Aè  Aõ  A÷  Aù  Aû  B‹  B˜  Bš  Bœ  B  C(  C5  C7  C9  C;  CÊ  C×  CÙ  CÛ  Cİ  Dg  Dt  Dv  Dx  Dz  E  E  E  E  E  E¦  E³  EÈ  EÊ  EÌ  EÎ  EĞ  EÓ  EÖ  EÙ  EÜ  Eß  Eâ  E÷  Eú  Eü  Eş  F   F  F  F  F	  F  F  F  F-  FL  Fp  F’  F¶  FÜ  G   G  G>  G[  Gd  Gg  Gj  Gl  Gu  G~  G  G„  G‡  GŠ  GŒ  G  G  G  G   G£  G¦  G©  G«  GÈ  GÕ  GÚ  Gİ  Gß  Gä  Gç  Gé  Gë  Gú  Gı  H
  H  H  H  H  H  H  H   H/  H<  HQ  HT  HW  HZ  H]  H`  Hc  Hf  Hi  Hl  Ho  H„  H‡  H‰  H‹  H  H  H’  H”  H–  H™  H›  H  Hº  HÙ  Hı  I  IC  Ii  I  I¬  IË  Iè  Iñ  Iô  I÷  Iù  J  J  J  J  J  J  J  J  J  J*  J-  J0  J3  J6  J8  JU  Jb  Jg  Jj  Jl  Jq  Jt  Jv  Jx  J‡  J‰  J–  J›  J  J   J¥  J§  Jª  J¬  J»  JÈ  Jİ  Jà  Jã  Jæ  Jé  Jì  Jï  Jò  Jõ  Jø  Jû  K  K  K  K  K  K  K  K   K"  K%  K'  K)  KF  Ke  K‰  K«  KÏ  Kõ  L  L8  LW  Lt  L}  L€  Lƒ  L…  L  L—  L™  L›  L  LŸ  L¡  L®  L±  L´  L·  Lº  L¼  LÙ  Læ  Lë  Lí  Lğ  Lõ  L÷  Lú  Lü  M  M  M  M   M"  M%  M*  M,  M/  M1  M@  MM  Mb  Me  Mh  Mk  Mn  Mq  Mt  Mw  Mz  M}  M€  M•  M˜  Mš  Mœ  M  M¡  M£  M¥  M§  Mª  M¬  M®  MË  Mê  N  N0  NT  Nz  N  N½  NÜ  Nù  O  O  O  O
  O  O  O  O"  O%  O(  O*  O,  O9  O<  O?  OB  OE  OG  Od  Oq  Ov  Ox  O{  O€  O‚  O…  O‡  O–  O™  O¦  O«  O­  O°  Oµ  O·  Oº  O¼  OË  OØ  Oí  Oğ  Oó  Oö  Où  Oü  Oÿ  P  P  P  P  P   P#  P%  P'  P)  P,  P.  P0  P2  P5  P7  P9  PV  Pu  P™  P»  Pß  Q  Q)  QH  Qg  Q„  Q  Q  Q“  Q•  Q  Q§  Qª  Q­  Q°  Q³  Qµ  Q·  Q¹  QÆ  QÉ  QÌ  QÏ  QÒ  QÔ  Qñ  Qş  R  R  R  R  R  R  R  R#  R&  R3  R8  R:  R=  RB  RD  RG  RI  RX  Re  Rz  R}  R€  Rƒ  R†  R‰  RŒ  R  R’  R•  R˜  R­  R°  R²  R´  R¶  R¹  R»  R½  R¿  RÂ  RÄ  RÆ  Rã  S  S&  SH  Sl  S’  S¶  SÕ  Sô  T  T  T  T   T"  T+  T4  T6  T8  T:  T<  T>  TK  TN  TQ  TT  TW  TY  Tv  Tƒ  Tˆ  T‹  T  T’  T•  T—  T™  T¨  Tª  T·  T¼  T¾  TÁ  TÆ  TÈ  TË  TÍ  TÜ  Té  Tş  U  U  U  U
  U  U  U  U  U  U  U1  U4  U6  U8  U:  U=  U?  UA  UC  UF  UH  UJ  Ug  U†  Uª  UÌ  Uğ  V  V:  VY  Vx  V•  V  V¡  V¤  V¦  V¯  V¸  V»  V¾  VÁ  VÄ  VÆ  VÈ  VÊ  V×  VÚ  Vİ  Và  Vã  Vå  W  W  W  W  W  W  W   W#  W%  W4  W7  WD  WI  WK  WN  WS  WU  WX  WZ  Wi  Wv  W‹  W  W‘  W”  W—  Wš  W  W   W£  W¦  W©  W¾  WÁ  WÃ  WÅ  WÇ  WÊ  WÍ  WĞ  WÒ  WÕ  W×  WÙ  Wö  X  X9  X[  X  X¥  XÉ  Xè  Y  Y$  Y-  Y0  Y3  Y5  Y>  YG  YJ  YL  YO  YQ  YS  YU  YW  Yd  Yg  Yj  Ym  Yp  Yr  Y  Yœ  Y¡  Y£  Y¦  Y«  Y­  Y°  Y²  YÁ  YÄ  YÍ  YÏ  YÜ  Yá  Yã  Yæ  Yë  Yí  Yğ  Yò  Z  Z  Z#  Z&  Z)  Z,  Z/  Z2  Z5  Z8  Z;  Z>  ZA  ZV  ZY  Z[  Z]  Z_  Zb  Zd  Zf  Zh  Zk  Zm  Zo  ZŒ  Z«  ZÏ  Zñ  [  [;  [_  [~  [  [º  [Ã  [Æ  [É  [Ë  [Ô  [İ  [à  [â  [å  [ç  [é  [ë  [ø  [û  [ş  \  \  \  \#  \0  \5  \7  \:  \?  \A  \D  \F  \U  \X  \e  \j  \l  \o  \t  \v  \y  \{  \Š  \—  \¬  \¯  \²  \µ  \¸  \»  \¾  \Á  \Ä  \Ç  \Ê  \ß  \â  \ä  \æ  \è  \ë  \í  \ï  \ñ  \ô  \ö  \ø  ]  ]4  ]X  ]z  ]  ]Ä  ]è  ^  ^&  ^C  ^L  ^O  ^R  ^T  ^]  ^f  ^i  ^l  ^o  ^r  ^t  ^v  ^ƒ  ^†  ^‰  ^Œ  ^  ^‘  ^®  ^»  ^À  ^Ã  ^Å  ^Ê  ^Í  ^Ï  ^Ñ  ^à  ^ã  ^ğ  ^õ  ^÷  ^ú  ^ÿ  _  _  _  _  _"  _7  _:  _=  _@  _C  _F  _I  _L  _O  _R  _U  _j  _m  _o  _q  _s  _v  _y  _|  _~  _  _ƒ  _…  _¢  _Á  _å  `  `+  `Q  `u  `”  `³  `Ğ  `Ù  `Ü  `ß  `á  `ê  `ó  `ö  `ù  `ü  `ÿ  a  a  a  a  a  a  a  a  a;  aH  aM  aO  aR  aW  aY  a\  a^  am  ap  ay  a{  aˆ  a  a  a’  a—  a™  aœ  a  a­  aº  aÏ  aÒ  aÕ  aØ  aÛ  aŞ  aá  aä  aç  aê  aí  b  b  b  b	  b  b  b  b  b  b  b  b  b8  bW  b{  b  bÁ  bç  c  c*  cI  cf  co  cr  cu  cw  c€  c‰  cŒ  c  c’  c•  c—  c™  c¦  c©  c¬  c¯  c²  c´  cÑ  cŞ  cã  cå  cè  cí  cï  cò  cô  d  d  d  d  d  d  d"  d$  d'  d)  d8  dE  dZ  d]  d`  dc  df  di  dl  do  dr  du  dx  d  d  d’  d”  d–  d™  dœ  dŸ  d¡  d¤  d¦  d¨  dÅ  dä  e  e*  eN  et  e˜  e·  eÖ  eó  eü  eÿ  f  f  f  f  f  f  f  f   f"  f$  f1  f4  f7  f:  f=  f?  f\  fi  fn  fp  fs  fx  fz  f}  f  f  f‘  f“  f   f¥  f§  fª  f¯  f±  f´  f¶  fÅ  fÒ  fé  fì  fï  fò  fõ  fø  fû  fş  g  g  g  g
  g!  g$  g&  g(  g*  g-  g0  g3  g6  g8  g;  g=  g?  g\  g{  gŸ  gÁ  gå  h  h6  hZ  hy  h˜  hµ  h¾  hÁ  hÄ  hÆ  hÏ  hØ  hÛ  hİ  hà  hã  hå  hç  hé  hö  hù  hü  hÿ  i  i  i!  i.  i3  i6  i8  i=  i@  iB  iD  iS  iV  i_  il  is  iv  iy  i|  iƒ  i†  iˆ  i‹  i  i¢  iÁ  iß  iè  ié  ië  iø  iı  iÿ  j  j  j	  j  j  j  j*  j?  jB  jE  jH  jK  jN  jQ  jT  jW  jZ  j]  jr  ju  jw  jy  j{  j~  j  j„  j†  j‰  j‹  j  jª  jÉ  jí  k  k3  kY  k}  kœ  k»  kØ  ká  kä  kç  ké  kò  kû  kş  l   l  l  l  l	  l  l  l  l  l"  l$  lA  lN  lS  lU  lX  l]  l_  lb  ld  ls  lv  l  l  l  l“  l•  l˜  l  lŸ  l¢  l¤  l³  lÀ  lÕ  lØ  lÛ  lŞ  lá  lä  lç  lê  lí  lğ  ló  m  m  m  m  m  m  m  m  m  m  m  m!  m>  m]  m  m£  mÇ  mí  n  n0  nO  nl  nu  nx  n{  n}  n†  n  n’  n•  n˜  n›  n  nŸ  n¬  n¯  n²  nµ  n¸  nº  n×  nä  né  nì  nî  nó  nö  nø  nú  o	  o  o  o  o   o#  o(  o*  o-  o/  o>  oK  o`  oc  of  oi  ol  oo  or  ou  ox  o{  o~  o“  o–  o˜  oš  oœ  oŸ  o¢  o¤  o¦  o©  o«  o­  oÊ  oé  p  p/  pS  py  p  p¼  pÛ  pø  q  q  q  q	  q  q  q  q!  q$  q'  q)  q6  q9  q<  q?  qB  qD  qa  qn  qs  qv  qx  q}  q€  q‚  q„  q“  q–  qŸ  q¬  q±  q³  q¶  q»  q½  qÀ  qÂ  qÑ  qŞ  qó  qö  qù  qü  qÿ  r  r  r  r  r  r  r&  r)  r+  r-  r/  r2  r5  r8  r:  r=  r?  rA  r^  r}  r¡  rÃ  rç  s  s1  sP  so  sŒ  s•  s˜  s›  s  s¦  s¯  s²  s´  s·  s¹  s»  s½  sÊ  sÍ  sĞ  sÓ  sÖ  sØ  sõ  t  t  t
  t  t  t  t  t  t'  t*  t3  t@  tE  tG  tJ  tO  tQ  tT  tV  te  tr  t‡  tŠ  t  t  t“  t–  t™  tœ  tŸ  t¢  t¥  tº  t½  t¿  tÁ  tÃ  tÆ  tÈ  tÊ  tÌ  tÏ  tÑ  tÓ  tğ  u  u3  uU  uy  uŸ  uÃ  uâ  v  v  v'  v*  v-  v/  v8  vA  vD  vF  vI  vL  vN  vP  vR  v_  vb  ve  vh  vk  vm  vŠ  v—  vœ  v  v¡  v¦  v¨  v«  v­  v¼  v¿  vÌ  vÑ  vÓ  vÖ  vÛ  vİ  và  vâ  vñ  vş  w  w  w  w  w  w"  w%  w(  w+  w.  w1  wF  wI  wK  wM  wO  wR  wT  wV  wX  w[  w]  w_  w|  w›  w¿  wá  x  x+  xO  xn  x  xª  x³  x¶  x¹  x»  xÄ  xÍ  xĞ  xÓ  xÖ  xÙ  xÛ  xè  xë  xî  xñ  xô  xö  y  y   y%  y'  y*  y/  y1  y4  y6  yE  yH  yU  yZ  y\  y_  yd  yf  yi  yk  yz  y‡  yœ  yŸ  y¢  y¥  y¨  y«  y®  y±  y´  y·  yº  yÏ  yÒ  yÔ  yÖ  yØ  yÛ  yİ  yß  yá  yä  yæ  yè  z  z$  zH  zj  z  z´  zØ  z÷  {  {3  {<  {?  {B  {D  {M  {V  {Y  {[  {^  {`  {b  {d  {q  {t  {w  {z  {}  {  {œ  {©  {®  {±  {³  {¸  {»  {½  {¿  {Î  {Ñ  {Ş  {ã  {å  {è  {í  {ï  {ò  {ô  |  |  |%  |(  |+  |.  |1  |4  |7  |:  |=  |@  |C  |X  |[  |]  |_  |a  |d  |f  |h  |j  |m  |o  |q  |  |­  |Ñ  |ó  }  }=  }a  }€  }Ÿ  }¼  }Å  }È  }Ë  }Í  }Ö  }ß  }â  }å  }è  }ë  }í  }ï  }ü  }ÿ  ~  ~  ~  ~
  ~'  ~4  ~9  ~<  ~>  ~C  ~F  ~H  ~J  ~Y  ~\  ~i  ~n  ~p  ~s  ~x  ~z  ~}  ~  ~  ~›  ~°  ~³  ~¶  ~¹  ~¼  ~¿  ~Â  ~Å  ~È  ~Ë  ~Î  ~ã  ~æ  ~è  ~ê  ~ì  ~ï  ~ñ  ~ó  ~õ  ~ø  ~ú  ~ü    8  \  ~  ¢  È  ì  €  €*  €G  €P  €S  €V  €X  €a  €j  €m  €o  €r  €t  €v  €ƒ  €†  €‰  €Œ  €  €‘  €®  €»  €À  €Ã  €Å  €Ê  €Í  €Ï  €Ñ  €à  €í  €ò  €ô  €÷  €ü  €ş          4  7  :  =  @  C  F  I  L  O  R  g  j  l  n  p  s  u  w  y  |  ~  €    ¼  à  ‚  ‚&  ‚L  ‚p  ‚  ‚®  ‚Ë  ‚Ô  ‚×  ‚Ú  ‚Ü  ‚å  ‚î  ‚ñ  ‚ô  ‚÷  ‚ú  ‚ü  ƒ	  ƒ  ƒ  ƒ  ƒ  ƒ  ƒ4  ƒA  ƒF  ƒI  ƒK  ƒP  ƒS  ƒU  ƒW  ƒf  ƒi  ƒv  ƒ{  ƒ}  ƒ€  ƒ…  ƒ‡  ƒŠ  ƒŒ  ƒ›  ƒ¨  ƒ½  ƒÀ  ƒÃ  ƒÆ  ƒÉ  ƒÌ  ƒÏ  ƒÒ  ƒÕ  ƒØ  ƒÛ  ƒğ  ƒó  ƒõ  ƒ÷  ƒù  ƒü  ƒş  „   „  „  „  „	  „&  „E  „i  „‹  „¯  „Õ  „ù  …  …7  …T  …]  …`  …c  …e  …n  …w  …z  …|  …  …  …ƒ  …  …“  …–  …™  …œ  …  …»  …È  …Í  …Ğ  …Ò  …×  …Ú  …Ü  …Ş  …í  …ğ  …ı  †  †  †  †  †  †  †  †"  †/  †D  †G  †J  †M  †P  †S  †V  †Y  †\  †_  †b  †w  †z  †|  †~  †€  †ƒ  †…  †‡  †‰  †Œ  †  †  †­  †Ì  †ğ  ‡  ‡6  ‡\  ‡€  ‡Ÿ  ‡¾  ‡Û  ‡ä  ‡ç  ‡ê  ‡ì  ‡õ  ‡ş  ˆ  ˆ  ˆ  ˆ
  ˆ  ˆ  ˆ  ˆ  ˆ!  ˆ$  ˆ'  ˆ)  ˆF  ˆS  ˆX  ˆ[  ˆ]  ˆb  ˆe  ˆg  ˆi  ˆx  ˆ{  ˆˆ  ˆ  ˆ  ˆ’  ˆ—  ˆ™  ˆœ  ˆ  ˆ­  ˆº  ˆÏ  ˆÒ  ˆÕ  ˆØ  ˆÛ  ˆŞ  ˆá  ˆä  ˆç  ˆê  ˆí  ‰  ‰  ‰  ‰	  ‰  ‰  ‰  ‰  ‰  ‰  ‰  ‰  ‰:  ‰Y  ‰}  ‰Ÿ  ‰Ã  ‰é  Š  Š,  ŠK  Šh  Šq  Št  Šw  Šy  Š‚  Š‹  Š  Š‘  Š”  Š—  Š™  Š›  Š¨  Š«  Š®  Š±  Š´  Š¶  ŠÓ  Šà  Šå  Šè  Šê  Šï  Šò  Šô  Šö  ‹  ‹  ‹  ‹  ‹   ‹%  ‹'  ‹*  ‹/  ‹1  ‹4  ‹6  ‹E  ‹R  ‹g  ‹j  ‹m  ‹p  ‹s  ‹v  ‹y  ‹|  ‹  ‹‚  ‹…  ‹š  ‹  ‹Ÿ  ‹¡  ‹£  ‹¦  ‹©  ‹¬  ‹®  ‹±  ‹³  ‹µ  ‹Ò  ‹ñ  Œ  Œ7  Œ[  Œ  Œ¥  ŒÄ  Œã     	          #  &  (  +  .  0  2  ?  B  E  H  K  M  j  w  |      †  ‰  ‹    œ  Ÿ  ¨  ª  ·  ¼  ¾  Á  Æ  È  Ë  Í  Ü  é  ş        
              1  4  6  8  :  =  @  C  E  H  J  L  i  ˆ  ¬  Î  ò    <  [  z  —     £  ¦  ¨  ±  º  ½  ¿  Â  Ä  Æ  È  Õ  Ø  Û  Ş  á  ã                 !  #  2  5  B  G  I  L  Q  S  V  X  g  t  ‰  Œ    ’  •  ˜  ›    ¡  ¤  §  ¼  ¿  Á  Ã  Å  È  Ê  Ì  Î  Ñ  Ó  Õ  ò  ‘  ‘5  ‘W  ‘{  ‘¡  ‘Å  ‘ä  ’  ’   ’)  ’,  ’/  ’1  ’:  ’C  ’F  ’I  ’L  ’O  ’Q  ’^  ’a  ’d  ’g  ’j  ’l  ’‰  ’–  ’›  ’  ’   ’¥  ’¨  ’ª  ’¬  ’»  ’¾  ’Ë  ’Ğ  ’Ò  ’Õ  ’Ú  ’Ü  ’ß  ’á  ’ğ  ’ı  “  “  “  “  “  “!  “$  “'  “*  “-  “0  “E  “H  “J  “L  “N  “Q  “T  “W  “Y  “\  “^  “`  “}  “œ  “À  “â  ”  ”,  ”P  ”o  ”  ”«  ”´  ”·  ”º  ”¼  ”Å  ”Î  ”Ñ  ”Ô  ”×  ”Ú  ”Ü  ”Ş  ”ë  ”î  ”ñ  ”ô  ”÷  ”ù  •  •#  •(  •+  •-  •2  •5  •7  •9  •H  •K  •M  •Z  •_  •a  •d  •i  •k  •n  •p  •  •Œ  •¡  •¤  •§  •ª  •­  •°  •³  •¶  •¹  •¼  •¿  •Ô  •×  •Ù  •Û  •İ  •à  •â  •ä  •æ  •é  •ë  •í  –
  –)  –M  –o  –“  –¹  –İ  –ü  —  —8  —A  —D  —G  —I  —R  —[  —^  —a  —d  —g  —i  —v  —y  —|  —  —‚  —„  —¡  —®  —³  —µ  —¸  —½  —¿  —Â  —Ä  —Ó  —Ö  —ã  —è  —ê  —í  —ò  —ô  —÷  —ù  ˜  ˜  ˜*  ˜-  ˜0  ˜3  ˜6  ˜9  ˜<  ˜?  ˜B  ˜E  ˜H  ˜]  ˜`  ˜b  ˜d  ˜f  ˜i  ˜l  ˜n  ˜p  ˜s  ˜u  ˜w  ˜”  ˜³  ˜×  ˜ù  ™  ™C  ™g  ™†  ™¥  ™Â  ™Ë  ™Î  ™Ñ  ™Ó  ™Ü  ™å  ™ç  ™é  ™ë  ™í  ™ï  ™ü  ™ÿ  š  š  š  š
  š'  š4  š9  š<  š>  šC  šF  šH  šJ  šY  šf  šk  šm  šp  šu  šw  šz  š|  š‹  š˜  š­  š°  š³  š¶  š¹  š¼  š¿  šÂ  šÅ  šÈ  šË  šà  šã  šå  šç  šé  šì  šï  šò  šô  š÷  šù  šû  ›  ›7  ›[  ›}  ›¡  ›Ç  ›ë  œ
  œ)  œF  œO  œR  œU  œW  œ`  œi  œl  œn  œq  œs  œu  œw  œ„  œ‡  œŠ  œ  œ  œ’  œ¯  œ¼  œÁ  œÄ  œÆ  œË  œÎ  œĞ  œÒ  œá  œä  œí  œú  œÿ      	          ,  A  D  G  J  M  P  S  V  Y  \  _  t  w  y  {  }  €  ƒ  †  ˆ  ‹      ¬  Ë  ï    5  [      ½  Ú  ã  æ  é  ë  ô  ı  Ÿ   Ÿ  Ÿ  Ÿ	  Ÿ  Ÿ  Ÿ  Ÿ  Ÿ!  Ÿ$  Ÿ&  ŸC  ŸP  ŸU  ŸX  ŸZ  Ÿ_  Ÿb  Ÿd  Ÿf  Ÿu  Ÿx  Ÿ…  ŸŠ  ŸŒ  Ÿ  Ÿ”  Ÿ–  Ÿ™  Ÿ›  Ÿª  Ÿ·  ŸÎ  ŸÑ  ŸÔ  Ÿ×  ŸÚ  Ÿİ  Ÿà  Ÿã  Ÿæ  Ÿé  Ÿì  Ÿï      	                              !   #   @   _   ƒ   ¥   É   ï  ¡  ¡>  ¡]  ¡|  ¡™  ¡¢  ¡¥  ¡¨  ¡ª  ¡³  ¡¼  ¡¾  ¡À  ¡Â  ¡Ä  ¡Æ  ¡Ó  ¡Ö  ¡Ù  ¡Ü  ¡ß  ¡á  ¡ş  ¢  ¢  ¢  ¢  ¢  ¢  ¢  ¢!  ¢0  ¢3  ¢@  ¢G  ¢J  ¢M  ¢P  ¢W  ¢Z  ¢\  ¢_  ¢a  ¢v  ¢•  ¢³  ¢À  ¢Å  ¢Ç  ¢Ê  ¢Ï  ¢Ñ  ¢Ô  ¢Ö  ¢å  ¢ò  £  £
  £  £  £  £  £  £  £  £"  £%  £:  £=  £?  £A  £C  £F  £H  £J  £L  £O  £Q  £S  £p  £  £³  £Õ  £ù  ¤  ¤C  ¤b  ¤  ¤  ¤§  ¤ª  ¤­  ¤¯  ¤¸  ¤Á  ¤Ä  ¤Ç  ¤Ê  ¤Í  ¤Ï  ¤Ü  ¤ß  ¤â  ¤å  ¤è  ¤ê  ¥  ¥  ¥  ¥  ¥  ¥#  ¥&  ¥(  ¥*  ¥9  ¥<  ¥I  ¥N  ¥P  ¥S  ¥X  ¥Z  ¥]  ¥_  ¥n  ¥{  ¥  ¥“  ¥–  ¥™  ¥œ  ¥Ÿ  ¥¢  ¥¥  ¥¨  ¥«  ¥®  ¥Ã  ¥Æ  ¥È  ¥Ê  ¥Ì  ¥Ï  ¥Ñ  ¥Ó  ¥Õ  ¥Ø  ¥Ú  ¥Ü  ¥ù  ¦  ¦<  ¦^  ¦‚  ¦¨  ¦Ì  ¦ë  §
  §'  §0  §3  §6  §8  §A  §J  §M  §P  §S  §V  §X  §Z  §g  §j  §m  §p  §s  §u  §’  §Ÿ  §¤  §¦  §©  §®  §°  §³  §µ  §Ä  §Ç  §Ô  §Ù  §Û  §Ş  §ã  §å  §è  §ê  §ù  ¨  ¨  ¨  ¨!  ¨$  ¨'  ¨*  ¨-  ¨0  ¨3  ¨6  ¨9  ¨N  ¨Q  ¨S  ¨U  ¨W  ¨Z  ¨]  ¨_  ¨a  ¨d  ¨f  ¨h  ¨…  ¨¤  ¨È  ¨ê  ©  ©4  ©X  ©w  ©–  ©³  ©¼  ©¿  ©Â  ©Ä  ©Í  ©Ö  ©Ù  ©Ü  ©ß  ©â  ©ä  ©ñ  ©ô  ©÷  ©ú  ©ı  ©ÿ  ª  ª)  ª.  ª0  ª3  ª8  ª:  ª=  ª?  ªN  ªQ  ª^  ªc  ªe  ªh  ªm  ªo  ªr  ªt  ªƒ  ª  ª¥  ª¨  ª«  ª®  ª±  ª´  ª·  ªº  ª½  ªÀ  ªÃ  ªØ  ªÛ  ªİ  ªß  ªá  ªä  ªç  ªé  ªë  ªî  ªğ  ªò  «  «.  «R  «t  «˜  «¾  «â  ¬  ¬   ¬=  ¬F  ¬I  ¬L  ¬N  ¬W  ¬`  ¬c  ¬f  ¬i  ¬l  ¬n  ¬p  ¬}  ¬€  ¬ƒ  ¬†  ¬‰  ¬‹  ¬¨  ¬µ  ¬º  ¬½  ¬¿  ¬Ä  ¬Ç  ¬É  ¬Ë  ¬Ú  ¬İ  ¬æ  ¬ó  ¬ø  ¬ú  ¬ı  ­  ­  ­  ­	  ­  ­%  ­<  ­?  ­B  ­E  ­H  ­K  ­N  ­Q  ­T  ­W  ­Z  ­]  ­t  ­w  ­y  ­{  ­}  ­€  ­ƒ  ­†  ­ˆ  ­Š  ­  ­  ­‘  ­®  ­Í  ­ñ  ®  ®7  ®]  ®ˆ  ®¬  ®Ë  ®ê  ¯  ¯  ¯  ¯  ¯  ¯!  ¯*  ¯,  ¯.  ¯0  ¯2  ¯4  ¯A  ¯D  ¯G  ¯J  ¯M  ¯O  ¯l  ¯y  ¯~  ¯  ¯ƒ  ¯ˆ  ¯‹  ¯  ¯  ¯  ¯«  ¯²  ¯µ  ¯¸  ¯»  ¯Â  ¯Å  ¯Ç  ¯Ê  ¯Ì  ¯ê  °	  °  °+  °0  °2  °5  °:  °<  °?  °A  °P  °]  °r  °u  °x  °{  °~  °  °„  °‡  °Š  °  °  °¥  °¨  °ª  °¬  °®  °±  °³  °µ  °·  °º  °¼  °¾  °Û  °ú  ±  ±@  ±d  ±Š  ±®  ±Í  ±ì  ²	  ²  ²  ²  ²  ²#  ²,  ²/  ²2  ²5  ²8  ²:  ²G  ²J  ²M  ²P  ²S  ²U  ²r  ²  ²„  ²‡  ²‰  ²  ²‘  ²“  ²•  ²¤  ²§  ²´  ²¹  ²»  ²¾  ²Ã  ²Å  ²È  ²Ê  ²Ù  ²æ  ²û  ²ş  ³  ³  ³  ³
  ³  ³  ³  ³  ³  ³.  ³1  ³3  ³5  ³7  ³:  ³<  ³>  ³@  ³C  ³E  ³G  ³d  ³ƒ  ³§  ³É  ³í  ´  ´7  ´V  ´u  ´’  ´›  ´  ´¡  ´£  ´¬  ´µ  ´·  ´¹  ´»  ´½  ´¿  ´Ì  ´Ï  ´Ò  ´Õ  ´Ø  ´Ú  ´÷  µ  µ	  µ  µ  µ  µ  µ  µ  µ)  µ+  µ8  µ=  µ?  µB  µG  µI  µL  µN  µ]  µj  µ  µ‚  µ…  µˆ  µ‹  µ  µ‘  µ”  µ—  µš  µ  µ²  µµ  µ·  µ¹  µ»  µ¾  µÀ  µÂ  µÄ  µÇ  µÉ  µË  µè  ¶  ¶+  ¶M  ¶q  ¶—  ¶»  ¶Ú  ¶ù  ·  ·  ·"  ·%  ·'  ·0  ·9  ·<  ·>  ·A  ·C  ·E  ·G  ·T  ·W  ·Z  ·]  ·`  ·b  ·  ·Œ  ·‘  ·”  ·–  ·›  ·  ·   ·¢  ·±  ·´  ·Á  ·Æ  ·È  ·Ë  ·Ğ  ·Ò  ·Õ  ·×  ·æ  ·ó  ¸  ¸  ¸  ¸  ¸  ¸  ¸  ¸  ¸   ¸#  ¸&  ¸;  ¸>  ¸@  ¸B  ¸D  ¸G  ¸J  ¸M  ¸O  ¸R  ¸T  ¸V  ¸s  ¸’  ¸¶  ¸Ø  ¸ü  ¹"  ¹F  ¹e  ¹„  ¹¡  ¹ª  ¹­  ¹°  ¹²  ¹»  ¹Ä  ¹Ç  ¹Ê  ¹Í  ¹Ğ  ¹Ò  ¹ß  ¹â  ¹å  ¹è  ¹ë  ¹í  º
  º  º  º  º!  º&  º(  º+  º-  º<  º?  ºL  ºQ  ºS  ºV  º[  º]  º`  ºb  ºq  º~  º“  º–  º™  ºœ  ºŸ  º¢  º¥  º¨  º«  º®  º±  ºÆ  ºÉ  ºË  ºÍ  ºÏ  ºÒ  ºÕ  º×  ºÙ  ºÜ  ºŞ  ºà  ºı  »  »@  »b  »†  »¬  »Ğ  »ï  ¼  ¼+  ¼4  ¼7  ¼:  ¼<  ¼E  ¼N  ¼P  ¼R  ¼T  ¼V  ¼X  ¼e  ¼h  ¼k  ¼n  ¼q  ¼s  ¼  ¼  ¼¢  ¼¤  ¼§  ¼¬  ¼®  ¼±  ¼³  ¼Â  ¼Å  ¼Ò  ¼×  ¼Ù  ¼Ü  ¼á  ¼ã  ¼æ  ¼è  ¼÷  ½  ½  ½  ½  ½"  ½%  ½(  ½+  ½.  ½1  ½4  ½7  ½L  ½O  ½Q  ½S  ½U  ½X  ½Z  ½\  ½^  ½a  ½c  ½e  ½‚  ½¡  ½Å  ½ç  ¾  ¾1  ¾U  ¾t  ¾“  ¾°  ¾¹  ¾¼  ¾¿  ¾Á  ¾Ê  ¾Ó  ¾Ö  ¾Ø  ¾Û  ¾İ  ¾ß  ¾á  ¾î  ¾ñ  ¾ô  ¾÷  ¾ú  ¾ü  ¿  ¿&  ¿+  ¿-  ¿0  ¿5  ¿7  ¿:  ¿<  ¿K  ¿N  ¿[  ¿`  ¿b  ¿e  ¿j  ¿l  ¿o  ¿q  ¿€  ¿  ¿¢  ¿¥  ¿¨  ¿«  ¿®  ¿±  ¿´  ¿·  ¿º  ¿½  ¿À  ¿Õ  ¿Ø  ¿Ú  ¿Ü  ¿Ş  ¿á  ¿ã  ¿å  ¿ç  ¿ê  ¿ì  ¿î  À  À*  ÀN  Àp  À”  Àº  ÀŞ  Àı  Á  Á9  ÁB  ÁE  ÁH  ÁJ  ÁS  Á\  Á^  Á`  Áb  Ád  Áf  Ás  Áv  Áy  Á|  Á  Á  Á  Á«  Á°  Á³  Áµ  Áº  Á½  Á¿  ÁÁ  ÁĞ  ÁÒ  Áß  Áä  Áæ  Áé  Áî  Áğ  Áó  Áõ  Â  Â  Â&  Â)  Â,  Â/  Â2  Â5  Â8  Â;  Â>  ÂA  ÂD  ÂY  Â\  Â^  Â`  Âb  Âe  Âg  Âi  Âk  Ân  Âp  Âr  Â  Â®  ÂÒ  Âô  Ã  Ã>  Ãb  Ã  Ã   Ã½  ÃÆ  ÃÉ  ÃÌ  ÃÎ  Ã×  Ãà  Ãã  Ãå  Ãè  Ãê  Ãì  Ãù  Ãü  Ãÿ  Ä  Ä  Ä  Ä$  Ä1  Ä6  Ä9  Ä;  Ä@  ÄC  ÄE  ÄG  ÄV  ÄY  Äf  Äk  Äm  Äp  Äu  Äw  Äz  Ä|  Ä‹  Ä˜  Ä­  Ä°  Ä³  Ä¶  Ä¹  Ä¼  Ä¿  ÄÂ  ÄÅ  ÄÈ  ÄË  Äà  Äã  Äå  Äç  Äé  Äì  Äï  Äò  Äô  Ä÷  Äù  Äû  Å  Å7  Å[  Å}  Å¡  ÅÇ  Åë  Æ
  Æ)  ÆF  ÆO  ÆR  ÆU  ÆW  Æ`  Æi  Æl  Æo  Ær  Æu  Æw  Æ„  Æ‡  ÆŠ  Æ  Æ  Æ’  Æ¯  Æ¼  ÆÁ  ÆÄ  ÆÆ  ÆË  ÆÎ  ÆĞ  ÆÒ  Æá  Æî  Æó  Æõ  Æø  Æı  Æÿ  Ç  Ç  Ç  Ç   Ç5  Ç8  Ç;  Ç>  ÇA  ÇD  ÇG  ÇJ  ÇM  ÇP  ÇS  Çh  Çk  Çm  Ço  Çq  Çt  Çw  Çz  Ç|  Ç  Ç  Çƒ  Ç   Ç¿  Çã  È  È)  ÈO  Ès  È’  È±  ÈÎ  È×  ÈÚ  Èİ  Èß  Èè  Èñ  Èô  È÷  Èú  Èı  Èÿ  É  É  É  É  É  É  É  É9  ÉF  ÉK  ÉM  ÉP  ÉU  ÉW  ÉZ  É\  Ék  Én  Ép  É}  É‚  É„  É‡  ÉŒ  É  É‘  É“  É¢  É¯  ÉÄ  ÉÇ  ÉÊ  ÉÍ  ÉĞ  ÉÓ  ÉÖ  ÉÙ  ÉÜ  Éß  Éâ  É÷  Éú  Éü  Éş  Ê   Ê  Ê  Ê  Ê	  Ê  Ê  Ê  Ê-  ÊL  Êp  Ê’  Ê¶  ÊÜ  Ë   Ë  Ë>  Ë[  Ëd  Ëg  Ëj  Ël  Ëu  Ë~  Ë  Ë„  Ë‡  ËŠ  ËŒ  Ë  Ë›  Ë  Ë¡  Ë¤  Ë§  Ë©  ËÆ  ËÓ  ËØ  ËÚ  Ëİ  Ëâ  Ëä  Ëç  Ëé  Ëø  Ëû  Ì  Ì  Ì  Ì  Ì  Ì  Ì  Ì  Ì-  Ì:  ÌO  ÌR  ÌU  ÌX  Ì[  Ì^  Ìa  Ìd  Ìg  Ìj  Ìm  Ì‚  Ì…  Ì‡  Ì‰  Ì‹  Ì  Ì‘  Ì”  Ì–  Ì™  Ì›  Ì  Ìº  ÌÙ  Ìı  Í  ÍC  Íi  Í  Í¬  ÍË  Íè  Íñ  Íô  Í÷  Íù  Î  Î  Î  Î  Î  Î  Î  Î  Î(  Î+  Î.  Î1  Î4  Î6  ÎS  Î`  Îe  Îg  Îj  Îo  Îq  Ît  Îv  Î…  Îˆ  Î•  Îš  Îœ  ÎŸ  Î¤  Î¦  Î©  Î«  Îº  ÎÇ  ÎÜ  Îß  Îâ  Îå  Îè  Îë  Îî  Îñ  Îô  Î÷  Îú  Ï  Ï  Ï  Ï  Ï  Ï  Ï  Ï  Ï!  Ï$  Ï&  Ï(  ÏE  Ïd  Ïˆ  Ïª  ÏÎ  Ïô  Ğ  Ğ7  ĞV  Ğs  Ğ|  Ğ  Ğ‚  Ğ„  Ğ  Ğ–  Ğ™  Ğœ  ĞŸ  Ğ¢  Ğ¤  Ğ±  Ğ´  Ğ·  Ğº  Ğ½  Ğ¿  ĞÜ  Ğé  Ğî  Ğñ  Ğó  Ğø  Ğû  Ğı  Ğÿ  Ñ  Ñ  Ñ  Ñ#  Ñ%  Ñ(  Ñ-  Ñ/  Ñ2  Ñ4  ÑC  ÑP  Ñe  Ñh  Ñk  Ñn  Ñq  Ñt  Ñw  Ñz  Ñ}  Ñ€  Ñƒ  Ñ˜  Ñ›  Ñ  ÑŸ  Ñ¡  Ñ¤  Ñ¦  Ñ¨  Ñª  Ñ­  Ñ¯  Ñ±  ÑÎ  Ñí  Ò  Ò3  ÒW  Ò}  Ò¡  ÒÀ  Òß  Òü  Ó  Ó  Ó  Ó  Ó  Ó  Ó"  Ó%  Ó(  Ó+  Ó-  Ó/  Ó<  Ó?  ÓB  ÓE  ÓH  ÓJ  Óg  Ót  Óy  Ó{  Ó~  Óƒ  Ó…  Óˆ  ÓŠ  Ó™  Óœ  Ó©  Ó®  Ó°  Ó³  Ó¸  Óº  Ó½  Ó¿  ÓÎ  ÓÛ  Óğ  Óó  Óö  Óù  Óü  Óÿ  Ô  Ô  Ô  Ô  Ô  Ô#  Ô&  Ô(  Ô*  Ô,  Ô/  Ô2  Ô5  Ô7  Ô:  Ô<  Ô>  Ô[  Ôz  Ô  ÔÀ  Ôä  Õ
  Õ.  ÕM  Õl  Õ‰  Õ’  Õ•  Õ˜  Õš  Õ£  Õ¬  Õ¯  Õ²  Õµ  Õ¸  Õº  ÕÇ  ÕÊ  ÕÍ  ÕĞ  ÕÓ  ÕÕ  Õò  Õÿ  Ö  Ö  Ö	  Ö  Ö  Ö  Ö  Ö$  Ö'  Ö*  Ö7  Ö<  Ö>  ÖA  ÖF  ÖH  ÖK  ÖM  Ö\  Öi  Ö~  Ö  Ö„  Ö‡  ÖŠ  Ö  Ö  Ö“  Ö–  Ö™  Öœ  Ö±  Ö´  Ö¶  Ö¸  Öº  Ö½  Ö¿  ÖÁ  ÖÃ  ÖÆ  ÖÈ  ÖÊ  Öç  ×  ×*  ×L  ×p  ×–  ×º  ×Ù  ×ø  Ø  Ø  Ø!  Ø$  Ø&  Ø/  Ø8  Ø;  Ø=  Ø@  ØB  ØD  ØQ  ØT  ØW  ØZ  Ø]  Ø_  Ø|  Ø‰  Ø  Ø  Ø“  Ø˜  Øš  Ø  ØŸ  Ø®  Ø°  Ø½  ØÂ  ØÄ  ØÇ  ØÌ  ØÎ  ØÑ  ØÓ  Øâ  Øï  Ù  Ù  Ù
  Ù  Ù  Ù  Ù  Ù  Ù  Ù  Ù"  Ù7  Ù:  Ù<  Ù>  Ù@  ÙC  ÙE  ÙG  ÙI  ÙL  ÙN  ÙP  Ùm  ÙŒ  Ù°  ÙÒ  Ùö  Ú  Ú@  Ú_  Ú~  Ú›  Ú¤  Ú§  Úª  Ú¬  Úµ  Ú¾  ÚÁ  ÚÄ  ÚÇ  ÚÊ  ÚÌ  ÚÙ  ÚÜ  Úß  Úâ  Úå  Úç  Û  Û  Û  Û  Û  Û   Û"  Û%  Û'  Û6  Û9  ÛF  ÛK  ÛM  ÛP  ÛU  ÛW  ÛZ  Û\  Ûk  Ûx  Û  Û‚  Û…  Ûˆ  Û  Û’  Û•  Û˜  Ûš  Û»  Ûó  Ü  Ü)  Ü,  Ü/  Ü2  Ü5  Ü7  ÜC  ÜL  ÜT  Ü]  Ü`  Üi  Üp  Üu  Ü‚  Ü…  Üˆ  Ü‹  Ü  Ü  Üœ  Ü¥  Ü¯  Ü²  Üğ  Üù  İ  İ  İ  İ(  İ+  İ.  İ1  İ4  İ6  İt  İ}  İ†  İ‰  İ–  İ™  İœ  İŸ  İ¢  İ¤  İ²  İ¾  İË  İà  İã  İæ  İé  İì  İï  İò  İõ  İø  İû  İş  Ş  Ş  Ş  Ş  Ş  Ş  Ş"  Ş%  Ş(  Ş+  Ş.  Ş0  Ş;  ŞR  Şk  Ş„  Ş—  Ş®  ŞÃ  ŞÍ  Şâ  Şú  ß  ß  ß_  ßf  ß|  ßƒ  ß  ß›  ß¤  ß§  ßª  ß­  ß°  ß¹  ß¼  ß¿  ßÂ  ßÅ  ßÇ  ßà  ßû  à  àI  àO  àV  àX  àa  àh  àk  àn  àq  àt  à  àˆ  à‹  à  à‘  à˜  à›  à  à¡  à£  àÌ  àú  á&  á(  á5  á<  á?  áB  áE  áL  áO  áR  áU  áW  á^  ág  áo  å~  å  åŠ  å˜  åŸ  å¦  å³  åÀ  åÃ  åÆ  åÉ  åÌ  åÏ  åÒ  åß  åâ  åå  åè  åë  åî  åñ  åó  æ  æ=  æg  æ‘  æš  æ£  æ¦  æµ  æ¾  æÍ  æÔ  æİ  æà  æâ  æä  æñ  æô  æö  æù  æü  æş  ç  ç
  ç  ç  ç  ç  ç  ç%  ç:  ç<  ç>  ç@  çB  çD  çF  çH  çJ  çL  çN  çP  ç]  çl  ço  çr  çu  çx  ç{  ç~  ç  ç  ç’  ç•  ç—  çš  ç  ç   ç¢  ç¤  çÀ  çÏ  çø  è#  èE  è†  è¡  è¾  èÇ  ğÿ  ñ  ñ  ñ"  ñ%  ñ(  ñ+  ñ.  ñ1  ñ4  ñ7  ñ:  ñ=  ñP  ñR  ñU  ñX  ñ[  ñ^  ña  ñd  ñf  ñi  ñk  ñ~  ñ‘  ñ¥  ñ¸  ñÌ  ñÙ  ñï  ò  ò  ò  ò*  ò-  ò0  ò3  ò6  ò9  ò<  òI  òL  òO  òR  òU  òX  ò[  ò]  ò€  ò  ò½  òÑ  òã  ó	  ó  ó  ó"  ó%  ó(  ó+  ó4  ó7  ó:  ó=  ó@  óB  óY  óm  ó†  ó¡  ó®  ó¯  ó°  ó²  óÍ  óÚ  óİ  óà  óã  óæ  óè  ô  ô  ô)  ô,  ô/   	 2 9 b Ç Ô Ù Ü ß ä ç ê ì   # , / 2 5 8 A D G J M O h ‡ “ ¨ ª Â Ç à ê ô ş 	        # 4 L U Z ] _ b k n o x  • ­ Å Ê Ó ô ù  ' 4 7 : = @ C F S V Y [ ] ` b d  ™ ³ Î İ ı ÿ      ! * - 0 2 5 7 O u © Ê Ó Ö Ù Û ç ô ı ş   ; H Q T W Z ] f i l o r t ™ ½ å  . L Y b e h k n w z } € ƒ … œ ° É ä ñ ô ÷ ú ı ÿ  Œ § ´ · º ½ À Â İ æ   	 ´ Á Ä Ç Ê Í Ï ê ó    Á Ê × Ú İ à ã å 	     A J W Z ] ` b d ‚  ” — š Ÿ ¡ £ ¥ ½ ß ì   	        ! $ ; > @ C F I L O R U X [ ] € ­ ¿ Ê í  + ? b x ‡ ” ¨ » ¾ Ç Ó Ø ä ï ñ      % 2 5 8 ; > A D Q T W Z ] ` b d o ƒ ™ § ³ Ó Õ â é ì ï ò ù ü ÿ    , > G R U X [ ^ a c „   Ä Ö ô   2 5 8 ; > A D F I b u „ œ ¯ Ë Î Ñ Ô × Ú Ü     # & ) , . ; F L O R U i r ‘ – µ Â Å Ç Ê Î Û Ş à ã ï ü ÿ      " $ - C H ^ g r u x İ æ ò ÷   7 < g } ­ º Ï Ò Õ Ø Û Ş á ä ç ê í        	                  9  X  |    Â  è ! !+ !J !g !p !s !v !x ! !Š ! ! !“ !– !˜ !¥ !¨ !« !® !± !³ !Ğ !İ !â !å !ç !ì !ï !ñ !ó " " " " " " "  "# "% "4 "9 "B "Y "^ "u "– "™ "œ "Ÿ "¢ "¥ "¨ "ª "­ "Æ "É "Ì "Ï "Ò "Õ "× "à "í "ğ "ó "ö "ù "ü "ÿ # # # # # #. #; #> #@ #C #M #Z #] #_ #b #i #v #y #| # #† #“ #– #˜ #› #¨ #« #­ #° #¼ #É #Ë #Î #Ğ #İ #ò #õ #ø #û #ş $ $ $ $
 $ $ $% $( $* $, $. $1 $4 $7 $9 $< $> $@ $] $| $  $Â $æ % %0 %O %n %‹ %” %— %š %œ %¥ %® %± %´ %· %º %¼ %É %Ì %Ï %Ò %Õ %× %ô & & &	 & & & & & && &3 &8 &: &= &B &D &G &I &X &e &† &‰ &Œ & &’ &• &˜ &š & &¶ &¹ &¼ &¿ &Â &Å &Ç &Ğ &İ &à &ã &æ &é &ì &ï &ñ &ş ' ' ' ' '& ') ', '/ '4 'A 'D 'F 'I 'V 'Y '\ '_ 'l 'o 'q 't ' '„ '† '‰ '• '¢ '¤ '§ '© '¶ 'Ë 'Î 'Ñ 'Ô '× 'Ú 'İ 'à 'ã 'æ 'é 'ş ( ( ( ( (
 ( ( ( ( ( ( (6 (U (y (› (¿ (å )	 )( )G )d )m )p )s )u )~ )‡ )Š ) ) )“ )• )¢ )¥ )¨ )« )® )° )Í )Ú )ß )â )ä )é )ì )î )ğ )ÿ * * * * * *$ *& *) *+ *: *[ *^ *a *c *f *i *l *n *q *Š * * *“ *– *™ *› *¤ *« *® *± *´ *¶ *Ã *Æ *É *Ì *Ş *ë *î *ğ *ó +  + + + + +! +# +& +( +D +o + +“ +– +™ +œ +Ÿ +¢ +¤ +§ +À +Ã +Æ +É +Ì +Ï +Ñ +Ú +ã +æ +é +ì +ï +ñ +ş , , , , ,* ,- ,/ ,2 ,? ,B ,D ,G ,T ,W ,Y ,\ ,h ,u ,w ,z ,| ,‰ , ,¡ ,¤ ,§ ,ª ,­ ,° ,³ ,¶ ,¹ ,¼ ,Ñ ,Ô ,Ö ,Ø ,Ú ,İ ,ß ,á ,ã ,æ ,è ,ê - -& -J -l - -¶ -Ú -ù . .5 .> .A .D .F .O .X .[ .^ .a .d .f .s .v .y .| . . . .« .° .² .µ .º .¼ .¿ .Á .Ğ .İ .â .ä .ç .ì .î .ñ .ó / / / / / / /8 /; /> /_ /b /e /h /k /n /q /s /v / /’ /• /˜ /› / /  /© /° /³ /¶ /¹ /» /È /Ë /Î /Ñ /Ş /á /ã /æ /ó /ö /ø /û 0 0 0 0 0 0( 09 0< 0? 0B 0E 0H 0K 0N 0Q 0b 0e 0h 0k 0m 0p 0s 0v 0y 0{ 0• 0¡ 0´ 0Ï 0İ 0ğ 1 1 1 1 1 1! 1> 1G 1J 1M 1O 1l 1Œ 1™ 1š 1› 1 1ª 1­ 1° 1³ 1¶ 1¸ 1Ê 1Ó 1Ö 1Ø 1Ú 1é 1ö 1÷ 1ø 1ú 2 2 2- 22 2J 2\ 2u 2x 2{ 2~ 2 2„ 2‡ 2 2™ 2œ 2Ÿ 2¢ 2¥ 2§ 2Â 2Ï 2Ò 2Õ 2Ø 2å 2è 2ê 2í 2ú 2ı 2ÿ 3 3 3 3, 3/ 31 33 36 3S 3\ 3z 3 3Ÿ 3µ 3Â 3Å 3È 3Ë 3Î 3Ğ 3ä 3í 3ò 3õ 3ø 3û 4 4 4 4 4 4 4" 4$ 4' 4) 47 4D 4W 4a 4j 4w 4~ 4 4„ 4‡ 4 4‘ 4“ 4– 4˜ 4ª 4³ 4À 4Á 4Â 4Ä 4Ñ 4Ú 4İ 4à 4ã 4æ 4ï 4ò 4õ 4ø 4û 4ı 5 5 5 53 5@ 5I 5L 5O 5R 5U 5^ 5` 5b 5d 5f 5h 5† 5£ 5Æ 5ß 5ì 5ï 5ò 5õ 5÷ 5ù 6 6 6 6  6# 6& 6( 6< 6E 6J 6M 6P 6S 6` 6g 6j 6m 6p 6w 6z 6| 6 6 6Š 6“ 6  6§ 6ª 6­ 6° 6· 6º 6¼ 6¿ 6Á 6Ë 6Ô                         6Ö

/== TheMealsApp.xcodeproj/xcuserdata/ben.xcuserdatad/xcschemes/xcschememanagement.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>SchemeUserState</key>
	<dict>
		<key>TheMealsApp.xcscheme_^#shared#^_</key>
		<dict>
			<key>orderHint</key>
			<integer>0</integer>
		</dict>
	</dict>
</dict>
</plist>


/== TheMealsApp.xcodeproj/xcuserdata/gilangramadhan.xcuserdatad/xcdebugger/Breakpoints_v2.xcbkptlist
<?xml version="1.0" encoding="UTF-8"?>
<Bucket
   uuid = "2E0E5556-CFAC-425D-A534-44E4169CD24C"
   type = "1"
   version = "2.0">
</Bucket>


/== TheMealsApp.xcodeproj/xcuserdata/gilangramadhan.xcuserdatad/xcschemes/xcschememanagement.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>SchemeUserState</key>
	<dict>
		<key>GettingStarted (Playground) 1.xcscheme</key>
		<dict>
			<key>isShown</key>
			<false/>
			<key>orderHint</key>
			<integer>2</integer>
		</dict>
		<key>GettingStarted (Playground) 2.xcscheme</key>
		<dict>
			<key>isShown</key>
			<false/>
			<key>orderHint</key>
			<integer>3</integer>
		</dict>
		<key>GettingStarted (Playground).xcscheme</key>
		<dict>
			<key>isShown</key>
			<false/>
			<key>orderHint</key>
			<integer>0</integer>
		</dict>
		<key>TheMealsApp.xcscheme_^#shared#^_</key>
		<dict>
			<key>orderHint</key>
			<integer>0</integer>
		</dict>
	</dict>
</dict>
</plist>


