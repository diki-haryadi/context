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
//  Copyright ¬© 2020 Dicoding Indonesia. All rights reserved.
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
bplist00‘        
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—  	UStateÄØ∞      9 : ; < = > ? @ A B C D E F G T Z [ a e f j k o p t u y z ~  É Ñ à â ç é ë ù û ü † ° ˇ	!"&'+,0156:;?@DEIJNOSTXY]^bcghlmqrvw{|ÄÅÖÜäãèêîïôöûü£§®©≠Æ≤≥ÀÃÕŒœ–—“”‘’Ÿ‡·‚„ÎÏÌ˜¯˘˙˛	 !"%=>?@ABCDEFGKRZ[\fghimqyz{|ÑÖùûü†°¢£§•¶ß´≤≥¥µΩæø… ÀÃ–‘‹›‡·‚„ÎÏ	
#$%/0126:BCDLMefghijklmnosz{|ÑÖÜéèêöõúù°•≠Æ∆«»… ÀÃÕŒœ–‘€‹‰ÂÊÒÚÛ˜˚()*+,-./0126=EFGQRSTX\defnoáàâäãåçéèêëïúù•¶ßØ∞±ªºΩæ¬∆ŒœÁËÈÍÎÏÌÓÔÒı¸˝˛%&'()12JKLMNOPQRSTX_`hijrst~ÄÅÖâëí™´¨≠ÆØ∞±≤≥¥∏ø«»…”‘’÷⁄ﬁÊÁËÈÍÚÛ !)*+345?@ABFJRSklmnopqrstuyÄàâäîïñóõüß®©±≤ ÀÃÕŒœ–—“”‘ÿﬂ‡·ÈÍÎıˆ˜¯¸ 	
-./01234567;BCDLMNVWXbcdeimnvwèêëíìîïñóòôù§¨≠Æ∏π∫ªø√ÀÃÕ’÷ÓÔÒÚÛÙıˆ˜¯¸ $,-.67OPQRSTUVWXY]demnoyz{|ÄÑåçéñóØ∞±≤≥¥µ∂∑∏πΩƒÃÕŒÿŸ⁄€ﬂ„ÎÏÌıˆ													#	+	,	-	5	6	7	A	B	C	D	H	L	T	U	m	n	o	p	q	r	s	t	u	v	w	{	Ç	É	ã	å	ç	ï	ñ	ó	°	¢	£	§	®	¨	≠	µ	∂	Œ	œ	–	—	“	”	‘	’	÷	◊	ÿ	‹	„	‰	Ï	Ì	Ó	ˆ	˜	¯
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
é
è
ê
ë
í
ì
î
ï
ñ
ó
ò
ú
£
§
¨
≠
Æ
∂
∑
∏
¬
√
ƒ
≈
…
Õ
’
÷
Ó
Ô

Ò
Ú
Û
Ù
ı
ˆ
˜
¯
¸!"#$(,45MNOPQRSTUVW[bcklmwxyz~ÇäãåçïñÆØ∞±≤≥¥µ∂∑∏º√ƒ≈ÕŒœ◊ÿŸ„‰ÂÊÍÓÔ˜¯%&'/01;<=>BFNOPXYqrstuvwxyz{Üáèêëôöõ•¶ß®¨∞±π∫“”‘’÷◊ÿŸ⁄€‹‡ÁËÒÚ˙˚¸	23456789:;<@GHPQRZ[\fghimqyzíìîïñóòôöõú†ß®∞±≤ºΩæø√«œ–—Ÿ⁄ÚÛÙıˆ˜¯˘˙˚¸ &'()-19:RSTUVWXYZ[\`gopqyz{ÖÜáàåêòô±≤≥¥µ∂∑∏π∫ªø∆«œ–—€‹›ﬁ‚ÊÓÔÒ˘˙ '(012<=>?CGOPQRZ[stuvwxyz{|}Åàêëíöõú¶ß®©≠±π∫“”‘’÷◊ÿŸ⁄€‹‡ÁËÒÚ˙˚¸	23456789:;<@GOPQ[\]^bfnopxyëíìîïñóòôöõü¶ÆØ∞∫ªºΩ¡≈ÕŒ÷◊ÔÒÚÛÙıˆ˜¯˘˝"#$%)-56NOPQRSTUVWX\cklmuvwÅÇÉÑàåîï≠ÆØ∞±≤≥¥µ∂∑ª¬ ÀÃ‘’÷‡·‚„ÁÎÛÙ!"#+,-789:>BJKLTUmnopqrstuvw{Çäãåîïñ†°¢£ß´≥¥∫æøÀÃÕŒœ–—’›ﬁﬂÁËÈÍÎÙ¸ "&*/56:@CFL`abcdefghlmquvwx|ÇÉáàäãèïñóôú†¶™≠∑∏π∫¿¡≈»Œœ”◊›ﬁ·˘˙˚¸˝˛ˇ 	
 &01234>?BRSTUVYZnopqrstuvwçéèêëíìîïñ¶ß®©™´¨∏π∫ªº¿¡«»ÃÕ“⁄€‹ÊÁËÈÍÚ˜˙˛ˇ !%&'345678DEFGHNOPQWX\]cdhijrstwx~áàâ£§•¶ß®©™´¨≠Æ±≤≥∑ªÀÃÕŒœ–—“⁄€‹‰Ù 
#$()-.237;<AEFG_`abcdefghimtu}~áàíìîïôù•¶ß´¥ª¡≈∆ Œœ”‘’ÈÍÎÏÌÓÔÒıˆ˙˚¸ !*./348<@DEIJbcdefghijklpwÄÅâäîïñóõüß®©≤π¬∆«ÀÃ–‘ÿ‹›·‚˙˚¸˝˛ˇ !",-./37?@AJQX\]abfjkopàâäãåçéèêëíñù•¶ß±≤≥¥∏ºƒ≈ÕŒœ”◊‡ÁÙ¯¸ 	%&'()*+,-./3:BCDLMWXYZ^bjklpqxÅÖâçëïôöû§•™¥µ∂∑∏π√ƒ≈∆“”‘’÷‚„‰ÂÊ˙˚¸˝˛ˇ 	$%&U$null”      WNS.keysZNS.objectsV$class¢  ÄÄ¢  ÄÅ Äì_IDEWorkspaceDocument_$9229D32F-C4FF-43F2-A95F-816FAA71C582”     ) 8Æ        ! " # $ % & ' (ÄÄÄÄÄ	Ä
ÄÄÄÄÄÄÄÄÆ * + , - . / 0 1 2 3 - 5 6 0ÄÄ*ÅΩÅ¿ÄÖÅ¬ÄÜÅ√Å∆Å’Å¿ÅÁÅËÄÜÄù_RecentEditorDocumentURLs_DefaultEditorStatesForURLs\ActiveScheme_ActiveProjectSetIdentifierKey_$RunDestinationArchitectureVisibility_DocumentWindows_EnableThreadGallery_WindowArrangementDebugInfo_RunContextRecents_ActiveRunDestination_ActiveProjectSetNameKey_SelectedWindows_0LastCompletedPersistentSchemeBasedActivityReport_BreakpointsActivated“   H S™ I J K L M N O P Q RÄÄÄÄÄÄÄ!Ä#Ä%Ä'Ä)” U  V W X YWNS.base[NS.relativeÄ ÄÄ_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift“ \ ] ^ _Z$classnameX$classesUNSURL¢ ^ `XNSObject” U  V W X dÄ ÄÄ_Zfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Assets.xcassets” U  V W X iÄ ÄÄ_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/ContentView.swift” U  V W X nÄ ÄÄ_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift” U  V W X sÄ ÄÄ_tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift” U  V W X xÄ ÄÄ _vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteGameDataSource.swift” U  V W X }Ä ÄÄ"_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swift” U  V W X ÇÄ ÄÄ$_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swift” U  V W X áÄ ÄÄ&_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swift” U  V W X åÄ ÄÄ(_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/GameMapper.swift“ \ ] è êWNSArray¢ è `”    í ó 8§ ì î ï ñÄ+Ä,Ä-Ä.§ ò ô ö õÄ/Å{ÅêÅ§Äù_-Xcode.IDEKit.EditorDocument.PegasusSourceCode_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project_'Xcode.IDEKit.EditorDocument.LogDocument_(Xcode.IDEKit.EditorDocument.AssetCatalog”    ¢ – 8Ø- £ § • ¶ ß N © ™ Q ¨ ≠ Æ Ø K ± ≤ P ¥ L ∂ O ∏ π R ª º M æ ø ¿ ¡ ¬ √ ƒ ≈ I « » …   À Ã Õ Œ œÄ0Ä2Ä4Ä6Ä8ÄÄ:Ä<Ä%Ä>Ä@ÄBÄDÄÄFÄHÄ#ÄJÄÄLÄ!ÄNÄPÄ'ÄRÄTÄÄVÄXÄZÄ\Ä^Ä`ÄbÄdÄÄfÄhÄjÄlÄnÄpÄrÄtÄvØ- — “ ” ‘ ’ ÷ ◊ ÿ Ÿ ⁄ € ‹ › ﬁ ﬂ ‡ · ‚ „ ‰ Â Ê Á Ë È Í Î Ï Ì Ó Ô  Ò Ú Û Ù ı ˆ ˜ ¯ ˘ ˙ ˚ ¸ ˝ÄxÄûÄ∫Ä€Ä¯ÅÅ3ÅNÅjÅâÅ•Å¬ÅﬁÅ˘ÅÅ6ÅQÅnÅäÅ•Å¿Å›Å˘ÅÅ1ÅLÅiÅáÅ§Å¡Å›Å˘ÅÅ1ÅLÅiÅÜÅ°ÅΩÅÿÅÚÅÅ(ÅCÅ`Äù” U  V W XÄ ÄÄ1_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift” U  V W XÄ ÄÄ3_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameDetailModel.swift” U  V W XÄ ÄÄ5_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleGameDataSource.swift” U  V W XÄ ÄÄ7_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swift” U  V W XÄ ÄÄ9_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GameDetailResponse.swift” U  V W XÄ ÄÄ;_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swift” U  V W X Ä ÄÄ=_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift” U  V W X%Ä ÄÄ?_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift” U  V W X*Ä ÄÄA_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swift” U  V W X/Ä ÄÄC_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swift” U  V W X4Ä ÄÄE_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift” U  V W X9Ä ÄÄG_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swift” U  V W X>Ä ÄÄI_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swift” U  V W XCÄ ÄÄK_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swift” U  V W XHÄ ÄÄM_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift” U  V W XMÄ ÄÄO_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel.swift” U  V W XRÄ ÄÄQ_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/DI/Injection.swift” U  V W XWÄ ÄÄS_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swift” U  V W X\Ä ÄÄU_|file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift” U  V W XaÄ ÄÄW_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity.swift” U  V W XfÄ ÄÄY_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GamesResponse.swift” U  V W XkÄ ÄÄ[_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/GameRespository.swift” U  V W XpÄ ÄÄ]_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swift” U  V W XuÄ ÄÄ__pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swift” U  V W XzÄ ÄÄa_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swift” U  V W XÄ ÄÄc_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swift” U  V W XÑÄ ÄÄe_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swift” U  V W XâÄ ÄÄg_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel%202.swift” U  V W XéÄ ÄÄi_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swift” U  V W XìÄ ÄÄk_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift” U  V W XòÄ ÄÄm_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/Untitled.swift” U  V W XùÄ ÄÄo_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift” U  V W X¢Ä ÄÄq_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift” U  V W XßÄ ÄÄs_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift” U  V W X¨Ä ÄÄu_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity%202.swift” U  V W X±Ä ÄÄw_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift”   ¥ø 8™µ∂∑∏π∫ªºΩæÄyÄzÄ{Ä|Ä}Ä~ÄÄÄÄÅÄÇ™¿ 0¬¬ƒ≈ .¬» 0ÄÉÄÜÄáÄáÄàÄôÄÖÄáÄöÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ÷ S°◊ÄÑÄ)“  ⁄ S§ . . . .ÄÖÄÖÄÖÄÖÄ) 	”   ‰Á ¢ÂÊÄâÄä¢ËÈÄãÄîÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ÓÚ £ÔÒÄåÄçÄé£ÛÙıÄèÄëÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“˚ ¸˝\NS.uuidbytesO!vÌ€<I∫ñÒx›ﬁ4UÄê“ \ ]ˇ VNSUUID¢ˇ `“ ˝\NS.uuidbytesO•§8âùŒIæù‡cº:÷4Äê#@d†     “ \ ]\NSDictionary¢ `”   
 ¢ÄïÄñ¢ÄóÄòÄì[lineIndexes^documentLength“   S†Ä)U#        ”    ¢ÄõÄú¢ÄóÄòÄìUfolds^documentLength“ \ ]#$_NSMutableDictionary£# `”   &1 8™'()*+,-./0ÄüÄ†Ä°Ä¢Ä£Ä§Ä•Ä¶ÄßÄ®™2 0¬¬67 .¬: 0Ä©ÄÜÄáÄáÄ´Ä∑ÄÖÄáÄ∏ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  H S°IÄ™Ä)“  L S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   SV ¢TUÄ¨Ä≠¢WXÄÆÄ¥Äì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ]a £^_`ÄØÄ∞Ä±£bcıÄ≤Ä≥ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“j k˝\NS.uuidbytesOùV¥	MçD˛ûw ˜)^Z9Äê“n o˝\NS.uuidbytesO·AΩ^$lC¥ÄåI„|a›Äê”   ru ¢sÄµÄï¢vÄ∂ÄóÄì^documentLengthÚ#ø      ”   }Ä ¢ÄõÄπ¢vÄóÄ∂Äì^documentLength”   Üë 8™áàâäãåçéèêÄªÄºÄΩÄæÄøÄ¿Ä¡Ä¬Ä√Äƒ™í 0¬¬ñóò¬ö 0Ä≈ÄÜÄáÄáÄ Ä◊ÄÿÄáÄŸÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ® S°©Ä∆Ä)“  ¨ S§≠ÆØ .Ä«Ä»Ä…ÄÖÄ)eq”   ∂π ¢∑∏ÄÀÄÃ¢∫ªÄÕÄ”Äì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ¿ƒ £¡¬√ÄŒÄœÄ–£≈ı«Ä—ÄíÄ“Äì_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore“Õ Œ˝\NS.uuidbytesO6ñ\◊∏zL’¨∫l'^#∑ıÄê“— “˝\NS.uuidbytesO¸g™∂m¢Eæ§*-™∏,°Äê”   ’ÿ ¢◊ÄïÄ‘¢Ÿ⁄Ä’Ä÷Äì^documentLength“  ﬁ S†Ä)ï#@      F”   ‰Á ¢ÊÄõÄ⁄¢Ÿ⁄Ä’Ä÷Äì^documentLength”   Ì¯ 8™ÓÔÒÚÛÙıˆ˜Ä‹Ä›ÄﬁÄﬂÄ‡Ä·Ä‚Ä„Ä‰ÄÂ™˘ 0¬¬˝≈ .¬ 0ÄÊÄÜÄáÄáÄÍÄôÄÖÄáÄˆÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÄÁÄ)“   S§ÄËÄÈÄËÄÈÄ)K”    ¢ÄÎÄÏ¢ !ÄÌÄÛÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   &* £'()ÄÓÄÔÄ£+ı-ÄÒÄíÄÚÄì_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore“3 4˝\NS.uuidbytesOªŒC7À¿E˛Öü;é ?ï§Äê“7 8˝\NS.uuidbytesOPÊˇ´–GO¿ÜLËt0ÊvÄê”   ;> ¢=ÄïÄÙ¢Ÿ@Ä’ÄıÄì^documentLengthF”   EH ¢GÄõÄ˜¢Ÿ@Ä’ÄıÄì^documentLength”   NY 8™OPQRSTUVWXÄ˘Ä˙Ä˚Ä¸Ä˝Ä˛ÄˇÅ ÅÅ™Z 0¬¬^7 .¬b 0ÅÄÜÄáÄáÅÄ∑ÄÖÄáÅÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  p S°qÅÄ)“  t S§uvuvÅÅÅÅÄ)f”   }Ä ¢~ÅÅ	¢ÅÇÅ
ÅÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   áä ¢âÄïÅ¢ŸåÄ’ÅÄì^documentLength	_”   ëï £íìîÅÅÅ£ñıòÅÄíÅÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“û ü˝\NS.uuidbytesOo›ñoÕ"Jn£»ÁTÒ∫!Äê“¢ £˝\NS.uuidbytesOrubYÆbJsß®‹évFÒáÄê”   ¶© ¢®ÄõÅ¢ŸåÄ’ÅÄì^documentLength”   Ø∫ 8™∞±≤≥¥µ∂∑∏πÅÅÅÅÅÅÅÅÅÅ™ª 0¬¬ø¿¡¬√ 0Å ÄÜÄáÄáÅ#Å/Å0ÄáÅ1ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  — S°“Å!Ä)“  ’ S§÷v÷vÅ"ÅÅ"ÅÄ)L”   ›‡ ¢ﬁﬂÅ$Å%¢·‚Å&Å,Äì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ÁÎ £ËÈÍÅ'Å(Å)£ıÌÓÄíÅ*Å+Äì_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“Ù ı˝\NS.uuidbytesOó◊,”	˛M=©í3&^PÏÄê“¯ ˘˝\NS.uuidbytesOjmºã6@√õˆxDﬁÄê”   ¸ˇ ¢˛ÄïÅ-¢ŸÄ’Å.Äì^documentLength	f#@1      ”    ¢
ÄõÅ2¢ŸÄ’Å.Äì^documentLength”    8™Å4Å5Å6Å7Å8Å9Å:Å;Å<Å=™ 0¬¬!7 .¬% 0Å>ÄÜÄáÄáÅ@Ä∑ÄÖÄáÅLÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  3 S°4Å?Ä)“  7 S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   >A ¢?@ÅAÅB¢BCÅCÅIÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   HL £IJKÅDÅEÅF£MNıÅGÅHÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“U V˝\NS.uuidbytesONx\µöAN±öí`Ÿ<J	ÕÄê“Y Z˝\NS.uuidbytesO◊òä∞„⁄CPØcZV·Äê”   ]` ¢^ÅJÄï¢aÅKÄóÄì^documentLength¯”   gj ¢iÄõÅM¢aÄóÅKÄì^documentLength”   p{ 8™qrstuvwxyzÅOÅPÅQÅRÅSÅTÅUÅVÅWÅX™| 0¬¬Ä≈ .¬Ñ 0ÅYÄÜÄáÄáÅ\ÄôÄÖÄáÅhÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  í S°ìÅZÄ)“  ñ S§¡ò¡òÅ0Å[Å0Å[Ä)”   û° ¢ü†Å]Å^¢¢£Å_ÅbÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ®´ ¢©Å`Äï¢¨ŸÅaÄ’Äì^documentLength≥”   ≤∂ £≥¥µÅcÅdÅe£∑∏ıÅfÅgÄíÄì_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width“ø ¿˝\NS.uuidbytesOŒLû(§9DF≤ª»—èºÌ˚Äê“√ ƒ˝\NS.uuidbytesOU∑˜øO∫MÚù˙∫ú„n'¸Äê”   «  ¢…ÄõÅi¢Ÿ¨Ä’ÅaÄì^documentLength”   –€ 8™—“”‘’÷◊ÿŸ⁄ÅkÅlÅmÅnÅoÅpÅqÅrÅsÅt™‹ 0¬¬‡·‚¬‰ 0ÅuÄÜÄáÄáÅyÅÖÅÜÄáÅáÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ú S°ÛÅvÄ)“  ˆ S§˜¯˜¯ÅwÅxÅwÅxÄ)G”   ˇ ¢ ÅzÅ{¢Å|ÅÇÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   	 £
Å}Å~Å£ıÅÄÄíÅÅÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“ ˝\NS.uuidbytesOÕ>•KhXL“Øl’‡ëıüΩÄê“ ˝\NS.uuidbytesO/â»08@ÅcÓ¯®–éÄê”   ! ¢ ÄïÅÉ¢Ÿ#Ä’ÅÑÄì^documentLength##?      ”   *- ¢,ÄõÅà¢Ÿ#Ä’ÅÑÄì^documentLength”   3> 8™456789:;<=ÅäÅãÅåÅçÅéÅèÅêÅëÅíÅì™? 0¬¬C7 .¬G 0ÅîÄÜÄáÄáÅóÄ∑ÄÖÄáÅ£ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  U S°VÅïÄ)“  Y S§ZvZvÅñÅÅñÅÄ)”   ad ¢bcÅòÅô¢efÅöÅùÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   kn ¢lÅõÄï¢oŸÅúÄ’Äì^documentLength””   uy £vwxÅûÅüÅ†£z{ıÅ°Å¢ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ç É˝\NS.uuidbytesO£ˆ<Û"Kj™é`P[:†Äê“Ü á˝\NS.uuidbytesO¸Î◊oëÕK)à∑ËŸ#p˘KÄê”   äç ¢åÄõÅ§¢ŸoÄ’ÅúÄì^documentLength”   ìû 8™îïñóòôöõúùÅ¶ÅßÅ®Å©Å™Å´Å¨Å≠ÅÆÅØ™ü 0¬¬£§•¬ß 0Å∞ÄÜÄáÄáÅ≤ÅæÅøÄáÅ¿ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  µ S°∂Å±Ä)“  π S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   ¿√ ¢¡¬Å≥Å¥¢ƒ≈ÅµÅªÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”    Œ £ÀÃÕÅ∂Å∑Å∏£œ–ıÅπÅ∫ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“◊ ÿ˝\NS.uuidbytesOﬂIü0>ÈK‹≠(õdπskÄê“€ ‹˝\NS.uuidbytesO‹û~∑BOõIT“\WèÇÄê”   ﬂ‚ ¢‡ÅºÄï¢„ÅΩÄóÄì^documentLengthÄ#@,      î”   ÎÓ ¢ÌÄõÅ¡¢„ÄóÅΩÄì^documentLength”   Ùˇ 8™ıˆ˜¯˘˙˚¸˝˛Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀÅÃ™  0¬¬7 .¬ 0ÅÕÄÜÄáÄáÅ–Ä∑ÄÖÄáÅ‹ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅŒÄ)“   S§vvÅœÅÅœÅÄ)”   "% ¢#$Å—Å“¢&'Å”Å÷Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ,/ ¢.ÄïÅ‘¢Ÿ1Ä’Å’Äì^documentLengthf”   6: £789Å◊ÅÿÅŸ£ı<=ÄíÅ⁄Å€Äì_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore“C D˝\NS.uuidbytesON‡«4÷jD◊£ƒ“C{b°Äê“G H˝\NS.uuidbytesO+ïÀôl√@›ø*=_i0GDÄê”   KN ¢MÄõÅ›¢Ÿ1Ä’Å’Äì^documentLength”   T_ 8™UVWXYZ[\]^ÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁÅË™` 0¬¬d7 .¬h 0ÅÈÄÜÄáÄáÅÎÄ∑ÄÖÄáÅ˜ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  v S°wÅÍÄ)“  z S§vvÅœÅÅœÅÄ)”   ÅÑ ¢ÇÉÅÏÅÌ¢ÖÜÅÓÅÙÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ãè £åçéÅÔÅÅÒ£ıëíÄíÅÚÅÛÄì_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore“ò ô˝\NS.uuidbytesO∑#ﬁÊIö∑YÌ@˚˚zÄê“ú ù˝\NS.uuidbytesO	üï&ÏL¨ÅdW¶"·w	Äê”   †£ ¢¢ÄïÅı¢Ÿ•Ä’ÅˆÄì^documentLengthj”   ™≠ ¢¨ÄõÅ¯¢Ÿ•Ä’ÅˆÄì^documentLength”   ≥æ 8™¥µ∂∑∏π∫ªºΩÅ˙Å˚Å¸Å˝Å˛ÅˇÅ ÅÅÅ™ø 0¬¬√ƒ≈¬« 0ÅÄÜÄáÄáÅÅÅÄáÅÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ’ S°÷ÅÄ)“  Ÿ S§⁄€⁄€ÅÅÅÅÄ)”   ‚Â ¢„‰Å	Å
¢ÊÁÅÅÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   Ï £ÌÓÔÅÅÅ£ıÚÛÄíÅÅÄì_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“˘ ˙˝\NS.uuidbytesOÎ._˛gII©∏Üƒk1MÄê“˝ ˛˝\NS.uuidbytesOyiW˘Á AJâı#NÒñmÄê”    ¢ÄïÅ¢ŸÄ’ÅÄì^documentLength±#@*      ”    ¢ÄõÅ¢ŸÄ’ÅÄì^documentLength”   ! 8™ ÅÅÅÅÅÅÅÅ Å!Å"™" 0¬¬&ó(¬* 0Å#ÄÜÄáÄáÅ'Ä◊Å3ÄáÅ4ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  8 S°9Å$Ä)“  < S§=>=>Å%Å&Å%Å&Ä)°+”   EH ¢FGÅ(Å)¢IJÅ*Å-Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   OR ¢PÅ+Äï¢SŸÅ,Ä’Äì^documentLength3Ù”   Y] £Z[\Å.Å/Å0£^_ıÅ1Å2ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“f g˝\NS.uuidbytesO/πµ!]GJ≥¯AS`6]Äê“j k˝\NS.uuidbytesO~’úS◊J$¶º+My{SVÄêÜ”   or ¢qÄõÅ5¢ŸSÄ’Å,Äì^documentLength”   xÉ 8™yz{|}~ÄÅÇÅ7Å8Å9Å:Å;Å<Å=Å>Å?Å@™Ñ 0¬¬à7 .¬å 0ÅAÄÜÄáÄáÅCÄ∑ÄÖÄáÅOÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ö S°õÅBÄ)“  û S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   •® ¢¶ßÅDÅE¢©™ÅFÅLÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   Ø≥ £∞±≤ÅGÅHÅI£¥µıÅJÅKÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“º Ω˝\NS.uuidbytesOÕ⁄{-ñáAådÄ;‘V&Äê“¿ ¡˝\NS.uuidbytesOß^ÃˆD∆∑yË‡8˜'Äê”   ƒ« ¢∆ÄïÅM¢…ÄóÅNÄì^documentLength„”   Œ— ¢–ÄõÅP¢…ÄóÅNÄì^documentLength”   ◊‚ 8™ÿŸ⁄€‹›ﬁﬂ‡·ÅRÅSÅTÅUÅVÅWÅXÅYÅZÅ[™„ 0¬¬Á§≈¬Î 0Å\ÄÜÄáÄáÅ`ÅæÅÄáÅlÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ˘ S°˙Å]Ä)“  ˝ S§˛ˇ˛ˇÅ^Å_Å^Å_Ä)!”   	 ¢ÅaÅb¢
ÅcÅiÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”    £ÅdÅeÅf£ıÅgÅhÄíÄì_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width“ ˝\NS.uuidbytesOù£ézóÒKO™¸ÊXé,1Äê“! "˝\NS.uuidbytesOB‹kÑEÓèÎ/h‰ÿ‹åÄê”   %( ¢'ÄïÅj¢Ÿ*Ä’ÅkÄì^documentLengthç”   /2 ¢1ÄõÅm¢Ÿ*Ä’ÅkÄì^documentLength”   8C 8™9:;<=>?@ABÅoÅpÅqÅrÅsÅtÅuÅvÅwÅx™D 0¬¬H≈ .¬L 0ÅyÄÜÄáÄáÅ|ÄôÄÖÄáÅàÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Z S°[ÅzÄ)“  ^ S§_v_vÅ{ÅÅ{ÅÄ)©”   fi ¢ghÅ}Å~¢jkÅÅÖÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   pt £qrsÅÄÅÅÅÇ£uvıÅÉÅÑÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“} ~˝\NS.uuidbytesOÚI_¿
nKÒéXüÑMp?“Äê“Å Ç˝\NS.uuidbytesOB+ŒÃõDcìcpxù)â˘Äê”   Öà ¢áÄïÅÜ¢ŸäÄ’ÅáÄì^documentLength”   èí ¢ëÄõÅâ¢ŸäÄ’ÅáÄì^documentLength”   ò£ 8™ôöõúùûü†°¢ÅãÅåÅçÅéÅèÅêÅëÅíÅìÅî™§ 0¬¬®7 .¬¨ 0ÅïÄÜÄáÄáÅóÄ∑ÄÖÄáÅ£ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∫ S°ªÅñÄ)“  æ S§⁄Æ⁄ÆÅÄ»ÅÄ»Ä)”   ≈» ¢∆«ÅòÅô¢… ÅöÅ†Äì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   œ” £–—“ÅõÅúÅù£‘’ıÅûÅüÄíÄì_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width“‹ ›˝\NS.uuidbytesO@jH´·3IM¨"†T€°pzÄê“‡ ·˝\NS.uuidbytesOpSzÖn Bl†¬C)TÕ*Äê”   ‰Á ¢ÂÅ°Äï¢ËŸÅ¢Ä’Äì^documentLengthb”   ÓÒ ¢ÄõÅ§¢ŸËÄ’Å¢Äì^documentLength”   ˜	 8™¯˘˙˚¸˝˛ˇ	 	Å¶ÅßÅ®Å©Å™Å´Å¨Å≠ÅÆÅØ™	 0¬¬	7 .¬	 0Å∞ÄÜÄáÄáÅ≤Ä∑ÄÖÄáÅæÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	 S°	Å±Ä)“  	 S§ZvZvÅñÅÅñÅÄ)”   	$	' ¢	%	&Å≥Å¥¢	(	)ÅµÅ∏Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   	.	1 ¢	0ÄïÅ∂¢Ÿ	3Ä’Å∑Äì^documentLength˚”   	8	< £	9	:	;ÅπÅ∫Åª£	=ı	?ÅºÄíÅΩÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“	E 	F˝\NS.uuidbytesOáip˜º˚NÆÖ˛’LM_Äê“	I 	J˝\NS.uuidbytesO.,qFœJ^âºÂH
˘ÿÄê”   	M	P ¢	OÄõÅø¢Ÿ	3Ä’Å∑Äì^documentLength”   	V	a 8™	W	X	Y	Z	[	\	]	^	_	`Å¡Å¬Å√ÅƒÅ≈Å∆Å«Å»Å…Å ™	b 0¬¬	f¿	h¬	j 0ÅÀÄÜÄáÄáÅŒÅ/Å⁄ÄáÅ€ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	x S°	yÅÃÄ)“  	| S§	} .	} .ÅÕÄÖÅÕÄÖÄ) ”   	Ñ	á ¢	Ö	ÜÅœÅ–¢	à	âÅ—Å‘Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   	é	ë ¢	êÄïÅ“¢Ÿ	ìÄ’Å”Äì^documentLength‰”   	ò	ú £	ô	ö	õÅ’Å÷Å◊£ı	û	üÄíÅÿÅŸÄì_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“	• 	¶˝\NS.uuidbytesOü~ûæ B©çú°/UAÄê“	© 	™˝\NS.uuidbytesOúˇ¢NùN£Æ$]„ÁjÄê	”   	Æ	± ¢	∞ÄõÅ‹¢Ÿ	ìÄ’Å”Äì^documentLength”   	∑	¬ 8™	∏	π	∫	ª	º	Ω	æ	ø	¿	¡ÅﬁÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁ™	√ 0¬¬	«7 .¬	À 0ÅËÄÜÄáÄáÅÎÄ∑ÄÖÄáÅ˜ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	Ÿ S°	⁄ÅÈÄ)“  	› S§	ﬁv	ﬁvÅÍÅÅÍÅÄ),”   	Â	Ë ¢	Ê	ÁÅÏÅÌ¢	È	ÍÅÓÅÒÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   	Ô	Ú ¢	ÅÔÄï¢	ÛŸÅÄ’Äì^documentLengthà”   	˘	˝ £	˙	˚	¸ÅÚÅÛÅÙ£	˛ı
 ÅıÄíÅˆÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“
 
˝\NS.uuidbytesOÚ"÷ØπO∞-J<Ê∆I¸Äê“

 
˝\NS.uuidbytesOpYX÷K7Bõ—ÚÆçÙ4Äê”   

 ¢
ÄõÅ¯¢Ÿ	ÛÄ’ÅÄì^documentLength”   

" 8™








 
!Å˙Å˚Å¸Å˝Å˛ÅˇÅ ÅÅÅ™
# 0¬¬
'≈ .¬
+ 0ÅÄÜÄáÄáÅÄôÄÖÄáÅÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
9 S°
:ÅÄ)“  
= S§
>v
>vÅÅÅÅÄ))”   
E
H ¢
F
GÅÅ	¢
I
JÅ
ÅÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   
O
R ¢
QÄïÅ¢Ÿ
TÄ’ÅÄì^documentLength@”   
Y
] £
Z
[
\ÅÅÅ£
^ı
`ÅÄíÅÄì_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore“
f 
g˝\NS.uuidbytesOˇ'#≥>6K{π∞*⁄ˆÚ#Äê“
j 
k˝\NS.uuidbytesO®Lv€ÊuJ<≠ôP∑VæÒÄê”   
n
q ¢
pÄõÅ¢Ÿ
TÄ’ÅÄì^documentLength”   
w
Ç 8™
x
y
z
{
|
}
~

Ä
ÅÅÅÅÅÅÅÅÅÅÅ™
É 0¬¬
á7 .¬
ã 0Å ÄÜÄáÄáÅ#Ä∑ÄÖÄáÅ/ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
ô S°
öÅ!Ä)“  
ù S§
û≈
û≈Å"ÅÅ"ÅÄ)”   
•
® ¢
¶
ßÅ$Å%¢
©
™Å&Å)Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   
Ø
≤ ¢
∞Å'Äï¢
≥ŸÅ(Ä’Äì^documentLength/”   
π
Ω £
∫
ª
ºÅ*Å+Å,£
æı
¿Å-ÄíÅ.Äì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“
∆ 
«˝\NS.uuidbytesOÄêÍ‡I›ød ™IŸÄê“
  
À˝\NS.uuidbytesO	ñ^K C™£ãõ#
á¸¶Äê”   
Œ
— ¢
–ÄõÅ0¢Ÿ
≥Ä’Å(Äì^documentLength”   
◊
‚ 8™
ÿ
Ÿ
⁄
€
‹
›
ﬁ
ﬂ
‡
·Å2Å3Å4Å5Å6Å7Å8Å9Å:Å;™
„ 0¬¬
Á7 .¬
Î 0Å<ÄÜÄáÄáÅ>Ä∑ÄÖÄáÅJÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
˘ S°
˙Å=Ä)“  
˝ S§ . . . .ÄÖÄÖÄÖÄÖÄ)”    ¢Å?Å@¢	ÅAÅDÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢ÅBÄï¢ŸÅCÄ’Äì^documentLengthm”    £ÅEÅFÅG£ıÅHÄíÅIÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“% &˝\NS.uuidbytesO•10Iµ MØæÿGí¨ªÄê“) *˝\NS.uuidbytesOcCåCN⁄ú÷'àÔîèÄê”   -0 ¢/ÄõÅK¢ŸÄ’ÅCÄì^documentLength”   6A 8™789:;<=>?@ÅMÅNÅOÅPÅQÅRÅSÅTÅUÅV™B 0¬¬FG⁄¬J 0ÅWÄÜÄáÄáÅZÅfÅÄáÅgÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  X S°YÅXÄ)“  \ S§]v]vÅYÅÅYÅÄ)2”   dg ¢efÅ[Å\¢hiÅ]ÅcÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   nr £opqÅ^Å_Å`£sıuÅaÄíÅbÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“{ |˝\NS.uuidbytesOjˆ¶MM«Æ˘CË@\WÄê“ Ä˝\NS.uuidbytesOahîÕbB˘Æ  X∑:∞cÄê”   ÉÜ ¢ÑÅdÄï¢áŸÅeÄ’Äì^documentLength	#@      ”   éë ¢êÄõÅh¢ŸáÄ’ÅeÄì^documentLength”   ó¢ 8™òôöõúùûü†°ÅjÅkÅlÅmÅnÅoÅpÅqÅrÅs™£ 0¬¬ß®€¬´ 0ÅtÄÜÄáÄáÅxÅÑÅÄáÅÖÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  π S°∫ÅuÄ)“  Ω S§æøæøÅvÅwÅvÅwÄ)0;”   ∆… ¢«»ÅyÅz¢ ÀÅ{Å~Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   –” ¢“ÄïÅ|¢Ÿ’Ä’Å}Äì^documentLength—”   ⁄ﬁ £€‹›ÅÅÄÅÅ£ﬂ‡ıÅÇÅÉÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Á Ë˝\NS.uuidbytesOÉmFl¶M,è˙˚¢öõd≈Äê“Î Ï˝\NS.uuidbytesOÃM∫8ÅﬂB≤Øô0˘êMÄê#@      ”   Û ¢ÚÄõÅÜ¢Ÿ’Ä’Å}Äì^documentLength”   ˘ 8™˙˚¸˝˛ˇ ÅàÅâÅäÅãÅåÅçÅéÅèÅêÅë™ 0¬¬	7 .¬ 0ÅíÄÜÄáÄáÅñÄ∑ÄÖÄáÅ¢ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅìÄ)“   S§ ! !ÅîÅïÅîÅïÄ)&”   (+ ¢)*ÅóÅò¢,-ÅôÅüÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   26 £345ÅöÅõÅú£78ıÅùÅûÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“? @˝\NS.uuidbytesO[W≤(P°H¥Åìjı,÷¥Äê“C D˝\NS.uuidbytesOQH_@Cæ˚góàd◊Äê”   GJ ¢IÄïÅ†¢ŸLÄ’Å°Äì^documentLength”   QT ¢SÄõÅ£¢ŸLÄ’Å°Äì^documentLength”   Ze 8™[\]^_`abcdÅ•Å¶ÅßÅ®Å©Å™Å´Å¨Å≠ÅÆ™f 0¬¬jk¬n 0ÅØÄÜÄáÄáÅ≤ÅæÄËÄáÅøÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  | S°}Å∞Ä)“  Ä S§ÅvÅvÅ±ÅÅ±ÅÄ)@”   àã ¢âäÅ≥Å¥¢åçÅµÅ∏Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   íï ¢îÄïÅ∂¢óÄóÅ∑Äì^documentLength/”   ú† £ùûüÅπÅ∫Åª£°¢ıÅºÅΩÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“© ™˝\NS.uuidbytesO∫ûä˛øBG∑a¥} uéÄê“≠ Æ˝\NS.uuidbytesOK˝¡8àHW©√Ù⁄tF˘±Äê#@       ”   ≤µ ¢¥ÄõÅ¿¢óÄóÅ∑Äì^documentLength”   ª∆ 8™ºΩæø¿¡¬√ƒ≈Å¬Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀ™« 0¬¬À7 .¬œ 0ÅÃÄÜÄáÄáÅœÄ∑ÄÖÄáÅ€ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  › S°ﬁÅÕÄ)“  · S§‚v‚vÅŒÅÅŒÅÄ)~”   ÈÏ ¢ÍÎÅ–Å—¢ÌÓÅ“Å’Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   Ûˆ ¢ıÄïÅ”¢Ÿ¯Ä’Å‘Äì^documentLengthÍ”   ˝ £˛ˇ Å÷Å◊Åÿ£ıÅŸÄíÅ⁄Äì_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore“
 ˝\NS.uuidbytesO5Ì|ÄH∑ÇÔ‹JœÜEÄê“ ˝\NS.uuidbytesO†∑4⁄e“E¶ácOd/Äê”    ¢ÄõÅ‹¢Ÿ¯Ä’Å‘Äì^documentLength”   & 8™ !"#$%ÅﬁÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁ™' 0¬¬+ó€¬/ 0ÅËÄÜÄáÄáÅÎÄ◊ÅÄáÅ˜ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  = S°>ÅÈÄ)“  A S§BvBvÅÍÅÅÍÅÄ)<”   IL ¢JKÅÏÅÌ¢MNÅÓÅÒÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   SV ¢UÄïÅÔ¢ŸXÄ’ÅÄì^documentLength@”   ]a £^_`ÅÚÅÛÅÙ£bıdÅıÄíÅˆÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“j k˝\NS.uuidbytesOƒÄKî"EJëΩQ∫πr8Äê“n o˝\NS.uuidbytesO˝¯∑:˙∆@ÓØÃº“oÖJSÄê”   ru ¢tÄõÅ¯¢ŸXÄ’ÅÄì^documentLength”   {Ü 8™|}~ÄÅÇÉÑÖÅ˙Å˚Å¸Å˝Å˛ÅˇÅ ÅÅÅ™á 0¬¬ã≈ .¬è 0ÅÄÜÄáÄáÅÄôÄÖÄáÅÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ù S°ûÅÄ)“  ° S§¢v¢vÅÅÅÅÄ)”   ©¨ ¢™´ÅÅ	¢≠ÆÅ
ÅÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ≥∑ £¥µ∂ÅÅÅ£∏ı∫ÅÄíÅÄì_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“¿ ¡˝\NS.uuidbytesO¡|ÌD≤˘Amòß´Ày|úÄê“ƒ ≈˝\NS.uuidbytesO§ƒ –VäFJñÊ;DÓÎÕ<Äê”   »À ¢…ÅÄï¢ÃŸÅÄ’Äì^documentLengthˆ”   “’ ¢‘ÄõÅ¢ŸÃÄ’ÅÄì^documentLength”   €Ê 8™‹›ﬁﬂ‡·‚„‰ÂÅÅÅÅÅÅÅÅÅÅ™Á 0¬¬Î7 .¬Ô 0Å ÄÜÄáÄáÅ#Ä∑ÄÖÄáÅ/ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ˝ S°˛Å!Ä)“   S§≈≈Å"ÅÅ"ÅÄ)”   	 ¢
Å$Å%¢Å&Å)Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢Å'Äï¢ŸÅ(Ä’Äì^documentLengthá”   ! £ Å*Å+Å,£"#ıÅ-Å.ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“* +˝\NS.uuidbytesO¸Gy∆1ÄG∫õËz¥Ò‡XÄê“. /˝\NS.uuidbytesOMﬁ=ß!óCEæ˚÷§£¸Â=Äê”   25 ¢4ÄõÅ0¢ŸÄ’Å(Äì^documentLength”   ;F 8™<=>?@ABCDEÅ2Å3Å4Å5Å6Å7Å8Å9Å:Å;™G 0¬¬K7 .¬O 0Å<ÄÜÄáÄáÅ>Ä∑ÄÖÄáÅJÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ] S°^Å=Ä)“  a S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   hk ¢ijÅ?Å@¢lmÅAÅDÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ru ¢sÅBÄï¢vÅCÄóÄì^documentLengthΩ”   |Ä £}~ÅEÅFÅG£ÅÇıÅHÅIÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“â ä˝\NS.uuidbytesOd D§ÄÙAﬁù9ΩÓ‚õíÄê“ç é˝\NS.uuidbytesO˝w[∫B'í$.Äy©†3Äê”   ëî ¢ìÄõÅK¢vÄóÅCÄì^documentLength”   ö• 8™õúùûü†°¢£§ÅMÅNÅOÅPÅQÅRÅSÅTÅUÅV™¶ 0¬¬™ó¨¬Æ 0ÅWÄÜÄáÄáÅZÄ◊ÅfÄáÅgÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  º S°ΩÅXÄ)“  ¿ S§¡v¡vÅYÅÅYÅÄ)J”   »À ¢… Å[Å\¢ÃÕÅ]ÅcÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   “÷ £”‘’Å^Å_Å`£ıÿŸÄíÅaÅbÄì_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“ﬂ ‡˝\NS.uuidbytesOEAÍÚOJµÜˇ„G(ÂJÄê“„ ‰˝\NS.uuidbytesO	–X∞M∫Z„Ï3íaÄê”   ÁÍ ¢ÈÄïÅd¢ŸÏÄ’ÅeÄì^documentLength⁄(”   Úı ¢ÙÄõÅh¢ŸÏÄ’ÅeÄì^documentLength”   ˚ 8™¸˝˛ˇ ÅjÅkÅlÅmÅnÅoÅpÅqÅrÅs™ 0¬¬
û¬ 0ÅtÄÜÄáÄáÅwÅÉÅ"ÄáÅÑÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅuÄ)“  ! S§"˜"˜ÅvÅwÅvÅwÄ)5”   ), ¢*+ÅxÅy¢-.ÅzÅÄÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   37 £456Å{Å|Å}£8ı:Å~ÄíÅÄì_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore“@ A˝\NS.uuidbytesO!rJrÁFÜµøuÖ‚jÄê“D E˝\NS.uuidbytesO+àBÈ◊NÖª·%ÿRs†Äê”   HK ¢JÄïÅÅ¢ŸMÄ’ÅÇÄì^documentLength#@       ”   SV ¢UÄõÅÖ¢ŸMÄ’ÅÇÄì^documentLength”   \g 8™]^_`abcdefÅáÅàÅâÅäÅãÅåÅçÅéÅèÅê™h 0¬¬l7 .¬p 0ÅëÄÜÄáÄáÅìÄ∑ÄÖÄáÅüÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ~ S°ÅíÄ)“  Ç S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   âå ¢äãÅîÅï¢çéÅñÅôÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ìñ ¢ïÄïÅó¢òÄóÅòÄì^documentLength±”   ù° £ûü†ÅöÅõÅú£¢£ıÅùÅûÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“™ ´˝\NS.uuidbytesO&˛,ïîãKÃ°”˛MT°EÔÄê“Æ Ø˝\NS.uuidbytesOú∆#Ôè?JÉ¢Û¯ËÇ—,Äê”   ≤µ ¢¥ÄõÅ†¢òÄóÅòÄì^documentLength”   ª∆ 8™ºΩæø¿¡¬√ƒ≈Å¢Å£Å§Å•Å¶ÅßÅ®Å©Å™Å´™« 0¬¬À≈ .¬œ 0Å¨ÄÜÄáÄáÅØÄôÄÖÄáÅªÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  › S°ﬁÅ≠Ä)“  · S§‚„‚„ÅÜÅÆÅÜÅÆÄ)”   ÈÏ ¢ÍÎÅ∞Å±¢ÌÓÅ≤ÅµÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   Ûˆ ¢ÙÅ≥Äï¢˜ŸÅ¥Ä’Äì^documentLength
”   ˝ £˛ˇ Å∂Å∑Å∏£ıÄíÅπÅ∫Äì_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“
 ˝\NS.uuidbytesOT¢7Ë=IuªïÉ’ì-]ƒÄê“ ˝\NS.uuidbytesOÎ¡£áH[Ö=¡Q.Y∞◊Äê”    ¢ÄõÅº¢Ÿ˜Ä’Å¥Äì^documentLength”   & 8™ !"#$%ÅæÅøÅ¿Å¡Å¬Å√ÅƒÅ≈Å∆Å«™' 0¬¬+7 .¬/ 0Å»ÄÜÄáÄáÅ Ä∑ÄÖÄáÅ÷ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  = S°>Å…Ä)“  A S§ZvZvÅñÅÅñÅÄ)”   HK ¢IJÅÀÅÃ¢LMÅÕÅ”Äì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   RV £STUÅŒÅœÅ–£WıYÅ—ÄíÅ“Äì_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore“_ `˝\NS.uuidbytesOÛ¶}≤©M©≥VÚ™Äê“c d˝\NS.uuidbytesOt®'p@;§0+(Äê”   gj ¢iÄïÅ‘¢ŸlÄ’Å’Äì^documentLength
”   qt ¢sÄõÅ◊¢ŸlÄ’Å’Äì^documentLength”   zÖ 8™{|}~ÄÅÇÉÑÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂÅ‡Å·Å‚™Ü 0¬¬ä7 .¬é 0Å„ÄÜÄáÄáÅÂÄ∑ÄÖÄáÅÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ú S°ùÅ‰Ä)“  † S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   ß™ ¢®©ÅÊÅÁ¢´¨ÅËÅÓÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ±µ £≤≥¥ÅÈÅÍÅÎ£∂∑ıÅÏÅÌÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“æ ø˝\NS.uuidbytesO*ExVÂE Ö®>˚ DÄê“¬ √˝\NS.uuidbytesOo{Q'πƒOÛü4Z ùV9∞Äê”   ∆… ¢«ÅÔÄï¢vÅÄóÄì^documentLength”   œ“ ¢—ÄõÅÒ¢vÄóÅÄì^documentLength”   ÿ„ 8™Ÿ⁄€‹›ﬁﬂ‡·‚ÅÛÅÙÅıÅˆÅ˜Å¯Å˘Å˙Å˚Å¸™‰ 0¬¬Ëƒ¬Ï 0Å˝ÄÜÄáÄáÅˇÅÅœÄáÅÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ˙ S°˚Å˛Ä)“  ˛ S§¡v¡vÅYÅÅYÅÄ)”    ¢Å Å¢	
ÅÅÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢ÄïÅ¢ŸÄ’ÅÄì^documentLengthq”    £ÅÅÅ£ıÅ	Å
ÄíÄì_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width“& '˝\NS.uuidbytesOäçmŒø≥LL∞§€g Ù Äê“* +˝\NS.uuidbytesO– ·tDeπ˜hÙÁ=ú6Äê”   .1 ¢0ÄõÅ¢ŸÄ’ÅÄì^documentLength”   7B 8™89:;<=>?@AÅÅÅÅÅÅÅÅÅÅ™C 0¬¬G≈ .¬K 0ÅÄÜÄáÄáÅÄôÄÖÄáÅ&ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Y S°ZÅÄ)“  ] S§]v]vÅYÅÅYÅÄ)”   dg ¢efÅÅ¢hiÅÅ Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   nq ¢pÄïÅ¢ŸsÄ’ÅÄì^documentLength‰”   x| £yz{Å!Å"Å#£ı~ÄíÅ$Å%Äì_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore“Ö Ü˝\NS.uuidbytesO8:ÃπHLH≥ò…Ø•ôŸÄê“â ä˝\NS.uuidbytesO˙r∆èFøÕ¶≥ÿò‘Äê”   çê ¢èÄõÅ'¢ŸsÄ’ÅÄì^documentLength”   ñ° 8™óòôöõúùûü†Å)Å*Å+Å,Å-Å.Å/Å0Å1Å2™¢ 0¬¬¶7 .¬™ 0Å3ÄÜÄáÄáÅ5Ä∑ÄÖÄáÅAÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∏ S°πÅ4Ä)“  º S§ . . . .ÄÖÄÖÄÖÄÖÄ)”   √∆ ¢ƒ≈Å6Å7¢«»Å8Å;Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   Õ– ¢ŒÅ9Äï¢—Å:ÄóÄì^documentLengthÑ”   ◊€ £ÿŸ⁄Å<Å=Å>£‹›ıÅ?Å@ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“‰ Â˝\NS.uuidbytesO|RıqÔ÷BÓ†≥V¯cÓÅÄê“Ë È˝\NS.uuidbytesOœ›È5÷#B$ònøOÚæ⁄Äê”   ÏÔ ¢ÓÄõÅB¢—ÄóÅ:Äì^documentLength”   ı  8™ˆ˜¯˘˙˚¸˝˛ˇÅDÅEÅFÅGÅHÅIÅJÅKÅLÅM™ 0¬¬≈ .¬	 0ÅNÄÜÄáÄáÅRÄôÄÖÄáÅ^ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅOÄ)“   S§ÅPÅQÅPÅQÄ)
”   $' ¢%&ÅSÅT¢()ÅUÅ[Äì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   .2 £/01ÅVÅWÅX£34ıÅYÅZÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“; <˝\NS.uuidbytesOËU^q3[B∫à	.ii ◊Äê“? @˝\NS.uuidbytesO£É‘bçàOpØ·ÛŸ¸a≤DÄê”   CF ¢DÅ\Äï¢GÅ]ÄóÄì^documentLength$”   MP ¢OÄõÅ_¢GÄóÅ]Äì^documentLength”   Va 8™WXYZ[\]^_`ÅaÅbÅcÅdÅeÅfÅgÅhÅiÅj™b 0¬¬f7 .¬j 0ÅkÄÜÄáÄáÅmÄ∑ÄÖÄáÅyÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  x S°yÅlÄ)“  | S§	}	}ÅPÅÕÅPÅÕÄ)”   ÉÜ ¢ÑÖÅnÅo¢áàÅpÅsÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   çê ¢éÅqÄï¢ëÅrÄóÄì^documentLength”   óõ £òôöÅtÅuÅv£úùıÅwÅxÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“§ •˝\NS.uuidbytesOúpS<¶K¨©L•whê≠Äê“® ©˝\NS.uuidbytesO≈ÂB´:YG˝ßÔé ü‡«Äê”   ¨Ø ¢ÆÄõÅz¢ëÄóÅrÄì^documentLength”   µ∑ 8°∂Å|°∏Å~Äù” U  V W XΩÄ ÄÅ}_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj”   ¿≈ 8§¡¬√ƒÅÅÄÅÅÅÇ§∆«»…ÅÉÅÑÅÖÅèÄù_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_&Xcode3ProjectEditor_Xcode3TargetEditor_Xcode3ProjectInfoEditor_Xcode3TargetEditor“  “ S°”ÅÜÄ)‘÷◊ÿ Ÿ⁄€‹YselectionYtimestamp[documentURLÅâÅàÅáÅé_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj#A∆Œ™∫§©”   ‡„ 8¢·‚ÅäÅã¢‰ÂÅåÅçÄùVEditorVTarget_Xcode3TargetEditor[TheMealsApp“ \ ]ÏÌ_Xcode3ProjectDocumentLocation£ÓÔ `_Xcode3ProjectDocumentLocation_DVTDocumentLocation”   ÒÚ 8††Äù”   ı¯ 8¢ˆ˜ÅëÅì¢˘˙ÅïÅûÄù” U  V W XˇÄ ÄÅí_2x-xcode-log://DE4259B5-7817-44E7-8091-BC59E4CDE608” U  V W XÄ ÄÅî_2x-xcode-log://55CEB2A9-122B-4C7C-8B4C-1B6CFB5F6402”   	 8°Åñ°
ÅóÄù_SelectedDocumentLocations“   S°ÅòÄ)’ ◊ÿ W‚ˇ_expandTranscriptYindexPathÅùÄ ÅíÅô” ≥_NSIndexPathLength_NSIndexPathDataÅöÅú“  !WNS.dataBÅõ“ \ ]#$]NSMutableData£#% `VNSData“ \ ]'([NSIndexPath¢) `[NSIndexPath“ \ ]+,_IDELogDocumentLocation£-. `_IDELogDocumentLocation_DVTDocumentLocation”   02 8°1Åü°3Å†Äù_SelectedDocumentLocations“  7 S°8Å°Ä)’ ◊ÿ W‚?ÅùÄ ÅîÅ¢” ≥AÅ£Åú“ D!B Åõ”   GI 8° JÄ°JÅ•Äù”   MV 8®NOPQRSTUÅ¶ÅßÅ®Å©Å™Å´Å¨Å≠®WXYZ[\]^ÅÆÅ±Å≥Å¥ÅµÅ∂ÅªÅºÄù_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area“  ik°jÅØÅ∞_B./assetNoFavorite.imageset/[universal][][][1x][][][][][][][][][][]“ \ ]no\NSMutableSet£np `UNSSet“  rk°sÅ≤Å∞_./assetNoFavorite.imageset_IBICCatalogOverviewController#@l¿     ”   yz 8††Äù”   } 8°~Å∑°ÄÅ∏Äù_expandedItemIDs“  ÑÜ°ÖÅπÅ∫Q.“ \ ]pâ¢p `ZdetailArea”   åç 8††Äù”   êí °ëÅæ°ìÅøÄì]IDENameString[TheMealsApp— òÅ¡“ \ ]öõVNSNull¢ö `“  ù S° ÄÄ)”   °£ 8° Ä°§ÅƒÄù“  ß©° ÄÅ≈“ \ ]´¨^NSMutableArray£´ è `”   Æ≤ £Ø∞±Å«Å»Å…£≥¥µÅ ÅŒÅ“Äì_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKey”   ªΩ 8°ºÅÀ°æÅÃÄù[TheMealsApp“¬ √ƒWNS.time#A∆ŒË0ÓøÅÕ“ \ ]∆«VNSDate¢∆ `”   …À 8° Åœ°ÃÅ–Äù[TheMealsApp“ –—“YNS.stringÅ—_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64“ \ ]‘’_NSMutableString£‘÷ `XNSString”   ÿ⁄ 8°ŸÅ”°€Å‘Äù_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64“¬ ﬂƒ#A∆ŒË1'ËOÅÕ”   ‚Ì ™„‰ÂÊÁËÈÍÎÏÅ÷Å◊ÅÿÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂ™ 0Ô 0¬ÚÛÙıˆ˜ÄÜÅ‡ÄÜÄáÅ·Å‚Å„Å‰ÅÂÅÊÄìZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:7A60B588-4926-4A88-9679-4FA36F2ECDD0Vx86_64_iphonesimulator18.2ViPhoneZiPhone17,1“  ©†Å≈”    8§ÅÈÅÍÅÎÅÏ§ÅÌÅÓÅÔÅÄù_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildX16C5032aÍ“  !©£"#$ÅÒÅˆÅ˘Å≈”   '+ 8£()*ÅÚÅÛÅÙ£k-ÅæÅıÅÌÄù_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ ”   59 8£()*ÅÚÅÛÅÙ£·;<ÅÖÅ˜Å¯Äùc  %  “ @!Obplist00‘
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—	TrootÄØ)*0:;<#=AIJKLMSWX\_U$null”XNSStringV$class\NSAttributesÄÄÄYSucceeded”WNS.keysZNS.objects°Ä°ÄÄVNSFont÷ !"#$%&'(VNSSizeXNSfFlags\NSDescriptorZNSHasWidthVNSName#@&      àÄÄÄ_.AppleSystemUIFontBold”+,-./_NSFontDescriptorOptions_NSFontDescriptorAttributesÄÄÑÄ”15£234Ä	Ä
Ä£678ÄÄÄÄ_NSFontSizeAttribute_ NSCTFontFeatureSettingsAttribute_NSCTFontUIUsageAttribute“>@°?ÄÄ”BE¢CDÄÄ¢FGÄÄÄ_CTFeatureSelectorIdentifier_CTFeatureTypeIdentifier “NOPQZ$classnameX$classes\NSDictionary¢PRXNSObject“NOTU^NSMutableArray£TVRWNSArray_CTFontBoldUsage“NOYZ_NSFontDescriptor¢[R_NSFontDescriptor“NO]^VNSFont¢]R“NO`a_NSAttributedString¢bR_NSAttributedString    $ ) 2 7 I L Q S o u | Ö å ô õ ù ü © ∞ ∏ √ ≈ « … À Õ ‘ · Ë Ò ˛	!#<C]z|ÅÉäéêíîòöúû†∂ŸÙ˘˚˝ˇ	4NPRWbkx{Ñâòú§∂ªŒ—‰ÈÛ¯             c              %Åõ”   CJ 8¶(EF*HIÅÚÅ˙Å˚ÅÙÅ¸Å˝¶GvMNvvÅfÅÅ˛ÅˇÅÅÄù_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle“¬ Wƒ#A∆Œˆx`À}ÅÕo T o d a y   a t   5 . 0 8 / A M”   [d 8®\]^_`abcÅÅÅÅÅÅÅÅ® 0f 0h¬_ 0¬ÄÜÅ	ÄÜÅ
ÄáÅÄÜÄáÄù_IDEWindowIsFullScreen^IDEWindowFrame_-IDEHasMigratedValuesFromNSRestorableStateData_>IDEWorkspaceTabController_75DACF4F-9477-44F2-97BD-12677AFE5FB7_&IDEWindowTabBarWasVisibleWithSingleTab_IDEActiveWorkspaceTabController_IDEWindowToolbarIsVisible_IDEWindowTabBarIsVisible_209 109 1400 900 0 0 1680 1050 ”   xÇ 8©yz{|}~ÄÅÅÅÅÅÅÅÅÅÅ© 0ÑÖÜáàâ¬ãÄÜÅÅZÅ[Å_Å`ÅbÄáÅeÄù_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorArea”   óû 8¶òôöõúùÅÅÅÅÅÅ¶ü†°¢£§ÅÅ'Å7ÅBÅCÅIÄù_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Find_"Xcode.IDEKit.Navigator.Test.Modern_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigator”   ≠≤ 8§ÆØ∞±ÅÅÅÅ§≥¥µ≥Å Å!Å"Å Äù_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey”   Ωæ ††Äì_IDENavigatorModeSolitary”   ¬ƒ °√Å#°≈Å$Äì_IDENavigatorModeSolitary“ … À_codablePlistRepresentationÅ&Å%O%bplist00‘¿ƒ_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆œ´µ·ØL!#%'),.358:>@CEHJNPRTVXZ\^`cegikmoqsuxz}ÇÑÜàäåéêíîóôõù†¢§¶©´≠Ø≤¥∂∏∫ºæ“	
TpathYindexHint•[TheMealsAppVModuleVSearchVRouter“^expansionState3A∆Œı"π‰i “	§XFavorite“3A∆Œı"πâ'“	¶TCoreTDataVRemoteXResponse“ 3A∆Œı"í“	"•“$3A∆Œı"πëh“	&¶“(3A∆Œı2;pã“	*°+_Package Dependencies“-3A∆Œı"π˝“	/2•01UUtilsWNetwork“43A∆Œı"πR“	6§7RDI“93A∆Œı"π!“	;=§<THome“?3A∆Œı"π≠\“	A=•<BTView“D3A∆Œı"π≈Ω“	F=•GBVDetail“I3A∆Œı"π“	K•LMVDomainUModel“O3A∆Œı"π‚“	Q=•B“S3A∆Œı"πÓÓ“	U£“W3A∆Œı"∏»`“	Y=•B“[3A∆Œı"πús“	]=£“_3A∆Œı"πb “	a•GbYPresenter“d3A∆Œı"πnì“	f•<b“h3A∆Œı"π∞`“	j¢“l3A∆Œı"∏¥–“	n§G“p3A∆Œı"πkÏ“	r°“t3A∆Œı"∏já“	v•LwWUseCase“y3A∆Œı"π-4“	{•|VLocale“~3A∆Œı1—W“	ÄÅ§“É3A∆Œı"π€ı“	Ö=§L“á3A∆Œı"πZ“	â•b“ã3A∆Œı"πﬁî“	ç•b“è3A∆Œı"πã÷“	ë•0B“ì3A∆Œı"πBˆ“	ï2§ñTMeal“ò3A∆Œı"π—“	ö•“ú3A∆Œı"∏ˆ‡“	û£üSApp“°3A∆Œı"∏º{“	£2§0“•3A∆Œı"π9ó“	ß¶|®VEntity“™3A∆Œı0ßçÁ“	¨¶|®“Æ3A∆Œı"í¸“	∞=•0±VMapper“≥3A∆Œı"πN”“	µ•<“∑3A∆Œı"π∑1“	π•G“ª3A∆Œı"πt“	Ω§“ø3A∆Œı"∏”ï°¡“	¬¶<B√^HomeView.swift¢≈»¢∆«#        #¿$      ¢… #@t¯     #@çÿ        $ . < K T £ ® ≠ ∑ Ω … – ◊ ﬁ ‡ Â Ù ˝ ˇ	 %,16=FKTY_dmry~áåé•™≥∏æƒÃŒ”‹·ÊÈÓ˜¸!&+49?FKTY_flqzÖäìòú°™Øµ∫√»Ã—⁄ﬂÂÔÙ˝#,16;DIKPY^dlqzÖåëöü§¶´¥πæ√Ã—◊‹ÂÍı˛	!&+49?DMRVZ_hmrwÄÖåìò°¶≠≤ª¿∆Õ“€‡ÊÎÙ˘ˇ%',3BEHQZ]f             À              o“ \ ]Œœ_&ExplorableOutlineViewArchivableUIState£–— `_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier_”   ”÷ 8¢‘’Å(Å)¢◊ÿÅ*Å6Äù_queryParametersController]filterPattern”   ›· 8£ﬁﬂ‡Å+Å,Å-£‚„‰Å.Å/Å5Äù_lastEasyToInitiateQueryClass_querySpecification[queryAction_IDEBatchFindTextQuery‘ÎÏÌ Ó WÒYqueryTermZnamedScopeZqueryClassÅ0Ä Å3Å4” ÛÙıˆ‡TtextXtermTypeÅ2Å1“ –—˘Å—Wmissing“ \ ]˚¸_IDEBatchFindQueryTerm¢˝ `_IDEBatchFindQueryTerm_IDEBatchFindTextQuery“ \ ] _IDEBatchFindQuerySpecification¢ `_IDEBatchFindQuerySpecificationTfindP”    8§	
Å8Å9Å:Å;§Å<Å=Å>Å<Äù_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey”    ††Äì_IDENavigatorModeSolitary”    °Å?°Å@Äì_IDENavigatorModeSolitary“ " $_codablePlistRepresentationÅ&ÅAO≤bplist00‘_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆Œù¨’Öæ††¢	¢
#        #¿$      ¢#@q      #@å¯     $.<KTUVY\enqz                            É_ Xcode.IDEKit.Navigator.Workspace”   (- 8§)*+,ÅDÅEÅFÅG§.¢ôöÅHÅBÅÅÄù_"Xcode.IDEKit.NavigatorGroup.Issues_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_ Xcode.IDEKit.NavigatorGroup.Test_#Xcode.IDENoticesKit.NoticeNavigator”   9> 8§:;<=ÅJÅKÅLÅM§?@ABÅNÅQÅRÅVÄù_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey”   IK °JÅO°LÅPÄì_IDENavigatorModeSolitaryOnbplist00‘
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—	TrootÄ©!$'+,U$null’V$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiersÄ ÄÄÄ“ZNS.objects†Ä“Z$classnameX$classesWNSArray¢ XNSObject“"#†Ä“%&^NSMutableArray£% “(#°)ÄÄ_IDEFilterIdentifier_NoticeError“-.__DVTFilterExpressionStateValue§/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u Ç ö Æ œ — ” ’ ◊ Ÿ ﬁ È Í Ï Ò ¸!&59>@BDfkåë≤Œ             2              Í_IDENavigatorModeSolitary”   RT °SÅS°UÅTÄì_IDENavigatorModeSolitary“ Y [_codablePlistRepresentationÅ&ÅUO_bplist00‘_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆ŒıÒ÷b§“	
TpathYindexHint¢[TheMealsApp_$Missing package product 'RealmSwift' “^expansionState3A∆ŒÔÂ‹„“	°“3A∆ŒÔÂ‹„7†¢¢#        #¿$      ¢#@q      #@çÿ        $ . < K T Y ^ c m p | £ • ™ π ¬ « … Œ ◊ ÿ € ﬁ Á  Û ¸                           ”   ^` °_ÅW°aÅXÄì_IDENavigatorModeSolitary“ e g_codablePlistRepresentationÅ&ÅYO4bplist00‘_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆ŒıÒ◊Ä§“	
TpathYindexHint°[TheMealsApp “^expansionState3A∆ŒÒˇî<∏“	¢_RemoteDataSource.swift“3A∆Œì©‰?†¢¢#        #¿$      ¢#@Çd     #@çÿ     $.<KTY^cmo{}Çëöü¢ª¿… Õ–Ÿ‚ÂÓ                            ˜#@p@     ”   kn 8¢lmÅ\Å]¢pÄóÅ^Äù_'userPreferredInspectorGroupExtensionIDs_!userPreferredCategoryExtensionIDs“  u©†Å≈#@t¯     ”   y{ 8°zÅa° 0ÄÜÄù_ShowsOnlyVisibleViewObjects”   ÄÉ 8¢ÅÇÅcÅd¢¬¬ÄáÄáÄù_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspace”   äñ 8´ãåçéèêëíìîïÅfÅgÅhÅiÅjÅkÅlÅmÅnÅoÅp´ó .ôöõúùûü†°ÅqÄÖÅrÅsÅtÅuÅ|ÅÜÅíÅìÅØÄù_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ DefaultPersistentRepresentations_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_DebuggerSplitView^MaximizedState_IDEDefaultDebugArea_ EditorMultipleSplitPrimaryLayout”Ø {‡_NSIndexPathValueÅúZOpenInTabs ”   ¥µ 8††Äù“  ∏©°πÅvÅ≈”   º√ 8¶Ωæø¿¡¬ÅwÅxÅyÅzÅ{Å|¶ƒ≈∆ƒ»¬Å}Å~ÅRÅ}Å{ÄáÄùZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments”   ”÷ 8¢‘’ÅÅÄ¢◊ .ÅÅÄÖÄù_TabsAsHistoryItems_SelectedTabIndex“  › S•ﬁﬂ‡·‚ÅÇÅπÅŸÅÅ+Ä)ÿÂÊÁË ÈÍÎÏÌÓÔÒ IÛ_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifierÅáÅöÅúÅÉÅ∏Å∑ÄÅõ÷ıˆ˜ ¯˘˙˚¸˝˛ ._DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifierÅìÅÖÅñÅôÅÑÄÖ_/Xcode.IDENavigableItemDomain.WorkspaceStructure“   S¶ÅÜÅâÅãÅçÅèÅëÄ)” Ï .ZIdentifierUIndexÅáÄÖÅà^HomeView.swift“ \ ]_IDEArchivableStringIndexPair¢ `_IDEArchivableStringIndexPair” ÆÅäÄ»ÅàTView” ÆÅåÄ»ÅàTHome”  ÆÅéÄ»ÅàVModule” % .ÅêÄÖÅà[TheMealsApp” * .ÅíÄÖÅà[TheMealsApp”ÿ ◊/0 WÅîÅïÄ _jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift“ \ ]45_DVTDocumentLocation¢6 `_DVTDocumentLocation“ 89:ZpathStringÅòÅó_M/Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj“ \ ]=>[DVTFilePath£?@ `[DVTFilePath_PackedPathEntry“ \ ]BC_(IDENavigableItemArchivableRepresentation¢D `_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCode”   HS 8™IJKLMNOPQRÅùÅûÅüÅ†Å°Å¢Å£Å§Å•Å¶™T 0¬¬X
û¬\ 0ÅßÄÜÄáÄáÅ™ÅÉÅ"ÄáÅµÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  j S°kÅ®Ä)“  n S§	ﬁp	ﬁ˛ÅÍÅ©ÅÍÅ^Ä)”   vy ¢wxÅ´Å¨¢z{Å≠ÅØÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ÄÉ ¢ÇÄïÅÆ¢MÄóÅÇÄì^documentLength”   âç £äãåÅ∞Å±Å≤£éèıÅ≥Å¥ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ñ ó˝\NS.uuidbytesO+àBÈ◊NÖª·%ÿRs†Äê“ö õ˝\NS.uuidbytesO!rJrÁFÜµøuÖ‚jÄê”   û° ¢†ÄõÅ∂¢MÄóÅÇÄì^documentLength^errorIndicator“ \ ]®©_IDEEditorHistoryItem¢™ `_IDEEditorHistoryItemÿÂÊÁË ÈÍÎ¨≠ÆØ± J ñÅΩÅ√ÅƒÅ∫Å∏ÅÿÄÄ.÷ıˆ˜ ¯˘µ∂¸˝˛ .Å¡ÅªÅñÅôÅÑÄÖ“  º S£ΩæøÅºÅæÅøÄ)” ¨ˇÅΩÅ_Åà_Assets.xcassets” % .ÅêÄÖÅà” À .Å¿ÄÖÅà[TheMealsApp”ÿ ◊–0 WÅ¬ÅïÄ _Zfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Assets.xcassets_com.apple.dt.assetcatalog”   ÷ﬂ 8®◊ÿŸ⁄€‹›ﬁÅ≈Å∆Å«Å»Å…Å ÅÀÅÃ®‡·‚Z‰ÂÊÁÅÕÅœÅ—Å¥Å“Å”Å÷Å◊Äù_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area“  Úk°ÛÅŒÅ∞_B./assetNoFavorite.imageset/[universal][][][1x][][][][][][][][][][]“  ˜k°¯Å–Å∞_./assetNoFavorite.imageset_IBICCatalogOverviewController”   ˝˛ 8††Äù”    8°Å‘°Å’Äù_expandedItemIDs“  Ü°ÖÅπÅ∫ZdetailArea”    8††Äù\Universal 1xÿÂÊÁË ÈÍÎÌ MÛÅ›ÅöÅÁÅ⁄Å∏ÅÄÅõ÷ıˆ˜ ¯˘¸˝˛ .ÅÂÅ€ÅñÅôÅÑÄÖ“  " S¶#$%&'(Å‹ÅﬁÅ‡Å·Å‚Å„Ä)”  .Å›ÄÖÅà_HomePresenter.swift” 0 .ÅﬂÄÖÅàYPresenter” ÆÅåÄ»Åà”  ÆÅéÄ»Åà” % .ÅêÄÖÅà” A .Å‰ÄÖÅà[TheMealsApp”ÿ ◊F0 WÅÊÅïÄ _tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift”   KV 8™LMNOPQRSTUÅËÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒ™W 0¬¬[®€¬_ 0ÅÚÄÜÄáÄáÅÙÅÑÅÄáÅˇÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  m S°nÅÛÄ)“  q S§æøæøÅvÅwÅvÅwÄ)”   x{ ¢yzÅıÅˆ¢|}Å˜Å˘Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ÇÖ ¢ÑÄïÅ¯¢’ÄóÅ}Äì^documentLength”   ãè £åçéÅ˙Å˚Å¸£êëıÅ˝Å˛ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ò ô˝\NS.uuidbytesOÉmFl¶M,è˙˚¢öõd≈Äê“ú ù˝\NS.uuidbytesOÃM∫8ÅﬂB≤Øô0˘êMÄê”   †£ ¢¢ÄõÅ ¢’ÄóÅ}Äì^documentLength_linkBuilder(for:content:)ÿÂÊÁË ÈÍÎ™Ì¨≠Ø LÛÅÅöÅÅÅ∏Å*ÄÅõ÷ıˆ˜ ¯˘≥¥¸˝˛ .ÅÅÅñÅôÅÑÄÖ“  ∫ S¶ªºΩæø¿ÅÅÅ	Å
ÅÅÄ)” ™ .ÅÄÖÅà_HomeRouter.swift” »vÅÅÅàVRouter” ÆÅåÄ»Åà”  ÆÅéÄ»Åà” % .ÅêÄÖÅà” Ÿ .ÅÄÖÅà[TheMealsApp”ÿ ◊ﬁ0 WÅÅïÄ _nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift”   „Ó 8™‰ÂÊÁËÈÍÎÏÌÅÅÅÅÅÅÅÅÅÅ™Ô 0¬¬Û7 .¬˜ 0ÅÄÜÄáÄáÅÄ∑ÄÖÄáÅ(ÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅÄ)“  	 S§⁄Æ⁄ÆÅÄ»ÅÄ»Ä)”    ¢ÅÅ¢Å Å"Äì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢Å!Äï¢ËÅ¢ÄóÄì^documentLength”   #' £$%&Å#Å$Å%£()ıÅ&Å'ÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“0 1˝\NS.uuidbytesOpSzÖn Bl†¬C)TÕ*Äê“4 5˝\NS.uuidbytesO@jH´·3IM¨"†T€°pzÄê”   8; ¢:ÄõÅ)¢ËÄóÅ¢Äì^documentLengthZHomeRouterÿÂÊÁË ÈÍÎBÌDEG KÛÅ/ÅöÅ7Å,Å∏ÅQÄÅõ÷ıˆ˜ ¯˘KL¸˝˛ .Å5Å-ÅñÅôÅÑÄÖ“  R S§STUVÅ.Å0Å2Å3Ä)” BvÅ/ÅÅà_ContentView.swift” ^ .Å1ÄÖÅàSApp” % .ÅêÄÖÅà” g .Å4ÄÖÅà[TheMealsApp”ÿ ◊l0 WÅ6ÅïÄ _`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/ContentView.swift”   q| 8™rstuvwxyz{Å8Å9Å:Å;Å<Å=Å>Å?Å@ÅA™} 0¬¬Åƒ≈¬Ö 0ÅBÄÜÄáÄáÅDÅÅÄáÅOÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ì S°îÅCÄ)“  ó S§⁄€⁄€ÅÅÅÅÄ)”   û° ¢ü†ÅEÅF¢¢£ÅGÅMÄì_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ®¨ £©™´ÅHÅIÅJ£≠ÆıÅKÅLÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“µ ∂˝\NS.uuidbytesOÎ._˛gII©∏Üƒk1MÄê“π ∫˝\NS.uuidbytesOyiW˘Á AJâı#NÒñmÄê”   Ω¿ ¢øÄïÅN¢ÄóÅÄì^documentLength”   ∆… ¢»ÄõÅP¢ÄóÅÄì^documentLengthTbody“  –©°—ÅSÅ≈“‘ ’÷_currentEditorHistoryItemÅTÅzÿÂÊÁË ÈÍÎÏÌ⁄€› IÛÅáÅöÅ_ÅUÅ∏ÅyÄÅõ÷ıˆ˜ ¯˘·‚¸˝˛ .Å^ÅVÅñÅôÅÑÄÖ“  Ë S¶ÈÍÎÏÌÓÅWÅXÅYÅZÅ[Å\Ä)” Ï .ÅáÄÖÅà” ÆÅäÄ»Åà” ÆÅåÄ»Åà”  ÆÅéÄ»Åà” % .ÅêÄÖÅà”  .Å]ÄÖÅà[TheMealsApp”ÿ ◊/0 WÅîÅïÄ ”    8™Å`ÅaÅbÅcÅdÅeÅfÅgÅhÅi™ 0¬¬
û¬" 0ÅjÄÜÄáÄáÅlÅÉÅ"ÄáÅwÄÜÄù_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  0 S°1ÅkÄ)“  4 S§"˜"˜ÅvÅwÅvÅwÄ)”   ;> ¢<=ÅmÅn¢?@ÅoÅqÄì_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   EH ¢GÄïÅp¢MÄóÅÇÄì^documentLength”   NR £OPQÅrÅsÅt£STıÅuÅvÄíÄì_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“[ \˝\NS.uuidbytesO+àBÈ◊NÖª·%ÿRs†Äê“_ `˝\NS.uuidbytesO!rJrÁFÜµøuÖ‚jÄê”   cf ¢eÄõÅx¢MÄóÅÇÄì^documentLengthZemptyGames“ \ ]mn_IDEEditorHistoryStack¢o `_IDEEditorHistoryStack_ItemKind_Editor÷ıˆ˜ ¯˘rs¸˝˛ .ÅÖÅ}ÅñÅôÅÑÄÖ“  y S¶z{|}~Å~ÅÅÄÅÅÅÇÅÉÄ)” Ï .ÅáÄÖÅà” ÆÅäÄ»Åà” ÆÅåÄ»Åà”  ÆÅéÄ»Åà” % .ÅêÄÖÅà” ñ .ÅÑÄÖÅà[TheMealsApp”ÿ ◊/0 WÅîÅïÄ ”   ü° 8°†Åá°¢ÅàÄù_DVTSplitViewItems“  ¶©¢ß®ÅâÅèÅ≈”   ´Ø £¨≠ÆÅäÅãÅå£∞ 0≤ÅçÄÜÅéÄì]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeYIDEEditor#@ç8     ”   ∫æ £¨≠ÆÅäÅãÅå£ø 0¡ÅêÄÜÅëÄì_IDEDebuggerArea#@\¿      ”   «Ã 8§»… ÀÅîÅïÅñÅó§ÕŒ .–ÅòÅùÄÖÅ¶ÄùXLeftViewYRightViewZLayoutMode_IDESplitViewDebugArea”   ◊‹ 8§ÿŸ⁄€ÅôÅöÅõÅú§¬ . 0ÄáÄÖÄóÄÜÄù_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsType”   Á 8®ËÈÍÎÏÌÓÔÅûÅüÅ†Å°Å¢Å£Å§Å•®¬¬¬ 0¬¬¬¬ÄáÄáÄáÄÜÄáÄáÄáÄáÄù_+IDEStructuredConsoleAreaLibraryEnabledState_-IDEStructuredConsoleAreaTimestampEnabledState_*IDEStructuredConsoleAreaPIDTIDEnabledState_,IDEStructuredConsoleAreaMetadataEnabledState_(IDEStructuredConsoleAreaTypeEnabledState_-IDEStructuredConsoleAreaSubsystemEnabledState_/IDEStructuredConsoleAreaProcessNameEnabledState_,IDEStructuredConsoleAreaCategoryEnabledState”    8°Åß°Å®Äù_DVTSplitViewItems“  
©¢Å©Å¨Å≈”    £¨≠ÆÅäÅãÅå£ 0Å™ÄÜÅ´ÄìXLeftView#@s`     ”    £¨≠ÆÅäÅãÅå£ ¬"Å≠ÄáÅÆÄìYRightView#@|      _Layout_LeftToRight         "   ,   1   :   ?   Q   V   \   ^  ¬  »  ’  ›  Ë  Ô  Ù  ˆ  ¯  ˝  ˇ        B  O  l  n  p  r  t  v  x  z  |  ~  Ä  Ç  Ñ  Ü  à  •  ß  ©  ¨  Ø  ±  ¥  ∂  π  º  ø  ¬  ≈  »     Ã  Á      1  X  j  Ä  ù  ±  »  ‚  Ù  '  >  G  \  ^  `  b  d  f  h  j  l  n  p  r    á  ì  ï  ó  ô        #  )  .  7  D  F  H  J  ß  ¥  ∂  ∏  ∫    *  ,  .  0  °  Æ  ∞  ≤  ¥  +  8  :  <  >  ∑  ƒ  ∆  »     8  E  G  I  K  ¡  Œ  –  “  ‘  @  M  O  Q  S  √  Ã  ‘  Ÿ  Ê  Ô  Ò  Û  ı  ˜               =  w  °  Ã  Ÿ  6  8  :  <  >  @  B  D  F  H  J  L  N  P  R  T  V  X  Z  \  ^  `  b  d  f  h  j  l  n  p  r  t  v  x  z  |  ~  Ä  Ç  Ñ  Ü  à  ä  å  é  ê  Ì  Ô  Ò  Û  ı  ˜  ˙  ˝         	                !  $  '  *  -  0  3  6  9  <  ?  B  E  H  K  N  Q  T  W  Z  ]  `  c  f  i  l  o  q  ~  Ä  Ç  Ñ  ˙    	      Ç  è  ë  ì  ï          !  ñ  £  •  ß  ©  )  6  8  :  <  Ø  º  æ  ¿  ¬  <  I  K  M  O  ≈  “  ‘  ÷  ÿ  H  U  W  Y  [  –  ›  ﬂ  ·  „   \   i   k   m   o   ‰   Ò   Û   ı   ˜  !l  !y  !{  !}  !  !  !˝  !ˇ  "  "  "}  "ä  "å  "é  "ê  "ˇ  #  #  #  #  #w  #Ñ  #Ü  #à  #ä  #  #˝  #ˇ  $  $  $Ç  $è  $ë  $ì  $ï  %  %  %  %  %  %ô  %¶  %®  %™  %¨  &  &&  &(  &*  &,  &ß  &¥  &∂  &∏  &∫  '-  ':  '<  '>  '@  'µ  '¬  'ƒ  '∆  '»  (7  (D  (F  (H  (J  (ø  (Ã  (Œ  (–  (“  )E  )R  )T  )V  )X  )…  )÷  )ÿ  )⁄  )‹  *T  *a  *c  *e  *g  *€  *Ë  *Í  *Ï  *Ó  +i  +v  +x  +z  +|  +Ù  ,  ,  ,  ,  ,Å  ,é  ,ê  ,í  ,î  -  -  -  -  -!  -ù  -™  -ø  -¡  -√  -≈  -«  -…  -À  -Õ  -œ  -—  -”  -Ë  -Í  -Ï  -Ó  -  -Ú  -Ù  -ˆ  -¯  -˙  -¸  -˛  .  .:  .^  .Ä  .§  .   .Ó  /  /,  /I  /R  /U  /W  /Y  /b  /k  /m  /o  /q  /s  /u  /w  /x  /y  /Ü  /ã  /ç  /è  /î  /ñ  /ò  /ö  /Ω  /⁄  /Á  /Ó  /  /Ú  /Ù  /˚  /˝  /ˇ  0  0  0,  0^  0  0à  0ï  0®  0™  0≥  0∫  0ø  0»  0’  0Ë  0Í  0Û  0¸  1	  1  1  1   1"  1$  1)  1+  1-  1/  1;  1J  1S  1T  1V  1Y  1b  1o  1t  1v  1x  1}  1  1Å  1É  1â  1ò  1°  1∑  1æ  1À  1‡  1‚  1‰  1Ê  1Ë  1Í  1Ï  1Ó  1  1Ú  1Ù  2	  2  2  2  2  2  2  2  2  2  2  2  2<  2[  2  2°  2≈  2Î  3  3.  3M  3j  3s  3v  3x  3z  3É  3å  3é  3ê  3í  3î  3ñ  3£  3®  3™  3¨  3±  3≥  3µ  3∑  3⁄  3˜  4  4  4  4  4  4  4  4  4  4   4I  4{  4ú  4•  4≤  4≈  4«  4–  4›  4  4Ú  4ˇ  5  5  5  5  5  5  5  5"  5%  5.  5;  5@  5B  5D  5I  5K  5M  5O  5^  5k  5Ä  5Ç  5Ñ  5Ü  5à  5ä  5å  5é  5ê  5í  5î  5©  5´  5≠  5Ø  5±  5≥  5µ  5∑  5π  5ª  5Ω  5ø  5‹  5˚  6  6A  6e  6ã  6Ø  6Œ  6Ì  7
  7  7  7  7  7#  7,  7.  70  72  74  76  78  7:  7<  7I  7N  7P  7R  7W  7Y  7[  7]  7Ä  7ù  7™  7±  7≥  7µ  7∑  7æ  7¿  7¬  7ƒ  7∆  7¯  8  8B  8K  8X  8k  8m  8v  8É  8ñ  8ò  8•  8™  8¨  8Æ  8≥  8µ  8∑  8π  8»  8—  8“  8‘  8◊  8‡  8‚  8Ô  8Ù  8ˆ  8¯  8˝  8ˇ  9  9  9  9  94  96  98  9:  9<  9>  9@  9B  9D  9F  9H  9]  9_  9a  9c  9e  9g  9i  9k  9m  9o  9q  9s  9ê  9Ø  9”  9ı  :  :?  :c  :Ç  :°  :æ  :«  :   :Ã  :Œ  :◊  :‡  :‚  :‰  :Ê  :Ë  :Í  :Ï  :Ó  :˚  ;   ;  ;  ;	  ;  ;  ;  ;2  ;O  ;\  ;c  ;e  ;g  ;i  ;p  ;r  ;t  ;v  ;x  ;™  ;À  ;Ù  ;˝  <
  <  <  <(  <5  <H  <J  <W  <\  <^  <`  <e  <g  <i  <k  <z  <}  <ä  <è  <ë  <ì  <ò  <ö  <ú  <û  <≠  <∫  <œ  <—  <”  <’  <◊  <Ÿ  <€  <›  <‡  <„  <Ê  <˚  <˛  =   =  =  =  =	  =  =  =  =  =  =1  =P  =t  =ñ  =∫  =‡  >  >#  >B  >_  >h  >k  >n  >p  >y  >Ç  >Ö  >à  >ã  >é  >ê  >í  >î  >°  >¶  >©  >¨  >±  >¥  >∑  >π  >÷  >˘  ?  ?  ?  ?  ?  ?  ?  ?  ?+  ?.  ?;  ?B  ?E  ?H  ?K  ?R  ?U  ?W  ?Z  ?\  ?Ö  ?¶  ?ÿ  ?·  ?Ó  @  @  @  @  @,  @.  @;  @@  @B  @E  @J  @L  @O  @Q  @`  @m  @Ç  @Ö  @à  @ã  @é  @ë  @î  @ó  @ö  @ù  @†  @µ  @∏  @∫  @º  @æ  @¡  @ƒ  @«  @…  @Ã  @Œ  @–  @Ì  A  A0  AR  Av  Aú  A¿  Aﬂ  A˛  B  B$  B'  B*  B,  B5  B>  BA  BD  BG  BJ  BL  BN  B[  B`  Bc  Bf  Bk  Bn  Bq  Bs  Bñ  B≥  B¿  B«  B   BÕ  B–  B◊  BŸ  B‹  Bﬂ  B·  C  C+  C]  Cf  Cs  CÜ  Cà  Cë  Cû  C±  C≥  C¿  C≈  C«  C   Cœ  C—  C‘  C÷  CÂ  CË  CÒ  CÛ  D   D  D  D
  D  D  D  D  D%  D2  DG  DJ  DM  DP  DS  DV  DY  D\  D_  Db  De  Dz  D}  D  DÅ  DÉ  DÜ  Dà  Dä  Då  Dè  Dë  Dì  D∞  Dœ  DÛ  E  E9  E_  EÉ  E¢  E¡  Eﬁ  EÁ  EÍ  EÌ  EÔ  E¯  F  F  F  F  F	  F  F  F  F   F#  F(  F+  F.  F0  FS  Fp  F}  FÑ  Fá  Fä  Fç  Fî  Fó  Fö  Fú  Fû  F«  F˘  G  G#  G0  GC  GE  GN  G[  Gn  Gp  G}  GÇ  GÖ  Gá  Gå  Gè  Gë  Gì  G¢  G§  G±  G∂  G∏  Gª  G¿  G¬  G≈  G«  G÷  G„  G¯  G˚  G˛  H  H  H  H
  H  H  H  H  H+  H.  H0  H2  H4  H7  H9  H;  H=  H@  HB  HD  Ha  HÄ  H§  H∆  HÍ  I  I4  IS  Ir  Iè  Iò  Iõ  Iû  I†  I©  I≤  Iµ  I∏  Iª  Iæ  I¿  I¬  Iœ  I‘  I◊  I⁄  Iﬂ  I‚  IÂ  IÁ  J  J'  J4  J9  J<  J>  JC  JF  JH  JJ  JY  J\  Ji  Jp  Js  Jv  Jy  JÄ  JÉ  JÜ  Jà  Jä  Jº  JÂ  K  K  K  K/  K1  K:  KG  KZ  K\  Ki  Kn  Kp  Ks  Kx  Kz  K}  K  Ké  Kõ  K∞  K≥  K∂  Kπ  Kº  Kø  K¬  K≈  K»  KÀ  KŒ  K„  KÊ  KË  KÍ  KÏ  KÔ  KÚ  Kı  K˜  K˙  K¸  K˛  L  L:  L^  LÄ  L§  L   LÓ  M  M,  MI  MR  MU  MX  MZ  Mc  Ml  Mo  Mr  Mu  Mx  Mz  M|  M~  Mã  Mê  Mì  Mñ  Mõ  Mû  M°  M£  M∆  M„  M  M˜  M˙  M˝  N   N  N
  N  N  N  N:  N[  Nç  Nñ  N£  N∂  N∏  N¡  NŒ  N·  N„  N  Nı  N˜  N˙  Nˇ  O  O  O  O  O  O!  O#  O0  O5  O7  O:  O?  OA  OD  OF  OU  Ob  Ow  Oz  O}  OÄ  OÉ  OÜ  Oâ  Oå  Oè  Oí  Oï  O™  O≠  OØ  O±  O≥  O∂  O∏  O∫  Oº  Oø  O¡  O√  O‡  Oˇ  P#  PE  Pi  Pè  P≥  P“  PÒ  Q  Q  Q  Q  Q  Q(  Q1  Q4  Q7  Q:  Q=  Q?  QA  QN  QS  QV  QY  Q^  Qa  Qd  Qf  QÉ  Q¶  Q≥  Q∏  Qª  QΩ  Q¬  Q≈  Q«  Q…  Qÿ  Q€  QË  QÔ  QÚ  Qı  Q¯  Qˇ  R  R  R  R	  R2  Rd  RÖ  Ré  Rõ  RÆ  R∞  Rπ  R∆  RŸ  R€  RË  RÌ  RÔ  RÚ  R˜  R˘  R¸  R˛  S  S  S/  S2  S5  S8  S;  S>  SA  SD  SG  SJ  SM  Sb  Se  Sg  Si  Sk  Sn  Sq  St  Sv  Sy  S{  S}  Sö  Sπ  S›  Sˇ  T#  TI  Tm  Tå  T´  T»  T—  T‘  T◊  TŸ  T‚  TÎ  TÌ  TÔ  TÒ  TÛ  Tı  U  U  U
  U  U  U  U  U  U=  UZ  Ug  Un  Uq  Ut  Uw  U~  UÅ  UÑ  UÜ  Uà  U±  U„  V  V  V  V-  V/  V8  VE  VX  VZ  Vg  Vl  Vo  Vq  Vv  Vy  V{  V}  Vå  Vè  Vò  Vö  Vß  V¨  VÆ  V±  V∂  V∏  Vª  VΩ  VÃ  VŸ  VÓ  VÒ  VÙ  V˜  V˙  V˝  W   W  W  W	  W  W!  W$  W&  W(  W*  W-  W/  W1  W3  W6  W8  W:  WW  Wv  Wö  Wº  W‡  X  X*  XI  Xh  XÖ  Xé  Xë  Xî  Xñ  Xü  X®  X´  XÆ  X±  X¥  X∂  X∏  X≈  X   XÕ  X–  X’  Xÿ  X€  X›  X˙  Y  Y*  Y/  Y1  Y4  Y9  Y;  Y>  Y@  YO  YR  Y_  Yf  Yi  Yl  Yo  Yv  Yx  Y{  Y~  YÄ  Y°  Y”  Y¸  Z  Z  Z%  Z'  Z0  Z=  ZP  ZR  Z_  Zd  Zf  Zi  Zn  Zp  Zs  Zu  ZÑ  Zë  Z¶  Z©  Z¨  ZØ  Z≤  Zµ  Z∏  Zª  Zæ  Z¡  Zƒ  ZŸ  Z‹  Zﬁ  Z‡  Z‚  ZÂ  ZÁ  ZÈ  ZÎ  ZÓ  Z  ZÚ  [  [.  [R  [t  [ò  [æ  [‚  \  \   \=  \F  \I  \L  \N  \W  \`  \c  \f  \i  \l  \n  \{  \Ä  \É  \Ü  \ã  \é  \ë  \ì  \∂  \”  \‡  \Á  \Í  \Ì  \  \˜  \˘  \¸  \ˇ  ]  ]"  ]T  ]}  ]Ü  ]ì  ]¶  ]®  ]±  ]æ  ]—  ]”  ]‡  ]Â  ]Á  ]Í  ]Ô  ]Ò  ]Ù  ]ˆ  ^  ^  ^  ^  ^  ^  ^$  ^&  ^)  ^+  ^:  ^G  ^\  ^_  ^b  ^e  ^h  ^k  ^n  ^q  ^t  ^w  ^z  ^è  ^í  ^î  ^ñ  ^ò  ^õ  ^û  ^°  ^£  ^¶  ^®  ^™  ^«  ^Ê  _
  _,  _P  _v  _ö  _π  _ÿ  _ı  _˛  `  `  `  `  `  `  `  `!  `$  `&  `(  `*  `7  `<  `?  `B  `G  `J  `M  `O  `r  `è  `ú  `£  `¶  `©  `¨  `≥  `µ  `∏  `ª  `Ω  `ﬁ  a  a9  aB  aO  ab  ad  am  az  aç  aè  aú  a°  a£  a¶  a´  a≠  a∞  a≤  a¡  aƒ  aÕ  aœ  a‹  a·  a„  aÊ  aÎ  aÌ  a  aÚ  b  b  b#  b&  b)  b,  b/  b2  b5  b8  b;  b>  bA  bV  bY  b[  b]  b_  bb  bd  bg  bi  bl  bn  bp  bç  b¨  b–  bÚ  c  c<  c`  c  cû  cª  cƒ  c«  c   cÃ  c’  cﬁ  c·  c‰  cÁ  cÍ  cÏ  cÓ  c  c˝  d  d  d  d  d  d  d  d2  dU  db  dg  dj  dl  dq  dt  dv  dx  dá  dä  dó  dû  d°  d§  dß  dÆ  d±  d¥  d∂  d∏  d·  e  e4  e=  eJ  e]  e_  eh  eu  eà  eä  eå  eô  eû  e†  e£  e®  e™  e≠  eØ  eæ  eÀ  e‡  e„  eÊ  eÈ  eÏ  eÔ  eÚ  eı  e¯  e˚  e˛  f  f  f  f  f  f  f!  f#  f%  f(  f*  f,  fI  fh  få  fÆ  f“  f¯  g  g;  gZ  gw  gÄ  gÉ  gÜ  gà  gë  gö  gú  gû  g†  g¢  g§  g±  g∂  gπ  gº  g¡  gƒ  g«  g…  gÏ  h	  h  h  h   h#  h&  h-  h0  h3  h5  h7  h`  hí  h≥  hº  h…  h‹  hﬁ  hÁ  hÙ  i  i	  i  i  i  i   i%  i'  i*  i,  i;  i=  iJ  iO  iQ  iT  iY  i[  i^  i`  io  i|  ië  iî  ió  iö  iù  i†  i£  i¶  i©  i¨  iØ  iƒ  i«  i…  iÀ  iÕ  i–  i”  i÷  iÿ  i€  i›  iﬂ  i¸  j  j?  ja  jÖ  j´  jœ  jÓ  k  k*  k3  k6  k9  k;  kD  kM  kP  kS  kV  kY  k[  k]  k_  kl  kq  kt  kw  k|  k  kÇ  kÑ  kß  kƒ  k—  kÿ  k€  kﬁ  k·  kË  kÎ  kÓ  k  kÚ  l$  lM  ln  lw  lÑ  ló  lô  l¢  lØ  l¬  lƒ  l—  l÷  lÿ  l€  l‡  l‚  lÂ  lÁ  lˆ  l˘  m  m  m  m  m  m  m  m  m+  m8  mM  mP  mS  mV  mY  m\  m_  mb  me  mh  mk  mÄ  mÉ  mÖ  má  mâ  må  mé  mê  mí  mï  mó  mô  m∂  m’  m˘  n  n?  ne  nâ  n®  n«  n‰  nÌ  n  nÛ  nı  n˛  o  o
  o  o  o  o  o  o$  o)  o,  o/  o4  o7  o:  o<  o_  o|  oâ  oê  oì  oñ  oô  o†  o£  o¶  o®  o™  o”  p  p&  p/  p<  pO  pQ  pZ  pg  pz  p|  pâ  pé  pê  pì  pò  pö  pù  pü  pÆ  p±  pæ  p√  p≈  p»  pÕ  pœ  p“  p‘  p„  p  q  q  q  q  q  q  q  q  q  q   q#  q8  q;  q=  q?  qA  qD  qF  qH  qJ  qM  qO  qQ  qn  qç  q±  q”  q˜  r  rA  r`  r  rú  r•  r®  r´  r≠  r∂  rø  r¬  rƒ  r«  r…  rÀ  rÿ  r›  r‡  r„  rË  rÎ  rÓ  r  s  s0  s=  sD  sG  sJ  sM  sT  sW  sZ  s\  s^  sê  sπ  s⁄  s„  s  t  t  t  t  t.  t0  t=  tB  tE  tG  tL  tO  tQ  tS  tb  te  tr  tw  ty  t|  tÅ  tÉ  tÜ  tà  tó  t§  tπ  tº  tø  t¬  t≈  t»  tÀ  tŒ  t—  t‘  t◊  tÏ  tÔ  tÒ  tÛ  tı  t¯  t˙  t¸  t˛  u  u  u  u"  uA  ue  uá  u´  u—  uı  v  v3  vP  vY  v\  v_  va  vj  vs  vv  vy  v|  v  vÅ  vé  vì  vñ  vô  vû  v°  v§  v¶  v√  vÊ  vÛ  v¯  v˙  v˝  w  w  w  w	  w  w  w(  w/  w2  w5  w8  w?  wB  wD  wG  wI  wr  wì  w≈  wŒ  w€  wÓ  w  w˘  x  x  x  x(  x-  x/  x2  x7  x9  x<  x>  xM  xZ  xo  xr  xu  xx  x{  x~  xÅ  xÑ  xá  xä  xç  x¢  x•  xß  x©  x´  xÆ  x±  x¥  x∂  xπ  xª  xΩ  x⁄  x˘  y  y?  yc  yâ  y≠  yÃ  yÎ  z  z  z  z  z  z"  z+  z.  z0  z3  z5  z7  z9  zF  zK  zN  zQ  zV  zY  z\  z^  z{  zû  z´  z∞  z≤  zµ  z∫  zº  zø  z¡  z–  z”  z‡  zÁ  zÍ  zÌ  z  z˜  z˘  z¸  zˇ  {  {"  {K  {}  {Ü  {ì  {¶  {®  {±  {æ  {—  {”  {’  {‚  {Á  {È  {Ï  {Ò  {Û  {ˆ  {¯  |  |  |)  |,  |/  |2  |5  |8  |;  |>  |A  |D  |G  |\  |_  |a  |c  |e  |h  |j  |l  |n  |q  |s  |u  |í  |±  |’  |˜  }  }A  }e  }Ñ  }£  }¿  }…  }Ã  }œ  }—  }⁄  }„  }Ê  }È  }Ï  }Ô  }Ò  }Û  ~   ~  ~  ~  ~  ~  ~  ~  ~5  ~X  ~e  ~j  ~m  ~o  ~t  ~w  ~y  ~{  ~ä  ~ç  ~ö  ~°  ~§  ~ß  ~™  ~±  ~¥  ~∂  ~π  ~ª  ~‰    7  @  M  `  b  k  x  ã  ç  ö  ü  °  §  ©  ´  Æ  ∞  ø  Ã  ·  ‰  Á  Í  Ì    Û  ˆ  ˘  ¸  ˇ  Ä  Ä  Ä  Ä  Ä  Ä   Ä"  Ä$  Ä&  Ä)  Ä+  Ä-  ÄJ  Äi  Äç  ÄØ  Ä”  Ä˘  Å  Å<  Å[  Åx  ÅÅ  ÅÑ  Åá  Åâ  Åí  Åõ  Åû  Å°  Å§  Åß  Å©  Å´  Å∏  ÅΩ  Å¿  Å√  Å»  ÅÀ  ÅŒ  Å–  ÅÌ  Ç  Ç  Ç"  Ç$  Ç'  Ç,  Ç.  Ç1  Ç3  ÇB  ÇE  ÇR  ÇY  Ç\  Ç_  Çb  Çi  Çl  Çn  Çq  Çs  Ç•  Ç∆  ÇÔ  Ç¯  É  É  É  É#  É0  ÉC  ÉE  ÉR  ÉW  ÉY  É\  Éa  Éc  Éf  Éh  Éw  ÉÑ  Éô  Éú  Éü  É¢  É•  É®  É´  ÉÆ  É±  É¥  É∑  ÉÃ  Éœ  É—  É”  É’  Éÿ  É⁄  É‹  Éﬁ  É·  É„  ÉÂ  Ñ  Ñ!  ÑE  Ñg  Ñã  Ñ±  Ñ’  ÑÙ  Ö  Ö0  Ö9  Ö<  Ö?  ÖA  ÖJ  ÖS  ÖV  ÖY  Ö\  Ö_  Öa  Öc  Öp  Öu  Öx  Ö{  ÖÄ  ÖÉ  ÖÜ  Öà  Ö•  Ö»  Ö’  Ö⁄  Ö›  Öﬂ  Ö‰  ÖÁ  ÖÈ  ÖÎ  Ö˙  Ö˝  Ü
  Ü  Ü  Ü  Ü  Ü!  Ü$  Ü&  Ü)  Ü+  ÜT  Üu  Üß  Ü∞  ÜΩ  Ü–  Ü“  Ü€  ÜË  Ü˚  Ü˝  á
  á  á  á  á  á  á  á   á/  á<  áQ  áT  áW  áZ  á]  á`  ác  áf  ái  ál  áo  áÑ  áá  áâ  áã  áç  áê  áí  áî  áñ  áô  áõ  áù  á∫  áŸ  á˝  à  àC  ài  àç  à¨  àÀ  àË  àÒ  àÙ  à˜  à˘  â  â  â  â  â  â  â  â"  â'  â*  â-  â2  â5  â8  â:  âW  âz  âá  âå  âè  âë  âñ  âô  âõ  âù  â¨  âØ  âº  â√  â∆  â…  âÃ  â”  â÷  âÿ  â€  â›  ä  ä'  äY  äb  äo  äÇ  äÑ  äç  äö  ä≠  äØ  äº  ä¡  ä√  ä∆  äÀ  äÕ  ä–  ä“  ä·  äÓ  ã  ã  ã	  ã  ã  ã  ã  ã  ã  ã  ã!  ã6  ã9  ã;  ã=  ã?  ãB  ãE  ãH  ãJ  ãM  ãO  ãQ  ãn  ãç  ã±  ã”  ã˜  å  åA  å`  å  åú  å•  å®  å´  å≠  å∂  åø  å¬  å≈  å»  åÀ  åÕ  åœ  å‹  å·  å‰  åÁ  åÏ  åÔ  åÚ  åÙ  ç  ç4  çA  çH  çK  çN  çQ  çX  ç[  ç]  ç`  çb  çã  ç¨  çﬁ  çÁ  çÙ  é  é	  é  é  é2  é4  éA  éF  éI  éK  éP  éS  éU  éW  éf  éi  ér  é  éÑ  éÜ  éâ  éé  éê  éì  éï  é§  é±  é∆  é…  éÃ  éœ  é“  é’  éÿ  é€  éﬁ  é·  é‰  é˘  é¸  é˛  è   è  è  è  è  è  è  è  è  è1  èP  èt  èñ  è∫  è‡  ê  ê#  êB  ê_  êh  êk  ên  êp  êy  êÇ  êÖ  êà  êã  êé  êê  êí  êî  ê°  ê¶  ê©  ê¨  ê±  ê¥  ê∑  êπ  ê÷  ê˘  ë  ë  ë  ë  ë  ë  ë  ë  ë+  ë.  ë;  ëB  ëE  ëH  ëK  ëR  ëU  ëX  ëZ  ë\  ëÖ  ë∑  ëÿ  ë·  ëÓ  í  í  í  í  í,  í.  í7  íD  íI  íK  íN  íS  íU  íX  íZ  íi  ív  íã  íé  íë  íî  íó  íö  íù  í†  í£  í¶  í©  íæ  í¡  í√  í≈  í«  í   íÃ  íŒ  í–  í”  í’  í◊  íÙ  ì  ì7  ìY  ì}  ì£  ì«  ìÊ  î  î"  î+  î.  î1  î3  î<  îE  îH  îK  îN  îQ  îS  îU  îW  îd  îi  îl  îo  ît  îw  îz  î|  îü  îº  î…  î–  î”  î÷  îŸ  î‡  î„  îÊ  îË  îÍ  ï  ïE  ïf  ïo  ï|  ïè  ïë  ïö  ïß  ï∫  ïº  ï…  ïŒ  ï–  ï”  ïÿ  ï⁄  ï›  ïﬂ  ïÓ  ïÒ  ï˛  ñ  ñ  ñ  ñ  ñ  ñ  ñ  ñ#  ñ0  ñE  ñH  ñK  ñN  ñQ  ñT  ñW  ñZ  ñ]  ñ`  ñc  ñx  ñ{  ñ}  ñ  ñÅ  ñÑ  ñá  ñâ  ñã  ñé  ñê  ñí  ñØ  ñŒ  ñÚ  ó  ó8  ó^  óÇ  ó°  ó¿  ó›  óÊ  óÈ  óÏ  óÓ  ó˜  ò   ò  ò  ò	  ò  ò  ò  ò  ò"  ò%  ò(  ò-  ò0  ò3  ò5  òR  òu  òÇ  òá  òâ  òå  òë  òì  òñ  òò  òß  ò™  ò∑  òæ  ò¡  òƒ  ò«  òŒ  ò—  ò‘  ò÷  òÿ  ô  ô3  ôT  ô]  ôj  ô}  ô  ôà  ôï  ô®  ô™  ô≥  ô¿  ô≈  ô«  ô   ôœ  ô—  ô‘  ô÷  ôÂ  ôÚ  ö  ö
  ö  ö  ö  ö  ö  ö  ö  ö"  ö%  ö:  ö=  ö?  öA  öC  öF  öH  öJ  öL  öO  öQ  öS  öp  öè  ö≥  ö’  ö˘  õ  õC  õb  õÅ  õû  õß  õ™  õ≠  õØ  õ∏  õ¡  õƒ  õ«  õ   õÕ  õœ  õ—  õﬁ  õ„  õÊ  õÈ  õÓ  õÒ  õÙ  õˆ  ú  ú6  úC  úH  úJ  úM  úR  úT  úW  úY  úh  úk  úx  ú  úÇ  úÖ  úà  úè  úí  úî  úó  úô  úÀ  úÏ  ù  ù  ù+  ù>  ù@  ùI  ùV  ùi  ùk  ùx  ù}  ù  ùÇ  ùá  ùâ  ùå  ùé  ùù  ù™  ùø  ù¬  ù≈  ù»  ùÀ  ùŒ  ù—  ù‘  ù◊  ù⁄  ù›  ùÚ  ùı  ù˜  ù˘  ù˚  ù˛  û   û  û  û  û
  û  û)  ûH  ûl  ûé  û≤  ûÿ  û¸  ü  ü:  üW  ü`  üc  üf  üh  üq  üz  ü}  üÄ  üÉ  üÜ  üà  üä  üó  üú  üü  ü¢  üß  ü™  ü≠  üØ  üÃ  üÔ  ü¸  †  †  †  †  †  †  †  †!  †$  †1  †8  †;  †>  †A  †H  †K  †M  †P  †R  †{  †ú  †Œ  †◊  †‰  †˜  †˘  °  °  °"  °$  °1  °6  °8  °;  °@  °B  °E  °G  °V  °c  °x  °{  °~  °Å  °Ñ  °á  °ä  °ç  °ê  °ì  °ñ  °´  °Æ  °∞  °≤  °¥  °∑  °π  °ª  °Ω  °¿  °¬  °ƒ  °·  ¢   ¢$  ¢F  ¢j  ¢ê  ¢¥  ¢”  ¢Ú  £  £  £  £  £   £)  £2  £5  £8  £;  £>  £@  £B  £O  £T  £W  £Z  £_  £b  £e  £g  £ä  £ß  £¥  £ª  £æ  £¡  £ƒ  £À  £Œ  £–  £”  £’  £˛  §  §Q  §Z  §g  §z  §|  §Ö  §í  §•  §ß  §¥  §π  §º  §æ  §√  §∆  §»  §   §Ÿ  §‹  §È  §Ó  §  §Û  §¯  §˙  §˝  §ˇ  •  •  •0  •3  •6  •9  •<  •?  •B  •E  •H  •K  •N  •c  •f  •h  •j  •l  •o  •q  •s  •u  •x  •z  •|  •ô  •∏  •‹  •˛  ¶"  ¶H  ¶l  ¶ã  ¶™  ¶«  ¶–  ¶”  ¶÷  ¶ÿ  ¶·  ¶Í  ¶Ì  ¶  ¶Û  ¶ˆ  ¶¯  ¶˙  ß  ß  ß  ß  ß  ß  ß  ß  ß<  ß_  ßl  ßq  ßt  ßv  ß{  ß~  ßÄ  ßÇ  ßë  ßî  ß°  ß®  ß´  ßÆ  ß±  ß∏  ßª  ßæ  ß¿  ß¬  ßÎ  ®  ®>  ®G  ®T  ®g  ®i  ®r  ®  ®í  ®î  ®°  ®¶  ®®  ®´  ®∞  ®≤  ®µ  ®∑  ®∆  ®”  ®Ë  ®Î  ®Ó  ®Ò  ®Ù  ®˜  ®˙  ®˝  ©   ©  ©  ©  ©  ©   ©"  ©$  ©'  ©)  ©+  ©-  ©0  ©2  ©4  ©Q  ©p  ©î  ©∂  ©⁄  ™   ™$  ™C  ™b  ™  ™à  ™ã  ™é  ™ê  ™ô  ™¢  ™§  ™¶  ™®  ™™  ™¨  ™π  ™æ  ™¡  ™ƒ  ™…  ™Ã  ™œ  ™—  ™Ó  ´  ´  ´#  ´&  ´(  ´-  ´0  ´2  ´4  ´C  ´F  ´S  ´Z  ´]  ´`  ´c  ´j  ´m  ´p  ´r  ´t  ´ù  ´œ  ´  ´˘  ¨  ¨  ¨  ¨$  ¨1  ¨D  ¨F  ¨S  ¨X  ¨Z  ¨]  ¨b  ¨d  ¨g  ¨i  ¨x  ¨Ö  ¨ö  ¨ù  ¨†  ¨£  ¨¶  ¨©  ¨¨  ¨Ø  ¨≤  ¨µ  ¨∏  ¨Õ  ¨–  ¨“  ¨‘  ¨÷  ¨Ÿ  ¨€  ¨ﬁ  ¨‡  ¨„  ¨Â  ¨Á  ≠  ≠#  ≠G  ≠i  ≠ç  ≠≥  ≠◊  ≠ˆ  Æ  Æ2  Æ;  Æ>  ÆA  ÆC  ÆL  ÆU  ÆX  Æ[  Æ^  Æa  Æc  Æe  Ær  Æw  Æz  Æ}  ÆÇ  ÆÖ  Æà  Æä  Æ≠  Æ   Æ◊  Æﬁ  Æ·  Æ‰  ÆÁ  ÆÓ  Æ  ÆÛ  Æˆ  Æ¯  Ø  ØB  Øt  Ø}  Øä  Øù  Øü  Ø®  Øµ  Ø»  Ø   Ø◊  Ø‹  Øﬁ  Ø·  ØÊ  ØË  ØÎ  ØÌ  Ø¸  Øˇ  ∞  ∞  ∞  ∞  ∞  ∞  ∞  ∞"  ∞$  ∞3  ∞@  ∞U  ∞X  ∞[  ∞^  ∞a  ∞d  ∞g  ∞j  ∞m  ∞p  ∞s  ∞à  ∞ã  ∞ç  ∞è  ∞ë  ∞î  ∞ó  ∞ö  ∞ú  ∞ü  ∞°  ∞£  ∞¿  ∞ﬂ  ±  ±%  ±I  ±o  ±ì  ±≤  ±—  ±Ó  ±˜  ±˙  ±˝  ±ˇ  ≤  ≤  ≤  ≤  ≤  ≤  ≤  ≤!  ≤.  ≤3  ≤6  ≤9  ≤>  ≤A  ≤D  ≤F  ≤i  ≤Ü  ≤ì  ≤ö  ≤ù  ≤†  ≤£  ≤™  ≤≠  ≤Ø  ≤≤  ≤¥  ≤Ê  ≥  ≥0  ≥9  ≥F  ≥Y  ≥[  ≥d  ≥q  ≥Ñ  ≥Ü  ≥ì  ≥ò  ≥ö  ≥ù  ≥¢  ≥§  ≥ß  ≥©  ≥∏  ≥ª  ≥ƒ  ≥—  ≥÷  ≥ÿ  ≥€  ≥‡  ≥‚  ≥Â  ≥Á  ≥ˆ  ¥  ¥  ¥  ¥  ¥!  ¥$  ¥'  ¥*  ¥-  ¥0  ¥3  ¥6  ¥K  ¥N  ¥P  ¥R  ¥T  ¥W  ¥Y  ¥[  ¥]  ¥`  ¥b  ¥d  ¥Å  ¥†  ¥ƒ  ¥Ê  µ
  µ0  µT  µs  µí  µØ  µ∏  µª  µæ  µ¿  µ…  µ“  µ‘  µ÷  µÿ  µ⁄  µ‹  µÈ  µÓ  µÒ  µÙ  µ˘  µ¸  µˇ  ∂  ∂  ∂A  ∂N  ∂S  ∂U  ∂X  ∂]  ∂_  ∂b  ∂d  ∂s  ∂v  ∂É  ∂ä  ∂ç  ∂ê  ∂ì  ∂ö  ∂ù  ∂†  ∂¢  ∂§  ∂Õ  ∂ˇ  ∑   ∑)  ∑6  ∑I  ∑K  ∑T  ∑a  ∑t  ∑v  ∑É  ∑à  ∑ä  ∑ç  ∑í  ∑î  ∑ó  ∑ô  ∑®  ∑µ  ∑   ∑Õ  ∑–  ∑”  ∑÷  ∑Ÿ  ∑‹  ∑ﬂ  ∑‚  ∑Â  ∑Ë  ∑˝  ∏   ∏  ∏  ∏  ∏	  ∏  ∏  ∏  ∏  ∏  ∏  ∏3  ∏R  ∏v  ∏ò  ∏º  ∏‚  π  π%  πD  πa  πj  πm  πp  πr  π{  πÑ  πá  πä  πç  πê  πí  πî  π°  π¶  π©  π¨  π±  π¥  π∑  ππ  π÷  π˘  ∫  ∫  ∫  ∫  ∫  ∫  ∫  ∫  ∫+  ∫.  ∫;  ∫B  ∫E  ∫H  ∫K  ∫R  ∫T  ∫W  ∫Z  ∫\  ∫}  ∫¶  ∫ÿ  ∫·  ∫Ó  ª  ª  ª  ª  ª,  ª.  ª;  ª@  ªB  ªE  ªJ  ªL  ªO  ªQ  ª`  ªm  ªÇ  ªÖ  ªà  ªã  ªé  ªë  ªî  ªó  ªö  ªù  ª†  ªµ  ª∏  ª∫  ªº  ªæ  ª¡  ª√  ª≈  ª«  ª   ªÃ  ªŒ  ªÎ  º
  º.  ºP  ºt  ºö  ºæ  º›  º¸  Ω  Ω"  Ω%  Ω(  Ω*  Ω3  Ω<  Ω?  ΩB  ΩE  ΩH  ΩJ  ΩW  Ω\  Ω_  Ωb  Ωg  Ωj  Ωm  Ωo  Ωí  ΩØ  Ωº  Ω√  Ω∆  Ω…  ΩÃ  Ω”  Ω÷  Ωÿ  Ω€  Ω›  æ  æ0  æY  æb  æo  æÇ  æÑ  æç  æö  æ≠  æØ  æº  æ¡  æ√  æ∆  æÀ  æÕ  æ–  æ“  æ·  æ‰  æÒ  æˆ  æ¯  æ˚  ø   ø  ø  ø  ø  ø#  ø8  ø;  ø>  øA  øD  øG  øJ  øM  øP  øS  øV  øk  øn  øp  ør  øt  øw  øy  ø{  ø}  øÄ  øÇ  øÑ  ø°  ø¿  ø‰  ¿  ¿*  ¿P  ¿t  ¿ì  ¿≤  ¿œ  ¿ÿ  ¿€  ¿ﬁ  ¿‡  ¿È  ¿Ú  ¿Ù  ¿ˆ  ¿¯  ¿˙  ¿¸  ¡	  ¡  ¡  ¡  ¡  ¡  ¡  ¡!  ¡D  ¡a  ¡n  ¡u  ¡x  ¡{  ¡~  ¡Ö  ¡à  ¡ã  ¡ç  ¡è  ¡∏  ¡Í  ¬  ¬  ¬!  ¬4  ¬6  ¬?  ¬L  ¬_  ¬a  ¬n  ¬s  ¬v  ¬x  ¬}  ¬Ä  ¬Ç  ¬Ñ  ¬ì  ¬†  ¬•  ¬ß  ¬™  ¬Ø  ¬±  ¬¥  ¬∂  ¬≈  ¬“  ¬Á  ¬Í  ¬Ì  ¬  ¬Û  ¬ˆ  ¬˘  ¬¸  ¬ˇ  √  √  √  √  √  √!  √#  √&  √)  √,  √.  √1  √3  √5  √R  √q  √ï  √∑  √€  ƒ  ƒ%  ƒD  ƒc  ƒÄ  ƒâ  ƒå  ƒè  ƒë  ƒö  ƒ£  ƒ¶  ƒ©  ƒ¨  ƒØ  ƒ±  ƒæ  ƒ√  ƒ∆  ƒ…  ƒŒ  ƒ—  ƒ‘  ƒ÷  ƒÛ  ≈  ≈#  ≈(  ≈*  ≈-  ≈2  ≈4  ≈7  ≈9  ≈H  ≈K  ≈X  ≈_  ≈b  ≈e  ≈h  ≈o  ≈r  ≈u  ≈w  ≈y  ≈´  ≈‘  ≈ı  ≈˛  ∆  ∆  ∆   ∆)  ∆6  ∆I  ∆K  ∆X  ∆]  ∆_  ∆b  ∆g  ∆i  ∆l  ∆n  ∆}  ∆ä  ∆ü  ∆¢  ∆•  ∆®  ∆´  ∆Æ  ∆±  ∆¥  ∆∑  ∆∫  ∆Ω  ∆“  ∆’  ∆◊  ∆Ÿ  ∆€  ∆ﬁ  ∆‡  ∆‚  ∆‰  ∆Á  ∆È  ∆Î  «  «'  «K  «m  «ë  «∑  «€  «˙  »  »6  »?  »B  »E  »G  »P  »Y  »\  »_  »b  »e  »g  »t  »y  »|  »  »Ñ  »á  »ä  »å  »©  »Ã  »Ÿ  »ﬁ  »‡  »„  »Ë  »Í  »Ì  »Ô  »˛  …  …  …  …  …  …  …%  …'  …*  …-  …/  …P  …Ç  …´  …¥  …¡  …‘  …÷  …ﬂ  …Ï  …ˇ                        "   $   3   @   U   X   [   ^   a   d   g   j   m   p   s   à   ã   ç   è   ë   î   ñ   ò   ö   ù   ü   °   æ   ›  À  À#  ÀG  Àm  Àë  À∞  Àœ  ÀÏ  Àı  À¯  À˚  À˝  Ã  Ã  Ã  Ã  Ã  Ã  Ã  Ã&  Ã+  Ã.  Ã1  Ã6  Ã9  Ã<  Ã>  Ã[  Ã~  Ãã  Ãê  Ãì  Ãï  Ãö  Ãù  Ãü  Ã°  Ã∞  Ã≥  Ã¿  Ã«  Ã   ÃÕ  Ã–  Ã◊  Ã⁄  Ã›  Ãﬂ  Ã·  Õ
  Õ<  Õ]  Õf  Õs  ÕÜ  Õà  Õë  Õû  Õ±  Õ≥  Õ¿  Õ≈  Õ«  Õ   Õœ  Õ—  Õ‘  Õ÷  ÕÂ  ÕÚ  Œ  Œ
  Œ  Œ  Œ  Œ  Œ  Œ  Œ  Œ"  Œ%  Œ:  Œ=  Œ?  ŒA  ŒC  ŒF  ŒH  ŒJ  ŒL  ŒO  ŒQ  ŒS  Œp  Œè  Œ≥  Œ’  Œ˘  œ  œC  œb  œÅ  œû  œß  œ™  œ≠  œØ  œ∏  œ¡  œƒ  œ«  œ   œÕ  œœ  œ—  œ”  œ‡  œÂ  œË  œÎ  œ  œÛ  œˆ  œ¯  –  –8  –E  –L  –O  –R  –U  –\  –_  –b  –d  –f  –è  –¡  –‚  –Î  –¯  —  —  —  —#  —6  —8  —E  —J  —M  —O  —T  —W  —Y  —[  —j  —m  —z  —  —Å  —Ñ  —â  —ã  —é  —ê  —ü  —¨  —¡  —ƒ  —«  —   —Õ  —–  —”  —÷  —Ÿ  —‹  —ﬂ  —Ù  —˜  —˘  —˚  —˝  “   “  “  “  “	  “  “  “*  “I  “m  “è  “≥  “Ÿ  “˝  ”  ”;  ”X  ”a  ”d  ”g  ”i  ”r  ”{  ”~  ”Å  ”Ñ  ”á  ”â  ”ñ  ”õ  ”û  ”°  ”¶  ”©  ”¨  ”Æ  ”À  ”Ó  ”˚  ‘   ‘  ‘  ‘
  ‘  ‘  ‘  ‘   ‘#  ‘0  ‘7  ‘:  ‘=  ‘@  ‘G  ‘J  ‘M  ‘O  ‘Q  ‘z  ‘¨  ‘Õ  ‘÷  ‘„  ‘ˆ  ‘¯  ’  ’  ’!  ’#  ’0  ’5  ’7  ’:  ’?  ’A  ’D  ’F  ’U  ’b  ’e  ’h  ’k  ’n  ’p  ’}  ’  ’Å  ’Ñ  ’€  ’Ë  ’Ò  ’Ù  ’˜  ’˙  ’˝  ÷  ÷	  ÷  ÷  ÷  ÷  ÷D  ÷s  ÷¢  ÷À  ÷Â  ÷˙  ◊  ◊  ◊	  ◊  ◊  ◊&  ◊0  ◊<  ◊?  ◊B  ◊E  ◊H  ◊ü  ◊®  ◊µ  ◊∫  ◊Ω  ◊¿  ◊≈  ◊»  ◊À  ◊Õ  ◊‘  ◊€  ◊  ◊¸  ÿ  ÿ%  ÿ,  ÿL  ÿb  ÿo  ÿp  ÿq  ÿs  ÿÄ  ÿÖ  ÿà  ÿã  ÿê  ÿì  ÿñ  ÿò  ÿ•  ÿß  ÿ©  ÿ¨  ÿ·  ÿÓ  ÿ  ÿÚ  ÿı  Ÿ*  Ÿ7  Ÿ:  Ÿ=  Ÿ@  ŸC  ŸE  Ÿa  Ÿj  Ÿm  Ÿp  Ÿr  Ÿá  Ÿö  Ÿ§  Ÿß  Ÿ©  Ÿ™  Ÿ≠  Ÿ∞  ŸΩ  Ÿ—  Ÿ„  ŸÊ  ŸÈ  ŸÚ  Ÿ˙  Ÿ˝  ⁄   ⁄	  ⁄  ⁄  ⁄%  ⁄.  ⁄:  ⁄?  ⁄K  ⁄T  ⁄m  ⁄t  ⁄ç  ⁄£  ⁄∞  ⁄≥  ⁄∂  ⁄π  ⁄º  ⁄æ  ⁄⁄  ⁄„  ⁄Ê  ⁄È  ⁄Î  €   €  €  €  €	  €  €  €  €  €(  €+  €.  €;  €>  €@  €C  €F  €H  €U  €f  €i  €l  €o  €r  €u  €x  €{  €~  €è  €í  €ï  €ò  €õ  €û  €°  €§  €ß  €©  €√  €œ  €‚  €˝  ‹  ‹  ‹0  ‹>  ‹G  ‹J  ‹M  ‹P  ‹ï  ‹û  ‹´  ‹≤  ‹∏  ‹¡  ‹ƒ  ‹«  ‹   ‹Á  ›  ›  ›  ›  ›  ›!  ›.  ›1  ›4  ›7  ›:  ›<  ›N  ›W  ›Z  ›]  ›`  ›b  ›k  ›p  ›{  ›à  ›â  ›ä  ›å  ›ô  ›ú  ›ü  ›¢  ›•  ›ß  ›µ  ›¡  ›∆  ›…  ›“  ›Ÿ  ›ﬁ  ›Á  ›Í  ›Ï  ›Ó  ›˚  ›˛  ﬁ   ﬁ  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ"  ﬁ1  ﬁ8  ﬁE  ﬁL  ﬁO  ﬁR  ﬁU  ﬁ\  ﬁ_  ﬁb  ﬁe  ﬁg  ﬁà  ﬁ¿  ﬁÈ  ﬁˆ  ﬁ˘  ﬁ¸  ﬁˇ  ﬂ  ﬂ  ﬂ  ﬂ  ﬂ!  ﬂ*  ﬂ-  ﬂ6  ﬂ=  ﬂB  ﬂO  ﬂR  ﬂU  ﬂX  ﬂ[  ﬂ]  ﬂi  ﬂr  ﬂ|  ﬂ  ﬂΩ  ﬂ∆  ﬂÿ  ﬂﬂ  ﬂË  ﬂı  ﬂ¯  ﬂ˚  ﬂ˛  ‡  ‡  ‡A  ‡J  ‡S  ‡V  ‡c  ‡x  ‡{  ‡~  ‡Å  ‡Ñ  ‡á  ‡ä  ‡ç  ‡ê  ‡ì  ‡ñ  ‡´  ‡≠  ‡∞  ‡≤  ‡¥  ‡∑  ‡∫  ‡Ω  ‡¿  ‡√  ‡∆  ‡»  ‡”  ‡Í  ·  ·  ·/  ·F  ·[  ·e  ·z  ·í  ·§  ·∂  ·˜  ·˛  ‚  ‚  ‚&  ‚/  ‚0  ‚3  ‚@  ‚I  ‚L  ‚O  ‚R  ‚U  ‚^  ‚a  ‚d  ‚g  ‚j  ‚l  ‚Ö  ‚†  ‚ª  ‚Ó  ‚Ù  ‚˝  ‚ˇ  „  „  „  „  „  „  „(  „/  „2  „5  „8  „?  „B  „E  „H  „J  „s  „°  „Õ  „œ  „‹  „„  „Ê  „È  „Ï  „Û  „ˆ  „˘  „¸  „˛  ‰  ‰  Ë  Ë   Ë-  Ë:  Ë=  Ë@  ËC  ËF  ËI  ËL  ËY  Ë\  Ë_  Ëb  Ëe  Ëh  Ëk  Ëm  Ëí  Ë∑  Ë·  È  È  È  È   ÈC  ÈP  Èa  Èd  Èg  Èj  Èm  Èp  Ès  Èv  Èy  Èä  Èå  Èè  Èë  Èî  Èñ  Èô  Èõ  Èù  Èü  È∑  È∆  Èˆ  Í7  Í`  ÍÇ  Íû  Íπ  Í€  ÍË  Í˚  Í˛  Î  Î  Î  Î
  Î  Î  Î  Î  Î)  Î+  Î.  Î1  Î4  Î7  Î:  Î=  Î?  ÎB  ÎD  ÎW  Îj  Î~  Îë  Î•  Î≤  Î»  Î€  ÎÈ  Îˆ  Ï  Ï  Ï	  Ï  Ï  Ï  Ï  Ï"  Ï%  Ï(  Ï+  Ï.  Ï1  Ï4  Ï6  ÏY  Ïw  Ïú  Ï∞  Ï¬  ÏË  Ïı  Ï˛  Ì  Ì  Ì  Ì
  Ì  Ì  Ì  Ì  Ì  Ì!  Ì8  ÌL  Ìe  ÌÄ  Ìç  Ìé  Ìè  Ìë  Ì¨  Ìπ  Ìº  Ìø  Ì¬  Ì≈  Ì«  Ì‚  ÌÎ  Ó  Ó  Ó  ı7  ı@  ıi  ıp  ıô  ı˛  ˆ  ˆ  ˆ  ˆ  ˆ  ˆ  ˆ!  ˆ#  ˆ?  ˆM  ˆZ  ˆa  ˆd  ˆg  ˆj  ˆq  ˆt  ˆw  ˆz  ˆ|  ˆõ  ˆ∞  ˆº  ˆ‘  ˆÂ  ˆÔ  ˆ˙  ˜  ˜  ˜
  ˜  ˜  ˜  ˜"  ˜+  ˜.  ˜1  ˜:  ˜=  ˜E  ˜N  ˜f  ˜k  ˜É  ˜õ  ˜§  ˜≈  ˜   ˜Î  ˜  ˜Ò  ˜˛  ¯  ¯
  ¯  ¯  ¯  ¯  ¯  ¯"  ¯%  ¯(  ¯*  ¯A  ¯U  ¯n  ¯â  ¯ñ  ¯ó  ¯ò  ¯ö  ¯µ  ¯¬  ¯≈  ¯»  ¯À  ¯Œ  ¯–  ¯Î  ¯Ù  ˘  ˘  ˘  ˘Ã  ˘Ô  ˘¸  ˙  ˙  ˙  ˙  ˙  ˙  ˙  ˙   ˙#  ˙&  ˙(  ˙M  ˙u  ˙ò  ˙ª  ˙·  ˙Ó  ˙˜  ˙˙  ˙˝  ˚   ˚  ˚  ˚  ˚  ˚  ˚  ˚  ˚1  ˚E  ˚^  ˚y  ˚Ü  ˚â  ˚å  ˚è  ˚í  ˚î  ˚Ø  ˛!  ˛<  ˛I  ˛L  ˛O  ˛R  ˛U  ˛W  ˛r  ˛{  ˛ò  ˛õ  ˛û                7  @  ]  `  c õ § ± ∂ π º ¡ √ ∆ » Ú     # , 9 < ? B D F d q v y | Å É Ö á ü ¡ Œ Â Ë Î Ó Ò Ù ˜ ˙ ˝        " % ( + . 1 4 7 : = ? b è ° ¨ œ Á  ! 0 F i v â å ó ô ¶ ß ® ™ ≥ ∂ π º … ÷ Ÿ ‹ ﬂ ‚ Â Ë ı ¯ ˚ ˛      ' = K W w y Ü ã é ë ñ ô õ ù ≤ ≈ Œ Ÿ ‹ ﬂ ‚ Â Ë Í  ' K ] { è õ π º ø ¬ ≈ » À Õ – È ¸  # 6 R U X [ ^ a c ï û ´ Æ ± ¥ ∑ ∫ Ω ø Ã ◊ › ‡ ‚ Â Ù ˝  ! @ M P R U Z g j l o t Å Ñ Ü â ê ù † ¢ • ± æ ¡ √ ∆ “ ﬂ ‚ Â Á 	T 	] 	s 	x 	é 	ó 	¢ 	• 	® 	¯ 
 
 
 
  
2 
; 
f 
k 
ñ 
¨ 
‹ 
È 
˛    
       1 4 6 8 : = @ C E H J L i à ¨ Œ Ú  < [ z ó † £ ¶ ® ± ∫ Ω ¿ √ ∆ »   ◊ ‹ ﬂ ‚ Á Í Ì Ô  / < A C F K M P R a n u x { ~ Ö à ã ç è ∏ Í   ! 4 6 ? L _ a n s u x }  Ç Ñ ì ¢ ´ ¬ « ﬁ ˇ         . 1 4 7 : = ? H O R U X Z g j m p Ç è í î ó § ß © ¨ ∏ ≈ » À Õ * F S d g j m p s v y | ç ê ì ñ ô ú ü ¢ • ß ¡ Õ ‡ ˚ 	  . < E H K N ì ú ü ¢ • ¬ ‚ Ô  Ò Û     	     ) , / 2 = J K L N [ |  Ç Ö à ã é ê ì ¨ Ø ≤ µ ∏ ª Ω ∆ ” ÷ Ÿ ‹ ﬂ ‚ Â Á Ù ˜ ˘ ¸   " $ ' 1 > A C F S V X [ h k m p } Ä Ç Ö ë û ° § ¶  * ? B E H K N Q T W Z ] r u w y { ~ Å Ñ Ü â ã ç ™ … Ì  3 Y } ú ª ÿ · ‰ Á È Ú ˚ ˛    	    ! & ) , . K n { Ä Ç Ö ä å è ë † ≠ ¥ ∑ ∫ Ω ƒ «   Ã Œ ˜ ) J S ` s u ~ ã û † ≠ ≤ ¥ ∑ º æ ¡ √ “ Ó       ! # & ? B E H K N P Y f i l o r u x z á ä å è ¢ Ø ≤ µ ∏ ø Ã œ — ‘ · ‰ Ê È ˆ ˘ ˚ ˛      , / 2 4 • ≤ «   Õ – ” ÷ Ÿ ‹ ﬂ ‚ Â ˙ ˝ ˇ     
     0 O s ï π ﬂ  " A ^ g j m o x Å Ñ Ü â ã ç ö ü ¢ • ™ ≠ ∞ ≤ œ Ú ˇ   	     $ 1 8 ; > A H K N P R { ≠ Œ ◊ ‰ ˜ ˘   " $ 1 6 8 ; @ B E G V a Ç Ö à ã é ë î ñ ô ≤ µ ∏ ª æ ¡ √ Ã ’ ÿ € ﬁ · „  Û ˆ ˘     " & 3 6 8 ; H K M P \ i l o q ‘ · ˆ ˘ ¸ ˇ        ) , . 0 2 5 8 ; = @ B D a Ä § ∆ Í    4  S  r  è  ò  õ  û  †  ©  ≤  µ  ∏  ª  æ  ¿  Õ  “  ’  ÿ  ›  ‡  „  Â ! !% !2 !9 !< !? !B !I !L !O !Q !S !| !Æ !œ !ÿ !Â !¯ !˙ " " "# "% "2 "7 "9 "< "A "C "F "H "W "d "i "k "n "s "u "x "z "â "é "ó "ö "ù "† "© "ƒ "« "  "Î "Ó "Ò "Ù "˜ "˙ "˝ "ˇ # # # #! #$ #' #* #, #5 #B #E #H #K #N #Q #T #V #c #f #h #k #x #{ #} #Ä #ç #ê #í #ï #¢ #• #ß #™ #∑ #∫ #º #ø #Ã #œ #— #‘ #‡ #Ì # #Û #ı $ $ $ $ $  $# $& $) $, $/ $2 $5 $J $M $O $Q $S $V $Y $\ $^ $a $c $e $Ç $° $≈ $Á % %1 %U %t %ì %∞ %π %º %ø %¡ %  %” %÷ %Ÿ %‹ %ﬂ %· %Ó %Û %ˆ %˘ %˛ & & & &# &F &S &X &Z &] &b &d &g &i &x &Ö &å &è &í &ï &ú &ü &¢ &§ &¶ &œ ' '" '+ '8 'K 'M 'V 'c 'v 'x 'Ö 'ä 'å 'è 'î 'ñ 'ô 'õ '™ 'µ 'æ '÷ '€ 'Û ( ( (! ($ (' (* (- (/ (8 (E (H (K (N (Q (T (W (Y (f (i (k (n ({ (~ (Ä (É (ê (ì (ï (ò (• (® (™ (≠ (∫ (Ω (ø (¬ (œ (“ (‘ (◊ („ ( (Û (ˆ (¯ ) ) ) ) ) ) )' )0 )5 )8 ); )> )K )R )U )X )[ )b )e )g )j )l )z )á )ö )§ )≠ )∫ )¡ )ƒ )« )  )— )‘ )÷ )Ÿ )€ )Ì )ˆ )¯ * * * * * * *# *& *) *+ *. *0 *9 *C *N *f *s *| * *Ç *Ö *à *ë *ì *ï *ó *ô *õ *π *÷ *˘ + + +0 +3 +6 +9 +< +? +B +E +H +Y +[ +] +_ +a +c +e +g +i +k +ô +… +ˆ ,% ,P ,Ä ,≤ ,· ,Ó ,Ò ,Ù ,˜ ,˙ ,¸ - - - -! -$ -' -4 -; -> -A -D -K -N -P -S -U -^ -g -t -{ -~ -Å -Ñ -ã -é -ê -ì -ï -ü -®            '             -Ω

/== TheMealsApp.xcodeproj/project.xcworkspace/xcuserdata/gilangramadhan.xcuserdatad/UserInterfaceState.xcuserstate
bplist00‘        
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—  	UStateÄØÄ      1 2 3 4 5 6 7 8 9 : ; < J K L M N O U [ \ b v w x y z { | } ~ Ç É á ã å ç é í ñ ú ù ° ¢ § • © Ø ≥ ¥ ¿ ¡ ¬ √ ƒ ≈ ∆   “ ” ‘ ‹ › ﬁ ﬂ ‡ Â Ë Ï Ù ¯ ˘ ˝ ˛ $+,-.45=>?BCEFNOPhijklmnopqrv}ÉÑåçïñû¢£ß®ÆØ≤∏0459:>?CDHIMNRSWX\]abfgklpquvz{ÄÑÖâäéèìîòôùû¢£ß®¨≠±≤∂∑ªº¿¡≈∆ Àœ–‘’Ÿ⁄ﬁﬂ„‰ËÈÌÓÚÛ˜¯¸˝ $%)*./3489=>BCGHLMQRjklmnopqrstxÄÅáàêëíöõ≥¥µ∂∑∏π∫ªºΩ¡»… –—Ÿ⁄€„‰¸˝˛ˇ 
 !"*+CDEFGHIJKLMQXY_`hijrsãåçéèêëíìîïô†°¢®©±≤≥ªº‘’÷◊ÿŸ⁄€‹›ﬁ‚ÈÔ¯˘˙ !"#$%)01289ABCKLdefghijklmnryz{ÅÇäãåçéñóØ∞±≤≥¥µ∂∑∏πΩƒ≈ÀÃ‘’÷ﬁﬂ˜¯˘˙˚¸˝˛ˇ &'?@ABCDEFGHIMTU[\defghpqâäãåçéèêëíìóûü•¶ÆØ∞∏π—“”‘’÷◊ÿŸ⁄€ﬂÊÁÌÓˆ˜¯˘ !"#$%&'+234:;CDEFPQRSTUV^_wxyz{|}~ÄÅÖåçìîúùûü†®©¡¬√ƒ≈∆«»… Àœ÷◊›ﬁÊÁËÒ	
$%-./089QRSTUVWXYZ[_fgmnvwxyÅÇöõúùûü†°¢£§®Ø∞±∑∏¿¡¬ À„‰ÂÊÁËÈÍÎÏÌÒ¯˛ˇ							*	+	,	-	.	/	0	1	2	3	4	8	?	@	F	G	O	P	Q	Y	Z	r	s	t	u	v	w	x	y	z	{	|	Ä	á	à	é	è	ó	ò	ô	°	¢	∫	ª	º	Ω	æ	ø	¿	¡	¬	√	ƒ	»	œ	’	÷	ﬁ	ﬂ	Á	Ë
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
é
è
ê
ë
í
ì
î
ï
ñ
ó
ò
ú
£
§
™
´
≥
¥
µ
Ω
æ
÷
◊
ÿ
Ÿ
⁄
€
‹
›
ﬁ
ﬂ
‡
‰
Î
Ï
Ú
Û
˚
¸
˝
˛
ˇ !"#$%&'()*.56<=EFGHIQRjklmnopqrstxÄÜáèêëôö≤≥¥µ∂∑∏π∫ªº¿«ÕŒ÷◊ÿ‡·˘˙˚¸˝˛ˇ  !)*BCDEFGHIJKLPW]^fghpqâäãåçéèêëíìóû§•≠Æ∂∑œ–—“”‘’÷◊ÿŸ›‰ÂÎÏÙıˆ˜ˇ  !"&-34<=>FGabcdefghijklpw}~Üáàíìîïùû∂∑∏π∫ªºΩæø¿ƒÀ—“⁄€‹‰Â˝˛ˇ "#$,-EFGHIJKLMNOSZ`aijkståçéèêëíìîïñö°¢®©±≤≥¥ºΩ◊ÿŸ⁄€‹›ﬁﬂ‡·‚ÊÌÛÙ¸˝	
+,-./0123459@FGOPQYZrstuvwxyz{|Äáçéñóò†°π∫ªºΩæø¿¡¬√«Œœ’÷ﬁﬂ‡ËÈ	
%&'/0HIJKLMNOPQRV]cdlmnvwèêëíìîïñóòôù§•´¨¥µ∂æø◊ÿŸ⁄€‹›ﬁﬂ‡·ÂÏÚÛ˚¸˝ !"#$%&'(,39:BCDLMefghijklmnoszÄÅâäíì´¨≠ÆØ∞±≤≥¥µπ¿¡«»–—“”€‹Ùıˆ˜¯˘˙˚¸˝˛	
#$<=>?@ABCDEFJQRXYabcklÑÖÜáàâäãåçéíôü†®©™≤≥ÀÃÕŒœ–—“”‘’Ÿ‡·ÁËÒÚ˙˚!(./789:BC[\]^_`abcdeipvwÄÅâä¢£§•¶ß®©™´¨∞∑Ωæ∆«»–—€‹›ﬁ‰ÂÈÏÚÛ˜˚%&'()*+,-./0123456BCDEFGHIOYZ[\]ghlpÄÅÇÉÑáàãèïôú©ªºΩæø¿¡¬√∆‹›ﬁﬂ‡·‚„‰Âıˆ˜¯˘˙˚	
!)*+789:;<=>JQTXYZ^nopqrstuÅÇÉÑÖâäãéèêúùûü†°¢ÆØ∞±≤∏π∫ª¡¬∆«ÕŒ“”‘⁄€ﬂ‡·ÁËÒÚ !"&6789:;<=GHIJRbnov|}ÅÖÜäãèêîòúù°•¶ßø¿¡¬√ƒ≈∆«»…Õ‘⁄€„‰ÏÌÓÚ˚#'(,DEFGHIJKLMNRY_`hiqrs|Éåêëïñöû¢¶ß´√ƒ≈∆«»… ÀÃÕ—ÿﬁﬂÁËÈÒÚ˚%,378<@DEIabcdefghijkov|}ÖÜéèìó†ß≠±µπ∫æ“”‘’÷◊ÿŸ⁄ﬁﬂ„‰ÂÈÔÙı˘˙˛ˇ!"'-.3=>?@ABLMNR^_`abnopqrxyÄÖèêëõúùU$null”      WNS.keysZNS.objectsV$class¢  ÄÄ¢  ÄÅÄe_IDEWorkspaceDocument_$F98B5DD0-830B-4F1E-8A69-9F65C20AC7EE”     % 0™        ! " # $ÄÄÄÄÄ	Ä
ÄÄÄÄ™ & ' ( ) * + , - . /ÄÄÅÀÅ⁄Å›ÅÔÅ	Å
ÅÅÄ+_BreakpointsActivated_DefaultEditorStatesForURLs_RunContextRecents\ActiveScheme_ActiveRunDestination_0LastCompletedPersistentSchemeBasedActivityReport_DocumentWindows_WindowArrangementDebugInfo_SelectedWindows_RecentEditorDocumentURLs	”    = C 0• > ? @ A BÄÄÄÄÄ• D E F G HÄÄ3ÄIÄ}ÄÜÄ+_(Xcode.IDEKit.EditorDocument.AssetCatalog_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project_:Xcode.IDEKit.EditorDocument.PegasusNonFileSystemSourceCode_'Xcode.IDEKit.EditorDocument.LogDocument_-Xcode.IDEKit.EditorDocument.PegasusSourceCode”    P R 0° QÄ° SÄÄ+” V  W X Y ZWNS.base[NS.relativeÄ ÄÄ_ofile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Assets.xcassets“ ] ^ _ `Z$classnameX$classesUNSURL¢ _ aXNSObject”    c l 0® d e f g h i j kÄÄÄÄÄÄ Ä!Ä"® m n o p q r s tÄ#Ä&Ä(Ä)Ä*Ä,Ä1Ä2Ä+_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area“    Å° ÄÄ$Ä%_./assetSearchMeal.imageset“ ] ^ Ñ Ö\NSMutableSet£ Ñ Ü aUNSSet“   à Å° âÄ'Ä%_./assetSearchMeal.imageset_IBICCatalogOverviewController#@l¿     ”    è ê 0††Ä+“ ] ^ ì î_NSMutableDictionary£ ì ï a\NSDictionary”    ó ô 0° òÄ-° öÄ.Ä+_expandedItemIDs“   û †° üÄ/Ä0Q.“ ] ^ Ü £¢ Ü a^sourceListArea”    ¶ ß 0††Ä+”    ™ ¨ 0° ´Ä4° ≠Ä6Ä+” V  W X Y ≤Ä ÄÄ5_ifile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj”    µ ∫ 0§ ∂ ∑ ∏ πÄ7Ä8Ä9Ä:§ ª º Ω æÄ;Ä<Ä=ÄHÄ+_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_(Xcode3ProjectEditor_Xcode3PackagesEditor_Xcode3PackagesEditor_Xcode3TargetEditor“   « …° »Ä>ÄG‘ À Ã Õ  Œ œ – —YselectionYtimestamp[documentURLÄAÄ@Ä?ÄF_ifile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj#Aƒñ\›o;”    ’ ÿ 0¢ ÷ ◊ÄBÄC¢ Ÿ ⁄ÄDÄEÄ+WProjectVEditor[TheMealsApp_Xcode3PackagesEditor“ ] ^ · ‚_Xcode3ProjectDocumentLocation£ „ ‰ a_Xcode3ProjectDocumentLocation_DVTDocumentLocation“ ] ^ Ê ÁWNSArray¢ Ê a”    È Í 0††Ä+”    Ì  0¢ Ó ÔÄJÄL¢ Ò ÚÄNÄjÄ+” V  W X Y ˜Ä ÄÄK_}x-xcode-disassembly://stack_frame?launchSessionRef=7fc6d80ce450&stackFrameHash=3526088571801097364&stackFrameRef=7fc6e3be1200” V  W X Y ¸Ä ÄÄM_}x-xcode-disassembly://stack_frame?launchSessionRef=7fc6d80d1430&stackFrameHash=3526088571801097364&stackFrameRef=7fc6c4876b10”    ˇ
 0™ 	ÄOÄPÄQÄRÄSÄTÄUÄVÄWÄX™ &ÄYÄ]Ä]Ä]Ä^ÄfÄ\Ä]ÄgÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ! …°"ÄZÄG“  % …§&&Ä[Ä\Ä[Ä\ÄG ”   /1 °0Ä_°2Ä`Äe_DeltaEditorLayoutExtension”   69 ¢78ÄaÄb¢:;ÄcÄdÄe[lineIndexes^documentLength“  @ …†ÄG“ ] ^ ïD¢ ï a#ø      ”   GJ ¢HIÄhÄi¢:;ÄcÄdÄeUfolds^documentLength”   Q\ 0™RSTUVWXYZ[ÄkÄlÄmÄnÄoÄpÄqÄrÄsÄt™]ae &ÄuÄ]Ä]Ä]ÄwÄfÄ\Ä]Ä{ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  s …°tÄvÄG“  w …§&&Ä[Ä\Ä[Ä\ÄG”   ~Ä °Äx°ÅÄyÄe_DeltaEditorLayoutExtension”   Öà ¢Ü7ÄzÄa¢;:ÄdÄcÄe^documentLength”   éë ¢HêÄhÄ|¢:;ÄcÄdÄe^documentLength”   óö 0¢òôÄ~ÄÄ¢õúÄÇÄÖÄ+” V  W X Y°Ä ÄÄ_2x-xcode-log://70021531-02B3-4560-BC7E-8EEBD4B2389C” V  W X Y¶Ä ÄÄÅ_2x-xcode-log://ACC57B49-B56F-4A2A-BF4F-6A5CD2C698B1”   ©´ 0°™ÄÉ°¨ÄÑÄ+_SelectedDocumentLocations“  ∞ …†ÄG”   ≥µ 0°™ÄÉ°¨ÄÑÄ+”   πÙ 0Ø:∫ªºΩæø¿¡¬√ƒ≈∆«»… ÀÃÕŒœ–—“”‘’÷◊ÿŸ⁄€‹›ﬁﬂ‡·‚„‰ÂÊÁËÈÍÎÏÌÓÔÒÚÛÄáÄâÄãÄçÄèÄëÄìÄïÄóÄôÄõÄùÄüÄ°Ä£Ä•ÄßÄ©Ä´Ä≠ÄØÄ±Ä≥ÄµÄ∑ÄπÄªÄΩÄøÄ¡Ä√Ä≈Ä«Ä…ÄÀÄÕÄœÄ—Ä”Ä’Ä◊ÄŸÄ€Ä›ÄﬂÄ·Ä„ÄÂÄÁÄÈÄÎÄÌÄÔÄÒÄÛÄıÄ˜Ä˘Ø:ıˆ˜¯˘˙˚¸˝˛ˇ 	
 !"#$%&'()*+,-.Ä˚ÅÅ'Å;ÅPÅfÅzÅêÅ®ÅΩÅ“ÅÈÅ˛ÅÅ3ÅJÅ_ÅtÅäÅ†Å¥Å…ÅﬁÅÒÅÅÅ.ÅEÅ\ÅqÅÖÅõÅØÅ¬ÅÿÅÏÅÅÅ.ÅBÅXÅpÅÑÅòÅ≠Å¡Å’ÅÍÅ˛ÅÅ%Å;ÅPÅeÅyÅéÅ£Å∑Ä+” V  W X Y3Ä ÄÄà_Çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/View/SearchRow.swift” V  W X Y8Ä ÄÄä_Öfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swift” V  W X Y=Ä ÄÄå_Öfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/CategoryEntity.swift” V  W X YBÄ ÄÄé_äfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift” V  W X YGÄ ÄÄê_äfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift” V  W X YLÄ ÄÄí_}file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/File.swift” V  W X YQÄ ÄÄî_xfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swift” V  W X YVÄ ÄÄñ_Üfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteRow.swift” V  W X Y[Ä ÄÄò_Üfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/CategoryMapper.swift” V  W X Y`Ä ÄÄö_éfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift” V  W X YeÄ ÄÄú_âfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift” V  W X YjÄ ÄÄû_ãfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extension/CustomeError+Ext.swift” V  W X YoÄ ÄÄ†_Çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swift” V  W X YtÄ ÄÄ¢_Çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/View/CategoryRow.swift” V  W X YyÄ ÄÄ§_wfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/DI/Injection.swift” V  W X Y~Ä ÄÄ¶_Åfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swift” V  W X YÉÄ ÄÄ®_~file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swift” V  W X YàÄ ÄÄ™_Äfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/MealRow.swift” V  W X YçÄ ÄÄ¨_Éfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swift” V  W X YíÄ ÄÄÆ_Ñfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extension/Color+Ext.swift” V  W X YóÄ ÄÄ∞_áfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swift” V  W X YúÄ ÄÄ≤_}file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/TabItem.swift” V  W X Y°Ä ÄÄ¥_áfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swift” V  W X Y¶Ä ÄÄ∂_Üfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataStore.swift” V  W X Y´Ä ÄÄ∏_Éfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift” V  W X Y∞Ä ÄÄ∫_çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/LocaleDataStore.swift” V  W X YµÄ ÄÄº_ëfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift” V  W X Y∫Ä ÄÄæ_Äfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swift” V  W X YøÄ ÄÄ¿_âfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Meal/Presenter/MealPresenter.swift” V  W X YƒÄ ÄÄ¬_ëfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/DetailMeal/Presenter/DetailPresenter.swift” V  W X Y…Ä ÄÄƒ_êfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoryResponse.swift” V  W X YŒÄ ÄÄ∆_ãfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift” V  W X Y”Ä ÄÄ»_åfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift” V  W X YÿÄ ÄÄ _áfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swift” V  W X Y›Ä ÄÄÃ_çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swift” V  W X Y‚Ä ÄÄŒ_qfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/ContentView.swift” V  W X YÁÄ ÄÄ–_àfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/Mapper/IngredientMapper.swift” V  W X YÏÄ ÄÄ“_Äfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/CustomIcon.swift” V  W X YÒÄ ÄÄ‘_àfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift” V  W X YˆÄ ÄÄ÷_ufile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/App/ContentView.swift” V  W X Y˚Ä ÄÄÿ_Éfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/a209-ios-expert-modularization/TheMealsApp/TheMealsApp/ContentView.swift” V  W X Y Ä ÄÄ⁄_àfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift” V  W X YÄ ÄÄ‹_~file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/BlurView.swift” V  W X Y
Ä ÄÄﬁ_àfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swift” V  W X YÄ ÄÄ‡_Éfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swift” V  W X YÄ ÄÄ‚_ñfile:///Users/gilangramadhan/Library/Developer/Xcode/DerivedData/TheMealsApp-dxiufbyyxhculcgugbkseletdnte/SourcePackages/checkouts/Alamofire/README.md” V  W X YÄ ÄÄ‰_çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/MealsResponse.swift” V  W X YÄ ÄÄÊ_tfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/TheMealsAppApp.swift” V  W X Y#Ä ÄÄË_Öfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swift” V  W X Y(Ä ÄÄÍ_ífile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoriesResponse.swift” V  W X Y-Ä ÄÄÏ_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Meal/View/MealView.swift” V  W X Y2Ä ÄÄÓ_file:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift” V  W X Y7Ä ÄÄ_çfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift” V  W X Y<Ä ÄÄÚ_áfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swift” V  W X YAÄ ÄÄÙ_åfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift” V  W X YFÄ ÄÄˆ_áfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swift” V  W X YKÄ ÄÄ¯_áfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swift” V  W X YPÄ ÄÄ˙_åfile:///Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift”   S^ 0™TUVWXYZ[\]Ä¸Ä˝Ä˛ÄˇÅ ÅÅÅÅÅ™_cg &ÅÄ]Ä]Ä]Å
ÄfÄ\Ä]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  u …°vÅÄG“  y …§z{z{ÅÅ	ÅÅ	ÄG"0”   ÇÑ °ÉÅ°ÖÅÄe_DeltaEditorLayoutExtension”   âå ¢ä7ÅÄa¢ç:ÅÄcÄe^documentLength∫”   ìñ ¢HïÄhÅ¢:çÄcÅÄe^documentLength”   úß 0™ùûü†°¢£§•¶ÅÅÅÅÅÅÅÅÅÅ™®¨∞ &ÅÄ]Ä]Ä]Å ÄfÄ\Ä]Å%ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  æ …°øÅÄG“  ¬ …§√ƒ√ƒÅÅÅÅÄG”   ÀÕ °ÃÅ!°ŒÅ"Äe_DeltaEditorLayoutExtension”   “’ ¢”7Å#Äa¢÷¨Å$ÄÑÄe^documentLength¯”   ‹ﬂ ¢HﬁÄhÅ&¢¨÷ÄÑÅ$Äe^documentLength”   Â 0™ÊÁËÈÍÎÏÌÓÔÅ(Å)Å*Å+Å,Å-Å.Å/Å0Å1™Òı˘ &Å2Ä]Ä]Ä]Å4ÄfÄ\Ä]Å9ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°Å3ÄG“   …§Ä\Ä\Ä\Ä\ÄG”    °Å5°Å6Äe_DeltaEditorLayoutExtension”    ¢7ÄaÅ7¢¨ÄÑÅ8Äe^documentLength'”   #& ¢H%ÄhÅ:¢¨ÄÑÅ8Äe^documentLength”   ,7 0™-./0123456Å<Å=Å>Å?Å@ÅAÅBÅCÅDÅE™8<@ &ÅFÄ]Ä]Ä]ÅIÄfÄ\Ä]ÅNÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  N …°OÅGÄG“  R …§SƒSƒÅHÅÅHÅÄG”   Z\ °[ÅJ°]ÅKÄe_DeltaEditorLayoutExtension”   ad ¢7cÄaÅL¢¨fÄÑÅMÄe^documentLength”   kn ¢HmÄhÅO¢¨fÄÑÅMÄe^documentLength”   t 0™uvwxyz{|}~ÅQÅRÅSÅTÅUÅVÅWÅXÅYÅZ™ÄÑà &Å[Ä]Ä]Ä]Å_ÄfÄ\Ä]ÅdÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ñ …°óÅ\ÄG“  ö …§õúõúÅ]Å^Å]Å^ÄG”   £• °§Å`°¶ÅaÄe_DeltaEditorLayoutExtension”   ™≠ ¢7¨ÄaÅb¢¨ØÄÑÅcÄe^documentLengthœ”   ¥∑ ¢H∂ÄhÅe¢¨ØÄÑÅcÄe^documentLength”   Ω» 0™æø¿¡¬√ƒ≈∆«ÅgÅhÅiÅjÅkÅlÅmÅnÅoÅp™…Õ— &ÅqÄ]Ä]Ä]ÅsÄfÄ\Ä]ÅxÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ﬂ …°‡ÅrÄG“  „ …§Ä\Ä\Ä\Ä\ÄG”   ÍÏ °ÎÅt°ÌÅuÄe_DeltaEditorLayoutExtension”   ÒÙ ¢Ú7ÅvÄa¢ı¨ÅwÄÑÄe^documentLengthg”   ˚˛ ¢H˝ÄhÅy¢¨ıÄÑÅwÄe^documentLength”    0™	
Å{Å|Å}Å~ÅÅÄÅÅÅÇÅÉÅÑ™ &ÅÖÄ]Ä]Ä]ÅâÄfÄ\Ä]ÅéÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  & …°'ÅÜÄG“  * …§+,+,ÅáÅàÅáÅàÄG”   35 °4Åä°6ÅãÄe_DeltaEditorLayoutExtension”   := ¢7<ÄaÅå¢:?ÄcÅçÄe^documentLengthm”   DG ¢HFÄhÅè¢:?ÄcÅçÄe^documentLength”   MX 0™NOPQRSTUVWÅëÅíÅìÅîÅïÅñÅóÅòÅôÅö™Y]^_a &ÅõÄ]Ä]Ä]ÅüÅ§Å•Ä]Å¶ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  o …°pÅúÄG“  s …§tvÅùÄ\ÅûÄ\ÄG%&”   |~ °}Å†°Å°Äe_DeltaEditorLayoutExtension”   ÉÜ ¢7ÖÄaÅ¢¢¨àÄÑÅ£Äe^documentLength¿#@      +”   èí ¢HëÄhÅß¢¨àÄÑÅ£Äe^documentLength”   ò£ 0™ôöõúùûü†°¢Å©Å™Å´Å¨Å≠ÅÆÅØÅ∞Å±Å≤™§®¨ &Å≥Ä]Ä]Ä]Å∂ÄfÄ\Ä]ÅªÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∫ …°ªÅ¥ÄG“  æ …§øõÅµÄ\Å]Ä\ÄG”   ∆» °«Å∑°…Å∏Äe_DeltaEditorLayoutExtension”   Õ– ¢7œÄaÅπ¢¨“ÄÑÅ∫Äe^documentLength˛”   ◊⁄ ¢HŸÄhÅº¢¨“ÄÑÅ∫Äe^documentLength”   ‡Î 0™·‚„‰ÂÊÁËÈÍÅæÅøÅ¿Å¡Å¬Å√ÅƒÅ≈Å∆Å«™ÏÙ &Å»Ä]Ä]Ä]ÅÀÄfÄ\Ä]Å–ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°Å…ÄG“   …§Å Å Å Å ÄG”    °ÅÃ°ÅÕÄe_DeltaEditorLayoutExtension”    ¢7ÅŒÄa¢¨ÅœÄÑÄe^documentLength”   " ¢H!ÄhÅ—¢¨ÄÑÅœÄe^documentLength”   (3 0™)*+,-./012Å”Å‘Å’Å÷Å◊ÅÿÅŸÅ⁄Å€Å‹™489:< &Å›Ä]Ä]Ä]Å‡ÅÂÅÊÄ]ÅÁÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  J …°KÅﬁÄG“  N …§{P{PÅ	ÅﬂÅ	ÅﬂÄG”   VX °WÅ·°YÅ‚Äe_DeltaEditorLayoutExtension”   ]` ¢7_ÄaÅ„¢¨bÄÑÅ‰Äe^documentLength#@*      ”   il ¢HkÄhÅË¢¨bÄÑÅ‰Äe^documentLength”   r} 0™stuvwxyz{|ÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚÅÛ™~ÇÜ &ÅÙÄ]Ä]Ä]Å˜ÄfÄ\Ä]Å¸ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  î …°ïÅıÄG“  ò …§ôƒôƒÅˆÅÅˆÅÄG#”   †¢ °°Å¯°£Å˘Äe_DeltaEditorLayoutExtension”   ß™ ¢7©ÄaÅ˙¢¨¨ÄÑÅ˚Äe^documentLength≥”   ±¥ ¢H≥ÄhÅ˝¢¨¨ÄÑÅ˚Äe^documentLength”   ∫≈ 0™ªºΩæø¿¡¬√ƒÅˇÅ ÅÅÅÅÅÅÅÅ™∆ 9ÃŒ &Å	Ä]Ä]Ä]ÅÅÂÅÄ]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ‹ …°›Å
ÄG“  ‡ …§··ÅÄ\ÅÄ\ÄGØ”   ËÍ °ÈÅ°ÎÅÄe_DeltaEditorLayoutExtension”   ÔÚ ¢7ÒÄaÅ¢¨ÙÄÑÅÄe^documentLengthÄj”   ˙˝ ¢H¸ÄhÅ¢¨ÙÄÑÅÄe^documentLength”    0´	
ÅÅÅÅÅÅÅÅÅÅÅ´ &Å Ä]Ä]Ä]Å$Å)Å*Å0Ä]Å1ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ( …°)Å!ÄG“  , …§S/0ÅHÄ\Å"Å#ÄG D”   57 °6Å%°8Å&Äe_DeltaEditorLayoutExtension”   <? ¢=7Å'Äa¢@¨Å(ÄÑÄe^documentLengtht#@&      ”   GK 0£HIJÅ+Å,Å-£LNÅ.Ä]Å/Ä+_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_XcodePreviews.PinnedPreview#?      P”   WZ ¢HYÄhÅ2¢¨@ÄÑÅ(Äe^documentLength”   `k 0™abcdefghijÅ4Å5Å6Å7Å8Å9Å:Å;Å<Å=™lpqrt &Å>Ä]Ä]Ä]ÅAÅFÅGÄ]ÅHÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ç …°ÉÅ?ÄG“  Ü …§ááÅ@Ä\Å@Ä\ÄG”   éê °èÅB°ëÅCÄe_DeltaEditorLayoutExtension”   ïò ¢7óÄaÅD¢¨öÄÑÅEÄe^documentLength…#@      ”   °§ ¢H£ÄhÅI¢¨öÄÑÅEÄe^documentLength”   ™µ 0™´¨≠ÆØ∞±≤≥¥ÅKÅLÅMÅNÅOÅPÅQÅRÅSÅT™∂∫æ &ÅUÄ]Ä]Ä]ÅXÄfÄ\Ä]Å]ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ã …°ÕÅVÄG“  – …§—ø—√ÅWÅµÅWÅÄG	”   ÿ⁄ °ŸÅY°€ÅZÄe_DeltaEditorLayoutExtension”   ﬂ‚ ¢‡7Å[Äa¢„¨Å\ÄÑÄe^documentLengthΩ”   ÈÏ ¢HÎÄhÅ^¢¨„ÄÑÅ\Äe^documentLength”   Ú˝ 0™ÛÙıˆ˜¯˘˙˚¸Å`ÅaÅbÅcÅdÅeÅfÅgÅhÅi™˛ &ÅjÄ]Ä]Ä]ÅlÅqÄ\Ä]ÅrÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°ÅkÄG“   …§√_√_ÅÅ•ÅÅ•ÄG”   ! ° Åm°"ÅnÄe_DeltaEditorLayoutExtension”   &) ¢'7ÅoÄa¢*¨ÅpÄÑÄe^documentLengthD#        ”   14 ¢H3ÄhÅs¢¨*ÄÑÅpÄe^documentLength”   :E 0™;<=>?@ABCDÅuÅvÅwÅxÅyÅzÅ{Å|Å}Å~™FJKƒN &ÅÄ]Ä]Ä]ÅÇÅáÅÄ]ÅàÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  \ …°]ÅÄÄG“  ` …§aaÅÅÄ\ÅÅÄ\ÄG”   hj °iÅÉ°kÅÑÄe_DeltaEditorLayoutExtension”   or ¢p7ÅÖÄa¢s¨ÅÜÄÑÄe^documentLength#@      ”   z} ¢H|ÄhÅâ¢¨sÄÑÅÜÄe^documentLength”   Éé 0™ÑÖÜáàâäãåçÅãÅåÅçÅéÅèÅêÅëÅíÅìÅî™èìó &ÅïÄ]Ä]Ä]ÅôÄfÄ\Ä]ÅûÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  • …°¶ÅñÄG“  © …§™¨rÅóÄ\ÅòÅGÄG8>”   ≤¥ °≥Åö°µÅõÄe_DeltaEditorLayoutExtension”   πº ¢7ªÄaÅú¢¨æÄÑÅùÄe^documentLength’”   √∆ ¢H≈ÄhÅü¢¨æÄÑÅùÄe^documentLength”   Ã◊ 0™ÕŒœ–—“”‘’÷Å°Å¢Å£Å§Å•Å¶ÅßÅ®Å©Å™™ÿ‹‡ &Å´Ä]Ä]Ä]Å≠ÄfÄ\Ä]Å≤ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ó …°ÔÅ¨ÄG“  Ú …§øúøúÅµÅ^ÅµÅ^ÄG”   ˘˚ °˙ÅÆ°¸ÅØÄe_DeltaEditorLayoutExtension”   	 	 ¢7	ÄaÅ∞¢¨	ÄÑÅ±Äe^documentLength”   	
	 ¢H	ÄhÅ≥¢¨	ÄÑÅ±Äe^documentLength”   		 0™										ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩÅæ™		#	' &ÅøÄ]Ä]Ä]Å¬ÄfÄ\Ä]Å«ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	5 …°	6Å¿ÄG“  	9 …§	:	:Å¡Ä\Å¡Ä\ÄG”   	A	C °	BÅ√°	DÅƒÄe_DeltaEditorLayoutExtension”   	H	K ¢	I7Å≈Äa¢	L¨Å∆ÄÑÄe^documentLengthg”   	R	U ¢H	TÄhÅ»¢¨	LÄÑÅ∆Äe^documentLength”   	[	f 0™	\	]	^	_	`	a	b	c	d	eÅ ÅÀÅÃÅÕÅŒÅœÅ–Å—Å“Å”™	g	k	o &Å‘Ä]Ä]Ä]Å◊ÄfÄ\Ä]Å‹ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	} …°	~Å’ÄG“  	Å …§ú	Éú	ÉÅ^Å÷Å^Å÷ÄG”   	â	ã °	äÅÿ°	åÅŸÄe_DeltaEditorLayoutExtension”   	ê	ì ¢	ë7Å⁄Äa¢	î:Å€ÄcÄe^documentLength”   	ö	ù ¢H	úÄhÅ›¢:	îÄcÅ€Äe^documentLength”   	£	Æ 0™	§	•	¶	ß	®	©	™	´	¨	≠ÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁÅË™	Ø	≥	∑ &ÅÈÄ]Ä]Ä]ÅÎÄfÄ\Ä]ÅÔÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	≈ …°	∆ÅÍÄG“  	… …§	:	:Å¡Ä\Å¡Ä\ÄG”   	–	“ °	—ÅÏ°	”ÅÌÄe_DeltaEditorLayoutExtension”   	◊	⁄ ¢	ÿ7ÅÓÄa¢	L¨Å∆ÄÑÄe^documentLength”   	‡	„ ¢H	‚ÄhÅ¢¨	LÄÑÅ∆Äe^documentLength”   	È	Ù 0™	Í	Î	Ï	Ì	Ó	Ô		Ò	Ú	ÛÅÚÅÛÅÙÅıÅˆÅ˜Å¯Å˘Å˙Å˚™	ı	˘	˝ &Å¸Ä]Ä]Ä]Å˛ÄfÄ\Ä]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
 …°
Å˝ÄG“  
 …§õ:õ:Å]ÅÊÅ]ÅÊÄG”   

 °
Åˇ°
Å Äe_DeltaEditorLayoutExtension”   

  ¢
7ÅÄa¢
!¨ÅÄÑÄe^documentLengthÛ”   
'
* ¢H
)ÄhÅ¢¨
!ÄÑÅÄe^documentLength”   
0
; 0™
1
2
3
4
5
6
7
8
9
:ÅÅÅÅ	Å
ÅÅÅÅÅ™
<
@
D &ÅÄ]Ä]Ä]ÅÄfÄ\Ä]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
R …°
SÅÄG“  
V …§	:	:Å¡Ä\Å¡Ä\ÄG”   
]
_ °
^Å°
`ÅÄe_DeltaEditorLayoutExtension”   
d
g ¢
e7ÅÄa¢
h¨ÅÄÑÄe^documentLengthÅ”   
n
q ¢H
pÄhÅ¢¨
hÄÑÅÄe^documentLength”   
w
Ç 0™
x
y
z
{
|
}
~

Ä
ÅÅÅÅÅÅÅÅ Å!Å"Å#™
É
á
ã &Å$Ä]Ä]Ä]Å'ÄfÄ\Ä]Å,ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
ô …°
öÅ%ÄG“  
ù …§
ûƒ
ûƒÅ&ÅÅ&ÅÄG”   
•
ß °
¶Å(°
®Å)Äe_DeltaEditorLayoutExtension”   
¨
Ø ¢
≠7Å*Äa¢
∞¨Å+ÄÑÄe^documentLengthÖ”   
∂
π ¢H
∏ÄhÅ-¢¨
∞ÄÑÅ+Äe^documentLength”   
ø
  0™
¿
¡
¬
√
ƒ
≈
∆
«
»
…Å/Å0Å1Å2Å3Å4Å5Å6Å7Å8™
À
œ
–
—
” &Å9Ä]Ä]Ä]Å<ÅAÅBÄ]ÅCÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
· …°
‚Å:ÄG“  
Â …§
ÊP
ÊPÅ;ÅﬂÅ;ÅﬂÄG/”   
Ì
Ô °
ÓÅ=°
Å>Äe_DeltaEditorLayoutExtension”   
Ù
˜ ¢
ı7Å?Äa¢
¯¨Å@ÄÑÄe^documentLength#@       ”     ¢HÄhÅD¢¨
¯ÄÑÅ@Äe^documentLength”   	 0™
ÅFÅGÅHÅIÅJÅKÅLÅMÅNÅO™ &ÅPÄ]Ä]Ä]ÅSÅXÅYÄ]ÅZÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  + …°,ÅQÄG“  / …§—2ƒÅWÄ\ÅRÅÄG'”   79 °8ÅT°:ÅUÄe_DeltaEditorLayoutExtension”   >A ¢?7ÅVÄa¢B¨ÅWÄÑÄe^documentLengthä#@      ”   JM ¢HLÄhÅ[¢¨BÄÑÅWÄe^documentLength”   S^ 0™TUVWXYZ[\]Å]Å^Å_Å`ÅaÅbÅcÅdÅeÅf™_cLrg &ÅgÄ]Ä]Ä]ÅjÅ.ÅGÄ]ÅoÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  u …°vÅhÄG“  y …§—|ÅWÄ\ÅiÄ\ÄG
”   ÅÉ °ÇÅk°ÑÅlÄe_DeltaEditorLayoutExtension”   àã ¢â7ÅmÄa¢å¨ÅnÄÑÄe^documentLengthﬂ”   íï ¢HîÄhÅp¢¨åÄÑÅnÄe^documentLength”   õ¶ 0™úùûü†°¢£§•ÅrÅsÅtÅuÅvÅwÅxÅyÅzÅ{™ß´Ø &Å|Ä]Ä]Ä]Å~ÄfÄ\Ä]ÅÉÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ω …°æÅ}ÄG“  ¡ …§øøø:ÅµÅµÅµÅÊÄG”   »  °…Å°ÀÅÄÄe_DeltaEditorLayoutExtension”   œ“ ¢–7ÅÅÄa¢”¨ÅÇÄÑÄe^documentLengthÀ”   Ÿ‹ ¢H€ÄhÅÑ¢¨”ÄÑÅÇÄe^documentLength”   ‚Ì 0™„‰ÂÊÁËÈÍÎÏÅÜÅáÅàÅâÅäÅãÅåÅçÅéÅè™ÓÚÙˆ &ÅêÄ]Ä]Ä]ÅìÅXÅòÄ]ÅôÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°ÅëÄG“   …§	ƒ	ƒÅíÅÅíÅÄG”    °Åî°ÅïÄe_DeltaEditorLayoutExtension”    ¢7ÅñÄa¢¨ÅóÄÑÄe^documentLength”   "% ¢H$ÄhÅö¢¨ÄÑÅóÄe^documentLength”   +6 0™,-./012345ÅúÅùÅûÅüÅ†Å°Å¢Å£Å§Å•™7;? &Å¶Ä]Ä]Ä]Å®ÄfÄ\Ä]Å≠ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  M …°NÅßÄG“  Q …§
û
ûÅ Å&Å Å&ÄG”   XZ °YÅ©°[Å™Äe_DeltaEditorLayoutExtension”   _b ¢7aÄaÅ´¢¨dÄÑÅ¨Äe^documentLengthk”   il ¢HkÄhÅÆ¢¨dÄÑÅ¨Äe^documentLength”   r} 0™stuvwxyz{|Å∞Å±Å≤Å≥Å¥ÅµÅ∂Å∑Å∏Åπ™~ÇÜ &Å∫Ä]Ä]Ä]ÅºÅqÄ\Ä]Å¿ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  î …°ïÅªÄG“  ò …§Ä\Ä\Ä\Ä\ÄG”   ü° °†ÅΩ°¢ÅæÄe_DeltaEditorLayoutExtension”   ¶© ¢ß7ÅøÄa¢¨¨Å˚ÄÑÄe^documentLength”   Ø≤ ¢H±ÄhÅ¡¢¨¨ÄÑÅ˚Äe^documentLength”   ∏√ 0™π∫ªºΩæø¿¡¬Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀÅÃ™ƒ»…øÃ &ÅÕÄ]Ä]Ä]Å–Å’ÅµÄ]Å÷ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ⁄ …°€ÅŒÄG“  ﬁ …§ﬂﬂÅœÄ\ÅœÄ\ÄG1”   ÊË °ÁÅ—°ÈÅ“Äe_DeltaEditorLayoutExtension”   Ì ¢Ó7Å”Äa¢Ò¨Å‘ÄÑÄe^documentLengths#@"      ”   ¯˚ ¢H˙ÄhÅ◊¢¨ÒÄÑÅ‘Äe^documentLength”    0™	
ÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂÅ‡Å·Å‚™L, &Å„Ä]Ä]Ä]ÅÂÅ.ÅàÄ]ÅÍÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  # …°$Å‰ÄG“  ' …§vav/ÅûÅÅÅûÅ"ÄG”   .0 °/ÅÊ°1ÅÁÄe_DeltaEditorLayoutExtension”   58 ¢67ÅËÄa¢9:ÅÈÄcÄe^documentLength”   ?B ¢HAÄhÅÎ¢:9ÄcÅÈÄe^documentLength”   HT 0´IJKLMNOPQRSÅÌÅÓÅÔÅÅÒÅÚÅÛÅÙÅıÅˆÅ˜´UY[^ &Å¯Ä]Ä]Ä]Å˙ÅqÅˇÄ\Ä]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  m …°nÅ˘ÄG“  q …§Ä\Ä\Ä\Ä\ÄG”   xz °yÅ˚°{Å¸Äe_DeltaEditorLayoutExtension”   Ç ¢Ä7Å˝Äa¢É¨Å˛ÄÑÄe^documentLengthÊ”   âç 0£äãåÅ ÅÅ£LNÅ.Ä]Å/Ä+_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_XcodePreviews.PinnedPreview”   ñô ¢HòÄhÅ¢¨ÉÄÑÅ˛Äe^documentLength”   ü™ 0™†°¢£§•¶ß®©ÅÅÅÅ	Å
ÅÅÅÅÅ™´Ø≥ &ÅÄ]Ä]Ä]ÅÄfÄ\Ä]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ¡ …°¬ÅÄG“  ≈ …§
—õ
—õÅBÅ]ÅBÅ]ÄG”   ÃŒ °ÕÅ°œÅÄe_DeltaEditorLayoutExtension”   ”÷ ¢‘7ÅÄa¢◊¨ÅÄÑÄe^documentLengthÙ”   ›‡ ¢HﬂÄhÅ¢¨◊ÄÑÅÄe^documentLength”   ÊÒ 0™ÁËÈÍÎÏÌÓÔÅÅÅÅÅÅÅ Å!Å"Å#™Úˆ˙ &Å$Ä]Ä]Ä]Å'ÄfÄ\Ä]Å,ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°	Å%ÄG“   …§rrÅ&ÅGÅ&ÅGÄG”    °Å(°Å)Äe_DeltaEditorLayoutExtension”    ¢7ÄaÅ*¢¨ ÄÑÅ+Äe^documentLengthÃ”   %( ¢H'ÄhÅ-¢¨ ÄÑÅ+Äe^documentLength”   .9 0™/012345678Å/Å0Å1Å2Å3Å4Å5Å6Å7Å8™:>
–&B &Å9Ä]Ä]Ä]Å;ÅAÄ[Ä]Å@ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  P …°QÅ:ÄG“  T …§√õ√õÅÅ]ÅÅ]ÄG”   [] °\Å<°^Å=Äe_DeltaEditorLayoutExtension”   be ¢7dÄaÅ>¢¨gÄÑÅ?Äe^documentLength˝”   lo ¢HnÄhÅA¢¨gÄÑÅ?Äe^documentLength”   uÄ 0™vwxyz{|}~ÅCÅDÅEÅFÅGÅHÅIÅJÅKÅL™ÅÖÜâ &ÅMÄ]Ä]Ä]ÅPÅUÄ\Ä]ÅVÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ó …°òÅNÄG“  õ …§SùSùÅHÅOÅHÅOÄG”   £• °§ÅQ°¶ÅRÄe_DeltaEditorLayoutExtension”   ™≠ ¢´7ÅSÄa¢Æ:ÅTÄcÄe^documentLength6#@       ”   µ∏ ¢H∑ÄhÅW¢:ÆÄcÅTÄe^documentLength”   æ  0´ø¿¡¬√ƒ≈∆«»…ÅYÅZÅ[Å\Å]Å^Å_Å`ÅaÅbÅc´Àœ—‘ &ÅdÄ]Ä]Ä]ÅfÅqÅjÄ\Ä]ÅnÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  „ …°‰ÅeÄG“  Á …§Ä\Ä\Ä\Ä\ÄG”   Ó °ÔÅg°ÒÅhÄe_DeltaEditorLayoutExtension”   ı¯ ¢ˆ7ÅiÄa¢É¨Å˛ÄÑÄe^documentLength”   ˛ 0£ˇ ÅkÅlÅm£NLÅ/Ä]Å.Ä+_XcodePreviews.PinnedPreview_XcodePreviews.ShowViewBounds_XcodePreviews.Zoom”    ¢HÄhÅo¢¨ÉÄÑÅ˛Äe^documentLength”    0™ÅqÅrÅsÅtÅuÅvÅwÅxÅyÅz™ $( &Å{Ä]Ä]Ä]Å}ÄfÄ\Ä]ÅÇÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  6 …°7Å|ÄG“  : …§ø:ø:ÅµÅÊÅµÅÊÄG”   AC °BÅ~°DÅÄe_DeltaEditorLayoutExtension”   HK ¢I7ÅÄÄa¢L¨ÅÅÄÑÄe^documentLengthU”   RU ¢HTÄhÅÉ¢¨LÄÑÅÅÄe^documentLength”   [f 0™\]^_`abcdeÅÖÅÜÅáÅàÅâÅäÅãÅåÅçÅé™gko &ÅèÄ]Ä]Ä]ÅëÄfÄ\Ä]ÅñÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  } …°~ÅêÄG“  Å …§Ä\Ä\Ä\Ä\ÄG”   àä °âÅí°ãÅìÄe_DeltaEditorLayoutExtension”   èí ¢7ëÄaÅî¢¨îÄÑÅïÄe^documentLengthk”   ôú ¢HõÄhÅó¢¨îÄÑÅïÄe^documentLength”   ¢≠ 0™£§•¶ß®©™´¨ÅôÅöÅõÅúÅùÅûÅüÅ†Å°Å¢™Æ≤∂ &Å£Ä]Ä]Ä]Å¶ÄfÄ\Ä]Å´ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ƒ …°≈Å§ÄG“  » …§……Å•Ä\Å•Ä\ÄG,”   –“ °—Åß°”Å®Äe_DeltaEditorLayoutExtension”   ◊⁄ ¢ÿ7Å©Äa¢€¨Å™ÄÑÄe^documentLengthF”   ·‰ ¢H„ÄhÅ¨¢¨€ÄÑÅ™Äe^documentLength”   Íı 0™ÎÏÌÓÔÒÚÛÙÅÆÅØÅ∞Å±Å≤Å≥Å¥ÅµÅ∂Å∑™ˆ˙
–˛ &Å∏Ä]Ä]Ä]Å∫ÅAÅ0Ä]ÅøÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°ÅπÄG“   …§+ú+úÅáÅ^ÅáÅ^ÄG”    °Åª°ÅºÄe_DeltaEditorLayoutExtension”   ! ¢7 ÄaÅΩ¢:#ÄcÅæÄe^documentLength©”   (+ ¢H*ÄhÅ¿¢:#ÄcÅæÄe^documentLength”   1< 0™23456789:;Å¬Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀ™=AE &ÅÃÄ]Ä]Ä]ÅŒÅqÄ\Ä]Å”ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  S …°TÅÕÄG“  W …§Ä\Ä\Ä\Ä\ÄG”   ^` °_Åœ°aÅ–Äe_DeltaEditorLayoutExtension”   eh ¢7gÄaÅ—¢¨jÄÑÅ“Äe^documentLength>Û”   or ¢HqÄhÅ‘¢¨jÄÑÅ“Äe^documentLength”   xÉ 0™yz{|}~ÄÅÇÅ÷Å◊ÅÿÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂ™Ñàå &Å‡Ä]Ä]Ä]Å„ÄfÄ\Ä]ÅËÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ö …°õÅ·ÄG“  û …§üüÅ‚Ä\Å‚Ä\ÄGz”   ¶® °ßÅ‰°©ÅÂÄe_DeltaEditorLayoutExtension”   ≠∞ ¢7ØÄaÅÊ¢¨≤ÄÑÅÁÄe^documentLengthË”   ∑∫ ¢HπÄhÅÈ¢¨≤ÄÑÅÁÄe^documentLength”   ¿À 0™¡¬√ƒ≈∆«»… ÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚÅÛÅÙ™Ã–‘ &ÅıÄ]Ä]Ä]Å˜ÄfÄ\Ä]Å¸ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ‚ …°„ÅˆÄG“  Ê …§Ä\Ä\Ä\Ä\ÄG”   ÌÔ °ÓÅ¯°Å˘Äe_DeltaEditorLayoutExtension”   Ù˜ ¢ı7Å˙Äa¢¯¨Å˚ÄÑÄe^documentLengthÔ”   ˛ ¢H ÄhÅ˝¢¨¯ÄÑÅ˚Äe^documentLength”    0™	
ÅˇÅ ÅÅÅÅÅÅÅÅ™ &Å	Ä]Ä]Ä]ÅÄfÄ\Ä]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ) …°*Å
ÄG“  - …§ÅYÄ\ÅYÄ\ÄG”   46 °5Å°7ÅÄe_DeltaEditorLayoutExtension”   ;> ¢<7ÅÄa¢?¨ÅÄÑÄe^documentLength‰”   EH ¢HGÄhÅ¢¨?ÄÑÅÄe^documentLength”   NY 0™OPQRSTUVWXÅÅÅÅÅÅÅÅÅÅ™Z^
–Ùb &ÅÄ]Ä]Ä]ÅÅAÅòÄ]Å#ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  p …°qÅÄG“  t …§	ƒ	ƒÅíÅÅíÅÄG”   {} °|Å °~Å!Äe_DeltaEditorLayoutExtension”   ÇÖ ¢É7Å"Äa¢¨ÅóÄÑÄe^documentLength”   ãé ¢HçÄhÅ$¢¨ÄÑÅóÄe^documentLength”   îü 0™ïñóòôöõúùûÅ&Å'Å(Å)Å*Å+Å,Å-Å.Å/™†§^¶® &Å0Ä]Ä]Ä]Å3Å§Å8Ä]Å9ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∂ …°∑Å1ÄG“  ∫ …§ª	:ª	:Å2Å¡Å2Å¡ÄGy”   ¬ƒ °√Å4°≈Å5Äe_DeltaEditorLayoutExtension”   …Ã ¢7ÀÄaÅ6¢¨ŒÄÑÅ7Äe^documentLengthÙA”   ‘◊ ¢H÷ÄhÅ:¢¨ŒÄÑÅ7Äe^documentLength”   ›Ë 0™ﬁﬂ‡·‚„‰ÂÊÁÅ<Å=Å>Å?Å@ÅAÅBÅCÅDÅE™ÈÌÒ &ÅFÄ]Ä]Ä]ÅIÄfÄ\Ä]ÅNÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ˇ …° ÅGÄG“   …§	:	:ÅHÅ¡ÅHÅ¡ÄG)”    °ÅJ°ÅKÄe_DeltaEditorLayoutExtension”    ¢7ÄaÅL¢:ÄcÅMÄe^documentLength¨”    ¢HÄhÅO¢:ÄcÅMÄe^documentLength”   %0 0™&'()*+,-./ÅQÅRÅSÅTÅUÅVÅWÅXÅYÅZ™15
–	9 &Å[Ä]Ä]Ä]Å^ÅAÅíÄ]ÅcÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  G …°HÅ\ÄG“  K …§LPLPÅ]ÅﬂÅ]ÅﬂÄG2”   SU °TÅ_°VÅ`Äe_DeltaEditorLayoutExtension”   Z] ¢7\ÄaÅa¢¨_ÄÑÅbÄe^documentLengthC”   dg ¢HfÄhÅd¢¨_ÄÑÅbÄe^documentLength”   mx 0™nopqrstuvwÅfÅgÅhÅiÅjÅkÅlÅmÅnÅo™y}Å &ÅpÄ]Ä]Ä]ÅrÄfÄ\Ä]ÅwÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  è …°êÅqÄG“  ì …§
ûá
ûáÅ&Å@Å&Å@ÄG”   öú °õÅs°ùÅtÄe_DeltaEditorLayoutExtension”   °§ ¢¢7ÅuÄa¢•¨ÅvÄÑÄe^documentLength”   ´Æ ¢H≠ÄhÅx¢¨•ÄÑÅvÄe^documentLength”   ¥ø 0™µ∂∑∏π∫ªºΩæÅzÅ{Å|Å}Å~ÅÅÄÅÅÅÇÅÉ™¿ƒ» &ÅÑÄ]Ä]Ä]ÅáÄfÄ\Ä]ÅåÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ÷ …°◊ÅÖÄG“  ⁄ …§|‹|‹ÅiÅÜÅiÅÜÄG”   ‚‰ °„Åà°ÂÅâÄe_DeltaEditorLayoutExtension”   ÈÏ ¢7ÎÄaÅä¢¨ÓÄÑÅãÄe^documentLengthÑ”   Ûˆ ¢HıÄhÅç¢¨ÓÄÑÅãÄe^documentLength”   ¸ 0™˝˛ˇ ÅèÅêÅëÅíÅìÅîÅïÅñÅóÅò™^ &ÅôÄ]Ä]Ä]ÅõÅ§Å†Ä]Å°ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   …°ÅöÄG“  " …§……Å0Å•Å0Å•ÄG”   )+ °*Åú°,ÅùÄe_DeltaEditorLayoutExtension”   03 ¢17ÅûÄa¢4¨ÅüÄÑÄe^documentLength!¸
”   ;> ¢H=ÄhÅ¢¢¨4ÄÑÅüÄe^documentLength”   DO 0™EFGHIJKLMNÅ§Å•Å¶ÅßÅ®Å©Å™Å´Å¨Å≠™PTX &ÅÆÄ]Ä]Ä]Å∞ÄfÄ\Ä]ÅµÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  f …°gÅØÄG“  j …§√√ÅÄ\ÅÄ\ÄG”   qs °rÅ±°tÅ≤Äe_DeltaEditorLayoutExtension”   x{ ¢7zÄaÅ≥¢¨}ÄÑÅ¥Äe^documentLength„”   ÇÖ ¢HÑÄhÅ∂¢¨}ÄÑÅ¥Äe^documentLength”   ãñ 0™åçéèêëíìîïÅ∏ÅπÅ∫ÅªÅºÅΩÅæÅøÅ¿Å¡™óõü &Å¬Ä]Ä]Ä]ÅƒÄfÄ\Ä]Å…ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ≠ …°ÆÅ√ÄG“  ± …§SƒSƒÅHÅÅHÅÄG”   ∏∫ °πÅ≈°ªÅ∆Äe_DeltaEditorLayoutExtension”   ø¬ ¢7¡ÄaÅ«¢¨ƒÄÑÅ»Äe^documentLength”   …Ã ¢HÀÄhÅ ¢¨ƒÄÑÅ»Äe^documentLength”   “÷ £”‘’ÅÃÅÕÅŒ£◊ÿŸÅœÅ”Å◊Äe_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKey”   ﬂ· 0°‡Å–°‚Å—Ä+[TheMealsApp“Ê ÁËWNS.time#Aƒö˛G"6Å““ ] ^ÍÎVNSDate¢Í a”   ÌÔ 0°ÓÅ‘°Å’Ä+[TheMealsApp“ ÙıˆYNS.stringÅ÷_;76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9_iphonesimulator_x86_64“ ] ^¯˘_NSMutableString£¯˙ aXNSString”   ¸˛ 0°˝Åÿ°ˇÅŸÄ+_;76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9_iphonesimulator_x86_64“Ê Ë#Aƒö˛G[-<Å“”    °Å€°	Å‹Äe]IDENameString[TheMealsApp”    ™ÅﬁÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁ™ & & !"#ÄÅËÄÄ]ÅÈÅÍÅÎÅÏÅÌÅÓÄeZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:76B5EDCD-AA06-4DD3-8715-3EA44C79C5F9Vx86_64_iphonesimulator16.1ViPhoneZiPhone15,2”   7< 0§89:;ÅÅÒÅÚÅÛ§=>?@ÅÙÅıÅˆÅ˜Ä+_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildV14B47bÍ“  JN£KLMÅ¯Å˝ÅÅ”   PT 0£QRSÅ˘Å˙Å˚£
–V=ÅAÅ¸ÅÙÄ+_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ ”   ^b 0£QRSÅ˘Å˙Å˚£LdeÅ.Å˛ÅˇÄ+c  %  “i jkWNS.dataObplist00‘
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—	TrootÄØ)*0:;<#=AIJKLMSWX\_U$null”XNSStringV$class\NSAttributesÄÄÄYSucceeded”WNS.keysZNS.objects°Ä°ÄÄVNSFont÷ !"#$%&'(VNSSizeXNSfFlags\NSDescriptorZNSHasWidthVNSName#@&      àÄÄÄ_.AppleSystemUIFontBold”+,-./_NSFontDescriptorOptions_NSFontDescriptorAttributesÄÄÄÄ”15£234Ä	Ä
Ä£678ÄÄÄÄ_NSFontSizeAttribute_ NSCTFontFeatureSettingsAttribute_NSCTFontUIUsageAttribute“>@°?ÄÄ”BE¢CDÄÄ¢FGÄÄÄ_CTFeatureSelectorIdentifier_CTFeatureTypeIdentifier “NOPQZ$classnameX$classes\NSDictionary¢PRXNSObject“NOTU^NSMutableArray£TVRWNSArray_CTFontBoldUsage“NOYZ_NSFontDescriptor¢[R_NSFontDescriptor“NO]^VNSFont¢]R“NO`a_NSAttributedString¢bR_NSAttributedString    $ ) 2 7 I L Q S o u | Ö å ô õ ù ü © ∞ ∏ √ ≈ « … À Õ ‘ · Ë Ò ˛	!#<C]z|ÅÉäéêíîòöúû†∂ŸÙ˘˚˝ˇ	4NPRWbkx{Ñâòú§∂ªŒ—‰ÈÛ¯             c              %Å “ ] ^mn]NSMutableData£mo aVNSData”   qx 0¶QstSvwÅ˘ÅÅÅ˚ÅÅ¶^ƒ{|ƒƒÅ§ÅÅÅÅÅÄ+_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle“Ê ÖË#Aƒõºˆ¨Å“^Today at 16.08“ ] ^âä^NSMutableArray£â Ê a“  å …° ÄÄG”   êí 0° Ä°ìÅÄ+“  ñN° ÄÅ“  öN†Å“  ù …™ QÊ·‹ Ô¿ Ó∫œÌÄÄﬂÄ’ÄÀÄLÄìÄJÄáÄ±ÄÌÄG”   ™≤ 0ß´¨≠ÆØ∞±ÅÅÅÅÅÅÅß &¥∂∞∏ÄÅÄ]ÅÅÅÄ]Ä+_IDEWindowToolbarIsVisible^IDEWindowFrame_&IDEWindowTabBarWasVisibleWithSingleTab_(IDEWorkspaceWindow_NSRestorableStateData_IDEActiveWorkspaceTabController_>IDEWorkspaceTabController_933AE80D-5978-481C-9637-73C7D8929EFE_IDEWindowTabBarIsVisible_{{1679, 30}, {1322, 1025}}“i ƒkO4bplist00‘0X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiverﬂ	
 !"#$%'()+,-./[NSStyleMask]NSWindowFrame_NSFirstResponder_NSManagedFrameIsUserDefined_&NSWindowManagementPersistentIdentifier__NSWindowLayouts^NSWindowNumber__NSWindowRestorer_NSUnmanagedWindowFrame2WNSTitleWNSIsKey__NSWindowResizeGenerationXNSTabIdx_NSWindowWorkspaceIDXNSIsMain[NSClassNameVNSUIID_NSUIPreviousFrameZNSTabBrVisZNSTabGrpIDÄÄÄ	ÄÄyÄÄ Ä	 Ä	ÄÄÄ(Ä'Ø)123456789@AGU[\]`defjklpqryz{~ÉÑÖäãåëíìñóU$null_$B24954C0-1B4E-4D32-BD8B-EF32C5F4CAA9_#1679 30 1322 1025 1680 0 1920 1055 U_NS:8o T h e M e a l s A p p      A s s e t s . x c a s s e t s_IDEWorkspaceWindow_$A370BEB6-8D1B-4CD5-83E3-A4BB8517370B_IDEDocumentController”:;<=>?V$classXwindowID_persistentIdentifierÄ
¥Ä	V_NS:92“BCDEZ$classnameX$classes_NSPersistentUIEncodedReference¢DFXNSObject”HI:JOTWNS.keysZNS.objects§KLMNÄÄÄÄ§PQRSÄÄÄ Ä#Ä&”VW:XYZ_NSScreenLayoutUUIDString_NSScreenLayoutSizeÄÄÄ_$F2B5A86A-9901-8E02-AF53-20B8D78B142E\{1920, 1080}“BC^_^NSScreenLayout¢^F”VW:abZÄÄÄ_$85A2805F-F0DD-0306-8C8C-4F1AD0C2EEC5\{1920, 1080}”VW:ghZÄÄÄ_$6FAF45A8-9789-0F06-22FA-018734C0F4CF\{1680, 1050}”VW:mnZÄÄÄ_$42591195-F398-F010-A125-104918E150C1\{1920, 1080}‘st:uv'wx_NSWindowLayoutScreenLayoutFrame_NSWindowLayoutResizeGeneration_NSWindowLayoutWindowFrameÄÄÄ_{{1679, 30}, {1322, 1025}}_{{1680, 0}, {1920, 1055}}“BC|}^NSWindowLayout¢|F‘st:uÄwÇÄÄÄ_{{1680, -62}, {1920, 1055}}_{{1680, -62}, {1920, 1055}}‘st:uÜáwâÄ"ÄÄ!_{{0, 0}, {1680, 1025}}_{{0, 0}, {1680, 1025}}‘st:uçéwêÄ%ÄÄ$_{{2505, 163}, {1078, 635}}_{{1680, -62}, {1920, 1055}}“BCîï\NSDictionary¢îF^0x60000184f2a0_0 0 1680 1025 0 0 1680 1025     $ ) 2 7 I t Ä é ° ø Ë ˚
8@HdmÉåòü≥æ…ÃŒ–—”’ÿ⁄‹ﬁﬂ·„ÂÊËÍÏÌÔ!Hnt±∆Ì3579@EPYz}Üçï†•ß©´≠≤¥∂∏∫º√ﬁÛı˜˘ -2ADKMOQxÖåéêíπ∆Õœ—”˙2SoqsuíÆ≥¬≈Œ–“‘÷Ù!#<U^`bdfÉ°¶≥∂≈             ò              ‰Å ”   «— 0©»… ÀÃÕŒœ–ÅÅÅÅÅÅÅÅ Å!© &”‘’÷◊ÿ &⁄ÄÅ"ÅrÅsÅwÅxÅzÄÅ}Ä+_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorArea”   ÊÌ 0¶ÁËÈÍÎÏÅ#Å$Å%Å&Å'Å(¶ÓÔÒÚÛÅ)Å5ÅGÅYÅZÅaÄ+_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Find_Xcode.IDEKit.Navigator.Debug_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigator”   ¸ 0§˝˛ˇ Å*Å+Å,Å-§Å.Å/Å0Å.Ä+_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey”    ††Äe_IDENavigatorModeSolitary”    °Å1°Å2Äe_IDENavigatorModeSolitary“ _codablePlistRepresentationÅ4Å3OÕbplist00‘     îïúYitemState_lastAccessedDate^scrollPosition]selectedItemsØh      # ' * . 3 7 ; ? B F L P T W [ _ d h k o r v z ~ Ç Ü â ç ë ï ò ú † § ® ¨ Ø ≥ ∂ ∫ ø √ ∆   Œ “ ’ Ÿ › · ‰ Ë Î Ô Ú ˆ ˘ ˝ "%),037:>AEIMPTX\`dgknruy|ÄÉÜâåê“   	 TpathYindexHint§ 
   [TheMealsApp[TheMealsAppTCoreUUtils“    _lastAccessedDate^expansionState3Aƒõ±]WÕ “    •     [TheMealsAppVMapper“    _lastAccessedDate^expansionState3Aƒõ±K¥“    •     ! "[TheMealsApp[TheMealsAppTDataVRemote“ $ % & _lastAccessedDate^expansionState3Aƒõ±]-”“   ( • )   ! "[TheMealsApp“ + , - _lastAccessedDate^expansionState3Aƒõ±K¥“   / § 0  1 2[TheMealsAppVModuleZDetailMeal“ 4 5 6 _lastAccessedDate^expansionState3Aƒõ±K¥“   8 ¢ 9 :[TheMealsApp[TheMealsApp“ < = > _lastAccessedDate^expansionState3Aƒõ±]	J“   @ ° A[TheMealsApp“ C D E _lastAccessedDate^expansionState3Aƒõ±\˙“   G • H I 1 J K[TheMealsApp[TheMealsAppVDetailVRouter“ M N O _lastAccessedDate^expansionState3Aƒõ±K¥-“   Q • R I 1 S K[TheMealsAppVSearch“ U V O _lastAccessedDate^expansionState“   X • Y    Z[TheMealsAppYExtension“ \ ] ^ _lastAccessedDate^expansionState3Aƒõ±K¥>“   ` • a   b c[TheMealsAppVDomainUModel“ e f g _lastAccessedDate^expansionState3Aƒõ±]B>“   i £ j  [TheMealsApp“ l m n _lastAccessedDate^expansionState3Aƒõ±]“   p • q   b c[TheMealsApp“ s t u _lastAccessedDate^expansionState3Aƒõ±K¥N“   w § x I 1 y[TheMealsAppTMeal“ { | } _lastAccessedDate^expansionState3Aƒõ±]£w“    Å£ Ä  1[TheMealsApp“ É Ñ Ö _lastAccessedDate^expansionState3Aƒõ±]hî“   á § à  1 J[TheMealsApp“ ä ã å _lastAccessedDate^expansionState3Aƒõ±K¥W“   é • è I   ê[TheMealsAppTView“ í ì î _lastAccessedDate^expansionState3Aƒõ±]^û“   ñ • ó I 1 S ê[TheMealsApp“ ô ö õ _lastAccessedDate^expansionState3Aƒõ±K¥_“   ù Å• û  1 ü ê[TheMealsAppTHome“ ° ¢ £ _lastAccessedDate^expansionState3Aƒõ±]ù“   • Å• ¶ I 1 ß ê[TheMealsAppXFavorite“ © ™ ´ _lastAccessedDate^expansionState3Aƒõ±]ç?“   ≠ Å• Æ I 1 J ê[TheMealsApp“ ∞ ± ≤ _lastAccessedDate^expansionState3Aƒõ±]w‚“   ¥ Å• µ I 1 S ê[TheMealsApp“ ∑ ∏ π _lastAccessedDate^expansionState3Aƒõ±]ªÆ“   ª • º Ω  ! æ[TheMealsApp[TheMealsAppVLocale“ ¿ ¡ ¬ _lastAccessedDate^expansionState3Aƒõ±]!´“   ƒ § ≈ I 1 ß[TheMealsApp“ « » … _lastAccessedDate^expansionState3Aƒõ±]~≥“   À • Ã  1 ü Õ[TheMealsAppYPresenter“ œ – — _lastAccessedDate^expansionState3Aƒõ±]ñ“   ” § ‘ I 1 J[TheMealsApp“ ÷ ◊ ÿ _lastAccessedDate^expansionState3Aƒõ±]o “   ⁄ § €   ‹[TheMealsAppRDI“ ﬁ ﬂ ‡ _lastAccessedDate^expansionState3Aƒõ±]53“   ‚ • „  1 2 Õ[TheMealsApp“ Â Ê Á _lastAccessedDate^expansionState3Aƒõ±K¥ë“   È • Í   1 J Õ[TheMealsApp“ Ï Ì Ó _lastAccessedDate^expansionState3Aƒõ±]pÁ“    • Ò I 1 ß Õ[TheMealsApp“ Û Ù ı _lastAccessedDate^expansionState3Aƒõ±]Ä°“   ˜ • ¯ I 1 y Õ[TheMealsApp“ ˙ ˚ ¸ _lastAccessedDate^expansionState3Aƒõ±]•“   ˛ • ˇ I 1 S Õ[TheMealsApp“ _lastAccessedDate^expansionState3Aƒõ±]¥p“   Å§ I 1 ü[TheMealsApp“	
 _lastAccessedDate^expansionState3Aƒõ±]î“   ¶ Ω  ! æ[TheMealsAppVEntity“ _lastAccessedDate^expansionState3Aƒõ±K¥´“   • Ω  [TheMealsAppZExtensions“ _lastAccessedDate^expansionState“   £ [TheMealsAppSApp“ ! _lastAccessedDate^expansionState3Aƒõ±]o“  # ¶$ Ω  ! æ[TheMealsApp“&'( _lastAccessedDate^expansionState3Aƒõ±](R“  * §+   ![TheMealsApp“-./ _lastAccessedDate^expansionState3Aƒõ±]…“  1 Å§2   b[TheMealsApp“456 _lastAccessedDate^expansionState3Aƒõ±];…“  8 •9 I 1 ß K[TheMealsApp“;<= _lastAccessedDate^expansionState3Aƒõ±]Ü]“  ? •@  1 ü K[TheMealsApp“BCD _lastAccessedDate^expansionState3Aƒõ±Å	–“  FH§G I 1 S[TheMealsApp“JKL _lastAccessedDate^expansionState3Aƒõ±]≤â“  N §O  1 ü[TheMealsApp“QRS _lastAccessedDate^expansionState3Aƒõ±K¥Ã“  U ¶V   ! "W[TheMealsAppXResponse“YZ[ _lastAccessedDate^expansionState3Aƒõ±]2“  ] •^   b_[TheMealsAppWUseCase“abc _lastAccessedDate^expansionState3Aƒõ±]K˘“  e •f  1 J ê[TheMealsApp“hij _lastAccessedDate^expansionState3Aƒõ±K¥’“  l •m I 1 y ê[TheMealsApp“opq _lastAccessedDate^expansionState3Aƒõ±]™7“  s §t   ![TheMealsApp“vwx _lastAccessedDate^expansionState3Aƒõ±K¥›“  z Å•{ I 1 ß Õ[TheMealsApp“}~ _lastAccessedDate^expansionState3Aƒõ±K¥Â“  Å Å•Ç I 1 S Õ[TheMealsApp“ÑÖ _lastAccessedDate^expansionState“  á •à I 1 J Õ[TheMealsApp“äã _lastAccessedDate^expansionState“  ç Å•é   è[TheMealsAppWNetwork“ëíì _lastAccessedDate^expansionState3Aƒõ±K¥Ó3Aƒõ%‘È¢ñô¢óò#        #@êÄ     ¢öõ#@p     #@ç     °ù“  û £ü†°[TheMealsApp[TheMealsApp_Assets.xcassets   # 6 E S&/4>GS_djluàó†¢´∂¬…À‘Áˆˇ+07@Sbktãîß∂ø»—›‰Ô¯#,1=IRet}Üâïû±¿…“›Èı¸.7@KW^gzâíù©≥ºœﬁÁ˚0?HQXdmÄèò°¨∏¡‘„Ïı˛
+:CLS_aj}åïûß≥ºœﬁÁ˚(7@IT`i|ãîù®¥π¬’‰Ìˆ2AJS^jsÜïûß≤æ«⁄ÈÚ˚				%	.	A	P	Y	b	k	w	Ä	ì	¢	´	¥	ø	À	’	ﬁ	Ò
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
Ö
ò
ß
∞
π
ƒ
–
Ÿ
Ï
˚$-@OXalxÅî£¨µ¿Ã’Ë˜ 	 )<KT]fr{éù¶Øº»œÿÎ˙#.7JYbiuyÇï§≠∂√œÿÎ˙!*=LU^gs|èûß∞ª«–„Ú˚$7FOXamoxãö£¨µ¡ ›Ïı˛ )<KT]ht|Öòß∞πƒ–ŸÏ˚$-@OXajví°™≥æ ”Êı˛':IR]irÖîù®¥º≈ÿÁ˘˛#,/8?KW            ¢              i“ ] ^_&ExplorableOutlineViewArchivableUIState£  a_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier_”   "% 0¢#$Å6Å7¢&NÅ8Å/Ä+_queryParametersController]filterPattern”   ,1 0§-./0Å9Å:Å;Å<§2345Å=Å>Å?Å@Ä+_presentingScopeChooser_lastEasyToInitiateQueryClass[queryAction_querySpecification _IDEBatchFindTextQueryTfind÷?@ABC DE- XHIYanchoringYqueryTermYmatchCaseZnamedScopeZqueryClassÅEÅAÄ ÅDÅF‘ KLMN X,P_explicitDisplayStringXtermTypeTtextÅCÄ ÅB“ ÙıSÅ÷P“ ] ^UV_IDEBatchFindQueryTerm¢W a_IDEBatchFindQueryTerm_IDEBatchFindTextQueryTnone“ ] ^[\_IDEBatchFindQuerySpecification¢] a_IDEBatchFindQuerySpecification”   _f 0¶`abcdeÅHÅIÅJÅKÅLÅM¶gh &kÅNÅOÄ\ÄÅXÄ]Ä+_IDEStackCompressionValue_IDEDebugTransientStates_DBGNavigatorContentMode_IDEShowOnlyRunningBlocks^IDEVisibleRect_IDEShowOnlyInterestingContent”   v{ 0§wxyzÅPÅQÅRÅS§|} &ÅTÅVÄÅWÄ+_IDEDebugExpandedItems_#IDECurrentLaunchSessionReferenceKey_1IDEHaveInitiallyExpandedCPUDebuggingChildrenState_IDEDebugSelectedNavigableItems“  Ü Å°áÅUÄ%[TheMealsApp\7fc6c48ac7f0“  åN†Å_{{0, 0}, {257, 920}}_ Xcode.IDEKit.Navigator.Workspace”   ëñ 0§íìîïÅ[Å\Å]Å^§óÈÒöÅ_Å%ÅYÅ`Ä+_"Xcode.IDEKit.NavigatorGroup.Issues_!Xcode.IDEKit.NavigatorGroup.Debug_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_#Xcode.IDENoticesKit.NoticeNavigator_Xcode.IDEKit.Navigator.Find”   £® 0§§•¶ßÅbÅcÅdÅe§©™´¨ÅfÅiÅjÅnÄ+_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey”   ≥µ °¥Åg°∂ÅhÄe_IDENavigatorModeSolitaryOnbplist00‘
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—	TrootÄ©!$'+,U$null’V$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiersÄ ÄÄÄ“ZNS.objects†Ä“Z$classnameX$classesWNSArray¢ XNSObject“"#†Ä“%&^NSMutableArray£% “(#°)ÄÄ_IDEFilterIdentifier_NoticeError“-.__DVTFilterExpressionStateValue§/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u Ç ö Æ œ — ” ’ ◊ Ÿ ﬁ È Í Ï Ò ¸!&59>@BDfkåë≤Œ             2              Í_IDENavigatorModeSolitary”   ºæ °ΩÅk°øÅlÄe_IDENavigatorModeSolitary“ √≈_codablePlistRepresentationÅ4ÅmO®bplist00‘YitemState_lastAccessedDate^scrollPosition]selectedItems†3Aƒõ Z|[¢
¢		#        ¢#@Ç     #@ç∞     †.=KLUX[dgpy                            z”   »  °…Åo°ÀÅpÄe_IDENavigatorModeSolitary“ œ—_codablePlistRepresentationÅ4ÅqO®bplist00‘YitemState_lastAccessedDate^scrollPosition]selectedItems†3Aƒõ Z||¢
¢		#        ¢#@p     #@å¿     †.=KLUX[dgpy                            z#@p@     ”   ’◊ 0°÷Åt°ÿÅuÄ+_!userPreferredCategoryExtensionIDs“  ‹N°›ÅvÅ_#Xcode.IDEKit.InspectorCategory.File#@p     ”   ‚‰ 0°„Åy° &ÄÄ+_ShowsOnlyVisibleViewObjects”   ÈÏ 0¢ÍÎÅ{Å|¢Ä]Ä]Ä+_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspace”   Ûˇ 0´Ùıˆ˜¯˘˙˚¸˝˛Å~ÅÅÄÅÅÅÇÅÉÅÑÅÖÅÜÅáÅà´ 	
ÅâÄ\ÅãÅåÅçÅéÅSÅ]ÅiÅjÅÄ+_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ EditorMultipleSplitPrimaryLayout_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_DebuggerSplitView_ DefaultPersistentRepresentations_IDEDefaultDebugArea^MaximizedState” …,_NSIndexPathLength_NSIndexPathValueÅä“ ] ^[NSIndexPath¢ a[NSIndexPathZOpenInTabs _Layout_LeftToRight“  #N°$ÅèÅ”   '. 0¶()*+,-ÅêÅëÅíÅìÅîÅï¶/01/3ÅñÅóÅ2ÅñÅRÄ]Ä+ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments ”   >B 0£?@AÅòÅôÅö£CrrÅõÅGÅGÄ+_TabsAsHistoryItems_SelectedTabIndex_DynamicTabIndex“  K …•LMNOPÅúÅ∆ÅÈÅ
ÅÄGÿSTUV WXYZ[\]^_·a_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifierÅ°ÅØÅ±ÅùÅ≈ÅƒÄ’Å∞÷cde fghijkl_DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifierÅ©ÅüÅ´ÅÆÅûÄ\_/Xcode.IDENavigableItemDomain.WorkspaceStructure“  p …§qrstÅ†Å£Å•ÅßÄG”wx Zƒ{ZIdentifierUIndexÅ°ÅÅ¢_ContentView.swift“ ] ^~_IDEArchivableStringIndexPair¢Ä a_IDEArchivableStringIndexPair”wx Ç{Å§Ä\Å¢SApp”wx á{Å¶Ä\Å¢[TheMealsApp”wx å{Å®Ä\Å¢[TheMealsApp” Õ  Ãˆí XÄ÷Å™Ä “ ] ^ïñ_DVTDocumentLocation¢ó a_DVTDocumentLocation“ ôöõZpathStringÅ≠Å¨_b/Users/gilangramadhan/Documents/ProjectiOS/dicoding/MiDE-Project/TheMealsApp/TheMealsApp.xcodeproj“ ] ^ûü[DVTFilePath¢† a[DVTFilePath“ ] ^¢£_(IDENavigableItemArchivableRepresentation¢§ a_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCode”   ®≥ 0™©™´¨≠ÆØ∞±≤Å≤Å≥Å¥ÅµÅ∂Å∑Å∏ÅπÅ∫Åª™¥∏Üº &ÅºÄ]Ä]Ä]ÅæÅUÄ\Ä]Å¬ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“    …°ÀÅΩÄG“  Œ …§SùSùÅHÅOÅHÅOÄG”   ’◊ °÷Åø°ÿÅ¿Äe_DeltaEditorLayoutExtension”   ‹ﬂ ¢›7Å¡Äa¢Æ¨ÅTÄÑÄe^documentLength”   ÂË ¢HÁÄhÅ√¢¨ÆÄÑÅTÄe^documentLengthTbody“ ] ^Ô_IDEEditorHistoryItem¢Ò a_IDEEditorHistoryItemÿSTUV WXYÛ[ıˆ^¯‹aÅ ÅØÅ’Å«Å≈ÅËÄÀÅ∞÷cde fg¸˝jklÅ‘Å»Å´ÅÆÅûÄ\“   …¶	Å…ÅÀÅÕÅœÅ—Å“ÄG”wx Û{Å Ä\Å¢_SearchPresenter.swift”wx {ÅÃÄ\Å¢YPresenter”wx &{ÅŒÄ[Å¢VSearch”wx g{Å–ÅNÅ¢VModule”wx á{Å¶Ä\Å¢”wx ${Å”Ä\Å¢[TheMealsApp” Õ  Ã›í XÄÃÅ™Ä ”   -8 0™./01234567Å÷Å◊ÅÿÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂ™9=L,A &Å‡Ä]Ä]Ä]Å‚Å.ÅàÄ]ÅÊÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  O …°PÅ·ÄG“  S …§vav/ÅûÅÅÅûÅ"ÄG”   Z\ °[Å„°]Å‰Äe_DeltaEditorLayoutExtension”   ad ¢b7ÅÂÄa¢9¨ÅÈÄÑÄe^documentLength”   jm ¢HlÄhÅÁ¢¨9ÄÑÅÈÄe^documentLength\searchMeal()ÿSTUV WXYt[vw^_ÊaÅÌÅØÅˆÅÍÅ≈ÅƒÄﬂÅ∞÷cde fg}~jklÅıÅÎÅ´ÅÆÅûÄ\“  Ñ …¶ÖÜáàâäÅÏÅÓÅÅÒÅÚÅÛÄG”wx t{ÅÌÄ\Å¢_SearchView.swift”wx íg{ÅÔÅNÅ¢TView”wx &{ÅŒÄ[Å¢”wx g{Å–ÅNÅ¢”wx á{Å¶Ä\Å¢”wx £{ÅÙÄ\Å¢[TheMealsApp” Õ  Ãí XÄ‡Å™Ä ”   ¨∑ 0™≠ÆØ∞±≤≥¥µ∂Å˜Å¯Å˘Å˙Å˚Å¸Å˝Å˛ÅˇÅ ™∏ºΩr¿ &ÅÄ]Ä]Ä]ÅÅÅGÄ]ÅÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Œ …°œÅÄG“  “ …§+ú+úÅáÅ^ÅáÅ^ÄG”   Ÿ€ °⁄Å°‹ÅÄe_DeltaEditorLayoutExtension”   ‡„ ¢·7ÅÄa¢#¨ÅæÄÑÄe^documentLength#@(      ”   ÍÌ ¢HÏÄhÅ	¢¨#ÄÑÅæÄe^documentLengthÿSTUV WXYÛÙ Xˆ^t Q˙ÅÅÄ ÅÅ≈ÅÌÄÅ÷cde fg¸˝jklÅÅÅ´ÅÆÅûÄ\“   …£ÅÅÅÄG”wx Ûr{ÅÅGÅ¢_Assets.xcassets”wx á{Å¶Ä\Å¢”wx {ÅÄ\Å¢[TheMealsApp” Õ  Ã Zí XÄÅ™Ä _com.apple.dt.assetcatalog_(Xcode.IDEKit.EditorDocument.AssetCatalogÿSTUV WXY[ ^_¿aÅÅØÅÅÅ≈ÅƒÄìÅ∞÷cde fg&'jklÅÅÅ´ÅÆÅûÄ\“  - …§./01ÅÅÅÅÄG”wx {ÅÄ\Å¢_TheMealsAppApp.swift”wx Ç{Å§Ä\Å¢”wx á{Å¶Ä\Å¢”wx A{ÅÄ\Å¢[TheMealsApp” Õ  ÃQí XÄîÅ™Ä ”   JU 0™KLMNOPQRSTÅ Å!Å"Å#Å$Å%Å&Å'Å(Å)™VZ^ &Å*Ä]Ä]Ä]Å,ÄfÄ\Ä]Å0ÄÄ+_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  l …°mÅ+ÄG“  p …§+,+,ÅáÅàÅáÅàÄG”   wy °xÅ-°zÅ.Äe_DeltaEditorLayoutExtension”   ~Å ¢7ÄÄaÅ/¢¨?ÄÑÅçÄe^documentLength”   áä ¢HâÄhÅ1¢¨?ÄÑÅçÄe^documentLength“  êN°ëÅ3Å“î ïñ_currentEditorHistoryItemÅ4ÅQÿSTUV WXYÛÙöõ^ù Q˙ÅÅÅ<Å5Å≈ÅPÄÅ÷cde fg°¢jklÅ;Å6Å´ÅÆÅûÄ\“  ® …£©™´Å7Å8Å9ÄG”wx Ûr{ÅÅGÅ¢”wx á{Å¶Ä\Å¢”wx ∂{Å:Ä\Å¢[TheMealsApp” Õ  Ã Zí XÄÅ™Ä ”   ø» 0®¿¡¬√ƒ≈∆«Å=Å>Å?Å@ÅAÅBÅCÅD®… À pÕŒœ–ÅEÅGÅIÄ)ÅJÅKÅNÅOÄ+_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area“  € Å°‹ÅFÄ%_./assetSearchMeal.imageset“  ‡ Å°·ÅHÄ%_./assetSearchMeal.imageset_IBICCatalogOverviewController”   ÊÁ 0††Ä+”   ÍÏ 0°ÎÅL°ÌÅMÄ+_expandedItemIDs“  Ò †° üÄ/Ä0^sourceListArea”   ˆ˜ 0††Ä+_assetSearchMeal“ ] ^˚¸_IDEEditorHistoryStack¢˝ a_IDEEditorHistoryStack_ItemKind_Editor÷cde fg jklƒÅZÅTÅ´ÅÆÅûÅ“   …§	
ÅUÅVÅWÅXÄG_assetSearchMeal.imageset”wx Ûr{ÅÅGÅ¢”wx á{Å¶Ä\Å¢”wx {ÅYÄ\Å¢[TheMealsApp‘  Ã Õ X Z \relativePathÅ\Ä ÄÅ[_./assetSearchMeal.imageset“ ] ^#$_IBICCatalogDocumentLocation£%& a_IBICCatalogDocumentLocation_DVTDocumentLocation”   (* 0°)Å^°+Å_Ä+_DVTSplitViewItems“  /N¢01Å`ÅfÅ”   48 £567ÅaÅbÅc£9 &;ÅdÄÅeÄe]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeYIDEEditor#@Öh     ”   CG £567ÅaÅbÅc£H &JÅgÄÅhÄe_IDEDebuggerArea#@u@     ”   OP 0††Ä+”   SX 0§TUVWÅkÅlÅmÅn§YZƒ\ÅoÅtÅÅvÄ+XLeftViewYRightViewZLayoutMode_IDESplitViewDebugArea”   ch 0§defgÅpÅqÅrÅs§¨ &Ä]Ä\ÄÑÄÄ+_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsType”   su 0°tÅu°Ä\Ä+_ConsoleFilterMode”   z| 0°{Åw°}ÅxÄ+_DVTSplitViewItems“  ÅN¢ÇÉÅyÅ|Å”   Üä £567ÅaÅbÅc£ã &çÅzÄÅ{ÄeXLeftView#@{p     ”   íñ £567ÅaÅbÅc£ó &ôÅ}ÄÅ~ÄeYRightView#@v∞               "   ,   1   :   ?   Q   V   \   ^  b  h  u  }  à  è  î  ñ  ò  ù  ü  ¢  §  ª  ‚  Ô        
                -  /  1  4  7  :  =  @  C  F  I  K  b    ì  †  ∑  Í  ¸    +  F  G  T  _  a  c  e  g  i  t  v  x  z  |  ~  Ä  ´  Â  "  L  |  â  å  é  ë  ì  ï  ¢  ™  ∂  ∏  ∫  º  .  7  B  K  Q  V  _  l  }    Å  É  Ö  á  â  ã  ç  û  †  ¢  §  ¶  ®  ™  ¨  Æ  ∞     ÷  È      %  7  E  N  Q  S  U  r  {  à  è  ï  û  °  £  •  ¬  ‚  Î  ¯  ˘  ˙  ¸      "  /  <  ?  A  D  F  H  Z  c  f  h  j  l  u  z  â  ñ  ó  ò  ö  ß  ™  ¨  Ø  ±  ≥  ¿  ¬  ƒ  ∆  2  ?  H  J  L  N  P  Y  [  ]  _  a  c  ì  ¬  Ò    3  H  Q  T  V  X  i  s  }  â  ã  ç  è  ë  ˝            !  #  %  '  /  6  B  Y  b  Ç  â  ©  ø  »  –  ’  ‚  „  ‰  Ê  Û  ¯  ˙  ¸                  ö  ß  ©  ´  ≠  -  :  O  Q  S  U  W  Y  [  ]  _  a  c  x  z  |  ~  Ä  Ç  Ñ  Ü  à  ä  å  é  ´     Ó    4  Z  ~  ù  º  Ÿ  ‚  Â  Á  È  Ú  ˚  ˝  ˇ          	  
          !  #  @  M  R  T  V  [  ]  _  a  m  |  Ö  Ü  à  ã  î  ô  ¢  Ø  ¥  ∂  ∏  Ω  ø  ¡  √  …  ÿ  Â  ˙  ¸  ˛             
      #  %  '  )  +  -  /  1  3  5  7  9  V  u  ô  ª  ﬂ    )  H  g  Ñ  ç  ê  í  î  ù  ¶  ®  ™  ¨  Æ  ∞  Ω  ¿  ¬  ≈  «  …  Ê  Û  ¯  ˙  ¸            #  (  *  ,  1  3  5  7  F  S  X  Z  \  a  c  e  g  t  v  x  z  Ø  º  æ  ¿  ¬  ˜         	            ,   5   6   8   E   H   J   M   O   Q   ^   ’   ◊   Ÿ   €   ›   ﬂ   ·   „   Â   Á   È   Î   Ì   Ô   Ò   Û   ı   ˜   ˘   ˚   ˝   ˇ  !  !  !  !  !	  !  !  !  !  !  !  !  !  !  !  !  !!  !#  !%  !'  !)  !+  !-  !/  !1  !3  !5  !7  !9  !;  !=  !?  !A  !C  !E  !G  !I  !¿  !¬  !≈  !»  !À  !Œ  !—  !‘  !◊  !⁄  !›  !‡  !„  !Ê  !È  !Ï  !Ô  !Ú  !ı  !¯  !˚  !˛  "  "  "  "
  "  "  "  "  "  "  "  ""  "%  "(  "+  ".  "1  "4  "7  ":  "=  "@  "C  "F  "I  "L  "O  "R  "U  "X  "[  "^  "a  "d  "g  "j  "m  "o  "|  "~  "Ä  "Ç  #  #  #  #  #  #¢  #Ø  #±  #≥  #µ  $=  $J  $L  $N  $P  $›  $Í  $Ï  $Ó  $  %}  %ä  %å  %é  %ê  &  &  &  &!  &#  &û  &´  &≠  &Ø  &±  ':  'G  'I  'K  'M  '÷  '„  'Â  'Á  'È  (z  (á  (â  (ã  (ç  )  )&  )(  )*  ),  )∫  )«  )…  )À  )Õ  *R  *_  *a  *c  *e  *Í  *˜  *˘  *˚  *˝  +w  +Ñ  +Ü  +à  +ä  ,  ,  ,  ,  ,!  ,¢  ,Ø  ,±  ,≥  ,µ  -8  -E  -G  -I  -K  -—  -ﬁ  -‡  -‚  -‰  .k  .x  .z  .|  .~  /  /  /  /  /  /õ  /®  /™  /¨  /Æ  08  0E  0G  0I  0K  0‘  0·  0„  0Â  0Á  1m  1z  1|  1~  1Ä  2  2  2  2!  2#  2∑  2ƒ  2∆  2»  2   3M  3Z  3\  3^  3`  3Ï  3˘  3˚  3˝  3ˇ  4ì  4†  4¢  4§  4¶  59  5F  5H  5J  5L  5⁄  5Á  5È  5Î  5Ì  6|  6â  6ã  6ç  6è  7  7&  7(  7*  7,  7º  7…  7À  7Õ  7œ  8C  8P  8R  8T  8V  8·  8Ó  8  8Ú  8Ù  9w  9Ñ  9Ü  9à  9ä  :  :"  :$  :&  :(  :†  :≠  :Ø  :±  :≥  ;9  ;F  ;H  ;J  ;L  ;◊  ;‰  ;Ê  ;Ë  ;Í  <k  <x  <z  <|  <~  =	  =  =  =  =  =¢  =Ø  =±  =≥  =µ  >N  >[  >]  >_  >a  >Ò  >˛  ?   ?  ?  ?{  ?à  ?ä  ?å  ?é  @  @#  @%  @'  @)  @æ  @À  @Õ  @œ  @—  AS  A`  Ab  Ad  Af  AË  Aı  A˜  A˘  A˚  Bã  Bò  Bö  Bú  Bû  C(  C5  C7  C9  C;  C   C◊  CŸ  C€  C›  Dg  Dt  Dv  Dx  Dz  E  E  E  E  E  E¶  E≥  E»  E   EÃ  EŒ  E–  E”  E÷  EŸ  E‹  Eﬂ  E‚  E˜  E˙  E¸  E˛  F   F  F  F  F	  F  F  F  F-  FL  Fp  Fí  F∂  F‹  G   G  G>  G[  Gd  Gg  Gj  Gl  Gu  G~  GÅ  GÑ  Gá  Gä  Gå  Gé  Gê  Gù  G†  G£  G¶  G©  G´  G»  G’  G⁄  G›  Gﬂ  G‰  GÁ  GÈ  GÎ  G˙  G˝  H
  H  H  H  H  H  H  H   H/  H<  HQ  HT  HW  HZ  H]  H`  Hc  Hf  Hi  Hl  Ho  HÑ  Há  Hâ  Hã  Hç  Hê  Hí  Hî  Hñ  Hô  Hõ  Hù  H∫  HŸ  H˝  I  IC  Ii  Iç  I¨  IÀ  IË  IÒ  IÙ  I˜  I˘  J  J  J  J  J  J  J  J  J  J*  J-  J0  J3  J6  J8  JU  Jb  Jg  Jj  Jl  Jq  Jt  Jv  Jx  Já  Jâ  Jñ  Jõ  Jù  J†  J•  Jß  J™  J¨  Jª  J»  J›  J‡  J„  JÊ  JÈ  JÏ  JÔ  JÚ  Jı  J¯  J˚  K  K  K  K  K  K  K  K   K"  K%  K'  K)  KF  Ke  Kâ  K´  Kœ  Kı  L  L8  LW  Lt  L}  LÄ  LÉ  LÖ  Lé  Ló  Lô  Lõ  Lù  Lü  L°  LÆ  L±  L¥  L∑  L∫  Lº  LŸ  LÊ  LÎ  LÌ  L  Lı  L˜  L˙  L¸  M  M  M  M   M"  M%  M*  M,  M/  M1  M@  MM  Mb  Me  Mh  Mk  Mn  Mq  Mt  Mw  Mz  M}  MÄ  Mï  Mò  Mö  Mú  Mû  M°  M£  M•  Mß  M™  M¨  MÆ  MÀ  MÍ  N  N0  NT  Nz  Nû  NΩ  N‹  N˘  O  O  O  O
  O  O  O  O"  O%  O(  O*  O,  O9  O<  O?  OB  OE  OG  Od  Oq  Ov  Ox  O{  OÄ  OÇ  OÖ  Oá  Oñ  Oô  O¶  O´  O≠  O∞  Oµ  O∑  O∫  Oº  OÀ  Oÿ  OÌ  O  OÛ  Oˆ  O˘  O¸  Oˇ  P  P  P  P  P   P#  P%  P'  P)  P,  P.  P0  P2  P5  P7  P9  PV  Pu  Pô  Pª  Pﬂ  Q  Q)  QH  Qg  QÑ  Qç  Qê  Qì  Qï  Qû  Qß  Q™  Q≠  Q∞  Q≥  Qµ  Q∑  Qπ  Q∆  Q…  QÃ  Qœ  Q“  Q‘  QÒ  Q˛  R  R  R  R  R  R  R  R#  R&  R3  R8  R:  R=  RB  RD  RG  RI  RX  Re  Rz  R}  RÄ  RÉ  RÜ  Râ  Rå  Rè  Rí  Rï  Rò  R≠  R∞  R≤  R¥  R∂  Rπ  Rª  RΩ  Rø  R¬  Rƒ  R∆  R„  S  S&  SH  Sl  Sí  S∂  S’  SÙ  T  T  T  T   T"  T+  T4  T6  T8  T:  T<  T>  TK  TN  TQ  TT  TW  TY  Tv  TÉ  Tà  Tã  Tç  Tí  Tï  Tó  Tô  T®  T™  T∑  Tº  Tæ  T¡  T∆  T»  TÀ  TÕ  T‹  TÈ  T˛  U  U  U  U
  U  U  U  U  U  U  U1  U4  U6  U8  U:  U=  U?  UA  UC  UF  UH  UJ  Ug  UÜ  U™  UÃ  U  V  V:  VY  Vx  Vï  Vû  V°  V§  V¶  VØ  V∏  Vª  Væ  V¡  Vƒ  V∆  V»  V   V◊  V⁄  V›  V‡  V„  VÂ  W  W  W  W  W  W  W   W#  W%  W4  W7  WD  WI  WK  WN  WS  WU  WX  WZ  Wi  Wv  Wã  Wé  Wë  Wî  Wó  Wö  Wù  W†  W£  W¶  W©  Wæ  W¡  W√  W≈  W«  W   WÕ  W–  W“  W’  W◊  WŸ  Wˆ  X  X9  X[  X  X•  X…  XË  Y  Y$  Y-  Y0  Y3  Y5  Y>  YG  YJ  YL  YO  YQ  YS  YU  YW  Yd  Yg  Yj  Ym  Yp  Yr  Yè  Yú  Y°  Y£  Y¶  Y´  Y≠  Y∞  Y≤  Y¡  Yƒ  YÕ  Yœ  Y‹  Y·  Y„  YÊ  YÎ  YÌ  Y  YÚ  Z  Z  Z#  Z&  Z)  Z,  Z/  Z2  Z5  Z8  Z;  Z>  ZA  ZV  ZY  Z[  Z]  Z_  Zb  Zd  Zf  Zh  Zk  Zm  Zo  Zå  Z´  Zœ  ZÒ  [  [;  [_  [~  [ù  [∫  [√  [∆  […  [À  [‘  [›  [‡  [‚  [Â  [Á  [È  [Î  [¯  [˚  [˛  \  \  \  \#  \0  \5  \7  \:  \?  \A  \D  \F  \U  \X  \e  \j  \l  \o  \t  \v  \y  \{  \ä  \ó  \¨  \Ø  \≤  \µ  \∏  \ª  \æ  \¡  \ƒ  \«  \   \ﬂ  \‚  \‰  \Ê  \Ë  \Î  \Ì  \Ô  \Ò  \Ù  \ˆ  \¯  ]  ]4  ]X  ]z  ]û  ]ƒ  ]Ë  ^  ^&  ^C  ^L  ^O  ^R  ^T  ^]  ^f  ^i  ^l  ^o  ^r  ^t  ^v  ^É  ^Ü  ^â  ^å  ^è  ^ë  ^Æ  ^ª  ^¿  ^√  ^≈  ^   ^Õ  ^œ  ^—  ^‡  ^„  ^  ^ı  ^˜  ^˙  ^ˇ  _  _  _  _  _"  _7  _:  _=  _@  _C  _F  _I  _L  _O  _R  _U  _j  _m  _o  _q  _s  _v  _y  _|  _~  _Å  _É  _Ö  _¢  _¡  _Â  `  `+  `Q  `u  `î  `≥  `–  `Ÿ  `‹  `ﬂ  `·  `Í  `Û  `ˆ  `˘  `¸  `ˇ  a  a  a  a  a  a  a  a  a;  aH  aM  aO  aR  aW  aY  a\  a^  am  ap  ay  a{  aà  aç  aè  aí  aó  aô  aú  aû  a≠  a∫  aœ  a“  a’  aÿ  a€  aﬁ  a·  a‰  aÁ  aÍ  aÌ  b  b  b  b	  b  b  b  b  b  b  b  b  b8  bW  b{  bù  b¡  bÁ  c  c*  cI  cf  co  cr  cu  cw  cÄ  câ  cå  cè  cí  cï  có  cô  c¶  c©  c¨  cØ  c≤  c¥  c—  cﬁ  c„  cÂ  cË  cÌ  cÔ  cÚ  cÙ  d  d  d  d  d  d  d"  d$  d'  d)  d8  dE  dZ  d]  d`  dc  df  di  dl  do  dr  du  dx  dç  dê  dí  dî  dñ  dô  dú  dü  d°  d§  d¶  d®  d≈  d‰  e  e*  eN  et  eò  e∑  e÷  eÛ  e¸  eˇ  f  f  f  f  f  f  f  f   f"  f$  f1  f4  f7  f:  f=  f?  f\  fi  fn  fp  fs  fx  fz  f}  f  fé  fë  fì  f†  f•  fß  f™  fØ  f±  f¥  f∂  f≈  f“  fÈ  fÏ  fÔ  fÚ  fı  f¯  f˚  f˛  g  g  g  g
  g!  g$  g&  g(  g*  g-  g0  g3  g6  g8  g;  g=  g?  g\  g{  gü  g¡  gÂ  h  h6  hZ  hy  hò  hµ  hæ  h¡  hƒ  h∆  hœ  hÿ  h€  h›  h‡  h„  hÂ  hÁ  hÈ  hˆ  h˘  h¸  hˇ  i  i  i!  i.  i3  i6  i8  i=  i@  iB  iD  iS  iV  i_  il  is  iv  iy  i|  iÉ  iÜ  ià  iã  iç  i¢  i¡  iﬂ  iË  iÈ  iÎ  i¯  i˝  iˇ  j  j  j	  j  j  j  j*  j?  jB  jE  jH  jK  jN  jQ  jT  jW  jZ  j]  jr  ju  jw  jy  j{  j~  jÅ  jÑ  jÜ  jâ  jã  jç  j™  j…  jÌ  k  k3  kY  k}  kú  kª  kÿ  k·  k‰  kÁ  kÈ  kÚ  k˚  k˛  l   l  l  l  l	  l  l  l  l  l"  l$  lA  lN  lS  lU  lX  l]  l_  lb  ld  ls  lv  l  lÅ  lé  lì  lï  lò  lù  lü  l¢  l§  l≥  l¿  l’  lÿ  l€  lﬁ  l·  l‰  lÁ  lÍ  lÌ  l  lÛ  m  m  m  m  m  m  m  m  m  m  m  m!  m>  m]  mÅ  m£  m«  mÌ  n  n0  nO  nl  nu  nx  n{  n}  nÜ  nè  ní  nï  nò  nõ  nù  nü  n¨  nØ  n≤  nµ  n∏  n∫  n◊  n‰  nÈ  nÏ  nÓ  nÛ  nˆ  n¯  n˙  o	  o  o  o  o   o#  o(  o*  o-  o/  o>  oK  o`  oc  of  oi  ol  oo  or  ou  ox  o{  o~  oì  oñ  oò  oö  oú  oü  o¢  o§  o¶  o©  o´  o≠  o   oÈ  p  p/  pS  py  pù  pº  p€  p¯  q  q  q  q	  q  q  q  q!  q$  q'  q)  q6  q9  q<  q?  qB  qD  qa  qn  qs  qv  qx  q}  qÄ  qÇ  qÑ  qì  qñ  qü  q¨  q±  q≥  q∂  qª  qΩ  q¿  q¬  q—  qﬁ  qÛ  qˆ  q˘  q¸  qˇ  r  r  r  r  r  r  r&  r)  r+  r-  r/  r2  r5  r8  r:  r=  r?  rA  r^  r}  r°  r√  rÁ  s  s1  sP  so  så  sï  sò  sõ  sù  s¶  sØ  s≤  s¥  s∑  sπ  sª  sΩ  s   sÕ  s–  s”  s÷  sÿ  sı  t  t  t
  t  t  t  t  t  t'  t*  t3  t@  tE  tG  tJ  tO  tQ  tT  tV  te  tr  tá  tä  tç  tê  tì  tñ  tô  tú  tü  t¢  t•  t∫  tΩ  tø  t¡  t√  t∆  t»  t   tÃ  tœ  t—  t”  t  u  u3  uU  uy  uü  u√  u‚  v  v  v'  v*  v-  v/  v8  vA  vD  vF  vI  vL  vN  vP  vR  v_  vb  ve  vh  vk  vm  vä  vó  vú  vû  v°  v¶  v®  v´  v≠  vº  vø  vÃ  v—  v”  v÷  v€  v›  v‡  v‚  vÒ  v˛  w  w  w  w  w  w"  w%  w(  w+  w.  w1  wF  wI  wK  wM  wO  wR  wT  wV  wX  w[  w]  w_  w|  wõ  wø  w·  x  x+  xO  xn  xç  x™  x≥  x∂  xπ  xª  xƒ  xÕ  x–  x”  x÷  xŸ  x€  xË  xÎ  xÓ  xÒ  xÙ  xˆ  y  y   y%  y'  y*  y/  y1  y4  y6  yE  yH  yU  yZ  y\  y_  yd  yf  yi  yk  yz  yá  yú  yü  y¢  y•  y®  y´  yÆ  y±  y¥  y∑  y∫  yœ  y“  y‘  y÷  yÿ  y€  y›  yﬂ  y·  y‰  yÊ  yË  z  z$  zH  zj  zé  z¥  zÿ  z˜  {  {3  {<  {?  {B  {D  {M  {V  {Y  {[  {^  {`  {b  {d  {q  {t  {w  {z  {}  {  {ú  {©  {Æ  {±  {≥  {∏  {ª  {Ω  {ø  {Œ  {—  {ﬁ  {„  {Â  {Ë  {Ì  {Ô  {Ú  {Ù  |  |  |%  |(  |+  |.  |1  |4  |7  |:  |=  |@  |C  |X  |[  |]  |_  |a  |d  |f  |h  |j  |m  |o  |q  |é  |≠  |—  |Û  }  }=  }a  }Ä  }ü  }º  }≈  }»  }À  }Õ  }÷  }ﬂ  }‚  }Â  }Ë  }Î  }Ì  }Ô  }¸  }ˇ  ~  ~  ~  ~
  ~'  ~4  ~9  ~<  ~>  ~C  ~F  ~H  ~J  ~Y  ~\  ~i  ~n  ~p  ~s  ~x  ~z  ~}  ~  ~é  ~õ  ~∞  ~≥  ~∂  ~π  ~º  ~ø  ~¬  ~≈  ~»  ~À  ~Œ  ~„  ~Ê  ~Ë  ~Í  ~Ï  ~Ô  ~Ò  ~Û  ~ı  ~¯  ~˙  ~¸    8  \  ~  ¢  »  Ï  Ä  Ä*  ÄG  ÄP  ÄS  ÄV  ÄX  Äa  Äj  Äm  Äo  Är  Ät  Äv  ÄÉ  ÄÜ  Äâ  Äå  Äè  Äë  ÄÆ  Äª  Ä¿  Ä√  Ä≈  Ä   ÄÕ  Äœ  Ä—  Ä‡  ÄÌ  ÄÚ  ÄÙ  Ä˜  Ä¸  Ä˛  Å  Å  Å  Å  Å4  Å7  Å:  Å=  Å@  ÅC  ÅF  ÅI  ÅL  ÅO  ÅR  Åg  Åj  Ål  Ån  Åp  Ås  Åu  Åw  Åy  Å|  Å~  ÅÄ  Åù  Åº  Å‡  Ç  Ç&  ÇL  Çp  Çè  ÇÆ  ÇÀ  Ç‘  Ç◊  Ç⁄  Ç‹  ÇÂ  ÇÓ  ÇÒ  ÇÙ  Ç˜  Ç˙  Ç¸  É	  É  É  É  É  É  É4  ÉA  ÉF  ÉI  ÉK  ÉP  ÉS  ÉU  ÉW  Éf  Éi  Év  É{  É}  ÉÄ  ÉÖ  Éá  Éä  Éå  Éõ  É®  ÉΩ  É¿  É√  É∆  É…  ÉÃ  Éœ  É“  É’  Éÿ  É€  É  ÉÛ  Éı  É˜  É˘  É¸  É˛  Ñ   Ñ  Ñ  Ñ  Ñ	  Ñ&  ÑE  Ñi  Ñã  ÑØ  Ñ’  Ñ˘  Ö  Ö7  ÖT  Ö]  Ö`  Öc  Öe  Ön  Öw  Öz  Ö|  Ö  ÖÅ  ÖÉ  Öê  Öì  Öñ  Öô  Öú  Öû  Öª  Ö»  ÖÕ  Ö–  Ö“  Ö◊  Ö⁄  Ö‹  Öﬁ  ÖÌ  Ö  Ö˝  Ü  Ü  Ü  Ü  Ü  Ü  Ü  Ü"  Ü/  ÜD  ÜG  ÜJ  ÜM  ÜP  ÜS  ÜV  ÜY  Ü\  Ü_  Üb  Üw  Üz  Ü|  Ü~  ÜÄ  ÜÉ  ÜÖ  Üá  Üâ  Üå  Üé  Üê  Ü≠  ÜÃ  Ü  á  á6  á\  áÄ  áü  áæ  á€  á‰  áÁ  áÍ  áÏ  áı  á˛  à  à  à  à
  à  à  à  à  à!  à$  à'  à)  àF  àS  àX  à[  à]  àb  àe  àg  ài  àx  à{  àà  àç  àè  àí  àó  àô  àú  àû  à≠  à∫  àœ  à“  à’  àÿ  à€  àﬁ  à·  à‰  àÁ  àÍ  àÌ  â  â  â  â	  â  â  â  â  â  â  â  â  â:  âY  â}  âü  â√  âÈ  ä  ä,  äK  äh  äq  ät  äw  äy  äÇ  äã  äé  äë  äî  äó  äô  äõ  ä®  ä´  äÆ  ä±  ä¥  ä∂  ä”  ä‡  äÂ  äË  äÍ  äÔ  äÚ  äÙ  äˆ  ã  ã  ã  ã  ã   ã%  ã'  ã*  ã/  ã1  ã4  ã6  ãE  ãR  ãg  ãj  ãm  ãp  ãs  ãv  ãy  ã|  ã  ãÇ  ãÖ  ãö  ãù  ãü  ã°  ã£  ã¶  ã©  ã¨  ãÆ  ã±  ã≥  ãµ  ã“  ãÒ  å  å7  å[  åÅ  å•  åƒ  å„  ç   ç	  ç  ç  ç  ç  ç#  ç&  ç(  ç+  ç.  ç0  ç2  ç?  çB  çE  çH  çK  çM  çj  çw  ç|  ç  çÅ  çÜ  çâ  çã  çç  çú  çü  ç®  ç™  ç∑  çº  çæ  ç¡  ç∆  ç»  çÀ  çÕ  ç‹  çÈ  ç˛  é  é  é  é
  é  é  é  é  é  é  é1  é4  é6  é8  é:  é=  é@  éC  éE  éH  éJ  éL  éi  éà  é¨  éŒ  éÚ  è  è<  è[  èz  èó  è†  è£  è¶  è®  è±  è∫  èΩ  èø  è¬  èƒ  è∆  è»  è’  èÿ  è€  èﬁ  è·  è„  ê   ê  ê  ê  ê  ê  ê  ê!  ê#  ê2  ê5  êB  êG  êI  êL  êQ  êS  êV  êX  êg  êt  êâ  êå  êè  êí  êï  êò  êõ  êû  ê°  ê§  êß  êº  êø  ê¡  ê√  ê≈  ê»  ê   êÃ  êŒ  ê—  ê”  ê’  êÚ  ë  ë5  ëW  ë{  ë°  ë≈  ë‰  í  í   í)  í,  í/  í1  í:  íC  íF  íI  íL  íO  íQ  í^  ía  íd  íg  íj  íl  íâ  íñ  íõ  íû  í†  í•  í®  í™  í¨  íª  íæ  íÀ  í–  í“  í’  í⁄  í‹  íﬂ  í·  í  í˝  ì  ì  ì  ì  ì  ì!  ì$  ì'  ì*  ì-  ì0  ìE  ìH  ìJ  ìL  ìN  ìQ  ìT  ìW  ìY  ì\  ì^  ì`  ì}  ìú  ì¿  ì‚  î  î,  îP  îo  îé  î´  î¥  î∑  î∫  îº  î≈  îŒ  î—  î‘  î◊  î⁄  î‹  îﬁ  îÎ  îÓ  îÒ  îÙ  î˜  î˘  ï  ï#  ï(  ï+  ï-  ï2  ï5  ï7  ï9  ïH  ïK  ïM  ïZ  ï_  ïa  ïd  ïi  ïk  ïn  ïp  ï  ïå  ï°  ï§  ïß  ï™  ï≠  ï∞  ï≥  ï∂  ïπ  ïº  ïø  ï‘  ï◊  ïŸ  ï€  ï›  ï‡  ï‚  ï‰  ïÊ  ïÈ  ïÎ  ïÌ  ñ
  ñ)  ñM  ño  ñì  ñπ  ñ›  ñ¸  ó  ó8  óA  óD  óG  óI  óR  ó[  ó^  óa  ód  óg  ói  óv  óy  ó|  ó  óÇ  óÑ  ó°  óÆ  ó≥  óµ  ó∏  óΩ  óø  ó¬  óƒ  ó”  ó÷  ó„  óË  óÍ  óÌ  óÚ  óÙ  ó˜  ó˘  ò  ò  ò*  ò-  ò0  ò3  ò6  ò9  ò<  ò?  òB  òE  òH  ò]  ò`  òb  òd  òf  òi  òl  òn  òp  òs  òu  òw  òî  ò≥  ò◊  ò˘  ô  ôC  ôg  ôÜ  ô•  ô¬  ôÀ  ôŒ  ô—  ô”  ô‹  ôÂ  ôÁ  ôÈ  ôÎ  ôÌ  ôÔ  ô¸  ôˇ  ö  ö  ö  ö
  ö'  ö4  ö9  ö<  ö>  öC  öF  öH  öJ  öY  öf  ök  öm  öp  öu  öw  öz  ö|  öã  öò  ö≠  ö∞  ö≥  ö∂  öπ  öº  öø  ö¬  ö≈  ö»  öÀ  ö‡  ö„  öÂ  öÁ  öÈ  öÏ  öÔ  öÚ  öÙ  ö˜  ö˘  ö˚  õ  õ7  õ[  õ}  õ°  õ«  õÎ  ú
  ú)  úF  úO  úR  úU  úW  ú`  úi  úl  ún  úq  ús  úu  úw  úÑ  úá  úä  úç  úê  úí  úØ  úº  ú¡  úƒ  ú∆  úÀ  úŒ  ú–  ú“  ú·  ú‰  úÌ  ú˙  úˇ  ù  ù  ù	  ù  ù  ù  ù  ù,  ùA  ùD  ùG  ùJ  ùM  ùP  ùS  ùV  ùY  ù\  ù_  ùt  ùw  ùy  ù{  ù}  ùÄ  ùÉ  ùÜ  ùà  ùã  ùç  ùè  ù¨  ùÀ  ùÔ  û  û5  û[  û  ûû  ûΩ  û⁄  û„  ûÊ  ûÈ  ûÎ  ûÙ  û˝  ü   ü  ü  ü	  ü  ü  ü  ü  ü!  ü$  ü&  üC  üP  üU  üX  üZ  ü_  üb  üd  üf  üu  üx  üÖ  üä  üå  üè  üî  üñ  üô  üõ  ü™  ü∑  üŒ  ü—  ü‘  ü◊  ü⁄  ü›  ü‡  ü„  üÊ  üÈ  üÏ  üÔ  †  †	  †  †  †  †  †  †  †  †  †  †!  †#  †@  †_  †É  †•  †…  †Ô  °  °>  °]  °|  °ô  °¢  °•  °®  °™  °≥  °º  °æ  °¿  °¬  °ƒ  °∆  °”  °÷  °Ÿ  °‹  °ﬂ  °·  °˛  ¢  ¢  ¢  ¢  ¢  ¢  ¢  ¢!  ¢0  ¢3  ¢@  ¢G  ¢J  ¢M  ¢P  ¢W  ¢Z  ¢\  ¢_  ¢a  ¢v  ¢ï  ¢≥  ¢¿  ¢≈  ¢«  ¢   ¢œ  ¢—  ¢‘  ¢÷  ¢Â  ¢Ú  £  £
  £  £  £  £  £  £  £  £"  £%  £:  £=  £?  £A  £C  £F  £H  £J  £L  £O  £Q  £S  £p  £è  £≥  £’  £˘  §  §C  §b  §Å  §û  §ß  §™  §≠  §Ø  §∏  §¡  §ƒ  §«  §   §Õ  §œ  §‹  §ﬂ  §‚  §Â  §Ë  §Í  •  •  •  •  •  •#  •&  •(  •*  •9  •<  •I  •N  •P  •S  •X  •Z  •]  •_  •n  •{  •ê  •ì  •ñ  •ô  •ú  •ü  •¢  ••  •®  •´  •Æ  •√  •∆  •»  •   •Ã  •œ  •—  •”  •’  •ÿ  •⁄  •‹  •˘  ¶  ¶<  ¶^  ¶Ç  ¶®  ¶Ã  ¶Î  ß
  ß'  ß0  ß3  ß6  ß8  ßA  ßJ  ßM  ßP  ßS  ßV  ßX  ßZ  ßg  ßj  ßm  ßp  ßs  ßu  ßí  ßü  ß§  ß¶  ß©  ßÆ  ß∞  ß≥  ßµ  ßƒ  ß«  ß‘  ßŸ  ß€  ßﬁ  ß„  ßÂ  ßË  ßÍ  ß˘  ®  ®  ®  ®!  ®$  ®'  ®*  ®-  ®0  ®3  ®6  ®9  ®N  ®Q  ®S  ®U  ®W  ®Z  ®]  ®_  ®a  ®d  ®f  ®h  ®Ö  ®§  ®»  ®Í  ©  ©4  ©X  ©w  ©ñ  ©≥  ©º  ©ø  ©¬  ©ƒ  ©Õ  ©÷  ©Ÿ  ©‹  ©ﬂ  ©‚  ©‰  ©Ò  ©Ù  ©˜  ©˙  ©˝  ©ˇ  ™  ™)  ™.  ™0  ™3  ™8  ™:  ™=  ™?  ™N  ™Q  ™^  ™c  ™e  ™h  ™m  ™o  ™r  ™t  ™É  ™ê  ™•  ™®  ™´  ™Æ  ™±  ™¥  ™∑  ™∫  ™Ω  ™¿  ™√  ™ÿ  ™€  ™›  ™ﬂ  ™·  ™‰  ™Á  ™È  ™Î  ™Ó  ™  ™Ú  ´  ´.  ´R  ´t  ´ò  ´æ  ´‚  ¨  ¨   ¨=  ¨F  ¨I  ¨L  ¨N  ¨W  ¨`  ¨c  ¨f  ¨i  ¨l  ¨n  ¨p  ¨}  ¨Ä  ¨É  ¨Ü  ¨â  ¨ã  ¨®  ¨µ  ¨∫  ¨Ω  ¨ø  ¨ƒ  ¨«  ¨…  ¨À  ¨⁄  ¨›  ¨Ê  ¨Û  ¨¯  ¨˙  ¨˝  ≠  ≠  ≠  ≠	  ≠  ≠%  ≠<  ≠?  ≠B  ≠E  ≠H  ≠K  ≠N  ≠Q  ≠T  ≠W  ≠Z  ≠]  ≠t  ≠w  ≠y  ≠{  ≠}  ≠Ä  ≠É  ≠Ü  ≠à  ≠ä  ≠ç  ≠è  ≠ë  ≠Æ  ≠Õ  ≠Ò  Æ  Æ7  Æ]  Æà  Æ¨  ÆÀ  ÆÍ  Ø  Ø  Ø  Ø  Ø  Ø!  Ø*  Ø,  Ø.  Ø0  Ø2  Ø4  ØA  ØD  ØG  ØJ  ØM  ØO  Øl  Øy  Ø~  ØÅ  ØÉ  Øà  Øã  Øç  Øè  Øû  Ø´  Ø≤  Øµ  Ø∏  Øª  Ø¬  Ø≈  Ø«  Ø   ØÃ  ØÍ  ∞	  ∞  ∞+  ∞0  ∞2  ∞5  ∞:  ∞<  ∞?  ∞A  ∞P  ∞]  ∞r  ∞u  ∞x  ∞{  ∞~  ∞Å  ∞Ñ  ∞á  ∞ä  ∞ç  ∞ê  ∞•  ∞®  ∞™  ∞¨  ∞Æ  ∞±  ∞≥  ∞µ  ∞∑  ∞∫  ∞º  ∞æ  ∞€  ∞˙  ±  ±@  ±d  ±ä  ±Æ  ±Õ  ±Ï  ≤	  ≤  ≤  ≤  ≤  ≤#  ≤,  ≤/  ≤2  ≤5  ≤8  ≤:  ≤G  ≤J  ≤M  ≤P  ≤S  ≤U  ≤r  ≤  ≤Ñ  ≤á  ≤â  ≤é  ≤ë  ≤ì  ≤ï  ≤§  ≤ß  ≤¥  ≤π  ≤ª  ≤æ  ≤√  ≤≈  ≤»  ≤   ≤Ÿ  ≤Ê  ≤˚  ≤˛  ≥  ≥  ≥  ≥
  ≥  ≥  ≥  ≥  ≥  ≥.  ≥1  ≥3  ≥5  ≥7  ≥:  ≥<  ≥>  ≥@  ≥C  ≥E  ≥G  ≥d  ≥É  ≥ß  ≥…  ≥Ì  ¥  ¥7  ¥V  ¥u  ¥í  ¥õ  ¥û  ¥°  ¥£  ¥¨  ¥µ  ¥∑  ¥π  ¥ª  ¥Ω  ¥ø  ¥Ã  ¥œ  ¥“  ¥’  ¥ÿ  ¥⁄  ¥˜  µ  µ	  µ  µ  µ  µ  µ  µ  µ)  µ+  µ8  µ=  µ?  µB  µG  µI  µL  µN  µ]  µj  µ  µÇ  µÖ  µà  µã  µé  µë  µî  µó  µö  µù  µ≤  µµ  µ∑  µπ  µª  µæ  µ¿  µ¬  µƒ  µ«  µ…  µÀ  µË  ∂  ∂+  ∂M  ∂q  ∂ó  ∂ª  ∂⁄  ∂˘  ∑  ∑  ∑"  ∑%  ∑'  ∑0  ∑9  ∑<  ∑>  ∑A  ∑C  ∑E  ∑G  ∑T  ∑W  ∑Z  ∑]  ∑`  ∑b  ∑  ∑å  ∑ë  ∑î  ∑ñ  ∑õ  ∑û  ∑†  ∑¢  ∑±  ∑¥  ∑¡  ∑∆  ∑»  ∑À  ∑–  ∑“  ∑’  ∑◊  ∑Ê  ∑Û  ∏  ∏  ∏  ∏  ∏  ∏  ∏  ∏  ∏   ∏#  ∏&  ∏;  ∏>  ∏@  ∏B  ∏D  ∏G  ∏J  ∏M  ∏O  ∏R  ∏T  ∏V  ∏s  ∏í  ∏∂  ∏ÿ  ∏¸  π"  πF  πe  πÑ  π°  π™  π≠  π∞  π≤  πª  πƒ  π«  π   πÕ  π–  π“  πﬂ  π‚  πÂ  πË  πÎ  πÌ  ∫
  ∫  ∫  ∫  ∫!  ∫&  ∫(  ∫+  ∫-  ∫<  ∫?  ∫L  ∫Q  ∫S  ∫V  ∫[  ∫]  ∫`  ∫b  ∫q  ∫~  ∫ì  ∫ñ  ∫ô  ∫ú  ∫ü  ∫¢  ∫•  ∫®  ∫´  ∫Æ  ∫±  ∫∆  ∫…  ∫À  ∫Õ  ∫œ  ∫“  ∫’  ∫◊  ∫Ÿ  ∫‹  ∫ﬁ  ∫‡  ∫˝  ª  ª@  ªb  ªÜ  ª¨  ª–  ªÔ  º  º+  º4  º7  º:  º<  ºE  ºN  ºP  ºR  ºT  ºV  ºX  ºe  ºh  ºk  ºn  ºq  ºs  ºê  ºù  º¢  º§  ºß  º¨  ºÆ  º±  º≥  º¬  º≈  º“  º◊  ºŸ  º‹  º·  º„  ºÊ  ºË  º˜  Ω  Ω  Ω  Ω  Ω"  Ω%  Ω(  Ω+  Ω.  Ω1  Ω4  Ω7  ΩL  ΩO  ΩQ  ΩS  ΩU  ΩX  ΩZ  Ω\  Ω^  Ωa  Ωc  Ωe  ΩÇ  Ω°  Ω≈  ΩÁ  æ  æ1  æU  æt  æì  æ∞  æπ  æº  æø  æ¡  æ   æ”  æ÷  æÿ  æ€  æ›  æﬂ  æ·  æÓ  æÒ  æÙ  æ˜  æ˙  æ¸  ø  ø&  ø+  ø-  ø0  ø5  ø7  ø:  ø<  øK  øN  ø[  ø`  øb  øe  øj  øl  øo  øq  øÄ  øç  ø¢  ø•  ø®  ø´  øÆ  ø±  ø¥  ø∑  ø∫  øΩ  ø¿  ø’  øÿ  ø⁄  ø‹  øﬁ  ø·  ø„  øÂ  øÁ  øÍ  øÏ  øÓ  ¿  ¿*  ¿N  ¿p  ¿î  ¿∫  ¿ﬁ  ¿˝  ¡  ¡9  ¡B  ¡E  ¡H  ¡J  ¡S  ¡\  ¡^  ¡`  ¡b  ¡d  ¡f  ¡s  ¡v  ¡y  ¡|  ¡  ¡Å  ¡û  ¡´  ¡∞  ¡≥  ¡µ  ¡∫  ¡Ω  ¡ø  ¡¡  ¡–  ¡“  ¡ﬂ  ¡‰  ¡Ê  ¡È  ¡Ó  ¡  ¡Û  ¡ı  ¬  ¬  ¬&  ¬)  ¬,  ¬/  ¬2  ¬5  ¬8  ¬;  ¬>  ¬A  ¬D  ¬Y  ¬\  ¬^  ¬`  ¬b  ¬e  ¬g  ¬i  ¬k  ¬n  ¬p  ¬r  ¬è  ¬Æ  ¬“  ¬Ù  √  √>  √b  √Å  √†  √Ω  √∆  √…  √Ã  √Œ  √◊  √‡  √„  √Â  √Ë  √Í  √Ï  √˘  √¸  √ˇ  ƒ  ƒ  ƒ  ƒ$  ƒ1  ƒ6  ƒ9  ƒ;  ƒ@  ƒC  ƒE  ƒG  ƒV  ƒY  ƒf  ƒk  ƒm  ƒp  ƒu  ƒw  ƒz  ƒ|  ƒã  ƒò  ƒ≠  ƒ∞  ƒ≥  ƒ∂  ƒπ  ƒº  ƒø  ƒ¬  ƒ≈  ƒ»  ƒÀ  ƒ‡  ƒ„  ƒÂ  ƒÁ  ƒÈ  ƒÏ  ƒÔ  ƒÚ  ƒÙ  ƒ˜  ƒ˘  ƒ˚  ≈  ≈7  ≈[  ≈}  ≈°  ≈«  ≈Î  ∆
  ∆)  ∆F  ∆O  ∆R  ∆U  ∆W  ∆`  ∆i  ∆l  ∆o  ∆r  ∆u  ∆w  ∆Ñ  ∆á  ∆ä  ∆ç  ∆ê  ∆í  ∆Ø  ∆º  ∆¡  ∆ƒ  ∆∆  ∆À  ∆Œ  ∆–  ∆“  ∆·  ∆Ó  ∆Û  ∆ı  ∆¯  ∆˝  ∆ˇ  «  «  «  «   «5  «8  «;  «>  «A  «D  «G  «J  «M  «P  «S  «h  «k  «m  «o  «q  «t  «w  «z  «|  «  «Å  «É  «†  «ø  «„  »  »)  »O  »s  »í  »±  »Œ  »◊  »⁄  »›  »ﬂ  »Ë  »Ò  »Ù  »˜  »˙  »˝  »ˇ  …  …  …  …  …  …  …  …9  …F  …K  …M  …P  …U  …W  …Z  …\  …k  …n  …p  …}  …Ç  …Ñ  …á  …å  …é  …ë  …ì  …¢  …Ø  …ƒ  …«  …   …Õ  …–  …”  …÷  …Ÿ  …‹  …ﬂ  …‚  …˜  …˙  …¸  …˛                	            -   L   p   í   ∂   ‹  À   À  À>  À[  Àd  Àg  Àj  Àl  Àu  À~  ÀÅ  ÀÑ  Àá  Àä  Àå  Àé  Àõ  Àû  À°  À§  Àß  À©  À∆  À”  Àÿ  À⁄  À›  À‚  À‰  ÀÁ  ÀÈ  À¯  À˚  Ã  Ã  Ã  Ã  Ã  Ã  Ã  Ã  Ã-  Ã:  ÃO  ÃR  ÃU  ÃX  Ã[  Ã^  Ãa  Ãd  Ãg  Ãj  Ãm  ÃÇ  ÃÖ  Ãá  Ãâ  Ãã  Ãé  Ãë  Ãî  Ãñ  Ãô  Ãõ  Ãù  Ã∫  ÃŸ  Ã˝  Õ  ÕC  Õi  Õç  Õ¨  ÕÀ  ÕË  ÕÒ  ÕÙ  Õ˜  Õ˘  Œ  Œ  Œ  Œ  Œ  Œ  Œ  Œ  Œ(  Œ+  Œ.  Œ1  Œ4  Œ6  ŒS  Œ`  Œe  Œg  Œj  Œo  Œq  Œt  Œv  ŒÖ  Œà  Œï  Œö  Œú  Œü  Œ§  Œ¶  Œ©  Œ´  Œ∫  Œ«  Œ‹  Œﬂ  Œ‚  ŒÂ  ŒË  ŒÎ  ŒÓ  ŒÒ  ŒÙ  Œ˜  Œ˙  œ  œ  œ  œ  œ  œ  œ  œ  œ!  œ$  œ&  œ(  œE  œd  œà  œ™  œŒ  œÙ  –  –7  –V  –s  –|  –  –Ç  –Ñ  –ç  –ñ  –ô  –ú  –ü  –¢  –§  –±  –¥  –∑  –∫  –Ω  –ø  –‹  –È  –Ó  –Ò  –Û  –¯  –˚  –˝  –ˇ  —  —  —  —#  —%  —(  —-  —/  —2  —4  —C  —P  —e  —h  —k  —n  —q  —t  —w  —z  —}  —Ä  —É  —ò  —õ  —ù  —ü  —°  —§  —¶  —®  —™  —≠  —Ø  —±  —Œ  —Ì  “  “3  “W  “}  “°  “¿  “ﬂ  “¸  ”  ”  ”  ”  ”  ”  ”"  ”%  ”(  ”+  ”-  ”/  ”<  ”?  ”B  ”E  ”H  ”J  ”g  ”t  ”y  ”{  ”~  ”É  ”Ö  ”à  ”ä  ”ô  ”ú  ”©  ”Æ  ”∞  ”≥  ”∏  ”∫  ”Ω  ”ø  ”Œ  ”€  ”  ”Û  ”ˆ  ”˘  ”¸  ”ˇ  ‘  ‘  ‘  ‘  ‘  ‘#  ‘&  ‘(  ‘*  ‘,  ‘/  ‘2  ‘5  ‘7  ‘:  ‘<  ‘>  ‘[  ‘z  ‘û  ‘¿  ‘‰  ’
  ’.  ’M  ’l  ’â  ’í  ’ï  ’ò  ’ö  ’£  ’¨  ’Ø  ’≤  ’µ  ’∏  ’∫  ’«  ’   ’Õ  ’–  ’”  ’’  ’Ú  ’ˇ  ÷  ÷  ÷	  ÷  ÷  ÷  ÷  ÷$  ÷'  ÷*  ÷7  ÷<  ÷>  ÷A  ÷F  ÷H  ÷K  ÷M  ÷\  ÷i  ÷~  ÷Å  ÷Ñ  ÷á  ÷ä  ÷ç  ÷ê  ÷ì  ÷ñ  ÷ô  ÷ú  ÷±  ÷¥  ÷∂  ÷∏  ÷∫  ÷Ω  ÷ø  ÷¡  ÷√  ÷∆  ÷»  ÷   ÷Á  ◊  ◊*  ◊L  ◊p  ◊ñ  ◊∫  ◊Ÿ  ◊¯  ÿ  ÿ  ÿ!  ÿ$  ÿ&  ÿ/  ÿ8  ÿ;  ÿ=  ÿ@  ÿB  ÿD  ÿQ  ÿT  ÿW  ÿZ  ÿ]  ÿ_  ÿ|  ÿâ  ÿé  ÿê  ÿì  ÿò  ÿö  ÿù  ÿü  ÿÆ  ÿ∞  ÿΩ  ÿ¬  ÿƒ  ÿ«  ÿÃ  ÿŒ  ÿ—  ÿ”  ÿ‚  ÿÔ  Ÿ  Ÿ  Ÿ
  Ÿ  Ÿ  Ÿ  Ÿ  Ÿ  Ÿ  Ÿ  Ÿ"  Ÿ7  Ÿ:  Ÿ<  Ÿ>  Ÿ@  ŸC  ŸE  ŸG  ŸI  ŸL  ŸN  ŸP  Ÿm  Ÿå  Ÿ∞  Ÿ“  Ÿˆ  ⁄  ⁄@  ⁄_  ⁄~  ⁄õ  ⁄§  ⁄ß  ⁄™  ⁄¨  ⁄µ  ⁄æ  ⁄¡  ⁄ƒ  ⁄«  ⁄   ⁄Ã  ⁄Ÿ  ⁄‹  ⁄ﬂ  ⁄‚  ⁄Â  ⁄Á  €  €  €  €  €  €   €"  €%  €'  €6  €9  €F  €K  €M  €P  €U  €W  €Z  €\  €k  €x  €  €Ç  €Ö  €à  €è  €í  €ï  €ò  €ö  €ª  €Û  ‹  ‹)  ‹,  ‹/  ‹2  ‹5  ‹7  ‹C  ‹L  ‹T  ‹]  ‹`  ‹i  ‹p  ‹u  ‹Ç  ‹Ö  ‹à  ‹ã  ‹é  ‹ê  ‹ú  ‹•  ‹Ø  ‹≤  ‹  ‹˘  ›  ›  ›  ›(  ›+  ›.  ›1  ›4  ›6  ›t  ›}  ›Ü  ›â  ›ñ  ›ô  ›ú  ›ü  ›¢  ›§  ›≤  ›æ  ›À  ›‡  ›„  ›Ê  ›È  ›Ï  ›Ô  ›Ú  ›ı  ›¯  ›˚  ›˛  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ"  ﬁ%  ﬁ(  ﬁ+  ﬁ.  ﬁ0  ﬁ;  ﬁR  ﬁk  ﬁÑ  ﬁó  ﬁÆ  ﬁ√  ﬁÕ  ﬁ‚  ﬁ˙  ﬂ  ﬂ  ﬂ_  ﬂf  ﬂ|  ﬂÉ  ﬂé  ﬂõ  ﬂ§  ﬂß  ﬂ™  ﬂ≠  ﬂ∞  ﬂπ  ﬂº  ﬂø  ﬂ¬  ﬂ≈  ﬂ«  ﬂ‡  ﬂ˚  ‡  ‡I  ‡O  ‡V  ‡X  ‡a  ‡h  ‡k  ‡n  ‡q  ‡t  ‡Å  ‡à  ‡ã  ‡é  ‡ë  ‡ò  ‡õ  ‡û  ‡°  ‡£  ‡Ã  ‡˙  ·&  ·(  ·5  ·<  ·?  ·B  ·E  ·L  ·O  ·R  ·U  ·W  ·^  ·g  ·o  Â~  ÂÅ  Âä  Âò  Âü  Â¶  Â≥  Â¿  Â√  Â∆  Â…  ÂÃ  Âœ  Â“  Âﬂ  Â‚  ÂÂ  ÂË  ÂÎ  ÂÓ  ÂÒ  ÂÛ  Ê  Ê=  Êg  Êë  Êö  Ê£  Ê¶  Êµ  Êæ  ÊÕ  Ê‘  Ê›  Ê‡  Ê‚  Ê‰  ÊÒ  ÊÙ  Êˆ  Ê˘  Ê¸  Ê˛  Á  Á
  Á  Á  Á  Á  Á  Á%  Á:  Á<  Á>  Á@  ÁB  ÁD  ÁF  ÁH  ÁJ  ÁL  ÁN  ÁP  Á]  Ál  Áo  Ár  Áu  Áx  Á{  Á~  ÁÅ  Áê  Áí  Áï  Áó  Áö  Áù  Á†  Á¢  Á§  Á¿  Áœ  Á¯  Ë#  ËE  ËÜ  Ë°  Ëæ  Ë«  ˇ  Ò  Ò  Ò"  Ò%  Ò(  Ò+  Ò.  Ò1  Ò4  Ò7  Ò:  Ò=  ÒP  ÒR  ÒU  ÒX  Ò[  Ò^  Òa  Òd  Òf  Òi  Òk  Ò~  Òë  Ò•  Ò∏  ÒÃ  ÒŸ  ÒÔ  Ú  Ú  Ú  Ú*  Ú-  Ú0  Ú3  Ú6  Ú9  Ú<  ÚI  ÚL  ÚO  ÚR  ÚU  ÚX  Ú[  Ú]  ÚÄ  Úû  ÚΩ  Ú—  Ú„  Û	  Û  Û  Û"  Û%  Û(  Û+  Û4  Û7  Û:  Û=  Û@  ÛB  ÛY  Ûm  ÛÜ  Û°  ÛÆ  ÛØ  Û∞  Û≤  ÛÕ  Û⁄  Û›  Û‡  Û„  ÛÊ  ÛË  Ù  Ù  Ù)  Ù,  Ù/   	 2 9 b « ‘ Ÿ ‹ ﬂ ‰ Á Í Ï   # , / 2 5 8 A D G J M O h á ì ® ™ ¬ « ‡ Í Ù ˛ 	        # 4 L U Z ] _ b k n o x ê ï ≠ ≈   ” Ù ˘  ' 4 7 : = @ C F S V Y [ ] ` b d  ô ≥ Œ › ˝ ˇ      ! * - 0 2 5 7 O u ©   ” ÷ Ÿ € Á Ù ˝ ˛   ; H Q T W Z ] f i l o r t ô Ω Â  . L Y b e h k n w z } Ä É Ö ú ∞ … ‰ Ò Ù ˜ ˙ ˝ ˇ  å ß ¥ ∑ ∫ Ω ¿ ¬ › Ê   	 ¥ ¡ ƒ «   Õ œ Í Û    ¡   ◊ ⁄ › ‡ „ Â 	     A J W Z ] ` b d Ç è î ó ö ü ° £ • Ω ﬂ Ï   	        ! $ ; > @ C F I L O R U X [ ] Ä ≠ ø   Ì  + ? b x á î ® ª æ « ” ÿ ‰ Ô Ò      % 2 5 8 ; > A D Q T W Z ] ` b d o É ô ß ≥ ” ’ ‚ È Ï Ô Ú ˘ ¸ ˇ    , > G R U X [ ^ a c Ñ † ƒ ÷ Ù   2 5 8 ; > A D F I b u Ñ ú Ø À Œ — ‘ ◊ ⁄ ‹     # & ) , . ; F L O R U i r ë ñ µ ¬ ≈ «   Œ € ﬁ ‡ „ Ô ¸ ˇ      " $ - C H ^ g r u x › Ê Ú ˜   7 < g } ≠ ∫ œ “ ’ ÿ € ﬁ · ‰ Á Í Ì        	                  9  X  |  û  ¬  Ë ! !+ !J !g !p !s !v !x !Å !ä !ç !ê !ì !ñ !ò !• !® !´ !Æ !± !≥ !– !› !‚ !Â !Á !Ï !Ô !Ò !Û " " " " " " "  "# "% "4 "9 "B "Y "^ "u "ñ "ô "ú "ü "¢ "• "® "™ "≠ "∆ "… "Ã "œ "“ "’ "◊ "‡ "Ì " "Û "ˆ "˘ "¸ "ˇ # # # # # #. #; #> #@ #C #M #Z #] #_ #b #i #v #y #| # #Ü #ì #ñ #ò #õ #® #´ #≠ #∞ #º #… #À #Œ #– #› #Ú #ı #¯ #˚ #˛ $ $ $ $
 $ $ $% $( $* $, $. $1 $4 $7 $9 $< $> $@ $] $| $† $¬ $Ê % %0 %O %n %ã %î %ó %ö %ú %• %Æ %± %¥ %∑ %∫ %º %… %Ã %œ %“ %’ %◊ %Ù & & &	 & & & & & && &3 &8 &: &= &B &D &G &I &X &e &Ü &â &å &è &í &ï &ò &ö &ù &∂ &π &º &ø &¬ &≈ &« &– &› &‡ &„ &Ê &È &Ï &Ô &Ò &˛ ' ' ' ' '& ') ', '/ '4 'A 'D 'F 'I 'V 'Y '\ '_ 'l 'o 'q 't 'Å 'Ñ 'Ü 'â 'ï '¢ '§ 'ß '© '∂ 'À 'Œ '— '‘ '◊ '⁄ '› '‡ '„ 'Ê 'È '˛ ( ( ( ( (
 ( ( ( ( ( ( (6 (U (y (õ (ø (Â )	 )( )G )d )m )p )s )u )~ )á )ä )ç )ê )ì )ï )¢ )• )® )´ )Æ )∞ )Õ )⁄ )ﬂ )‚ )‰ )È )Ï )Ó ) )ˇ * * * * * *$ *& *) *+ *: *[ *^ *a *c *f *i *l *n *q *ä *ç *ê *ì *ñ *ô *õ *§ *´ *Æ *± *¥ *∂ *√ *∆ *… *Ã *ﬁ *Î *Ó * *Û +  + + + + +! +# +& +( +D +o +ê +ì +ñ +ô +ú +ü +¢ +§ +ß +¿ +√ +∆ +… +Ã +œ +— +⁄ +„ +Ê +È +Ï +Ô +Ò +˛ , , , , ,* ,- ,/ ,2 ,? ,B ,D ,G ,T ,W ,Y ,\ ,h ,u ,w ,z ,| ,â ,û ,° ,§ ,ß ,™ ,≠ ,∞ ,≥ ,∂ ,π ,º ,— ,‘ ,÷ ,ÿ ,⁄ ,› ,ﬂ ,· ,„ ,Ê ,Ë ,Í - -& -J -l -ê -∂ -⁄ -˘ . .5 .> .A .D .F .O .X .[ .^ .a .d .f .s .v .y .| . .Å .û .´ .∞ .≤ .µ .∫ .º .ø .¡ .– .› .‚ .‰ .Á .Ï .Ó .Ò .Û / / / / / / /8 /; /> /_ /b /e /h /k /n /q /s /v /è /í /ï /ò /õ /û /† /© /∞ /≥ /∂ /π /ª /» /À /Œ /— /ﬁ /· /„ /Ê /Û /ˆ /¯ /˚ 0 0 0 0 0 0( 09 0< 0? 0B 0E 0H 0K 0N 0Q 0b 0e 0h 0k 0m 0p 0s 0v 0y 0{ 0ï 0° 0¥ 0œ 0› 0 1 1 1 1 1 1! 1> 1G 1J 1M 1O 1l 1å 1ô 1ö 1õ 1ù 1™ 1≠ 1∞ 1≥ 1∂ 1∏ 1  1” 1÷ 1ÿ 1⁄ 1È 1ˆ 1˜ 1¯ 1˙ 2 2 2- 22 2J 2\ 2u 2x 2{ 2~ 2Å 2Ñ 2á 2ê 2ô 2ú 2ü 2¢ 2• 2ß 2¬ 2œ 2“ 2’ 2ÿ 2Â 2Ë 2Í 2Ì 2˙ 2˝ 2ˇ 3 3 3 3, 3/ 31 33 36 3S 3\ 3z 3Å 3ü 3µ 3¬ 3≈ 3» 3À 3Œ 3– 3‰ 3Ì 3Ú 3ı 3¯ 3˚ 4 4 4 4 4 4 4" 4$ 4' 4) 47 4D 4W 4a 4j 4w 4~ 4Å 4Ñ 4á 4é 4ë 4ì 4ñ 4ò 4™ 4≥ 4¿ 4¡ 4¬ 4ƒ 4— 4⁄ 4› 4‡ 4„ 4Ê 4Ô 4Ú 4ı 4¯ 4˚ 4˝ 5 5 5 53 5@ 5I 5L 5O 5R 5U 5^ 5` 5b 5d 5f 5h 5Ü 5£ 5∆ 5ﬂ 5Ï 5Ô 5Ú 5ı 5˜ 5˘ 6 6 6 6  6# 6& 6( 6< 6E 6J 6M 6P 6S 6` 6g 6j 6m 6p 6w 6z 6| 6 6Å 6ä 6ì 6† 6ß 6™ 6≠ 6∞ 6∑ 6∫ 6º 6ø 6¡ 6À 6‘            û             6÷

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


