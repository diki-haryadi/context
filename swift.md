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
//  Created on 03/04/25.
//

import SwiftUI

struct ContentView: View {
  
  @EnvironmentObject var appState: AppState
  
  var body: some View {
    TabView {
      NavigationView {
        HomeView(
          presenter: HomePresenter(
            homeUseCase: Injection.init().provideHome()
          )
        )
      }
      .tabItem {
        Image(systemName: "gamecontroller")
        Text("Games")
      }
      
      NavigationView {
        SearchView(
          presenter: SearchPresenter(
            searchUseCase: Injection.init().provideSearch()
          )
        )
      }
      .tabItem {
        Image(systemName: "magnifyingglass")
        Text("Search")
      }
      
      NavigationView {
        FavoriteView(
          presenter: FavoritePresenter(
            favoriteUseCase: Injection.init().provideFavorite()
          )
        )
      }
      .tabItem {
        Image(systemName: "heart")
        Text("Favorites")
      }
    }
    .accentColor(.red)
  }
}

class AppState: ObservableObject {
  @Published var selectedTab: Int = 0
  @Published var needsRefreshFavorites: Bool = false
  
  static let shared = AppState()
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
      "filename" : "5118aff5091cb3efec399c808f8c598f.jpg",
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
      "filename" : "5118aff5091cb3efec399c808f8c598f.jpg",
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
      "filename" : "5118aff5091cb3efec399c808f8c598f.jpg",
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
//  Updated on 03/04/25.
//

import Foundation
import RealmSwift

final class Injection: NSObject {

  private func provideRepository() -> GameRepositoryProtocol {
    let realm = try? Realm()

    let locale: LocaleGameDataSource = LocaleGameDataSource.sharedInstance(realm)
    let remote: RemoteGameDataSource = RemoteGameDataSource.sharedInstance

    return GameRepository.sharedInstance(locale, remote)
  }

  func provideHome() -> HomeUseCase {
    let repository = provideRepository()
    return HomeInteractor(repository: repository)
  }

  func provideDetail(gameId: Int) -> DetailUseCase {
    let repository = provideRepository()
    return DetailInteractor(repository: repository, gameId: gameId)
  }

  func provideSearch() -> SearchUseCase {
    let repository = provideRepository()
    return SearchInteractor(repository: repository)
  }

  func provideFavorite() -> FavoriteUseCase {
    let repository = provideRepository()
    return FavoriteInteractor(repository: repository)
  }
}


/== TheMealsApp/Core/Data/GameRespository.swift
//
//  GameRepository.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import Combine

protocol GameRepositoryProtocol {
  func getGames() -> AnyPublisher<[GameModel], Error>
  func getGameDetail(id: Int) -> AnyPublisher<GameModel, Error>
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error>
  func getFavoriteGames() -> AnyPublisher<[GameModel], Error>
  func addToFavorite(game: GameModel) -> AnyPublisher<Bool, Error>
  func removeFromFavorite(id: Int) -> AnyPublisher<Bool, Error>
  func checkIsFavorite(id: Int) -> AnyPublisher<Bool, Error>
}

final class GameRepository: NSObject {
  
  typealias GameInstance = (LocaleGameDataSource, RemoteGameDataSource) -> GameRepository
  
  fileprivate let remote: RemoteGameDataSource
  fileprivate let locale: LocaleGameDataSource
  
  private init(locale: LocaleGameDataSource, remote: RemoteGameDataSource) {
    self.locale = locale
    self.remote = remote
  }
  
  static let sharedInstance: GameInstance = { localeRepo, remoteRepo in
    return GameRepository(locale: localeRepo, remote: remoteRepo)
  }
}

extension GameRepository: GameRepositoryProtocol {
  
  func getGames() -> AnyPublisher<[GameModel], Error> {
    return self.remote.getGames()
      .flatMap { games -> AnyPublisher<[GameModel], Error> in
        return self.addFavoriteStatusToGames(games)
      }
      .eraseToAnyPublisher()
  }
  
  func getGameDetail(id: Int) -> AnyPublisher<GameModel, Error> {
    return self.remote.getGameDetail(id: id)
      .flatMap { game -> AnyPublisher<GameModel, Error> in
        return self.locale.checkIsFavorite(id: game.id)
          .map { isFavorite in
            return GameModel(
              id: game.id,
              name: game.name,
              released: game.released,
              backgroundImage: game.backgroundImage,
              rating: game.rating,
              ratingCount: game.ratingCount,
              description: game.description,
              genres: game.genres,
              platforms: game.platforms,
              isFavorite: isFavorite
            )
          }
          .eraseToAnyPublisher()
      }
      .eraseToAnyPublisher()
  }
  
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error> {
    return self.remote.searchGames(query: query)
      .flatMap { games -> AnyPublisher<[GameModel], Error> in
        return self.addFavoriteStatusToGames(games)
      }
      .eraseToAnyPublisher()
  }
  
  func getFavoriteGames() -> AnyPublisher<[GameModel], Error> {
    return self.locale.getFavoriteGames()
      .eraseToAnyPublisher()
  }
  
  func addToFavorite(game: GameModel) -> AnyPublisher<Bool, Error> {
    return self.locale.addToFavorite(from: game)
      .eraseToAnyPublisher()
  }
  
  func removeFromFavorite(id: Int) -> AnyPublisher<Bool, Error> {
    return self.locale.removeFromFavorite(id: id)
      .eraseToAnyPublisher()
  }
  
  func checkIsFavorite(id: Int) -> AnyPublisher<Bool, Error> {
    return self.locale.checkIsFavorite(id: id)
      .eraseToAnyPublisher()
  }
  
  // Helper method to add favorite status to a list of games
  private func addFavoriteStatusToGames(_ games: [GameModel]) -> AnyPublisher<[GameModel], Error> {
    // Create a publisher for each game to check if it's a favorite
    let publishers = games.map { game in
      self.locale.checkIsFavorite(id: game.id)
        .map { isFavorite in
          // Return a new GameModel with updated favorite status
          return GameModel(
            id: game.id,
            name: game.name,
            released: game.released,
            backgroundImage: game.backgroundImage,
            rating: game.rating,
            ratingCount: game.ratingCount,
            description: game.description,
            genres: game.genres,
            platforms: game.platforms,
            isFavorite: isFavorite
          )
        }
        .eraseToAnyPublisher()
    }
    
    // Combine all publishers into a single publisher
    return Publishers.MergeMany(publishers)
      .collect()
      .eraseToAnyPublisher()
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
//  Created on 03/04/25.
//

import Foundation
import RealmSwift

class GameEntity: Object {
    @Persisted(primaryKey: true) var id: Int
      @Persisted var name: String = ""
      @Persisted var released: String = ""
      @Persisted var backgroundImage: String = ""
      @Persisted var rating: Double = 0.0
      @Persisted var ratingCount: Int = 0
      @Persisted var desc: String = ""
      @Persisted var genres: List<String> = List<String>()
      @Persisted var platforms: List<String> = List<String>()
  
  func toGameModel() -> GameModel {
    return GameModel(
      id: id,
      name: name,
      released: released,
      backgroundImage: backgroundImage,
      rating: rating,
      ratingCount: ratingCount,
      description: desc,
      genres: genres.map { $0 },
      platforms: platforms.map { $0 },
      isFavorite: true
    )
  }
  
  static func fromGameModel(_ model: GameModel) -> GameEntity {
    let entity = GameEntity()
    entity.id = model.id
    entity.name = model.name
    entity.released = model.released
    entity.backgroundImage = model.backgroundImage
    entity.rating = model.rating
    entity.ratingCount = model.ratingCount
    entity.desc = model.description
    
    let genresList = List<String>()
    model.genres.forEach { genresList.append($0) }
    entity.genres = genresList
    
    let platformsList = List<String>()
    model.platforms.forEach { platformsList.append($0) }
    entity.platforms = platformsList
    
    return entity
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
//            gameEntity.setValue(game.ratingTop, forKey: "ratingTop")
//            gameEntity.setValue(game.favorite, forKey: "favorite")
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
//            gameEntity.setValue(!gameEntity.favorite, forKey: "favorite")
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


/== TheMealsApp/Core/Data/Locale/LocaleGameDataSource.swift
//
//  LocaleGameDataSource.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import RealmSwift
import Combine

protocol LocaleGameDataSourceProtocol {
  func getFavoriteGames() -> AnyPublisher<[GameModel], Error>
  func addToFavorite(from game: GameModel) -> AnyPublisher<Bool, Error>
  func removeFromFavorite(id: Int) -> AnyPublisher<Bool, Error>
  func checkIsFavorite(id: Int) -> AnyPublisher<Bool, Error>
}

final class LocaleGameDataSource: NSObject {
  
  private let realm: Realm?
  
  private init(realm: Realm?) {
    self.realm = realm
  }
  
  static let sharedInstance: (Realm?) -> LocaleGameDataSource = { realmDatabase in
    return LocaleGameDataSource(realm: realmDatabase)
  }
}

extension LocaleGameDataSource: LocaleGameDataSourceProtocol {
  
  func getFavoriteGames() -> AnyPublisher<[GameModel], Error> {
    return Future<[GameModel], Error> { completion in
      if let realm = self.realm {
        let games: Results<GameEntity> = {
          realm.objects(GameEntity.self)
        }()
        
        let gameModels = GameMapper.mapGameEntitiesToDomainModels(
          input: Array(games)
        )
        
        completion(.success(gameModels))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }
  
  func addToFavorite(from game: GameModel) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          let gameEntity = GameMapper.mapGameModelToEntity(input: game)
          try realm.write {
            realm.add(gameEntity, update: .modified)
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
  
  func removeFromFavorite(id: Int) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        do {
          if let game = realm.object(ofType: GameEntity.self, forPrimaryKey: id) {
            try realm.write {
              realm.delete(game)
            }
            completion(.success(true))
          } else {
            completion(.failure(DatabaseError.requestFailed))
          }
        } catch {
          completion(.failure(DatabaseError.requestFailed))
        }
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }
  
  func checkIsFavorite(id: Int) -> AnyPublisher<Bool, Error> {
    return Future<Bool, Error> { completion in
      if let realm = self.realm {
        let game = realm.object(ofType: GameEntity.self, forPrimaryKey: id)
        completion(.success(game != nil))
      } else {
        completion(.failure(DatabaseError.invalidInstance))
      }
    }.eraseToAnyPublisher()
  }
}
//
//enum DatabaseError: LocalizedError {
//  case invalidInstance
//  case requestFailed
//  
//  var errorDescription: String? {
//    switch self {
//    case .invalidInstance: return "Database can't instance."
//    case .requestFailed: return "Your request failed."
//    }
//  }
//}


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
  
//  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<[GameModel], Error>
//  func getGameDetail(by id: Int) -> AnyPublisher<GameDetailModel, Error>
//  func searchGame(by title: String) -> AnyPublisher<[GameModel], Error>
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
  
//  func searchGame(
//    by title: String
//  ) -> AnyPublisher<[GameModel], Error> {
//    return self.getGames(search: title)
//      .eraseToAnyPublisher()
//  }
//  
//  func getGames(
//    page: Int = 1,
//    pageSize: Int = 10,
//    search: String? = nil
//  ) -> AnyPublisher<[GameModel], Error> {
//    return self.remote.getGames(page: page, pageSize: pageSize, search: search)
//      .map { GameMapper.mapGamesResponseToModels(input: $0) }
//      .eraseToAnyPublisher()
//  }
//  
//  func getGameDetail(
//    by id: Int
//  ) -> AnyPublisher<GameDetailModel, Error> {
//    return self.remote.getGameDetail(by: id)
//      .map { GameMapper.mapGameDetailResponseToModel(input: $0) }
//      .eraseToAnyPublisher()
//  }

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


/== TheMealsApp/Core/Data/Remote/RemoteGameDataSource.swift
//
//  RemoteGameDataSource.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import Combine

protocol RemoteGameDataSourceProtocol {
  func getGames() -> AnyPublisher<[GameModel], Error>
  func getGameDetail(id: Int) -> AnyPublisher<GameModel, Error>
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error>
}

final class RemoteGameDataSource: NSObject {
  
  private override init() { }
  
  static let sharedInstance: RemoteGameDataSource = RemoteGameDataSource()
  
  private let baseUrl = "https://api.rawg.io/api"
  private let apiKey = "YOUR_API_KEY" // Add your RAWG API key
  
  private func createRequest(endpoint: String, queryParams: [String: String] = [:]) -> URLRequest {
    var components = URLComponents(string: "\(baseUrl)/\(endpoint)")!
    var queryItems = [URLQueryItem(name: "key", value: apiKey)]
    
    for (key, value) in queryParams {
      queryItems.append(URLQueryItem(name: key, value: value))
    }
    
    components.queryItems = queryItems
    
    return URLRequest(url: components.url!)
  }
}

extension RemoteGameDataSource: RemoteGameDataSourceProtocol {
  
  func getGames() -> AnyPublisher<[GameModel], Error> {
    let request = createRequest(endpoint: "games")
    
    return URLSession.shared.dataTaskPublisher(for: request)
      .map { $0.data }
      .decode(type: GamesResponse.self, decoder: JSONDecoder())
      .map { response in
        GameMapper.mapGameResponsesToDomainModels(input: response.results)
      }
      .eraseToAnyPublisher()
  }
  
  func getGameDetail(id: Int) -> AnyPublisher<GameModel, Error> {
    let request = createRequest(endpoint: "games/\(id)")
    
    return URLSession.shared.dataTaskPublisher(for: request)
      .map { $0.data }
      .decode(type: GameDetailResponse.self, decoder: JSONDecoder())
      .map { response in
        GameMapper.mapDetailResponseToDomainModel(input: response)
      }
      .eraseToAnyPublisher()
  }
  
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error> {
    let request = createRequest(endpoint: "games", queryParams: ["search": query])
    
    return URLSession.shared.dataTaskPublisher(for: request)
      .map { $0.data }
      .decode(type: GamesResponse.self, decoder: JSONDecoder())
      .map { response in
        GameMapper.mapGameResponsesToDomainModels(input: response.results)
      }
      .eraseToAnyPublisher()
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


/== TheMealsApp/Core/Data/Remote/Response/GameDetailResponse.swift
//
//  GameDetailResponse.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation

// This struct matches the structure of the game detail API response
struct GameDetailResponse: Decodable {
    let id: Int
    let name: String
    let released: String?
    let backgroundImage: String?
    let rating: Double
    let ratingsCount: Int
    let description: String?
    let genres: [GenreResponse]
    let platforms: [PlatformWrapper]
    let developers: [DeveloperResponse]?
    let publishers: [PublisherResponse]?
    let tags: [TagResponse]?
    let esrbRating: ESRBRating?
    
    enum CodingKeys: String, CodingKey {
        case id
        case name
        case released
        case backgroundImage = "background_image"
        case rating
        case ratingsCount = "ratings_count"
        case description = "description_raw"
        case genres
        case platforms
        case developers
        case publishers
        case tags
        case esrbRating = "esrb_rating"
    }
    
    func toGameModel() -> GameModel {
        return GameModel(
            id: id,
            name: name,
            released: released ?? "Unknown",
            backgroundImage: backgroundImage ?? "",
            rating: rating,
            ratingCount: ratingsCount,
            description: description ?? "No description available",
            genres: genres.map { $0.name },
            platforms: platforms.map { $0.platform.name },
            isFavorite: false
        )
    }
}

// Additional response types for game detail data
struct DeveloperResponse: Decodable {
    let id: Int
    let name: String
}

struct PublisherResponse: Decodable {
    let id: Int
    let name: String
}

struct TagResponse: Decodable {
    let id: Int
    let name: String
}

struct ESRBRating: Decodable {
    let id: Int
    let name: String
}

// These response types were already defined in GameResponse.swift
// Included here for reference in case you need to add them
/*
struct GenreResponse: Decodable {
    let id: Int
    let name: String
}

struct PlatformWrapper: Decodable {
    let platform: PlatformResponse
}

struct PlatformResponse: Decodable {
    let id: Int
    let name: String
}
*/

// List response type for the games list endpoint
struct GamesResponse: Decodable {
    let results: [GameResponse]
    let count: Int?
    let next: String?
    let previous: String?
}


/== TheMealsApp/Core/Data/Remote/Response/GamesResponse.swift
//
//  GameResponse.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation

struct GameResponse: Decodable {
  let id: Int
  let name: String
  let released: String?
  let backgroundImage: String?
  let rating: Double
  let ratingsCount: Int
  let description: String?
  let genres: [GenreResponse]
  let platforms: [PlatformWrapper]
  
  enum CodingKeys: String, CodingKey {
    case id
    case name
    case released
    case backgroundImage = "background_image"
    case rating
    case ratingsCount = "ratings_count"
    case description = "description_raw"
    case genres
    case platforms
  }
  
  func toGameModel() -> GameModel {
    return GameModel(
      id: id,
      name: name,
      released: released ?? "Unknown",
      backgroundImage: backgroundImage ?? "",
      rating: rating,
      ratingCount: ratingsCount,
      description: description ?? "No description available",
      genres: genres.map { $0.name },
      platforms: platforms.map { $0.platform.name },
      isFavorite: false
    )
  }
}

struct GenreResponse: Decodable {
  let id: Int
  let name: String
}

struct PlatformWrapper: Decodable {
  let platform: PlatformResponse
}

struct PlatformResponse: Decodable {
  let id: Int
  let name: String
}

struct GamesListResponse: Decodable {
  let results: [GameResponse]
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
//  Created on 03/04/25.
//

import Foundation

struct GameModel: Equatable, Identifiable {
  let id: Int
  let name: String
  let released: String
  let backgroundImage: String
  let rating: Double
  let ratingCount: Int
  let description: String
  let genres: [String]
  let platforms: [String]
  let isFavorite: Bool
  
  init(
    id: Int,
    name: String,
    released: String,
    backgroundImage: String,
    rating: Double,
    ratingCount: Int,
    description: String,
    genres: [String],
    platforms: [String],
    isFavorite: Bool = false
  ) {
    self.id = id
    self.name = name
    self.released = released
    self.backgroundImage = backgroundImage
    self.rating = rating
    self.ratingCount = ratingCount
    self.description = description
    self.genres = genres
    self.platforms = platforms
    self.isFavorite = isFavorite
  }
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
//  DetailUseCase.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import Combine

protocol DetailUseCase {
  func getGameDetail() -> AnyPublisher<GameModel, Error>
  func addToFavorite() -> AnyPublisher<Bool, Error>
  func removeFromFavorite() -> AnyPublisher<Bool, Error>
  func checkIsFavorite() -> AnyPublisher<Bool, Error>
  func getGameId() -> Int
}

class DetailInteractor: DetailUseCase {
  private let repository: GameRepositoryProtocol
  private let gameId: Int
  
  required init(repository: GameRepositoryProtocol, gameId: Int) {
    self.repository = repository
    self.gameId = gameId
  }
  
  func getGameDetail() -> AnyPublisher<GameModel, Error> {
    return repository.getGameDetail(id: gameId)
  }
  
  func addToFavorite() -> AnyPublisher<Bool, Error> {
    return repository.getGameDetail(id: gameId)
      .flatMap { game in
        self.repository.addToFavorite(game: game)
      }
      .eraseToAnyPublisher()
  }
  
  func removeFromFavorite() -> AnyPublisher<Bool, Error> {
    return repository.removeFromFavorite(id: gameId)
  }
  
  func checkIsFavorite() -> AnyPublisher<Bool, Error> {
    return repository.checkIsFavorite(id: gameId)
  }
  
  func getGameId() -> Int {
    return gameId
  }
}


/== TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift
//
//  FavoriteUseCase.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import Combine

protocol FavoriteUseCase {
  func getFavoriteGames() -> AnyPublisher<[GameModel], Error>
}

class FavoriteInteractor: FavoriteUseCase {
  
  private let repository: GameRepositoryProtocol
  
  required init(repository: GameRepositoryProtocol) {
    self.repository = repository
  }
  
  func getFavoriteGames() -> AnyPublisher<[GameModel], Error> {
    return repository.getFavoriteGames()
  }
}


/== TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift
//
//  HomeUseCase.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import Combine

protocol HomeUseCase {
  func getGames() -> AnyPublisher<[GameModel], Error>
}

class HomeInteractor: HomeUseCase {
  
  private let repository: GameRepositoryProtocol
  
  required init(repository: GameRepositoryProtocol) {
    self.repository = repository
  }
  
  func getGames() -> AnyPublisher<[GameModel], Error> {
    return repository.getGames()
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
//  func getGame() -> AnyPublisher<GameDetailModel, Error>

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
    
//
//  func getGame() -> AnyPublisher<GameDetailModel, Error> {
//      return repository.getGameDetail(by: game.id)
//  }
//    
//  func getGame() -> GameModel {
//        return game
//  }
}


/== TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift
//
//  SearchUseCase.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import Foundation
import Combine

protocol SearchUseCase {
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error>
}

class SearchInteractor: SearchUseCase {
  
  private let repository: GameRepositoryProtocol
  
  required init(repository: GameRepositoryProtocol) {
    self.repository = repository
  }
  
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error> {
    return repository.searchGames(query: query)
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
//  Created on 03/04/25.
//

import Foundation
import RealmSwift

final class GameMapper {
    
    static func mapGameResponsesToDomainModels(
        input gameResponses: [GameResponse]
    ) -> [GameModel] {
        return gameResponses.map { mapGameResponseToDomainModel(input: $0) }
    }
    static func mapGamesResponseToModels(
          input gameResponses: [GameResponse]
      ) -> [GameModel] {
          return mapGameResponsesToDomainModels(input: gameResponses)
      }
      
    
    static func mapGameResponseToDomainModel(
        input response: GameResponse,
        isFavorite: Bool = false
    ) -> GameModel {
        return GameModel(
            id: response.id,
            name: response.name,
            released: response.released ?? "Unknown",
            backgroundImage: response.backgroundImage ?? "",
            rating: response.rating,
            ratingCount: response.ratingsCount,
            description: response.description ?? "No description available",
            genres: response.genres.map { $0.name },
            platforms: response.platforms.map { $0.platform.name },
            isFavorite: isFavorite
        )
    }
    
    static func mapDetailResponseToDomainModel(
        input response: GameDetailResponse,
        isFavorite: Bool = false
    ) -> GameModel {
        return GameModel(
            id: response.id,
            name: response.name,
            released: response.released ?? "Unknown",
            backgroundImage: response.backgroundImage ?? "",
            rating: response.rating,
            ratingCount: response.ratingsCount,
            description: response.description ?? "No description available",
            genres: response.genres.map { $0.name },
            platforms: response.platforms.map { $0.platform.name },
            isFavorite: isFavorite
        )
    }
    
    static func mapGameEntitiesToDomainModels(
        input gameEntities: [GameEntity]
    ) -> [GameModel] {
        return gameEntities.map { mapGameEntityToDomainModel(input: $0) }
    }
    
    static func mapGameEntityToDomainModel(
        input entity: GameEntity
    ) -> GameModel {
        return GameModel(
            id: entity.id,
            name: entity.name,
            released: entity.released,
            backgroundImage: entity.backgroundImage,
            rating: entity.rating,
            ratingCount: entity.ratingCount,
            description: entity.desc,
            genres: Array(entity.genres),
            platforms: Array(entity.platforms),
            isFavorite: true
        )
    }
    
    static func mapGameModelToEntity(
        input model: GameModel
    ) -> GameEntity {
        let entity = GameEntity()
        entity.id = model.id
        entity.name = model.name
        entity.released = model.released
        entity.backgroundImage = model.backgroundImage
        entity.rating = model.rating
        entity.ratingCount = model.ratingCount
        entity.desc = model.description
        
        let genresList = List<String>()
        model.genres.forEach { genresList.append($0) }
        entity.genres = genresList
        
        let platformsList = List<String>()
        model.platforms.forEach { platformsList.append($0) }
        entity.platforms = platformsList
        
        return entity
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
//  Created on 03/04/25.
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
        .frame(width: 250, height: 250)
      
      Text(title)
        .font(.system(.body, design: .rounded))
        .multilineTextAlignment(.center)
        .foregroundColor(.gray)
    }
  }
}


/== TheMealsApp/Core/Utils/View/CustomIcon.swift
//
//  CustomIcon.swift
//  TheMealsApp
//
//  Created by Ari Supriatna on 19/08/20.
//  Copyright © 2020 Dicoding Indonesia. All rights reserved.
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
//  Created on 03/04/25.
//

import SwiftUI
import Combine

class DetailPresenter: ObservableObject {
  
  private var cancellables: Set<AnyCancellable> = []
  private let detailUseCase: DetailUseCase
  
  @Published var game: GameModel?
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false
  @Published var isFavorite: Bool = false
  
  init(detailUseCase: DetailUseCase) {
    self.detailUseCase = detailUseCase
    checkIsFavorite()
  }
  
  func getGameDetail() {
    isLoading = true
    detailUseCase.getGameDetail()
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
      }, receiveValue: { game in
        self.game = game
      })
      .store(in: &cancellables)
  }
  
  func checkIsFavorite() {
    detailUseCase.checkIsFavorite()
      .receive(on: RunLoop.main)
      .sink(receiveCompletion: { _ in },
            receiveValue: { status in
              self.isFavorite = status
            })
      .store(in: &cancellables)
  }
  
  func updateFavoriteStatus() {
    if isFavorite {
      detailUseCase.removeFromFavorite()
        .receive(on: RunLoop.main)
        .sink(receiveCompletion: { _ in },
              receiveValue: { _ in
                self.isFavorite = false
              })
        .store(in: &cancellables)
    } else {
      detailUseCase.addToFavorite()
        .receive(on: RunLoop.main)
        .sink(receiveCompletion: { _ in },
              receiveValue: { _ in
                self.isFavorite = true
              })
        .store(in: &cancellables)
    }
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

    func makeGameDetailView(for gameId: Int) -> some View {
       let detailUseCase = Injection.init().provideDetail(gameId: gameId)
       let presenter = DetailPresenter(detailUseCase: detailUseCase)
       return DetailView(presenter: presenter)
     }

}


/== TheMealsApp/Module/Detail/View/DetailView.swift
//
//  DetailView.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import SwiftUI
import SDWebImageSwiftUI

struct DetailView: View {
  
  @ObservedObject var presenter: DetailPresenter
  
  var body: some View {
    ZStack {
      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isError {
        errorIndicator
      } else if let game = presenter.game {
        ScrollView(.vertical, showsIndicators: true) {
          VStack(alignment: .leading, spacing: 16) {
            headerSection(game)
            
            Divider()
            
            descriptionSection(game)
            
            Divider()
            
            detailsSection(game)
          }
          .padding()
        }
      } else {
        emptyGame
      }
    }
    .navigationBarTitle(
      Text(presenter.game?.name ?? "Game Detail"),
      displayMode: .inline
    )
    .navigationBarItems(trailing: favoriteButton)
    .onAppear {
      self.presenter.getGameDetail()
    }
  }
  
  var favoriteButton: some View {
    Button(action: {
      self.presenter.updateFavoriteStatus()
    }) {
      Image(systemName: presenter.isFavorite ? "heart.fill" : "heart")
        .foregroundColor(presenter.isFavorite ? .red : .gray)
    }
  }
}

extension DetailView {
  
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
    )
  }
  
  var emptyGame: some View {
    CustomEmptyView(
      image: "assetNoData",
      title: "No game data available"
    )
  }
  
  func headerSection(_ game: GameModel) -> some View {
    VStack(alignment: .leading, spacing: 8) {
      WebImage(url: URL(string: game.backgroundImage))
        .resizable()
        .indicator(.activity)
        .transition(.fade(duration: 0.5))
        .scaledToFill()
        .frame(height: 200)
        .clipped()
        .cornerRadius(12)
      
      Text(game.name)
        .font(.title)
        .fontWeight(.bold)
      
      HStack {
        HStack {
          Image(systemName: "star.fill")
            .foregroundColor(.yellow)
          Text(String(format: "%.1f", game.rating))
          Text("(\(game.ratingCount))")
            .foregroundColor(.secondary)
        }
        
        Spacer()
        
        Text("Released: \(game.released)")
          .foregroundColor(.secondary)
      }
      .font(.subheadline)
    }
  }
  
  func descriptionSection(_ game: GameModel) -> some View {
    VStack(alignment: .leading, spacing: 8) {
      Text("Description")
        .font(.headline)
      
      Text(game.description)
        .font(.body)
        .fixedSize(horizontal: false, vertical: true)
    }
  }
  
  func detailsSection(_ game: GameModel) -> some View {
    VStack(alignment: .leading, spacing: 16) {
      if !game.genres.isEmpty {
        VStack(alignment: .leading, spacing: 8) {
          Text("Genres")
            .font(.headline)
          
          ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
              ForEach(game.genres, id: \.self) { genre in
                Text(genre)
                  .font(.caption)
                  .padding(.horizontal, 12)
                  .padding(.vertical, 6)
                  .background(Color.gray.opacity(0.2))
                  .cornerRadius(16)
              }
            }
          }
        }
      }
      
      if !game.platforms.isEmpty {
        VStack(alignment: .leading, spacing: 8) {
          Text("Platforms")
            .font(.headline)
          
          ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
              ForEach(game.platforms, id: \.self) { platform in
                Text(platform)
                  .font(.caption)
                  .padding(.horizontal, 12)
                  .padding(.vertical, 6)
                  .background(Color.gray.opacity(0.2))
                  .cornerRadius(16)
              }
            }
          }
        }
      }
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
//  Created on 03/04/25.
//

import SwiftUI
import Combine

class FavoritePresenter: ObservableObject {
  
  private var cancellables: Set<AnyCancellable> = []
  private let router = FavoriteRouter()
  private let favoriteUseCase: FavoriteUseCase
  
  @Published var games: [GameModel] = []
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false
  
  init(favoriteUseCase: FavoriteUseCase) {
    self.favoriteUseCase = favoriteUseCase
  }
  
  func getFavoriteGames() {
    isLoading = true
    favoriteUseCase.getFavoriteGames()
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
      }, receiveValue: { games in
        self.games = games
      })
      .store(in: &cancellables)
  }
  
  func linkBuilder<Content: View>(
    for gameId: Int,
    @ViewBuilder content: () -> Content
  ) -> some View {
    NavigationLink(destination: router.makeDetailView(for: gameId)) { content() }
  }
}


/== TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift
//
//  FavoriteRouter.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import SwiftUI

class FavoriteRouter {
  
  func makeDetailView(for gameId: Int) -> some View {
    let detailUseCase = Injection.init().provideDetail(gameId: gameId)
    let presenter = DetailPresenter(detailUseCase: detailUseCase)
    return DetailView(presenter: presenter)
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
//  Created on 03/04/25.
//

import SwiftUI
import SDWebImageSwiftUI

struct FavoriteView: View {
  
  @ObservedObject var presenter: FavoritePresenter
  
  var body: some View {
    ZStack {
      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isError {
        errorIndicator
      } else if presenter.games.isEmpty {
        emptyFavorites
      } else {
        content
      }
    }
    .onAppear {
      self.presenter.getFavoriteGames()
    }
    .navigationBarTitle(
      Text("Favorite Games"),
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
      title: "Your favorite games list is empty"
    ).offset(y: 80)
  }
  
  var content: some View {
    ScrollView(.vertical, showsIndicators: false) {
      LazyVStack(spacing: 16) {
        ForEach(self.presenter.games) { game in
          ZStack {
            self.presenter.linkBuilder(for: game.id) {
              GameRow(game: game)
            }
            .buttonStyle(PlainButtonStyle())
          }
          .padding(.horizontal)
        }
      }
      .padding(.vertical)
    }
  }
}


/== TheMealsApp/Module/Home/Presenter/HomePresenter.swift
//
//  HomePresenter.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import SwiftUI
import Combine

class HomePresenter: ObservableObject {
  
  private var cancellables: Set<AnyCancellable> = []
  private let router = HomeRouter()
  private let homeUseCase: HomeUseCase
  
  @Published var games: [GameModel] = []
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false
  
  init(homeUseCase: HomeUseCase) {
    self.homeUseCase = homeUseCase
  }
  
  func getGames() {
    isLoading = true
    homeUseCase.getGames()
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
      }, receiveValue: { games in
        self.games = games
      })
      .store(in: &cancellables)
  }
  
  func linkBuilder<Content: View>(
    for gameId: Int,
    @ViewBuilder content: () -> Content
  ) -> some View {
    NavigationLink(destination: router.makeDetailView(for: gameId)) { content() }
  }
}


/== TheMealsApp/Module/Home/Router/HomeRouter.swift
//
//  HomeRouter.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import SwiftUI

class HomeRouter {
  
  func makeDetailView(for gameId: Int) -> some View {
    let detailUseCase = Injection.init().provideDetail(gameId: gameId)
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
//  Created on 03/04/25.
//

import SwiftUI
import SDWebImageSwiftUI

struct HomeView: View {
  
  @ObservedObject var presenter: HomePresenter
  
  var body: some View {
    ZStack {
      if presenter.isLoading {
        loadingIndicator
      } else if presenter.isError {
        errorIndicator
      } else if presenter.games.isEmpty {
        emptyGames
      } else {
        content
      }
    }.onAppear {
      self.presenter.getGames()
    }.navigationBarTitle(
      Text("Game Catalog"),
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
  
  var emptyGames: some View {
    CustomEmptyView(
      image: "assetNoData",
      title: "No games found"
    ).offset(y: 80)
  }
  
  var content: some View {
    ScrollView(.vertical, showsIndicators: false) {
      LazyVStack(spacing: 16) {
        ForEach(self.presenter.games) { game in
          ZStack {
            self.presenter.linkBuilder(for: game.id) {
              GameRow(game: game)
            }
            .buttonStyle(PlainButtonStyle())
          }
          .padding(.horizontal)
        }
      }
      .padding(.vertical)
    }
  }
}

struct GameRow: View {
  var game: GameModel
  
  var body: some View {
    HStack(alignment: .top, spacing: 16) {
      WebImage(url: URL(string: game.backgroundImage))
        .resizable()
        .indicator(.activity)
        .transition(.fade(duration: 0.5))
        .scaledToFill()
        .frame(width: 120, height: 80)
        .cornerRadius(8)
      
      VStack(alignment: .leading, spacing: 4) {
        Text(game.name)
          .font(.headline)
          .lineLimit(2)
        
        Text("Released: \(game.released)")
          .font(.subheadline)
          .foregroundColor(.secondary)
        
        HStack {
          Image(systemName: "star.fill")
            .foregroundColor(.yellow)
          Text(String(format: "%.1f", game.rating))
          Text("(\(game.ratingCount))")
            .foregroundColor(.secondary)
        }
        .font(.subheadline)
        
        if !game.genres.isEmpty {
          Text(game.genres.prefix(3).joined(separator: ", "))
            .font(.caption)
            .foregroundColor(.secondary)
            .lineLimit(1)
        }
      }
      
      Spacer()
      
      if game.isFavorite {
        Image(systemName: "heart.fill")
          .foregroundColor(.red)
      }
    }
    .padding()
    .background(Color(.systemBackground))
    .cornerRadius(12)
    .shadow(color: Color.black.opacity(0.1), radius: 5, x: 0, y: 2)
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
//  Created on 03/04/25.
//

import SwiftUI
import Combine

class SearchPresenter: ObservableObject {
  
  private var cancellables: Set<AnyCancellable> = []
  private let router = SearchRouter()
  private let searchUseCase: SearchUseCase
  
  @Published var games: [GameModel] = []
  @Published var searchQuery: String = ""
  @Published var errorMessage: String = ""
  @Published var isLoading: Bool = false
  @Published var isError: Bool = false
  
  init(searchUseCase: SearchUseCase) {
    self.searchUseCase = searchUseCase
    
    $searchQuery
      .debounce(for: .milliseconds(800), scheduler: RunLoop.main)
      .removeDuplicates()
      .filter { !$0.isEmpty }
      .sink(receiveValue: { [weak self] query in
        self?.searchGames(query: query)
      })
      .store(in: &cancellables)
  }
  
  func searchGames(query: String) {
    isLoading = true
    searchUseCase.searchGames(query: query)
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
      }, receiveValue: { games in
        self.games = games
      })
      .store(in: &cancellables)
  }
  
  func linkBuilder<Content: View>(
    for gameId: Int,
    @ViewBuilder content: () -> Content
  ) -> some View {
    NavigationLink(destination: router.makeDetailView(for: gameId)) { content() }
  }
}


/== TheMealsApp/Module/Search/Router/SearchRouter.swift
//
//  SearchRouter.swift
//  TheMealsApp
//
//  Created on 03/04/25.
//

import SwiftUI

class SearchRouter {
  
  func makeDetailView(for gameId: Int) -> some View {
    let detailUseCase = Injection.init().provideDetail(gameId: gameId)
    let presenter = DetailPresenter(detailUseCase: detailUseCase)
    return DetailView(presenter: presenter)
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
//  Created on 03/04/25.
//

import SwiftUI
import SDWebImageSwiftUI

struct SearchView: View {
  
  @ObservedObject var presenter: SearchPresenter
  
  var body: some View {
    ZStack {
      VStack {
        searchBar
        
        if presenter.isLoading {
          loadingIndicator
        } else if presenter.isError {
          errorIndicator
        } else if presenter.games.isEmpty && !presenter.searchQuery.isEmpty {
          emptyGames
        } else if !presenter.games.isEmpty {
          gameList
        } else {
          startSearching
        }
      }
    }
    .navigationBarTitle(
      Text("Search Games"),
      displayMode: .automatic
    )
  }
  
  var searchBar: some View {
    HStack {
      Image(systemName: "magnifyingglass")
        .foregroundColor(.gray)
      
      TextField("Search for games...", text: $presenter.searchQuery)
        .disableAutocorrection(true)
      
      if !presenter.searchQuery.isEmpty {
        Button(action: {
          presenter.searchQuery = ""
          presenter.games = []
        }) {
          Image(systemName: "xmark.circle.fill")
            .foregroundColor(.gray)
        }
      }
    }
    .padding()
    .background(Color(.systemGray6))
    .cornerRadius(10)
    .padding()
  }
}

extension SearchView {
  var loadingIndicator: some View {
    VStack {
      Spacer()
      ProgressView()
        .progressViewStyle(CircularProgressViewStyle())
      Text("Searching...")
        .padding(.top, 8)
      Spacer()
    }
  }
  
  var errorIndicator: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: presenter.errorMessage
    )
  }
  
  var emptyGames: some View {
    CustomEmptyView(
      image: "assetSearchNotFound",
      title: "No games found matching '\(presenter.searchQuery)'"
    )
  }
  
  var startSearching: some View {
    VStack {
      Spacer()
      Image(systemName: "magnifyingglass")
        .font(.system(size: 85))
        .foregroundColor(.gray)
      Text("Search for your favorite games")
        .font(.title3)
        .foregroundColor(.gray)
        .padding()
      Spacer()
    }
  }
  
  var gameList: some View {
    ScrollView {
      LazyVStack(spacing: 16) {
        ForEach(presenter.games) { game in
          ZStack {
            self.presenter.linkBuilder(for: game.id) {
              GameRow(game: game)
            }
            .buttonStyle(PlainButtonStyle())
          }
          .padding(.horizontal)
        }
      }
      .padding(.vertical)
    }
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
		B5FD193F2D9DD00900DC56F7 /* GameRespository.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD193E2D9DD00100DC56F7 /* GameRespository.swift */; };
		B5FD19412D9DD0EA00DC56F7 /* LocaleGameDataSource.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19402D9DD0E600DC56F7 /* LocaleGameDataSource.swift */; };
		B5FD19432D9DD11A00DC56F7 /* RemoteGameDataSource.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19422D9DD11800DC56F7 /* RemoteGameDataSource.swift */; };
		B5FD19462D9DD36800DC56F7 /* SDWebImageSwiftUI in Frameworks */ = {isa = PBXBuildFile; productRef = B5FD19452D9DD36800DC56F7 /* SDWebImageSwiftUI */; };
		B5FD19482D9DDFC400DC56F7 /* GameDetailResponse.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19472D9DDFBF00DC56F7 /* GameDetailResponse.swift */; };
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
		B5FD193E2D9DD00100DC56F7 /* GameRespository.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameRespository.swift; sourceTree = "<group>"; };
		B5FD19402D9DD0E600DC56F7 /* LocaleGameDataSource.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = LocaleGameDataSource.swift; sourceTree = "<group>"; };
		B5FD19422D9DD11800DC56F7 /* RemoteGameDataSource.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = RemoteGameDataSource.swift; sourceTree = "<group>"; };
		B5FD19472D9DDFBF00DC56F7 /* GameDetailResponse.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameDetailResponse.swift; sourceTree = "<group>"; };
/* End PBXFileReference section */

/* Begin PBXFrameworksBuildPhase section */
		2A9D4528292C805D008FABEB /* Frameworks */ = {
			isa = PBXFrameworksBuildPhase;
			buildActionMask = 2147483647;
			files = (
				B5FD19462D9DD36800DC56F7 /* SDWebImageSwiftUI in Frameworks */,
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
				B5FD193E2D9DD00100DC56F7 /* GameRespository.swift */,
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
				B5FD19422D9DD11800DC56F7 /* RemoteGameDataSource.swift */,
				2A9D4546292C814E008FABEB /* Response */,
				2A9D4549292C817B008FABEB /* RemoteDataSource.swift */,
			);
			path = Remote;
			sourceTree = "<group>";
		};
		2A9D4546292C814E008FABEB /* Response */ = {
			isa = PBXGroup;
			children = (
				B5FD19472D9DDFBF00DC56F7 /* GameDetailResponse.swift */,
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
				B5FD19402D9DD0E600DC56F7 /* LocaleGameDataSource.swift */,
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
				B5FD19452D9DD36800DC56F7 /* SDWebImageSwiftUI */,
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
				B5FD19442D9DD36800DC56F7 /* XCRemoteSwiftPackageReference "SDWebImageSwiftUI" */,
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
				B5FD19482D9DDFC400DC56F7 /* GameDetailResponse.swift in Sources */,
				2A9D456A292C84C2008FABEB /* CategoryRow.swift in Sources */,
				2A91C79E2935E061005A9155 /* DetailRouter.swift in Sources */,
				B5FD193F2D9DD00900DC56F7 /* GameRespository.swift in Sources */,
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
				B5FD19412D9DD0EA00DC56F7 /* LocaleGameDataSource.swift in Sources */,
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
				B5FD19432D9DD11A00DC56F7 /* RemoteGameDataSource.swift in Sources */,
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
		B5FD19442D9DD36800DC56F7 /* XCRemoteSwiftPackageReference "SDWebImageSwiftUI" */ = {
			isa = XCRemoteSwiftPackageReference;
			repositoryURL = "https://github.com/SDWebImage/SDWebImageSwiftUI.git";
			requirement = {
				kind = upToNextMajorVersion;
				minimumVersion = 3.1.3;
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
		B5FD19452D9DD36800DC56F7 /* SDWebImageSwiftUI */ = {
			isa = XCSwiftPackageProductDependency;
			package = B5FD19442D9DD36800DC56F7 /* XCRemoteSwiftPackageReference "SDWebImageSwiftUI" */;
			productName = SDWebImageSwiftUI;
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
  "originHash" : "a1a1061106ac97c26bec79dad5ef007c9094b0aaf3572f79960d081c60d51db8",
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
      "identity" : "sdwebimage",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/SDWebImage/SDWebImage.git",
      "state" : {
        "revision" : "cac9a55a3ae92478a2c95042dcc8d9695d2129ca",
        "version" : "5.21.0"
      }
    },
    {
      "identity" : "sdwebimageswiftui",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/SDWebImage/SDWebImageSwiftUI.git",
      "state" : {
        "revision" : "451c6dfd5ecec2cf626d1d9ca81c2d4a60355172",
        "version" : "3.1.3"
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
  "version" : 3
}


/== TheMealsApp.xcodeproj/project.xcworkspace/xcuserdata/ben.xcuserdatad/UserInterfaceState.xcuserstate
bplist00�        
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�  	UState���      9 : ; < = > ? @ A B C D E F G T Z [ a e f j k o p t u y z ~  � � � � � � � � � � � � �	!"&'+,0156:;?@DEIJNOSTXY]^bcghlmqrvw{|����������������������������������������������	 !"%=>?@ABCDEFGKRZ[\fghimqyz{|�����������������������������������	
#$%/0126:BCDLMefghijklmnosz{|�������������������������������������()*+,-./0126=EFGQRSTX\defno�������������������������������������������%&'()12JKLMNOPQRSTX_`hijrst~����������������������������������� !)*+345?@ABFJRSklmnopqrstuy�������������������������������������� 	
-./01234567;BCDLMNVWXbcdeimnvw��������������������������������������� $,-.67OPQRSTUVWXY]demnoyz{|����������������������������������													#	+	,	-	5	6	7	A	B	C	D	H	L	T	U	m	n	o	p	q	r	s	t	u	v	w	{	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�




	



.
/
0
1
2
3
4
5
6
7
8
<
C
D
L
M
N
V
W
X
b
c
d
e
i
m
u
v
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�!"#$(,45MNOPQRSTUVW[bcklmwxyz~�������������������������������������%&'/01;<=>BFNOPXYqrstuvwxyz{�������������������������������������	23456789:;<@GHPQRZ[\fghimqyz��������������������������������������� &'()-19:RSTUVWXYZ[\`gopqyz{������������������������������������� '(012<=>?CGOPQRZ[stuvwxyz{|}������������������������������������	23456789:;<@GOPQ[\]^bfnopxy��������������������������������������"#$%)-56NOPQRSTUVWX\cklmuvw�����������������������������������!"#+,-789:>BJKLTUmnopqrstuvw{������������������������������������� "&*/56:@CFL`abcdefghlmquvwx|�������������������������������������� 	
 &01234>?BRSTUVYZnopqrstuvw������������������������������������������ !%&'345678DEFGHNOPQWX\]cdhijrstwx~��������������������������������� 
#$()-.237;<AEFG_`abcdefghimtu}~������������������������������������� !*./348<@DEIJbcdefghijklpw��������������������������������� !",-./37?@AJQX\]abfjkop����������������������������������� 	%&'()*+,-./3:BCDLMWXYZ^bjklpqx�������������������������������������� 	$%&U$null�      WNS.keysZNS.objectsV$class�  ���  �� ��_IDEWorkspaceDocument_$9229D32F-C4FF-43F2-A95F-816FAA71C582�     ) 8�        ! " # $ % & ' (�����	�
��������� * + , - . / 0 1 2 3 - 5 6 0��*���������ÁƁՁ���耆��_RecentEditorDocumentURLs_DefaultEditorStatesForURLs\ActiveScheme_ActiveProjectSetIdentifierKey_$RunDestinationArchitectureVisibility_DocumentWindows_EnableThreadGallery_WindowArrangementDebugInfo_RunContextRecents_ActiveRunDestination_ActiveProjectSetNameKey_SelectedWindows_0LastCompletedPersistentSchemeBasedActivityReport_BreakpointsActivated�   H S� I J K L M N O P Q R�������!�#�%�'�)� U  V W X YWNS.base[NS.relative� ��_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift� \ ] ^ _Z$classnameX$classesUNSURL� ^ `XNSObject� U  V W X d� ��_Zfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Assets.xcassets� U  V W X i� ��_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/ContentView.swift� U  V W X n� ��_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift� U  V W X s� ��_tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift� U  V W X x� �� _vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteGameDataSource.swift� U  V W X }� ��"_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swift� U  V W X �� ��$_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swift� U  V W X �� ��&_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swift� U  V W X �� ��(_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/GameMapper.swift� \ ] � �WNSArray� � `�    � � 8� � � � ��+�,�-�.� � � � ��/�{������_-Xcode.IDEKit.EditorDocument.PegasusSourceCode_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project_'Xcode.IDEKit.EditorDocument.LogDocument_(Xcode.IDEKit.EditorDocument.AssetCatalog�    � � 8�- � � � � � N � � Q � � � � K � � P � L � O � � R � � M � � � � � � � � I � � � � � � � � π0�2�4�6�8��:�<�%�>�@�B�D��F�H�#�J��L�!�N�P�'�R�T��V�X�Z�\�^�`�b�d��f�h�j�l�n�p�r�t�v�- � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � ��x�����ۀ���3�N�j�����ށ���6�Q�n�������݁���1�L�i�������݁���1�L�i�������؁��(�C�`��� U  V W X� ��1_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift� U  V W X� ��3_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameDetailModel.swift� U  V W X� ��5_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleGameDataSource.swift� U  V W X� ��7_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swift� U  V W X� ��9_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GameDetailResponse.swift� U  V W X� ��;_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swift� U  V W X � ��=_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift� U  V W X%� ��?_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift� U  V W X*� ��A_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swift� U  V W X/� ��C_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swift� U  V W X4� ��E_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift� U  V W X9� ��G_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swift� U  V W X>� ��I_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swift� U  V W XC� ��K_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swift� U  V W XH� ��M_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift� U  V W XM� ��O_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel.swift� U  V W XR� ��Q_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/DI/Injection.swift� U  V W XW� ��S_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swift� U  V W X\� ��U_|file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift� U  V W Xa� ��W_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity.swift� U  V W Xf� ��Y_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GamesResponse.swift� U  V W Xk� ��[_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/GameRespository.swift� U  V W Xp� ��]_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swift� U  V W Xu� ��__pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swift� U  V W Xz� ��a_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swift� U  V W X� ��c_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swift� U  V W X�� ��e_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swift� U  V W X�� ��g_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel%202.swift� U  V W X�� ��i_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swift� U  V W X�� ��k_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift� U  V W X�� ��m_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/Untitled.swift� U  V W X�� ��o_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift� U  V W X�� ��q_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift� U  V W X�� ��s_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift� U  V W X�� ��u_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity%202.swift� U  V W X�� ��w_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift�   �� 8������������y�z�{�|�}�~��������� 0���� .�� 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�׀��)�  � S� . . . .���������) 	�   �� ��怉����逋����_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���񀌀���������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO!v��<I���x��4U��� \ ]� VNSUUID�� `� �\NS.uuidbytesO��8���I���c�:�4��#@d�     � \ ]\NSDictionary� `�   
 ������������[lineIndexes^documentLength�   S��)U#        �    ������������Ufolds^documentLength� \ ]#$_NSMutableDictionary�# `�   &1 8�'()*+,-./0���������������������2 0��67 .�: 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  H S�I���)�  L S� . . . .���������)�   SV �TU�����WX������_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   ]a �^_`�������bc���������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�j k�\NS.uuidbytesO�V�	M�D��w �)^Z9���n o�\NS.uuidbytesO�A�^$lC���I�|a݀��   ru �s�����v������^documentLength�#��      �   }� ������v������^documentLength�   �� 8��������������������������ÀĪ� 0������� 0�ŀ������ʀ׀؀��ـ���_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���ƀ)�  � S���� .�ǀȀɀ��)eq�   �� ����ˀ̢���̀Ӏ�_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���À΀πУ��ǀр��Ҁ�_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore�� ��\NS.uuidbytesO6�\׸zLլ�l'^#������ ��\NS.uuidbytesO�g��m�E��*-��,����   �� �׀��Ԣ�ڀՀր�^documentLength�  � S��)�#@      F�   �� �怛�ڢ�ڀՀր�^documentLength�   �� 8������������܀݀ހ߀�������� 0���� .� 0�怆�����ꀙ����������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S���)�   S������)K�    ���� !���_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   &* �'()����+�-�񀒀�_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore�3 4�\NS.uuidbytesO��C7��E���;� ?�����7 8�\NS.uuidbytesOP����GO��L�t0�v���   ;> �=������@�Հ���^documentLengthF�   EH �G������@�Հ���^documentLength�   NY 8�OPQRSTUVWX��������������� ���Z 0��^7 .�b 0�������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  p S�q��)�  t S�uvuv�����)f�   }� �~��	����
���_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ���������Ձ��^documentLength	_�   �� �����������������_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�� ��\NS.uuidbytesOoݖo�"Jn���T�!���� ��\NS.uuidbytesOrubY�bJs��܎vF񇀐�   �� ���������Ձ��^documentLength�   �� 8����������������������� 0������� 0� �������#�/�0���1����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ҁ!�)�  � S��v�v�"��"��)L�   �� ��߁$�%���&�,��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ����'�(�)����*�+��_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�� ��\NS.uuidbytesO��,�	�M=��3&^P쀐�� ��\NS.uuidbytesOjm��6@Û��xDހ��   �� �����-���Ձ.��^documentLength	f#@1      �    �
���2���Ձ.��^documentLength�    8��4�5�6�7�8�9�:�;�<�=� 0��!7 .�% 0�>�������@�������L����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  3 S�4�?�)�  7 S� . . . .���������)�   >A �?@�A�B�BC�C�I��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   HL �IJK�D�E�F�MN��G�H����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�U V�\NS.uuidbytesONx\��AN���`�<J	̀��Y Z�\NS.uuidbytesOט����CP�cZVတ�   ]` �^�J���a�K����^documentLength��   gj �i���M�a���K��^documentLength�   p{ 8�qrstuvwxyz�O�P�Q�R�S�T�U�V�W�X�| 0���� .�� 0�Y�������\�������h����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���Z�)�  � S������0�[�0�[�)�   �� ����]�^����_�b��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ���`����فa�Հ�^documentLength��   �� �����c�d�e�����f�g����_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�L�(�9DF���я������� ��\NS.uuidbytesOU���O�M�����n'����   �� �ɀ��i����Ձa��^documentLength�   �� 8����������ځk�l�m�n�o�p�q�r�s�t�� 0������� 0�u�������y������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S��v�)�  � S������w�x�w�x�)G�   � � �z�{��|����_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   	 �
�}�~�����������_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController� �\NS.uuidbytesO�>�KhXLүl��������� �\NS.uuidbytesO/��08@�c���Ў���   ! � ������#�Ձ���^documentLength##?�      �   *- �,������#�Ձ���^documentLength�   3> 8�456789:;<=���������������������? 0��C7 .�G 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  U S�V���)�  Y S�ZvZv�������)�   ad �bc�����ef������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   kn �l�����oف��Հ�^documentLength��   uy �vwx�������z{���������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��<�"Kj��`P[:����� ��\NS.uuidbytesO���o��K)����#p�K���   �� ��������o�Ձ���^documentLength�   �� 8��������������������������������� 0������� 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S� . . . .���������)�   �� �������Ł�����_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���́�����������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�I�0>�Kܭ(�d�sk���� ��\NS.uuidbytesO��~�BO�IT�\W�����   �� ��������������^documentLength�#@,      ��   �� �퀛���〗����^documentLength�   �� 8������������ÁāŁƁǁȁɁʁˁ̪  0��7 .� 0�̀������Ѐ������܀���_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S��΀)�   S�vv�ρ�ρ�)�   "% �#$�сҢ&'�Ӂր�_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ,/ �.���Ԣ�1�ՁՀ�^documentLengthf�   6: �789�ׁ؁٣�<=���ځۀ�_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore�C D�\NS.uuidbytesON��4�jDף��C{b����G H�\NS.uuidbytesO+�˙l�@ݿ*=_i0GD���   KN �M���ݢ�1�ՁՀ�^documentLength�   T_ 8�UVWXYZ[\]^�߁����������` 0��d7 .�h 0�逆�����뀷����������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  v S�w��)�  z S�vv�ρ�ρ�)�   �� �����������_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �����������������_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore�� ��\NS.uuidbytesO�#��I��Y�@��z���� ��\NS.uuidbytesO	��&�L��dW�"�w	���   �� ����������Ձ���^documentLengthj�   �� ����������Ձ���^documentLength�   �� 8������������������������ ����� 0������� 0�����������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ց�)�  � S����ہ����)�   �� ���	�
������_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���������󀒁���_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�� ��\NS.uuidbytesO�._�gII����k1M���� ��\NS.uuidbytesOyiW�� AJ��#N��m���    �������Ձ��^documentLength�#@*      �    �������Ձ��^documentLength�   ! 8� �������� �!�"�" 0��&�(�* 0�#�������'�ׁ3���4����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  8 S�9�$�)�  < S�=>=>�%�&�%�&�)�+�   EH �FG�(�)�IJ�*�-��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   OR �P�+���Sف,�Հ�^documentLength3��   Y] �Z[\�.�/�0�^_��1�2����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�f g�\NS.uuidbytesO/��!]GJ��AS`6]���j k�\NS.uuidbytesO~՜S�J$��+My{SV����   or �q���5��S�Ձ,��^documentLength�   x� 8�yz{|}~����7�8�9�:�;�<�=�>�?�@�� 0���7 .�� 0�A�������C�������O����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���B�)�  � S� . . . .���������)�   �� ����D�E����F�L��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �����G�H�I�����J�K����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��{-��A�d�;�V&���� ��\NS.uuidbytesO�^��DƷy��8�'���   �� �ƀ��M�ɀ��N��^documentLength��   �� �Ѐ��P�ɀ��N��^documentLength�   �� 8�����������R�S�T�U�V�W�X�Y�Z�[�� 0������� 0�\�������`������l����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���]�)�  � S������^�_�^�_�)!�   	 ��a�b�
�c�i��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�    ��d�e�f���g�h����_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width� �\NS.uuidbytesO���z��KO���X�,1���! "�\NS.uuidbytesOB�k�E��/h��܌���   %( �'���j��*�Ձk��^documentLength��   /2 �1���m��*�Ձk��^documentLength�   8C 8�9:;<=>?@AB�o�p�q�r�s�t�u�v�w�x�D 0��H� .�L 0�y�������|������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  Z S�[�z�)�  ^ S�_v_v�{��{��)��   fi �gh�}�~�jk�����_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   pt �qrs�������uv���������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�} ~�\NS.uuidbytesO�I_�
nK�X��Mp?Ҁ��� ��\NS.uuidbytesOB+�̛Dc�cpx�)�����   �� ����������Ձ���^documentLength�   �� ����������Ձ���^documentLength�   �� 8��������������������������������� 0���7 .�� 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S�������ȁ�Ȁ)�   �� ��ǁ�����ʁ�����_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���ҁ�����������������_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO@jH��3IM�"�Tۡpz���� ��\NS.uuidbytesOpSz�n Bl��C)T�*���   �� ��������ف��Հ�^documentLengthb�   �� ���������Ձ���^documentLength�   �	 8���������	 	���������������������	 0��	7 .�	 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	 S�	���)�  	 S�ZvZv�������)�   	$	' �	%	&�����	(	)������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   	.	1 �	0������	3�Ձ���^documentLength��   	8	< �	9	:	;�������	=�	?��������_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�	E 	F�\NS.uuidbytesO�ip���N����LM_���	I 	J�\NS.uuidbytesO.,qF�J^���H
�؀��   	M	P �	O������	3�Ձ���^documentLength�   	V	a 8�	W	X	Y	Z	[	\	]	^	_	`���ÁāŁƁǁȁɁʪ	b 0��	f�	h�	j 0�ˀ������΁/�ڀ��ۀ���_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	x S�	y�̀)�  	| S�	} .	} .�̀��̀��) �   	�	� �	�	��ρТ	�	��сԀ�_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   	�	� �	����Ң�	��ՁӀ�^documentLength��   	�	� �	�	�	��Ձցף�	�	����؁ـ�_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�	� 	��\NS.uuidbytesO�~�� B����/UA���	� 	��\NS.uuidbytesO���N�N��$]��j��	�   	�	� �	����ܢ�	��ՁӀ�^documentLength�   	�	� 8�	�	�	�	�	�	�	�	�	�	��ށ߁���������	� 0��	�7 .�	� 0�耆�����뀷����������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	� S�	ځ�)�  	� S�	�v	�v�����),�   	�	� �	�	���	�	���_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   	�	� �	���	�ف��Հ�^documentLength��   	�	� �	�	�	������	��
 ��������_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�
 
�\NS.uuidbytesO�"֯�O�-J<��I����

 
�\NS.uuidbytesOpYX�K7B������4���   

 �
������	�Ձ���^documentLength�   

" 8�








 
!������������� ����
# 0��
'� .�
+ 0�������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
9 S�
:��)�  
= S�
>v
>v�����))�   
E
H �
F
G��	�
I
J�
���_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   
O
R �
Q�����
T�Ձ��^documentLength@�   
Y
] �
Z
[
\����
^�
`������_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore�
f 
g�\NS.uuidbytesO�'#�>6K{��*���#���
j 
k�\NS.uuidbytesO�Lv��uJ<��P�V���   
n
q �
p�����
T�Ձ��^documentLength�   
w
� 8�
x
y
z
{
|
}
~

�
������������
� 0��
�7 .�
� 0� �������#�������/����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
� S�
��!�)�  
� S�
��
�Ł"��"��)�   
�
� �
�
��$�%�
�
��&�)��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   
�
� �
��'���
�ف(�Հ�^documentLength/�   
�
� �
�
�
��*�+�,�
��
��-���.��_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�
� 
��\NS.uuidbytesO����IݿdʪIـ��
� 
��\NS.uuidbytesO	�^K�C����#
������   
�
� �
Ѐ��0��
��Ձ(��^documentLength�   
�
� 8�
�
�
�
�
�
�
�
�
�
�2�3�4�5�6�7�8�9�:�;�
� 0��
�7 .�
� 0�<�������>�������J����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
� S�
��=�)�  
� S� . . . .���������)�    ��?�@�	�A�D��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ��B���فC�Հ�^documentLengthm�    ��E�F�G���H���I��_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�% &�\NS.uuidbytesO�10I��M����G������) *�\NS.uuidbytesOcC�CNڜ�'����   -0 �/���K���ՁC��^documentLength�   6A 8�789:;<=>?@�M�N�O�P�Q�R�S�T�U�V�B 0��FG��J 0�W�������Z�f����g����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  X S�Y�X�)�  \ S�]v]v�Y��Y��)2�   dg �ef�[�\�hi�]�c��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   nr �opq�^�_�`�s�u�a���b��_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�{ |�\NS.uuidbytesOj��MMǮ�C�@\W��� ��\NS.uuidbytesOah��bB��� X�:�c���   �� ���d����فe�Հ�^documentLength	#@      �   �� �����h����Ձe��^documentLength�   �� 8������������j�k�l�m�n�o�p�q�r�s�� 0������� 0�t�������x�����������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���u�)�  � S������v�w�v�w�)0;�   �� ��ȁy�z��ˁ{�~��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� �Ҁ��|��ՀՁ}��^documentLength��   �� ���݁����������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�mFl�M,������dŀ��� ��\NS.uuidbytesO�M�8��B���0��M��#@      �   �� �򀛁���ՀՁ}��^documentLength�   � 8������� ��������������������� 0��	7 .� 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S����)�   S� ! !���������)&�   (+ �)*�����,-������_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   26 �345�������78���������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�? @�\NS.uuidbytesO[W�(P�H���j�,ִ���C D�\NS.uuidbytesOQH_@C��g��d����   GJ �I������L�Ձ���^documentLength�   QT �S������L�Ձ���^documentLength�   Ze 8�[\]^_`abcd���������������������f 0��jk�n 0�������������耇������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  | S�}���)�  � S��v�v�������)@�   �� ����������������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��������������^documentLength/�   �� ����������������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�����BG�a�}��u����� ��\NS.uuidbytesOK��8�HW����tF����#@       �   �� ��������������^documentLength�   �� 8����������ŁÁāŁƁǁȁɁʁ˪� 0���7 .�� 0�̀������π������ۀ���_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ށ̀)�  � S��v�v�΁�΁�)~�   �� ���ЁѢ��ҁՀ�_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� �����Ӣ���ՁԀ�^documentLength��   � ��� �ցׁأ��ـ��ڀ�_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore�
 �\NS.uuidbytesO5�|�H����JφE��� �\NS.uuidbytesO��4�e�E��cOd/���    ����ܢ���ՁԀ�^documentLength�   & 8� !"#$%�ށ߁���������' 0��+���/ 0�耆������ׁ��������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  = S�>��)�  A S�BvBv�����)<�   IL �JK���MN���_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   SV �U�����X�Ձ���^documentLength@�   ]a �^_`�����b�d��������_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�j k�\NS.uuidbytesOĀK�"EJ��Q��r8���n o�\NS.uuidbytesO���:��@�̼�o�JS���   ru �t������X�Ձ���^documentLength�   {� 8�|}~������������������� ����� 0���� .�� 0�������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�  � S��v�v�����)�   �� �����	����
���_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �����������������_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�� ��\NS.uuidbytesO�|�D��Am����y|����� ��\NS.uuidbytesO�� �V�FJ��;D���<���   �� �������ف�Հ�^documentLength��   �� �Ԁ����̀Ձ��^documentLength�   �� 8���������������������� 0���7 .�� 0� �������#�������/����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���!�)�   S��Ł"��"��)�   	 �
�$�%��&�)��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ��'���ف(�Հ�^documentLength��   ! � �*�+�,�"#��-�.����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�* +�\NS.uuidbytesO�Gy�1�G���z���X���. /�\NS.uuidbytesOM�=�!�CE��֤���=���   25 �4���0���Ձ(��^documentLength�   ;F 8�<=>?@ABCDE�2�3�4�5�6�7�8�9�:�;�G 0��K7 .�O 0�<�������>�������J����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ] S�^�=�)�  a S� . . . .���������)�   hk �ij�?�@�lm�A�D��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ru �s�B���v�C����^documentLength��   |� �}~�E�F�G�����H�I����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOd D���Aޝ9��⛒���� ��\NS.uuidbytesO�w[�B'�$.�y��3���   �� �����K�v���C��^documentLength�   �� 8������������M�N�O�P�Q�R�S�T�U�V�� 0������� 0�W�������Z�ׁf���g����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���X�)�  � S��v�v�Y��Y��)J�   �� ��ʁ[�\��́]�c��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���Ձ^�_�`���ـ��a�b��_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�� ��\NS.uuidbytesOEA��OJ����G(�J���� ��\NS.uuidbytesO	�X�M�Z��3�a���   �� �逕�d���Ձe��^documentLength�(�   �� �􀛁h���Ձe��^documentLength�   � 8����� �j�k�l�m�n�o�p�q�r�s� 0��
�� 0�t�������w���"��������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S��u�)�  ! S�"�"��v�w�v�w�)5�   ), �*+�x�y�-.�z����_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   37 �456�{�|�}�8�:�~�����_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore�@ A�\NS.uuidbytesO!rJr�F���u��j���D E�\NS.uuidbytesO+�B��N���%�Rs����   HK �J������M�Ձ���^documentLength#@       �   SV �U������M�Ձ���^documentLength�   \g 8�]^_`abcdef���������������������h 0��l7 .�p 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ~ S����)�  � S� . . . .���������)�   �� ����������������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��������������^documentLength��   �� ����������������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO&�,���K̡��MT�E�� ��\NS.uuidbytesO��#�?J�������,���   �� ��������������^documentLength�   �� 8����������Ł��������������������� 0���� .�� 0����������������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ށ��)�  � S�������������)�   �� ��������������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��������ف��Հ�^documentLength
�   � ��� ����������������_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�
 �\NS.uuidbytesOT�7�=Iu���Փ-]Ā�� �\NS.uuidbytesO����H[�=�Q.Y�׀��    ���������Ձ���^documentLength�   & 8� !"#$%���������ÁāŁƁǪ' 0��+7 .�/ 0�Ȁ������ʀ������ր���_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  = S�>�ɀ)�  A S�ZvZv�������)�   HK �IJ�ˁ̢LM�́Ӏ�_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   RV �STU�΁ρУW�Y�р��Ҁ�_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore�_ `�\NS.uuidbytesO�}��M��V����c d�\NS.uuidbytesOt�'p@;�0+(����   gj �i���Ԣ�l�ՁՀ�^documentLength
�   qt �s���ע�l�ՁՀ�^documentLength�   z� 8�{|}~������فځہ܁݁ށ߁����� 0���7 .�� 0�〆�����倷����������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�  � S� . . . .���������)�   �� ����������_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �������������퀒��_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO*ExV�E ��>� D���� ��\NS.uuidbytesOo{Q'��O�4Z �V9����   �� ����v�����^documentLength�   �� �р���v�����^documentLength�   �� 8������������������������������ 0������ 0������������π������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S��v�v�Y��Y��)�    �� ��	
����_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    �������Ձ��^documentLengthq�    �������	�
����_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width�& '�\NS.uuidbytesO��mο�LL���g �ʀ��* +�\NS.uuidbytesO� �tDe��h��=�6���   .1 �0������Ձ��^documentLength�   7B 8�89:;<=>?@A�����������C 0��G� .�K 0���������������&����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  Y S�Z��)�  ] S�]v]v�Y��Y��)�   dg �ef���hi�� ��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   nq �p�����s�Ձ��^documentLength��   x| �yz{�!�"�#��~���$�%��_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore�� ��\NS.uuidbytesO8:̹HLH��ɯ������� ��\NS.uuidbytesO�rƏF����ؘԀ��   �� �����'��s�Ձ��^documentLength�   �� 8������������)�*�+�,�-�.�/�0�1�2�� 0���7 .�� 0�3�������5�������A����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���4�)�  � S� . . . .���������)�   �� ��Ł6�7��ȁ8�;��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ���9�����:����^documentLength��   �� ���ځ<�=�>�����?�@����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO|R�q��BV�c��� ��\NS.uuidbytesO���5�#B$�n�O�ڀ��   �� ��B�р��:��^documentLength�   �  8������������D�E�F�G�H�I�J�K�L�M� 0��� .�	 0�N�������R�������^����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S��O�)�   S��P�Q�P�Q�)
�   $' �%&�S�T�()�U�[��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   .2 �/01�V�W�X�34��Y�Z����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�; <�\NS.uuidbytesO�U^q3[B��	.ii�����? @�\NS.uuidbytesO���b��Op�����a�D���   CF �D�\���G�]����^documentLength$�   MP �O���_�G���]��^documentLength�   Va 8�WXYZ[\]^_`�a�b�c�d�e�f�g�h�i�j�b 0��f7 .�j 0�k�������m�������y����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  x S�y�l�)�  | S�	}	}�P�́P�̀)�   �� ����n�o����p�s��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ���q�����r����^documentLength�   �� �����t�u�v�����w�x����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�pS<�K��L�wh������ ��\NS.uuidbytesO��B�:YG��� ��ǀ��   �� �����z�����r��^documentLength�   �� 8���|���~��� U  V W X�� ��}_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj�   �� 8����ā����������Ɂ���������_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_&Xcode3ProjectEditor_Xcode3TargetEditor_Xcode3ProjectInfoEditor_Xcode3TargetEditor�  � S�Ӂ��)���� ����YselectionYtimestamp[documentURL��������_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj#A�Ϊ����   �� 8��������������VEditorVTarget_Xcode3TargetEditor[TheMealsApp� \ ]��_Xcode3ProjectDocumentLocation��� `_Xcode3ProjectDocumentLocation_DVTDocumentLocation�   �� 8�����   �� 8����������������� U  V W X�� ���_2x-xcode-log://DE4259B5-7817-44E7-8091-BC59E4CDE608� U  V W X� ���_2x-xcode-log://55CEB2A9-122B-4C7C-8B4C-1B6CFB5F6402�   	 8����
����_SelectedDocumentLocations�   S����)� �� W��_expandTranscriptYindexPath��� ����� �_NSIndexPathLength_NSIndexPathData�����  !WNS.dataB��� \ ]#$]NSMutableData�#% `VNSData� \ ]'([NSIndexPath�) `[NSIndexPath� \ ]+,_IDELogDocumentLocation�-. `_IDELogDocumentLocation_DVTDocumentLocation�   02 8�1���3����_SelectedDocumentLocations�  7 S�8���)� �� W�?��� ����� �A����� D!B ���   GI 8� J��J�����   MV 8�NOPQRSTU�����������������WXYZ[\]^������������������_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area�  ik�j����_B./assetNoFavorite.imageset/[universal][][][1x][][][][][][][][][][]� \ ]no\NSMutableSet�np `UNSSet�  rk�s����_./assetNoFavorite.imageset_IBICCatalogOverviewController#@l�     �   yz 8�����   } 8�~��������_expandedItemIDs�  ��������Q.� \ ]p��p `ZdetailArea�   �� 8�����   �� ����������]IDENameString[TheMealsApp� ���� \ ]��VNSNull�� `�  � S� ��)�   �� 8� ����Ā��  ��� ���� \ ]��^NSMutableArray�� � `�   �� �����ǁȁɣ����ʁ΁Ҁ�_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKey�   �� 8���ˡ��̀�[TheMealsApp�� ��WNS.time#A���0�� \ ]��VNSDate�� `�   �� 8�ʁϡ́Ѐ�[TheMealsApp� ���YNS.string��_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64� \ ]��_NSMutableString��� `XNSString�   �� 8�فӡہԀ�_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64�� ��#A���1'�O���   �� �����������ցׁ؁فځہ܁݁ށߪ 0� 0���������������������怓ZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:7A60B588-4926-4A88-9679-4FA36F2ECDD0Vx86_64_iphonesimulator18.2ViPhoneZiPhone17,1�  �����    8�������������_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildX16C5032a��  !��"#$��������   '+ 8�()*�����k-�����퀝_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ �   59 8�()*������;<��������c  %  � @!Obplist00�
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	Troot��)*0:;<#=AIJKLMSWX\_U$null�XNSStringV$class\NSAttributes���YSucceeded�WNS.keysZNS.objects�����VNSFont� !"#$%&'(VNSSizeXNSfFlags\NSDescriptorZNSHasWidthVNSName#@&      ����_.AppleSystemUIFontBold�+,-./_NSFontDescriptorOptions_NSFontDescriptorAttributes�����15�234�	�
��678����_NSFontSizeAttribute_ NSCTFontFeatureSettingsAttribute_NSCTFontUIUsageAttribute�>@�?���BE�CD���FG���_CTFeatureSelectorIdentifier_CTFeatureTypeIdentifier �NOPQZ$classnameX$classes\NSDictionary�PRXNSObject�NOTU^NSMutableArray�TVRWNSArray_CTFontBoldUsage�NOYZ_NSFontDescriptor�[R_NSFontDescriptor�NO]^VNSFont�]R�NO`a_NSAttributedString�bR_NSAttributedString    $ ) 2 7 I L Q S o u | � � � � � � � � � � � � � � � � � � � �	!#<C]z|�������������������	4NPRWbkx{��������������             c              %���   CJ 8�(EF*HI�����������GvMNvv�f���������_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle�� W�#A���x`�}��o T o d a y   a t   5 . 0 8 / A M�   [d 8�\]^_`abc��������� 0f 0h�_ 0��	���
���������_IDEWindowIsFullScreen^IDEWindowFrame_-IDEHasMigratedValuesFromNSRestorableStateData_>IDEWorkspaceTabController_75DACF4F-9477-44F2-97BD-12677AFE5FB7_&IDEWindowTabBarWasVisibleWithSingleTab_IDEActiveWorkspaceTabController_IDEWindowToolbarIsVisible_IDEWindowTabBarIsVisible_209 109 1400 900 0 0 1680 1050 �   x� 8�yz{|}~������������ 0������������Z�[�_�`�b���e��_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorArea�   �� 8����������������������'�7�B�C�I��_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Find_"Xcode.IDEKit.Navigator.Test.Modern_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigator�   �� 8��������������� �!�"� ��_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey�   �� ����_IDENavigatorModeSolitary�   �� �Á#�Ł$��_IDENavigatorModeSolitary� ���_codablePlistRepresentation�&�%O%bplist00���_lastAccessedDateYitemState]selectedItems^scrollPosition3A�����L!#%'),.358:>@CEHJNPRTVXZ\^`cegikmoqsuxz}������������������������������	
TpathYindexHint�[TheMealsAppVModuleVSearchVRouter�^expansionState3A���"��i �	�XFavorite�3A���"��'�	�TCoreTDataVRemoteXResponse� 3A���"��	"��$3A���"��h�	&��(3A���2;p��	*�+_Package Dependencies�-3A���"���	/2�01UUtilsWNetwork�43A���"�R�	6�7RDI�93A���"�!�	;=�<THome�?3A���"��\�	A=�<BTView�D3A���"�Ž�	F=�GBVDetail�I3A���"��	K�LMVDomainUModel�O3A���"���	Q=�B�S3A���"����	U��W3A���"��`�	Y=�B�[3A���"��s�	]=��_3A���"�b �	a�GbYPresenter�d3A���"�n��	f�<b�h3A���"��`�	j��l3A���"����	n�G�p3A���"�k��	r��t3A���"�j��	v�LwWUseCase�y3A���"�-4�	{�|VLocale�~3A���1�W�	�����3A���"����	�=�L��3A���"�Z�	��b��3A���"�ޔ�	��b��3A���"����	��0B��3A���"�B��	�2��TMeal��3A���"���	����3A���"����	���SApp��3A���"��{�	�2�0��3A���"�9��	��|�VEntity��3A���0����	��|���3A���"���	�=�0�VMapper��3A���"�N��	��<��3A���"��1�	��G��3A���"�t�	����3A���"�ӕ���	��<B�^HomeView.swift��Ȣ��#        #�$      ���#@t�     #@��        $ . < K T � � � � � � � � � � � � � �	 %,16=FKTY_dmry~�������������������!&+49?FKTY_flqz��������������������#,16;DIKPY^dlqz���������������������	!&+49?DMRVZ_hmrw���������������������%',3BEHQZ]f             �              o� \ ]��_&ExplorableOutlineViewArchivableUIState��� `_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier_�   �� 8��Ձ(�)��؁*�6��_queryParametersController]filterPattern�   �� 8�����+�,�-����.�/�5��_lastEasyToInitiateQueryClass_querySpecification[queryAction_IDEBatchFindTextQuery���� � W��YqueryTermZnamedScopeZqueryClass�0� �3�4� �����TtextXtermType�2�1� �����Wmissing� \ ]��_IDEBatchFindQueryTerm�� `_IDEBatchFindQueryTerm_IDEBatchFindTextQuery� \ ] _IDEBatchFindQuerySpecification� `_IDEBatchFindQuerySpecificationTfindP�    8�	
�8�9�:�;��<�=�>�<��_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey�    ����_IDENavigatorModeSolitary�    ��?��@��_IDENavigatorModeSolitary� "�$_codablePlistRepresentation�&�AO�bplist00�_lastAccessedDateYitemState]selectedItems^scrollPosition3A�Ν�Յ����	�
#        #�$      �#@q      #@��     $.<KTUVY\enqz                            �_ Xcode.IDEKit.Navigator.Workspace�   (- 8�)*+,�D�E�F�G�.����H�B����_"Xcode.IDEKit.NavigatorGroup.Issues_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_ Xcode.IDEKit.NavigatorGroup.Test_#Xcode.IDENoticesKit.NoticeNavigator�   9> 8�:;<=�J�K�L�M�?@AB�N�Q�R�V��_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey�   IK �J�O�L�P��_IDENavigatorModeSolitaryOnbplist00�
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	Troot��!$'+,U$null�V$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiers� ����ZNS.objects���Z$classnameX$classesWNSArray� XNSObject�"#���%&^NSMutableArray�% �(#�)��_IDEFilterIdentifier_NoticeError�-.__DVTFilterExpressionStateValue�/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u � � � � � � � � � � � � � � �!&59>@BDfk����             2              �_IDENavigatorModeSolitary�   RT �S�S�U�T��_IDENavigatorModeSolitary� Y�[_codablePlistRepresentation�&�UO_bplist00�_lastAccessedDateYitemState]selectedItems^scrollPosition3A�����b��	
TpathYindexHint�[TheMealsApp_$Missing package product 'RealmSwift' �^expansionState3A�������	��3A������7���#        #�$      �#@q      #@��        $ . < K T Y ^ c m p | � � � � � � � � � � � � � � � �                           �   ^` �_�W�a�X��_IDENavigatorModeSolitary� e�g_codablePlistRepresentation�&�YO4bplist00�_lastAccessedDateYitemState]selectedItems^scrollPosition3A����׀��	
TpathYindexHint�[TheMealsApp �^expansionState3A�����<��	�_RemoteDataSource.swift�3A����?���#        #�$      �#@�d     #@��     $.<KTY^cmo{}���������������                            �#@p@     �   kn 8�lm�\�]�p���^��_'userPreferredInspectorGroupExtensionIDs_!userPreferredCategoryExtensionIDs�  u����#@t�     �   y{ 8�z�a� 0����_ShowsOnlyVisibleViewObjects�   �� 8����c�d�������_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspace�   �� 8�������������f�g�h�i�j�k�l�m�n�o�p�� .����������q���r�s�t�u�|����������_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ DefaultPersistentRepresentations_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_DebuggerSplitView^MaximizedState_IDEDefaultDebugArea_ EditorMultipleSplitPrimaryLayout�� {�_NSIndexPathValue��ZOpenInTabs �   �� 8�����  �����v���   �� 8������w�x�y�z�{�|������}�~�R�}�{����ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments�   �� 8��Ձ���� .������_TabsAsHistoryItems_SelectedTabIndex�  � S����������ف�+�)����� ��������� I�_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifier������������������� ������� ._DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifier������������_/Xcode.IDENavigableItemDomain.WorkspaceStructure�   S��������������)� � .ZIdentifierUIndex������^HomeView.swift� \ ]_IDEArchivableStringIndexPair� `_IDEArchivableStringIndexPair� ����ȁ�TView� ����ȁ�THome�  ����ȁ�VModule� % .������[TheMealsApp� * .������[TheMealsApp�� �/0 W����� _jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift� \ ]45_DVTDocumentLocation�6 `_DVTDocumentLocation� 89:ZpathString����_M/Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj� \ ]=>[DVTFilePath�?@ `[DVTFilePath_PackedPathEntry� \ ]BC_(IDENavigableItemArchivableRepresentation�D `_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCode�   HS 8�IJKLMNOPQR���������������������T 0��X
��\ 0�������������"��������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  j S�k���)�  n S�	�p	�������^�)�   vy �wx�����z{������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� �������M������^documentLength�   �� ����������������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO+�B��N���%�Rs����� ��\NS.uuidbytesO!rJr�F���u��j���   �� �������M������^documentLength^errorIndicator� \ ]��_IDEEditorHistoryItem�� `_IDEEditorHistoryItem����� ��������� J ����Áā����؀�.���� ������� .�������������  � S�����������)� �����_��_Assets.xcassets� % .������� � .������[TheMealsApp�� ��0 W��� _Zfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Assets.xcassets_com.apple.dt.assetcatalog�   �� 8��������ށŁƁǁȁɁʁˁ̨���Z����́ρс��ҁӁց׀�_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area�  �k��΁�_B./assetNoFavorite.imageset/[universal][][][1x][][][][][][][][][][]�  �k���Ё�_./assetNoFavorite.imageset_IBICCatalogOverviewController�   �� 8�����    8��ԡ�Հ�_expandedItemIDs�  �������ZdetailArea�    8����\Universal 1x����� ����� M�݁���ځ��������� ����� .��ہ��������  " S�#$%&'(�܁ށ�����)�  .�݀���_HomePresenter.swift� 0 .�߀���YPresenter� ����ȁ��  ����ȁ�� % .������� A .�䀅��[TheMealsApp�� �F0 W���� _tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift�   KV 8�LMNOPQRSTU������������W 0��[���_ 0�򀆀���������������_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  m S�n��)�  q S������v�w�v�w�)�   x{ �yz�����|}������_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� �������Հ��}��^documentLength�   �� ����������������������_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�mFl�M,������dŀ��� ��\NS.uuidbytesO�M�8��B���0��M���   �� ����� �Հ��}��^documentLength_linkBuilder(for:content:)����� ��������� L��������*������� ������� .�����������  � S����������	�
���)� � .�����_HomeRouter.swift� �v����VRouter� ����ȁ��  ����ȁ�� % .������� � .�����[TheMealsApp�� ��0 W���� _nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift�   �� 8���������������������� 0���7 .�� 0���������������(����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S���)�  	 S�������ȁ�Ȁ)�    ����� �"��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ��!����������^documentLength�   #' �$%&�#�$�%�()��&�'����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�0 1�\NS.uuidbytesOpSz�n Bl��C)T�*���4 5�\NS.uuidbytesO@jH��3IM�"�Tۡpz���   8; �:���)�耗����^documentLengthZHomeRouter����� ���B�DE�G K�/���7�,���Q������� ��KL��� .�5�-���������  R S�STUV�.�0�2�3�)� Bv�/���_ContentView.swift� ^ .�1����SApp� % .������� g .�4����[TheMealsApp�� �l0 W�6��� _`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/ContentView.swift�   q| 8�rstuvwxyz{�8�9�:�;�<�=�>�?�@�A�} 0������� 0�B�������D�����O����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���C�)�  � S����ہ����)�   �� ����E�F����G�M��_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �����H�I�J�����K�L����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�._�gII����k1M���� ��\NS.uuidbytesOyiW�� AJ��#N��m���   �� �����N������^documentLength�   �� �Ȁ��P������^documentLengthTbody�  ���сS���� ��_currentEditorHistoryItem�T�z����� ��������� I�����_�U���y������� ������� .�^�V���������  � S�������W�X�Y�Z�[�\�)� � .������� ����ȁ�� ����ȁ��  ����ȁ�� % .�������  .�]����[TheMealsApp�� �/0 W����� �    8��`�a�b�c�d�e�f�g�h�i� 0��
��" 0�j�������l���"���w����_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  0 S�1�k�)�  4 S�"�"��v�w�v�w�)�   ;> �<=�m�n�?@�o�q��_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   EH �G���p�M������^documentLength�   NR �OPQ�r�s�t�ST��u�v����_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�[ \�\NS.uuidbytesO+�B��N���%�Rs����_ `�\NS.uuidbytesO!rJr�F���u��j���   cf �e���x�M������^documentLengthZemptyGames� \ ]mn_IDEEditorHistoryStack�o `_IDEEditorHistoryStack_ItemKind_Editor���� ��rs��� .���}���������  y S�z{|}~�~����������)� � .������� ����ȁ�� ����ȁ��  ����ȁ�� % .������� � .������[TheMealsApp�� �/0 W����� �   �� 8����������_DVTSplitViewItems�  ������������   �� ������������ 0���������]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeYIDEEditor#@�8     �   �� ������������ 0���������_IDEDebuggerArea#@\�      �   �� 8����ˁ���������� .Ё���������XLeftViewYRightViewZLayoutMode_IDESplitViewDebugArea�   �� 8����ہ��������� . 0����������_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsType�   �� 8���������������������������� 0��������������������_+IDEStructuredConsoleAreaLibraryEnabledState_-IDEStructuredConsoleAreaTimestampEnabledState_*IDEStructuredConsoleAreaPIDTIDEnabledState_,IDEStructuredConsoleAreaMetadataEnabledState_(IDEStructuredConsoleAreaTypeEnabledState_-IDEStructuredConsoleAreaSubsystemEnabledState_/IDEStructuredConsoleAreaProcessNameEnabledState_,IDEStructuredConsoleAreaCategoryEnabledState�    8��������_DVTSplitViewItems�  
���������    ����������� 0��������XLeftView#@s`     �    ����������� �"��������YRightView#@|      _Layout_LeftToRight         "   ,   1   :   ?   Q   V   \   ^  �  �  �  �  �  �  �  �  �  �  �        B  O  l  n  p  r  t  v  x  z  |  ~  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �      1  X  j  �  �  �  �  �  �  '  >  G  \  ^  `  b  d  f  h  j  l  n  p  r    �  �  �  �  �        #  )  .  7  D  F  H  J  �  �  �  �  �    *  ,  .  0  �  �  �  �  �  +  8  :  <  >  �  �  �  �  �  8  E  G  I  K  �  �  �  �  �  @  M  O  Q  S  �  �  �  �  �  �  �  �  �  �               =  w  �  �  �  6  8  :  <  >  @  B  D  F  H  J  L  N  P  R  T  V  X  Z  \  ^  `  b  d  f  h  j  l  n  p  r  t  v  x  z  |  ~  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �         	                !  $  '  *  -  0  3  6  9  <  ?  B  E  H  K  N  Q  T  W  Z  ]  `  c  f  i  l  o  q  ~  �  �  �  �    	      �  �  �  �  �          !  �  �  �  �  �  )  6  8  :  <  �  �  �  �  �  <  I  K  M  O  �  �  �  �  �  H  U  W  Y  [  �  �  �  �  �   \   i   k   m   o   �   �   �   �   �  !l  !y  !{  !}  !  !�  !�  !�  "  "  "}  "�  "�  "�  "�  "�  #  #  #  #  #w  #�  #�  #�  #�  #�  #�  #�  $  $  $�  $�  $�  $�  $�  %  %  %  %  %  %�  %�  %�  %�  %�  &  &&  &(  &*  &,  &�  &�  &�  &�  &�  '-  ':  '<  '>  '@  '�  '�  '�  '�  '�  (7  (D  (F  (H  (J  (�  (�  (�  (�  (�  )E  )R  )T  )V  )X  )�  )�  )�  )�  )�  *T  *a  *c  *e  *g  *�  *�  *�  *�  *�  +i  +v  +x  +z  +|  +�  ,  ,  ,  ,  ,�  ,�  ,�  ,�  ,�  -  -  -  -  -!  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  -�  .  .:  .^  .�  .�  .�  .�  /  /,  /I  /R  /U  /W  /Y  /b  /k  /m  /o  /q  /s  /u  /w  /x  /y  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  0  0  0,  0^  0  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  1	  1  1  1   1"  1$  1)  1+  1-  1/  1;  1J  1S  1T  1V  1Y  1b  1o  1t  1v  1x  1}  1  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  2	  2  2  2  2  2  2  2  2  2  2  2  2<  2[  2  2�  2�  2�  3  3.  3M  3j  3s  3v  3x  3z  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  3�  4  4  4  4  4  4  4  4  4  4   4I  4{  4�  4�  4�  4�  4�  4�  4�  4�  4�  4�  5  5  5  5  5  5  5  5"  5%  5.  5;  5@  5B  5D  5I  5K  5M  5O  5^  5k  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  5�  6  6A  6e  6�  6�  6�  6�  7
  7  7  7  7  7#  7,  7.  70  72  74  76  78  7:  7<  7I  7N  7P  7R  7W  7Y  7[  7]  7�  7�  7�  7�  7�  7�  7�  7�  7�  7�  7�  7�  7�  8  8B  8K  8X  8k  8m  8v  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  8�  9  9  9  9  94  96  98  9:  9<  9>  9@  9B  9D  9F  9H  9]  9_  9a  9c  9e  9g  9i  9k  9m  9o  9q  9s  9�  9�  9�  9�  :  :?  :c  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  ;   ;  ;  ;	  ;  ;  ;  ;2  ;O  ;\  ;c  ;e  ;g  ;i  ;p  ;r  ;t  ;v  ;x  ;�  ;�  ;�  ;�  <
  <  <  <(  <5  <H  <J  <W  <\  <^  <`  <e  <g  <i  <k  <z  <}  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  =   =  =  =  =	  =  =  =  =  =  =1  =P  =t  =�  =�  =�  >  >#  >B  >_  >h  >k  >n  >p  >y  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  >�  ?  ?  ?  ?  ?  ?  ?  ?  ?+  ?.  ?;  ?B  ?E  ?H  ?K  ?R  ?U  ?W  ?Z  ?\  ?�  ?�  ?�  ?�  ?�  @  @  @  @  @,  @.  @;  @@  @B  @E  @J  @L  @O  @Q  @`  @m  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  @�  A  A0  AR  Av  A�  A�  A�  A�  B  B$  B'  B*  B,  B5  B>  BA  BD  BG  BJ  BL  BN  B[  B`  Bc  Bf  Bk  Bn  Bq  Bs  B�  B�  B�  B�  B�  B�  B�  B�  B�  B�  B�  B�  C  C+  C]  Cf  Cs  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  C�  D   D  D  D
  D  D  D  D  D%  D2  DG  DJ  DM  DP  DS  DV  DY  D\  D_  Db  De  Dz  D}  D  D�  D�  D�  D�  D�  D�  D�  D�  D�  D�  D�  D�  E  E9  E_  E�  E�  E�  E�  E�  E�  E�  E�  E�  F  F  F  F  F	  F  F  F  F   F#  F(  F+  F.  F0  FS  Fp  F}  F�  F�  F�  F�  F�  F�  F�  F�  F�  F�  F�  G  G#  G0  GC  GE  GN  G[  Gn  Gp  G}  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  H  H  H  H
  H  H  H  H  H+  H.  H0  H2  H4  H7  H9  H;  H=  H@  HB  HD  Ha  H�  H�  H�  H�  I  I4  IS  Ir  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  I�  J  J'  J4  J9  J<  J>  JC  JF  JH  JJ  JY  J\  Ji  Jp  Js  Jv  Jy  J�  J�  J�  J�  J�  J�  J�  K  K  K  K/  K1  K:  KG  KZ  K\  Ki  Kn  Kp  Ks  Kx  Kz  K}  K  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  K�  L  L:  L^  L�  L�  L�  L�  M  M,  MI  MR  MU  MX  MZ  Mc  Ml  Mo  Mr  Mu  Mx  Mz  M|  M~  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  N   N  N
  N  N  N  N:  N[  N�  N�  N�  N�  N�  N�  N�  N�  N�  N�  N�  N�  N�  N�  O  O  O  O  O  O!  O#  O0  O5  O7  O:  O?  OA  OD  OF  OU  Ob  Ow  Oz  O}  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  P#  PE  Pi  P�  P�  P�  P�  Q  Q  Q  Q  Q  Q(  Q1  Q4  Q7  Q:  Q=  Q?  QA  QN  QS  QV  QY  Q^  Qa  Qd  Qf  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  R  R  R  R	  R2  Rd  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  S  S  S/  S2  S5  S8  S;  S>  SA  SD  SG  SJ  SM  Sb  Se  Sg  Si  Sk  Sn  Sq  St  Sv  Sy  S{  S}  S�  S�  S�  S�  T#  TI  Tm  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  U  U  U
  U  U  U  U  U  U=  UZ  Ug  Un  Uq  Ut  Uw  U~  U�  U�  U�  U�  U�  U�  V  V  V  V-  V/  V8  VE  VX  VZ  Vg  Vl  Vo  Vq  Vv  Vy  V{  V}  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  W   W  W  W	  W  W!  W$  W&  W(  W*  W-  W/  W1  W3  W6  W8  W:  WW  Wv  W�  W�  W�  X  X*  XI  Xh  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  X�  Y  Y*  Y/  Y1  Y4  Y9  Y;  Y>  Y@  YO  YR  Y_  Yf  Yi  Yl  Yo  Yv  Yx  Y{  Y~  Y�  Y�  Y�  Y�  Z  Z  Z%  Z'  Z0  Z=  ZP  ZR  Z_  Zd  Zf  Zi  Zn  Zp  Zs  Zu  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  Z�  [  [.  [R  [t  [�  [�  [�  \  \   \=  \F  \I  \L  \N  \W  \`  \c  \f  \i  \l  \n  \{  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  ]  ]"  ]T  ]}  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ]�  ^  ^  ^  ^  ^  ^  ^$  ^&  ^)  ^+  ^:  ^G  ^\  ^_  ^b  ^e  ^h  ^k  ^n  ^q  ^t  ^w  ^z  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  _
  _,  _P  _v  _�  _�  _�  _�  _�  `  `  `  `  `  `  `  `!  `$  `&  `(  `*  `7  `<  `?  `B  `G  `J  `M  `O  `r  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  a  a9  aB  aO  ab  ad  am  az  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  b  b  b#  b&  b)  b,  b/  b2  b5  b8  b;  b>  bA  bV  bY  b[  b]  b_  bb  bd  bg  bi  bl  bn  bp  b�  b�  b�  b�  c  c<  c`  c  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  d  d  d  d  d  d  d  d2  dU  db  dg  dj  dl  dq  dt  dv  dx  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  e  e4  e=  eJ  e]  e_  eh  eu  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  e�  f  f  f  f  f  f  f!  f#  f%  f(  f*  f,  fI  fh  f�  f�  f�  f�  g  g;  gZ  gw  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  g�  h	  h  h  h   h#  h&  h-  h0  h3  h5  h7  h`  h�  h�  h�  h�  h�  h�  h�  h�  i  i	  i  i  i  i   i%  i'  i*  i,  i;  i=  iJ  iO  iQ  iT  iY  i[  i^  i`  io  i|  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  j  j?  ja  j�  j�  j�  j�  k  k*  k3  k6  k9  k;  kD  kM  kP  kS  kV  kY  k[  k]  k_  kl  kq  kt  kw  k|  k  k�  k�  k�  k�  k�  k�  k�  k�  k�  k�  k�  k�  k�  k�  l$  lM  ln  lw  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  m  m  m  m  m  m  m  m  m+  m8  mM  mP  mS  mV  mY  m\  m_  mb  me  mh  mk  m�  m�  m�  m�  m�  m�  m�  m�  m�  m�  m�  m�  m�  m�  m�  n  n?  ne  n�  n�  n�  n�  n�  n�  n�  n�  n�  o  o
  o  o  o  o  o  o$  o)  o,  o/  o4  o7  o:  o<  o_  o|  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  p  p&  p/  p<  pO  pQ  pZ  pg  pz  p|  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  p�  q  q  q  q  q  q  q  q  q  q   q#  q8  q;  q=  q?  qA  qD  qF  qH  qJ  qM  qO  qQ  qn  q�  q�  q�  q�  r  rA  r`  r  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  r�  s  s0  s=  sD  sG  sJ  sM  sT  sW  sZ  s\  s^  s�  s�  s�  s�  s�  t  t  t  t  t.  t0  t=  tB  tE  tG  tL  tO  tQ  tS  tb  te  tr  tw  ty  t|  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  u  u  u  u"  uA  ue  u�  u�  u�  u�  v  v3  vP  vY  v\  v_  va  vj  vs  vv  vy  v|  v  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  w  w  w  w	  w  w  w(  w/  w2  w5  w8  w?  wB  wD  wG  wI  wr  w�  w�  w�  w�  w�  w�  w�  x  x  x  x(  x-  x/  x2  x7  x9  x<  x>  xM  xZ  xo  xr  xu  xx  x{  x~  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  y  y?  yc  y�  y�  y�  y�  z  z  z  z  z  z"  z+  z.  z0  z3  z5  z7  z9  zF  zK  zN  zQ  zV  zY  z\  z^  z{  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  {  {"  {K  {}  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  |  |  |)  |,  |/  |2  |5  |8  |;  |>  |A  |D  |G  |\  |_  |a  |c  |e  |h  |j  |l  |n  |q  |s  |u  |�  |�  |�  |�  }  }A  }e  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  ~   ~  ~  ~  ~  ~  ~  ~  ~5  ~X  ~e  ~j  ~m  ~o  ~t  ~w  ~y  ~{  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�    7  @  M  `  b  k  x  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �   �"  �$  �&  �)  �+  �-  �J  �i  ��  ��  ��  ��  �  �<  �[  �x  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �"  �$  �'  �,  �.  �1  �3  �B  �E  �R  �Y  �\  �_  �b  �i  �l  �n  �q  �s  ��  ��  ��  ��  �  �  �  �#  �0  �C  �E  �R  �W  �Y  �\  �a  �c  �f  �h  �w  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �!  �E  �g  ��  ��  ��  ��  �  �0  �9  �<  �?  �A  �J  �S  �V  �Y  �\  �_  �a  �c  �p  �u  �x  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �  �  �  �  �!  �$  �&  �)  �+  �T  �u  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �  �  �  �  �  �  �   �/  �<  �Q  �T  �W  �Z  �]  �`  �c  �f  �i  �l  �o  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �C  �i  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �"  �'  �*  �-  �2  �5  �8  �:  �W  �z  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �'  �Y  �b  �o  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �  �  �  �!  �6  �9  �;  �=  �?  �B  �E  �H  �J  �M  �O  �Q  �n  ��  ��  ��  ��  �  �A  �`  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �4  �A  �H  �K  �N  �Q  �X  �[  �]  �`  �b  ��  ��  ��  ��  ��  �  �	  �  �  �2  �4  �A  �F  �I  �K  �P  �S  �U  �W  �f  �i  �r  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �  �  �  �1  �P  �t  ��  ��  ��  �  �#  �B  �_  �h  �k  �n  �p  �y  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �+  �.  �;  �B  �E  �H  �K  �R  �U  �X  �Z  �\  ��  ��  ��  ��  ��  �  �  �  �  �,  �.  �7  �D  �I  �K  �N  �S  �U  �X  �Z  �i  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �7  �Y  �}  ��  ��  ��  �  �"  �+  �.  �1  �3  �<  �E  �H  �K  �N  �Q  �S  �U  �W  �d  �i  �l  �o  �t  �w  �z  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �E  �f  �o  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �#  �0  �E  �H  �K  �N  �Q  �T  �W  �Z  �]  �`  �c  �x  �{  �}  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �8  �^  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �	  �  �  �  �  �"  �%  �(  �-  �0  �3  �5  �R  �u  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �3  �T  �]  �j  �}  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �
  �  �  �  �  �  �  �  �"  �%  �:  �=  �?  �A  �C  �F  �H  �J  �L  �O  �Q  �S  �p  ��  ��  ��  ��  �  �C  �b  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �6  �C  �H  �J  �M  �R  �T  �W  �Y  �h  �k  �x  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �+  �>  �@  �I  �V  �i  �k  �x  �}  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �
  �  �)  �H  �l  ��  ��  ��  ��  �  �:  �W  �`  �c  �f  �h  �q  �z  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �!  �$  �1  �8  �;  �>  �A  �H  �K  �M  �P  �R  �{  ��  ��  ��  ��  ��  ��  �  �  �"  �$  �1  �6  �8  �;  �@  �B  �E  �G  �V  �c  �x  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �$  �F  �j  ��  ��  ��  ��  �  �  �  �  �   �)  �2  �5  �8  �;  �>  �@  �B  �O  �T  �W  �Z  �_  �b  �e  �g  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �Q  �Z  �g  �z  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �0  �3  �6  �9  �<  �?  �B  �E  �H  �K  �N  �c  �f  �h  �j  �l  �o  �q  �s  �u  �x  �z  �|  ��  ��  ��  ��  �"  �H  �l  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �<  �_  �l  �q  �t  �v  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �>  �G  �T  �g  �i  �r  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �   �"  �$  �'  �)  �+  �-  �0  �2  �4  �Q  �p  ��  ��  ��  �   �$  �C  �b  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �#  �&  �(  �-  �0  �2  �4  �C  �F  �S  �Z  �]  �`  �c  �j  �m  �p  �r  �t  ��  ��  ��  ��  �  �  �  �$  �1  �D  �F  �S  �X  �Z  �]  �b  �d  �g  �i  �x  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �#  �G  �i  ��  ��  ��  ��  �  �2  �;  �>  �A  �C  �L  �U  �X  �[  �^  �a  �c  �e  �r  �w  �z  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �B  �t  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �"  �$  �3  �@  �U  �X  �[  �^  �a  �d  �g  �j  �m  �p  �s  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �%  �I  �o  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �!  �.  �3  �6  �9  �>  �A  �D  �F  �i  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �0  �9  �F  �Y  �[  �d  �q  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �!  �$  �'  �*  �-  �0  �3  �6  �K  �N  �P  �R  �T  �W  �Y  �[  �]  �`  �b  �d  ��  ��  ��  ��  �
  �0  �T  �s  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �A  �N  �S  �U  �X  �]  �_  �b  �d  �s  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �)  �6  �I  �K  �T  �a  �t  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �	  �  �  �  �  �  �  �3  �R  �v  ��  ��  ��  �  �%  �D  �a  �j  �m  �p  �r  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �+  �.  �;  �B  �E  �H  �K  �R  �T  �W  �Z  �\  �}  ��  ��  ��  ��  �  �  �  �  �,  �.  �;  �@  �B  �E  �J  �L  �O  �Q  �`  �m  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �.  �P  �t  ��  ��  ��  ��  �  �"  �%  �(  �*  �3  �<  �?  �B  �E  �H  �J  �W  �\  �_  �b  �g  �j  �m  �o  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �0  �Y  �b  �o  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �#  �8  �;  �>  �A  �D  �G  �J  �M  �P  �S  �V  �k  �n  �p  �r  �t  �w  �y  �{  �}  ��  ��  ��  ��  ��  ��  �  �*  �P  �t  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �	  �  �  �  �  �  �  �!  �D  �a  �n  �u  �x  �{  �~  ��  ��  ��  ��  ��  ��  ��  �  �  �!  �4  �6  �?  �L  �_  �a  �n  �s  �v  �x  �}             ¥  §  ª  ¯  ±  ´  ¶  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �!  �#  �&  �)  �,  �.  �1  �3  �5  �R  �q  Õ  ÷  ��  �  �%  �D  �c  Ā  ĉ  Č  ď  đ  Ě  ģ  Ħ  ĩ  Ĭ  į  ı  ľ  ��  ��  ��  ��  ��  ��  ��  ��  �  �#  �(  �*  �-  �2  �4  �7  �9  �H  �K  �X  �_  �b  �e  �h  �o  �r  �u  �w  �y  ū  ��  ��  ��  �  �  �   �)  �6  �I  �K  �X  �]  �_  �b  �g  �i  �l  �n  �}  Ɗ  Ɵ  Ƣ  ƥ  ƨ  ƫ  Ʈ  Ʊ  ƴ  Ʒ  ƺ  ƽ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �'  �K  �m  Ǒ  Ƿ  ��  ��  �  �6  �?  �B  �E  �G  �P  �Y  �\  �_  �b  �e  �g  �t  �y  �|  �  Ȅ  ȇ  Ȋ  Ȍ  ȩ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �%  �'  �*  �-  �/  �P  ɂ  ɫ  ɴ  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �"  �$  �3  �@  �U  �X  �[  �^  �a  �d  �g  �j  �m  �p  �s  ʈ  ʋ  ʍ  ʏ  ʑ  ʔ  ʖ  ʘ  ʚ  ʝ  ʟ  ʡ  ʾ  ��  �  �#  �G  �m  ˑ  ˰  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �&  �+  �.  �1  �6  �9  �<  �>  �[  �~  ̋  ̐  ̓  ̕  ̚  ̝  ̟  ̡  ̰  ̳  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �<  �]  �f  �s  ͆  ͈  ͑  ͞  ͱ  ͳ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �
  �  �  �  �  �  �  �  �"  �%  �:  �=  �?  �A  �C  �F  �H  �J  �L  �O  �Q  �S  �p  Ώ  γ  ��  ��  �  �C  �b  ρ  Ϟ  ϧ  Ϫ  ϭ  ϯ  ϸ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �8  �E  �L  �O  �R  �U  �\  �_  �b  �d  �f  Џ  ��  ��  ��  ��  �  �  �  �#  �6  �8  �E  �J  �M  �O  �T  �W  �Y  �[  �j  �m  �z  �  с  ф  щ  ы  ю  ѐ  џ  Ѭ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �	  �  �  �*  �I  �m  ҏ  ҳ  ��  ��  �  �;  �X  �a  �d  �g  �i  �r  �{  �~  Ӂ  ӄ  Ӈ  Ӊ  Ӗ  ӛ  Ӟ  ӡ  Ӧ  ө  Ӭ  Ӯ  ��  ��  ��  �   �  �  �
  �  �  �  �   �#  �0  �7  �:  �=  �@  �G  �J  �M  �O  �Q  �z  Ԭ  ��  ��  ��  ��  ��  �  �  �!  �#  �0  �5  �7  �:  �?  �A  �D  �F  �U  �b  �e  �h  �k  �n  �p  �}  �  Ձ  Մ  ��  ��  ��  ��  ��  ��  ��  �  �	  �  �  �  �  �D  �s  ֢  ��  ��  ��  �  �  �	  �  �  �&  �0  �<  �?  �B  �E  �H  ן  ר  ׵  ׺  ׽  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �%  �,  �L  �b  �o  �p  �q  �s  ؀  ؅  ؈  ؋  ؐ  ؓ  ؖ  ؘ  إ  ا  ة  ج  ��  ��  ��  ��  ��  �*  �7  �:  �=  �@  �C  �E  �a  �j  �m  �p  �r  ه  ٚ  ٤  ٧  ٩  ٪  ٭  ٰ  ٽ  ��  ��  ��  ��  ��  ��  ��  �   �	  �  �  �%  �.  �:  �?  �K  �T  �m  �t  ڍ  ڣ  ڰ  ڳ  ڶ  ڹ  ڼ  ھ  ��  ��  ��  ��  ��  �   �  �  �  �	  �  �  �  �  �(  �+  �.  �;  �>  �@  �C  �F  �H  �U  �f  �i  �l  �o  �r  �u  �x  �{  �~  ۏ  ے  ە  ۘ  ۛ  ۞  ۡ  ۤ  ۧ  ۩  ��  ��  ��  ��  �  �  �0  �>  �G  �J  �M  �P  ܕ  ܞ  ܫ  ܲ  ܸ  ��  ��  ��  ��  ��  �  �  �  �  �  �!  �.  �1  �4  �7  �:  �<  �N  �W  �Z  �]  �`  �b  �k  �p  �{  ݈  ݉  ݊  ݌  ݙ  ݜ  ݟ  ݢ  ݥ  ݧ  ݵ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �  �  �"  �1  �8  �E  �L  �O  �R  �U  �\  �_  �b  �e  �g  ވ  ��  ��  ��  ��  ��  ��  �  �  �  �  �!  �*  �-  �6  �=  �B  �O  �R  �U  �X  �[  �]  �i  �r  �|  �  ߽  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �A  �J  �S  �V  �c  �x  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  �  �  �/  �F  �[  �e  �z  �  �  �  ��  ��  �  �  �&  �/  �0  �3  �@  �I  �L  �O  �R  �U  �^  �a  �d  �g  �j  �l  �  �  �  ��  ��  ��  ��  �  �  �  �  �  �  �(  �/  �2  �5  �8  �?  �B  �E  �H  �J  �s  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �   �-  �:  �=  �@  �C  �F  �I  �L  �Y  �\  �_  �b  �e  �h  �k  �m  �  �  ��  �  �  �  �   �C  �P  �a  �d  �g  �j  �m  �p  �s  �v  �y  �  �  �  �  �  �  �  �  �  �  �  ��  ��  �7  �`  �  �  �  ��  ��  ��  ��  �  �  �  �
  �  �  �  �  �)  �+  �.  �1  �4  �7  �:  �=  �?  �B  �D  �W  �j  �~  �  �  �  ��  ��  ��  ��  �  �  �	  �  �  �  �  �"  �%  �(  �+  �.  �1  �4  �6  �Y  �w  �  �  ��  ��  ��  ��  �  �  �  �
  �  �  �  �  �  �!  �8  �L  �e  �  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  �  �  �  �7  �@  �i  �p  ��  ��  �  �  �  �  �  �  �!  �#  �?  �M  �Z  �a  �d  �g  �j  �q  �t  �w  �z  �|  ��  ��  ��  ��  ��  ��  ��  �  �  �
  �  �  �  �"  �+  �.  �1  �:  �=  �E  �N  �f  �k  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �
  �  �  �  �  �  �"  �%  �(  �*  �A  �U  �n  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  ��  ��  ��  �  �  �  �  �  �  �  �   �#  �&  �(  �M  �u  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �  �  �1  �E  �^  �y  ��  ��  ��  ��  ��  ��  ��  �!  �<  �I  �L  �O  �R  �U  �W  �r  �{  ��  ��  ��                7  @  ]  `  c � � � � � � � � � � �     # , 9 < ? B D F d q v y | � � � � � � � � � � � � � � � �        " % ( + . 1 4 7 : = ? b � � � � �  ! 0 F i v � � � � � � � � � � � � � � � � � � � � � � � �      ' = K W w y � � � � � � � � � � � � � � � � � �  ' K ] { � � � � � � � � � � � � �  # 6 R U X [ ^ a c � � � � � � � � � � � � � � � � � �  ! @ M P R U Z g j l o t � � � � � � � � � � � � � � � � � � � 	T 	] 	s 	x 	� 	� 	� 	� 	� 	� 
 
 
 
  
2 
; 
f 
k 
� 
� 
� 
� 
�    
       1 4 6 8 : = @ C E H J L i � � � �  < [ z � � � � � � � � � � � � � � � � � � � � �  / < A C F K M P R a n u x { ~ � � � � � � �   ! 4 6 ? L _ a n s u x }  � � � � � � � � �         . 1 4 7 : = ? H O R U X Z g j m p � � � � � � � � � � � � � � * F S d g j m p s v y | � � � � � � � � � � � � � � 	  . < E H K N � � � � � � � � � � �     	     ) , / 2 = J K L N [ |  � � � � � � � � � � � � � � � � � � � � � � � � � � �   " $ ' 1 > A C F S V X [ h k m p } � � � � � � � �  * ? B E H K N Q T W Z ] r u w y { ~ � � � � � � � � �  3 Y } � � � � � � � � � �    	    ! & ) , . K n { � � � � � � � � � � � � � � � � � � � ) J S ` s u ~ � � � � � � � � � � � � �       ! # & ? B E H K N P Y f i l o r u x z � � � � � � � � � � � � � � � � � � � � � �      , / 2 4 � � � � � � � � � � � � � � � �     
     0 O s � � �  " A ^ g j m o x � � � � � � � � � � � � � � � � �   	     $ 1 8 ; > A H K N P R { � � � � � �   " $ 1 6 8 ; @ B E G V a � � � � � � � � � � � � � � � � � � � � � � � � � � �     " & 3 6 8 ; H K M P \ i l o q � � � � � �        ) , . 0 2 5 8 ; = @ B D a � � � �    4  S  r  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  � ! !% !2 !9 !< !? !B !I !L !O !Q !S !| !� !� !� !� !� !� " " "# "% "2 "7 "9 "< "A "C "F "H "W "d "i "k "n "s "u "x "z "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� # # # #! #$ #' #* #, #5 #B #E #H #K #N #Q #T #V #c #f #h #k #x #{ #} #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� $ $ $ $ $  $# $& $) $, $/ $2 $5 $J $M $O $Q $S $V $Y $\ $^ $a $c $e $� $� $� $� % %1 %U %t %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� & & & &# &F &S &X &Z &] &b &d &g &i &x &� &� &� &� &� &� &� &� &� &� &� ' '" '+ '8 'K 'M 'V 'c 'v 'x '� '� '� '� '� '� '� '� '� '� '� '� '� '� ( ( (! ($ (' (* (- (/ (8 (E (H (K (N (Q (T (W (Y (f (i (k (n ({ (~ (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� ) ) ) ) ) ) )' )0 )5 )8 ); )> )K )R )U )X )[ )b )e )g )j )l )z )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� * * * * * * *# *& *) *+ *. *0 *9 *C *N *f *s *| * *� *� *� *� *� *� *� *� *� *� *� *� + + +0 +3 +6 +9 +< +? +B +E +H +Y +[ +] +_ +a +c +e +g +i +k +� +� +� ,% ,P ,� ,� ,� ,� ,� ,� ,� ,� ,� - - - -! -$ -' -4 -; -> -A -D -K -N -P -S -U -^ -g -t -{ -~ -� -� -� -� -� -� -� -� -�            '             -�

/== TheMealsApp.xcodeproj/project.xcworkspace/xcuserdata/gilangramadhan.xcuserdatad/UserInterfaceState.xcuserstate
bplist00�        
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�  	UState���      1 2 3 4 5 6 7 8 9 : ; < J K L M N O U [ \ b v w x y z { | } ~ � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � $+,-.45=>?BCEFNOPhijklmnopqrv}���������������0459:>?CDHIMNRSWX\]abfgklpquvz{��������������������������������������������������� $%)*./3489=>BCGHLMQRjklmnopqrstx����������������������������������� 
 !"*+CDEFGHIJKLMQXY_`hijrs���������������������������������������� !"#$%)01289ABCKLdefghijklmnryz{��������������������������������������� &'?@ABCDEFGHIMTU[\defghpq����������������������������������������� !"#$%&'+234:;CDEFPQRSTUV^_wxyz{|}~�����������������������������������	
$%-./089QRSTUVWXYZ[_fgmnvwxy���������������������������������������							*	+	,	-	.	/	0	1	2	3	4	8	?	@	F	G	O	P	Q	Y	Z	r	s	t	u	v	w	x	y	z	{	|	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�
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
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
�
� !"#$%&'()*.56<=EFGHIQRjklmnopqrstx�����������������������������������  !)*BCDEFGHIJKLPW]^fghpq����������������������������������������  !"&-34<=>FGabcdefghijklpw}~�������������������������������� "#$,-EFGHIJKLMNOSZ`aijkst����������������������������������������	
+,-./0123459@FGOPQYZrstuvwxyz{|������������������������������	
%&'/0HIJKLMNOPQRV]cdlmnvw��������������������������������������� !"#$%&'(,39:BCDLMefghijklmnosz���������������������������������������	
#$<=>?@ABCDEFJQRXYabckl�����������������������������������������!(./789:BC[\]^_`abcdeipvw������������������������������������%&'()*+,-./0123456BCDEFGHIOYZ[\]ghlp����������������������������������������	
!)*+789:;<=>JQTXYZ^nopqrstu���������������������������������������������� !"&6789:;<=GHIJRbnov|}�������������������������������������#'(,DEFGHIJKLMNRY_`hiqrs|���������������������������������%,378<@DEIabcdefghijkov|}�������������������������������������!"'-.3=>?@ABLMNR^_`abnopqrxy��������U$null�      WNS.keysZNS.objectsV$class�  ���  ���e_IDEWorkspaceDocument_$F98B5DD0-830B-4F1E-8A69-9F65C20AC7EE�     % 0�        ! " # $�����	�
����� & ' ( ) * + , - . /���ˁځ݁�	�
���+_BreakpointsActivated_DefaultEditorStatesForURLs_RunContextRecents\ActiveScheme_ActiveRunDestination_0LastCompletedPersistentSchemeBasedActivityReport_DocumentWindows_WindowArrangementDebugInfo_SelectedWindows_RecentEditorDocumentURLs	�    = C 0� > ? @ A B������ D E F G H��3�I�}���+_(Xcode.IDEKit.EditorDocument.AssetCatalog_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project_:Xcode.IDEKit.EditorDocument.PegasusNonFileSystemSourceCode_'Xcode.IDEKit.EditorDocument.LogDocument_-Xcode.IDEKit.EditorDocument.PegasusSourceCode�    P R 0� Q�� S��+� V  W X Y ZWNS.base[NS.relative� ��_ofile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Assets.xcassets� ] ^ _ `Z$classnameX$classesUNSURL� _ aXNSObject�    c l 0� d e f g h i j k������ �!�"� m n o p q r s t�#�&�(�)�*�,�1�2�+_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area�    �� ��$�%_./assetSearchMeal.imageset� ] ^ � �\NSMutableSet� � � aUNSSet�   � �� ��'�%_./assetSearchMeal.imageset_IBICCatalogOverviewController#@l�     �    � � 0���+� ] ^ � �_NSMutableDictionary� � � a\NSDictionary�    � � 0� ��-� ��.�+_expandedItemIDs�   � �� ��/�0Q.� ] ^ � �� � a^sourceListArea�    � � 0���+�    � � 0� ��4� ��6�+� V  W X Y �� ��5_ifile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj�    � � 0� � � � ��7�8�9�:� � � � ��;�<�=�H�+_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_(Xcode3ProjectEditor_Xcode3PackagesEditor_Xcode3PackagesEditor_Xcode3TargetEditor�   � ɡ Ȁ>�G� � � �  � � � �YselectionYtimestamp[documentURL�A�@�?�F_ifile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj#AĖ\�o;�    � � 0� � ׀B�C� � ڀD�E�+WProjectVEditor[TheMealsApp_Xcode3PackagesEditor� ] ^ � �_Xcode3ProjectDocumentLocation� � � a_Xcode3ProjectDocumentLocation_DVTDocumentLocation� ] ^ � �WNSArray� � a�    � � 0���+�    � � 0� � �J�L� � �N�j�+� V  W X Y �� ��K_}x-xcode-disassembly://stack_frame?launchSessionRef=7fc6d80ce450&stackFrameHash=3526088571801097364&stackFrameRef=7fc6e3be1200� V  W X Y �� ��M_}x-xcode-disassembly://stack_frame?launchSessionRef=7fc6d80d1430&stackFrameHash=3526088571801097364&stackFrameRef=7fc6c4876b10�    �
 0� 	�O�P�Q�R�S�T�U�V�W�X� &�Y�]�]�]�^�f�\�]�g��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ! ɡ"�Z�G�  % ɤ&&�[�\�[�\�G �   /1 �0�_�2�`�e_DeltaEditorLayoutExtension�   69 �78�a�b�:;�c�d�e[lineIndexes^documentLength�  @ ɠ�G� ] ^ �D� � a#��      �   GJ �HI�h�i�:;�c�d�eUfolds^documentLength�   Q\ 0�RSTUVWXYZ[�k�l�m�n�o�p�q�r�s�t�]ae &�u�]�]�]�w�f�\�]�{��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  s ɡt�v�G�  w ɤ&&�[�\�[�\�G�   ~� ��x���y�e_DeltaEditorLayoutExtension�   �� ��7�z�a�;:�d�c�e^documentLength�   �� �H��h�|�:;�c�d�e^documentLength�   �� 0����~����������+� V  W X Y�� ��_2x-xcode-log://70021531-02B3-4560-BC7E-8EEBD4B2389C� V  W X Y�� ���_2x-xcode-log://ACC57B49-B56F-4A2A-BF4F-6A5CD2C698B1�   �� 0���������+_SelectedDocumentLocations�  � ɠ�G�   �� 0���������+�   �� 0�:���������������������������������������������������������󀇀����������������������������������������������������������Àŀǀɀˀ̀πрӀՀ׀ـۀ݀߀����������������:����������� 	
 !"#$%&'()*+,-.����'�;�P�f�z�������ҁ����3�J�_�t�������Ɂށ���.�E�\�q�������؁���.�B�X�p���������Ձ����%�;�P�e�y�������+� V  W X Y3� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/View/SearchRow.swift� V  W X Y8� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swift� V  W X Y=� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/CategoryEntity.swift� V  W X YB� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift� V  W X YG� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift� V  W X YL� ���_}file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/File.swift� V  W X YQ� ���_xfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swift� V  W X YV� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteRow.swift� V  W X Y[� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/CategoryMapper.swift� V  W X Y`� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift� V  W X Ye� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift� V  W X Yj� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extension/CustomeError+Ext.swift� V  W X Yo� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swift� V  W X Yt� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/View/CategoryRow.swift� V  W X Yy� ���_wfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/DI/Injection.swift� V  W X Y~� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swift� V  W X Y�� ���_~file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/MealRow.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extension/Color+Ext.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swift� V  W X Y�� ���_}file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/TabItem.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataStore.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/LocaleDataStore.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Meal/Presenter/MealPresenter.swift� V  W X YĀ ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/DetailMeal/Presenter/DetailPresenter.swift� V  W X Yɀ ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoryResponse.swift� V  W X Y΀ ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift� V  W X YӀ ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift� V  W X Y؀ ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swift� V  W X Y݀ ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swift� V  W X Y� ���_qfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/ContentView.swift� V  W X Y� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/IngredientMapper.swift� V  W X Y� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/CustomIcon.swift� V  W X Y� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift� V  W X Y�� ���_ufile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/App/ContentView.swift� V  W X Y�� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/a209-ios-expert-modularization/TheMealsApp/TheMealsApp/ContentView.swift� V  W X Y � ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift� V  W X Y� ���_~file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/BlurView.swift� V  W X Y
� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swift� V  W X Y� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swift� V  W X Y� ���_�file:///Users/gilangramadhan/Library/Developer/Xcode/DerivedData/TheMealsApp-dxiufbyyxhculcgugbkseletdnte/SourcePackages/checkouts/Alamofire/README.md� V  W X Y� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/MealsResponse.swift� V  W X Y� ���_tfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/TheMealsAppApp.swift� V  W X Y#� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swift� V  W X Y(� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoriesResponse.swift� V  W X Y-� ���_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Meal/View/MealView.swift� V  W X Y2� ���_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift� V  W X Y7� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift� V  W X Y<� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swift� V  W X YA� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift� V  W X YF� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swift� V  W X YK� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swift� V  W X YP� ���_�file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift�   S^ 0�TUVWXYZ[\]��������� ������_cg &��]�]�]�
�f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  u ɡv��G�  y ɤz{z{��	��	�G"0�   �� �������e_DeltaEditorLayoutExtension�   �� ��7��a��:��c�e^documentLength��   �� �H��h��:��c��e^documentLength�   �� 0������������������������� &��]�]�]� �f�\�]�%��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ���G�  � ɤ���ā����G�   �� �́!�΁"�e_DeltaEditorLayoutExtension�   �� ��7�#�a����$���e^documentLength��   �� �Hހh�&��ր��$�e^documentLength�   �� 0�����������(�)�*�+�,�-�.�/�0�1���� &�2�]�]�]�4�f�\�]�9��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ�3�G�   ɤ�\�\�\�\�G�    ��5��6�e_DeltaEditorLayoutExtension�    �7�a�7�����8�e^documentLength'�   #& �H%�h�:�����8�e^documentLength�   ,7 0�-./0123456�<�=�>�?�@�A�B�C�D�E�8<@ &�F�]�]�]�I�f�\�]�N��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  N ɡO�G�G�  R ɤS�SāH��H��G�   Z\ �[�J�]�K�e_DeltaEditorLayoutExtension�   ad �7c�a�L��f���M�e^documentLength�   kn �Hm�h�O��f���M�e^documentLength�   t 0�uvwxyz{|}~�Q�R�S�T�U�V�W�X�Y�Z���� &�[�]�]�]�_�f�\�]�d��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��\�G�  � ɤ�����]�^�]�^�G�   �� ���`���a�e_DeltaEditorLayoutExtension�   �� �7��a�b������c�e^documentLength��   �� �H��h�e������c�e^documentLength�   �� 0����������ǁg�h�i�j�k�l�m�n�o�p���� &�q�]�]�]�s�f�\�]�x��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��r�G�  � ɤ�\�\�\�\�G�   �� ��t��u�e_DeltaEditorLayoutExtension�   �� ��7�v�a����w���e^documentLengthg�   �� �H��h�y������w�e^documentLength�    0�	
�{�|�}�~������������ &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  & ɡ'���G�  * ɤ+,+,���������G�   35 �4���6���e_DeltaEditorLayoutExtension�   := �7<�a���:?�c���e^documentLengthm�   DG �HF�h���:?�c���e^documentLength�   MX 0�NOPQRSTUVW���������������������Y]^_a &���]�]�]�������]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  o ɡp���G�  s ɤtv���\���\�G%&�   |~ �}������e_DeltaEditorLayoutExtension�   �� �7��a����������e^documentLength�#@      +�   �� �H��h����������e^documentLength�   �� 0����������������������������������� &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ����G�  � ɤ�����\�]�\�G�   �� �ǁ��Ɂ��e_DeltaEditorLayoutExtension�   �� �7πa����Ҁ����e^documentLength��   �� �Hـh����Ҁ����e^documentLength�   �� 0�������������������ÁāŁƁǪ��� &�Ȁ]�]�]�ˀf�\�]�Ѐ�+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ�ɀG�   ɤ�ʁʁʁʀG�    ��̡�̀e_DeltaEditorLayoutExtension�    �7�΀a���π��e^documentLength�   " �H!�h�Ѣ����πe^documentLength�   (3 0�)*+,-./012�ӁԁՁցׁ؁فځہܪ489:< &�݀]�]�]�����]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  J ɡK�ހG�  N ɤ{P{P�	�߁	�߀G�   VX �W��Y��e_DeltaEditorLayoutExtension�   ]` �7_�a���b����e^documentLength#@*      �   il �Hk�h���b����e^documentLength�   r} 0�stuvwxyz{|������������~�� &��]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ����G�  � ɤ���ā������G#�   �� ���������e_DeltaEditorLayoutExtension�   �� �7��a����������e^documentLength��   �� �H��h����������e^documentLength�   �� 0����������ā�� �����������9�� &�	�]�]�]����]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ݁
�G�  � ɤ����\��\�G��   �� �����e_DeltaEditorLayoutExtension�   �� �7�a���􀄁�e^documentLength�j�   �� �H��h���􀄁�e^documentLength�    0�	
������������ &� �]�]�]�$�)�*�0�]�1��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ( ɡ)�!�G�  , ɤS/0�H�\�"�#�G D�   57 �6�%�8�&�e_DeltaEditorLayoutExtension�   <? �=7�'�a�@��(���e^documentLengtht#@&      �   GK 0�HIJ�+�,�-�LN�.�]�/�+_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_XcodePreviews.PinnedPreview#?�      P�   WZ �HY�h�2��@���(�e^documentLength�   `k 0�abcdefghij�4�5�6�7�8�9�:�;�<�=�lpqrt &�>�]�]�]�A�F�G�]�H��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��?�G�  � ɤ���@�\�@�\�G�   �� ���B���C�e_DeltaEditorLayoutExtension�   �� �7��a�D������E�e^documentLength�#@      �   �� �H��h�I������E�e^documentLength�   �� 0������������K�L�M�N�O�P�Q�R�S�T���� &�U�]�]�]�X�f�\�]�]��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ́V�G�  � ɤ���ÁW���W��G	�   �� �فY�ہZ�e_DeltaEditorLayoutExtension�   �� ��7�[�a����\���e^documentLength��   �� �H�h�^��〄�\�e^documentLength�   �� 0������������`�a�b�c�d�e�f�g�h�i�� &�j�]�]�]�l�q�\�]�r��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ�k�G�   ɤ�_�_�������G�   ! � �m�"�n�e_DeltaEditorLayoutExtension�   &) �'7�o�a�*��p���e^documentLengthD#        �   14 �H3�h�s��*���p�e^documentLength�   :E 0�;<=>?@ABCD�u�v�w�x�y�z�{�|�}�~�FJK�N &��]�]�]������]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  \ ɡ]���G�  ` ɤaa���\���\�G�   hj �i���k���e_DeltaEditorLayoutExtension�   or �p7���a�s������e^documentLength#@      �   z} �H|�h����s�����e^documentLength�   �� 0����������������������������������� &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ����G�  � ɤ��r���\���G�G8>�   �� ���������e_DeltaEditorLayoutExtension�   �� �7��a����������e^documentLength��   �� �Hŀh����������e^documentLength�   �� 0����������ց����������������������� &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ���G�  � ɤ�������^���^�G�   �� ���������e_DeltaEditorLayoutExtension�   	 	 �7	�a����	�����e^documentLength�   	
	 �H	�h����	�����e^documentLength�   		 0�										���������������������		#	' &���]�]�]�f�\�]�ǀ�+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	5 ɡ	6���G�  	9 ɤ	:	:���\���\�G�   	A	C �	B�á	D�Āe_DeltaEditorLayoutExtension�   	H	K �	I7�ŀa�	L��ƀ��e^documentLengthg�   	R	U �H	T�h�Ȣ�	L���ƀe^documentLength�   	[	f 0�	\	]	^	_	`	a	b	c	d	e�ʁˁ́́΁ρЁсҁӪ	g	k	o &�Ԁ]�]�]�׀f�\�]�܀�+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	} ɡ	~�ՀG�  	� ɤ�	��	��^�ց^�րG�   	�	� �	��ء	��ـe_DeltaEditorLayoutExtension�   	�	� �	�7�ڀa�	�:�ۀc�e^documentLength�   	�	� �H	��h�ݢ:	��c�ۀe^documentLength�   	�	� 0�	�	�	�	�	�	�	�	�	�	��߁����������	�	�	� &��]�]�]��f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	� ɡ	Ɓ�G�  	� ɤ	:	:���\���\�G�   	�	� �	с�	Ӂ�e_DeltaEditorLayoutExtension�   	�	� �	�7��a�	L��ƀ��e^documentLength�   	�	� �H	�h���	L���ƀe^documentLength�   	�	� 0�	�	�	�	�	�	�	�	�	�	������������������	�	�	� &���]�]�]���f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
 ɡ
���G�  
 ɤ�:�:�]��]��G�   

 �
���
� �e_DeltaEditorLayoutExtension�   

  �
7��a�
!�����e^documentLength��   
'
* �H
)�h���
!����e^documentLength�   
0
; 0�
1
2
3
4
5
6
7
8
9
:����	�
������
<
@
D &��]�]�]��f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
R ɡ
S��G�  
V ɤ	:	:���\���\�G�   
]
_ �
^��
`��e_DeltaEditorLayoutExtension�   
d
g �
e7��a�
h�����e^documentLength��   
n
q �H
p�h���
h����e^documentLength�   
w
� 0�
x
y
z
{
|
}
~

�
�������� �!�"�#�
�
�
� &�$�]�]�]�'�f�\�]�,��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
� ɡ
��%�G�  
� ɤ
��
�ā&��&��G�   
�
� �
��(�
��)�e_DeltaEditorLayoutExtension�   
�
� �
�7�*�a�
���+���e^documentLength��   
�
� �H
��h�-��
����+�e^documentLength�   
�
� 0�
�
�
�
�
�
�
�
�
�
Ɂ/�0�1�2�3�4�5�6�7�8�
�
�
�
�
� &�9�]�]�]�<�A�B�]�C��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
� ɡ
�:�G�  
� ɤ
�P
�P�;�߁;�߀G/�   
�
� �
�=�
��>�e_DeltaEditorLayoutExtension�   
�
� �
�7�?�a�
���@���e^documentLength#@       �     �H�h�D��
����@�e^documentLength�   	 0�
�F�G�H�I�J�K�L�M�N�O� &�P�]�]�]�S�X�Y�]�Z��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  + ɡ,�Q�G�  / ɤ�2āW�\�R��G'�   79 �8�T�:�U�e_DeltaEditorLayoutExtension�   >A �?7�V�a�B��W���e^documentLength�#@      �   JM �HL�h�[��B���W�e^documentLength�   S^ 0�TUVWXYZ[\]�]�^�_�`�a�b�c�d�e�f�_cLrg &�g�]�]�]�j�.�G�]�o��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  u ɡv�h�G�  y ɤ�|�W�\�i�\�G
�   �� ���k���l�e_DeltaEditorLayoutExtension�   �� ��7�m�a����n���e^documentLength��   �� �H��h�p������n�e^documentLength�   �� 0������������r�s�t�u�v�w�x�y�z�{���� &�|�]�]�]�~�f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��}�G�  � ɤ���:��������G�   �� �Ɂ�ˁ��e_DeltaEditorLayoutExtension�   �� ��7���a��������e^documentLength��   �� �Hۀh����Ӏ����e^documentLength�   �� 0����������������������������������� &���]�]�]���X���]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ���G�   ɤ	�	ā������G�    �������e_DeltaEditorLayoutExtension�    �7���a�������e^documentLength�   "% �H$�h���������e^documentLength�   +6 0�,-./012345���������������������7;? &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  M ɡN���G�  Q ɤ
�
��ʁ&�ʁ&�G�   XZ �Y���[���e_DeltaEditorLayoutExtension�   _b �7a�a����d�����e^documentLengthk�   il �Hk�h����d�����e^documentLength�   r} 0�stuvwxyz{|���������������������~�� &���]�]�]���q�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ����G�  � ɤ�\�\�\�\�G�   �� ���������e_DeltaEditorLayoutExtension�   �� ��7���a��������e^documentLength�   �� �H��h����������e^documentLength�   �� 0����������ÁāŁƁǁȁɁʁˁ̪����� &�̀]�]�]�ЁՁ��]�ր�+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡہ΀G�  � ɤ���π\�π\�G1�   �� ��ѡ�Ҁe_DeltaEditorLayoutExtension�   �� ��7�Ӏa����Ԁ��e^documentLengths#@"      �   �� �H��h�ע�񀄁Ԁe^documentLength�    0�	
�فځہ܁݁ށ߁����L, &��]�]�]��.���]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  # ɡ$��G�  ' ɤvav/�������"�G�   .0 �/��1��e_DeltaEditorLayoutExtension�   58 �67��a�9:��c�e^documentLength�   ?B �HA�h��:9�c��e^documentLength�   HT 0�IJKLMNOPQRS����������������UY[^ &���]�]�]���q���\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  m ɡn���G�  q ɤ�\�\�\�\�G�   xz �y���{���e_DeltaEditorLayoutExtension�   � ��7���a��������e^documentLength��   �� 0����� ���LN�.�]�/�+_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_XcodePreviews.PinnedPreview�   �� �H��h���������e^documentLength�   �� 0���������������	�
��������� &��]�]�]��f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ�G�  � ɤ
��
���B�]�B�]�G�   �� �́�ρ�e_DeltaEditorLayoutExtension�   �� ��7��a�������e^documentLength��   �� �H߀h���׀���e^documentLength�   �� 0������������������ �!�"�#���� &�$�]�]�]�'�f�\�]�,��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ	�%�G�   ɤrr�&�G�&�G�G�    ��(��)�e_DeltaEditorLayoutExtension�    �7�a�*�� ���+�e^documentLength��   %( �H'�h�-�� ���+�e^documentLength�   .9 0�/012345678�/�0�1�2�3�4�5�6�7�8�:>
�&B &�9�]�]�]�;�A�[�]�@��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  P ɡQ�:�G�  T ɤ������]��]�G�   [] �\�<�^�=�e_DeltaEditorLayoutExtension�   be �7d�a�>��g���?�e^documentLength��   lo �Hn�h�A��g���?�e^documentLength�   u� 0�vwxyz{|}~�C�D�E�F�G�H�I�J�K�L����� &�M�]�]�]�P�U�\�]�V��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��N�G�  � ɤS�S��H�O�H�O�G�   �� ���Q���R�e_DeltaEditorLayoutExtension�   �� ��7�S�a��:�T�c�e^documentLength6#@       �   �� �H��h�W�:��c�T�e^documentLength�   �� 0�����������ɁY�Z�[�\�]�^�_�`�a�b�c����� &�d�]�]�]�f�q�j�\�]�n��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ�e�G�  � ɤ�\�\�\�\�G�   �� ��g��h�e_DeltaEditorLayoutExtension�   �� ��7�i�a��������e^documentLength�   � 0�� �k�l�m�NL�/�]�.�+_XcodePreviews.PinnedPreview_XcodePreviews.ShowViewBounds_XcodePreviews.Zoom�    �H�h�o��������e^documentLength�    0��q�r�s�t�u�v�w�x�y�z� $( &�{�]�]�]�}�f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  6 ɡ7�|�G�  : ɤ�:�:�������G�   AC �B�~�D��e_DeltaEditorLayoutExtension�   HK �I7���a�L������e^documentLengthU�   RU �HT�h����L�����e^documentLength�   [f 0�\]^_`abcde���������������������gko &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  } ɡ~���G�  � ɤ�\�\�\�\�G�   �� ���������e_DeltaEditorLayoutExtension�   �� �7��a����������e^documentLengthk�   �� �H��h����������e^documentLength�   �� 0����������������������������������� &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡŁ��G�  � ɤ�����\���\�G,�   �� �с��Ӂ��e_DeltaEditorLayoutExtension�   �� ��7���a��������e^documentLengthF�   �� �H�h����ۀ����e^documentLength�   �� 0���������������������������������
�� &���]�]�]���A�0�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ���G�   ɤ+�+����^���^�G�    �������e_DeltaEditorLayoutExtension�   ! �7 �a���:#�c���e^documentLength��   (+ �H*�h���:#�c���e^documentLength�   1< 0�23456789:;�ÁāŁƁǁȁɁʁ˪=AE &�̀]�]�]�΁q�\�]�Ӏ�+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  S ɡT�̀G�  W ɤ�\�\�\�\�G�   ^` �_�ϡa�Ѐe_DeltaEditorLayoutExtension�   eh �7g�a�Ѣ�j���Ҁe^documentLength>��   or �Hq�h�Ԣ�j���Ҁe^documentLength�   x� 0�yz{|}~����ցׁ؁فځہ܁݁ށߪ��� &���]�]�]��f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ���G�  � ɤ����\��\�Gz�   �� �������e_DeltaEditorLayoutExtension�   �� �7��a��������e^documentLength��   �� �H��h��������e^documentLength�   �� 0����������ʁ��������������� &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ���G�  � ɤ�\�\�\�\�G�   �� ��������e_DeltaEditorLayoutExtension�   �� ��7���a��������e^documentLength��   � �H �h����������e^documentLength�    0�	
��� ��������� &�	�]�]�]��f�\�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ) ɡ*�
�G�  - ɤ�Y�\�Y�\�G�   46 �5��7��e_DeltaEditorLayoutExtension�   ;> �<7��a�?�����e^documentLength��   EH �HG�h���?����e^documentLength�   NY 0�OPQRSTUVWX�����������Z^
��b &��]�]�]��A���]�#��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  p ɡq��G�  t ɤ	�	ā������G�   {} �|� �~�!�e_DeltaEditorLayoutExtension�   �� ��7�"�a�������e^documentLength�   �� �H��h�$�������e^documentLength�   �� 0������������&�'�(�)�*�+�,�-�.�/���^�� &�0�]�]�]�3���8�]�9��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��1�G�  � ɤ�	:�	:�2���2���Gy�   �� �Á4�Ł5�e_DeltaEditorLayoutExtension�   �� �7ˀa�6��΀��7�e^documentLength�A�   �� �Hրh�:��΀��7�e^documentLength�   �� 0�����������<�=�>�?�@�A�B�C�D�E���� &�F�]�]�]�I�f�\�]�N��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ �G�G�   ɤ	:	:�H���H���G)�    ��J��K�e_DeltaEditorLayoutExtension�    �7�a�L�:�c�M�e^documentLength��    �H�h�O�:�c�M�e^documentLength�   %0 0�&'()*+,-./�Q�R�S�T�U�V�W�X�Y�Z�15
�	9 &�[�]�]�]�^�A���]�c��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  G ɡH�\�G�  K ɤLPLP�]�߁]�߀G2�   SU �T�_�V�`�e_DeltaEditorLayoutExtension�   Z] �7\�a�a��_���b�e^documentLengthC�   dg �Hf�h�d��_���b�e^documentLength�   mx 0�nopqrstuvw�f�g�h�i�j�k�l�m�n�o�y}� &�p�]�]�]�r�f�\�]�w��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��q�G�  � ɤ
��
���&�@�&�@�G�   �� ���s���t�e_DeltaEditorLayoutExtension�   �� ��7�u�a����v���e^documentLength�   �� �H��h�x������v�e^documentLength�   �� 0������������z�{�|�}�~������������� &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡׁ��G�  � ɤ|�|܁i���i���G�   �� �������e_DeltaEditorLayoutExtension�   �� �7�a�������e^documentLength��   �� �H��h�������e^documentLength�   � 0���� ���������������������^ &���]�]�]�������]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   ɡ���G�  " ɤ�Ɂ0���0���G�   )+ �*���,���e_DeltaEditorLayoutExtension�   03 �17���a�4������e^documentLength!�
�   ;> �H=�h����4�����e^documentLength�   DO 0�EFGHIJKLMN���������������������PTX &���]�]�]���f�\�]����+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  f ɡg���G�  j ɤ����\��\�G�   qs �r���t���e_DeltaEditorLayoutExtension�   x{ �7z�a����}�����e^documentLength��   �� �H��h����}�����e^documentLength�   �� 0����������������������������������� &�]�]�]�Āf�\�]�ɀ�+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡ��ÀG�  � ɤS�SāH��H��G�   �� ���š��ƀe_DeltaEditorLayoutExtension�   �� �7��a�Ǣ�Ā��Ȁe^documentLength�   �� �Hˀh�ʢ�Ā��Ȁe^documentLength�   �� ���Ձ́́Σ��فρӁ׀e_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKey�   �� 0���С�р+[TheMealsApp�� ��WNS.time#AĚ�G"6��� ] ^��VNSDate�� a�   �� 0��ԡ��Հ+[TheMealsApp� ���YNS.string��_;76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9_iphonesimulator_x86_64� ] ^��_NSMutableString��� aXNSString�   �� 0���ء��ـ+_;76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9_iphonesimulator_x86_64�� �#AĚ�G[-<���    ��ۡ	�܀e]IDENameString[TheMealsApp�    ��ށ߁��������� & & !"#����]�������eZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9Vx86_64_iphonesimulator16.1ViPhoneZiPhone15,2�   7< 0�89:;������=>?@��������+_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildV14B47b��  JN�KLM�������   PT 0�QRS�������
�V=�A����+_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ �   ^b 0�QRS�������Lde�.�����+c  %  �i jkWNS.dataObplist00�
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	Troot��)*0:;<#=AIJKLMSWX\_U$null�XNSStringV$class\NSAttributes���YSucceeded�WNS.keysZNS.objects�����VNSFont� !"#$%&'(VNSSizeXNSfFlags\NSDescriptorZNSHasWidthVNSName#@&      ����_.AppleSystemUIFontBold�+,-./_NSFontDescriptorOptions_NSFontDescriptorAttributes�����15�234�	�
��678����_NSFontSizeAttribute_ NSCTFontFeatureSettingsAttribute_NSCTFontUIUsageAttribute�>@�?���BE�CD���FG���_CTFeatureSelectorIdentifier_CTFeatureTypeIdentifier �NOPQZ$classnameX$classes\NSDictionary�PRXNSObject�NOTU^NSMutableArray�TVRWNSArray_CTFontBoldUsage�NOYZ_NSFontDescriptor�[R_NSFontDescriptor�NO]^VNSFont�]R�NO`a_NSAttributedString�bR_NSAttributedString    $ ) 2 7 I L Q S o u | � � � � � � � � � � � � � � � � � � � �	!#<C]z|�������������������	4NPRWbkx{��������������             c              %� � ] ^mn]NSMutableData�mo aVNSData�   qx 0�QstSvw���������^�{|�ā�������+_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle�� ��#Aě�����^Today at 16.08� ] ^��^NSMutableArray�� � a�  � ɡ ��G�   �� 0� �����+�  �N� ���  �N���  � ɪ Q��� �� �����߀ՀˀL���J������G�   �� 0���������������� &�������]����]�+_IDEWindowToolbarIsVisible^IDEWindowFrame_&IDEWindowTabBarWasVisibleWithSingleTab_(IDEWorkspaceWindow_NSRestorableStateData_IDEActiveWorkspaceTabController_>IDEWorkspaceTabController_933AE80D-5978-481C-9637-73C7D8929EFE_IDEWindowTabBarIsVisible_{{1679, 30}, {1322, 1025}}�i �kO4bplist00�0X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	
 !"#$%'()+,-./[NSStyleMask]NSWindowFrame_NSFirstResponder_NSManagedFrameIsUserDefined_&NSWindowManagementPersistentIdentifier__NSWindowLayouts^NSWindowNumber__NSWindowRestorer_NSUnmanagedWindowFrame2WNSTitleWNSIsKey__NSWindowResizeGenerationXNSTabIdx_NSWindowWorkspaceIDXNSIsMain[NSClassNameVNSUIID_NSUIPreviousFrameZNSTabBrVisZNSTabGrpID���	��y�� �	 �	���(�'�)123456789@AGU[\]`defjklpqryz{~�����������U$null_$B24954C0-1B4E-4D32-BD8B-EF32C5F4CAA9_#1679 30 1322 1025 1680 0 1920 1055 U_NS:8o T h e M e a l s A p p      A s s e t s . x c a s s e t s_IDEWorkspaceWindow_$A370BEB6-8D1B-4CD5-83E3-A4BB8517370B_IDEDocumentController�:;<=>?V$classXwindowID_persistentIdentifier�
��	V_NS:92�BCDEZ$classnameX$classes_NSPersistentUIEncodedReference�DFXNSObject�HI:JOTWNS.keysZNS.objects�KLMN�����PQRS��� �#�&�VW:XYZ_NSScreenLayoutUUIDString_NSScreenLayoutSize���_$F2B5A86A-9901-8E02-AF53-20B8D78B142E\{1920, 1080}�BC^_^NSScreenLayout�^F�VW:abZ���_$85A2805F-F0DD-0306-8C8C-4F1AD0C2EEC5\{1920, 1080}�VW:ghZ���_$6FAF45A8-9789-0F06-22FA-018734C0F4CF\{1680, 1050}�VW:mnZ���_$42591195-F398-F010-A125-104918E150C1\{1920, 1080}�st:uv'wx_NSWindowLayoutScreenLayoutFrame_NSWindowLayoutResizeGeneration_NSWindowLayoutWindowFrame���_{{1679, 30}, {1322, 1025}}_{{1680, 0}, {1920, 1055}}�BC|}^NSWindowLayout�|F�st:u�w����_{{1680, -62}, {1920, 1055}}_{{1680, -62}, {1920, 1055}}�st:u��w��"��!_{{0, 0}, {1680, 1025}}_{{0, 0}, {1680, 1025}}�st:u��w��%��$_{{2505, 163}, {1078, 635}}_{{1680, -62}, {1920, 1055}}�BC��\NSDictionary��F^0x60000184f2a0_0 0 1680 1025 0 0 1680 1025     $ ) 2 7 I t � � � � � �
8@Hdm���������������������������!Hnt���3579@EPYz}��������������������� -2ADKMOQx������������2Soqsu�����������!#<U^`bdf������             �              � �   �� 0���������Ё������� �!� &������ &ڀ�"�r�s�w�x�z��}�+_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorArea�   �� 0�������#�$�%�&�'�(�������)�5�G�Y�Z�a�+_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Find_Xcode.IDEKit.Navigator.Debug_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigator�   � 0���� �*�+�,�-��.�/�0�.�+_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey�    ���e_IDENavigatorModeSolitary�    ��1��2�e_IDENavigatorModeSolitary� _codablePlistRepresentation�4�3O�bplist00�     ���YitemState_lastAccessedDate^scrollPosition]selectedItems�h      # ' * . 3 7 ; ? B F L P T W [ _ d h k o r v z ~ � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � "%),037:>AEIMPTX\`dgknruy|�������   	 TpathYindexHint� 
   [TheMealsApp[TheMealsAppTCoreUUtils�    _lastAccessedDate^expansionState3Aě�]W� �    �     [TheMealsAppVMapper�    _lastAccessedDate^expansionState3Aě�K��    �     ! "[TheMealsApp[TheMealsAppTDataVRemote� $ % & _lastAccessedDate^expansionState3Aě�]-��   ( � )   ! "[TheMealsApp� + , - _lastAccessedDate^expansionState3Aě�K��   / � 0  1 2[TheMealsAppVModuleZDetailMeal� 4 5 6 _lastAccessedDate^expansionState3Aě�K��   8 � 9 :[TheMealsApp[TheMealsApp� < = > _lastAccessedDate^expansionState3Aě�]	J�   @ � A[TheMealsApp� C D E _lastAccessedDate^expansionState3Aě�\��   G � H I 1 J K[TheMealsApp[TheMealsAppVDetailVRouter� M N O _lastAccessedDate^expansionState3Aě�K�-�   Q � R I 1 S K[TheMealsAppVSearch� U V O _lastAccessedDate^expansionState�   X � Y    Z[TheMealsAppYExtension� \ ] ^ _lastAccessedDate^expansionState3Aě�K�>�   ` � a   b c[TheMealsAppVDomainUModel� e f g _lastAccessedDate^expansionState3Aě�]B>�   i � j  [TheMealsApp� l m n _lastAccessedDate^expansionState3Aě�]�   p � q   b c[TheMealsApp� s t u _lastAccessedDate^expansionState3Aě�K�N�   w � x I 1 y[TheMealsAppTMeal� { | } _lastAccessedDate^expansionState3Aě�]�w�    �� �  1[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]h��   � � �  1 J[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�K�W�   � � � I   �[TheMealsAppTView� � � � _lastAccessedDate^expansionState3Aě�]^��   � � � I 1 S �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�K�_�   � �� �  1 � �[TheMealsAppTHome� � � � _lastAccessedDate^expansionState3Aě�]��   � �� � I 1 � �[TheMealsAppXFavorite� � � � _lastAccessedDate^expansionState3Aě�]�?�   � �� � I 1 J �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]w��   � �� � I 1 S �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]���   � � � �  ! �[TheMealsApp[TheMealsAppVLocale� � � � _lastAccessedDate^expansionState3Aě�]!��   � � � I 1 �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]~��   � � �  1 � �[TheMealsAppYPresenter� � � � _lastAccessedDate^expansionState3Aě�]��   � � � I 1 J[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]o �   � � �   �[TheMealsAppRDI� � � � _lastAccessedDate^expansionState3Aě�]53�   � � �  1 2 �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�K���   � � �   1 J �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]p��   � � � I 1 � �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]���   � � � I 1 y �[TheMealsApp� � � � _lastAccessedDate^expansionState3Aě�]��   � � � I 1 S �[TheMealsApp� _lastAccessedDate^expansionState3Aě�]�p�   �� I 1 �[TheMealsApp�	
 _lastAccessedDate^expansionState3Aě�]��   � �  ! �[TheMealsAppVEntity� _lastAccessedDate^expansionState3Aě�K���   � �  [TheMealsAppZExtensions� _lastAccessedDate^expansionState�   � [TheMealsAppSApp� ! _lastAccessedDate^expansionState3Aě�]o�  # �$ �  ! �[TheMealsApp�&'( _lastAccessedDate^expansionState3Aě�](R�  * �+   ![TheMealsApp�-./ _lastAccessedDate^expansionState3Aě�]��  1 ��2   b[TheMealsApp�456 _lastAccessedDate^expansionState3Aě�];��  8 �9 I 1 � K[TheMealsApp�;<= _lastAccessedDate^expansionState3Aě�]�]�  ? �@  1 � K[TheMealsApp�BCD _lastAccessedDate^expansionState3Aě��	��  FH�G I 1 S[TheMealsApp�JKL _lastAccessedDate^expansionState3Aě�]���  N �O  1 �[TheMealsApp�QRS _lastAccessedDate^expansionState3Aě�K���  U �V   ! "W[TheMealsAppXResponse�YZ[ _lastAccessedDate^expansionState3Aě�]2��  ] �^   b_[TheMealsAppWUseCase�abc _lastAccessedDate^expansionState3Aě�]K��  e �f  1 J �[TheMealsApp�hij _lastAccessedDate^expansionState3Aě�K���  l �m I 1 y �[TheMealsApp�opq _lastAccessedDate^expansionState3Aě�]�7�  s �t   ![TheMealsApp�vwx _lastAccessedDate^expansionState3Aě�K���  z ��{ I 1 � �[TheMealsApp�}~ _lastAccessedDate^expansionState3Aě�K���  � ��� I 1 S �[TheMealsApp��� _lastAccessedDate^expansionState�  � �� I 1 J �[TheMealsApp��� _lastAccessedDate^expansionState�  � ���   �[TheMealsAppWNetwork���� _lastAccessedDate^expansionState3Aě�K��3Aě%�������#        #@��     ���#@p     #@�     ���  � ����[TheMealsApp[TheMealsApp_Assets.xcassets   # 6 E S&/4>GS_djlu�������������+07@Sbkt�����������#,1=IRet}������������.7@KW^gz�����������0?HQXdm������������
+:CLS_aj}�����������(7@IT`i|�����������2AJS^js�����������				%	.	A	P	Y	b	k	w	�	�	�	�	�	�	�	�	�	�
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
�
�
�
�
�
�
�
�
�
�$-@OXalx���������� 	 )<KT]fr{����������#.7JYbiuy����������!*=LU^gs|����������$7FOXamox����������� )<KT]ht|����������$-@OXajv����������':IR]ir������������#,/8?KW            �              i� ] ^_&ExplorableOutlineViewArchivableUIState�  a_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier_�   "% 0�#$�6�7�&N�8�/�+_queryParametersController]filterPattern�   ,1 0�-./0�9�:�;�<�2345�=�>�?�@�+_presentingScopeChooser_lastEasyToInitiateQueryClass[queryAction_querySpecification _IDEBatchFindTextQueryTfind�?@ABC DE- XHIYanchoringYqueryTermYmatchCaseZnamedScopeZqueryClass�E�A� �D�F� KLMN X,P_explicitDisplayStringXtermTypeTtext�C� �B� ��S��P� ] ^UV_IDEBatchFindQueryTerm�W a_IDEBatchFindQueryTerm_IDEBatchFindTextQueryTnone� ] ^[\_IDEBatchFindQuerySpecification�] a_IDEBatchFindQuerySpecification�   _f 0�`abcde�H�I�J�K�L�M�gh &k�N�O�\��X�]�+_IDEStackCompressionValue_IDEDebugTransientStates_DBGNavigatorContentMode_IDEShowOnlyRunningBlocks^IDEVisibleRect_IDEShowOnlyInterestingContent�   v{ 0�wxyz�P�Q�R�S�|} &�T�V��W�+_IDEDebugExpandedItems_#IDECurrentLaunchSessionReferenceKey_1IDEHaveInitiallyExpandedCPUDebuggingChildrenState_IDEDebugSelectedNavigableItems�  � ����U�%[TheMealsApp\7fc6c48ac7f0�  �N��_{{0, 0}, {257, 920}}_ Xcode.IDEKit.Navigator.Workspace�   �� 0������[�\�]�^������_�%�Y�`�+_"Xcode.IDEKit.NavigatorGroup.Issues_!Xcode.IDEKit.NavigatorGroup.Debug_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_#Xcode.IDENoticesKit.NoticeNavigator_Xcode.IDEKit.Navigator.Find�   �� 0������b�c�d�e������f�i�j�n�+_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey�   �� ���g���h�e_IDENavigatorModeSolitaryOnbplist00�
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	Troot��!$'+,U$null�V$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiers� ����ZNS.objects���Z$classnameX$classesWNSArray� XNSObject�"#���%&^NSMutableArray�% �(#�)��_IDEFilterIdentifier_NoticeError�-.__DVTFilterExpressionStateValue�/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u � � � � � � � � � � � � � � �!&59>@BDfk����             2              �_IDENavigatorModeSolitary�   �� ���k���l�e_IDENavigatorModeSolitary� ��_codablePlistRepresentation�4�mO�bplist00�YitemState_lastAccessedDate^scrollPosition]selectedItems�3Aě �Z|[�
�		#        �#@��     #@��     �.=KLUX[dgpy                            z�   �� �Ɂo�ˁp�e_IDENavigatorModeSolitary� ��_codablePlistRepresentation�4�qO�bplist00�YitemState_lastAccessedDate^scrollPosition]selectedItems�3Aě �Z||�
�		#        �#@p     #@��     �.=KLUX[dgpy                            z#@p@     �   �� 0�ցt�؁u�+_!userPreferredCategoryExtensionIDs�  �N�݁v�_#Xcode.IDEKit.InspectorCategory.File#@p     �   �� 0��y� &��+_ShowsOnlyVisibleViewObjects�   �� 0���{�|��]�]�+_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspace�   �� 0�������������~�������������������� 	
���\���������S�]�i�j��+_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ EditorMultipleSplitPrimaryLayout_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_DebuggerSplitView_ DefaultPersistentRepresentations_IDEDefaultDebugArea^MaximizedState� �,_NSIndexPathLength_NSIndexPathValue��� ] ^[NSIndexPath� a[NSIndexPathZOpenInTabs _Layout_LeftToRight�  #N�$����   '. 0�()*+,-�������������/01/3�����2���R�]�+ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments �   >B 0�?@A�������Crr���G�G�+_TabsAsHistoryItems_SelectedTabIndex_DynamicTabIndex�  K ɥLMNOP���Ɓ�
��G�STUV WXYZ[\]^_�a_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifier���������ŁĀՁ��cde fghijkl_DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifier�����������\_/Xcode.IDENavigableItemDomain.WorkspaceStructure�  p ɤqrst���������G�wx Z�{ZIdentifierUIndex�����_ContentView.swift� ] ^~_IDEArchivableStringIndexPair�� a_IDEArchivableStringIndexPair�wx �{���\��SApp�wx �{���\��[TheMealsApp�wx �{���\��[TheMealsApp� �  ��� X�ց�� � ] ^��_DVTDocumentLocation�� a_DVTDocumentLocation� ���ZpathString����_b/Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj� ] ^��[DVTFilePath�� a[DVTFilePath� ] ^��_(IDENavigableItemArchivableRepresentation�� a_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCode�   �� 0������������������������������������ &���]�]�]���U�\�]��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡˁ��G�  � ɤS�S��H�O�H�O�G�   �� �ց��؁��e_DeltaEditorLayoutExtension�   �� ��7���a����T���e^documentLength�   �� �H�h�â�����T�e^documentLengthTbody� ] ^��_IDEEditorHistoryItem�� a_IDEEditorHistoryItem�STUV WXY�[��^��a�ʁ��ՁǁŁ�ˁ��cde fg��jkl�ԁȁ������\�   ɦ	�Ɂˁ́ρсҀG�wx �{�ʀ\��_SearchPresenter.swift�wx {�̀\��YPresenter�wx &{�΀[��VSearch�wx g{�ЁN��VModule�wx �{���\���wx ${�Ӏ\��[TheMealsApp� �  ��� X�́�� �   -8 0�./01234567�ցׁ؁فځہ܁݁ށߪ9=L,A &���]�]�]��.���]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  O ɡP��G�  S ɤvav/�������"�G�   Z\ �[��]��e_DeltaEditorLayoutExtension�   ad �b7��a�9��逄�e^documentLength�   jm �Hl�h���9����e^documentLength\searchMeal()�STUV WXYt[vw^_�a�������ŁĀ߁��cde fg}~jkl����������\�  � ɦ��������������G�wx t{��\��_SearchView.swift�wx �g{��N��TView�wx &{�΀[���wx g{�ЁN���wx �{���\���wx �{��\��[TheMealsApp� �  �� X����� �   �� 0������������������������������ ����r� &��]�]�]���G�]���+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � ɡρ�G�  � ɤ+�+����^���^�G�   �� �ځ�܁�e_DeltaEditorLayoutExtension�   �� ��7��a�#������e^documentLength#@(      �   �� �H�h�	��#�����e^documentLength�STUV WXY�� X�^t Q���� ��Ł���cde fg��jkl���������\�   ɣ����G�wx �r{��G��_Assets.xcassets�wx �{���\���wx {��\��[TheMealsApp� �  � Z� X���� _com.apple.dt.assetcatalog_(Xcode.IDEKit.EditorDocument.AssetCatalog�STUV WXY[ ^_�a������ŁĀ����cde fg&'jkl���������\�  - ɤ./01�����G�wx {��\��_TheMealsAppApp.swift�wx �{���\���wx �{���\���wx A{��\��[TheMealsApp� �  �Q� X����� �   JU 0�KLMNOPQRST� �!�"�#�$�%�&�'�(�)�VZ^ &�*�]�]�]�,�f�\�]�0��+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  l ɡm�+�G�  p ɤ+,+,���������G�   wy �x�-�z�.�e_DeltaEditorLayoutExtension�   ~� �7��a�/��?�����e^documentLength�   �� �H��h�1��?�����e^documentLength�  �N���3��� ��_currentEditorHistoryItem�4�Q�STUV WXY����^� Q����<�5�ŁP���cde fg��jkl�;�6�������\�  � ɣ����7�8�9�G�wx �r{��G���wx �{���\���wx �{�:�\��[TheMealsApp� �  � Z� X���� �   �� 0��������ǁ=�>�?�@�A�B�C�D���� p���ЁE�G�I�)�J�K�N�O�+_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area�  � ��܁F�%_./assetSearchMeal.imageset�  � ���H�%_./assetSearchMeal.imageset_IBICCatalogOverviewController�   �� 0���+�   �� 0��L��M�+_expandedItemIDs�  � �� ��/�0^sourceListArea�   �� 0���+_assetSearchMeal� ] ^��_IDEEditorHistoryStack�� a_IDEEditorHistoryStack_ItemKind_Editor�cde fg jklāZ�T��������   ɤ	
�U�V�W�X�G_assetSearchMeal.imageset�wx �r{��G���wx �{���\���wx {�Y�\��[TheMealsApp�  � � X Z \relativePath�\� ��[_./assetSearchMeal.imageset� ] ^#$_IBICCatalogDocumentLocation�%& a_IBICCatalogDocumentLocation_DVTDocumentLocation�   (* 0�)�^�+�_�+_DVTSplitViewItems�  /N�01�`�f��   48 �567�a�b�c�9 &;�d��e�e]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeYIDEEditor#@�h     �   CG �567�a�b�c�H &J�g��h�e_IDEDebuggerArea#@u@     �   OP 0���+�   SX 0�TUVW�k�l�m�n�YZ�\�o�t��v�+XLeftViewYRightViewZLayoutMode_IDESplitViewDebugArea�   ch 0�defg�p�q�r�s�� &�]�\����+_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsType�   su 0�t�u��\�+_ConsoleFilterMode�   z| 0�{�w�}�x�+_DVTSplitViewItems�  �N����y�|��   �� �567�a�b�c�� &��z��{�eXLeftView#@{p     �   �� �567�a�b�c�� &��}��~�eYRightView#@v�               "   ,   1   :   ?   Q   V   \   ^  b  h  u  }  �  �  �  �  �  �  �  �  �  �  �  �        
                -  /  1  4  7  :  =  @  C  F  I  K  b    �  �  �  �  �    +  F  G  T  _  a  c  e  g  i  t  v  x  z  |  ~  �  �  �  "  L  |  �  �  �  �  �  �  �  �  �  �  �  �  .  7  B  K  Q  V  _  l  }    �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �      %  7  E  N  Q  S  U  r  {  �  �  �  �  �  �  �  �  �  �  �  �  �  �      "  /  <  ?  A  D  F  H  Z  c  f  h  j  l  u  z  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  2  ?  H  J  L  N  P  Y  [  ]  _  a  c  �  �  �    3  H  Q  T  V  X  i  s  }  �  �  �  �  �  �            !  #  %  '  /  6  B  Y  b  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �                  �  �  �  �  �  -  :  O  Q  S  U  W  Y  [  ]  _  a  c  x  z  |  ~  �  �  �  �  �  �  �  �  �  �  �    4  Z  ~  �  �  �  �  �  �  �  �  �  �  �          	  
          !  #  @  M  R  T  V  [  ]  _  a  m  |  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �             
      #  %  '  )  +  -  /  1  3  5  7  9  V  u  �  �  �    )  H  g  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �            #  (  *  ,  1  3  5  7  F  S  X  Z  \  a  c  e  g  t  v  x  z  �  �  �  �  �  �         	            ,   5   6   8   E   H   J   M   O   Q   ^   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �   �  !  !  !  !  !	  !  !  !  !  !  !  !  !  !  !  !  !!  !#  !%  !'  !)  !+  !-  !/  !1  !3  !5  !7  !9  !;  !=  !?  !A  !C  !E  !G  !I  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  !�  "  "  "  "
  "  "  "  "  "  "  "  ""  "%  "(  "+  ".  "1  "4  "7  ":  "=  "@  "C  "F  "I  "L  "O  "R  "U  "X  "[  "^  "a  "d  "g  "j  "m  "o  "|  "~  "�  "�  #  #  #  #  #  #�  #�  #�  #�  #�  $=  $J  $L  $N  $P  $�  $�  $�  $�  $�  %}  %�  %�  %�  %�  &  &  &  &!  &#  &�  &�  &�  &�  &�  ':  'G  'I  'K  'M  '�  '�  '�  '�  '�  (z  (�  (�  (�  (�  )  )&  )(  )*  ),  )�  )�  )�  )�  )�  *R  *_  *a  *c  *e  *�  *�  *�  *�  *�  +w  +�  +�  +�  +�  ,  ,  ,  ,  ,!  ,�  ,�  ,�  ,�  ,�  -8  -E  -G  -I  -K  -�  -�  -�  -�  -�  .k  .x  .z  .|  .~  /  /  /  /  /  /�  /�  /�  /�  /�  08  0E  0G  0I  0K  0�  0�  0�  0�  0�  1m  1z  1|  1~  1�  2  2  2  2!  2#  2�  2�  2�  2�  2�  3M  3Z  3\  3^  3`  3�  3�  3�  3�  3�  4�  4�  4�  4�  4�  59  5F  5H  5J  5L  5�  5�  5�  5�  5�  6|  6�  6�  6�  6�  7  7&  7(  7*  7,  7�  7�  7�  7�  7�  8C  8P  8R  8T  8V  8�  8�  8�  8�  8�  9w  9�  9�  9�  9�  :  :"  :$  :&  :(  :�  :�  :�  :�  :�  ;9  ;F  ;H  ;J  ;L  ;�  ;�  ;�  ;�  ;�  <k  <x  <z  <|  <~  =	  =  =  =  =  =�  =�  =�  =�  =�  >N  >[  >]  >_  >a  >�  >�  ?   ?  ?  ?{  ?�  ?�  ?�  ?�  @  @#  @%  @'  @)  @�  @�  @�  @�  @�  AS  A`  Ab  Ad  Af  A�  A�  A�  A�  A�  B�  B�  B�  B�  B�  C(  C5  C7  C9  C;  C�  C�  C�  C�  C�  Dg  Dt  Dv  Dx  Dz  E  E  E  E  E  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  E�  F   F  F  F  F	  F  F  F  F-  FL  Fp  F�  F�  F�  G   G  G>  G[  Gd  Gg  Gj  Gl  Gu  G~  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  G�  H
  H  H  H  H  H  H  H   H/  H<  HQ  HT  HW  HZ  H]  H`  Hc  Hf  Hi  Hl  Ho  H�  H�  H�  H�  H�  H�  H�  H�  H�  H�  H�  H�  H�  H�  H�  I  IC  Ii  I�  I�  I�  I�  I�  I�  I�  I�  J  J  J  J  J  J  J  J  J  J*  J-  J0  J3  J6  J8  JU  Jb  Jg  Jj  Jl  Jq  Jt  Jv  Jx  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  J�  K  K  K  K  K  K  K  K   K"  K%  K'  K)  KF  Ke  K�  K�  K�  K�  L  L8  LW  Lt  L}  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  L�  M  M  M  M   M"  M%  M*  M,  M/  M1  M@  MM  Mb  Me  Mh  Mk  Mn  Mq  Mt  Mw  Mz  M}  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  M�  N  N0  NT  Nz  N�  N�  N�  N�  O  O  O  O
  O  O  O  O"  O%  O(  O*  O,  O9  O<  O?  OB  OE  OG  Od  Oq  Ov  Ox  O{  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  O�  P  P  P  P  P   P#  P%  P'  P)  P,  P.  P0  P2  P5  P7  P9  PV  Pu  P�  P�  P�  Q  Q)  QH  Qg  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  Q�  R  R  R  R  R  R  R  R#  R&  R3  R8  R:  R=  RB  RD  RG  RI  RX  Re  Rz  R}  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  R�  S  S&  SH  Sl  S�  S�  S�  S�  T  T  T  T   T"  T+  T4  T6  T8  T:  T<  T>  TK  TN  TQ  TT  TW  TY  Tv  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  T�  U  U  U  U
  U  U  U  U  U  U  U1  U4  U6  U8  U:  U=  U?  UA  UC  UF  UH  UJ  Ug  U�  U�  U�  U�  V  V:  VY  Vx  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  V�  W  W  W  W  W  W  W   W#  W%  W4  W7  WD  WI  WK  WN  WS  WU  WX  WZ  Wi  Wv  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  W�  X  X9  X[  X  X�  X�  X�  Y  Y$  Y-  Y0  Y3  Y5  Y>  YG  YJ  YL  YO  YQ  YS  YU  YW  Yd  Yg  Yj  Ym  Yp  Yr  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Y�  Z  Z  Z#  Z&  Z)  Z,  Z/  Z2  Z5  Z8  Z;  Z>  ZA  ZV  ZY  Z[  Z]  Z_  Zb  Zd  Zf  Zh  Zk  Zm  Zo  Z�  Z�  Z�  Z�  [  [;  [_  [~  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  [�  \  \  \  \#  \0  \5  \7  \:  \?  \A  \D  \F  \U  \X  \e  \j  \l  \o  \t  \v  \y  \{  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  \�  ]  ]4  ]X  ]z  ]�  ]�  ]�  ^  ^&  ^C  ^L  ^O  ^R  ^T  ^]  ^f  ^i  ^l  ^o  ^r  ^t  ^v  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  ^�  _  _  _  _  _"  _7  _:  _=  _@  _C  _F  _I  _L  _O  _R  _U  _j  _m  _o  _q  _s  _v  _y  _|  _~  _�  _�  _�  _�  _�  _�  `  `+  `Q  `u  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  `�  a  a  a  a  a  a  a  a  a;  aH  aM  aO  aR  aW  aY  a\  a^  am  ap  ay  a{  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  a�  b  b  b  b	  b  b  b  b  b  b  b  b  b8  bW  b{  b�  b�  b�  c  c*  cI  cf  co  cr  cu  cw  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  c�  d  d  d  d  d  d  d"  d$  d'  d)  d8  dE  dZ  d]  d`  dc  df  di  dl  do  dr  du  dx  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  d�  e  e*  eN  et  e�  e�  e�  e�  e�  e�  f  f  f  f  f  f  f  f   f"  f$  f1  f4  f7  f:  f=  f?  f\  fi  fn  fp  fs  fx  fz  f}  f  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  f�  g  g  g  g
  g!  g$  g&  g(  g*  g-  g0  g3  g6  g8  g;  g=  g?  g\  g{  g�  g�  g�  h  h6  hZ  hy  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  h�  i  i  i!  i.  i3  i6  i8  i=  i@  iB  iD  iS  iV  i_  il  is  iv  iy  i|  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  i�  j  j  j	  j  j  j  j*  j?  jB  jE  jH  jK  jN  jQ  jT  jW  jZ  j]  jr  ju  jw  jy  j{  j~  j�  j�  j�  j�  j�  j�  j�  j�  j�  k  k3  kY  k}  k�  k�  k�  k�  k�  k�  k�  k�  k�  k�  l   l  l  l  l	  l  l  l  l  l"  l$  lA  lN  lS  lU  lX  l]  l_  lb  ld  ls  lv  l  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  l�  m  m  m  m  m  m  m  m  m  m  m  m!  m>  m]  m�  m�  m�  m�  n  n0  nO  nl  nu  nx  n{  n}  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  n�  o	  o  o  o  o   o#  o(  o*  o-  o/  o>  oK  o`  oc  of  oi  ol  oo  or  ou  ox  o{  o~  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  o�  p  p/  pS  py  p�  p�  p�  p�  q  q  q  q	  q  q  q  q!  q$  q'  q)  q6  q9  q<  q?  qB  qD  qa  qn  qs  qv  qx  q}  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  r  r  r  r  r  r  r&  r)  r+  r-  r/  r2  r5  r8  r:  r=  r?  rA  r^  r}  r�  r�  r�  s  s1  sP  so  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  t  t  t
  t  t  t  t  t  t'  t*  t3  t@  tE  tG  tJ  tO  tQ  tT  tV  te  tr  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  t�  u  u3  uU  uy  u�  u�  u�  v  v  v'  v*  v-  v/  v8  vA  vD  vF  vI  vL  vN  vP  vR  v_  vb  ve  vh  vk  vm  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  v�  w  w  w  w  w  w"  w%  w(  w+  w.  w1  wF  wI  wK  wM  wO  wR  wT  wV  wX  w[  w]  w_  w|  w�  w�  w�  x  x+  xO  xn  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  y  y   y%  y'  y*  y/  y1  y4  y6  yE  yH  yU  yZ  y\  y_  yd  yf  yi  yk  yz  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  y�  z  z$  zH  zj  z�  z�  z�  z�  {  {3  {<  {?  {B  {D  {M  {V  {Y  {[  {^  {`  {b  {d  {q  {t  {w  {z  {}  {  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  {�  |  |  |%  |(  |+  |.  |1  |4  |7  |:  |=  |@  |C  |X  |[  |]  |_  |a  |d  |f  |h  |j  |m  |o  |q  |�  |�  |�  |�  }  }=  }a  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  }�  ~  ~  ~  ~
  ~'  ~4  ~9  ~<  ~>  ~C  ~F  ~H  ~J  ~Y  ~\  ~i  ~n  ~p  ~s  ~x  ~z  ~}  ~  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�    8  \  ~  �  �  �  �  �*  �G  �P  �S  �V  �X  �a  �j  �m  �o  �r  �t  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �4  �7  �:  �=  �@  �C  �F  �I  �L  �O  �R  �g  �j  �l  �n  �p  �s  �u  �w  �y  �|  �~  ��  ��  ��  ��  �  �&  �L  �p  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �	  �  �  �  �  �  �4  �A  �F  �I  �K  �P  �S  �U  �W  �f  �i  �v  �{  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �	  �&  �E  �i  ��  ��  ��  ��  �  �7  �T  �]  �`  �c  �e  �n  �w  �z  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �"  �/  �D  �G  �J  �M  �P  �S  �V  �Y  �\  �_  �b  �w  �z  �|  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �6  �\  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �
  �  �  �  �  �!  �$  �'  �)  �F  �S  �X  �[  �]  �b  �e  �g  �i  �x  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �	  �  �  �  �  �  �  �  �  �:  �Y  �}  ��  ��  ��  �  �,  �K  �h  �q  �t  �w  �y  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �   �%  �'  �*  �/  �1  �4  �6  �E  �R  �g  �j  �m  �p  �s  �v  �y  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �7  �[  ��  ��  ��  ��  �   �	  �  �  �  �  �#  �&  �(  �+  �.  �0  �2  �?  �B  �E  �H  �K  �M  �j  �w  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �
  �  �  �  �  �  �  �1  �4  �6  �8  �:  �=  �@  �C  �E  �H  �J  �L  �i  ��  ��  ��  ��  �  �<  �[  �z  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �  �!  �#  �2  �5  �B  �G  �I  �L  �Q  �S  �V  �X  �g  �t  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �5  �W  �{  ��  ��  ��  �  �   �)  �,  �/  �1  �:  �C  �F  �I  �L  �O  �Q  �^  �a  �d  �g  �j  �l  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �!  �$  �'  �*  �-  �0  �E  �H  �J  �L  �N  �Q  �T  �W  �Y  �\  �^  �`  �}  ��  ��  ��  �  �,  �P  �o  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �#  �(  �+  �-  �2  �5  �7  �9  �H  �K  �M  �Z  �_  �a  �d  �i  �k  �n  �p  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �)  �M  �o  ��  ��  ��  ��  �  �8  �A  �D  �G  �I  �R  �[  �^  �a  �d  �g  �i  �v  �y  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �*  �-  �0  �3  �6  �9  �<  �?  �B  �E  �H  �]  �`  �b  �d  �f  �i  �l  �n  �p  �s  �u  �w  ��  ��  ��  ��  �  �C  �g  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �
  �'  �4  �9  �<  �>  �C  �F  �H  �J  �Y  �f  �k  �m  �p  �u  �w  �z  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �7  �[  �}  ��  ��  ��  �
  �)  �F  �O  �R  �U  �W  �`  �i  �l  �n  �q  �s  �u  �w  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �,  �A  �D  �G  �J  �M  �P  �S  �V  �Y  �\  �_  �t  �w  �y  �{  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �5  �[  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �	  �  �  �  �  �!  �$  �&  �C  �P  �U  �X  �Z  �_  �b  �d  �f  �u  �x  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �	  �  �  �  �  �  �  �  �  �  �!  �#  �@  �_  ��  ��  ��  ��  �  �>  �]  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �!  �0  �3  �@  �G  �J  �M  �P  �W  �Z  �\  �_  �a  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �
  �  �  �  �  �  �  �  �"  �%  �:  �=  �?  �A  �C  �F  �H  �J  �L  �O  �Q  �S  �p  ��  ��  ��  ��  �  �C  �b  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �#  �&  �(  �*  �9  �<  �I  �N  �P  �S  �X  �Z  �]  �_  �n  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �<  �^  ��  ��  ��  ��  �
  �'  �0  �3  �6  �8  �A  �J  �M  �P  �S  �V  �X  �Z  �g  �j  �m  �p  �s  �u  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �!  �$  �'  �*  �-  �0  �3  �6  �9  �N  �Q  �S  �U  �W  �Z  �]  �_  �a  �d  �f  �h  ��  ��  ��  ��  �  �4  �X  �w  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �)  �.  �0  �3  �8  �:  �=  �?  �N  �Q  �^  �c  �e  �h  �m  �o  �r  �t  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �.  �R  �t  ��  ��  ��  �  �   �=  �F  �I  �L  �N  �W  �`  �c  �f  �i  �l  �n  �p  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �	  �  �%  �<  �?  �B  �E  �H  �K  �N  �Q  �T  �W  �Z  �]  �t  �w  �y  �{  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �7  �]  ��  ��  ��  ��  �  �  �  �  �  �!  �*  �,  �.  �0  �2  �4  �A  �D  �G  �J  �M  �O  �l  �y  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �	  �  �+  �0  �2  �5  �:  �<  �?  �A  �P  �]  �r  �u  �x  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �@  �d  ��  ��  ��  ��  �	  �  �  �  �  �#  �,  �/  �2  �5  �8  �:  �G  �J  �M  �P  �S  �U  �r  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �
  �  �  �  �  �  �.  �1  �3  �5  �7  �:  �<  �>  �@  �C  �E  �G  �d  ��  ��  ��  ��  �  �7  �V  �u  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �	  �  �  �  �  �  �  �)  �+  �8  �=  �?  �B  �G  �I  �L  �N  �]  �j  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �+  �M  �q  ��  ��  ��  ��  �  �  �"  �%  �'  �0  �9  �<  �>  �A  �C  �E  �G  �T  �W  �Z  �]  �`  �b  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �   �#  �&  �;  �>  �@  �B  �D  �G  �J  �M  �O  �R  �T  �V  �s  ��  ��  ��  ��  �"  �F  �e  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �  �  �  �!  �&  �(  �+  �-  �<  �?  �L  �Q  �S  �V  �[  �]  �`  �b  �q  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �@  �b  ��  ��  ��  ��  �  �+  �4  �7  �:  �<  �E  �N  �P  �R  �T  �V  �X  �e  �h  �k  �n  �q  �s  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �"  �%  �(  �+  �.  �1  �4  �7  �L  �O  �Q  �S  �U  �X  �Z  �\  �^  �a  �c  �e  ��  ��  ��  ��  �  �1  �U  �t  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �&  �+  �-  �0  �5  �7  �:  �<  �K  �N  �[  �`  �b  �e  �j  �l  �o  �q  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �*  �N  �p  ��  ��  ��  ��  �  �9  �B  �E  �H  �J  �S  �\  �^  �`  �b  �d  �f  �s  �v  �y  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �&  �)  �,  �/  �2  �5  �8  �;  �>  �A  �D  �Y  �\  �^  �`  �b  �e  �g  �i  �k  �n  �p  �r    ®  ��  ��  �  �>  �b  Á  à  ý  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �$  �1  �6  �9  �;  �@  �C  �E  �G  �V  �Y  �f  �k  �m  �p  �u  �w  �z  �|  ċ  Ę  ĭ  İ  ĳ  Ķ  Ĺ  ļ  Ŀ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �7  �[  �}  š  ��  ��  �
  �)  �F  �O  �R  �U  �W  �`  �i  �l  �o  �r  �u  �w  Ƅ  Ƈ  Ɗ  ƍ  Ɛ  ƒ  Ư  Ƽ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �   �5  �8  �;  �>  �A  �D  �G  �J  �M  �P  �S  �h  �k  �m  �o  �q  �t  �w  �z  �|  �  ǁ  ǃ  Ǡ  ǿ  ��  �  �)  �O  �s  Ȓ  ȱ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �9  �F  �K  �M  �P  �U  �W  �Z  �\  �k  �n  �p  �}  ɂ  Ʉ  ɇ  Ɍ  Ɏ  ɑ  ɓ  ɢ  ɯ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �	  �  �  �  �-  �L  �p  ʒ  ʶ  ��  �   �  �>  �[  �d  �g  �j  �l  �u  �~  ˁ  ˄  ˇ  ˊ  ˌ  ˎ  ˛  ˞  ˡ  ˤ  ˧  ˩  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �-  �:  �O  �R  �U  �X  �[  �^  �a  �d  �g  �j  �m  ̂  ̅  ̇  ̉  ̋  ̎  ̑  ̔  ̖  ̙  ̛  ̝  ̺  ��  ��  �  �C  �i  ͍  ͬ  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �(  �+  �.  �1  �4  �6  �S  �`  �e  �g  �j  �o  �q  �t  �v  ΅  Έ  Ε  Κ  Μ  Ο  Τ  Φ  Ω  Ϋ  κ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �  �!  �$  �&  �(  �E  �d  ψ  Ϫ  ��  ��  �  �7  �V  �s  �|  �  Ђ  Є  Ѝ  Ж  Й  М  П  Т  Ф  б  д  з  к  н  п  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �#  �%  �(  �-  �/  �2  �4  �C  �P  �e  �h  �k  �n  �q  �t  �w  �z  �}  р  у  ј  ћ  ѝ  џ  ѡ  Ѥ  Ѧ  Ѩ  Ѫ  ѭ  ѯ  ѱ  ��  ��  �  �3  �W  �}  ҡ  ��  ��  ��  �  �  �  �  �  �  �"  �%  �(  �+  �-  �/  �<  �?  �B  �E  �H  �J  �g  �t  �y  �{  �~  Ӄ  Ӆ  ӈ  ӊ  ә  Ӝ  ө  Ӯ  Ӱ  ӳ  Ӹ  Ӻ  ӽ  ӿ  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �#  �&  �(  �*  �,  �/  �2  �5  �7  �:  �<  �>  �[  �z  Ԟ  ��  ��  �
  �.  �M  �l  Չ  Ւ  Օ  ՘  ՚  գ  լ  կ  ղ  յ  ո  պ  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �$  �'  �*  �7  �<  �>  �A  �F  �H  �K  �M  �\  �i  �~  ց  ք  և  ֊  ֍  ֐  ֓  ֖  ֙  ֜  ֱ  ִ  ֶ  ָ  ֺ  ֽ  ֿ  ��  ��  ��  ��  ��  ��  �  �*  �L  �p  ז  ׺  ��  ��  �  �  �!  �$  �&  �/  �8  �;  �=  �@  �B  �D  �Q  �T  �W  �Z  �]  �_  �|  ؉  ؎  ؐ  ؓ  ؘ  ؚ  ؝  ؟  خ  ذ  ؽ  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �
  �  �  �  �  �  �  �  �"  �7  �:  �<  �>  �@  �C  �E  �G  �I  �L  �N  �P  �m  ٌ  ٰ  ��  ��  �  �@  �_  �~  ڛ  ڤ  ڧ  ڪ  ڬ  ڵ  ھ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �   �"  �%  �'  �6  �9  �F  �K  �M  �P  �U  �W  �Z  �\  �k  �x  �  ۂ  ۅ  ۈ  ۏ  ے  ە  ۘ  ۚ  ۻ  ��  �  �)  �,  �/  �2  �5  �7  �C  �L  �T  �]  �`  �i  �p  �u  ܂  ܅  ܈  ܋  ܎  ܐ  ܜ  ܥ  ܯ  ܲ  ��  ��  �  �  �  �(  �+  �.  �1  �4  �6  �t  �}  ݆  ݉  ݖ  ݙ  ݜ  ݟ  ݢ  ݤ  ݲ  ݾ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �"  �%  �(  �+  �.  �0  �;  �R  �k  ބ  ޗ  ޮ  ��  ��  ��  ��  �  �  �_  �f  �|  ߃  ߎ  ߛ  ߤ  ߧ  ߪ  ߭  ߰  ߹  ߼  ߿  ��  ��  ��  ��  ��  �  �I  �O  �V  �X  �a  �h  �k  �n  �q  �t  ��  ��  ��  ��  ��  ��  ��  ��  �  �  ��  ��  �&  �(  �5  �<  �?  �B  �E  �L  �O  �R  �U  �W  �^  �g  �o  �~  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �=  �g  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �
  �  �  �  �  �  �%  �:  �<  �>  �@  �B  �D  �F  �H  �J  �L  �N  �P  �]  �l  �o  �r  �u  �x  �{  �~  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  �#  �E  �  �  �  ��  ��  �  �  �"  �%  �(  �+  �.  �1  �4  �7  �:  �=  �P  �R  �U  �X  �[  �^  �a  �d  �f  �i  �k  �~  �  �  �  ��  ��  ��  �  �  �  �*  �-  �0  �3  �6  �9  �<  �I  �L  �O  �R  �U  �X  �[  �]  �  �  �  ��  ��  �	  �  �  �"  �%  �(  �+  �4  �7  �:  �=  �@  �B  �Y  �m  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  �  �  �)  �,  �/   	 2 9 b � � � � � � � � �   # , / 2 5 8 A D G J M O h � � � � � � � � � � 	        # 4 L U Z ] _ b k n o x � � � � � � � �  ' 4 7 : = @ C F S V Y [ ] ` b d  � � � � � �      ! * - 0 2 5 7 O u � � � � � � � � � �   ; H Q T W Z ] f i l o r t � � �  . L Y b e h k n w z } � � � � � � � � � � � � �  � � � � � � � � � �   	 � � � � � � � � �    � � � � � � � � 	     A J W Z ] ` b d � � � � � � � � � � � �   	        ! $ ; > @ C F I L O R U X [ ] � � � � �  + ? b x � � � � � � � � � � �      % 2 5 8 ; > A D Q T W Z ] ` b d o � � � � � � � � � � � � � �    , > G R U X [ ^ a c � � � � �   2 5 8 ; > A D F I b u � � � � � � � � � �     # & ) , . ; F L O R U i r � � � � � � � � � � � � � � �      " $ - C H ^ g r u x � � � �   7 < g } � � � � � � � � � � � � �        	                  9  X  |  �  �  � ! !+ !J !g !p !s !v !x !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� !� " " " " " " "  "# "% "4 "9 "B "Y "^ "u "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� # # # # # #. #; #> #@ #C #M #Z #] #_ #b #i #v #y #| # #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� #� $ $ $ $
 $ $ $% $( $* $, $. $1 $4 $7 $9 $< $> $@ $] $| $� $� $� % %0 %O %n %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� & & &	 & & & & & && &3 &8 &: &= &B &D &G &I &X &e &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� &� ' ' ' ' '& ') ', '/ '4 'A 'D 'F 'I 'V 'Y '\ '_ 'l 'o 'q 't '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� ( ( ( ( (
 ( ( ( ( ( ( (6 (U (y (� (� (� )	 )( )G )d )m )p )s )u )~ )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� * * * * * *$ *& *) *+ *: *[ *^ *a *c *f *i *l *n *q *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� *� +  + + + + +! +# +& +( +D +o +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� , , , , ,* ,- ,/ ,2 ,? ,B ,D ,G ,T ,W ,Y ,\ ,h ,u ,w ,z ,| ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� ,� - -& -J -l -� -� -� -� . .5 .> .A .D .F .O .X .[ .^ .a .d .f .s .v .y .| . .� .� .� .� .� .� .� .� .� .� .� .� .� .� .� .� .� .� .� / / / / / / /8 /; /> /_ /b /e /h /k /n /q /s /v /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� 0 0 0 0 0 0( 09 0< 0? 0B 0E 0H 0K 0N 0Q 0b 0e 0h 0k 0m 0p 0s 0v 0y 0{ 0� 0� 0� 0� 0� 0� 1 1 1 1 1 1! 1> 1G 1J 1M 1O 1l 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 2 2 2- 22 2J 2\ 2u 2x 2{ 2~ 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 2� 3 3 3 3, 3/ 31 33 36 3S 3\ 3z 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 4 4 4 4 4 4 4" 4$ 4' 4) 47 4D 4W 4a 4j 4w 4~ 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 5 5 5 53 5@ 5I 5L 5O 5R 5U 5^ 5` 5b 5d 5f 5h 5� 5� 5� 5� 5� 5� 5� 5� 5� 5� 6 6 6 6  6# 6& 6( 6< 6E 6J 6M 6P 6S 6` 6g 6j 6m 6p 6w 6z 6| 6 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6�            �             6�

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


