/== GameCatalog/App/ContentView.swift
//
//  ContentView.swift
//  GameCatalog
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
                presenter: Injection.init().provideSearchPresenter()
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
        NavigationView {
                AboutView()
          }
          .tabItem {
            Image(systemName: "person.circle")
            Text("About")
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


/== GameCatalog/App/GameCatalogApp.swift
//
//  GameCatalogApp.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 22/11/22.
//

import SwiftUI

@main
struct GameCatalogApp: App {
  let homePresenter = HomePresenter(homeUseCase: Injection.init().provideHome())
  let favoritePresenter = FavoritePresenter(favoriteUseCase: Injection.init().provideFavorite())
  let searchPresenter = Injection.init().provideSearchPresenter()

  var body: some Scene {
    WindowGroup {
      ContentView()
        .environmentObject(homePresenter)
        .environmentObject(favoritePresenter)
        .environmentObject(searchPresenter)
    }
  }
}


/== GameCatalog/Assets.xcassets/AccentColor.colorset/Contents.json
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


/== GameCatalog/Assets.xcassets/AppIcon.appiconset/Contents.json
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


/== GameCatalog/Assets.xcassets/Contents.json
{
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== GameCatalog/Assets.xcassets/assetNoFavorite.imageset/Contents.json
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


/== GameCatalog/Assets.xcassets/assetSearchMeal.imageset/Contents.json
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


/== GameCatalog/Assets.xcassets/assetSearchNotFound.imageset/Contents.json
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


/== GameCatalog/Core/DI/Injection.swift
//
//  Injection.swift
//  GameCatalog
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
    
    func provideSearchPresenter() -> SearchPresenter {
           let searchUseCase = provideSearch()
           let repository = provideRepository()
           return SearchPresenter(searchUseCase: searchUseCase, repository: repository)
       }

  func provideFavorite() -> FavoriteUseCase {
    let repository = provideRepository()
    return FavoriteInteractor(repository: repository)
  }
}


/== GameCatalog/Core/Data/GameRespository.swift
//
//  GameRepository.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Locale/Entity/CategoryEntity.swift
//
//  CategoryEntity.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Locale/Entity/GameEntity.swift
//
//  GameEntity.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Locale/Entity/IngredientEntity.swift
//
//  IngredientEntity.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Locale/Entity/MealEntity.swift
//
//  MealEntity.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Locale/LocaleDataSource.swift
//
//  LocaleDataStore.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Locale/LocaleGameDataSource.swift
//
//  LocaleGameDataSource.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/MealRepository.swift
//
//  MealRepository.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 22/11/22.
//
//
//import Foundation
//import Combine
//
//protocol MealRepositoryProtocol {
//
//  func getCategories() -> AnyPublisher<[CategoryModel], Error>
//  func getMeal(by idMeal: String) -> AnyPublisher<MealModel, Error>
//  func getMeals(by category: String) -> AnyPublisher<[MealModel], Error>
//  func searchMeal(by title: String) -> AnyPublisher<[MealModel], Error>
//  func getFavoriteMeals() -> AnyPublisher<[MealModel], Error>
//  func updateFavoriteMeal(by idMeal: String) -> AnyPublisher<MealModel, Error>
//  
////  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<[GameModel], Error>
////  func getGameDetail(by id: Int) -> AnyPublisher<GameDetailModel, Error>
////  func searchGame(by title: String) -> AnyPublisher<[GameModel], Error>
//}
//
//final class MealRepository: NSObject {
//
//  typealias MealInstance = (LocaleDataSource, RemoteDataSource) -> MealRepository
//
//  fileprivate let remote: RemoteDataSource
//  fileprivate let locale: LocaleDataSource
//
//  private init(locale: LocaleDataSource, remote: RemoteDataSource) {
//    self.locale = locale
//    self.remote = remote
//  }
//
//  static let sharedInstance: MealInstance = { localeRepo, remoteRepo in
//    return MealRepository(locale: localeRepo, remote: remoteRepo)
//  }
//
//}
//
//extension MealRepository: MealRepositoryProtocol {
//  
////  func searchGame(
////    by title: String
////  ) -> AnyPublisher<[GameModel], Error> {
////    return self.getGames(search: title)
////      .eraseToAnyPublisher()
////  }
////  
////  func getGames(
////    page: Int = 1,
////    pageSize: Int = 10,
////    search: String? = nil
////  ) -> AnyPublisher<[GameModel], Error> {
////    return self.remote.getGames(page: page, pageSize: pageSize, search: search)
////      .map { GameMapper.mapGamesResponseToModels(input: $0) }
////      .eraseToAnyPublisher()
////  }
////  
////  func getGameDetail(
////    by id: Int
////  ) -> AnyPublisher<GameDetailModel, Error> {
////    return self.remote.getGameDetail(by: id)
////      .map { GameMapper.mapGameDetailResponseToModel(input: $0) }
////      .eraseToAnyPublisher()
////  }
//
//  func getCategories() -> AnyPublisher<[CategoryModel], Error> {
//    return self.locale.getCategories()
//      .flatMap { result -> AnyPublisher<[CategoryModel], Error> in
//        if result.isEmpty {
//          return self.remote.getCategories()
//            .map { CategoryMapper.mapCategoryResponsesToEntities(input: $0) }
//            .catch { _ in self.locale.getCategories() }
//            .flatMap { self.locale.addCategories(from: $0) }
//            .filter { $0 }
//            .flatMap { _ in self.locale.getCategories()
//              .map { CategoryMapper.mapCategoryEntitiesToDomains(input: $0) }
//            }
//            .eraseToAnyPublisher()
//        } else {
//          return self.locale.getCategories()
//            .map { CategoryMapper.mapCategoryEntitiesToDomains(input: $0) }
//            .eraseToAnyPublisher()
//        }
//      }.eraseToAnyPublisher()
//  }
//
//  func getMeal(
//    by idMeal: String
//  ) -> AnyPublisher<MealModel, Error> {
//    return self.locale.getMeal(by: idMeal)
//      .flatMap { result -> AnyPublisher<MealModel, Error> in
//        if result.ingredients.isEmpty {
//          return self.remote.getMeal(by: idMeal)
//            .map { MealMapper.mapDetailMealResponseToEntity(by: idMeal, input: $0) }
//            .catch { _ in self.locale.getMeal(by: idMeal) }
//            .flatMap { self.locale.updateMeal(by: idMeal, meal: $0) }
//            .filter { $0 }
//            .flatMap { _ in self.locale.getMeal(by: idMeal)
//              .map { MealMapper.mapDetailMealEntityToDomain(input: $0) }
//            }.eraseToAnyPublisher()
//        } else {
//          return self.locale.getMeal(by: idMeal)
//            .map { MealMapper.mapDetailMealEntityToDomain(input: $0) }
//            .eraseToAnyPublisher()
//        }
//      }.eraseToAnyPublisher()
//  }
//
//  func getMeals(
//    by category: String
//  ) -> AnyPublisher<[MealModel], Error> {
//    return self.locale.getMeals(by: category)
//      .flatMap { result -> AnyPublisher<[MealModel], Error> in
//        if result.isEmpty {
//          return self.remote.getMeals(by: category)
//            .map { MealMapper.mapMealResponsesToEntities(by: category, input: $0) }
//            .catch { _ in self.locale.getMeals(by: category) }
//            .flatMap { self.locale.addMeals(by: category, from: $0) }
//            .filter { $0 }
//            .flatMap { _ in self.locale.getMeals(by: category)
//              .map {  MealMapper.mapMealEntitiesToDomains(input: $0) }
//            }.eraseToAnyPublisher()
//        } else {
//          return self.locale.getMeals(by: category)
//            .map { MealMapper.mapMealEntitiesToDomains(input: $0) }
//            .eraseToAnyPublisher()
//        }
//      }.eraseToAnyPublisher()
//  }
//
//  func searchMeal(
//    by title: String
//  ) -> AnyPublisher<[MealModel], Error> {
//    return self.remote.searchMeal(by: title)
//      .map { MealMapper.mapDetailMealResponseToEntity(input: $0) }
//      .catch { _ in self.locale.getMealsBy(title) }
//      .flatMap { responses  in
//        self.locale.getMealsBy(title)
//          .flatMap { locale -> AnyPublisher<[MealModel], Error> in
//            if responses.count > locale.count {
//              return self.locale.addMealsBy(title, from: responses)
//                .filter { $0 }
//                .flatMap { _ in self.locale.getMealsBy(title)
//                  .map { MealMapper.mapDetailMealEntityToDomains(input: $0) }
//                }.eraseToAnyPublisher()
//            } else {
//              return self.locale.getMealsBy(title)
//                .map { MealMapper.mapDetailMealEntityToDomains(input: $0) }
//                .eraseToAnyPublisher()
//            }
//          }
//      }.eraseToAnyPublisher()
//  }
//
//  func getFavoriteMeals() -> AnyPublisher<[MealModel], Error> {
//    return self.locale.getFavoriteMeals()
//      .map { MealMapper.mapMealEntitiesToDomains(input: $0) }
//      .eraseToAnyPublisher()
//  }
//
//  func updateFavoriteMeal(
//    by idMeal: String
//  ) -> AnyPublisher<MealModel, Error> {
//    return self.locale.updateFavoriteMeal(by: idMeal)
//      .map { MealMapper.mapDetailMealEntityToDomain(input: $0) }
//      .eraseToAnyPublisher()
//  }
//
//}


/== GameCatalog/Core/Data/Remote/RemoteDataSource.swift
//
//  RemoteDataSource.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 22/11/22.
//
//
//import Foundation
//import Alamofire
//import Combine
//
//protocol RemoteDataSourceProtocol: AnyObject {
//
//  func getCategories() -> AnyPublisher<[CategoryResponse], Error>
//  func getMeal(by id: String) -> AnyPublisher<MealResponse, Error>
//  func getMeals(by category: String) -> AnyPublisher<[MealResponse], Error>
//  func searchMeal(by title: String) -> AnyPublisher<[MealResponse], Error>
//  
//  func getGames(page: Int, pageSize: Int, search: String?) -> AnyPublisher<GamesResponse, Error>
//  func getGameDetail(by id: Int) -> AnyPublisher<GameDetailResponse, Error>
//
//}
//
//final class RemoteDataSource: NSObject {
//
//  private override init() { }
//
//  static let sharedInstance: RemoteDataSource =  RemoteDataSource()
//
//}
//
//extension RemoteDataSource: RemoteDataSourceProtocol {
//
//  func getGames(
//    page: Int = 1,
//    pageSize: Int = 10,
//    search: String? = nil
//  ) -> AnyPublisher<GamesResponse, Error> {
//    return Future<GamesResponse, Error> { completion in
//      var urlString = Endpoints.Gets.games.url
//      urlString += "&page=\(page)&page_size=\(pageSize)"
//      if let searchQuery = search {
//        urlString += "&search=\(searchQuery)"
//      }
//      
//      if let url = URL(string: urlString) {
//        AF.request(url)
//          .validate()
//          .responseDecodable(of: GamesResponse.self) { response in
//            switch response.result {
//            case .success(let value):
//              completion(.success(value))
//            case .failure:
//              completion(.failure(URLError.invalidResponse))
//            }
//          }
//      }
//    }.eraseToAnyPublisher()
//  }
//  
//  func getGameDetail(
//    by id: Int
//  ) -> AnyPublisher<GameDetailResponse, Error> {
//    return Future<GameDetailResponse, Error> { completion in
//      if let url = URL(string: Endpoints.Gets.gameDetail(id: id).url) {
//        AF.request(url)
//          .validate()
//          .responseDecodable(of: GameDetailResponse.self) { response in
//            switch response.result {
//            case .success(let value):
//              completion(.success(value))
//            case .failure:
//              completion(.failure(URLError.invalidResponse))
//            }
//          }
//      }
//    }.eraseToAnyPublisher()
//  }
//
//
//  func getCategories() -> AnyPublisher<[CategoryResponse], Error> {
//    return Future<[CategoryResponse], Error> { completion in
//      if let url = URL(string: Endpoints.Gets.categories.url) {
//        AF.request(url)
//          .validate()
//          .responseDecodable(of: CategoriesResponse.self) { response in
//            switch response.result {
//            case .success(let value):
//              completion(.success(value.categories))
//            case .failure:
//              completion(.failure(URLError.invalidResponse))
//            }
//          }
//      }
//    }.eraseToAnyPublisher()
//  }
//
//  func getMeal(
//    by id: String
//  ) -> AnyPublisher<MealResponse, Error> {
//    return Future<MealResponse, Error> { completion in
//      if let url = URL(string: Endpoints.Gets.meal.url + id) {
//        AF.request(url)
//          .validate()
//          .responseDecodable(of: MealsResponse.self) { response in
//            switch response.result {
//            case .success(let value):
//              completion(.success(value.meals[0]))
//            case .failure:
//              completion(.failure(URLError.invalidResponse))
//            }
//          }
//      }
//    }.eraseToAnyPublisher()
//  }
//
//  func getMeals(
//    by category: String
//  ) -> AnyPublisher<[MealResponse], Error> {
//    return Future<[MealResponse], Error> { completion in
//      if let url = URL(string: Endpoints.Gets.meals.url + category) {
//        AF.request(url)
//          .validate()
//          .responseDecodable(of: MealsResponse.self) { response in
//            switch response.result {
//            case .success(let value):
//              completion(.success(value.meals))
//            case .failure:
//              completion(.failure(URLError.invalidResponse))
//            }
//          }
//      }
//    }.eraseToAnyPublisher()
//  }
//
//  func searchMeal(
//    by title: String
//  ) -> AnyPublisher<[MealResponse], Error> {
//    return Future<[MealResponse], Error> { completion in
//      if let url = URL(string: Endpoints.Gets.search.url + title) {
//        AF.request(url)
//          .validate()
//          .responseDecodable(of: MealsResponse.self) { response in
//            switch response.result {
//            case .success(let value):
//              completion(.success(value.meals))
//            case .failure:
//              completion(.failure(URLError.invalidResponse))
//            }
//          }
//      }
//    }.eraseToAnyPublisher()
//  }
//}


/== GameCatalog/Core/Data/Remote/RemoteGameDataSource.swift
//
//  RemoteGameDataSource.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import Foundation
import Combine
import Alamofire

protocol RemoteGameDataSourceProtocol {
  func getGames() -> AnyPublisher<[GameModel], Error>
  func getGameDetail(id: Int) -> AnyPublisher<GameModel, Error>
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error>
}

// Define a custom error enum for cleaner error handling
enum NetworkError: Error {
    case invalidURL
    case decodingError
    case serverError
    case noData
    case emptyQuery
}

// Network Logger for Alamofire
class NetworkLogger: EventMonitor {
    let queue = DispatchQueue(label: "com.GameCatalog.networklogger")
    
    // Log when a request starts
    func requestDidResume(_ request: Request) {
        print("REQUEST STARTED: \(request.description)")
        
        // Log headers
        if let headers = request.request?.allHTTPHeaderFields, !headers.isEmpty {
            print("Headers: \(headers)")
        }
        
        // Log HTTP body if present
        if let httpBody = request.request?.httpBody, let bodyString = String(data: httpBody, encoding: .utf8) {
            print("Body: \(bodyString)")
        }
    }
    
    // Log when a request finishes
    func request<Value>(_ request: DataRequest, didParseResponse response: DataResponse<Value, AFError>) {
        print("\n RESPONSE RECEIVED: \(request.description)")
        
        // Log status code
        if let statusCode = response.response?.statusCode {
            let emojiStatus = statusCode >= 200 && statusCode < 300 ? "✅" : "❌"
            print("\(emojiStatus) Status Code: \(statusCode)")
        }
        
        // Log headers
        if let headers = response.response?.allHeaderFields {
//            print("Response Headers: \(headers)")
        }
        
        // Log the response data
        switch response.result {
        case .success:
            if let data = response.data, !data.isEmpty {
//                print("original data")
//                print(data)
                if let json = try? JSONSerialization.jsonObject(with: data),
                   let prettyData = try? JSONSerialization.data(withJSONObject: json, options: .prettyPrinted),
                   let prettyString = String(data: prettyData, encoding: .utf8) {
                    // Truncate large responses for readability
//                    print("original data: \(prettyString)")
                    let truncated = prettyString.count > 1000 ? prettyString.prefix(1000) + "...(truncated)" : prettyString
//                    print("Response Data: \(truncated)")
                } else if let string = String(data: data, encoding: .utf8) {
                    let truncated = string.count > 500 ? string.prefix(500) + "...(truncated)" : string
                    print("Response Data (not JSON): \(truncated)")
                }
            } else {
                print("esponse Data: Empty")
            }
        case .failure(let error):
            print("Response Error: \(error.localizedDescription)")
            if let responseData = response.data, let string = String(data: responseData, encoding: .utf8) {
                print("Error Response Data: \(string)")
            }
        }
        
        print("Request Duration: \(String(format: "%.2f", request.metrics?.taskInterval.duration ?? 0)) seconds")
        print("Request Completed: \(Date())\n")
    }
    
    // Log request retries
    func request(_ request: Request, didRetrieveCachedResponse response: CachedURLResponse) {
        print("Retrieved response from cache for: \(request)")
    }
    
    // Log errors
    func request(_ request: Request, didFailToCreateURLRequestWithError error: AFError) {
        print("Failed to create request: \(error)")
    }
    
    func request(_ request: Request, didFailTask task: URLSessionTask, earlyWithError error: AFError) {
        print("Request \(request) failed early with error: \(error)")
    }
    
    func request(_ request: Request, didFailToValidateResponse response: HTTPURLResponse, data: Data?, withError error: AFError) {
        print("Request \(request) failed validation with error: \(error)")
    }
}

final class RemoteGameDataSource: NSObject {
  
  private override init() {
    // Initialize Alamofire session with the logger
    let configuration = URLSessionConfiguration.default
    configuration.timeoutIntervalForRequest = 30
    
    // Create the Alamofire session with our logger
    session = Session(configuration: configuration, eventMonitors: [NetworkLogger()])
    
    super.init()
  }
  
  static let sharedInstance: RemoteGameDataSource = RemoteGameDataSource()
  
  // Use the API values from APICall.swift instead of hardcoding
  private let baseUrl = API.gameBaseUrl
  private let apiKey = API.gameApiKey
  
  // Alamofire session
  private let session: Session
  
  // Create a JSON decoder with custom strategies for snake_case to camelCase conversion
  private lazy var decoder: JSONDecoder = {
    let decoder = JSONDecoder()
    decoder.keyDecodingStrategy = .convertFromSnakeCase
    return decoder
  }()
  
  // Creates standard headers for API requests
  private var standardHeaders: HTTPHeaders {
    return [
        "Content-Type": "application/json",
        "Accept": "application/json"
    ]
  }
}

extension RemoteGameDataSource: RemoteGameDataSourceProtocol {
  
  func getGames() -> AnyPublisher<[GameModel], Error> {
    // Use the endpoint from Endpoints enum
    guard let url = URL(string: Endpoints.Gets.games.url) else {
        return Fail(error: NetworkError.invalidURL).eraseToAnyPublisher()
    }
    
    print("Requesting games from URL: \(Endpoints.Gets.games.url)")
    
    return session.request(
        url,
        method: .get,
        headers: standardHeaders
    )
    .validate()
    .publishData()
    .tryMap { response -> Data in
        print("Response status code: \(response.response?.statusCode ?? 0)")
        
        guard let data = response.data, !data.isEmpty else {
            print("Response contains no data")
            throw NetworkError.noData
        }
        
        // Debug: Print sample of response for verification
        if let responseString = String(data: data, encoding: .utf8) {
            if !responseString.contains("\"results\"") {
                print("Warning: Response does not contain 'results' key")
                print("Full Response: \(responseString)")
            }
        }
        
        return data
    }
    .decode(type: GamesListResponse.self, decoder: JSONDecoder())
    .map { response in
        print("Successfully decoded response with \(response.results.count) games")
           if let firstGame = response.results.first {
               print("First game details:")
               print("- ID: \(firstGame.id)")
               print("- Name: \(firstGame.name)")
               print("- Background Image: \(firstGame.backgroundImage ?? "nil")")
               // Print other properties as needed
               
               // Introspect the object to see all available properties
               let mirror = Mirror(reflecting: firstGame)
               print("All properties in first game:")
               for (label, value) in mirror.children {
                   print("  \(label ?? "unknown"): \(value)")
               }
           }
        return GameMapper.mapGameResponsesToDomainModels(input: response.results)
    }
    .mapError { error -> Error in
        self.handleNetworkError(error, operation: "Get Games")
    }
    .eraseToAnyPublisher()
  }
  
  func getGameDetail(id: Int) -> AnyPublisher<GameModel, Error> {
    guard let url = URL(string: Endpoints.Gets.gameDetail(id: id).url) else {
        return Fail(error: NetworkError.invalidURL).eraseToAnyPublisher()
    }
    
    print("Requesting game detail for ID \(id) from URL: \(url.absoluteString)")
    
    return session.request(
        url,
        method: .get,
        headers: standardHeaders
    )
    .validate()
    .publishData()
    .tryMap { response -> Data in
        print("Response status code: \(response.response?.statusCode ?? 0)")
        
        guard let data = response.data, !data.isEmpty else {
            print("Response contains no data")
            throw NetworkError.noData
        }
        
        return data
    }
    .decode(type: GameDetailResponse.self, decoder: JSONDecoder())
    .map { response in
        print("Successfully decoded game detail for ID \(id)")
        return GameMapper.mapDetailResponseToDomainModel(input: response)
    }
    .mapError { error -> Error in
        self.handleNetworkError(error, operation: "Get Game Detail")
    }
    .eraseToAnyPublisher()
  }
  
  func searchGames(query: String) -> AnyPublisher<[GameModel], Error> {
    // Ensure the query isn't empty
    let trimmedQuery = query.trimmingCharacters(in: .whitespacesAndNewlines)
    guard !trimmedQuery.isEmpty else {
        print("Search query is empty")
        return Fail(error: NetworkError.emptyQuery).eraseToAnyPublisher()
    }
    
    // URL encode the search query for safety
    guard let encodedQuery = trimmedQuery.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed) else {
        return Fail(error: NetworkError.invalidURL).eraseToAnyPublisher()
    }
    
    // Create the base URL
    guard let url = URL(string: API.gameBaseUrl + "games") else {
        return Fail(error: NetworkError.invalidURL).eraseToAnyPublisher()
    }
    
    // Prepare parameters with proper encoding
    let parameters: [String: Any] = [
        "key": API.gameApiKey,
        "search": encodedQuery,
        "page_size": 20  // Limit results for better performance
    ]
    
    print("🔍 Searching games with query: '\(trimmedQuery)'")
    
    return session.request(
        url,
        method: .get,
        parameters: parameters,
        encoding: URLEncoding.queryString,  // Ensures proper URL parameter formatting
        headers: standardHeaders
    )
    .validate()
    .publishData()
    .tryMap { response -> Data in
        print("Search response status code: \(response.response?.statusCode ?? 0)")
        
        guard let data = response.data, !data.isEmpty else {
            print("Search response contains no data")
            throw NetworkError.noData
        }
        
        return data
    }
    .decode(type: GamesListResponse.self, decoder: JSONDecoder())
    .map { response in
        print("Successfully decoded search response with \(response.results.count) results")
        return GameMapper.mapGameResponsesToDomainModels(input: response.results)
    }
    .mapError { error -> Error in
        self.handleNetworkError(error, operation: "Search Games")
    }
    .eraseToAnyPublisher()
  }
  
  // Helper method to standardize error handling across all request types
  private func handleNetworkError(_ error: Error, operation: String) -> Error {
    if let afError = error as? AFError {
        print("\(operation) - Alamofire Error: \(afError.localizedDescription)")
        
        if let underlyingError = afError.underlyingError {
            print("  └ Underlying error: \(underlyingError)")
        }
        
        if let responseCode = afError.responseCode {
            print("  └ Response code: \(responseCode)")
        }
        
        return NetworkError.serverError
    } else if let decodingError = error as? DecodingError {
        print("\(operation) - Decoding Error: \(decodingError)")
        
        // Provide detailed context for decoding errors
        switch decodingError {
        case .keyNotFound(let key, let context):
            print("  └ Missing key: \(key.stringValue)")
            print("  └ Context: \(context.debugDescription)")
            print("  └ Coding path: \(context.codingPath.map { $0.stringValue })")
        case .typeMismatch(let type, let context):
            print("  └ Type mismatch: expected \(type)")
            print("  └ Context: \(context.debugDescription)")
            print("  └ Coding path: \(context.codingPath.map { $0.stringValue })")
        case .valueNotFound(let type, let context):
            print("  └ Value missing: expected \(type)")
            print("  └ Context: \(context.debugDescription)")
        case .dataCorrupted(let context):
            print("  └ Data corrupted: \(context.debugDescription)")
        @unknown default:
            print("  └ Unknown decoding error")
        }
        
        return NetworkError.decodingError
    }
    
    print("\(operation) - Unknown Error: \(error.localizedDescription)")
    return error
  }
}


/== GameCatalog/Core/Data/Remote/Response/CategoriesResponse.swift
//
//  CategoryResponse.swift
//  GameCatalog
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


/== GameCatalog/Core/Data/Remote/Response/GameDetailResponse.swift
//
//  GameDetailResponse.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

//import Foundation

//struct GameDetailResponse: Decodable {
//  let id: Int
//  let name: String
//  let slug: String? // Add missing field that might be in the response
//  let released: String?
//  let backgroundImage: String?
//  let rating: Double
//  let ratingsCount: Int? // Make this optional to handle missing field
//  let description: String?
//  let genres: [GenreResponse]
//  let platforms: [PlatformWrapper]
//  
//  enum CodingKeys: String, CodingKey {
//    case id, name, slug
//    case released
//    case backgroundImage = "background_image"
//    case rating
//    case ratingsCount = "ratings_count"
//    case description = "description_raw"
//    case genres
//    case platforms
//  }
//}


/== GameCatalog/Core/Data/Remote/Response/GamesResponse.swift
//
//  GameResponseModels.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import Foundation

struct GamesListResponse: Decodable {
    let count: Int
    let next: String?
    let previous: String?
    let results: [GameResponse]
}

struct GameDetailResponse: Decodable {
    let id: Int
    let slug: String
    let name: String
    let released: String?
    let backgroundImage: String?
    let rating: Double
    let ratingsCount: Int?
    let description: String?
    let genres: [GenreResponse]
    let platforms: [PlatformWrapper]
    
    enum CodingKeys: String, CodingKey {
        case id, slug, name, released, rating, genres, platforms
        case backgroundImage = "background_image"
        case ratingsCount = "ratings_count"
        case description = "description_raw"
    }
}

struct GameResponse: Decodable {
    let id: Int
    let slug: String
    let name: String
    let released: String?
    let backgroundImage: String?
    let rating: Double
    let ratingsCount: Int?
    let description: String?
    let genres: [GenreResponse]?
    let platforms: [PlatformWrapper]?
    let tags: [TagResponse]?
    let metacritic: Int?
    
    enum CodingKeys: String, CodingKey {
        case id, slug, name, released, rating, genres, platforms, tags, metacritic
        case backgroundImage = "background_image"
        case ratingsCount = "ratings_count"
        case description = "description_raw"
    }
    
    // Custom initializer to help with debugging missing or incorrect fields
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        // Decode required fields and log any errors
        id = try container.decode(Int.self, forKey: .id)
        slug = try container.decode(String.self, forKey: .slug)
        name = try container.decode(String.self, forKey: .name)
        
        // Decode optional fields with better logging
        do {
            backgroundImage = try container.decodeIfPresent(String.self, forKey: .backgroundImage)
            print("📸 Decoded backgroundImage for \(name): \(backgroundImage ?? "nil")")
        } catch {
            print("⚠️ Error decoding backgroundImage for \(name): \(error)")
            backgroundImage = nil
        }
        
        released = try container.decodeIfPresent(String.self, forKey: .released)
        rating = try container.decode(Double.self, forKey: .rating)
        ratingsCount = try container.decodeIfPresent(Int.self, forKey: .ratingsCount)
        description = try container.decodeIfPresent(String.self, forKey: .description)
        genres = try container.decodeIfPresent([GenreResponse].self, forKey: .genres)
        platforms = try container.decodeIfPresent([PlatformWrapper].self, forKey: .platforms)
        tags = try container.decodeIfPresent([TagResponse].self, forKey: .tags)
        metacritic = try container.decodeIfPresent(Int.self, forKey: .metacritic)
    }
}

struct GenreResponse: Decodable {
    let id: Int
    let name: String
    let slug: String?
}

struct PlatformWrapper: Decodable {
    let platform: PlatformResponse
}

struct PlatformResponse: Decodable {
    let id: Int
    let name: String
    let slug: String?
}

struct TagResponse: Decodable {
    let id: Int
    let name: String
    let slug: String?
}


/== GameCatalog/Core/Data/Remote/Response/MealsResponse.swift
//
//  MealsResponse.swift
//  GameCatalog
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


/== GameCatalog/Core/Domain/Model/CategoryModel.swift
//
//  CategoryModel.swift
//  GameCatalog
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


/== GameCatalog/Core/Domain/Model/GameDetailModel.swift
//
//  GameDetailModel.swift
//  GameCatalog
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

/== GameCatalog/Core/Domain/Model/GameModel.swift
//
//  GameModel.swift
//  GameCatalog
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
    var isFavorite: Bool
    
    // Computed property to handle empty or invalid image URLs
    var validBackgroundImageURL: URL? {
        // Ensure the URL string is not empty
        guard !backgroundImage.isEmpty else {
            return nil
        }
        
        // Check if the URL is valid
        if let url = URL(string: backgroundImage) {
            return url
        }
        
        // If the URL has special characters that need encoding
        if let encodedURLString = backgroundImage.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed),
           let url = URL(string: encodedURLString) {
            return url
        }
        
        return nil
    }
    
    // Helper to get a default placeholder if no image is available
    var imageURLOrPlaceholder: URL {
        return validBackgroundImageURL ?? URL(string: "https://via.placeholder.com/600x400?text=No+Image")!
    }
}


/== GameCatalog/Core/Domain/Model/IngredientModel.swift
//
//  IngredientModel.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//

//import Foundation
//
//struct IngredientModel: Equatable, Identifiable {
//
//  let id: String
//  let title: String
//  let idMeal: String
//
//}


/== GameCatalog/Core/Domain/Model/MealModel.swift
//
//  MealModel.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//
//
//import Foundation
//
//struct MealModel: Equatable, Identifiable {
//
//  let id: String
//  let title: String
//  let image: String
//  var category: String = ""
//  var area: String = ""
//  var instructions: String = ""
//  var tag: String = ""
//  var youtube: String = ""
//  var source: String = ""
//  var ingredients: [IngredientModel] = []
//  var favorite: Bool = false
//
//}


/== GameCatalog/Core/Domain/UseCase/DetailInteractor.swift
//
//  DetailUseCase.swift
//  GameCatalog
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


/== GameCatalog/Core/Domain/UseCase/FavoriteInteractor.swift
//
//  FavoriteUseCase.swift
//  GameCatalog
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


/== GameCatalog/Core/Domain/UseCase/HomeInteractor.swift
//
//  HomeUseCase.swift
//  GameCatalog
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


/== GameCatalog/Core/Domain/UseCase/MealInteractor.swift
//
//  MealInteractor.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//
//
//import Foundation
//import Combine
//
//protocol MealUseCase {
//
//  func getMeal() -> AnyPublisher<MealModel, Error>
//  func getMeal() -> MealModel
//  func updateFavoriteMeal() -> AnyPublisher<MealModel, Error>
////  func getGame() -> AnyPublisher<GameDetailModel, Error>
//
//}
//
//class MealInteractor: MealUseCase {
//
//  private let repository: MealRepositoryProtocol
//  private let meal: MealModel
//  private let game: GameModel
//
//  required init(
//    repository: MealRepositoryProtocol,
//    meal: MealModel,
//    game: GameModel
//  ) {
//    self.repository = repository
//    self.meal = meal
//    self.game = game
//  }
//
//  func getMeal() -> AnyPublisher<MealModel, Error> {
//    return repository.getMeal(by: meal.id)
//  }
//
//  func getMeal() -> MealModel {
//    return meal
//  }
//
//  func updateFavoriteMeal() -> AnyPublisher<MealModel, Error> {
//    return repository.updateFavoriteMeal(by: meal.id)
//  }
//    
//
//  func getGame() -> AnyPublisher<GameDetailModel, Error> {
//      return repository.getGameDetail(by: game.id)
//  }
//    
//  func getGame() -> GameModel {
//        return game
//  }
//}


/== GameCatalog/Core/Domain/UseCase/SearchInteractor.swift
//
//  SearchUseCase.swift
//  GameCatalog
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


/== GameCatalog/Core/Utils/AppLogger.swift
//
//  AppLogger.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import Foundation
import os.log

class AppLogger {
    
    enum Category: String {
        case network = "Network"
        case database = "Database"
        case uiFlow = "UI"
        case general = "General"
    }
    
    static let shared = AppLogger()
    private let subsystem = Bundle.main.bundleIdentifier ?? "com.GameCatalog"
    
    private init() {}
    
    func getLogger(category: Category) -> Logger {
        return Logger(subsystem: subsystem, category: category.rawValue)
    }
    
    func getLogger(category: String) -> Logger {
        return Logger(subsystem: subsystem, category: category)
    }
    
    // Network specific logging helpers
    
    static func logRequest(_ request: URLRequest) {
        let logger = AppLogger.shared.getLogger(category: .network)
        
        let method = request.httpMethod ?? "Unknown"
        let url = request.url?.absoluteString ?? "Unknown URL"
        
        var logMessage = " \(method) \(url)"
        
        if let headers = request.allHTTPHeaderFields, !headers.isEmpty {
            logMessage += "\nHeaders: \(headers)"
        }
        
        if let body = request.httpBody, let bodyString = String(data: body, encoding: .utf8) {
            let truncatedBody = bodyString.count > 500 ? bodyString.prefix(500) + "..." : bodyString
            logMessage += "\nBody: \(truncatedBody)"
        }
        
        logger.info("\(logMessage)")
    }
    
    static func logResponse(data: Data?, response: URLResponse?, error: Error?) {
        let logger = AppLogger.shared.getLogger(category: .network)
        
        if let error = error {
            logger.error("Network error: \(error.localizedDescription)")
            return
        }
        
        guard let httpResponse = response as? HTTPURLResponse else {
            logger.error("Invalid response type")
            return
        }
        
        let url = httpResponse.url?.absoluteString ?? "Unknown URL"
        let statusCode = httpResponse.statusCode
        
        var logMessage = "📱 Response: \(statusCode) for \(url)"
        
        if let data = data, !data.isEmpty, statusCode != 204 /* No Content */ {
            if let json = try? JSONSerialization.jsonObject(with: data, options: []),
               let jsonData = try? JSONSerialization.data(withJSONObject: json, options: [.prettyPrinted]),
               let jsonString = String(data: jsonData, encoding: .utf8) {
                
                let truncatedJson = jsonString.count > 1000 ? jsonString.prefix(1000) + "..." : jsonString
                logMessage += "\nResponse Body: \(truncatedJson)"
            } else if let string = String(data: data, encoding: .utf8) {
                let truncatedString = string.count > 500 ? string.prefix(500) + "..." : string
                logMessage += "\nResponse Body: \(truncatedString)"
            } else {
                logMessage += "\nResponse Body: Binary data of \(data.count) bytes"
            }
        }
        
        if (200...299).contains(statusCode) {
            logger.info("\(logMessage)")
        } else {
            logger.error("\(logMessage)")
        }
    }
    
    // Common error types
    
    static func logDecodeError(_ error: DecodingError, context: String = "") {
        let logger = AppLogger.shared.getLogger(category: .network)
        let contextPrefix = context.isEmpty ? "" : "[\(context)] "
        
        switch error {
        case .dataCorrupted(let context):
            logger.error("\(contextPrefix)Decoding error - corrupted data: \(context.debugDescription)")
            
        case .keyNotFound(let key, let context):
            logger.error("\(contextPrefix)Decoding error - key not found: \(key.stringValue), at path: \(context.codingPath.map { $0.stringValue }.joined(separator: "."))")
            
        case .typeMismatch(let type, let context):
            logger.error("\(contextPrefix)Decoding error - type mismatch: expected \(type), at path: \(context.codingPath.map { $0.stringValue }.joined(separator: "."))")
            
        case .valueNotFound(let type, let context):
            logger.error("\(contextPrefix)Decoding error - value not found: expected \(type), at path: \(context.codingPath.map { $0.stringValue }.joined(separator: "."))")
            
        @unknown default:
            logger.error("\(contextPrefix)Unknown decoding error: \(error.localizedDescription)")
        }
    }
}


/== GameCatalog/Core/Utils/Extensions/Color+Ext.swift
//
//  Color+Ext.swift
//  GameCatalog
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


/== GameCatalog/Core/Utils/Extensions/CustomeError+Ext.swift
//
//  CustomeError+Ext.swift
//  GameCatalog
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


/== GameCatalog/Core/Utils/Mapper/CategoryMapper.swift
//
//  CategoryMapper.swift
//  GameCatalog
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


/== GameCatalog/Core/Utils/Mapper/GameMapper.swift
//
//  GameMapper.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import Foundation
import RealmSwift

final class GameMapper {
    
    static func mapGameResponsesToDomainModels(
        input gameResponses: [GameResponse]
    ) -> [GameModel] {
        let mappedModels = gameResponses.map { mapGameResponseToDomainModel(input: $0) }
        
        // Log mapping results for debugging
        print("Mapped \(mappedModels.count) games")
        if let firstGame = mappedModels.first {
            print("First game mapped image URL: \(firstGame.backgroundImage)")
            print("Is valid URL: \(firstGame.validBackgroundImageURL != nil ? "Yes" : "No")")
        }
        
        return mappedModels
    }
    
    static func mapGameResponseToDomainModel(
        input response: GameResponse,
        isFavorite: Bool = false
    ) -> GameModel {
        // Handle optional arrays with nil coalescing
        let genreNames = response.genres?.map { $0.name } ?? []
        let platformNames = response.platforms?.map { $0.platform.name } ?? []
        
        print("background image di mapGameResponseToDomainModel: \(response.backgroundImage ?? "nil")")
        
        return GameModel(
            id: response.id,
            name: response.name,
            released: response.released ?? "Unknown",
            backgroundImage: response.backgroundImage ?? "https://via.placeholder.com/600x400?text=No+Image",
            rating: response.rating,
            ratingCount: response.ratingsCount ?? 0,
            description: response.description ?? "No description available",
            genres: genreNames,
            platforms: platformNames,
            isFavorite: isFavorite
        )
    }
    
    static func mapDetailResponseToDomainModel(
        input response: GameDetailResponse,
        isFavorite: Bool = false
    ) -> GameModel {
        // For non-optional arrays in GameDetailResponse
        let genreNames = response.genres.map { $0.name }
        let platformNames = response.platforms.map { $0.platform.name }
        
        // Properly handle the background image URL
        let backgroundImageURL = response.backgroundImage ?? ""
        
        // Log the background image URL for this specific game detail
        print("Mapping game detail: \(response.name)")
        print("Original background image: \(response.backgroundImage ?? "nil")")
        
        return GameModel(
            id: response.id,
            name: response.name,
            released: response.released ?? "Unknown",
            backgroundImage: response.backgroundImage ?? "https://via.placeholder.com/600x400?text=No+Image",
            rating: response.rating,
            ratingCount: response.ratingsCount ?? 0,
            description: response.description ?? "No description available",
            genres: genreNames,
            platforms: platformNames,
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


/== GameCatalog/Core/Utils/Mapper/IngredientMapper.swift
//
//  IngredientMapper.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//

//import RealmSwift
//
//final class IngredientMapper {
//
//  static func mapIngredientEntitiesToDomains(
//    input ingredientEntities: [IngredientEntity]
//  ) -> [IngredientModel] {
//    return ingredientEntities.map { result in
//      return IngredientModel(
//        id: result.id,
//        title: result.title,
//        idMeal: result.idMeal
//      )
//    }
//  }
//
//  static func mapIngredientResponseToEntities(
//     by idMeal: String,
//     input mealResponse: MealResponse
//   ) -> List<IngredientEntity> {
//     let ingredientEntities = List<IngredientEntity>()
//     var ingredients = [
//       mealResponse.ingredient1, mealResponse.ingredient2,
//       mealResponse.ingredient3, mealResponse.ingredient4,
//       mealResponse.ingredient5, mealResponse.ingredient6,
//       mealResponse.ingredient7, mealResponse.ingredient8,
//       mealResponse.ingredient9, mealResponse.ingredient10,
//       mealResponse.ingredient11, mealResponse.ingredient12,
//       mealResponse.ingredient13, mealResponse.ingredient14,
//       mealResponse.ingredient15, mealResponse.ingredient16,
//       mealResponse.ingredient17, mealResponse.ingredient18,
//       mealResponse.ingredient19, mealResponse.ingredient20
//     ].compactMap { $0 }
//     ingredients = ingredients.filter({ $0 != ""})
//
//     var measures = [
//       mealResponse.measure1, mealResponse.measure2,
//       mealResponse.measure3, mealResponse.measure4,
//       mealResponse.measure5, mealResponse.measure6,
//       mealResponse.measure7, mealResponse.measure8,
//       mealResponse.measure9, mealResponse.measure10,
//       mealResponse.measure11, mealResponse.measure12,
//       mealResponse.measure13, mealResponse.measure14,
//       mealResponse.measure15, mealResponse.measure16,
//       mealResponse.measure17, mealResponse.measure18,
//       mealResponse.measure19, mealResponse.measure20
//     ].compactMap { $0 }
//     measures = measures.filter({ $0 != ""})
//
//     let ingredientStrings = zip(ingredients, measures)
//       .map { "\($0) \($1)" }
//
//     for (index, ingredient) in ingredientStrings.enumerated() {
//       let ingredientEntity = IngredientEntity()
//       ingredientEntity.id = "\(index+1)"
//       ingredientEntity.title = "\(index+1). \(ingredient)"
//       ingredientEntity.idMeal = idMeal
//       ingredientEntities.append(ingredientEntity)
//     }
//
//     return ingredientEntities
//   }
//
//   static func mapIngredientResponseToDomains(
//     by idMeal: String,
//     input mealResponse: MealResponse
//   ) -> [IngredientModel] {
//     var ingredientDomains: [IngredientModel] = []
//     var ingredients = [
//       mealResponse.ingredient1, mealResponse.ingredient2,
//       mealResponse.ingredient3, mealResponse.ingredient4,
//       mealResponse.ingredient5, mealResponse.ingredient6,
//       mealResponse.ingredient7, mealResponse.ingredient8,
//       mealResponse.ingredient9, mealResponse.ingredient10,
//       mealResponse.ingredient11, mealResponse.ingredient12,
//       mealResponse.ingredient13, mealResponse.ingredient14,
//       mealResponse.ingredient15, mealResponse.ingredient16,
//       mealResponse.ingredient17, mealResponse.ingredient18,
//       mealResponse.ingredient19, mealResponse.ingredient20
//     ].compactMap { $0 }
//     ingredients = ingredients.filter({ $0 != ""})
//
//     var measures = [
//       mealResponse.measure1, mealResponse.measure2,
//       mealResponse.measure3, mealResponse.measure4,
//       mealResponse.measure5, mealResponse.measure6,
//       mealResponse.measure7, mealResponse.measure8,
//       mealResponse.measure9, mealResponse.measure10,
//       mealResponse.measure11, mealResponse.measure12,
//       mealResponse.measure13, mealResponse.measure14,
//       mealResponse.measure15, mealResponse.measure16,
//       mealResponse.measure17, mealResponse.measure18,
//       mealResponse.measure19, mealResponse.measure20
//     ].compactMap { $0 }
//     measures = measures.filter({ $0 != ""})
//
//     let ingredientStrings = zip(ingredients, measures)
//       .map { "\($0) \($1)" }
//
//     for (index, ingredient) in ingredientStrings.enumerated() {
//       let ingredientDomain = IngredientModel(
//         id: "\(index+1)",
//         title: "\(index+1). \(ingredient)",
//         idMeal: idMeal
//       )
//       ingredientDomains.append(ingredientDomain)
//     }
//     return ingredientDomains
//   }
//
//}


/== GameCatalog/Core/Utils/Mapper/MealMapper.swift
//
//  MealMapper.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//

//import Foundation
//
//final class MealMapper {
//  static func mapMealResponsesToEntities(
//    by category: String,
//    input mealResponses: [MealResponse]
//  ) -> [MealEntity] {
//    return mealResponses.map { result in
//      let newMeal = MealEntity()
//      newMeal.id = result.id ?? ""
//      newMeal.title = result.title ?? "Unknow"
//      newMeal.image = result.image ?? "Unknow"
//      newMeal.category = category
//      return newMeal
//    }
//  }
//
//  static func mapMealResponsesToDomains(
//    by category: String,
//    input mealResponses: [MealResponse]
//  ) -> [MealModel] {
//    return mealResponses.map { result in
//      var newMeal = MealModel(
//        id: result.id ?? "",
//        title: result.title ?? "Unknow",
//        image: result.image ?? "Unknow"
//      )
//      newMeal.category = category
//      return newMeal
//    }
//  }
//
//  static func mapMealResponsesToDomains(
//    input mealResponses: [MealResponse]
//  ) -> [MealModel] {
//    return mealResponses.map { result in
//      let ingredients = IngredientMapper.mapIngredientResponseToDomains(
//        by: result.id ?? "",
//        input: result
//      )
//      return MealModel(
//        id: result.id ?? "",
//        title: result.title ?? "Unknow",
//        image: result.image ?? "Unknow",
//        category: result.category ?? "Unknow",
//        area: result.area ?? "Unknow",
//        instructions: result.instructions ?? "Unknow",
//        tag: result.tag ?? "Unknow",
//        youtube: result.youtube ?? "Unknow",
//        source: result.source ?? "Unknow",
//        ingredients: ingredients
//      )
//    }
//  }
//
//  static func mapMealEntitiesToDomains(
//    input mealEntities: [MealEntity]
//  ) -> [MealModel] {
//    return mealEntities.map { result in
//      let ingredients = IngredientMapper.mapIngredientEntitiesToDomains(
//        input: Array(result.ingredients)
//      )
//      return MealModel(
//        id: result.id ,
//        title: result.title,
//        image: result.image,
//        category: result.category,
//        area: result.area,
//        instructions: result.instructions,
//        tag: result.tag,
//        youtube: result.youtube,
//        source: result.source,
//        ingredients: ingredients,
//        favorite: result.favorite
//      )
//    }
//  }
//
//  static func mapDetailMealEntityToDomain(
//    input mealEntity: MealEntity
//  ) -> MealModel {
//    let ingredients = IngredientMapper.mapIngredientEntitiesToDomains(
//      input: Array(mealEntity.ingredients)
//    )
//    return MealModel(
//      id: mealEntity.id ,
//      title: mealEntity.title,
//      image: mealEntity.image,
//      category: mealEntity.category,
//      area: mealEntity.area,
//      instructions: mealEntity.instructions,
//      tag: mealEntity.tag,
//      youtube: mealEntity.youtube,
//      source: mealEntity.source,
//      ingredients: ingredients,
//      favorite: mealEntity.favorite
//    )
//  }
//
//  static func mapDetailMealEntityToDomains(
//    input mealEntities: [MealEntity]
//  ) -> [MealModel] {
//    return mealEntities.map { result in
//      let ingredients = IngredientMapper.mapIngredientEntitiesToDomains(
//        input: Array(result.ingredients)
//      )
//      return MealModel(
//        id: result.id ,
//        title: result.title,
//        image: result.image,
//        category: result.category,
//        area: result.area,
//        instructions: result.instructions,
//        tag: result.tag,
//        youtube: result.youtube,
//        source: result.source,
//        ingredients: ingredients,
//        favorite: result.favorite
//      )
//    }
//  }
//
//  static func mapDetailMealResponseToEntity(
//    by idMeal: String,
//    input mealResponse: MealResponse
//  ) -> MealEntity {
//    let ingredients = IngredientMapper.mapIngredientResponseToEntities(
//      by: idMeal,
//      input: mealResponse
//    )
//    let mealEntity = MealEntity()
//    mealEntity.id = mealResponse.id ?? ""
//    mealEntity.title = mealResponse.title ?? "Unknow"
//    mealEntity.image = mealResponse.image ?? "Unknow"
//    mealEntity.category = mealResponse.category ?? "Unknow"
//    mealEntity.area = mealResponse.area ?? "Unknow"
//    mealEntity.instructions = mealResponse.instructions ?? "Unknow"
//    mealEntity.tag = mealResponse.tag ?? "Unknow"
//    mealEntity.youtube = mealResponse.youtube ?? "Unknow"
//    mealEntity.source = mealResponse.source ?? "Unknow"
//    mealEntity.ingredients = ingredients
//    return mealEntity
//  }
//
//  static func mapDetailMealResponseToEntity(
//    input mealResponse: [MealResponse]
//  ) -> [MealEntity] {
//    return mealResponse.map { result in
//      let ingredients = IngredientMapper.mapIngredientResponseToEntities(
//        by: result.id ?? "",
//        input: result
//      )
//      let mealEntity = MealEntity()
//      mealEntity.id = result.id ?? ""
//      mealEntity.title = result.title ?? "Unknow"
//      mealEntity.image = result.image ?? "Unknow"
//      mealEntity.category = result.category ?? "Unknow"
//      mealEntity.area = result.area ?? "Unknow"
//      mealEntity.instructions = result.instructions ?? "Unknow"
//      mealEntity.tag = result.tag ?? "Unknow"
//      mealEntity.youtube = result.youtube ?? "Unknow"
//      mealEntity.source = result.source ?? "Unknow"
//      mealEntity.ingredients = ingredients
//      return mealEntity
//    }
//  }
//
//}


/== GameCatalog/Core/Utils/Network/APICall.swift
//
//  APICall.swift
//  GameCatalog
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
      case .games: return "\(API.gameBaseUrl)games?key=\(API.gameApiKey)&page_size=20&page=1"
      case .gameDetail(let id): return "\(API.gameBaseUrl)games/\(id)?key=\(API.gameApiKey)"
      }
    }
  }

}


/== GameCatalog/Core/Utils/View/CustomEmptyView.swift
//
//  CustomEmptyView.swift
//  GameCatalog
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


/== GameCatalog/Core/Utils/View/CustomIcon.swift
//
//  CustomIcon.swift
//  GameCatalog
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


/== GameCatalog/Core/Utils/View/ImageUtility.swift
//
//  ImageUtility.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import Foundation
import UIKit

class ImageUtility {
    
    // Singleton instance
    static let shared = ImageUtility()
    
    // In-memory cache to store previously loaded images
    private let imageCache = NSCache<NSString, UIImage>()
    
    // Session used for fetching images
    private let session: URLSession
    
    private init() {
        // Configure session for image loading
        let configuration = URLSessionConfiguration.default
        configuration.timeoutIntervalForRequest = 30.0
        configuration.requestCachePolicy = .returnCacheDataElseLoad
        session = URLSession(configuration: configuration)
        
        // Configure cache
        imageCache.countLimit = 100 // Max number of images to keep in memory
        imageCache.totalCostLimit = 50 * 1024 * 1024 // 50MB of memory for cache
        
        // Register for memory warning notifications
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(clearMemoryCache),
            name: UIApplication.didReceiveMemoryWarningNotification,
            object: nil
        )
    }
    
    deinit {
        NotificationCenter.default.removeObserver(self)
    }
    
    // Load image into UIImageView with error handling and retries
    func loadImage(from urlString: String?, into imageView: UIImageView, placeholder: UIImage? = nil) {
        // Record start time for performance tracking
        let startTime = Date()
        
        // Set default placeholder
        let defaultPlaceholder = placeholder ?? UIImage(named: "placeholder_image")
        imageView.image = defaultPlaceholder
        
        // Guard against nil or empty URL strings
        guard let urlString = urlString, !urlString.isEmpty else {
            print("ImageUtility: Empty or nil URL string provided")
            return
        }
        
        print("Attempting to load image from: \(urlString)")
        
        // Use cache key based on URL string
        let cacheKey = NSString(string: urlString)
        
        // Check if image is already in cache
        if let cachedImage = imageCache.object(forKey: cacheKey) {
            print("Image loaded from cache: \(urlString)")
            imageView.image = cachedImage
            return
        }
        
        // Create a URL object - first try direct creation
        let imageURL: URL
        if let directURL = URL(string: urlString) {
            // Direct URL creation succeeded
            imageURL = directURL
        } else {
            // Direct URL creation failed, try with encoding
            guard let encodedUrlString = urlString.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed),
                  let encodedUrl = URL(string: encodedUrlString) else {
                print("ImageUtility: Invalid URL format even after encoding: \(urlString)")
                return
            }
            
            print("ImageUtility: URL required encoding: \(urlString) → \(encodedUrlString)")
            imageURL = encodedUrl
        }
        
        // Tag the image view with the current request URL to avoid stale images
        let requestID = UUID().uuidString
        imageView.accessibilityIdentifier = requestID
        
        // Create and start the data task
        let task = session.dataTask(with: imageURL) { [weak self] data, response, error in
            guard let self = self else { return }
            
            // Perform UI updates on main thread
            DispatchQueue.main.async {
                // Check if the image view was recycled for a different image
                if imageView.accessibilityIdentifier != requestID {
                    print("ImageView was recycled for a different request, aborting image update")
                    return
                }
                
                // Handle errors
                if let error = error {
                    let timeElapsed = Date().timeIntervalSince(startTime)
                    print("Image load failed after \(String(format: "%.2f", timeElapsed))s: \(error.localizedDescription)")
                    
                    // Try fallbacks for failed requests
                    self.tryLoadFallbackImage(into: imageView, originalUrl: imageURL)
                    return
                }
                
                // Check for valid HTTP response
                guard let httpResponse = response as? HTTPURLResponse else {
                    print("Invalid response type")
                    return
                }
                
                // Check for successful HTTP status code
                guard (200...299).contains(httpResponse.statusCode) else {
                    print("HTTP error: \(httpResponse.statusCode)")
                    
                    // Try fallbacks for unsuccessful responses
                    self.tryLoadFallbackImage(into: imageView, originalUrl: imageURL)
                    return
                }
                
                // Ensure we have data
                guard let data = data, !data.isEmpty else {
                    print("No image data received")
                    return
                }
                
                // Create image from data
                guard let image = UIImage(data: data) else {
                    print("Could not create image from data")
                    return
                }
                
                // Cache the image
                self.imageCache.setObject(image, forKey: cacheKey)
                
                // Update the image view
                let timeElapsed = Date().timeIntervalSince(startTime)
                print("Image loaded successfully in \(String(format: "%.2f", timeElapsed))s: \(imageURL.absoluteString)")
                print("Image size: \(image.size.width)x\(image.size.height)")
                
                // Animate image appearance
                UIView.transition(with: imageView, duration: 0.3, options: .transitionCrossDissolve, animations: {
                    imageView.image = image
                }, completion: nil)
            }
        }
        
        task.resume()
    }
    
    // Try to load from fallback sources when main image fails
    private func tryLoadFallbackImage(into imageView: UIImageView, originalUrl: URL) {
        // Check if we can use a different image source
        let urlString = originalUrl.absoluteString
        
        // Some APIs have alternative domain options, try switching domains
        if urlString.contains("media.rawg.io") {
            // Try an alternative domain if exists
            let alternativeUrl = urlString.replacingOccurrences(of: "media.rawg.io", with: "media.api.rawg.io")
            print("Trying alternative URL domain: \(alternativeUrl)")
            
            loadImage(from: alternativeUrl, into: imageView)
            return
        }
        
        // If we failed to load an HTTPS URL, try HTTP version as a last resort
        if urlString.hasPrefix("https://") {
            let httpUrl = urlString.replacingOccurrences(of: "https://", with: "http://")
            print("Trying HTTP fallback: \(httpUrl)")
            
            loadImage(from: httpUrl, into: imageView)
            return
        }
        
        print("All fallback attempts failed for image URL: \(urlString)")
    }
    
    // Clear the in-memory image cache (called on memory warnings)
    @objc func clearMemoryCache() {
        imageCache.removeAllObjects()
        print("🧹Memory cache cleared")
    }
    
    // Helper method to validate an image URL
    func isValidImageURL(_ urlString: String?) -> Bool {
        guard let urlString = urlString, !urlString.isEmpty else {
            return false
        }
        
        // First try direct URL creation
        if let _ = URL(string: urlString) {
            return true
        }
        
        // If that fails, try with percent encoding
        if let encodedString = urlString.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed),
           let _ = URL(string: encodedString) {
            return true
        }
        
        return false
    }
}

// MARK: - UIImageView Extension for easier usage
extension UIImageView {
    // Convenience method to load images directly on UIImageView
    func loadGameImage(from urlString: String?, placeholder: UIImage? = nil) {
        ImageUtility.shared.loadImage(from: urlString, into: self, placeholder: placeholder)
    }
}


/== GameCatalog/Core/Utils/View/TabItem.swift
//
//  TabItem.swift
//  GameCatalog
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


/== GameCatalog/Module/About/About.swift
//
//  AboutView.swift
//  GameCatalog
//
//  Created by ben on 22/03/25.
//

import SwiftUI
import GameCatalogAbout

struct AboutView: View {
    var body: some View {
        AboutMeView()
    }
}

#Preview {
    NavigationView {
        AboutView()
    }
    .preferredColorScheme(.dark)
}


/== GameCatalog/Module/Detail/Presenter/DetailPresenter.swift
//
//  DetailPresenter.swift
//  GameCatalog
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


/== GameCatalog/Module/Detail/Router/DetailRouter.swift
//
//  DetailRouter.swift
//  GameCatalog
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


/== GameCatalog/Module/Detail/View/DetailView.swift
//
//  DetailView.swift
//  GameCatalog
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


/== GameCatalog/Module/Detail/View/MealRow.swift
//
//  MealRow.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//
//
//import Foundation
//import SwiftUI
//import CachedAsyncImage
//
//struct MealRow: View {
//  var meal: MealModel
//
//  var body: some View {
//    GeometryReader { geometry in
//      ZStack(alignment: .bottomLeading) {
//        self.imageMeal
//          .frame(
//            width: geometry.size.width,
//            height: geometry.size.height,
//            alignment: .center
//        )
//        EmptyView().frame(
//          width: geometry.size.width,
//          height: 32
//        ).blur(radius: 20)
//
//        self.titleMeal
//      }
//    }.cornerRadius(12)
//  }
//}
//
//extension MealRow {
//
//  var imageMeal: some View {
//    CachedAsyncImage(url: URL(string: self.meal.image)) { image in
//      image.resizable()
//    } placeholder: {
//      ProgressView()
//    }.scaledToFit()
//  }
//
//  var titleMeal: some View {
//    Text(self.meal.title)
//      .font(.system(size: 14))
//      .lineLimit(1)
//      .foregroundColor(.white)
//      .padding(EdgeInsets(top: 0, leading: 8, bottom: 8, trailing: 8))
//  }
//
//}


/== GameCatalog/Module/Favorite/Presenter/FavoritePresenter.swift
//
//  FavoritePresenter.swift
//  GameCatalog
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


/== GameCatalog/Module/Favorite/Router/FavoriteRouter.swift
//
//  FavoriteRouter.swift
//  GameCatalog
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


/== GameCatalog/Module/Favorite/View/FavoriteRow.swift
//
//  FavoriteRow.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//

//import SwiftUI
//import CachedAsyncImage
//
//struct FavoriteRow: View {
//
//  var meal: MealModel
//
//  var body: some View {
//    VStack {
//      HStack(alignment: .top) {
//        imageCategory
//        content
//        Spacer()
//      }
//      .padding(.horizontal, 16)
//      .padding(.vertical, 8)
//
//      Divider()
//        .padding(.leading)
//    }
//  }
//
//}
//
//extension FavoriteRow {
//
//  var imageCategory: some View {
//    CachedAsyncImage(url: URL(string: meal.image)) { image in
//      image.resizable()
//    } placeholder: {
//      ProgressView()
//    }.cornerRadius(20).scaledToFit().frame(width: 120)
//  }
//
//  var content: some View {
//    VStack(alignment: .leading, spacing: 10) {
//      Text(meal.title)
//        .font(.system(size: 20, weight: .semibold, design: .rounded))
//        .lineLimit(3)
//
//      Text(meal.category)
//        .font(.system(size: 16))
//        .lineLimit(2)
//
//      if !meal.area.isEmpty {
//        Text("From \(meal.area)")
//          .font(.system(size: 14))
//          .lineLimit(2)
//      }
//
//    }.padding(
//      EdgeInsets(
//        top: 0,
//        leading: 16,
//        bottom: 16,
//        trailing: 16
//      )
//    )
//  }
//
//}


/== GameCatalog/Module/Favorite/View/FavoriteView.swift
//
//  FavoriteView.swift
//  GameCatalog
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


/== GameCatalog/Module/Home/Presenter/HomePresenter.swift
//
//  HomePresenter.swift
//  GameCatalog
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


/== GameCatalog/Module/Home/Router/HomeRouter.swift
//
//  HomeRouter.swift
//  GameCatalog
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


/== GameCatalog/Module/Home/View/CategoryRow.swift
//
//  CategoryRow.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 22/11/22.
//

//import SwiftUI
//import CachedAsyncImage
//
//struct CategoryRow: View {
//
//  var category: CategoryModel
//  var body: some View {
//    VStack {
//      imageCategory
//      content
//    }
//    .frame(width: UIScreen.main.bounds.width - 32, height: 250)
//    .background(Color.random.opacity(0.3))
//    .cornerRadius(30)
//  }
//
//}
//
//extension CategoryRow {
//
//  var imageCategory: some View {
//    CachedAsyncImage(url: URL(string: category.image)) { image in
//      image.resizable()
//    } placeholder: {
//      ProgressView()
//    }.cornerRadius(30).scaledToFit().frame(width: 200).padding(.top)
//  }
//
//  var content: some View {
//    VStack(alignment: .leading, spacing: 10) {
//      Text(category.title)
//        .font(.title)
//        .bold()
//
//      Text(category.description)
//        .font(.system(size: 14))
//        .lineLimit(2)
//    }.padding(
//      EdgeInsets(
//        top: 0,
//        leading: 16,
//        bottom: 16,
//        trailing: 16
//      )
//    )
//  }
//
//}
//
//struct CategoryRow_Previews: PreviewProvider {
//
//  static var previews: some View {
//    let meal = CategoryModel(
//      id: "1",
//      title: "Beef",
//      image: "https://www.themealdb.com/images/category/beef.png",
//      description: "Beef is the culinary name for meat from cattle, particularly skeletal muscle."
//    )
//    return CategoryRow(category: meal)
//  }
//
//}


/== GameCatalog/Module/Home/View/HomeView.swift
//
//  HomeView.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import SwiftUI
import SDWebImageSwiftUI
import CachedAsyncImage

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


/== GameCatalog/Module/Meal/Presenter/MealPresenter.swift
//
//  MealPresenter.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//
//
//import Foundation
//import Combine
//
//class MealPresenter: ObservableObject {
//
//  private var cancellables: Set<AnyCancellable> = []
//  private let mealUseCase: MealUseCase
//
//  @Published var meal: MealModel
//  @Published var errorMessage: String = ""
//  @Published var isLoading: Bool = false
//  @Published var isError: Bool = false
//
//  init(mealUseCase: MealUseCase) {
//    self.mealUseCase = mealUseCase
//    meal = mealUseCase.getMeal()
//  }
//
//  func getMeal() {
//    isLoading = true
//    mealUseCase.getMeal()
//      .receive(on: RunLoop.main)
//      .sink(receiveCompletion: { completion in
//          switch completion {
//          case .failure (let error):
//            self.errorMessage = error.localizedDescription
//            self.isError = true
//            self.isLoading = false
//          case .finished:
//            self.isLoading = false
//          }
//        }, receiveValue: { meal in
//          self.meal = meal
//        })
//        .store(in: &cancellables)
//  }
//
//  func updateFavoriteMeal() {
//    mealUseCase.updateFavoriteMeal()
//      .receive(on: RunLoop.main)
//      .sink(receiveCompletion: { completion in
//          switch completion {
//          case .failure:
//            self.errorMessage = String(describing: completion)
//          case .finished:
//            self.isLoading = false
//          }
//        }, receiveValue: { meal in
//          self.meal = meal
//        })
//        .store(in: &cancellables)
//  }
//
//}


/== GameCatalog/Module/Meal/View/MealView.swift
//
//  MealView.swift
//  GameCatalog
//
//  Created by Gilang Ramadhan on 29/11/22.
//
//
//import SwiftUI
//import CachedAsyncImage
//
//struct MealView: View {
//
//  @State private var showingAlert = false
//  @ObservedObject var presenter: MealPresenter
//
//  var body: some View {
//    ZStack {
//      if presenter.isLoading {
//        loadingIndicator
//      } else if presenter.isError {
//        errorIndicator
//      } else {
//        ScrollView(.vertical) {
//          VStack {
//            imageMeal
//            menuButtonMeal
//            content
//          }.padding()
//        }
//      }
//    }.onAppear {
//      self.presenter.getMeal()
//    }.alert(isPresented: $showingAlert) {
//      Alert(
//        title: Text("Oops!"),
//        message: Text("Something wrong!"),
//        dismissButton: .default(Text("OK"))
//      )
//    }.navigationBarTitle(
//      Text(presenter.meal.title),
//      displayMode: .automatic
//    )
//  }
//
//}
//
//extension MealView {
//
//  var loadingIndicator: some View {
//    VStack {
//      Text("Loading...")
//      ProgressView()
//    }
//  }
//
//  var errorIndicator: some View {
//    CustomEmptyView(
//      image: "assetSearchNotFound",
//      title: presenter.errorMessage
//    ).offset(y: 80)
//  }
//
//  var menuButtonMeal: some View {
//    HStack(alignment: .center) {
//      Spacer()
//      CustomIcon(
//        imageName: "link.circle",
//        title: "Source"
//      ).onTapGesture {
//        self.openUrl(self.presenter.meal.source)
//      }
//      Spacer()
//      CustomIcon(
//        imageName: "video",
//        title: "Video"
//      ).onTapGesture {
//        self.openUrl(self.presenter.meal.youtube)
//      }
//      Spacer()
//      if presenter.meal.favorite {
//        CustomIcon(
//          imageName: "heart.fill",
//          title: "Favorited"
//        ).onTapGesture { self.presenter.updateFavoriteMeal() }
//      } else {
//        CustomIcon(
//          imageName: "heart",
//          title: "Favorite"
//        ).onTapGesture { self.presenter.updateFavoriteMeal() }
//      }
//      Spacer()
//    }.padding()
//  }
//
//  var imageMeal: some View {
//    CachedAsyncImage(url: URL(string: self.presenter.meal.image)) { image in
//      image.resizable()
//    } placeholder: {
//      ProgressView()
//    }.scaledToFill()
//      .frame(width: UIScreen.main.bounds.width - 32, height: 250.0, alignment: .center)
//      .cornerRadius(30)
//  }
//
//  var content: some View {
//    VStack(alignment: .leading, spacing: 8) {
//      if !presenter.meal.ingredients.isEmpty {
//        Text("Ingredient")
//          .font(.headline)
//
//        ForEach(self.presenter.meal.ingredients, id: \.id) { ingredient in
//          ZStack {
//            Text(ingredient.title)
//              .font(.system(size: 16))
//          }
//        }
//      }
//
//      Divider()
//        .padding(.vertical)
//
//      Text("Instructions")
//        .font(.headline)
//
//      Text(self.presenter.meal.instructions)
//        .font(.system(size: 16))
//    }.padding(.top)
//  }
//
//}
//
//extension MealView {
//
//  func openUrl(_ linkUrl: String) {
//    if let link = URL(string: linkUrl) {
//      UIApplication.shared.open(link)
//    } else {
//      showingAlert = true
//    }
//  }
//
//}


/== GameCatalog/Module/Search/Presenter/SearchPresenter.swift
//
//  SearchPresenter.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import SwiftUI
import Combine

class SearchPresenter: ObservableObject {
    private var cancellables = Set<AnyCancellable>()
    private let searchUseCase: SearchUseCase
    private let repository: GameRepositoryProtocol
    
    @Published var games: [GameModel] = []
    @Published var errorMessage: String = ""
    @Published var isLoading: Bool = false
    
    init(searchUseCase: SearchUseCase, repository: GameRepositoryProtocol) {
        self.searchUseCase = searchUseCase
        self.repository = repository
    }
    
    func searchGames(query: String) {
        isLoading = true
        games = []
        
        searchUseCase.searchGames(query: query)
            .receive(on: RunLoop.main)
            .sink(receiveCompletion: { completion in
                switch completion {
                case .failure(let error):
                    self.errorMessage = error.localizedDescription
                    self.isLoading = false
                case .finished:
                    self.isLoading = false
                }
            }, receiveValue: { games in
                self.games = games
            })
            .store(in: &cancellables)
    }
    
    func toggleFavorite(game: GameModel, isFavorite: Bool) {
        if isFavorite {
            repository.addToFavorite(game: game)
                .receive(on: RunLoop.main)
                .sink(receiveCompletion: { completion in
                    switch completion {
                    case .failure(let error):
                        self.errorMessage = error.localizedDescription
                    case .finished:
                        print("Game added to favorites")
                    }
                }, receiveValue: { _ in
                    // Update the game in the list
                    if let index = self.games.firstIndex(where: { $0.id == game.id }) {
                        let updatedGame = GameModel(
                            id: game.id,
                            name: game.name,
                            released: game.released,
                            backgroundImage: game.backgroundImage,
                            rating: game.rating,
                            ratingCount: game.ratingCount,
                            description: game.description,
                            genres: game.genres,
                            platforms: game.platforms,
                            isFavorite: true
                        )
                        self.games[index] = updatedGame
                    }
                })
                .store(in: &cancellables)
        } else {
            repository.removeFromFavorite(id: game.id)
                .receive(on: RunLoop.main)
                .sink(receiveCompletion: { completion in
                    switch completion {
                    case .failure(let error):
                        self.errorMessage = error.localizedDescription
                    case .finished:
                        print("Game removed from favorites")
                    }
                }, receiveValue: { _ in
                    // Update the game in the list
                    if let index = self.games.firstIndex(where: { $0.id == game.id }) {
                        let updatedGame = GameModel(
                            id: game.id,
                            name: game.name,
                            released: game.released,
                            backgroundImage: game.backgroundImage,
                            rating: game.rating,
                            ratingCount: game.ratingCount,
                            description: game.description,
                            genres: game.genres,
                            platforms: game.platforms,
                            isFavorite: false
                        )
                        self.games[index] = updatedGame
                    }
                })
                .store(in: &cancellables)
        }
    }
}


/== GameCatalog/Module/Search/Router/SearchRouter.swift
//
//  SearchRouter.swift
//  GameCatalog
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


/== GameCatalog/Module/Search/View/SearchRow.swift
//
//  GameRow.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import SwiftUI
import CachedAsyncImage

struct SearchRow: View {
    var game: GameModel
    var onFavoriteToggle: ((Bool) -> Void)? = nil

    var body: some View {
        VStack {
            HStack(alignment: .top) {
                gameImage
                content
                Spacer()
                favoriteButton
            }
            .padding(.horizontal, 16)
            .padding(.vertical, 8)

            Divider()
                .padding(.leading)
        }
    }
    
    var gameImage: some View {
        CachedAsyncImage(url: URL(string: game.backgroundImage)) { image in
            image
                .resizable()
                .aspectRatio(contentMode: .fill)
        } placeholder: {
            Rectangle()
                .foregroundColor(.gray.opacity(0.3))
                .overlay(
                    Image(systemName: "gamecontroller")
                        .font(.largeTitle)
                        .foregroundColor(.gray)
                )
        }
        .frame(width: 120, height: 80)
        .cornerRadius(10)
        .clipped()
    }
    
    var content: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(game.name)
                .font(.system(size: 18, weight: .semibold, design: .rounded))
                .lineLimit(2)
            
            if !game.released.isEmpty && game.released != "Unknown" {
                Text("Released: \(formattedDate(game.released))")
                    .font(.system(size: 14))
                    .foregroundColor(.secondary)
            }
            
            HStack(spacing: 4) {
                Image(systemName: "star.fill")
                    .foregroundColor(.yellow)
                    .font(.system(size: 12))
                
                Text(String(format: "%.1f", game.rating))
                    .font(.system(size: 14, weight: .medium))
                
                Text("(\(game.ratingCount))")
                    .font(.system(size: 12))
                    .foregroundColor(.secondary)
            }
            
            if !game.genres.isEmpty {
                genresList
            }
        }
        .padding(.leading, 8)
    }
    
    var genresList: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 4) {
                ForEach(game.genres.prefix(3), id: \.self) { genre in
                    Text(genre)
                        .font(.system(size: 12))
                        .padding(.horizontal, 8)
                        .padding(.vertical, 4)
                        .background(Color.gray.opacity(0.2))
                        .cornerRadius(8)
                }
                
                if game.genres.count > 3 {
                    Text("+\(game.genres.count - 3)")
                        .font(.system(size: 12))
                        .padding(.horizontal, 8)
                        .padding(.vertical, 4)
                        .background(Color.gray.opacity(0.2))
                        .cornerRadius(8)
                }
            }
        }
        .frame(height: 30)
    }
    
    var favoriteButton: some View {
        Button(action: {
            onFavoriteToggle?(!game.isFavorite)
        }) {
            Image(systemName: game.isFavorite ? "heart.fill" : "heart")
                .foregroundColor(game.isFavorite ? .red : .gray)
                .font(.title3)
                .padding(8)
        }
    }
    
    // Helper function to format the date
    func formattedDate(_ dateString: String) -> String {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy-MM-dd"
        
        if let date = dateFormatter.date(from: dateString) {
            let displayFormatter = DateFormatter()
            displayFormatter.dateFormat = "MMM d, yyyy"
            return displayFormatter.string(from: date)
        }
        
        return dateString
    }
}


/== GameCatalog/Module/Search/View/SearchView.swift
//
//  SearchView.swift
//  GameCatalog
//
//  Created on 03/04/25.
//

import SwiftUI
import Combine

struct SearchView: View {
    @ObservedObject var presenter: SearchPresenter
    @State private var searchText: String = ""
    @State private var isSearching: Bool = false
    @State private var searchDebounce = PassthroughSubject<String, Never>()
    @State private var cancellables = Set<AnyCancellable>()
    
    // Initialize the view and set up the search debounce functionality
    init(presenter: SearchPresenter) {
        self.presenter = presenter
        // This is needed because we're setting up the debounce in init
        _searchDebounce = State(initialValue: PassthroughSubject<String, Never>())
    }
    
    var body: some View {
        VStack {
            searchBar
            
            if presenter.isLoading {
                loadingView
            } else if presenter.games.isEmpty && !searchText.isEmpty {
                emptyView
            } else {
                gamesList
            }
        }
        .navigationTitle("Search Games")
        .onAppear {
            // Set up the debounce when the view appears
            setupSearchDebounce()
        }
    }
    
    // Set up the debounce functionality for search
    private func setupSearchDebounce() {
        searchDebounce
            .debounce(for: .milliseconds(500), scheduler: RunLoop.main)
            .removeDuplicates()
            .filter { !$0.isEmpty }
            .sink { [weak presenter] searchQuery in
                isSearching = true
                presenter?.searchGames(query: searchQuery)
            }
            .store(in: &cancellables)
    }
}

extension SearchView {
    var searchBar: some View {
        HStack {
            Image(systemName: "magnifyingglass")
                .foregroundColor(.gray)
            
            TextField("Search games...", text: $searchText)
                .onChange(of: searchText) { newValue in
                    // Send the new search text to be debounced
                    searchDebounce.send(newValue)
                }
                .onSubmit {
                    if !searchText.isEmpty {
                        isSearching = true
                        presenter.searchGames(query: searchText)
                    }
                }
            
            if !searchText.isEmpty {
                Button(action: {
                    searchText = ""
                    presenter.games = []
                }) {
                    Image(systemName: "xmark.circle.fill")
                        .foregroundColor(.gray)
                }
            }
        }
        .padding(10)
        .background(Color(.systemGray6))
        .cornerRadius(10)
        .padding(.horizontal)
        .padding(.top, 8)
    }
    
    // Rest of the code remains unchanged
    var loadingView: some View {
        VStack {
            Spacer()
            ProgressView()
                .scaleEffect(1.5)
            Text("Searching for games...")
                .padding(.top, 16)
                .foregroundColor(.secondary)
            Spacer()
        }
    }
    
    var emptyView: some View {
        VStack {
            Spacer()
            Image(systemName: "gamecontroller")
                .font(.system(size: 60))
                .foregroundColor(.gray)
            Text("No games found")
                .font(.title2)
                .padding(.top, 8)
            Text("Try a different search term")
                .foregroundColor(.secondary)
            Spacer()
        }
    }
    
    var gamesList: some View {
        ScrollView {
            LazyVStack(spacing: 0) {
                ForEach(presenter.games) { game in
                    NavigationLink(destination: DetailView(presenter: DetailPresenter(detailUseCase: Injection.init().provideDetail(gameId: game.id)))) {
                        // Use your existing GameRow without the onFavoriteToggle parameter
                        GameRow(game: game)
                    }
                    .buttonStyle(PlainButtonStyle())
                }
            }
            .padding(.vertical, 8)
        }
    }
}


/== GameCatalog/Preview Content/Preview Assets.xcassets/Contents.json
{
  "info" : {
    "author" : "xcode",
    "version" : 1
  }
}


/== GameCatalog.xcodeproj/project.pbxproj
// !$*UTF8*$!
{
	archiveVersion = 1;
	classes = {
	};
	objectVersion = 70;
	objects = {

/* Begin PBXBuildFile section */
		2A91C7782935DC8A005A9155 /* FavoriteInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7772935DC8A005A9155 /* FavoriteInteractor.swift */; };
		2A91C77A2935DC9F005A9155 /* MealInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C7792935DC9F005A9155 /* MealInteractor.swift */; };
		2A91C77C2935DCB6005A9155 /* SearchInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A91C77B2935DCB6005A9155 /* SearchInteractor.swift */; };
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
		2A9D452F292C805D008FABEB /* GameCatalogApp.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D452E292C805D008FABEB /* GameCatalogApp.swift */; };
		2A9D4531292C805D008FABEB /* ContentView.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4530292C805D008FABEB /* ContentView.swift */; };
		2A9D4533292C805F008FABEB /* Assets.xcassets in Resources */ = {isa = PBXBuildFile; fileRef = 2A9D4532292C805F008FABEB /* Assets.xcassets */; };
		2A9D4536292C805F008FABEB /* Preview Assets.xcassets in Resources */ = {isa = PBXBuildFile; fileRef = 2A9D4535292C805F008FABEB /* Preview Assets.xcassets */; };
		2A9D454E292C81B1008FABEB /* Injection.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D454D292C81B1008FABEB /* Injection.swift */; };
		2A9D4554292C8202008FABEB /* HomeInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4553292C8202008FABEB /* HomeInteractor.swift */; };
		2A9D4556292C8225008FABEB /* DetailInteractor.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D4555292C8225008FABEB /* DetailInteractor.swift */; };
		2A9D455B292C8262008FABEB /* APICall.swift in Sources */ = {isa = PBXBuildFile; fileRef = 2A9D455A292C8262008FABEB /* APICall.swift */; };
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
		2AC9639F292CB8270061B65A /* CachedAsyncImage in Frameworks */ = {isa = PBXBuildFile; productRef = 2AC9639E292CB8270061B65A /* CachedAsyncImage */; };
		B53E70242DAE64D500C1123F /* GameCatalogAbout in Frameworks */ = {isa = PBXBuildFile; productRef = B53E70232DAE64D500C1123F /* GameCatalogAbout */; };
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
		B5FD19692D9E5CAA00DC56F7 /* AppLogger.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19682D9E5CA700DC56F7 /* AppLogger.swift */; };
		B5FD19862D9EA67100DC56F7 /* ImageUtility.swift in Sources */ = {isa = PBXBuildFile; fileRef = B5FD19852D9EA66E00DC56F7 /* ImageUtility.swift */; };
/* End PBXBuildFile section */

/* Begin PBXFileReference section */
		2A91C7772935DC8A005A9155 /* FavoriteInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = FavoriteInteractor.swift; sourceTree = "<group>"; };
		2A91C7792935DC9F005A9155 /* MealInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = MealInteractor.swift; sourceTree = "<group>"; };
		2A91C77B2935DCB6005A9155 /* SearchInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = SearchInteractor.swift; sourceTree = "<group>"; };
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
		2A9D452B292C805D008FABEB /* GameCatalog.app */ = {isa = PBXFileReference; explicitFileType = wrapper.application; includeInIndex = 0; path = GameCatalog.app; sourceTree = BUILT_PRODUCTS_DIR; };
		2A9D452E292C805D008FABEB /* GameCatalogApp.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameCatalogApp.swift; sourceTree = "<group>"; };
		2A9D4530292C805D008FABEB /* ContentView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = ContentView.swift; sourceTree = "<group>"; };
		2A9D4532292C805F008FABEB /* Assets.xcassets */ = {isa = PBXFileReference; lastKnownFileType = folder.assetcatalog; path = Assets.xcassets; sourceTree = "<group>"; };
		2A9D4535292C805F008FABEB /* Preview Assets.xcassets */ = {isa = PBXFileReference; lastKnownFileType = folder.assetcatalog; path = "Preview Assets.xcassets"; sourceTree = "<group>"; };
		2A9D454D292C81B1008FABEB /* Injection.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = Injection.swift; sourceTree = "<group>"; };
		2A9D4553292C8202008FABEB /* HomeInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomeInteractor.swift; sourceTree = "<group>"; };
		2A9D4555292C8225008FABEB /* DetailInteractor.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailInteractor.swift; sourceTree = "<group>"; };
		2A9D455A292C8262008FABEB /* APICall.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = APICall.swift; sourceTree = "<group>"; };
		2A9D455E292C828E008FABEB /* CustomeError+Ext.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = "CustomeError+Ext.swift"; sourceTree = "<group>"; };
		2A9D4560292C82A2008FABEB /* Color+Ext.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = "Color+Ext.swift"; sourceTree = "<group>"; };
		2A9D4565292C840B008FABEB /* HomePresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomePresenter.swift; sourceTree = "<group>"; };
		2A9D4567292C84B2008FABEB /* HomeView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomeView.swift; sourceTree = "<group>"; };
		2A9D4569292C84C2008FABEB /* CategoryRow.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = CategoryRow.swift; sourceTree = "<group>"; };
		2A9D456B292C8577008FABEB /* HomeRouter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = HomeRouter.swift; sourceTree = "<group>"; };
		2A9D456F292C8604008FABEB /* DetailPresenter.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailPresenter.swift; sourceTree = "<group>"; };
		2A9D4571292C8620008FABEB /* DetailView.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = DetailView.swift; sourceTree = "<group>"; };
		B5FD19302D9D3AF400DC56F7 /* GameEntity.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameEntity.swift; sourceTree = "<group>"; };
		B5FD19322D9D54DA00DC56F7 /* GamesResponse.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GamesResponse.swift; sourceTree = "<group>"; };
		B5FD19382D9D554000DC56F7 /* GameDetailModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameDetailModel.swift; sourceTree = "<group>"; };
		B5FD19392D9D554000DC56F7 /* GameModel.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameModel.swift; sourceTree = "<group>"; };
		B5FD193C2D9D556F00DC56F7 /* GameMapper.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameMapper.swift; sourceTree = "<group>"; };
		B5FD193E2D9DD00100DC56F7 /* GameRespository.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameRespository.swift; sourceTree = "<group>"; };
		B5FD19402D9DD0E600DC56F7 /* LocaleGameDataSource.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = LocaleGameDataSource.swift; sourceTree = "<group>"; };
		B5FD19422D9DD11800DC56F7 /* RemoteGameDataSource.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = RemoteGameDataSource.swift; sourceTree = "<group>"; };
		B5FD19472D9DDFBF00DC56F7 /* GameDetailResponse.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = GameDetailResponse.swift; sourceTree = "<group>"; };
		B5FD19682D9E5CA700DC56F7 /* AppLogger.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = AppLogger.swift; sourceTree = "<group>"; };
		B5FD19852D9EA66E00DC56F7 /* ImageUtility.swift */ = {isa = PBXFileReference; lastKnownFileType = sourcecode.swift; path = ImageUtility.swift; sourceTree = "<group>"; };
/* End PBXFileReference section */

/* Begin PBXFileSystemSynchronizedRootGroup section */
		B5FD19892D9EFA6C00DC56F7 /* About */ = {isa = PBXFileSystemSynchronizedRootGroup; explicitFileTypes = {}; explicitFolders = (); path = About; sourceTree = "<group>"; };
/* End PBXFileSystemSynchronizedRootGroup section */

/* Begin PBXFrameworksBuildPhase section */
		2A9D4528292C805D008FABEB /* Frameworks */ = {
			isa = PBXFrameworksBuildPhase;
			buildActionMask = 2147483647;
			files = (
				B5FD19462D9DD36800DC56F7 /* SDWebImageSwiftUI in Frameworks */,
				2AC96396292CB18A0061B65A /* RealmSwift in Frameworks */,
				B53E70242DAE64D500C1123F /* GameCatalogAbout in Frameworks */,
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
				B5FD19852D9EA66E00DC56F7 /* ImageUtility.swift */,
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
				2A9D452D292C805D008FABEB /* GameCatalog */,
				2A9D452C292C805D008FABEB /* Products */,
			);
			sourceTree = "<group>";
		};
		2A9D452C292C805D008FABEB /* Products */ = {
			isa = PBXGroup;
			children = (
				2A9D452B292C805D008FABEB /* GameCatalog.app */,
			);
			name = Products;
			sourceTree = "<group>";
		};
		2A9D452D292C805D008FABEB /* GameCatalog */ = {
			isa = PBXGroup;
			children = (
				2A9D453C292C80E6008FABEB /* App */,
				2A9D453D292C80EA008FABEB /* Core */,
				2A9D453E292C80F2008FABEB /* Module */,
				2A9D4532292C805F008FABEB /* Assets.xcassets */,
				2A9D4534292C805F008FABEB /* Preview Content */,
			);
			path = GameCatalog;
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
				2A9D452E292C805D008FABEB /* GameCatalogApp.swift */,
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
				B5FD19892D9EFA6C00DC56F7 /* About */,
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
				B5FD19682D9E5CA700DC56F7 /* AppLogger.swift */,
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
			);
			path = Remote;
			sourceTree = "<group>";
		};
		2A9D4546292C814E008FABEB /* Response */ = {
			isa = PBXGroup;
			children = (
				B5FD19472D9DDFBF00DC56F7 /* GameDetailResponse.swift */,
				B5FD19322D9D54DA00DC56F7 /* GamesResponse.swift */,
			);
			path = Response;
			sourceTree = "<group>";
		};
		2A9D454F292C81C2008FABEB /* Model */ = {
			isa = PBXGroup;
			children = (
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
			);
			path = Locale;
			sourceTree = "<group>";
		};
		2AC9639C292CB27B0061B65A /* Entity */ = {
			isa = PBXGroup;
			children = (
				B5FD19302D9D3AF400DC56F7 /* GameEntity.swift */,
			);
			path = Entity;
			sourceTree = "<group>";
		};
/* End PBXGroup section */

/* Begin PBXNativeTarget section */
		2A9D452A292C805D008FABEB /* GameCatalog */ = {
			isa = PBXNativeTarget;
			buildConfigurationList = 2A9D4539292C805F008FABEB /* Build configuration list for PBXNativeTarget "GameCatalog" */;
			buildPhases = (
				2A9D4527292C805D008FABEB /* Sources */,
				2A9D4528292C805D008FABEB /* Frameworks */,
				2A9D4529292C805D008FABEB /* Resources */,
			);
			buildRules = (
			);
			dependencies = (
			);
			fileSystemSynchronizedGroups = (
				B5FD19892D9EFA6C00DC56F7 /* About */,
			);
			name = GameCatalog;
			packageProductDependencies = (
				2A9276AF292CAA7B00C30767 /* Alamofire */,
				2AC96393292CB18A0061B65A /* Realm */,
				2AC96395292CB18A0061B65A /* RealmSwift */,
				2AC9639E292CB8270061B65A /* CachedAsyncImage */,
				B5FD19452D9DD36800DC56F7 /* SDWebImageSwiftUI */,
				B53E70232DAE64D500C1123F /* GameCatalogAbout */,
			);
			productName = GameCatalog;
			productReference = 2A9D452B292C805D008FABEB /* GameCatalog.app */;
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
			buildConfigurationList = 2A9D4526292C805D008FABEB /* Build configuration list for PBXProject "GameCatalog" */;
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
				B53E70222DAE64D500C1123F /* XCRemoteSwiftPackageReference "GameCatalogAbout" */,
			);
			productRefGroup = 2A9D452C292C805D008FABEB /* Products */;
			projectDirPath = "";
			projectRoot = "";
			targets = (
				2A9D452A292C805D008FABEB /* GameCatalog */,
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
				2A91C77A2935DC9F005A9155 /* MealInteractor.swift in Sources */,
				2A91C7AB2935E2E5005A9155 /* SearchPresenter.swift in Sources */,
				2A91C7952935DEDB005A9155 /* MealPresenter.swift in Sources */,
				2A9D4531292C805D008FABEB /* ContentView.swift in Sources */,
				B5FD19482D9DDFC400DC56F7 /* GameDetailResponse.swift in Sources */,
				2A9D456A292C84C2008FABEB /* CategoryRow.swift in Sources */,
				2A91C79E2935E061005A9155 /* DetailRouter.swift in Sources */,
				B5FD193F2D9DD00900DC56F7 /* GameRespository.swift in Sources */,
				B5FD19332D9D54DA00DC56F7 /* GamesResponse.swift in Sources */,
				2A91C7782935DC8A005A9155 /* FavoriteInteractor.swift in Sources */,
				2A91C7A42935E251005A9155 /* SearchView.swift in Sources */,
				2A9D455B292C8262008FABEB /* APICall.swift in Sources */,
				B5FD19692D9E5CAA00DC56F7 /* AppLogger.swift in Sources */,
				2A91C7882935DD8B005A9155 /* FavoritePresenter.swift in Sources */,
				2A9D4568292C84B2008FABEB /* HomeView.swift in Sources */,
				2A9D452F292C805D008FABEB /* GameCatalogApp.swift in Sources */,
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
				2A91C7AD2935FE79005A9155 /* TabItem.swift in Sources */,
				B5FD19432D9DD11A00DC56F7 /* RemoteGameDataSource.swift in Sources */,
				2A9D455F292C828E008FABEB /* CustomeError+Ext.swift in Sources */,
				2A91C78A2935DDA3005A9155 /* FavoriteRouter.swift in Sources */,
				2A91C7912935DE32005A9155 /* CustomEmptyView.swift in Sources */,
				2A91C78E2935DDDB005A9155 /* FavoriteRow.swift in Sources */,
				2A9D4572292C8620008FABEB /* DetailView.swift in Sources */,
				B5FD19862D9EA67100DC56F7 /* ImageUtility.swift in Sources */,
				2A91C7A62935E274005A9155 /* SearchRow.swift in Sources */,
				2A91C7992935DF79005A9155 /* CustomIcon.swift in Sources */,
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
				DEVELOPMENT_ASSET_PATHS = "\"GameCatalog/Preview Content\"";
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
				PRODUCT_BUNDLE_IDENTIFIER = com.dicoding.academy.GameCatalog;
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
				DEVELOPMENT_ASSET_PATHS = "\"GameCatalog/Preview Content\"";
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
				PRODUCT_BUNDLE_IDENTIFIER = com.dicoding.academy.GameCatalog;
				PRODUCT_NAME = "$(TARGET_NAME)";
				SWIFT_EMIT_LOC_STRINGS = YES;
				SWIFT_VERSION = 5.0;
				TARGETED_DEVICE_FAMILY = "1,2";
			};
			name = Release;
		};
/* End XCBuildConfiguration section */

/* Begin XCConfigurationList section */
		2A9D4526292C805D008FABEB /* Build configuration list for PBXProject "GameCatalog" */ = {
			isa = XCConfigurationList;
			buildConfigurations = (
				2A9D4537292C805F008FABEB /* Debug */,
				2A9D4538292C805F008FABEB /* Release */,
			);
			defaultConfigurationIsVisible = 0;
			defaultConfigurationName = Release;
		};
		2A9D4539292C805F008FABEB /* Build configuration list for PBXNativeTarget "GameCatalog" */ = {
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
		B53E70222DAE64D500C1123F /* XCRemoteSwiftPackageReference "GameCatalogAbout" */ = {
			isa = XCRemoteSwiftPackageReference;
			repositoryURL = "https://github.com/diki-haryadi/GameCatalogAbout.git";
			requirement = {
				kind = upToNextMinorVersion;
				minimumVersion = 1.0.1;
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
		B53E70232DAE64D500C1123F /* GameCatalogAbout */ = {
			isa = XCSwiftPackageProductDependency;
			package = B53E70222DAE64D500C1123F /* XCRemoteSwiftPackageReference "GameCatalogAbout" */;
			productName = GameCatalogAbout;
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


/== GameCatalog.xcodeproj/project.xcworkspace/contents.xcworkspacedata
<?xml version="1.0" encoding="UTF-8"?>
<Workspace
   version = "1.0">
   <FileRef
      location = "self:">
   </FileRef>
</Workspace>


/== GameCatalog.xcodeproj/project.xcworkspace/xcshareddata/IDEWorkspaceChecks.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>IDEDidComputeMac32BitWarning</key>
	<true/>
</dict>
</plist>


/== GameCatalog.xcodeproj/project.xcworkspace/xcshareddata/swiftpm/Package.resolved
{
  "originHash" : "f423457c471972d05aaf51e839ae6404009c3352979fabc75d660477dc232754",
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
      "identity" : "gamecatalogabout",
      "kind" : "remoteSourceControl",
      "location" : "https://github.com/diki-haryadi/GameCatalogAbout.git",
      "state" : {
        "revision" : "5f989ab47ca1e5256cfb1b8d436d0cf79521fea6",
        "version" : "1.0.1"
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


/== GameCatalog.xcodeproj/project.xcworkspace/xcuserdata/ben.xcuserdatad/IDEFindNavigatorScopes.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<array/>
</plist>


/== GameCatalog.xcodeproj/project.xcworkspace/xcuserdata/ben.xcuserdatad/UserInterfaceState.xcuserstate
bplist00�        
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�  	UState��R      9 : ; < = > ? @ A B C D E F G T Z [ a e f j k o p t u y z ~  � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � �  './0189@GOPW^_fgnu{�������������� ����������������������������������������������������� 	
"#'(,-1267;<@AEFJKOPTUYZ^_cdhimnrswx|}����������������������������������������������������	!"&'+,0156:;?@DEIJNOSTXY]^bcghlm���������������������������������������������%&'()12JKLMNOPQRSTX_ �`aijkuvwx|�������������������������������������#$%-./789CDEFJNVWopqrstuvwxy}������������������������������������� 23456789:;<@GHIQRS[\fghimqrs{|����������������������������������������
)*+,0456>?WXYZ[\]^_`aeltuv~�������������������������������������											 	$	+	3	4	5	?	@	A	B	F	J	R	S	T	\	]	u	v	w	x	y	z	{	|	}	~		�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�	�
 






2
3
4
5
6
7
8
9
:
;
<
@
G
H
P
Q
R
\
]
^
_
c
g
o
p
q
r
s
{
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
�	
 !%)1234<=UVWXYZ[\]^_cjrst|}~�������������������������������������� $+345?@ABFJRST\]uvwxyz{|}~������������������������������������	
456789:;<=>BIJRST\]^hijkos{|��������������������������������������&'()-19:RSTUVWXYZ[\`ghpqrz{|�������������������������������������!(012<=>?CGOPQYZrstuvwxyz{|������������������������������������123456789:;?FGOPQYZ[efghlpxy���������������������������������������&'()-19:RSTUVWXYZ[\`gopq{|}~�������������������������������� ()ABCDEFGHIJKOVW_`aklmnrv~������������������������������ 	
'()3456:>FG_`abcdefghimtuv~������������������������������������ !"#$%&'(,3;<=EFPQRSW[cd|}~�����������������������������������������	$%=>?@ABCDEFGKRZ[\defpqrsw{|��������������������������������� 	
 !)*+5678<@HIabcdefghijkovw�������������������������������������� 	!"#$%&'()*+/67?@AIJKUVWX\`hi�������������������������������������������	&'?@ABCDEFGHIMT\]^hijkos{|��������������������������������������	*+,-./012348?@HIJTUVW[_ghijrs������������������������������������������!"#$(,-56NOPQRSTUVWX\cklmuvw���������������������������������� !)*+345?@ABFJKSTlmnopqrstuvz������������������������������������� 	*+,-./012348?GHISTUVZ^fghpq������������������������������������������	 $()*23KLMNOPQRSTUY`hijrst~����������������������������������� ()*234>?@AEIQRjklmnopqrstx�����������������������������������&'()*+,-./04;CDEOPQRVZbcdlm������������������������������������������          " # $ , - E F G H I J K L M N O S Z [ c d e o p q r v z � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � �!!!!!!!	!
!!!!!! !!!"!*!+!5!6!7!8!<!@!H!I!a!b!c!d!e!f!g!h!i!j!k!o!v!~!!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�!�"""" "!"""#"$"%"&"'"(")"-"4"<"=">"H"I"J"K"O"S"["\"]"e"f"~""�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�"�###	#
#######$#<#=#>#?#@#A#B#C#D#E#F#J#Q#R#Z#[#\#f#g#h#i#m#q#y#z#{#|#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�#�$ $$$$$$$
$$$$$#$$$.$/$0$1$5$9$:$;$C$D$\$]$^$_$`$a$b$c$d$e$f$j$q$y$z${$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�$�% %%%%%%%%% %!%"%&%-%5%6%7%A%B%C%D%H%L%T%U%]%^%v%w%x%y%z%{%|%}%~%%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�%�&&	&
&&&&&&&5&6&7&8&9&:&;&<&=&>&?&C&J&R&S&T&^&_&`&a&e&i&q&r&z&{&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�&�''	''''''' '$'('0'1'9':'R'S'T'U'V'W'X'Y'Z'['\'`'g'h'i'q'r's'}'~''�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�'�(((((((((((("()(1(2(3(=(>(?(@(D(H(P(Q(R(Z([(s(t(u(v(w(x(y(z({(|(}(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�(�))))))))0)1)2)3)4)5)6)7)8)9):)>)E)M)N)O)W)X)b)c)d)e)i)m)n)v)w)})�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�)�* ************* *!*%*7*8*9*:*;*<*=*>*?*C*D*H*L*P*U*V*W*_*`*a*b*f*r*s*t*u*v*w*x*|*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�----------- -#-&-)-,-/-2-5-8-;->-A-D-G-J-M-P-S-V-Y-\-_-b-e-h-k-n-q-t-w-z-}-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�-�....
........".%.(.+...1.4.7.:.=.@.C.F.I.L.O.R.U.X.[.^.a.d.g.j.m.p.s.v.y.|..�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�.�/ ///	////////!/$/'/*/-/0/3/6/9/</?/B/E/H/K/N/Q/T/W/Z/]/`/c/f/i/l/o/r/u/x/{/~/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�/�00000000000 0#0&0)0,0/0205080;0>0A0D0G0J0M0P0S0V0Y0\0_0b0e0h0k0n0q0t0w0z0}0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�0�1111
11111111"1%1(1+1.1114171:1=1@1C1F1I1L1O1R1U1X1[1^1a1d1g1j1m1p1s1v1y1|11�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�1�2 222	22222222!2$2'2*2-202326292<2?2B2E2H2K2N2Q2T2W2Z2]2`2c2f2i2l2o2r2u2x2{2~2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�2�3 3333	3333333"3#3&3>3?3@3A3B3C3D3E3F3G3H3I3J3K3L3M3N3O3R3^3_3`3a3b3c3d3e3k3u3v3w3x3y3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�3�4444444444444&4'4(4)4*4+4,48494:4;4<4B4C4D4E4K4L4P4Q4W4X4\4]4e4f4g4q4r4s4t4u4}4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�5 5555555555555555%5&5'5A5B5C5D5E5F5G5H5I5J5K5L5O5P5Q5R5V5f5g5h5i5j5k5l5m5u5v5w5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�5�6 666666666	666666&6'61626364686<6D6E6I6R6Y6b6f6g6k6l6p6t6x6|6}6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�6�7777777777172737475767778797:7;7?7F7N7O7P7X7Y7c7d7e7f7j7n7v7w7x7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�7�8 88888888#8$8(8)8-818286878O8P8Q8R8S8T8U8V8W8X8Y8]8d8l8m8n8x8y8z8{88�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�8�99999999999#9+9,9-969=9F9J9K9O9P9T9X9\9`9a9e9f9~99�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�9�::::::::: :!:":#:$:(:/:7:8:9:C:D:E:F:J:N:V:W:_:`:i:p:x:|:}:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�:�;;;;;;;;!;%;&;*;+;C;D;E;F;G;H;I;J;K;L;M;Q;X;`;a;b;j;k;u;v;w;x;|;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�;�<<<<<<<<< <!<"<&<'<.<6<:<><B<F<J<K<O<[<\<]<^<_<k<l<m<n<o<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�<�U$null�      WNS.keysZNS.objectsV$class�  ���  ����|_IDEWorkspaceDocument_$9229D32F-C4FF-43F2-A95F-816FAA71C582�     ) 8�        ! " # $ % & ' (�����	�
��������� * + , - . / 0 1 2 3 - 5 6 0��*�k�n�r�p�s�q�s���n�����s�@_RecentEditorDocumentURLs_DefaultEditorStatesForURLs\ActiveScheme_ActiveProjectSetIdentifierKey_$RunDestinationArchitectureVisibility_DocumentWindows_EnableThreadGallery_WindowArrangementDebugInfo_RunContextRecents_ActiveRunDestination_ActiveProjectSetNameKey_SelectedWindows_0LastCompletedPersistentSchemeBasedActivityReport_BreakpointsActivated�   H S� I J K L M N O P Q R�������!�#�%�'�)� U  V W X YWNS.base[NS.relative� ��_zfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/DI/Injection.swift� \ ] ^ _Z$classnameX$classesUNSURL� ^ `XNSObject� U  V W X d� ��_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/Entity/GameEntity.swift� U  V W X i� ��_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/LocaleGameDataSource.swift� U  V W X n� ��_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Remote/RemoteGameDataSource.swift� U  V W X s� ��_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/GameRespository.swift� U  V W X x� �� _�file:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/SDWebImageSwiftUI/Package.swift� U  V W X }� ��"_�file:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/SDWebImage/Package.swift� U  V W X �� ��$_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/DetailView.swift� U  V W X �� ��&_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Presenter/DetailPresenter.swift� U  V W X �� ��(_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Router/DetailRouter.swift� \ ] � �WNSArray� � `�    � � 8� � � � � � � ��+�,�-�.�/�0�1� � � � � � � ��2�G�b�������@_'Xcode.IDEKit.EditorDocument.LogDocument_2Xcode.IDEKit.EditorDocument.DebugHierarchyDocument_:Xcode.IDEKit.EditorDocument.PegasusNonFileSystemSourceCode_-Xcode.IDEKit.EditorDocument.PegasusSourceCode_7Xcode.IDEKit.EditorDocument.MemoryGraphDebuggerDocument_(Xcode.IDEKit.EditorDocument.AssetCatalog_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project�    � � 8� � ��3�5� � ��7�A�@� U  V W X �� ��4_2x-xcode-log://DE4259B5-7817-44E7-8091-BC59E4CDE608� U  V W X �� ��6_2x-xcode-log://55CEB2A9-122B-4C7C-8B4C-1B6CFB5F6402�    � � 8� ��8� ��9�@_SelectedDocumentLocations�   � S� ŀ:�)�  � � � � � W � � �Ytimestamp_expandTranscript[documentURLYindexPath�?� �4�;� � �  � � �_NSIndexPathLength_NSIndexPathData�<�>� �  � �WNS.dataB�=� \ ] � �]NSMutableData� � � `VNSData� \ ] � �[NSIndexPath� � `[NSIndexPath� \ ] � �_IDELogDocumentLocation� � � `_IDELogDocumentLocation_DVTDocumentLocation� \ ] � �_NSMutableDictionary� � � `\NSDictionary�    � � 8� �B� ��C�@_SelectedDocumentLocations�   � S� ��D�)�  � � � � � W � � ��?� �6�E� � �  � � րF�>� �  �B �=�    8��H�J�	�L�Y�@� U  V W X� ��I_#x-xcode-debug-views:///7fe9b75fc020� U  V W X� ��K_#x-xcode-debug-views:///7fe9a34c3dd0�    8��M�N��O�P�@TzoomXrotation#?����#�	�  ! S�"#$%�Q�U�W�X�)�  ( S�)**,�R�S�S�T�)#?�      #        #��s�@   �  2 S�*)*6�S�R�S�V�)#?ۘL�   �  : S�**)*�S�S�R�S�)�  A S�***)�S�S�S�R�)�   HK 8��M�N�LM�Z�[�@#?����#�	�  Q S�RSTU�\�^�`�a�)�  X S�)**\�R�S�S�]�)#��R��   �  ` S�*)*d�S�R�S�_�)#?�g    �  h S�**)*�S�S�R�S�)�  o S�***)�S�S�S�R�)�   vx 8�w�c�y�e�@� U  V W X~� ��d_Nx-xcode-module://Foundation?language=Xcode.SourceCodeLanguage.Swift&swift-framework-paths=/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator&swift-header-paths=/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator&swift-module-cache-path=/Users/ben/Library/Developer/Xcode/DerivedData/ModuleCache.noindex&swift-sdk=iphonesimulator18.2&swift-target=x86_64-apple-ios16.1-simulator&toolchains=com.apple.dt.toolchain.XcodeDefault�   �� 8������������f�g�h�i�j�k�l�m�n�o�� 0 0 0�) . 0���p�s�s�s�t�R�r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���q�)�  � S� . . . .�r�r�r�r�) �   �� ����u�v����w�}�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����x�y����z�{�|^documentLength[lineIndexes�  � S��)� \ ] �Ȣ � `�   �� ���̀~������р������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��Wx&`@��G�[�x��� \ ]��VNSUUID�� `�� ��\NS.uuidbytesO�����D{���az6b���#@d�     �   �� ��倆������{�z�|Ufolds^documentLength	�   �Q 8�b������ Q�������� P O  J I	
 K !"#$%&'( N*+,- R/012345678 L: M<=>?@ABCDEFGHIJKLMNOP�������������%�����������������#�!�������������������������������ĀƀȀʀ̀΀Ѐ�ҀԀր؀ڀ܀ހ������������������'������� �����
������������� �"�$�&�(�*�,�.�0�2�4�6�8�bRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~�����������������������������������������������������:�W�w�����с�
�(�E�c�~�����Ё��#�A�\�z�����́���:�W�r�����Ł�����E�b�|�����΁��!�=�\�y�����́���F�c�������Ӂ���&�A�\�{�����΁�	�	�	:�	U�	q�	��	��	��	ہ	��
�
,�
I�
d�
��
��
��
΁
���<�V�s�����Ł��@� U  V W X�� ���_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Domain/UseCase/DetailInteractor.swift� U  V W X�� ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swift� U  V W X ���_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleGameDataSource.swift� U  V W Xǀ ���_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swift� U  V W X̀ ���_�file:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/realm-swift/Package.swift� U  V W Xр ���_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/ErrorLoggingDelegate.swift� U  V W Xր ���_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift� U  V W Xۀ ���_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift� U  V W X�� ���_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteRow.swift� U  V W X� ���_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Remote/Response/CategoriesResponse.swift� U  V W X� ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swift� U  V W X� ���_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Remote/Response/MealsResponse.swift� U  V W X� ���_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/CustomIcon.swift� U  V W X�� ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Remote/RemoteDataSource.swift� U  V W X�� ���_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/IngredientMapper.swift� U  V W X� ���_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/View/SearchRow.swift� U  V W X� ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swift� U  V W X� ���_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/MealsResponse.swift� U  V W X� ���_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift� U  V W X� ���_�file:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/GameCatalogAbout/Package.swift� U  V W X� ���_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift� U  V W X!� ���_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swift� U  V W X&� ���_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GamesResponse.swift� U  V W X+� ���_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Module/Untitled.swift� U  V W X0� ���_|file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift� U  V W X5� ���_hfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/TabItem.swift� U  V W X:� ���_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift� U  V W X?� ���_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel%202.swift� U  V W XD� ���_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GameDetailResponse.swift� U  V W XI� ���_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoriesResponse.swift� U  V W XN� ���_{file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/About/About.swift� U  V W XS� ���_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/DI/Injection.swift� U  V W XX� ���_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Network/URLSession+Extension.swift� U  V W X]� ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/LocaleDataSource.swift� U  V W Xb� ���_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/DI/Injection.swift� U  V W Xg� ���_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/App/GameCatalogApp.swift� U  V W Xl� ���_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/ContentView.swift� U  V W Xq� ���_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity.swift� U  V W Xv� ���_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/View/MealRow.swift� U  V W X{� ���_tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift� U  V W X�� ���_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/GameMapper.swift� U  V W X�� ���_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swift� U  V W X�� ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swift� U  V W X�� ���_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swift� U  V W X�� ���_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Domain/Model/CategoryModel.swift� U  V W X�� ���_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/Entity/MealEntity.swift� U  V W X�� ���_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/Untitled.swift� U  V W X�� ���_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Module/About/About.swift� U  V W X�� ���_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Meal/View/MealView.swift� U  V W X�� ���_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/App/ContentView.swift� U  V W X�� ���_efile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/AppLogger.swift� U  V W X�� ���_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift� U  V W X�� ���_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swift� U  V W X�� ���_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel.swift� U  V W Xƀ ���_|file:///Users/ben/belajar/ios-dicoding/project-expert/GameCatalogExpert/GameCatalog/Core/Domain/UseCase/HomeInteractor.swift� U  V W Xˀ ���_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/Entity/CategoryEntity.swift� U  V W XЀ ���_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteGameDataSource.swift� U  V W XՀ ���_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameDetailModel.swift� U  V W Xڀ ���_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity%202.swift� U  V W X߀ ��_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift� U  V W X� ��_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift� U  V W X� ��_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Home/Router/HomeRouter.swift� U  V W X� ��_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Utils/Mapper/CategoryMapper.swift� U  V W X� ��	_tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Meal/Presenter/MealPresenter.swift� U  V W X�� ��_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swift� U  V W X�� ��_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swift� U  V W X� ��_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/CategoryMapper.swift� U  V W X� ��_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swift� U  V W X� ��_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift� U  V W X� ��_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift� U  V W X� ��_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift� U  V W X� ��_�file:///Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/SourcePackages/checkouts/Alamofire/Source/Session.swift� U  V W X � ��_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swift� U  V W X%� ��_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/GameRespository.swift� U  V W X*� ��_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift� U  V W X/� ��!_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Domain/UseCase/HomeInteractor.swift� U  V W X4� ��#_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Domain/UseCase/HomeInteractor.swift� U  V W X9� ��%_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swift� U  V W X>� ��'_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swift� U  V W XC� ��)_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Home/View/HomeView.swift� U  V W XH� ��+_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/CategoryRow.swift� U  V W XM� ��-_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/MealRepository.swift� U  V W XR� ��/_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swift� U  V W XW� ��1_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/ImageUtility.swift� U  V W X\� ��3_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swift� U  V W Xa� ��5_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/Entity/IngredientEntity.swift� U  V W Xf� ��7_xfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/App/ContentView.swift� U  V W Xk� ��9_�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/MealRow.swift�   ny 8�opqrstuvwx�;�<�=�>�?�@�A�B�C�D�z� 0 0~� 0���E���s�s�G�S�T�s�U���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���F�)�  � S� . . . .�r�r�r�r�)�   �� ����H�I����J�M�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����K�y����L�{�|^documentLength��   �� �����N�O�P���сQ�R���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO!���Jߕ�W��h����� ��\NS.uuidbytesO5�
6K���X����#@1      �   �� ��Ȁ��V����{�L�|^documentLength�   �� 8����������فX�Y�Z�[�\�]�^�_�`�a��� 0 0��� 0���b���s�s�g�s�t�s�u���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S��c�)�  � S������d�e�d�f�)	�   � � �h�i��j�p�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   	 �
�k�l�m�сn�o���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width� �\NS.uuidbytesO5���A�Mრ������ �\NS.uuidbytesO2��[G0���V��q���   ! �� �y�q��#�{�r�|^documentLengthn#@(      Q�   *- ��,���v��#�{�r�|^documentLength�   3> 8�456789:;<=�x�y�z�{�|�}�~������?� 0 0CDE 0G������s�s�������s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  U S�V���)�  Y S�Z[\ .�������r�)eq�   be �cd�����fg�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   lp �mno�������qrс������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�y z�\NS.uuidbytesO�����N΄z����Ug���} ~�\NS.uuidbytesO�f�0{Ba���#���   �� ������y������{�|^documentLength�#@0�     ^�   �� �����������{���|^documentLength�   �� 8���������������������������������� 0 0�� . 0�������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S��������z���z�)�   �� ��Ɓ�����Ɂ����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ������y������{�|^documentLengthV�   �� ���ہ��������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO%���AJ�E-E0������ ��\NS.uuidbytesO�u~E�D����π�#��      �   �� ���������Ҁ{���|^documentLength�   � 8��������� ���������������������� 0 0* . 0������s�s�ÀS�r�s�π��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S����)�   S������)
!�   &) �'(�āŢ*+�Ɓɀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   03 ��2�y�Ǣ�5�{�Ȁ|^documentLength.!�   :> �;<=�ʁˁ̣?@с́΀��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�G H�\NS.uuidbytesO8�Ѐ�B���`	�����K L�\NS.uuidbytesOA,�Ց�L��%�K�����   OR ��Q���Т�5�{�Ȁ|^documentLength�   Xc 8�YZ[\]^_`ab�ҁӁԁՁցׁ؁فځ۪d� 0 0h� . 0l��܀��s�s�߁��r�s�ꀈ�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  z S�{�݀)�  ~ S�[[�ށ��ށ��)�   �� ������������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y����{�t�|^documentLength�   �� ����������с�逄�|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO����c�BЭb�Aq��|���� ��\NS.uuidbytesOog�)
K<�]%܋�O����   �� ���������{�t�|^documentLength�   �� 8��������������������������� 0 0�� . 0�������s�s�����r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ځ��)�  � S�� .� .���r���r�)K�   �� ������������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ������ ����с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO���!��I��׺3�4��� �\NS.uuidbytesO�/�e�r@��/�:�P�8���    ���y��	
���|^documentLength�   S��)q�    �����	�	
���|^documentLength�   & 8� !"#$%�����������'� 0 0+,- 0/�����s�s��$�%�s�&���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  = S�>��)�  A S�BCBC�����)�   JM �KL���NO���|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   TW ��V�y���
�{��|^documentLength�   ]a �^_`�� �!�bcс"�#���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�j k�\NS.uuidbytesOA�bMO���U{�{����n o�\NS.uuidbytesOE�ū=�L��1�;�����#@0�     8�   tw ��v���'��
�{��|^documentLength�   }� 8�~���������)�*�+�,�-�.�/�0�1�2��� 0 0�� . 0���3���s�s�7���r�s�C���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���4�)�  � S������5�6�5�6�)�   �� ����8�9����:�=�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�;����{�<�|^documentLength��   �� ���Á>�?�@���сA�B���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOp��-r@CЗvP�m/���� ��\NS.uuidbytesO���w��A��d��3n���   �� ��׀��D����{�<�|^documentLength�   �� 8�����������F�G�H�I�J�K�L�M�N�O��� 0 0��� 0���P���s�s�S�_�`�s�a���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�    S��Q�)�   S��� .�T�T�R�r�)D�    ��T�U��V�Y�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ���W�y���X�{�|^documentLength:�    $ �!"#�Z�[�\�%&с]�^���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�- .�\NS.uuidbytesO|X#ôNJM��{ՀG؀��1 2�\NS.uuidbytesOj�Z�J��m�y<6���#@      -�   7: ��9���b���{�X�|^documentLength�   @K 8�ABCDEFGHIJ�d�e�f�g�h�i�j�k�l�m�L� 0 0P* . 0T��n���s�s�p�S�r�s�|���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  b S�c�o�)�  f S� . . . .�r�r�r�r�)�   mp �no�q�r�qr�s�v�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   wz �x��t�y�{��u�{�|^documentLength�   �� �����w�x�y���сz�{���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO6�|t^K��芭F)���� ��\NS.uuidbytesO�J�YBG~�r��2S����   �� ������}��{�{�u�|^documentLength�   �� 8��������������������������������� 0 0�� . 0�������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���)�  � S��[� .�T�����r�)�   �� ��ρ�����ҁ����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���ځ��������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�lx�L����u\j���� ��\NS.uuidbytesO��]�`LEX�`��n-�ǀ��   �� ������y������{�|^documentLength��   �� �����������{���|^documentLength�   �	
 8�	 										���������������������	� 0 0	* . 0	������s�s���S�r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	! S�	"���)�  	% S� . . . .�r�r�r�r�)�   	,	/ �	-	.�����	0	1�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   	6	: �	7	8	9�������	;	<с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�	C 	D�\NS.uuidbytesOan�!�{G�c�{��Ղ���	G 	H�\NS.uuidbytesO�V�R��C~�����{-���   	K	N �	L����y�	O����{�|^documentLength��   	U	X ��	W������	O�{���|^documentLength�   	^	i 8�	_	`	a	b	c	d	e	f	g	h���������������������	j� 0 0	n* . 0	r������s�s�S�r�s�΀��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	� S�	����)�  	� S� . . . .�r�r�r�r�)�   	�	� �	�	��ÁĢ	�	��ŁȀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   	�	� ��	��y�Ƣ�	��{�ǀ|^documentLength��   	�	� �	�	�	��Ɂʁˣ	�	�с́̀��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�	� 	��\NS.uuidbytesO���]AM.�!���_�z���	� 	��\NS.uuidbytesO��/E�G)�G�,�vCƀ��   	�	� ��	����Ϣ�	��{�ǀ|^documentLength�   	�	� 8�	�	�	�	�	�	�	�	�	�	ǁсҁӁԁՁցׁ؁فڪ	�� 0 0	�* . 0	���ۀ��s�s�݀S�r�s�耈�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  	� S�	��܀)�  	� S� . . . .�r�r�r�r�)�   	�	� �	�	�ށߢ	�	����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   	�	� �	�	�	�����	�	�с�倄�|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�
 
�\NS.uuidbytesO�5o�mDI|�w�&��Oހ��
 
�\NS.uuidbytesOtx���KM����ÕgȀ��   
	
 ��
�y���#�{�r�|^documentLength�   

 ��
�����#�{�r�|^documentLength�   

& 8�




 
!
"
#
$
%�������������
'� 0 0
+
,
- 0
/������s�s�����s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
= S�
>���)�  
A S�
B�
B����������)��   
I
L �
J
K�����
M
N����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   
S
W �
T
U
V�������
X�
Z����� �|_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�
` 
a�\NS.uuidbytesO6)���?Au�&
�1�)���
d 
e�\NS.uuidbytesO�Z3^=�C��\$�̀��   
h
k �
i���y�
l	���|^documentLength#@$      ��   
t
w ��
v����	
l���|^documentLength�   
}
� 8�
~

�
�
�
�
�
�
�
��	�
���������
�� 0 0
�* . 0
������s�s��S�r�s�!���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
� S�
���)�  
� S� . . . .�r�r�r�r�)�   
�
� �
�
����
�
����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   
�
� �
�
�
�����
��
������|_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController�
� 
��\NS.uuidbytesOĎ��-�D�K���V���
� 
��\NS.uuidbytesO�Շq+F��AQ9��U���   
�
� ��
ˀy��	
΁� �|^documentLength}�   
�
� ��
Հ��"�	
΁� �|^documentLength�   
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
�$�%�&�'�(�)�*�+�,�-�
�� 0 0
��
� 0
���.���s�s�2�_�>�s�?���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  
� S�
��/�)�   S�� .�T�0�1�r�)v�    ��3�4��5�;�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�    ��6�7�8�с9�:���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�" #�\NS.uuidbytesO:�����Gg���=W'����& '�\NS.uuidbytesOi��7TN������Ӏ��   *- ��,�y�<��/�{�=�|^documentLength�_�   58 ��7���@��/�{�=�|^documentLength�   >I 8�?@ABCDEFGH�B�C�D�E�F�G�H�I�J�K�J� 0 0N* . 0R��L���s�s�N�S�r�s�Z���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ` S�a�M�)�  d S� . . . .�r�r�r�r�)�   kn �lm�O�P�op�Q�T�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ux �v��R�y�y	�S��|^documentLength�   � �����U�V�W���сX�Y���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�?�;�K:�5���W������ ��\NS.uuidbytesOv�_�]C̗���� 瀂�   �� ������[�	y��S�|^documentLength�   �� 8������������]�^�_�`�a�b�c�d�e�f��� 0 0�
,� 0���g���s�s�k��w�s�x���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���h�)�  � S����Łi�j�i�j�)>�   �� ��΁l�m��сn�q�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��؀y�o��ۀ{�p�|^documentLength��   �� ����r�s�t���сu�v���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOR>��)A��`~:����� ��\NS.uuidbytesOΕ>�4E��7�٬����   �� ������y��ۀ{�p�|^documentLength�   �
 8� 	�{�|�}�~������������� 0 0� . 0������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ! S�"���)�  % S�C�C���z��z�)�   ,/ �-.�����01�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   6: �789�������;<с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�C D�\NS.uuidbytesO=�-��@[��ƗN�I���G H�\NS.uuidbytesOr��qM��t���d�Ѐ��   KN ��M�y����P�{���|^documentLengthf�   UX ��W������P�{���|^documentLength�   ^i 8�_`abcdefgh���������������������j� 0 0nop 0r������s�s�������s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S� . . . .�r�r�r�r�)�   �� ���������������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO&���qyI�@d�d�ŀ��� ��\NS.uuidbytesO�`�)�B��4c�)����   �� ����y����	O�{���|^documentLength#@0�     g�   �� ���������	O�{���|^documentLength�   �� 8����������ȁ���������������������� 0 0�� . 0�������s�s�����r�s�ʀ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�  � S��[�[���������)�   �� ������������Ā|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�¢���{�À|^documentLengthb�   � � �ŁƁǣсȁɀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width� �\NS.uuidbytesO�lHn��N[�g����g)��� �\NS.uuidbytesO�n9��L{���,����    �����ˢ���{�À|^documentLength�   ( 8� !"#$%&'�́΁ρЁсҁӁԁՁ֪)� 0 0-� . 01��׀��s�s�ځ��r�s�怈�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ? S�@�؀)�  C S�D�D��فd�فd�)*�   KN �LM�ہܢOP�݁��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   UX �V��ހy�Y	�߁�|^documentLengthV�   _c �`ab�����ef�����|_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore�l m�\NS.uuidbytesOK�h���E͖���\"����p q�\NS.uuidbytesO��\�9Lյj3������   tw ��v����	Y��߀|^documentLength�   }� 8�~���������������������� 0 0�* . 0���󀈀s�s���S�r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�  � S� . . . .�r�r�r�r�)�   �� ���������������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ������y������{�|^documentLengthg�   �� �������������с�� ���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOhv��P�H'�IK������� ��\NS.uuidbytesOY�#� B߫A�v
<JP���   �� ��Հ������{���|^documentLength�   �� 8����������������	�
������ 0 0�* . 0������s�s��S�r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�   S������ށ��ހ)�   	 �
������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ���y����{��|^documentLength��   ! � ����"#с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�* +�\NS.uuidbytesO����nD)�ʌ��<�_���. /�\NS.uuidbytesO���>EؔH�1��G���   25 ��4������{��|^documentLength�   ;F 8�<=>?@ABCDE�� �!�"�#�$�%�&�'�(�G� 0 0K� . 0O��)���s�s�,���r�s�8���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ] S�^�*�)�  a S�b[b[�+���+���)7�   il �jk�-�.�mn�/�2�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   sv ��u�y�0��x�{�1�|^documentLength��   }� �~��3�4�5���с6�7���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��Y�Eբ^����}���� ��\NS.uuidbytesOO�BB�H��цdV�%ʀ��   �� ������9��x�{�1�|^documentLength�   �� 8������������;�<�=�>�?�@�A�B�C�D��� 0 0�� . 0���E���s�s�I���r�s�U���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���F�)�  � S����ÁG�H�G�H�)41�   �� ��́J�K��ρL�R�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���ׁM�N�O���сP�Q���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�@�<��E3�eKI��ƀ��� ��\NS.uuidbytesO�'Bf���su>���   �� ����S�y����T�{�|^documentLength��   �� ������V���{�T�|^documentLength�   � 8���� �X�Y�Z�[�\�]�^�_�`�a�� 0 0� . 0��b���s�s�d���r�s�p���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S��c�)�  " S� . . . .�r�r�r�r�)�   ), �*+�e�f�-.�g�m�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   37 �456�h�i�j�89сk�l���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�@ A�\NS.uuidbytesO�,߅cD9���x��@���D E�\NS.uuidbytesO�pg�Af�K"�)���   HK ��J�y�n��M�{�o�|^documentLengthM�   RU ��T���q��M�{�o�|^documentLength�   [f 8�\]^_`abcde�s�t�u�v�w�x�y�z�{�|�g� 0 0k� . 0o��}���s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  } S�~�~�)�  � S�������z��z�)2�   �� ���������������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO���E��R�V��:���� ��\NS.uuidbytesO:h
�Eޣʋ��s����   �� ����y������{���|^documentLength	�   �� �����������{���|^documentLength�   �� 8����������Ł���������������������� 0 0�* . 0�������s�s���S�r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ށ��)�  � S� . . . .�r�r�r�r�)�   �� �������������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ���y������{���|^documentLength�   �  �����������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�	 
�\NS.uuidbytesO���y��J)�]��$E��� �\NS.uuidbytesOwuͥ�MO�S��D-�
���    ����������{���|^documentLength�   % 8� !"#$���������������������&� 0 0*� . 0.������s�s�����r�s�À��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  < S�=���)�  @ S�A�A��G���G�)P�   HK �IJ�����LM�����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   RU ��T�y����W�{���|^documentLength �   \` �]^_�������abс����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�i j�\NS.uuidbytesO}v6�B��wx$Rg�B���m n�\NS.uuidbytesO&Wf�}@���rɔ��ـ��   qt ��s���Ģ�W�{���|^documentLength�   z� 8�{|}~������ƁǁȁɁʁˁ́́΁Ϫ�� 0 0�� . 0���Ѐ��s�s�ҁ��r�s�ހ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���р)�  � S� . . . .�r�r�r�r�)�   �� ����ӁԢ���Ձ؀|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�֢���{�׀|^documentLength��   �� �����فځۣ��с܁݀��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO&�,���K̡��MT�E�� ��\NS.uuidbytesO��#�?J�������,���   �� ��Ҁ��ߢ���{�׀|^documentLength�   �� 8����������������������� 0 0�� . 0���뀈�s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�  � S�  �����)(�   	 �
�������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ����y�����{�|^documentLength�   ! � �������"#с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�* +�\NS.uuidbytesO��ZD F��Ve�>�À��. /�\NS.uuidbytesOU���A��;��.V���   25 ��4�������{���|^documentLength�   ;F 8�<=>?@ABCDE��� ���������G� 0 0K� . 0O��	���s�s����r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ] S�^�
�)�  a S� . . . .�r�r�r�r�)�   hk �ij���lm���|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   rv �stu����wxс����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width� ��\NS.uuidbytesO;d1�I���L�X5���� ��\NS.uuidbytesOp&��1�JĈ�[1��ۀ��   �� ����y���{�{�u�|^documentLength�   �� ��������{�{�u�|^documentLength�   �� 8�������������������� �!�"�#��� 0 0��� . 0���$���s�s�&���2�r�s�C���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���%�)�  � S������d���d���)�   �� ��ˁ'�(��΁)�,�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��Հy�*�	؁�+�|^documentLength%�   �� �����-�.�/���䀄�0�1�|_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�� ��\NS.uuidbytesOT�F���A�����fì���� ��\NS.uuidbytesOsM���Du���^k瀂�   �� 8��������3�4�5�6�7�8�) 0�����R�s�9�>�?�B�@_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_Previews.Editor.CanvasMode_XcodePreviews.PinnedPreview_Previews.Editor.DestinationMode_Previews.Editor.Overrides�    �	
�:�;��<�=�|Vtarget\discriminantUlocalTliveP�    ��@��A�|\discriminantYautomatic�    ���|�   !$ ��#���D�	؁�+�|^documentLength�   *5 8�+,-./01234�F�G�H�I�J�K�L�M�N�O�6� 0 0:;< 0>��P���s�s�S�^�_�s�`���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  L S�M�Q�)�  P S�Q�Q��R�T�R�5�),�   X[ �YZ�T�U�\]�V�\�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   bf �cde�W�X�Y�ghсZ�[���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�o p�\NS.uuidbytesO	ڲ��N���+����s t�\NS.uuidbytesO����M���56�n{����   wz ��y�y�]��Y�{�߀|^documentLength#@.      �   �� ������a��Y�{�߀|^documentLength�   �� 8������������c�d�e�f�g�h�i�j�k�l��� 0 0�� . 0���m���s�s�o���r�s�z���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���n�)�  � S�� .� .���r���r�)�   �� ����p�q����r�x�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���Łs�t�u���сv�w���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOO�sYMƴ���QI����� ��\NS.uuidbytesO�E �L)���Zr�����   �� ����y�y����t�{�|^documentLength�   �� �� �{���{�t�|^documentLength�   �� 8�����������}�~������������������ 0 0�* . 0�������s�s���S�r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S����)�   S� . . . .�r�r�r�r�)�    �����������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    # �!����y�$����{�|^documentLength3��   *. �+,-�������/0с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�7 8�\NS.uuidbytesO�~��Rk@��vK̈́���; <�\NS.uuidbytesO'^#�J|�QW��倂�   ?B ��A������$�{���|^documentLength�   HS 8�IJKLMNOPQR���������������������T� 0 0XY� 0\������s�s�����5�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  j S�k���)�  n S�opop���������)5�   wz �xy�����{|�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�tB���Bx�|^�y;���� ��\NS.uuidbytesO�vBR�L���+a�J适�   �� ����y����Y�{�߀|^documentLength#@      �   �� ���������Y�{�߀|^documentLength�   �� 8���������������������������������� 0 0�* . 0�������s�s���S�r�s�̀��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�́��)�  � S�CoCo�������)�   �� ��؁â�ہāʀ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ����ŁƁǣ��сȁɀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��F-�G��a&�e�Dހ��� ��\NS.uuidbytesO/��ԧf@b�̍W\_�l���   �� ����y�ˢ�Ҁ{���|^documentLength�   � �� ���͢�Ҁ{���|^documentLength�    8�	
�ρЁсҁӁԁՁցׁت� 0 0* . 0��ـ��s�s�ۀS�r�s�怈�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ) S�*�ڀ)�  - S� . . . .�r�r�r�r�)�   47 �56�܁ݢ89�ށ��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   >A ��@�y�ߢ	�����|^documentLength�   GK �HIJ�����MN�����|_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�T U�\NS.uuidbytesO�t9�+G����@�`Ċ���X Y�\NS.uuidbytesO�]��{ JZ��&���O���   \_ ��^����	�����|^documentLength�   ep 8�fghijklmno������������q� 0 0uY 0y��󀈀s�s�����s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S����)�  � S��������e���e�)#�   �� ���������������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOd�љ�O޸��iì����� ��\NS.uuidbytesO� q�p�Ho��[��Y����   �� ���� �y�����{�|^documentLength��   �� ����������{��|^documentLength�   �� 8����������ρ����	�
������� 0 0��� 0������s�s����s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���)�  � S����������))�   �� �����������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   � ��� ����с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�
 �\NS.uuidbytesOӤ�>H3��&���퀂� �\NS.uuidbytesO���&D�C����
�����    ����y�y��S�{�|^documentLength#@       �     ����� ��y�{�S�|^documentLength�   &1 8�'()*+,-./0�"�#�$�%�&�'�(�)�*�+�2� 0 06�8 0:��,���s�s�.�_�:�s�;���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  H S�I�-�)�  L S��[p .�������r�)�   SV �TU�/�0�WX�1�4�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ]` �^��2�y�a��3�{�|^documentLength{�   gk �hij�5�6�7�lmс8�9���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�t u�\NS.uuidbytesO����WD1Ha������x y�\NS.uuidbytesO�se}AbO�;�t'��W���   }� �����<��a�{�3�|^documentLength�   �� 8������������>�?�@�A�B�C�D�E�F�G��� 0 0��� 0���H���s�s�L�X�Y�s�Z���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���I�)�  � S������J�K�J�K�)0;�   �� ����M�N����O�R�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�P��Ā{�Q�|^documentLength��   �� ���́S�T�U���сV�W���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��[���B��5���?����� ��\NS.uuidbytesOe��!��C ������,��#@0�      �   �� �� �[��Ā{�Q�|^documentLength�   �� 8�����������]�^�_�`�a�b�c�d�e�f��� 0 0�� . 0���g���s�s�k���r�s�w���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S��h�)�   S��i�j�i�j�)@�    ��l�m��n�q�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   "% ��$�y�o��'�{�p�|^documentLengthG�   ,0 �-./�r�s�t�12сu�v���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�9 :�\NS.uuidbytesO��G�Aε�g��U�Z���= >�\NS.uuidbytesOf�O]kG� �Xqʭ����   AD ��C���x��'�{�p�|^documentLength�   JU 8�KLMNOPQRST�z�{�|�}�~����������V� 0 0Z� . 0^������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  l S�m���)�  p S�q�q����������)�   x{ �yz�����|}�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO}ja��JŃB�;4*3����� ��\NS.uuidbytesO0�#��A �j\3��W���   �� ����y������{���|^documentLengthE�   �� �����������{���|^documentLength�   �� 8���������������������������������� 0 0�� . 0�������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�́��)�  � S����ҁ��������)�+�   �� ��ہ�����ށ����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ���y����$�{���|^documentLength�   �� ������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO"�qBGܽXw�w�j���� ��\NS.uuidbytesO�}2�ѺN��+��Kq�m���    ��������$�{���|^documentLength�   
 8����������������������� 0 0� . 0������s�s�����r�s�ˀ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  , S�-���)�  0 S��[3 .�������r�)��   8; �9:�����<=�ŀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   BE �C��Ày�F��Ā{�|^documentLengthq�   LP �MNO�ƁǁȣQRсɁʀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�Y Z�\NS.uuidbytesO����pJ��i�����À��] ^�\NS.uuidbytesO�����O���3��݀��   ad ��c���̢�F�{�Ā|^documentLength�   ju 8�klmnopqrst�΁ρЁсҁӁԁՁցתv� 0 0z� . 0~��؀��s�s�ځ��r�s�怈�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���ـ)�  � S� . . . .�r�r�r�r�)�   �� ����ہܢ���݁��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�ޢ���{�߀|^documentLength��   �� ����������с�倄�|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�(n�OyN�����d�s���� ��\NS.uuidbytesO��_i	�A��W]f���   �� ��������{�߀|^documentLength�   �� 8����������Ӂ������������� 0 0�* . 0���󀈀s�s���S�r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���)�  � S� . . . .�r�r�r�r�)�   �� ���������������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�     �����y�����{�|^documentLengthU�   
 ��������с�� ���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width� �\NS.uuidbytesO6�Г�@��1ki�~L��� �\NS.uuidbytesO��B�OC�Ȯg��z���   " ��!������{���|^documentLength�   (3 8�)*+,-./012������	�
����4� 0 08� . 0<�����s�s����r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  J S�K��)�  N S� . . . .�r�r�r�r�)�   UX �VW���YZ���|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   _c �`ab����deс����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�l m�\NS.uuidbytesO*ExV�E ��>� D���p q�\NS.uuidbytesOo{Q'��O�4Z �V9����   tw �u���y����z�{�|^documentLength�   }� ���������{�z�|^documentLength�   �� 8��������������� �!�"�#�$�%�&�'�(��� 0 0��� 0���)���s�s�,�S�8��s�D���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���*�)�  � S������j�+�j�+�)�   �� ����-�.����/�2�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��Ày�0��ƀ{�1�|^documentLength ��   �� ���΁3�4�5���с6�7���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesOA��ռIt��y�"a쀂�� ��\NS.uuidbytesO/e�AC�Q�*c�t ���   �� 8�������9�:�;�<�=�>�) 0����R�s�?�>�A�B�@_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_Previews.Editor.CanvasMode_XcodePreviews.PinnedPreview_Previews.Editor.Overrides_Previews.Editor.DestinationMode�   �� �	��:�@��<�=�|\discriminant�   �  ���|�    ��C��A�|\discriminant�   
 �����E��ƀ{�1�|^documentLength�    8��G�H�I�J�K�L�M�N�O�P�� 0 0#Y% 0'��Q���s�s�T���`�s�a���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  5 S�6�R�)�  9 S��[< .�����S�r�)��   AD �BC�U�V�EF�W�]�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   KO �LMN�X�Y�Z�PQс[�\���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�X Y�\NS.uuidbytesO=d���M���OХ�B���\ ]�\NS.uuidbytesO�G5��J�Trk�ۮ���   `c �a��^�y�d��_�{�|^documentLengthm�   kn ��m���b��d�{�_�|^documentLength�   t 8�uvwxyz{|}~�d�e�f�g�h�i�j�k�l�m��� 0 0��� 0���n���s�s�r�_�~�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���o�)�  � S��������p�q�)6�   �� ����s�t����u�{�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� �����v�w�x���сy�z���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��+9A���C\`kc���� ��\NS.uuidbytesO�f@ΈAׁ�U+uYM����   �� ��Āy�|��ǀ{�}�|^documentLengthK�   �� ��π�����ǀ{�}�|^documentLength�   �� 8���������������������������������� 0 0��� 0�������s�s���s���s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S��������z���z�)u�    ������	�����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    ���y�����{���|^documentLength��    ��������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�% &�\NS.uuidbytesO���XZ�M�G����Z2���) *�\NS.uuidbytesO/z~g*K"��!�1-���c�   .1 ��0�������{���|^documentLength�   7B 8�89:;<=>?@A���������������������C� 0 0G� . 0K������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  Y S�Z���)�  ] S������Y���Y�)�   dg �ef�����hi�����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   nq �o����y�r����{�|^documentLength�   x| �yz{�������}~с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO���-�bF1�S!�XN���� ��\NS.uuidbytesO�u��sMHE��_�+��&���   �� ���������r�{���|^documentLength�   �� 8����������������������������ê�� 0 0�* . 0���Ā��s�s�ƀS�r�s�р��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���ŀ)�  � S� . . . .�r�r�r�r�)�   �� ��ŁǁȢ�ȁɁˀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ��πy�ʢ	�����|^documentLength�   �� ���ف́́Σ��сρЀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�5Co�J��G/��Q���� ��\NS.uuidbytesO3��;-�O_�զ�1/����   �� ��퀆�Ң	�����|^documentLength�   �� 8������������ԁՁցׁ؁فځہ܁ݪ � 0 0� 0��ހ��s�s��_��s��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S��߀)�   S�� .�T�����r�)��   "% �#$���&'���|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ,/ ��.�y���1�{��|^documentLength��   6: �789����;<с�쀄�|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�C D�\NS.uuidbytesOYK�~k�I����IlQ;���G H�\NS.uuidbytesO�I�k��B�}'5Ҭ����   LO ��N�����1�{��|^documentLength�   U` 8�VWXYZ[\]^_�����������������a� 0 0e� . 0i������s�s�����r�s�
���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  w S�x���)�  { S�|�|Á��H���H�)"�   �� ������ ������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ����������с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��Ư-Nz�22qY�:适�� ��\NS.uuidbytesOe\�$�I+�+�}+�����   �� �����y����	�{�|^documentLength��   �� ����������{�	�|^documentLength�   �� 8������������������������ 0 0�* . 0������s�s��S�r�s�$���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�؁�)�  � S� . . . .�r�r�r�r�)�   �� ��������"�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���������с �!���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO���NMB��n�S���� ��\NS.uuidbytesO�����D,���̼d~���    ���y�#����{�<�|^documentLength�   
 �����%����{�<�|^documentLength�    8��'�(�)�*�+�,�-�.�/�0�� 0 0#* . 0'��1���s�s�3�S�r�s�?���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  5 S�6�2�)�  9 S� . . . .�r�r�r�r�)�   @C �AB�4�5�DE�6�<�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   JN �KLM�7�8�9�OPс:�;���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�W X�\NS.uuidbytesO=U���F���������[ \�\NS.uuidbytesOb;2��E���/s��u���   _b �`��=�y�c��>�{�|^documentLength��   il ��k���@��c�{�>�|^documentLength�   r} 8�stuvwxyz{|�B�C�D�E�F�G�H�I�J�K�~� 0 0�� . 0���L���s�s�N���r�s�Z���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���M�)�  � S�� .� .�T�r�f�r�)�   �� ����O�P����Q�T�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�R�	���S�|^documentLength��   �� �����U�V�W���сX�Y���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO,Q��3TD���ӵj-g���� ��\NS.uuidbytesO�4�^�TAކ�{�P9���   �� ��ʀ��[�	���S�|^documentLength�   �� 8����������ہ]�^�_�`�a�b�c�d�e�f��� 0 0��� 0���g���s�s�k�w�x�s�y���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S��h�)�  � S�� .���i�r�i�j�)H:�     ��l�m��n�q�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   
 ���o�y���p�{�|^documentLength1c�    ��r�s�t�сu�v���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�! "�\NS.uuidbytesO��O-�H˴�Ce�뿀��% &�\NS.uuidbytesO0�c4FD������4<���#@      B�   +. ��-���z���{�p�|^documentLength�   4? 8�56789:;<=>�|�}�~��������������@� 0 0DEC 0H������s�s������s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  V S�W���)�  Z S�CC��j��j�)�   ad �bc�����ef�����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   kn ��m�y����p�{���|^documentLength��   uy �vwx�������z{с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��$���O8�t�b�,U���� ��\NS.uuidbytesO�R/�P]@k��͜�P����#@0�     �   �� ���������p�{���|^documentLength�   �� 8���������������������������������� 0 0�* . 0�������s�s���S�r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�����)�  � S������������)�   �� ��ā�����ǁ����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   �� ���ρ��������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�U^q3[B��	.ii������ ��\NS.uuidbytesO���b��Op�����a�D���   �� ������y������{�|^documentLength$�   �� ��퀆�����{���|^documentLength�   �� 8�������������������������������� � 0 0� . 0������s�s�����r�s�̀��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   S����)�   S������+�z�+�z�)�   !$ �"#���¢%&�Áƀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   +. ��-�y�Ģ�0�{�ŀ|^documentLength��   59 �678�ǁȁɣ:;сʁˀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�B C�\NS.uuidbytesOUFz�["J�����7Y���F G�\NS.uuidbytesO3�̹+�C/��}ߊ���   JM ��L���͢�0�{�ŀ|^documentLength�   S^ 8�TUVWXYZ[\]�ρЁсҁӁԁՁցׁت_� 0 0c* . 0g��ـ��s�s�ۀS�r�s�怈�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  u S�v�ڀ)�  y S� . . . .�r�r�r�r�)�   �� ����܁ݢ���ށ��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����߀y�����{�|^documentLength�   �� ����������с�倄�|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO�����H��Ϡ���耂�� ��\NS.uuidbytesO�C�ӲK���z�w�w����   �� ���������{���|^documentLength�   �� 8������������������������� 0 0�� . 0���󀈀s�s�����r�s�	 ���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S�ԁ�)�  � S������w���w���)�   �� ��������������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ������y����À{�|^documentLength�   �� ������������с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO��MJsK��s�������� �\NS.uuidbytesOY$0A@è���뢹k���   	 �����	����{�À|^documentLength�    8��	�	�	�	�	�	�		�	
�	�	�� 0 0* . 0#��	���s�s�	�S�r�s�	���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  1 S�2�	�)�  5 S�88�0�:�0�:�)�   <? �=>�	�	�@A�	�	�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   FJ �GHI�	�	�	�KLс	�	���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�S T�\NS.uuidbytesO1�'�J?�+��A����W X�\NS.uuidbytesO!*���Iʩ��8�YƮ���   [^ �\��	�y�_��	�{�|^documentLength��   eh ��g���	��_�{�	�|^documentLength�   ny 8�opqrstuvwx�	�	�	 �	!�	"�	#�	$�	%�	&�	'�z� 0 0~�� 0���	(���s�s�	+�_�	7�s�	8���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S���	)�)�  � S��[� .�����	*�r�)=�   �� ����	,�	-����	.�	1�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   �� ����y�	/����{�	0�|^documentLengthM�   �� �����	2�	3�	4���с	5�	6���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�� ��\NS.uuidbytesO4k� ��DW�����i耂�� ��\NS.uuidbytesO��I�mC�����;n���&�   �� ��Ȁ��	9����{�	0�|^documentLength�   �� 8����������ف	;�	<�	=�	>�	?�	@�	A�	B�	C�	D��� 0 0�* . 0���	E���s�s�	G�S�r�s�	S���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  � S��	F�)�  � S�B�B���z��z�)�   �� ����	H�	I�   �	J�	P�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�     
 �   	�	K�	L�	M�  с	N�	O���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�   �\NS.uuidbytesO��@��G�V�������   �\NS.uuidbytesO����O��}WKE����      �� �y�	Q��  �{�	R�|^documentLength��    % ( �� '���	T��  �{�	R�|^documentLength�    . 9 8� / 0 1 2 3 4 5 6 7 8�	V�	W�	X�	Y�	Z�	[�	\�	]�	^�	_� :� 0 0 >� . 0 B��	`���s�s�	c���r�s�	o���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   P S� Q�	a�)�   T S� U� U��	b�z�	b�z�)J�    \ _ � ] ^�	d�	e� ` a�	f�	l�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�    f j � g h i�	g�	h�	i� k lс	j�	k���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width� s  t�\NS.uuidbytesO�C
�A��Ns�w:B���� w  x�\NS.uuidbytesO�cb��K���t&Q�"���    { ~ �� }�y�	m�� ��{�	n�|^documentLength��    � � �� ����	p�� ��{�	n�|^documentLength�    � � 8� � � � � � � � � � ��	r�	s�	t�	u�	v�	w�	x�	y�	z�	{� �� 0 0 �* . 0 ���	|���s�s�	~�S�r�s�	����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�   � S� ��	}�)�   � S�oooo���������)�    � � � � ��	�	�� � ��	��	��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�    � � �� ǀy�	��	��p�|^documentLength�    � � � � � с	��	��	��� � Հ��	��	��|_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore� �  ��\NS.uuidbytesO)]�Gö��?o'\��� �  ��\NS.uuidbytesO�ב%8b@��,�-D������    � � �� 倆�	��	��p�|^documentLength�    � � 8� � � � � � � � � � ��	��	��	��	��	��	��	��	��	��	�� �� 0 0 �* . 0! ��	����s�s�	��S�r�s�	����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ! S�!�	��)�  ! S� . . . .�r�r�r�r�)�   !! �!!�	��	��!!�	��	��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   !#!& ��!%�y�	���_�{�	�|^documentLength�   !,!0 �!-!.!/�	��	��	��!1!2с	��	����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�!9 !:�\NS.uuidbytesO&�1j��B������C�b���!= !>�\NS.uuidbytesO�����L�B������   !A!D ��!C���	���_�{�	�|^documentLength�   !J!U 8�!K!L!M!N!O!P!Q!R!S!T�	��	��	��	��	��	��	��	��	��	��!V� 0 0!Z* . 0!^��	����s�s�	��S�r�s�	����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  !l S�!m�	��)�  !p S� . . . .�r�r�r�r�)�   !w!z �!x!y�	��	��!{!|�	��	��|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   !�!� �!�!�!��	��	��	���!�!����	��	��|_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore�!� !��\NS.uuidbytesO0핌H���Cg�:����!� !��\NS.uuidbytesO�0qԽ�J���F1�q~%���   !�!� ��!��y�	��	!���	��|^documentLength��   !�!� ��!����	��	!���	��|^documentLength�   !�!� 8�!�!�!�!�!�!�!�!�!�!��	��		Á	ā	Ł	Ɓ	ǁ	ȁ	Ɂ	ʪ!�� 0 0!��!� 0!���	ˀ��s�s�	́w�	؀s�	ـ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  !� S�!́	̀)�  !� S�A�A����6���6�)�   !�!� �!�!؁	΁	Ϣ!�!ہ	Ё	Ҁ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   !�!� ��!�y�	Ѣ�
l�{��|^documentLength�   !�!� �!�!�!�	Ӂ	ԁ	գ!�!�с	ց	׀��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�!� !��\NS.uuidbytesO�_��aN��`�5�ʀ��!� !��\NS.uuidbytesO�oDB�.�h)6'j��O�   !�" ��"���	ڢ�
l�{��|^documentLength�   "" 8�"	"
""""""""�	܁	݁	ށ	߁	��	�	�	�	�	�"� 0 0"� . 0"��	怈�s�s�	���r�s�	􀈀@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  "* S�"+�	�)�  ". S������5���5�)�   "5"8 �"6"7�	�	�"9":�	�	�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   "?"C �"@"A"B�	�	�	�"D"Eс	�	����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�"L "M�\NS.uuidbytesO�z� t@O�����G�2���"P "Q�\NS.uuidbytesON4�=C�IP��Vw�\촀��   "T"W ��"V�y�	��"Y�{�	�|^documentLength
�   "^"a ��"`���	���"Y�{�	�|^documentLength�   "g"r 8�"h"i"j"k"l"m"n"o"p"q�	��	��	��	��	��	��	��	��	��
 �"s� 0 0"w� . 0"{��
���s�s�
���r�s�
���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  "� S�"��
�)�  "� S�C�C���z��z�)�   "�"� �"�"��
�
�"�"��
�
�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   "�"� �"�"�"��
�
�
	�"�"�с

�
���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�"� "��\NS.uuidbytesO��M8��I�9S���%3���"� "��\NS.uuidbytesO��T�),A��$���k-l���   "�"� ��"��y�
��"��{�
�|^documentLengthj�   "�"� ��"����
��"��{�
�|^documentLength�   "�"� 8�"�"�"�"�"�"�"�"�"�"Ё
�
�
�
�
�
�
�
�
�
�"�� 0 0"�* . 0"���
���s�s�
�S�r�s�
*���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  "� S�"�
�)�  "� S� . ."� .�r�r�
�r�)3�   "�"� �"�"��
 �
!�"�"��
"�
(�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   "�# �"�# #�
#�
$�
%�##с
&�
'���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�# #�\NS.uuidbytesO��	⡑Gb���v�W,���# #�\NS.uuidbytesO��{���K���Wtw+퀂�   ## ��#�y�
)����{�L�|^documentLength�   ## ��#���
+����{�L�|^documentLength�   #%#0 8�#&#'#(#)#*#+#,#-#.#/�
-�
.�
/�
0�
1�
2�
3�
4�
5�
6�#1� 0 0#5�#7 0#9��
7���s�s�
:�_�
F�s�
G���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  #G S�#H�
8�)�  #K S�#Lq#Lq�
9���
9���)�   #S#V �#T#U�
;�
<�#W#X�
=�
C�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   #]#a �#^#_#`�
>�
?�
@�#b#cс
A�
B���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�#j #k�\NS.uuidbytesOe0w�'@g�؈'�&����#n #o�\NS.uuidbytesO�hZ�l`F`�C��l����   #r#u ��#t�y�
D��#w�{�
E�|^documentLength ]�   #}#� ��#���
H��#w�{�
E�|^documentLength�   #�#� 8�#�#�#�#�#�#�#�#�#�#��
J�
K�
L�
M�
N�
O�
P�
Q�
R�
S�#�� 0 0#�#� . 0#���
T���s�s�
V�
a�r�s�
b���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  #� S�#��
U�)�  #� S��ށj���j���)�   #�#� �#�#��
W�
X�#�#��
Y�
[�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   #�#� ��#��y�
Z����{�S�|^documentLength�   #�#� �#�#�#Ɂ
\�
]�
^�#�#�с
_�
`���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�#� #��\NS.uuidbytesOL���_�D��\��������#� #��\NS.uuidbytesO������L`��5v.倂#��      �   #�#� ��#ހ��
c����{�S�|^documentLength�   #�#� 8�#�#�#�#�#�#�#�#�#�#�
e�
f�
g�
h�
i�
j�
k�
l�
m�
n�#�� 0 0#�#�#� 0#���
o���s�s�
q�
|�
}�s�
~���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  $ S�$�
p�)�  $ S�%%�`��`��)�   $$ �$$�
r�
s�$$�
t�
v�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   $$ �$��
u�y�!���	��{�|^documentLength�   $%$) �$&$'$(�
w�
x�
y�$*$+с
z�
{���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�$2 $3�\NS.uuidbytesO��y#�9FD�j�xYU&Ȁ��$6 $7�\NS.uuidbytesO*ykW��Bm��L]�	���#@0�     l�   $<$? ��$>���
��!��{�	��|^documentLength�   $E$P 8�$F$G$H$I$J$K$L$M$N$O�
��
��
��
��
��
��
��
��
��
��$Q� 0 0$U� . 0$Y��
����s�s�
����r�s�
����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  $g S�$h�
��)�  $k S� . . . .�r�r�r�r�)�   $r$u �$s$t�
��
��$v$w�
��
��|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   $|$� �$}$~$�
��
��
��$�$�с
��
����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�$� $��\NS.uuidbytesO�z��Q�H뺆��8�DL���$� $��\NS.uuidbytesO��tEI�g�'"�dĀ��   $�$� �$���
��y�c��>�{�|^documentLength�   $�$� ��$����
���c�{�>�|^documentLength�   $�$� 8�$�$�$�$�$�$�$�$�$�$��
��
��
��
��
��
��
��
��
��
��$�� 0 0$�* . 0$���
����s�s�
��S�r�s�
����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  $� S�$Ɓ
��)�  $� S� . . . .�r�r�r�r�)�   $�$� �$�$ҁ
��
��$�$Ձ
��
��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   $�$� �$���
��y����<�{�|^documentLength�   $�$� �$�$�$�
��
��
��$�$�с
��
����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�$� $��\NS.uuidbytesOS`��GN�`;����3���$� $��\NS.uuidbytesO��=m�C��;���ހ��   $�$� ��$����
�����{�<�|^documentLength�   %% 8�%%%%%%%%	%
%�
��
��
��
��
��
��
��
��
��
��%� 0 0%* . 0%��
����s�s�
��S�r�s�
̀��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  %# S�%$�
��)�  %' S� . . . .�r�r�r�r�)�   %.%1 �%/%0�

â%2%3�
ā
ʀ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   %8%< �%9%:%;�
Ł
Ɓ
ǣ%=%>с
ȁ
ɀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�%E %F�\NS.uuidbytesO��o�>O!�(v�VVB���%I %J�\NS.uuidbytesO8��fx�@&�2G�1�����   %M%P �%N��
ˀy����<�{�|^documentLength�   %V%Y ��%X���
͢���{�<�|^documentLength�   %_%j 8�%`%a%b%c%d%e%f%g%h%i�
ρ
Ё
с
ҁ
Ӂ
ԁ
Ձ
ց
ׁ
ت%k� 0 0%o� . 0%s��
ـ��s�s�
ہ��r�s�
怈�@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  %� S�%��
ڀ)�  %� S� . . . .�r�r�r�r�)�   %�%� �%�%��
܁
ݢ%�%��
ށ
�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   %�%� �%�%�%��
߁
��
�%�%�с
�
〄�|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�%� %��\NS.uuidbytesO�[��ڡKO�/�~�C���%� %��\NS.uuidbytesO�%��ȍ@����<*"K3���   %�%� ��%��y�
����{�߀|^documentLength�   %�%� ��%����
����{�߀|^documentLength�   %�%� 8�%�%�%�%�%�%�%�%�%�%ǁ
�
�
�
�
�
�
�
��
�
�%�� 0 0%�%�� 0%���
󀈀s�s�
���+�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  %� S�%��
�)�  %� S�%�%��
��0�
��0�)'�   %�%� �%�%�
��
��%�%��
��
��|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   %�%� ��%��y�
���%��{�
��|^documentLength��   %�& �& &&�
��
��
��&&с ����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�& &�\NS.uuidbytesO�����I�GΛ�o�����& &�\NS.uuidbytesO��R�+@;���j��O���#@       �   && ��&�����%��{�
��|^documentLength�   &&) 8�&& &!&"&#&$&%&&&'&(����	�
������&*� 0 0&.�
� 0&2�����s�s��s�>�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  &@ S�&A��)�  &D S� . . . .�r�r�r�r�)�   &K&N �&L&M���&O&P���|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   &U&Y �&V&W&X����&Z&[с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�&b &c�\NS.uuidbytesOR"u�u~LK����_�c���&f &g�\NS.uuidbytesO��W�#D�e�X��􁀂�   &j&m �&k���y�W����{�|^documentLength�   &s&v ��&u�����W�{���|^documentLength�   &|&� 8�&}&~&&�&�&�&�&�&�&�� �!�"�#�$�%�&�'�(�)�&�� 0 0&��&� 0&���*���s�s�-�_�9�s�:���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  &� S�&��+�)�  &� S��[&� .�T���,�r�)E�   &�&� �&�&��.�/�&�&��0�3�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   &�&� ��&��y�1��&��{�2�|^documentLength��   &�&� �&�&�&��4�5�6�&�&�с7�8���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�&� &��\NS.uuidbytesO��zr�F�Q_�q8���&� &��\NS.uuidbytesOգ��WBFx�	1lH�����.�   &�&� ��&ր��;��&��{�2�|^documentLength�   &�&� 8�&�&�&�&�&�&�&�&�&�&�=�>�?�@�A�B�C�D�E�F�&�� 0 0&�* . 0&���G���s�s�I�S�r�s�T���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  &� S�' �H�)�  ' S� . . . .�r�r�r�r�)�   '
' �''�J�K�''�L�R�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   '' �'''�M�N�O�''сP�Q���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�'! '"�\NS.uuidbytesO����WRGc��[_`�"����'% '&�\NS.uuidbytesO[�M̈́�}ɓ
ր��   ')', �'*��S�y�F��Ā{�|^documentLength�   '2'5 ��'4���U��F�{�Ā|^documentLength�   ';'F 8�'<'='>'?'@'A'B'C'D'E�W�X�Y�Z�[�\�]�^�_�`�'G� 0 0'K� 0'O��a���s�s�e��R�s�q���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  '] S�'^�b�)�  'a S�'b'c'b'c�c�d�c�d�)[A�   'j'm �'k'l�f�g�'n'o�h�n�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   't'x �'u'v'w�i�j�k�'y'zсl�m���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�'� '��\NS.uuidbytesO0'\&3FC��۪��ր��'� '��\NS.uuidbytesO����RBL�Y%��܀��   '�'� ��'��y�o��'��{�p�|^documentLength��   '�'� ��'����r��'��{�p�|^documentLength�   '�'� 8�'�'�'�'�'�'�'�'�'�'��t�u�v�w�x�y�z�{�|�}�'�� 0 0'�%�'� 0'���~���s�s������s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  '� S�'���)�  '� S�'�C'�C�������)��   '�'� �'�'́����'�'ρ����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   '�'� �'�'�'ׁ������'�'�с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�'� '��\NS.uuidbytesO��|��zBn�����er���'� '��\NS.uuidbytesO����E���6�nªU���   '�'� ��'�y����'�{���|^documentLength!���   '�'� ��'�������'�{���|^documentLength�   '�( 8�'�'�( (((((((���������������������(	� 0 0(� . 0(������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ( S�( ���)�  (# S��[� .�����5�r�)�   (*(- �(+(,�����(.(/�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   (4(8 �(5(6(7�������(9(:с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�(A (B�\NS.uuidbytesO�F�p��H��>���ŀ��(E (F�\NS.uuidbytesO%R�2�NiXF�~l���   (I(L ��(K�y����(N�{���|^documentLength��   (S(V ��(U������(N�{���|^documentLength�   (\(g 8�(](^(_(`(a(b(c(d(e(f���������������������(h� 0 0(l� . 0(p������s�s�����r�s�À��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  (~ S�(���)�  (� S� . . . .�r�r�r�r�)�   (�(� �(�(������(�(������|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   (�(� �(�����y�r����{�|^documentLength�   (�(� �(�(�(��������(�(�с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�(� (��\NS.uuidbytesOP �0�B��Ok��#���(� (��\NS.uuidbytesO�����I
��Q�G�>����   (�(� ��(����Ģ�r�{���|^documentLength�   (�(� 8�(�(�(�(�(�(�(�(�(�(āƁǁȁɁʁˁ́́΁Ϫ(�� 0 0(�(�� 0(���Ѐ��s�s�ҁ݁e�s�ހ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  (� S�(݁р)�  (� S� . . . .�r�r�r�r�)�   (�(� �(�(�ӁԢ(�(�Ձۀ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   (�(� �(�(�(�ցׁأ(�(�сفڀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�(� (��\NS.uuidbytesOF��b�dO`���fB΀��) )�\NS.uuidbytesO����?�M-�֒W��q���   ))	 ��)�y�ܢ�ǀ{�}�|^documentLength#@&      �   )) ��)���ߢ�ǀ{�}�|^documentLength�   ))$ 8�))))))) )!)")#�����������)%� 0 0)))*8 0)-��뀈�s�s����:�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ); S�)<��)�  )? S� . . . .�r�r�r�r�)�   )F)I �)G)H���)J)K����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   )P)S ��)R�y��	a��3�|^documentLength�   )Y)] �)Z)[)\������)_)`�������|_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController�)f )g�\NS.uuidbytesOֹ./#B��3�dG�f����)j )k�\NS.uuidbytesO��+���Mq�|C�_�2��#@#      �   )o)r ��)q�����	a��3�|^documentLength�   )x)z 8�)y���){���@� U  V W X)�� ���_*x-xcode-debug-memory-graph:///7fe9a34c3dd0�   )�)� 8�)�)�)���� ���**�{�S�S�@^disclosedNodes]cameraPanPosX]cameraPanPosY�   )�)� 8�)���)���@� U  V W X)�� ��_Zfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Assets.xcassets�   )�)� 8�)�)�)�)�)�)�)�)�����	�
����)�)�)�)�)�)�)�)����������@_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area�  )�)��)���_B./assetNoFavorite.imageset/[universal][][][1x][][][][][][][][][][]� \ ])�)�\NSMutableSet�)�)� `UNSSet�  )�)��)���_./assetNoFavorite.imageset_IBICCatalogOverviewController#@l�     �   )�)� 8���@�   )�)� 8�)́�)΁�@_expandedItemIDs�  )�)ԡ)Ӂ�Q.� \ ])�)ע)� `ZdetailArea�   )�)� 8���@�   )�)� 8�)�)�)�� �"�)�)�)�$�4�J�@� U  V W X)� ��_lfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog.xcodeproj� U  V W X)� ��!_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj� U  V W X)� ��#_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog.xcodeproj�   )�)� 8�)�)�)��%�&�'�)�)�)��(�2�3�@_,Xcode3ProjectEditorSelectedDocumentLocations_,Xcode3ProjectEditorPreviousTargetEditorClass_&Xcode3ProjectEditor_Xcode3TargetEditor�  * S�*�)�)�* � � *	*
**Yselection�,�+�*�1_lfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog.xcodeproj#A��2m0<��   ** 8�**�-�.�**�/�0�@VEditorVTarget_Xcode3TargetEditor[GameCatalog� \ ]**_Xcode3ProjectDocumentLocation�** `_Xcode3ProjectDocumentLocation_DVTDocumentLocation_Xcode3TargetEditor�   *"*# 8���@�   *&*. 8�*'*(*)***+*,*-�5�6�7�8�9�:�;�*/*0*1*2*3*4*5�<�=�>�?�@�A�I�@_-Xcode3ProjectEditorPreviousProjectEditorClass_+Xcode3ProjectEditor_Xcode3ProjectInfoEditor_,Xcode3ProjectEditorPreviousTargetEditorClass_-Xcode3ProjectEditor_Xcode3BuildSettingsEditor_(Xcode3ProjectEditor_Xcode3PackagesEditor_,Xcode3ProjectEditorSelectedDocumentLocations_&Xcode3ProjectEditor_Xcode3TargetEditor_Xcode3PackagesEditor�   *@*A 8���@_Xcode3TargetEditor�   *E*F 8���@�   *I*J 8���@�  *M S�*N�B�)�* � � *Q*R*S*�E�D�C�1_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj#A��wU�<>�   *X*[ 8�*Y*�F�-�*\*]�G�H�@WProject[TheMealsApp_Xcode3PackagesEditor�   *c*d 8���@�   *g*l 8�*h*i*j*k�K�L�M�N�*m*n*o*p�O�P�Q�j�@_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_-Xcode3ProjectEditor_Xcode3BuildSettingsEditor_Xcode3BuildSettingsEditor_Xcode3BuildSettingsEditor�  *y S�*z�R�)�* � � *}*~**�U�T�S�1_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog.xcodeproj#A��|'^#:�   *�*� 8�*Y**��F�-�V�*�*�*��W�X�Y�@_"Xcode3BuildSettingsEditorLocations[GameCatalog_Xcode3BuildSettingsEditor�  *� S�*��Z�)�   *�*� 8�*�*�*�*�*�*��[�\�]�^�_�`��*� . .*� .�z�a�r�r�e�r�@_"Xcode3BuildPropertyNameDisplayMode_Selected Build Properties_$Xcode3BuildSettingsEditorDisplayMode_#Xcode3BuildPropertyValueDisplayMode_#Collapsed Build Property Categories_Xcode3BuildSettingsEditorMode�  *�*��*��b�d� *�*�*�YNS.string�c_>Apple Clang - Code Generation||CLANG_OPTIMIZATION_PROFILE_FILE� \ ]*�*�_NSMutableString�*�*� `XNSString� \ ]*�*�^NSMutableArray�*� � `�  *�*��*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�*�+ +++++++++	+
++++++++++++++++++++++ +!+"+#+$+%+&+'+(+)+*+++,+-+.+/+0+1+2+3+4+5+6+7+8+9+:+;+<+=+>+?+@+A+B+C+D+E+F+G+H+I+J+K+L+M+N+O+P+Q+R+S+T+U+V+W+X+Y+Z+[+\+]+^+_+`+a+b+c+d+e+f+g+h+i+j+k+l+m+n+o+p+q+r+s+t+u+v+w+x+y+z+{+|+}+~++�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�+�, ,,,,,,,,,	,
,,,,,,,,,,,,,,,,,,,,,, ,!,",#,$,%,&,',(,),*,+,,,-,.,/,0,1,2,3,4,5,6,7,8,9,:,;,<,=,>,?,@,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z,[,\,],^,_,`,a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,{,|,},~,,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,�,��f�g�h�i�j�k�l�m�n�o�p�q�r�s�t�u�v�w�x�y�z�{�|�}�~��������������������������������������������������������������������������������������������������������������������������������������ÁāŁƁǁȁɁʁˁ́́΁ρЁсҁӁԁՁցׁ؁فځہ܁݁ށ߁��������������������������������������������� ���������	�
���������������������� �!�"�#�$�%�&�'�(�)�*�+�,�-�.�/�0�1�2�3�4�5�6�7�8�9�:�;�<�=�>�?�@�A�B�C�D�E�F�G�H�I�J�K�L�M�N�O�P�Q�R�S�T�U�V�W�X�Y�Z�[�\�]�^�_�`�a�b�c�d�e�f�g�h�i�j�k�l�m�n�o�p�q�r�s�t�u�v�w�x�y�z�{�|�}�~��������������������������������������������������������������������������������������������������������������������������������������ÁāŁƁǁȁɁʁˁ́́΁ρЁсҁӁԁՁցׁ؁فځہ܁݁ށ߁��������������������������������������������� ���������	�
���������������������� �!�"�#�$�%�&�'�(�)�*�+�,�-�.�/�0�1�2�3�4�5�6�7�8�9�:�;�<�=�>�?�@�A�B�C�D�E�F�G�H�I�J�K�L�M�N�O�P�Q�R�S�T�U�V�W�X�Y�Z�[�\�]�^�_�`�a�b�c�d�e�f�g�h�i�d� *�*�,c_Architectures||ADDITIONAL_SDKS� *�*�,Łc_Architectures||ARCHS� *�*�,ȁc_Architectures||SDKROOT� *�*�,ˁc_Architectures||EXCLUDED_ARCHS� *�*�,΁c_#Architectures||SUPPORTED_PLATFORMS� *�*�,сc_'Assets||ASSET_PACK_MANIFEST_URL_PREFIX� *�*�,ԁc_,Assets||EMBED_ASSET_PACKS_IN_PRODUCT_BUNDLE� *�*�,ׁc_#Assets||ENABLE_ON_DEMAND_RESOURCES� *�*�,ځc_1Assets||ON_DEMAND_RESOURCES_INITIAL_INSTALL_TAGS� *�*�,݁c_+Assets||ON_DEMAND_RESOURCES_PREFETCH_ORDER� *�*�,��c_Build Locations||SYMROOT� *�*�,�c_Build Locations||OBJROOT� *�*�,�c_%Build Locations||SHARED_PRECOMPS_DIR� *�*�,�c_4Build Options||ALLOW_TARGET_PLATFORM_SPECIALIZATION� *�*�,�c_5Build Options||ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES� *�*�,�c_.Build Options||BUILD_LIBRARY_FOR_DISTRIBUTION� *�*�,�c_Build Options||BUILD_VARIANTS� *�*�,��c_Build Options||GCC_VERSION� *�*�,��c_)Build Options||DEBUG_INFORMATION_VERSION� *�*�,��c_Build Options||EAGER_LINKING� *�*�,��c_$Build Options||ENABLE_CODE_COVERAGE� *�*�-�c_"Build Options||ENABLE_DEBUG_DYLIB� *�*�-�c_+Build Options||COMPILER_INDEX_STORE_ENABLE� *�*�-�c_+Build Options||ENABLE_TESTING_SEARCH_PATHS� *�*�-
�c_*Build Options||EXCLUDED_SOURCE_FILE_NAMES� *�*�-�c_'Build Options||GENERATE_PROFILING_CODE� *�*�-�c_*Build Options||IMPLICIT_DEPENDENCY_DOMAIN� *�*�-�c_*Build Options||INCLUDED_SOURCE_FILE_NAMES� *�*�-�c_@Build Options||PRECOMPS_INCLUDE_HEADERS_FROM_BUILT_PRODUCTS_DIR� *�*�-�c_.Build Options||APPLICATION_EXTENSION_API_ONLY� *�*�-�c_(Build Options||FUSE_BUILD_SCRIPT_PHASES� *�*�-�c_2Build Options||SCAN_ALL_SOURCE_FILES_FOR_INCLUDES� *�*�-"�c_<Build Options||TREAT_MISSING_SCRIPT_PHASE_OUTPUTS_AS_ERRORS� *�*�-%�c_-Build Options||ENABLE_USER_SCRIPT_SANDBOXING� *�*�-(�c_Deployment||STRIPFLAGS� *�*�-+�c_Deployment||ALTERNATE_GROUP� *�*�-.�c_Deployment||ALTERNATE_OWNER� *�*�-1�c_Deployment||ALTERNATE_MODE� *�*�-4�c_(Deployment||ALTERNATE_PERMISSIONS_FILES� *�*�-7�c_Deployment||MARKETPLACES� *�*�-:�c_)Deployment||ALTERNATIVE_DISTRIBUTION_WEB� *�*�-=�c_ Deployment||DEPLOYMENT_LOCATION� *�*�-@�c_&Deployment||DEPLOYMENT_POSTPROCESSING� *�*�-C�c_$Deployment||DEVELOPMENT_ASSET_PATHS� *�*�-F�c_(Deployment||DRIVERKIT_DEPLOYMENT_TARGET� *�*�-I�c_Deployment||INSTALL_GROUP� *�*�-L�c_Deployment||INSTALL_OWNER� *�*�-O�c_Deployment||INSTALL_MODE_FLAG� *�*�-R�c_Deployment||DSTROOT� *�*�-U�c_Deployment||INSTALL_PATH� *�*�-X�c_-Deployment||RESOURCES_TARGETED_DEVICE_FAMILY� *�*�-[�c_Deployment||SKIP_INSTALL� *�*�-^�c_Deployment||COPY_PHASE_STRIP� *�*�-a�c_$Deployment||STRIP_INSTALLED_PRODUCT� *�*�-d�c_Deployment||STRIP_STYLE� *�*�-g�c_ Deployment||STRIP_SWIFT_SYMBOLS� *�*�-j�c_#Deployment||TARGETED_DEVICE_FAMILY� *�*�-m�c_'Deployment||IPHONEOS_DEPLOYMENT_TARGET� *�*�-p�c_%Deployment||MACOSX_DEPLOYMENT_TARGET� *�*�-s�c_#Deployment||TVOS_DEPLOYMENT_TARGET� *�*�-v�c_#Deployment||XROS_DEPLOYMENT_TARGET� *�*�-y�c_&Deployment||WATCHOS_DEPLOYMENT_TARGET� *�*�-|�c_"Headers||COPY_HEADERS_RUN_UNIFDEF� *�*�-�c_$Headers||COPY_HEADERS_UNIFDEF_FLAGS� *�*�-��c_CInfo.plist Values||INFOPLIST_KEY_WKRunsIndependentlyOfCompanionApp� *�*�-��c_-Info.plist Values||INFOPLIST_KEY_WKWatchOnly� *�*�-��c_6Info.plist Values||INFOPLIST_KEY_NSMainStoryboardFile� *�*�-��c_;Info.plist Values||INFOPLIST_KEY_LSApplicationCategoryType� *�*�-��c_GInfo.plist Values||INFOPLIST_KEY_UIApplicationSceneManifest_Generation� *�*�-��c_-Info.plist Values||INFOPLIST_KEY_LSUIElement� *�*�-��c_2Info.plist Values||INFOPLIST_KEY_LSBackgroundOnly� *�*�-��c_5Info.plist Values||INFOPLIST_KEY_CFBundleDisplayName� *�*�-��c_?Info.plist Values||INFOPLIST_KEY_CLKComplicationPrincipalClass� *�*�-��c_:Info.plist Values||INFOPLIST_KEY_NSHumanReadableCopyright� *�*�-��c_;Info.plist Values||INFOPLIST_KEY_UILaunchScreen_Generation� *�*�-��c_8Info.plist Values||INFOPLIST_KEY_UILaunchStoryboardName� *�*�-��c_/Info.plist Values||INFOPLIST_KEY_NSMainNibFile� *�*�-��c_5Info.plist Values||INFOPLIST_KEY_MetalCaptureEnabled� *�*�-��c_2Info.plist Values||INFOPLIST_KEY_NSPrincipalClass� *�*�-��c_FInfo.plist Values||INFOPLIST_KEY_NSFileProviderDomainUsageDescription� *�*�-��c_?Info.plist Values||INFOPLIST_KEY_NSAppleEventsUsageDescription� *�*�-��c_CInfo.plist Values||INFOPLIST_KEY_NSBluetoothAlwaysUsageDescription� *�*�-��c_GInfo.plist Values||INFOPLIST_KEY_NSBluetoothPeripheralUsageDescription� *�*�-��c_GInfo.plist Values||INFOPLIST_KEY_NSBluetoothWhileInUseUsageDescription� *�*�-��c_=Info.plist Values||INFOPLIST_KEY_NSCalendarsUsageDescription� *�*�-��c_:Info.plist Values||INFOPLIST_KEY_NSCameraUsageDescription� *�*�-āc_<Info.plist Values||INFOPLIST_KEY_NSContactsUsageDescription� *�*�-ǁc_AInfo.plist Values||INFOPLIST_KEY_NSDesktopFolderUsageDescription� *�*�-ʁc_CInfo.plist Values||INFOPLIST_KEY_NSDocumentsFolderUsageDescription� *�*�-́c_CInfo.plist Values||INFOPLIST_KEY_NSDownloadsFolderUsageDescription� *�*�-Ёc_:Info.plist Values||INFOPLIST_KEY_OSBundleUsageDescription� *�*�-Ӂc_:Info.plist Values||INFOPLIST_KEY_NSFaceIDUsageDescription� *�*�-ցc_AInfo.plist Values||INFOPLIST_KEY_NSFallDetectionUsageDescription� *�*�-فc_HInfo.plist Values||INFOPLIST_KEY_NSFileProviderPresenceUsageDescription� *�*�-܁c_?Info.plist Values||INFOPLIST_KEY_NSFocusStatusUsageDescription� *�*�-߁c_@Info.plist Values||INFOPLIST_KEY_NSGKFriendListUsageDescription� *�*�-�c_TInfo.plist Values||INFOPLIST_KEY_NSHealthClinicalHealthRecordsShareUsageDescription� *�*�-�c_?Info.plist Values||INFOPLIST_KEY_NSHealthShareUsageDescription� *�*�-�c_@Info.plist Values||INFOPLIST_KEY_NSHealthUpdateUsageDescription� *�*�-�c_;Info.plist Values||INFOPLIST_KEY_NSHomeKitUsageDescription� *�*�-�c_<Info.plist Values||INFOPLIST_KEY_NSIdentityUsageDescription� *�*�-�c_@Info.plist Values||INFOPLIST_KEY_NSLocalNetworkUsageDescription� *�*�-�c_BInfo.plist Values||INFOPLIST_KEY_NSLocationAlwaysUsageDescription� *�*�-��c_NInfo.plist Values||INFOPLIST_KEY_NSLocationAlwaysAndWhenInUseUsageDescription� *�*�-��c_OInfo.plist Values||INFOPLIST_KEY_NSLocationTemporaryUsageDescriptionDictionary� *�*�-��c_<Info.plist Values||INFOPLIST_KEY_NSLocationUsageDescription� *�*�. �c_EInfo.plist Values||INFOPLIST_KEY_NSLocationWhenInUseUsageDescription� *�*�.�c_>Info.plist Values||INFOPLIST_KEY_NSAppleMusicUsageDescription� *�*�.�c_>Info.plist Values||INFOPLIST_KEY_NSMicrophoneUsageDescription� *�*�.	�c_:Info.plist Values||INFOPLIST_KEY_NSMotionUsageDescription� *�*�.�c_;Info.plist Values||INFOPLIST_KEY_NFCReaderUsageDescription� *�*�.�c_NInfo.plist Values||INFOPLIST_KEY_NSNearbyInteractionAllowOnceUsageDescription� *�*�.�c_EInfo.plist Values||INFOPLIST_KEY_NSNearbyInteractionUsageDescription� *�*�.�c_BInfo.plist Values||INFOPLIST_KEY_NSNetworkVolumesUsageDescription� *�*�.�c_CInfo.plist Values||INFOPLIST_KEY_NSPhotoLibraryAddUsageDescription� *�*�.�c_@Info.plist Values||INFOPLIST_KEY_NSPhotoLibraryUsageDescription� *�*�.�c_=Info.plist Values||INFOPLIST_KEY_NSRemindersUsageDescription� *�*�.!�c_DInfo.plist Values||INFOPLIST_KEY_NSRemovableVolumesUsageDescription� *�*�.$�c_=Info.plist Values||INFOPLIST_KEY_NSSensorKitPrivacyPolicyURL� *�*�.'�c_=Info.plist Values||INFOPLIST_KEY_NSSensorKitUsageDescription� *�*�.*�c_8Info.plist Values||INFOPLIST_KEY_NSSiriUsageDescription� *�*�.-�c_EInfo.plist Values||INFOPLIST_KEY_NSSpeechRecognitionUsageDescription� *�*�.0�c_HInfo.plist Values||INFOPLIST_KEY_NSSystemAdministrationUsageDescription� *�*�.3�c_CInfo.plist Values||INFOPLIST_KEY_NSSystemExtensionUsageDescription� *�*�.6�c_JInfo.plist Values||INFOPLIST_KEY_NSVideoSubscriberAccountUsageDescription� *�*�.9�c_@Info.plist Values||INFOPLIST_KEY_NSUserTrackingUsageDescription� *�*�.<�c_8Info.plist Values||INFOPLIST_KEY_NSVoIPUsageDescription� *�*�.?�c_>Info.plist Values||INFOPLIST_KEY_UIRequiredDeviceCapabilities� *�*�.B�c_6Info.plist Values||INFOPLIST_KEY_UIRequiresFullScreen� *�*�.E�c_3Info.plist Values||INFOPLIST_KEY_UIStatusBarHidden� *�*�.H�c_2Info.plist Values||INFOPLIST_KEY_UIStatusBarStyle� *�*�.K�c_7Info.plist Values||INFOPLIST_KEY_NSStickerSharingLevel� *�*�.N�c_BInfo.plist Values||INFOPLIST_KEY_UISupportedInterfaceOrientations� *�*�.Q�c_GInfo.plist Values||INFOPLIST_KEY_UISupportedInterfaceOrientations_iPad� *�*�.T�c_IInfo.plist Values||INFOPLIST_KEY_UISupportedInterfaceOrientations_iPhone� *�*�.W�c_;Info.plist Values||INFOPLIST_KEY_UISupportsDocumentBrowser� *�*�.Z�c_IInfo.plist Values||INFOPLIST_KEY_NSSupportsLiveActivitiesFrequentUpdates� *�*�.]�c_EInfo.plist Values||INFOPLIST_KEY_GCSupportsControllerUserInteraction� *�*�.`�c_4Info.plist Values||INFOPLIST_KEY_GCSupportsGameMode� *�*�.c�c_JInfo.plist Values||INFOPLIST_KEY_UIApplicationSupportsIndirectInputEvents� *�*�.f�c_LInfo.plist Values||INFOPLIST_KEY_WKSupportsLiveActivityLaunchAttributeTypes� *�*�.i�c_:Info.plist Values||INFOPLIST_KEY_NSSupportsLiveActivities� *�*�.l�c_CInfo.plist Values||INFOPLIST_KEY_LSSupportsOpeningDocumentsInPlace� *�*�.o�c_6Info.plist Values||INFOPLIST_KEY_UIMainStoryboardFile� *�*�.r�c_6Info.plist Values||INFOPLIST_KEY_UIUserInterfaceStyle� *�*�.u�c_@Info.plist Values||INFOPLIST_KEY_WKCompanionAppBundleIdentifier� *�*�.x�c_>Info.plist Values||INFOPLIST_KEY_WKExtensionDelegateClassName� *�*�.{�c_Kernel Module||MODULE_NAME� *�*�.~�c_Kernel Module||MODULE_START� *�*�.��c_Kernel Module||MODULE_STOP� *�*�.��c_Kernel Module||MODULE_VERSION� *�*�.��c_!Linking - General||BUNDLE_LOADER� *�*�.��c_"Linking - General||LD_CLIENT_NAME� *�*�.��c_/Linking - General||DYLIB_COMPATIBILITY_VERSION� *�*�.��c_)Linking - General||DYLIB_CURRENT_VERSION� *�*�.��c_'Linking - General||DEAD_CODE_STRIPPING� *�*�.��c_1Linking - General||LINKER_DISPLAYS_MANGLED_NAMES� *�*�.��c_.Linking - General||LD_DYLIB_ALLOWABLE_CLIENTS� *�*�.��c_)Linking - General||LD_DYLIB_INSTALL_NAME� *�*�.��c_+Linking - General||DYLIB_INSTALL_NAME_BASE� *�*�.��c_"Linking - General||LD_ENVIRONMENT� *�*�.��c_%Linking - General||LD_EXPORT_SYMBOLS� *�*�.��c_)Linking - General||EXPORTED_SYMBOLS_FILE� *�*�.��c_Linking - General||LD_NO_PIE� *�*�.��c_ Linking - General||INIT_ROUTINE� *�*�.��c_0Linking - General||LINK_WITH_STANDARD_LIBRARIES� *�*�.��c_Linking - General||MACH_O_TYPE� *�*�.��c_Linking - General||ORDER_FILE� *�*�.��c_&Linking - General||OTHER_LIBTOOLFLAGS� *�*�.��c_!Linking - General||OTHER_LDFLAGS� *�*�.��c_/Linking - General||GENERATE_MASTER_OBJECT_FILE� *�*�.Ác_ Linking - General||PRELINK_LIBS� *�*�.Ɓc_(Linking - General||KEEP_PRIVATE_EXTERNS� *�*�.Ɂc_ALinking - General||LD_QUOTE_LINKER_ARGUMENTS_FOR_COMPILER_DRIVER� *�*�.́c_.Linking - General||REEXPORTED_FRAMEWORK_NAMES� *�*�.ρc_,Linking - General||REEXPORTED_LIBRARY_NAMES� *�*�.ҁc_,Linking - General||REEXPORTED_LIBRARY_PATHS� *�*�.Ձc_+Linking - General||LD_RUNPATH_SEARCH_PATHS� *�*�.؁c_(Linking - General||SEPARATE_SYMBOL_EDIT� *�*�.ہc_!Linking - General||PRELINK_FLAGS� *�*�.ށc_#Linking - General||SECTORDER_FLAGS� *�*�.�c_+Linking - General||UNEXPORTED_SYMBOLS_FILE� *�*�.�c_(Linking - General||LD_GENERATE_MAP_FILE� *�*�.�c_1Linking - Mergeable Libraries||MERGEABLE_LIBRARY� *�*�.�c_2Linking - Mergeable Libraries||MERGED_BINARY_TYPE� *�*�.�c_0Linking - Warnings||LD_WARN_DUPLICATE_LIBRARIES� *�*�.��c_*Linking - Warnings||LD_WARN_UNUSED_DYLIBS� *�*�.�c_,Localization||LOCALIZATION_EXPORT_SUPPORTED� *�*�.��c_3Localization||LOCALIZATION_PREFERS_STRING_CATALOGS� *�*�.��c_+Localization||LOCALIZED_STRING_MACRO_NAMES� *�*�.��c_/Localization||LOCALIZED_STRING_SWIFTUI_SUPPORT� *�*�.��c_%Localization||SWIFT_EMIT_LOC_STRINGS� *�*�/�c_)Packaging||STRINGS_FILE_INFOPLIST_RENAME� *�*�/�c_%Packaging||APPLY_RULES_IN_COPY_FILES� *�*�/�c_.Packaging||CREATE_INFOPLIST_SECTION_IN_BINARY� *�*�/�c_Packaging||DEFINES_MODULE� *�*�/�c_(Packaging||DONT_GENERATE_INFOPLIST_FILE� *�*�/�c_ Packaging||EXECUTABLE_EXTENSION� *�*�/�c_Packaging||EXECUTABLE_PREFIX� *�*�/�c_+Packaging||INFOPLIST_EXPAND_BUILD_SETTINGS� *�*�/�c_!Packaging||GENERATE_PKGINFO_FILE� *�*�/�c_Packaging||FRAMEWORK_VERSION� *�*�/ �c_#Packaging||GENERATE_INFOPLIST_FILE� *�*�/#�c_Packaging||INFOPLIST_FILE� *�*�/&�c_.Packaging||INFOPLIST_OTHER_PREPROCESSOR_FLAGS� *�*�/)�c_#Packaging||INFOPLIST_OUTPUT_FORMAT� *�*�/,�c_.Packaging||INFOPLIST_PREPROCESSOR_DEFINITIONS� *�*�//�c_#Packaging||INFOPLIST_PREFIX_HEADER� *�*�/2�c_Packaging||MODULEMAP_FILE� *�*�/5�c_ Packaging||INFOPLIST_PREPROCESS� *�*�/8�c_&Packaging||COPYING_PRESERVES_HFS_DATA� *�*�/;�c_'Packaging||PRIVATE_HEADERS_FOLDER_PATH� *�*�/>�c_"Packaging||MODULEMAP_PRIVATE_FILE� *�*�/A�c_'Packaging||APPLY_RULES_IN_COPY_HEADERS� *�*�/D�c_%Packaging||PRODUCT_BUNDLE_IDENTIFIER� *�*�/G�c_Packaging||PRODUCT_MODULE_NAME� *�*�/J�c_Packaging||PRODUCT_NAME� *�*�/M�c_$Packaging||PLIST_FILE_OUTPUT_FORMAT� *�*�/P�c_&Packaging||PUBLIC_HEADERS_FOLDER_PATH� *�*�/S�c_(Packaging||STRINGS_FILE_OUTPUT_ENCODING� *�*�/V�c_Packaging||WRAPPER_EXTENSION� *�*�/Y�c_'Search Paths||ALWAYS_SEARCH_USER_PATHS� *�*�/\�c_%Search Paths||FRAMEWORK_SEARCH_PATHS� *�*�/_�c_"Search Paths||HEADER_SEARCH_PATHS� *�*�/b�c_#Search Paths||LIBRARY_SEARCH_PATHS� *�*�/e�c_Search Paths||REZ_SEARCH_PATHS� *�*�/h�c_<Search Paths||EXCLUDED_RECURSIVE_SEARCH_PATH_SUBDIRECTORIES� *�*�/k�c_<Search Paths||INCLUDED_RECURSIVE_SEARCH_PATH_SUBDIRECTORIES� *�*�/n�c_,Search Paths||SYSTEM_FRAMEWORK_SEARCH_PATHS� *�*�/q�c_)Search Paths||SYSTEM_HEADER_SEARCH_PATHS� *�*�/t�c_Search Paths||USE_HEADERMAP� *�*�/w�c_'Search Paths||USER_HEADER_SEARCH_PATHS� *�*�/z�c_ Signing||CODE_SIGN_ENTITLEMENTS� *�*�/}�c_Signing||CODE_SIGN_IDENTITY� *�*�/��c_,Signing||CODE_SIGN_INJECT_BASE_ENTITLEMENTS� *�*�/��c_Signing||CODE_SIGN_STYLE� *�*�/��c_Signing||DEVELOPMENT_TEAM� *�*�/��c_Signing||ENABLE_APP_SANDBOX� *�*�/��c_!Signing||ENABLE_HARDENED_RUNTIME� *�*�/��c_$Signing||ENABLE_USER_SELECTED_FILES� *�*�/��c_"Signing||LAUNCH_CONSTRAINT_PARENT� *�*�/��c_ Signing||LAUNCH_CONSTRAINT_SELF� *�*�/��c_'Signing||LAUNCH_CONSTRAINT_RESPONSIBLE� *�*�/��c_!Signing||LIBRARY_LOAD_CONSTRAINT� *�*�/��c_Signing||OTHER_CODE_SIGN_FLAGS� *�*�/��c_(Signing||PROVISIONING_PROFILE_SPECIFIER� *�*�/��c_-Siri||APP_SHORTCUTS_ENABLE_FLEXIBLE_MATCHING� *�*�/��c_Testing||TEST_HOST� *�*�/��c_2Testing||TREAT_MISSING_BASELINES_AS_TEST_FAILURES� *�*�/��c_7Text-Based API||GENERATE_INTERMEDIATE_TEXT_BASED_STUBS� *�*�/��c_,Text-Based API||TAPI_ENABLE_PROJECT_HEADERS� *�*�/��c_*Text-Based API||GENERATE_TEXT_BASED_STUBS� *�*�/��c_*Text-Based API||INLINE_PRIVATE_FRAMEWORKS� *�*�/��c_-Text-Based API||TAPI_EXCLUDE_PRIVATE_HEADERS� *�*�/��c_-Text-Based API||TAPI_EXCLUDE_PROJECT_HEADERS� *�*�/��c_,Text-Based API||TAPI_EXCLUDE_PUBLIC_HEADERS� *�*�/c_+Text-Based API||TAPI_EXTRA_PRIVATE_HEADERS� *�*�/Łc_+Text-Based API||TAPI_EXTRA_PROJECT_HEADERS� *�*�/ȁc_*Text-Based API||TAPI_EXTRA_PUBLIC_HEADERS� *�*�/ˁc_!Text-Based API||OTHER_TAPI_FLAGS� *�*�/΁c_(Text-Based API||SUPPORTS_TEXT_BASED_API� *�*�/сc_Text-Based API||TAPI_DEMANGLE� *�*�/ԁc_'Text-Based API||TAPI_LANGUAGE_STANDARD� *�*�/ׁc_Text-Based API||TAPI_LANGUAGE� *�*�/ځc_!Text-Based API||TAPI_VERIFY_MODE� *�*�/݁c_$Versioning||CURRENT_PROJECT_VERSION� *�*�/��c_Versioning||VERSION_INFO_FILE� *�*�/�c_%Versioning||VERSION_INFO_EXPORT_DECL� *�*�/�c_Versioning||MARKETING_VERSION� *�*�/�c_ Versioning||VERSION_INFO_PREFIX� *�*�/�c_ Versioning||VERSION_INFO_SUFFIX� *�*�/�c_Versioning||VERSIONING_SYSTEM� *�*�/�c_!Versioning||VERSION_INFO_BUILDER� *�*�/��c_KApple Clang - Address Sanitizer||CLANG_ADDRESS_SANITIZER_CONTAINER_OVERFLOW� *�*�/��c_<Apple Clang - Code Generation||CLANG_DEBUG_INFORMATION_LEVEL� *�*�/��c_<Apple Clang - Code Generation||CLANG_X86_VECTOR_INSTRUCTIONS� *�*�/��c_2Apple Clang - Code Generation||GCC_STRICT_ALIASING� *�*�0�c_=Apple Clang - Code Generation||GCC_GENERATE_DEBUGGING_SYMBOLS� *�*�0�c_?Apple Clang - Code Generation||GCC_GENERATE_TEST_COVERAGE_FILES� *�*�0�c_1Apple Clang - Code Generation||GCC_DYNAMIC_NO_PIC� *�*�0
�c_=Apple Clang - Code Generation||GCC_INLINES_ARE_PRIVATE_EXTERN� *�*�0�c_?Apple Clang - Code Generation||GCC_INSTRUMENT_PROGRAM_FLOW_ARCS� *�*�0�c_<Apple Clang - Code Generation||GCC_ENABLE_KERNEL_DEVELOPMENT� *�*�0�c_'Apple Clang - Code Generation||LLVM_LTO� *�*�0�c_0Apple Clang - Code Generation||GCC_REUSE_STRINGS� *�*�0�c_3Apple Clang - Code Generation||GCC_NO_COMMON_BLOCKS� *�*�0�c_,Apple Clang - Code Generation||GCC_FAST_MATH� *�*�0�c_5Apple Clang - Code Generation||GCC_THREADSAFE_STATICS� *�*�0"�c_9Apple Clang - Code Generation||GCC_SYMBOLS_PRIVATE_EXTERN� *�*�0%�c_/Apple Clang - Code Generation||GCC_UNROLL_LOOPS� *�*�0(�c_=Apple Clang - Code Generation||CLANG_USE_OPTIMIZATION_PROFILE� *�*�0+�c_1Apple Clang - Custom Compiler Flags||OTHER_CFLAGS� *�*�0.�c_9Apple Clang - Custom Compiler Flags||OTHER_CPLUSPLUSFLAGS� *�*�01�c_3Apple Clang - Custom Compiler Flags||WARNING_CFLAGS� *�*�04�c_1Apple Clang - Language||GCC_CHAR_IS_UNSIGNED_CHAR� *�*�07�c_.Apple Clang - Language||GCC_ENABLE_ASM_KEYWORD� *�*�0:�c_/Apple Clang - Language||GCC_C_LANGUAGE_STANDARD� *�*�0=�c_)Apple Clang - Language||GCC_CW_ASM_SYNTAX� *�*�0@�c_*Apple Clang - Language||GCC_INPUT_FILETYPE� *�*�0C�c_7Apple Clang - Language||GCC_LINK_WITH_DYNAMIC_LIBRARIES� *�*�0F�c_,Apple Clang - Language||GCC_ENABLE_TRIGRAPHS� *�*�0I�c_?Apple Clang - Language||GCC_ENABLE_FLOATING_POINT_LIBRARY_CALLS� *�*�0L�c_?Apple Clang - Language||GCC_INCREASE_PRECOMPILED_HEADER_SHARING� *�*�0O�c_4Apple Clang - Language||GCC_PRECOMPILE_PREFIX_HEADER� *�*�0R�c_)Apple Clang - Language||GCC_PREFIX_HEADER� *�*�0U�c_4Apple Clang - Language||GCC_ENABLE_BUILTIN_FUNCTIONS� *�*�0X�c_1Apple Clang - Language||GCC_ENABLE_PASCAL_STRINGS� *�*�0[�c_'Apple Clang - Language||GCC_SHORT_ENUMS� *�*�0^�c_/Apple Clang - Language||CLANG_USE_RESPONSE_FILE� *�*�0a�c_:Apple Clang - Language||GCC_USE_STANDARD_INCLUDE_SEARCHING� *�*�0d�c_9Apple Clang - Language - C++||CLANG_CXX_LANGUAGE_STANDARD� *�*�0g�c_AApple Clang - Language - C++||CLANG_ENABLE_CPP_STATIC_DESTRUCTORS� *�*�0j�c_7Apple Clang - Language - C++||GCC_ENABLE_CPP_EXCEPTIONS� *�*�0m�c_1Apple Clang - Language - C++||GCC_ENABLE_CPP_RTTI� *�*�0p�c_WApple Clang - Language - Modules||CLANG_ALLOW_NON_MODULAR_INCLUDES_IN_FRAMEWORK_MODULES� *�*�0s�c_GApple Clang - Language - Modules||CLANG_MODULES_DISABLE_PRIVATE_WARNING� *�*�0v�c_?Apple Clang - Language - Modules||CLANG_ENABLE_MODULE_DEBUGGING� *�*�0y�c_6Apple Clang - Language - Modules||CLANG_ENABLE_MODULES� *�*�0|�c_8Apple Clang - Language - Modules||CLANG_MODULES_AUTOLINK� *�*�0�c_FApple Clang - Language - Objective-C||CLANG_ENABLE_OBJC_ARC_EXCEPTIONS� *�*�0��c_@Apple Clang - Language - Objective-C||GCC_ENABLE_OBJC_EXCEPTIONS� *�*�0��c_=Apple Clang - Language - Objective-C||CLANG_LINK_OBJC_RUNTIME� *�*�0��c_;Apple Clang - Language - Objective-C||CLANG_ENABLE_OBJC_ARC� *�*�0��c_<Apple Clang - Language - Objective-C||CLANG_ENABLE_OBJC_WEAK� *�*�0��c_7Apple Clang - Preprocessing||ENABLE_STRICT_OBJC_MSGSEND� *�*�0��c_NApple Clang - Preprocessing||GCC_PREPROCESSOR_DEFINITIONS_NOT_USED_IN_PRECOMPS� *�*�0��c_VApple Clang - Undefined Behavior Sanitizer||CLANG_UNDEFINED_BEHAVIOR_SANITIZER_INTEGER� *�*�0��c_ZApple Clang - Undefined Behavior Sanitizer||CLANG_UNDEFINED_BEHAVIOR_SANITIZER_NULLABILITY� *�*�0��c_=Apple Clang - Warning Policies||GCC_WARN_INHIBIT_ALL_WARNINGS� *�*�0��c_1Apple Clang - Warning Policies||GCC_WARN_PEDANTIC� *�*�0��c_<Apple Clang - Warning Policies||GCC_TREAT_WARNINGS_AS_ERRORS� *�*�0��c_NApple Clang - Warnings - All languages||CLANG_WARN_BLOCK_CAPTURE_AUTORELEASING� *�*�0��c_HApple Clang - Warnings - All languages||GCC_WARN_CHECK_SWITCH_STATEMENTS� *�*�0��c_LApple Clang - Warnings - All languages||CLANG_WARN_COMPLETION_HANDLER_MISUSE� *�*�0��c_KApple Clang - Warnings - All languages||GCC_WARN_ABOUT_DEPRECATED_FUNCTIONS� *�*�0��c_IApple Clang - Warnings - All languages||CLANG_WARN_DOCUMENTATION_COMMENTS� *�*�0��c_=Apple Clang - Warnings - All languages||CLANG_WARN_EMPTY_BODY� *�*�0��c_IApple Clang - Warnings - All languages||GCC_WARN_FOUR_CHARACTER_CONSTANTS� *�*�0��c_7Apple Clang - Warnings - All languages||GCC_WARN_SHADOW� *�*�0��c_BApple Clang - Warnings - All languages||CLANG_WARN_BOOL_CONVERSION� *�*�0��c_FApple Clang - Warnings - All languages||CLANG_WARN_CONSTANT_CONVERSION� *�*�0��c_BApple Clang - Warnings - All languages||CLANG_WARN_ENUM_CONVERSION� *�*�0āc_GApple Clang - Warnings - All languages||CLANG_WARN_IMPLICIT_FALLTHROUGH� *�*�0ǁc_CApple Clang - Warnings - All languages||CLANG_WARN_FLOAT_CONVERSION� *�*�0ʁc_AApple Clang - Warnings - All languages||CLANG_WARN_INT_CONVERSION� *�*�0́c_NApple Clang - Warnings - All languages||CLANG_WARN_NON_LITERAL_NULL_CONVERSION� *�*�0Ёc_KApple Clang - Warnings - All languages||CLANG_WARN_IMPLICIT_SIGN_CONVERSION� *�*�0Ӂc_EApple Clang - Warnings - All languages||CLANG_WARN_INFINITE_RECURSION� *�*�0ցc_PApple Clang - Warnings - All languages||GCC_WARN_INITIALIZER_NOT_FULLY_BRACKETED� *�*�0فc_BApple Clang - Warnings - All languages||GCC_WARN_ABOUT_RETURN_TYPE� *�*�0܁c_DApple Clang - Warnings - All languages||GCC_WARN_MISSING_PARENTHESES� *�*�0߁c_QApple Clang - Warnings - All languages||GCC_WARN_ABOUT_MISSING_FIELD_INITIALIZERS� *�*�0�c_IApple Clang - Warnings - All languages||GCC_WARN_ABOUT_MISSING_PROTOTYPES� *�*�0�c_FApple Clang - Warnings - All languages||GCC_WARN_ABOUT_MISSING_NEWLINE� *�*�0�c_>Apple Clang - Warnings - All languages||CLANG_WARN_ASSIGN_ENUM� *�*�0�c_AApple Clang - Warnings - All languages||CLANG_WARN_PRIVATE_MODULE� *�*�0�c_IApple Clang - Warnings - All languages||GCC_WARN_ABOUT_POINTER_SIGNEDNESS� *�*�0�c_XApple Clang - Warnings - All languages||CLANG_WARN_FRAMEWORK_INCLUDE_PRIVATE_FROM_PUBLIC� *�*�0�c_UApple Clang - Warnings - All languages||CLANG_WARN_QUOTED_INCLUDE_IN_FRAMEWORK_HEADER� *�*�0��c_OApple Clang - Warnings - All languages||CLANG_WARN_SEMICOLON_BEFORE_METHOD_BODY� *�*�0��c_=Apple Clang - Warnings - All languages||GCC_WARN_SIGN_COMPARE� *�*�0��c_DApple Clang - Warnings - All languages||CLANG_WARN_STRICT_PROTOTYPES� *�*�1 �c_8Apple Clang - Warnings - All languages||CLANG_WARN_COMMA� *�*�1�c_QApple Clang - Warnings - All languages||CLANG_WARN_SUSPICIOUS_IMPLICIT_CONVERSION� *�*�1�c_>Apple Clang - Warnings - All languages||CLANG_WARN_PRAGMA_PACK� *�*�1	�c_^Apple Clang - Warnings - All languages||GCC_TREAT_INCOMPATIBLE_POINTER_TYPE_WARNINGS_AS_ERRORS� *�*�1�c_ZApple Clang - Warnings - All languages||GCC_TREAT_IMPLICIT_FUNCTION_DECLARATIONS_AS_ERRORS� *�*�1�c_JApple Clang - Warnings - All languages||GCC_WARN_TYPECHECK_CALLS_TO_PRINTF� *�*�1�c_IApple Clang - Warnings - All languages||CLANG_WARN_UNGUARDED_AVAILABILITY� *�*�1�c_DApple Clang - Warnings - All languages||GCC_WARN_UNINITIALIZED_AUTOS� *�*�1�c_@Apple Clang - Warnings - All languages||GCC_WARN_UNKNOWN_PRAGMAS� *�*�1�c_CApple Clang - Warnings - All languages||CLANG_WARN_UNREACHABLE_CODE� *�*�1�c_@Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_FUNCTION� *�*�1!�c_=Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_LABEL� *�*�1$�c_AApple Clang - Warnings - All languages||GCC_WARN_UNUSED_PARAMETER� *�*�1'�c_=Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_VALUE� *�*�1*�c_@Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_VARIABLE� *�*�1-�c_5Apple Clang - Warnings - C++||CLANG_WARN_VEXING_PARSE� *�*�10�c_@Apple Clang - Warnings - C++||CLANG_WARN_DELETE_NON_VIRTUAL_DTOR� *�*�13�c_?Apple Clang - Warnings - C++||CLANG_WARN__EXIT_TIME_DESTRUCTORS� *�*�16�c_=Apple Clang - Warnings - C++||GCC_WARN_NON_VIRTUAL_DESTRUCTOR� *�*�19�c_?Apple Clang - Warnings - C++||GCC_WARN_HIDDEN_VIRTUAL_FUNCTIONS� *�*�1<�c_<Apple Clang - Warnings - C++||CLANG_WARN_RANGE_LOOP_ANALYSIS� *�*�1?�c_8Apple Clang - Warnings - C++||CLANG_WARN_SUSPICIOUS_MOVE� *�*�1B�c_CApple Clang - Warnings - C++||GCC_WARN_ABOUT_INVALID_OFFSETOF_MACRO� *�*�1E�c_@Apple Clang - Warnings - C++||CLANG_WARN_ATOMIC_IMPLICIT_SEQ_CST� *�*�1H�c_9Apple Clang - Warnings - C++||CLANG_WARN_CXX0X_EXTENSIONS� *�*�1K�c_FApple Clang - Warnings - Objective-C||CLANG_WARN_DIRECT_OBJC_ISA_USAGE� *�*�1N�c_HApple Clang - Warnings - Objective-C||CLANG_WARN__DUPLICATE_METHOD_MATCH� *�*�1Q�c_PApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_IMPLICIT_ATOMIC_PROPERTIES� *�*�1T�c_HApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_LITERAL_CONVERSION� *�*�1W�c_PApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_MISSING_PROPERTY_SYNTHESIS� *�*�1Z�c_HApple Clang - Warnings - Objective-C||GCC_WARN_ALLOW_INCOMPLETE_PROTOCOL� *�*�1]�c_EApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_INTERFACE_IVARS� *�*�1`�c_AApple Clang - Warnings - Objective-C||CLANG_WARN_MISSING_NOESCAPE� *�*�1c�c_PApple Clang - Warnings - Objective-C||CLANG_WARN_DEPRECATED_OBJC_IMPLEMENTATIONS� *�*�1f�c_DApple Clang - Warnings - Objective-C||GCC_WARN_STRICT_SELECTOR_MATCH� *�*�1i�c_BApple Clang - Warnings - Objective-C||GCC_WARN_UNDECLARED_SELECTOR� *�*�1l�c_@Apple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_ROOT_CLASS� *�*�1o�c_UApple Clang - Warnings - Objective-C and ARC||CLANG_WARN_OBJC_EXPLICIT_OWNERSHIP_TYPE� *�*�1r�c_RApple Clang - Warnings - Objective-C and ARC||CLANG_WARN_OBJC_IMPLICIT_RETAIN_SELF� *�*�1u�c_RApple Clang - Warnings - Objective-C and ARC||CLANG_WARN_OBJC_REPEATED_USE_OF_WEAK� *�*�1x�c_PApple Clang - Warnings - Objective-C and ARC||CLANG_WARN__ARC_BRIDGE_CAST_NONARC� *�*�1{�c_=Apple Clang Module Verifier - Options||ENABLE_MODULE_VERIFIER� *�*�1~�c_BApple Clang Module Verifier - Options||OTHER_MODULE_VERIFIER_FLAGS� *�*�1��c_SApple Clang Module Verifier - Options||MODULE_VERIFIER_SUPPORTED_LANGUAGE_STANDARDS� *�*�1��c_JApple Clang Module Verifier - Options||MODULE_VERIFIER_SUPPORTED_LANGUAGES� *�*�1��c_OAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_ALTERNATE_APPICON_NAMES� *�*�1��c_HAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_LAUNCHIMAGE_NAME� *�*�1��c_:Asset Catalog Compiler - Options||ASSETCATALOG_OTHER_FLAGS� *�*�1��c_>Asset Catalog Compiler - Options||ENABLE_ONLY_ACTIVE_RESOURCES� *�*�1��c_<Asset Catalog Compiler - Options||ENABLE_INCREMENTAL_DISTILL� *�*�1��c_NAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GENERATE_ASSET_SYMBOLS� *�*�1��c_^Asset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GENERATE_SWIFT_ASSET_SYMBOL_EXTENSIONS� *�*�1��c_XAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GENERATE_ASSET_SYMBOL_FRAMEWORKS� *�*�1��c_PAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GLOBAL_ACCENT_COLOR_NAME� *�*�1��c_RAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_INCLUDE_ALL_APPICON_ASSETS� *�*�1��c_WAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_INCLUDE_INFOPLIST_LOCALIZATIONS� *�*�1��c_OAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_INCLUDE_STICKER_CONTENT� *�*�1��c_DAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_OPTIMIZATION� *�*�1��c_DAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_APPICON_NAME� *�*�1��c_6Asset Catalog Compiler - Options||ASSETCATALOG_NOTICES� *�*�1��c_7Asset Catalog Compiler - Options||ASSETCATALOG_WARNINGS� *�*�1��c_QAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_SKIP_APP_STORE_DEPLOYMENT� *�*�1��c_PAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_STANDALONE_ICON_BEHAVIOR� *�*�1��c_VAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_STICKER_PACK_IDENTIFIER_PREFIX� *�*�1��c_QAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_TARGET_STICKERS_ICON_ROLE� *�*�1Ác_TAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_WIDGET_BACKGROUND_COLOR_NAME� *�*�1Ɓc_MDesigned for iPhone & iPad - Deployment||SUPPORTS_XR_DESIGNED_FOR_IPHONE_IPAD� *�*�1Ɂc_NDesigned for iPhone & iPad - Deployment||SUPPORTS_MAC_DESIGNED_FOR_IPHONE_IPAD� *�*�1́c_<Documentation Compiler - Options||RUN_DOCUMENTATION_COMPILER� *�*�1ρc_9Documentation Compiler - Options||DOCC_ENABLE_CXX_SUPPORT� *�*�1ҁc_JDocumentation Compiler - Options||DOCC_EXTRACT_SWIFT_INFO_FOR_OBJC_SYMBOLS� *�*�1Ձc_JDocumentation Compiler - Options||DOCC_EXTRACT_OBJC_INFO_FOR_SWIFT_SYMBOLS� *�*�1؁c_8Documentation Compiler - Options||DOCC_HOSTING_BASE_PATH� *�*�1ہc_@Documentation Compiler - Options||DOCC_EXTRACT_EXTENSION_SYMBOLS� *�*�1ށc_2Documentation Compiler - Options||OTHER_DOCC_FLAGS� *�*�1�c_GMac Catalyst - Deployment||DERIVE_MACCATALYST_PRODUCT_BUNDLE_IDENTIFIER� *�*�1�c_/Mac Catalyst - Deployment||SUPPORTS_MACCATALYST� *�*�1�c_1OSACompile - Build Options||OTHER_OSACOMPILEFLAGS� *�*�1�c_3OSACompile - Build Options||OSACOMPILE_EXECUTE_ONLY� *�*�1�c_<Static Analysis - Analysis Policy||RUN_CLANG_STATIC_ANALYZER� *�*�1��c_OStatic Analysis - Analysis Policy||CLANG_STATIC_ANALYZER_MODE_ON_ANALYZE_ACTION� *�*�1�c_=Static Analysis - Analysis Policy||CLANG_STATIC_ANALYZER_MODE� *�*�1��c_AStatic Analysis - Generic Issues||CLANG_ANALYZER_NULL_DEREFERENCE� *�*�1��c_?Static Analysis - Generic Issues||CLANG_ANALYZER_DIVIDE_BY_ZERO� *�*�1��c_BStatic Analysis - Generic Issues||CLANG_ANALYZER_MEMORY_MANAGEMENT� *�*�1��c_CStatic Analysis - Generic Issues||CLANG_TIDY_BUGPRONE_INFINITE_LOOP� *�*�2�c_8Static Analysis - Generic Issues||CLANG_ANALYZER_NONNULL� *�*�2�c_HStatic Analysis - Generic Issues||CLANG_TIDY_BUGPRONE_ASSERT_SIDE_EFFECT� *�*�2�c_KStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_OSOBJECT_C_STYLE_CAST� *�*�2�c_DStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_OBJC_NSCFERROR� *�*�2�c_QStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_LOCALIZABILITY_NONLOCALIZED� *�*�2�c_RStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_LOCALIZABILITY_EMPTY_CONTEXT� *�*�2�c_FStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_OBJC_COLLECTIONS� *�*�2�c_9Static Analysis - Issues - Apple APIs||CLANG_ANALYZER_GCD� *�*�2�c_EStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_GCD_PERFORMANCE� *�*�2�c_NStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_NUMBER_OBJECT_CONVERSION� *�*�2 �c_JStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_LIBKERN_RETAIN_COUNT� *�*�2#�c_EStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_MIG_CONVENTIONS� *�*�2&�c_MStatic Analysis - Issues - C++||CLANG_TIDY_BUGPRONE_MOVE_FORWARDING_REFERENCE� *�*�2)�c_=Static Analysis - Issues - C++||CLANG_ANALYZER_USE_AFTER_MOVE� *�*�2,�c_BStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_ATSYNC� *�*�2/�c_CStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_DEALLOC� *�*�22�c_OStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_INCOMP_METHOD_TYPES� *�*�25�c_DStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_GENERICS� *�*�28�c_HStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_UNUSED_IVARS� *�*�2;�c_EStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_SELF_INIT� *�*�2>�c_HStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_RETAIN_COUNT� *�*�2A�c_MStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_FLOATLOOPCOUNTER� *�*�2D�c_IStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_KEYCHAIN_API� *�*�2G�c_XStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_UNCHECKEDRETURN� *�*�2J�c_SStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_GETPW_GETS� *�*�2M�c_PStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_MKSTEMP� *�*�2P�c_MStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_RAND� *�*�2S�c_OStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_STRCPY� *�*�2V�c_NStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_VFORK� *�*�2Y�c_JStatic Analysis - Issues - Unused Code||CLANG_ANALYZER_DEADCODE_DEADSTORES� *�*�2\�c_LStatic Analysis - Issues - Unused Code||CLANG_TIDY_MISC_REDUNDANT_EXPRESSION� *�*�2_�c_VStatic Analysis - Issues - Unused Code||CLANG_TIDY_BUGPRONE_REDUNDANT_BRANCH_CONDITION� *�*�2b�c_=Swift Compiler - Code Generation||SWIFT_DISABLE_SAFETY_CHECKS� *�*�2e�c_@Swift Compiler - Code Generation||SWIFT_ENFORCE_EXCLUSIVE_ACCESS� *�*�2h�c_0Swift Compiler - Custom Flags||OTHER_SWIFT_FLAGS� *�*�2k�c_>Swift Compiler - General||_EXPERIMENTAL_SWIFT_EXPLICIT_MODULES� *�*�2n�c_:Swift Compiler - General||SWIFT_OBJC_INTERFACE_HEADER_NAME� *�*�2q�c_3Swift Compiler - General||SWIFT_INSTALL_OBJC_HEADER� *�*�2t�c_.Swift Compiler - General||SWIFT_INSTALL_MODULE� *�*�2w�c_0Swift Compiler - General||SWIFT_MODULES_AUTOLINK� *�*�2z�c_4Swift Compiler - General||SWIFT_OBJC_BRIDGING_HEADER� *�*�2}�c_,Swift Compiler - General||SWIFT_PACKAGE_NAME� *�*�2��c_:Swift Compiler - General||SWIFT_PRECOMPILE_BRIDGING_HEADER� *�*�2��c_9Swift Compiler - General||SWIFT_REFLECTION_METADATA_LEVEL� *�*�2��c_?Swift Compiler - General||SWIFT_SKIP_AUTOLINKING_ALL_FRAMEWORKS� *�*�2��c_;Swift Compiler - General||SWIFT_SKIP_AUTOLINKING_FRAMEWORKS� *�*�2��c_:Swift Compiler - General||SWIFT_SKIP_AUTOLINKING_LIBRARIES� *�*�2��c_2Swift Compiler - Language||SWIFT_OBJC_INTEROP_MODE� *�*�2��c_9Swift Compiler - Language||SWIFT_ENABLE_EMIT_CONST_VALUES� *�*�2��c_(Swift Compiler - Language||SWIFT_VERSION� *�*�2��c_2Swift Compiler - Search Paths||SWIFT_INCLUDE_PATHS� *�*�2��c_ASwift Compiler - Upcoming Features||SWIFT_ENABLE_BARE_SLASH_REGEX� *�*�2��c_MSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_CONCISE_MAGIC_FILE� *�*�2��c_VSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_INTERNAL_IMPORTS_BY_DEFAULT� *�*�2��c_USwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_DEPRECATE_APPLICATION_MAIN� *�*�2��c_ZSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_DISABLE_OUTWARD_ACTOR_ISOLATION� *�*�2��c_TSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_FORWARD_TRAILING_CLOSURES� *�*�2��c_USwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_IMPLICIT_OPEN_EXISTENTIALS� *�*�2��c_TSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_IMPORT_OBJC_FORWARD_DECLS� *�*�2��c_WSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_INFER_SENDABLE_FROM_CAPTURES� *�*�2��c_RSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_ISOLATED_DEFAULT_VALUES� *�*�2��c_MSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_GLOBAL_CONCURRENCY� *�*�2��c_QSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_REGION_BASED_ISOLATION� *�*�2��c_JSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_EXISTENTIAL_ANY� *�*�2c_<Swift Compiler - Upcoming Features||SWIFT_STRICT_CONCURRENCY� *�*�2Łc_;Swift Compiler - Warnings Policies||SWIFT_SUPPRESS_WARNINGS� *�*�2ȁc_BSwift Compiler - Warnings Policies||SWIFT_TREAT_WARNINGS_AS_ERRORS� *�*�2ˁc_User-Defined||MTL_FAST_MATH�   2�2� 8���@�   2�2� �2ҁl�2ԁm�|]IDENameString[GameCatalog� 2فo� \ ]2�2�VNSNull�2� `�  2� S� ��)�   2�2� 8� ��2�r�@�  2�*�� ��d�   2�2� �2�2�2�t�u�v�2�2�2�w�}���|_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKey�   2�2� 8�2�2��x�y�2�2��z�|�@[TheMealsApp[GameCatalog�3 33WNS.time#A���0{� \ ]33VNSDate�3 `�3 3
3#A���h�rC�{�   33 8�33�~��33�����@[TheMealsApp[GameCatalog� *�*�3�c_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64� *�*�3�c_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64�   33 8�3���3 ���@_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64�3 3$3#A���j�H�{�   3'32 �3(3)3*3+3,3-3.3/3031����������������������34� 03738393:3;3<�������s�������������|ZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:7A60B588-4926-4A88-9679-4FA36F2ECDD0Vx86_64_iphonesimulator18.2ViPhoneZiPhone17,1�  3P*���d�   3S3X 8�3T3U3V3W���������3Y3Z3[3\���������@_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildX16C5032aj�  3f*��3g3h3i�������d�   3l3p 8�3m3n3o�������%�3r3Y������@_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ �   3z3~ 8�3m3n3o�������)3�3��R�����@c  %  � � 3� �Obplist00�
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	Troot��%&,/U$null�XNSStringV$class\NSAttributes��	�YSucceeded�WNS.keysZNS.objects�����VNSFont� !"#$VNSSizeXNSfFlagsVNSName#@&      ��_.AppleSystemUIFontBold�'()*Z$classnameX$classesVNSFont�)+XNSObject�'(-.\NSDictionary�-+�'(01_NSAttributedString�2+_NSAttributedString    $ ) 2 7 I L Q S ^ d k t { � � � � � � � � � � � � � � � � � � � � � �%,/8=JMRgj             3              �=�   3�3� 8�3m3�3�3o3�3��������������3��3�3������z�����z�z�@_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle#@      �3 3�3#A��2�G���{o 1 5 / 0 4 / 2 5   a t   5 . 0 1 / P M�   3�3� 8�3�3�3�3�3�3�3�3�������������������3��� 03�3� 0���������s�����s�@_IDEWindowIsFullScreen^IDEWindowFrame_-IDEHasMigratedValuesFromNSRestorableStateData_IDEWindowToolbarIsVisible_&IDEWindowTabBarWasVisibleWithSingleTab_IDEActiveWorkspaceTabController_>IDEWorkspaceTabController_75DACF4F-9477-44F2-97BD-12677AFE5FB7_IDEWindowTabBarIsVisible_209 109 1400 900 0 0 1680 1025 �   3�3� 8�3�3�3�3�3�3�3�3�3ǁ������������Áĩ�3�3�3�3�3�3� 03р��Ł/�0�4�5�7�s�:�@_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorArea�   3�3� 8�3�3�3�3�3�3�3�ƁǁȁɁʁˁ̧3�3�3�3�3�3�3�́؁ف��� �
�@_"Xcode.IDEKit.Navigator.Test.Modern_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigator_Xcode.IDEKit.Navigator.Find_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Debug�   3�3� 8�3�3�3�3��΁ρЁѤ�3�3��A�ҁӁA�@_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey_IDENavigatorModeSolitary�   44	 �4�ԡ4
�Հ|_IDENavigatorModeSolitary� 444_codablePlistRepresentation�ׁ�O�bplist00�_lastAccessedDateYitemState]selectedItems^scrollPosition3A�Ν�Յ����	�
#        #�$      �#@q      #@��     $.<KTUVY\enqz                            �� \ ]44_&ExplorableOutlineViewArchivableUIState�44 `_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier__ Xcode.IDEKit.Navigator.Workspace�   44 8�44444�ځہ܁݁ޥ4 3�3�3�3ށ߁́؁ʁƀ@_"Xcode.IDEKit.NavigatorGroup.Issues_!Xcode.IDEKit.NavigatorGroup.Debug_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_ Xcode.IDEKit.NavigatorGroup.Test_#Xcode.IDENoticesKit.NoticeNavigator�   4-42 8�4.4/4041�����43444546�����@_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey�   4=4? �4>��4@��|_IDENavigatorModeSolitaryOnbplist00�
X$versionY$archiverT$topX$objects ��_NSKeyedArchiver�	Troot��!$'+,U$null�V$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiers� ����ZNS.objects���Z$classnameX$classesWNSArray� XNSObject�"#���%&^NSMutableArray�% �(#�)��_IDEFilterIdentifier_NoticeError�-.__DVTFilterExpressionStateValue�/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u � � � � � � � � � � � � � � �!&59>@BDfk����             2              �_IDENavigatorModeSolitary�   4F4H �4G��4I��|_IDENavigatorModeSolitary� 4M44O_codablePlistRepresentation�ׁ�O_bplist00�_lastAccessedDateYitemState]selectedItems^scrollPosition3A��~�RH��	
TpathYindexHint�[TheMealsApp �^expansionState3A��S=9��	�_$Missing package product 'RealmSwift'�3A��S=9�~���#        #�$      �#@q      #@��        $ . < K T Y ^ c m o { } � � � � � � � � � � � � � � �                           �   4R4T �4S��4U��|_IDENavigatorModeSolitary� 4Y44[_codablePlistRepresentation�ׁ�O�bplist00�_lastAccessedDateYitemState]selectedItems^scrollPosition3A��~�Rz��	
TpathYindexHint�[TheMealsApp_SearchView.swift �^expansionState3A��~����	��3A��~����	�_RemoteDataSource.swift�3A��~������#        #�$      � !#@zp     #@��        $ . < K T [ ` e o r ~ � � � � � � � � � � � � � � � � �             "              "�   4^4a 8�4_4`���4b���>�@_queryParametersController]filterPattern�   4h4l 8�4i4j4k�������4m4n4o�������@_lastEasyToInitiateQueryClass_querySpecification[queryAction_IDEBatchFindTextQuery�4v4w4x 4y W4{4|YqueryTermZnamedScopeZqueryClass��� ����� 4~44�4��TtextXtermType����� *�*�4��cP� \ ]4�4�_IDEBatchFindQueryTerm�4� `_IDEBatchFindQueryTerm_IDEBatchFindTextQuery� \ ]4�4�_IDEBatchFindQuerySpecification�4� `_IDEBatchFindQuerySpecificationTfind�   4�4� 8�4�4�4�4�������4�4���B���B�@_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey_IDENavigatorModeSolitary�   4�4� �4���4���|_IDENavigatorModeSolitary� 4�44�_codablePlistRepresentation�ׁ	Obplist00�      ef_lastAccessedDateYitemState]selectedItems^scrollPosition3A��,n (���       ! % ' ) + / 1 3 5 8 : = ? B D F H J K N P S U W Y [ ] _ ` b d g i k m o q t v y { }  � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � � �	!#%')*,.01358:<=?ACDFHJLOQSUWY[]_ac�  	 
 TpathYindexHint�     [TheMealsAppTCoreUUtilsZExtensions�    ^expansionState3A���i���  	  �   [GameCatalog_Preview Content�    3A���i�i�  	  �     WNetwork�     3A���i�� �  	 "  �   # $VModuleVDetail�   &  3A���i���  	 ( �     �   *  3A���i��  	 , .�    -VDomain�   0  3A���i�)�  	 2 .�    -�   4 3A��*�=�  	 6  �   # 7ZNew Folder�   9  3A���i�:�  	 ; .�     <VMapper�   >  3A���i�B�  	 @  �    - AUModel�   C 3A���i�]��  	 E  �    - A�   G  3A���i�S�  	 I �     <�   G  �  	 L .�   # MTHome�   O  3A���i�d�  	 Q � R_Package Dependencies�   T  3A���rJE�  	 V  �  �   X  3A���i�l�  	 Z  �  �   \  3A���i�u�  	 ^ �     �   \  �  	 a  � �   c  3A���i�}�  	 e �   # $ fVRouter�   h  3A�� a����  	 j �   # M f�   l  3A���i�P��  	 n �   # M f�   p  3A���i���  	 r �   # s fVSearch�   u  3A���i���  	 w �   # x fXFavorite�   z  3A���i���  	 | �   # $ f�   ~  3A���i���  	 �  �   �SApp�   ~  �  	 �  �   ��   � 3A��*���  	 � �    - �WUseCase�   �  3A���i�&W�  	 � �    - ��   �  3A���i���  	 � .�   # x�   � 3A���i���  	 � �     <�   �  3A���i���  	 � �   # � �TMealTView�   �  3A���i���  	 � �     ��   �  �  	 �  �   ��   �  3A���i���  	 � .�   # s ��   �  �  	 � .�   # M ��   �  3A���i���  	 � .�   # $ ��   �  3A���i���  	 � .�   # x ��   �  �  	 � .�     ��   �  3A���i���  	 � .�   # $ ��   �  3A�� a@���  	 � .�   # M ��   �  3A���i�Z��  	 �  �     ��   �  3A���i���  	 � �   �   �  3A���i��  	 � �    �   �  3A���i��  	 � �    �   � 3A���i��A�  	 � �   �   � 3A��*�@�*�  	 � � R �URealm�   � 3A���i��  	 � �   # $�   �  3A�� `<���  	 � �   # M�   � 3A�� 
��  	 � �     �   �  3A���i�%�  	 � �   # s�   �  3A���i�-�  	 � �   # ��   �  3A���i�6�  	 � �    � � �TDataVRemoteXResponse�   �  3A����<�  	 � �    � � ��   �  3A���i�>�  	 �  �    � �VLocale�   �  �  	 � � R �_GameCatalogAbout�    3A���r4���  	  �    � � ��   3A���i�O�  	  �    � ��    3A��*�r���  	
 .�   #�    3A���i�W�  	 .�   #�    3A���i�:��  	  �   #UAbout�   3A�� 	���  	  �   # sYPresenter�    3A���i�h�  	  �   # $�    3A���~&`��  	   �   # x�  "  3A���i�p�  	$  �   # ��  &  3A���i�y�  	(  �   # $�  &  �  	+  �   # M�  -  3A���i���  	/  �    ��  -  �  	2  �    ��  4  3A��*����  	6  �    � �7VEntity�  9 3A���i���  	; .�     �  9  �  	> �    � ��  @  3A���i���  	B �   # x�  @  �  	E �    � ��  G  3A��*��T&�  	I �   # ��  K 3A���i�k��  	M �   NRDI�  P 3A��*딑!�  	R �   N�  T  3A���i���  	V  � �  X  3A���i����  	Z  �  �  \  3A���i����  	^ �    � �7�  `  3A���i���  	b �    � �7�  d  3A���Yh��gj�hi#        #�$      �kl#@zp     #@��        , 6 D S \������������	%79BKT_gpy{��������������� 	$-6AJS\gnw��������������!$;DMV[dmv{���������������	$/8AJU\enw���������������'09DMV_hqz���������������#,5@IR[fox��������������$-6=FOXajs|���������������					'	0	;	=	F	O	X	a	j	s	|	�	�	�	�	�	�	�	�	�	�	�	�	�	�



 
)
.
A
J
S
\
i
r
{
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
�#-6?HS\eny��������������!*3<IPYbkv�������������
(1:CLU^ajs|���������������             m              �   4�4� 8�4�4�4�4�4�4��������[4���4� 0����6���.�s�@_IDEStackCompressionValue_IDEDebugTransientStates_DBGNavigatorContentMode_IDEShowOnlyRunningBlocks^IDEVisibleRect_IDEShowOnlyInterestingContent�   4�4� 8�4�4�4�4�4�4�4ʁ������� .4�4�4�����r����������@_(IDENavigatorModeBeforeViewDebuggingState_#IDECurrentLaunchSessionReferenceKey_IDEDebugSelectedNavigableItems_IDEDebugExpandedItems_1IDEHaveInitiallyExpandedCPUDebuggingChildrenState_2IDEHaveInitiallyExpandedViewDebuggingChildrenState_%IDEDebugAlreadyShownForNewPausedState\7fe9a34c3dd0�  4�*���d�  4�)��4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4�4����� �!�"�#�$�%�&�'�(�)�*�+�,�-�_$E23CE460-B15E-4390-8F2A-14A44EEE78EC_$C336DFA3-A769-4502-891E-6AF332AD83E8[0x10d70c200_$AEB1D2B1-3C08-4FFE-ADCF-8EE2C54AAE78[0x10d410fe0[0x10d70f950_TheMealsApp %% 1_$1B15B918-8A69-44DF-B4DC-AA2889999165_$B27FB6E6-B2C5-40B7-859A-FBC765C10454[0x11000dd40_$9384F16E-0C51-4EBF-9D13-0963BC9D2684[TheMealsApp_$0A9225E6-0FCF-486A-8C89-F69765CD8DDD[0x11000ec70_$8A43148C-6250-4C9A-B852-65B746959956_$B124E38B-7701-44C5-AFEC-9A2876A893A6[0x10f82fc00_$9D43F377-2E6C-4E55-A5D1-CE95B01D0E12_{{0, -10}, {335.5, 955}}#@p@     �   5	5 8�5
5�1�2��5�{�3�@_'userPreferredInspectorGroupExtensionIDs_!userPreferredCategoryExtensionIDs�  5*���d#@zp     �   55 8�5�6�����@_ShowsOnlyVisibleViewObjects�   55! 8�55 �8�9� 0 0�s�s�@_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspace�   5(54 8�5)5*5+5,5-5.5/50515253�;�<�=�>�?�@�A�B�C�D�E�55 .5758595:5;5<5=5>5?�F�r�G�H�I�J��(�G�H�Q�@_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ EditorMultipleSplitPrimaryLayout_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_IDEDefaultDebugArea_ DefaultPersistentRepresentations_DebuggerSplitView^MaximizedState� �5M �� �_NSIndexPathValue�>ZOpenInTabs _Layout_LeftToRight�  5S*��5T�K�d�   5W5^ 8�5X5Y5Z5[5\5]�L�M�N�O�P�Q�5_5`5a5_5c 0�R�S���R��s�@ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments�   5n5q 8�5o5p�T�U�5r��V�d�@_TabsAsHistoryItems_SelectedTabIndex�  5x S�5y5z5{5|5}5~55�5�5��W�����݁�,�U�}���΀)�5�5�5�5� 5�5�5�5�5�5�5�5�5� Q5�_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifier�\�o�q�X���\�%�p�5�5�5� 5�5�5�5�5�5�5� ._DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifier�h�Z�k�n�Y�r_/Xcode.IDENavigableItemDomain.WorkspaceStructure�  5� S�5�5�5�5�5�5��[�^�`�b�d�f�)�5�5� 5� .5�ZIdentifierUIndex�\�r�]_DetailPresenter.swift� \ ]5�5�_IDEArchivableStringIndexPair�5� `_IDEArchivableStringIndexPair�5�5� 5� .5��_�r�]YPresenter�5�5� 5��5��a�z�]VDetail�5�5� 5�[5��c���]VModule�5�5� 5� .5��e�r�][GameCatalog�5�5� 5� .5��g�r�][GameCatalog� �  �5�5� W�i�j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Presenter/DetailPresenter.swift� \ ]5�5�_DVTDocumentLocation�5� `_DVTDocumentLocation� 5�5�5�ZpathString�m�l_e/Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog.xcodeproj� \ ]5�5�[DVTFilePath�5�5� `[DVTFilePath_PackedPathEntry� \ ]5�5�_(IDENavigableItemArchivableRepresentation�5� `_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCode�   5�5� 8�5�5�5�5�5�5�5�5�5�5�r�s�t�u�v�w�x�y�z�{�5�� 0 05�� . 05���|���s�s�~���r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  6
 S�6�}�)�  6 S�� .� .���r���r�)�   66 �66����66�����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   66" �6 ����y�
���{�|^documentLength�   6(6, �6)6*6+�������6-6.с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�65 66�\NS.uuidbytesO���!��I��׺3�4���69 6:�\NS.uuidbytesO�/�e�r@��/�:�P�8���   6=6@ ��6?������
�{��|^documentLength� \ ]6F6G_IDEEditorHistoryItem�6H `_IDEEditorHistoryItem�5�5�5�5� 5�5�5�6J5�6L6M5�6J R5����o���������'�p�5�5�5� 5�5�6S6T5�5�5� .�����k�n�Y�r�  6Z S�6[6\6]6^6_6`�������������)�5�5� 6J .5����r�]_DetailRouter.swift�5�5� 6h�5����z�]VRouter�5�5� 5��5��a�z�]�5�5� 5�[5��c���]�5�5� 5� .5��e�r�]�5�5� 6y .5����r�][GameCatalog� �  �6~5� W���j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Router/DetailRouter.swift�   6�6� 8�6�6�6�6�6�6�6�6�6�6����������������������6�� 0 06�� . 06�������s�s�����r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  6� S�6����)�  6� S�� .� .�T�r�f�r�)�   6�6� �6�6������6�6������|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   6�6� �6�6�6��������6�6�с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�6� 6��\NS.uuidbytesO,Q��3TD���ӵj-g���6� 6��\NS.uuidbytesO�4�^�TAކ�{�P9���   6�6� ��6рy������{�S�|^documentLength�   6�6� ��6ڀ�������{�S�|^documentLength�5�5�5�5� 5�5�5�6�5�6�6�5�6� P5����o�����܀#�p�5�5�5� 5�5�6�6�5�5�5� .�����k�n�Y�r�  6� S�6�6�6�6�6�6��������������)�5�5� 6� .5����r�]_DetailView.swift�5�5� 6�[5������]TView�5�5� 5��5��a�z�]�5�5� 5�[5��c���]�5�5� 5� .5��e�r�]�5�5� 7 .5����r�][GameCatalog� �  �75� W���j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/DetailView.swift�   77% 8�777777 7!7"7#7$�ÁāŁƁǁȁɁʁˁ̪7&� 0 07*
,
- 07.��̀��s�s�ρ��s�ڀ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  7< S�7=�΀)�  7@ S�
B�
B����������)�   7G7J �7H7I�ЁѢ7K7L�ҁԀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   7Q7T ��7S�y�Ӣ�
l�{��|^documentLength�   7Z7^ �7[7\7]�Ձցף7_7`с؁ـ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�7g 7h�\NS.uuidbytesO6)���?Au�&
�1�)���7k 7l�\NS.uuidbytesO�Z3^=�C��\$�̀��   7o7r ��7q���ۢ�
l�{��|^documentLength_detailsSection(_:)�5�5�5�5� 5�5�5�7y7z7{7|5�7y N5�����ށ����p�5�5�5� 5�5�7�7�5�5�5� .��߁k�n�Y�r�  7� S�7�7�7������)�5�5� 7��5���z�]]Package.swift�5�5� 7�5���ށ]_SDWebImageSwiftUI�5�5� 7��5���z�]_Package Dependencies� �  �7�5� W��j� _�file:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/SDWebImageSwiftUI/Package.swift_%com.apple.dt.swiftpm-package-manifest�   7�7� 8�7�7�7�7�7�7�7�7�7�7�������������7�� 0 07�* . 07���􀈀s�s���S�r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  7� S�7ǁ��)�  7� S� . . . .�r�r�r�r�)�   7�7� �7�7Ӂ����7�7ց����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   7�7� ��7݀y������{���|^documentLength�   7�7� �7�7�7�������7�7�с�� ���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�7� 7��\NS.uuidbytesO�5Co�J��G/��Q���7� 7��\NS.uuidbytesO3��;-�O_�զ�1/����   7�7� ��7��������{���|^documentLength]Package.swift�5�5�5�5� 5�5�5�85�885�8 M5���o�������p�5�5�5� 5�5�885�5�5� .���k�n�Y�r�  8 S�88888��	����)�5�5� 8[5�����]_GameRespository.swift�5�5� 8  .5��
�r�]TData�5�5� 8%�5���z�]TCore�5�5� 5� .5��e�r�]�5�5� 8. .5���r�][GameCatalog� �  �835� W��j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/GameRespository.swift�   888C 8�898:8;8<8=8>8?8@8A8B�����������8D� 0 08H* . 08L�����s�s��S�r�s�*���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  8Z S�8[��)�  8^ S� . . . .�r�r�r�r�)�   8e8h �8f8g� �!�8i8j�"�(�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   8o8s �8p8q8r�#�$�%�8t8uс&�'���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�8| 8}�\NS.uuidbytesO�0qԽ�J���F1�q~%���8� 8��\NS.uuidbytesO0핌H���Cg�:����   8�8� ��8��y�)��!��{�	��|^documentLength�   8�8� ��8����+��!��{�	��|^documentLength�5�5�5�5� 5�5�5�8�5�8�8�5�8� L5��0�o�:�-���T��p�5�5�5� 5�5�8�8�5�5�5� .�8�.�k�n�Y�r�  8� S�8�8�8�8�8�8��/�1�3�4�5�6�)�5�5� 8� .5��0�r�]_RemoteGameDataSource.swift�5�5� 8��5��2�z�]VRemote�5�5� 8  .5��
�r�]�5�5� 8%�5���z�]�5�5� 5� .5��e�r�]�5�5� 8� .5��7�r�][GameCatalog� �  �8�5� W�9�j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Remote/RemoteGameDataSource.swift�   8�8� 8�8�8�8�8�8�8�8�8�8�8ف;�<�=�>�?�@�A�B�C�D�8�� 0 08�* . 08���E���s�s�G�S�r�s�R���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  8� S�8�F�)�  8� S�oooo���������)�   8�8� �8�8��H�I�9 9�J�L�|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   99	 �9��K�y���p�{�|^documentLength�   99 �999�M�N�O�99сP�Q���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�9 9�\NS.uuidbytesO�ב%8b@��,�-D������9  9!�\NS.uuidbytesO)]�Gö��?o'\���   9$9' ��9&���S���{�p�|^documentLength\NetworkError�5�5�5�5� 5�5�5�9.5�90915�9. K5��Y�o�c�V���Y��p�5�5�5� 5�5�97985�5�5� .�a�W�k�n�Y�r�  9> S�9?9@9A9B9C9D�X�Z�\�]�^�_�)�5�5� 9. .5��Y�r�]_LocaleGameDataSource.swift�5�5� 9L .5��[�r�]VLocale�5�5� 8  .5��
�r�]�5�5� 8%�5���z�]�5�5� 5� .5��e�r�]�5�5� 9] .5��`�r�][GameCatalog� �  �9b5� W�b�j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/LocaleGameDataSource.swift�   9g9r 8�9h9i9j9k9l9m9n9o9p9q�d�e�f�g�h�i�j�k�l�m�9s� 0 09w* . 09{��n���s�s�p�S�r�s�{���@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  9� S�9��o�)�  9� S� . . . .�r�r�r�r�)�   9�9� �9�9��q�r�9�9��s�y�|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   9�9� �9�9�9��t�u�v�9�9�сw�x���|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�9� 9��\NS.uuidbytesO�t9�+G����@�`Ċ���9� 9��\NS.uuidbytesO�]��{ JZ��&���O���   9�9� �9���z�y������{�|^documentLength�   9�9� ��9����|����{���|^documentLength�5�5�5�5� 5�5�5�9�5�9�9�5�9� J5����o���~������p�5�5�5� 5�5�9�9�5�5�5� .����k�n�Y�r�  9� S�9�9�9�9�9�9�9܁��������������)�5�5� 9� .5����r�]_GameEntity.swift�5�5� 9��5����z�]VEntity�5�5� 9L .5��[�r�]�5�5� 8  .5��
�r�]�5�5� 8%�5���z�]�5�5� 5� .5��e�r�]�5�5� 9� .5����r�][GameCatalog� �  �9�5� W���j� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/Entity/GameEntity.swift�   :: 8�::::::	:
:::���������������������:� 0 0:* . 0:������s�s���S�r�s�����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  :% S�:&���)�  :) S� . . . .�r�r�r�r�)�   :0:3 �:1:2�����:4:5�����|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension�   :::> �:;:<:=�������:?:@с������|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�:G :H�\NS.uuidbytesO�?�;�K:�5���W�����:K :L�\NS.uuidbytesOv�_�]C̗���� 瀂�   :O:R ��:Q�y����y�{�S�|^documentLength�   :X:[ ��:Z������y�{�S�|^documentLength�5�5�5�5� 5�5�5�:a5�:c:d5�:f I5����o�������̀�p�5�5�5� 5�5�:j:k5�5�5� .�����k�n�Y�r�  :q S�:r:s:t:u:v�����������)�5�5� :a .5����r�]_Injection.swift�5�5� :~�5����z�]RDI�5�5� 8%�5���z�]�5�5� 5� .5��e�r�]�5�5� :� .5����r�][GameCatalog� �  �:�5� W���j� _zfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/DI/Injection.swift�   :�:� 8�:�:�:�:�:�:�:�:�:�:����������������������:�� 0 0:��q 0:�������s�s���s���s�ˀ��@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  :� S�:����)�  :� S�8�8�:�q�:��)�   :�:� �:�:ā��¢:�:ǁÁŀ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   :�:� �:���Āy�Y��߀{�|^documentLength�   :�:� �:�:�:؁Ɓǁȣ:�:�сɁʀ��|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�:� :��\NS.uuidbytesO��\�9Lյj3������:� :��\NS.uuidbytesOK�h���E͖���\"����   :�:� ��:쀆�̢�Y�{�߀|^documentLength_provideDetail(gameId:)�5�5�5�5� 5�5�5�:�5�:�:�5�:�P5��ҁo�ہρ��ҁ8�p�5�5�5� 5�5�:�:�5�5�5� .�فЁk�n�Y�r�  ; S�;;;;;	;
�сӁԁՁց׀)�5�5� :��5��Ҁz�]]MealRow.swift�5�5� 6�[5������]�5�5� 5��5��a�z�]�5�5� 5�[5��c���]�5�5� 5� .5��e�r�]�5�5� ;" .5��؀r�][GameCatalog� �  �;'5� W�ځj� _�file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/MealRow.swift�   ;,;7 8�;-;.;/;0;1;2;3;4;5;6�܁݁ށ߁�������;8� 0 0;<;=8 0;@��怈�s�s���:�s�􀈀@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ;N S�;O��)�  ;R S� . . . .�r�r�r�r�)�   ;Y;\ �;Z;[���;];^���|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ;c;f �;d���y�a��3�{�|^documentLength�   ;l;p �;m;n;o����;q;rс�򀄀|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�;y ;z�\NS.uuidbytesOֹ./#B��3�dG�f����;} ;~�\NS.uuidbytesO��+���Mq�|C�_�2��#@#      �   ;�;� ��;�������a�{�3�|^documentLength�  ;�*��;����d�;� ;�;�_currentEditorHistoryItem����5�5�5�5� 5�5�5�:a5�;�;�5�;� I5����o��������p�5�5�5� 5�5�;�;�5�5�5� .����k�n�Y�r�  ;� S�;�;�;�;�;������������)�5�5� :a .5����r�]�5�5� :~�5����z�]�5�5� 8%�5���z�]�5�5� 5� .5��e�r�]�5�5� ;� .5�� �r�][GameCatalog� �  �:�5� W���j� �   ;�;� 8�;�;�;�;�;�;�;�;�;�;΁������	�
���;�� 0 0;�� . 0;������s�s����r�s����@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines�  ;� S�;��)�  ;� S�D�D��فd�فd�)�   ;�;� �;�;���;�;����|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension�   ;�;� �;����y�Y��߀{�|^documentLength�   << �<<<����<	<
с����|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width�< <�\NS.uuidbytesO��\�9Lյj3������< <�\NS.uuidbytesOK�h���E͖���\"����   << ��<�����Y�{�߀|^documentLengthYInjection� \ ]<#<$_IDEEditorHistoryStack�<% `_IDEEditorHistoryStack_ItemKind_Editor�5�5�5� 5�5�<(<)5�5�5� .�'� �k�n�Y�r�  </ S�<0<1<2<3<4�!�"�#�$�%�)�5�5� :a .5����r�]�5�5� :~�5����z�]�5�5� 8%�5���z�]�5�5� 5� .5��e�r�]�5�5� <G .5��&�r�][GameCatalog� �  �:�5� W���j� �   <P<U 8�<Q<R<S<T�)�*�+�,�<V<W[<Y�-�2���;�@XLeftViewYRightViewZLayoutMode_IDESplitViewDebugArea�   <`<e 8�<a<b<c<d�.�/�0�1� 0 .���s�r�{���@_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsType�   <p<y 8�<q<r<s<t<u<v<w<x�3�4�5�6�7�8�9�:� 0 0 0� 0 0 0 0�s�s�s���s�s�s�s�@_+IDEStructuredConsoleAreaLibraryEnabledState_-IDEStructuredConsoleAreaTimestampEnabledState_*IDEStructuredConsoleAreaPIDTIDEnabledState_,IDEStructuredConsoleAreaMetadataEnabledState_(IDEStructuredConsoleAreaTypeEnabledState_-IDEStructuredConsoleAreaSubsystemEnabledState_/IDEStructuredConsoleAreaProcessNameEnabledState_,IDEStructuredConsoleAreaCategoryEnabledState�   <�<� 8�<��<�<��=�@_DVTSplitViewItems�  <�*��<�<��>�D�d�   <�<� �<�<�<��?�@�A�<� 0<��B�s�C�|]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeXLeftView#@��     �   <�<� �<�<�<��?�@�A�<��<��E���F�|YRightView#@|(     �   <�<� 8���@�   <�<� 8�<��I�<��J�@_DVTSplitViewItems�  <�*��<�<��K�N�d�   <�<� �<�<�<��?�@�A�<��<ʁL���M�|YIDEEditor#@��     �   <�<� �<�<�<��?�@�A�<��<ցO���P�|_IDEDebuggerArea#@_�               "   ,   1   :   ?   Q   V   \   ^  #  #  #  #!  #,  #3  #8  #:  #<  #A  #C  #F  #H  #_  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  #�  $   $  $  $	  $  $  $  $+  $H  $U  $u  $�  $�  $�  $�  $�  %  %&  %8  %k  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  %�  &Z  &c  &n  &w  &}  &�  &�  &�  &�  &�  &�  ',  '9  ';  '=  '?  '�  '�  '�  '�  '�  (t  (�  (�  (�  (�  )  )  )  )  )  )�  )�  )�  )�  )�  *_  *l  *n  *p  *r  *�  +  +
  +  +  +�  +�  +�  +�  +�  ,A  ,J  ,R  ,W  ,d  ,s  ,u  ,w  ,y  ,{  ,}  ,  ,�  ,�  ,�  ,�  ,�  ,�  ,�  ,�  ,�  ,�  ,�  -  -?  -o  -�  -�  .  .  .   ."  .$  .)  .+  .-  ./  .<  .>  .@  .B  .w  .�  .�  .�  .�  .�  .�  .�  .�  .�  .�  .�  .�  .�  /   /  /  /  /#  /6  /B  /L  /N  /P  /Q  /S  /U  /b  /v  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  /�  0  0  01  0G  0P  0f  0m  0z  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  0�  1	  1  1  1  1  1  1  1  1*  1,  1.  10  1V  1c  1e  1g  1i  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  1�  2  2  2  2#  2,  2.  20  22  24  26  2?  2H  2Q  2S  2U  2W  2Y  2[  2d  2m  2o  2q  2s  2u  2w  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  2�  3  3  3  3  3  3  3!  3#  3,  35  37  39  3;  3=  3?  3L  3O  3Q  3T  3V  3X  3e  3g  3i  3k  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  :�  ;  ;
  ;  ;  ;  ;  ;  ;  ;  ;  ;  ;  ;;  ;Z  ;~  ;�  ;�  ;�  <  <-  <L  <i  <r  <u  <w  <y  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  <�  =  =  =  =  =  =  =  =  =)  =5  =7  =@  =A  =C  =L  =Q  =^  =e  =g  =i  =k  =r  =t  =v  =x  =z  =�  =�  =�  =�  >  >  >!  >*  >1  >6  >?  >L  >_  >a  >j  >w  >|  >~  >�  >�  >�  >�  >�  >�  >�  >�  >�  ?u  ?w  ?y  ?{  ?}  ?  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  ?�  @  @  @  @
  @  @  @  @  @  @  @  @   @#  @&  @)  @,  @/  @2  @5  @8  @;  @>  @A  @D  @G  @J  @M  @P  @S  @V  A  A   A#  A&  A)  A,  A/  A2  A5  A8  A;  A>  AA  AD  AG  AJ  AM  AP  AS  AV  AY  A\  A_  Ab  Ae  Ah  Ak  An  Aq  At  Aw  Az  A}  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  A�  B  B  B  B
  B  B  B  B  B  B  B  B"  B%  B(  B+  B.  B1  B4  B7  B:  B=  B@  BC  BE  BR  BT  BV  BX  B�  B�  B�  B�  B�  Cp  C}  C  C�  C�  C�  D	  D  D  D  Du  D�  D�  D�  D�  E  E)  E+  E-  E/  E�  E�  E�  E�  E�  F0  F=  F?  FA  FC  F�  F�  F�  F�  F�  G@  GM  GO  GQ  GS  G�  G�  G�  G�  G�  H[  Hh  Hj  Hl  Hn  H�  H�  H�  H�  H�  Ij  Iw  Iy  I{  I}  I�  I�  J  J  J  J{  J�  J�  J�  J�  J�  K  K  K  K  K�  K�  K�  K�  K�  L  L!  L#  L%  L'  L�  L�  L�  L�  L�  MD  MQ  MS  MU  MW  M�  M�  M�  M�  M�  NZ  Ng  Ni  Nk  Nm  N�  N�  N�  N�  N�  O^  Ok  Om  Oo  Oq  O�  O�  O�  P  P  Pn  P{  P}  P  P�  P�  P�  P�  P�  Q  Qt  Q�  Q�  Q�  Q�  R  R  R  R  R  R�  R�  R�  R�  R�  S+  S8  S:  S<  S>  S�  S�  S�  S�  S�  T1  T>  T@  TB  TD  T�  T�  T�  T�  T�  U1  U>  U@  UB  UD  U�  U�  U�  U�  U�  V   V-  V/  V1  V3  V�  V�  V�  V�  V�  W*  W7  W9  W;  W=  W�  W�  W�  W�  W�  X7  XD  XF  XH  XJ  X�  X�  X�  X�  X�  YC  YP  YR  YT  YV  Y�  Y�  Y�  Y�  Y�  ZH  ZU  ZW  ZY  Z[  Z�  Z�  Z�  Z�  Z�  [X  [e  [g  [i  [k  [�  [�  [�  [�  [�  \Q  \^  \`  \b  \d  \�  \�  \�  \�  \�  ]B  ]O  ]Q  ]S  ]U  ]�  ]�  ]�  ]�  ]�  ^T  ^a  ^c  ^e  ^g  ^�  ^�  ^�  ^�  ^�  _h  _u  _w  _y  _{  _�  `  `  `  `  `�  `�  `�  `�  `�  a	  a  a  a  a  a�  a�  a�  a�  a�  b$  b1  b3  b5  b8  b�  b�  b�  b�  b�  cK  cX  cZ  c\  c_  c�  c�  c�  c�  c�  d^  dk  dm  do  dr  d�  d�  d�  d�  d�  en  e{  e}  e  e�  e�  f  f  f  f
  f{  f�  f�  f�  f�  g  g  g  g  g  g�  g�  g�  g�  g�  h   h-  h/  h1  h4  h�  h�  h�  h�  h�  iV  ic  ie  ig  ij  i�  i�  i�  i�  i�  je  jr  jt  jv  jy  j�  j�  j�  k   k  k�  k�  k�  k�  k�  l  l%  l'  l)  l,  l�  l�  l�  l�  l�  m3  m@  mB  mD  mG  m�  m�  m�  m�  m�  n7  nD  nF  nH  nK  n�  n�  n�  n�  n�  oJ  oW  oY  o[  o^  o�  o�  o�  o�  o�  p]  pj  pl  pn  pq  p�  p�  p�  p�  q   q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  q�  r  r2  rV  rx  r�  r�  r�  s  s$  sA  sJ  sM  sP  sR  s[  sd  sf  sh  sj  sl  sn  s{  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  s�  t  t  t  t  t  t"  t%  t,  t/  t2  t4  t6  t_  t�  t�  t�  t�  t�  t�  t�  t�  u  u  u  u  u   u%  u'  u*  u/  u1  u4  u6  uE  uR  ug  uj  um  up  us  uv  uy  u|  u  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  u�  v  v7  v[  v�  v�  v�  v�  w   w	  w  w  w  w  w#  w&  w)  w,  w/  w1  w3  w5  w7  wD  wI  wL  wO  wT  wW  wZ  w\  w  w�  w�  w�  w�  w�  w�  w�  w�  w�  w�  w�  w�  x%  xF  xO  x\  xo  xq  xz  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  x�  y  y  y0  y3  y6  y9  y<  y?  yB  yE  yH  yK  yN  yc  yf  yh  yj  yl  yo  yr  yu  yw  yz  y|  y~  y�  y�  y�  z   z$  zJ  zn  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  z�  {
  {  {  {  {  {  {   {"  {E  {b  {o  {v  {y  {|  {  {�  {�  {�  {�  {�  {�  {�  |  |  |"  |5  |7  |@  |M  |`  |b  |o  |t  |w  |y  |~  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  |�  }  }  }  }  }  }  }  })  },  }.  }0  }2  }5  }8  }:  }<  }?  }A  }C  }`  }  }�  }�  }�  ~  ~3  ~R  ~q  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�  ~�    $  1  6  9  ;  @  C  E  G  V  Y  f  m  p  s  v  }  �  �  �  �  �  �  �  �  �  �,  �.  �7  �D  �W  �Y  �b  �o  �t  �v  �y  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �>  �b  ��  ��  ��  ��  �  �0  �M  �V  �Y  �\  �^  �g  �p  �s  �v  �y  �|  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �
  �  �  �)  �0  �3  �6  �9  �@  �C  �F  �H  �J  �s  ��  ��  ��  ��  ��  ��  ��  �  �  �  �)  �.  �0  �3  �8  �:  �=  �?  �N  �[  �p  �s  �v  �y  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �?  �c  ��  ��  ��  ��  �  �  �  �  �  �"  �+  �.  �1  �4  �7  �9  �;  �H  �M  �P  �S  �X  �[  �^  �`  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �)  �[  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �&  �)  �,  �/  �2  �5  �8  �;  �>  �A  �D  �Y  �\  �^  �`  �b  �e  �h  �j  �l  �o  �q  �s  ��  ��  ��  ��  �  �?  �c  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �9  �V  �c  �j  �m  �p  �s  �z  �}  ��  ��  ��  ��  ��  �   �	  �  �)  �+  �4  �A  �T  �V  �c  �h  �j  �m  �r  �u  �x  �z  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �   �#  �%  �'  �)  �,  �/  �2  �4  �7  �9  �;  �X  �w  ��  ��  ��  �  �+  �J  �i  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �-  �2  �4  �7  �<  �>  �A  �C  �R  �_  �f  �i  �l  �o  �v  �y  �|  �~  ��  ��  ��  ��  �  �  �%  �'  �0  �=  �P  �R  �[  �]  �j  �o  �q  �t  �y  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �:  �^  ��  ��  ��  ��  �  �,  �I  �R  �U  �X  �Z  �c  �l  �o  �r  �u  �x  �z  �|  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �%  �,  �/  �2  �5  �<  �?  �B  �D  �F  �o  ��  ��  ��  ��  ��  ��  ��  �  �  �  �%  �*  �,  �/  �4  �6  �9  �;  �J  �W  �l  �o  �r  �u  �x  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �<  �`  ��  ��  ��  ��  �  �  �  �  �  �  �(  �+  �.  �1  �3  �5  �7  �D  �I  �L  �O  �T  �W  �Z  �\  �y  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �(  �Z  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �0  �3  �6  �9  �<  �?  �B  �E  �H  �K  �N  �c  �f  �h  �j  �l  �o  �q  �s  �u  �x  �z  �|  ��  ��  ��  ��  �"  �H  �l  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �  �6  �Y  �f  �k  �n  �p  �u  �x  �z  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �8  �A  �N  �a  �c  �l  �y  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �!  �$  �&  �(  �+  �-  �/  �L  �k  ��  ��  ��  ��  �  �>  �]  �z  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �%  �(  �+  �.  �5  �8  �;  �=  �?  �h  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �#  �&  �(  �-  �0  �2  �4  �C  �E  �R  �W  �Y  �\  �a  �c  �f  �h  �w  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �!  �E  �g  ��  ��  ��  ��  �  �0  �9  �<  �?  �A  �J  �S  �U  �W  �Y  �[  �]  �j  �o  �r  �u  �z  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �K  �l  �u  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �	  �  �  �  �  �  �  �)  �6  �K  �N  �Q  �T  �W  �Z  �]  �`  �c  �f  �i  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �=  �c  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �!  �$  �'  �,  �/  �2  �4  �Q  �t  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �2  �S  �\  �i  �|  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �	  �  �  �  �  �  �  �0  �3  �5  �7  �9  �<  �>  �@  �B  �E  �G  �I  �f  ��  ��  ��  ��  �  �9  �X  �w  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �	  �&  �3  �:  �=  �@  �C  �J  �M  �P  �R  �T  �}  ��  ��  ��  ��  ��  ��  �  �  �$  �&  �3  �8  �:  �=  �B  �D  �G  �I  �X  �e  �j  �l  �o  �t  �v  �y  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �6  �Z  �|  ��  ��  ��  �	  �(  �E  �N  �Q  �T  �V  �_  �h  �k  �n  �q  �t  �v  �x  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �	  �  �4  �U  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �+  �0  �2  �5  �:  �=  �@  �B  �Q  �^  �s  �v  �y  �|  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �A  �e  ��  ��  ��  ��  �
  �  �  �  �  �$  �-  �/  �1  �3  �5  �7  �D  �I  �L  �O  �T  �W  �Z  �\  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �F  �O  �\  �o  �q  �z  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �'  �*  �-  �0  �3  �6  �9  �<  �?  �B  �E  �Z  �]  �_  �a  �c  �f  �i  �l  �n  �q  �s  �u  ��  ��  ��  ��  �  �A  �e  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �  �<  �Y  �f  �m  �p  �s  �v  �}  ��  ��  ��  ��  ��  ��  �  �  �  �,  �.  �7  �D  �W  �Y  �f  �k  �m  �p  �u  �w  �z  �|  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �   �#  �%  �'  �)  �,  �.  �0  �M  �l  ��  ��  ��  ��  �   �?  �^  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �"  �$  �)  �,  �/  �1  �@  �C  �P  �W  �Z  �]  �`  �g  �j  �m  �o  �q  ��  ��  ��  ��  �  �  �  �!  �.  �A  �C  �P  �U  �W  �Z  �_  �b  �e  �g  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �"  �F  �h  ��  ��  ��  ��  �  �1  �:  �=  �@  �B  �K  �T  �W  �Z  �]  �`  �b  �d  �f  �s  �x  �{  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �$  �'  �*  �,  �.  �W  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �  �   �#  �%  �4  �A  �V  �Y  �\  �_  �b  �e  �h  �k  �n  �q  �t  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �%  �I  �o  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �*  �/  �2  �5  �:  �=  �@  �B  �e  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �,  �5  �B  �U  �W  �`  �m  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �   �#  �&  �)  �>  �A  �C  �E  �G  �J  �M  �P  �R  �U  �W  �Y  �v  ��  ��  ��  ��  �%  �I  �h  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �6  �C  �J  �M  �P  �S  �Z  �]  �`  �b  �d    ¿  ��  ��  ��  �	  �  �  �!  �4  �6  �C  �H  �J  �M  �R  �T  �W  �Y  �h  �q  �s  À  Å  Ç  Ê  Ï  Ñ  Ô  Ö  å  ò  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �	  �  �  �  �  �  �1  �P  �t  Ė  ĺ  ��  �  �#  �B  �_  �h  �k  �n  �p  �y  ł  Ņ  ň  ŋ  Ŏ  Ő  ŝ  Ţ  ť  Ũ  ŭ  Ű  ų  ŵ  ��  ��  �  �  �	  �  �  �  �  �  �'  �*  �7  �>  �A  �D  �G  �N  �Q  �T  �V  �X  Ɓ  Ƴ  ��  ��  ��  ��  ��  �  �  �(  �*  �7  �<  �>  �A  �F  �H  �K  �M  �\  �i  �~  ǁ  Ǆ  Ǉ  Ǌ  Ǎ  ǐ  Ǔ  ǖ  Ǚ  ǜ  Ǳ  Ǵ  Ƕ  Ǹ  Ǻ  ǽ  ��  ��  ��  ��  ��  ��  ��  �  �+  �M  �q  ȗ  Ȼ  ��  ��  �  �  �"  �%  �'  �0  �9  �<  �?  �B  �E  �G  �I  �V  �[  �^  �a  �f  �i  �l  �n  ɋ  ɮ  ɻ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �
  �  �  �  �3  �e  ʎ  ʗ  ʤ  ʷ  ʹ  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �$  �9  �<  �?  �B  �E  �H  �K  �N  �Q  �T  �W  �l  �o  �q  �s  �u  �x  �z  �|  �~  ˁ  ˃  ˅  ˢ  ��  ��  �  �+  �Q  �u  ̔  ̳  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �
  �  �  �  �  �  �   �"  �?  �b  �o  �t  �w  �y  �~  ́  ̓  ͅ  ͔  ͗  ͤ  ͫ  ͮ  ͱ  ʹ  ͻ  ;  ��  ��  ��  ��  �   �A  �J  �W  �j  �l  �u  ΂  Ε  Η  Τ  Ω  Ϋ  ή  γ  ε  θ  κ  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �	  �  �!  �#  �%  �'  �*  �,  �.  �0  �3  �5  �7  �T  �s  ϗ  Ϲ  ��  �  �'  �F  �e  Ђ  Ћ  Ў  Б  Г  М  Х  Ш  Ы  Ю  б  г  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �%  �*  �,  �/  �4  �6  �9  �;  �J  �M  �Z  �a  �d  �g  �j  �q  �t  �w  �y  �{  Ѥ  ��  ��  �   �  �   �"  �+  �8  �K  �M  �Z  �_  �a  �d  �i  �k  �n  �p  �  Ҍ  ҡ  Ҥ  ҧ  Ҫ  ҭ  Ұ  ҳ  Ҷ  ҹ  Ҽ  ҿ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �*  �N  �p  Ӕ  Ӻ  ��  ��  �  �9  �B  �E  �H  �J  �S  �\  �_  �b  �e  �h  �j  �l  �y  �~  ԁ  Ԅ  ԉ  Ԍ  ԏ  ԑ  Ԯ  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �   �#  �*  �-  �0  �2  �4  �]  Տ  հ  չ  ��  ��  ��  ��  ��  �  �  �  �  �  �  �"  �$  �'  �)  �8  �E  �Z  �]  �`  �c  �f  �i  �l  �o  �r  �u  �x  ֍  ֐  ֒  ֔  ֖  ֙  ֜  ֞  ֠  ֣  ֥  ֧  ��  ��  �  �)  �M  �s  ח  ׶  ��  ��  ��  ��  �  �  �  �  �  �  �  �!  �#  �%  �'  �4  �9  �<  �?  �D  �G  �J  �L  �o  ،  ؙ  ؠ  أ  ئ  ة  ذ  س  ض  ظ  غ  ��  �  �6  �?  �L  �_  �a  �j  �w  ي  ٌ  ٙ  ٞ  ١  ٣  ٨  ٫  ٭  ٯ  پ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �!  �$  �'  �*  �-  �0  �3  �H  �K  �M  �O  �Q  �T  �W  �Y  �[  �^  �`  �b  �  ڞ  ��  ��  �  �.  �R  �q  ې  ۭ  ۶  ۹  ۼ  ۾  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �"  �?  �L  �S  �V  �Y  �\  �c  �f  �i  �k  �m  ܖ  ��  ��  ��  ��  �  �  �  �*  �=  �?  �L  �Q  �S  �V  �[  �]  �`  �b  �q  �s  ݀  ݅  ݇  ݊  ݏ  ݑ  ݔ  ݖ  ݥ  ݲ  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �	  �  �  �  �  �  �1  �P  �t  ޖ  ޺  ��  �  �#  �B  �_  �h  �k  �n  �p  �y  ߂  ߅  ߇  ߊ  ߌ  ߎ  ߐ  ߝ  ߢ  ߥ  ߨ  ߭  ߰  ߳  ߵ  ��  ��  �  �	  �  �  �  �  �  �  �!  �#  �L  �~  ��  �  �  ��  ��  ��  ��  ��  ��  �  �  �	  �  �  �  �  �  �'  �*  �7  �<  �>  �A  �F  �H  �K  �M  �\  �i  �~  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  �  �*  �L  �p  �  �  ��  ��  �  �  �!  �$  �&  �/  �8  �:  �<  �>  �@  �B  �O  �T  �W  �Z  �_  �b  �e  �g  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �
  �3  �e  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �0  �3  �6  �9  �<  �?  �B  �E  �H  �K  �N  �c  �f  �h  �j  �l  �o  �r  �t  �v  �y  �{  �}  �  �  ��  ��  �#  �I  �m  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �   �=  �`  �m  �r  �t  �w  �|  �~  �  �  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  �  �?  �H  �U  �h  �j  �s  �  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �!  �#  �%  �(  �+  �-  �/  �2  �4  �6  �S  �r  �  �  ��  �  �&  �E  �d  �  �  �  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  �  �   �%  �'  �*  �/  �1  �4  �6  �E  �H  �U  �\  �_  �b  �e  �l  �o  �r  �t  �v  �  ��  ��  ��  �  �  �  �&  �3  �F  �H  �U  �Z  �\  �_  �d  �f  �i  �k  �z  �  �  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �%  �I  �k  �  �  ��  ��  �  �4  �=  �@  �C  �E  �N  �W  �Z  �]  �`  �c  �e  �g  �i  �k  �x  �}  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �"  �)  �,  �/  �1  �3  �\  �  �  �  ��  ��  ��  ��  ��  �  �  �  �  �  �  �!  �#  �&  �(  �7  �D  �Y  �\  �_  �b  �e  �h  �k  �n  �q  �t  �w  ��  ��  �  �  �  �  �  �  �  �  �  �  ��  ��  �  �(  �L  �r  �  �  ��  ��  ��  ��  �   �  �  �  �  �  �  �  �  �+  �0  �3  �6  �;  �>  �A  �C  �f  �  �  �  �  �  �  �  �  �  �  �  ��  �  �-  �6  �C  �V  �X  �a  �n  �  �  �  �  �  �  �  �  �  �  �  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �   �#  �&  �)  �,  �C  �F  �H  �J  �L  �O  �R  �U  �W  �Y  �\  �^  �`  �}  ��  ��  ��  �  �,  �W  �{  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �  �  �$  �'  �*  �,  �I  �l  �y  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �L  �U  �b  �u  �w  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �!  �>  �\  �~  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �   �"  �/  �4  �6  �9  �>  �A  �D  �F  �U  �b  �w  �z  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �%  �G  �k  ��  ��  ��  ��  �  �  �  �  �!  �*  �3  �6  �9  �<  �?  �A  �C  �P  �U  �X  �[  �`  �c  �f  �h  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �1  �R  �[  �h  �{  �}  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �  �  �  �  �  �$  �9  �<  �?  �B  �E  �H  �K  �N  �Q  �T  �W  �l  �o  �q  �s  �u  �x  �{  �}  �  ��  ��  ��  ��  ��  ��  �  �,  �R  �v  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  �   �  �  �  �  �  �   �#  �%  �H  �e  �r  �y  �|  �  ��  ��  ��  ��  ��  ��  ��  ��      %  8  :  C  P  c  e  r  w  z  |  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �     	  ! # % ' * , . 0 3 5 7 T s � � �  ' F e � � � � � � � � � � � � � � � � � � � � �  ! & ) + 0 3 5 7 F I V ] ` c f m p s u w � � � � 	   ' 4 G I V [ ] ` e g j l { � � � � � � � � � � � � � � � � � � � � � � � �  ' K m � � � �  6 ? B E G P Y \ _ b e g i k x } � � � � � � � � � � � � � � � � � � ' Y z � � � � � � � � � � � � � � � �      " ' ) , . = J _ b e h k n q t w z } � � � � � � � � � � � � � � 	 	- 	Q 	w 	� 	� 	� 	� 	� 
 
 
 
 
 
 
 
" 
% 
' 
4 
9 
< 
? 
D 
G 
J 
L 
o 
� 
� 
� 
� 
� 
� 
� 
� 
� 
� 
� 
�  6 ? L _ a j w � � � � � � � � � � � � � � � � � � � � �      ! $ ' * - 0 E H J L N Q S U W Z \ ^ { � � �  * N m � � � � � � � � � � � � � � � � � � � � �  ; H M O R W Z ] _ n { � � � � � � � � � � �  ! . A C L Y l n { � � � � � � � � � � � � � � � � � � � � � � � � �    
    . M q � � �    ? \ e h k m v  � � � � � � � � � � � � � � � �           " K } � � � � � � � � �   	      & ) 6 ; = @ E G J L [ h } � � � � � � � � � � � � � � � � � � � � � � �  + M q � � � �   " % ' 0 9 < ? B E G I V [ ^ a f i l n � � � � � � � � � � � �  7 X a n � � � � � � � � � � � � � � � � � � � �   	    * ? B E H K N Q T W Z ] r u w y { ~ � � � � � � � � �  3 Y } � � � � � � � � � �          % ( + - J m z  � � � � � � � � � � � � � � � � � � � + L U b u w � � � � � � � � � � � � � � � � � �    
     + . 0 2 4 7 : = ? B D F c � � � �  6 U t � � � � � � � � � � � � � � � � � � � � � �  + 8 = ? B G I L N ] ` m t w z } � � � � � � � 
    3 5 > K ^ ` i k x }  � � � � � � � � � � � � � � � � � � � � � � � �     
  ) H l � � � �    :  W  `  c  f  h  q  z  }  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  �  � ! ! ! ! ! ! ! !# !& !3 !: != !@ !C !J !M !P !R !T !} !� !� !� !� !� !� " " "$ "& "3 "8 ": "= "B "D "G "I "X "e "z "} "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� "� # #' #I #m #� #� #� #� $ $ $ $! $# $, $5 $8 $; $> $A $C $E $R $W $Z $] $b $e $h $j $� $� $� $� $� $� $� $� $� $� $� $� % %3 %T %] %j %} % %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� %� &  & & & &3 &6 &9 &< &? &B &E &H &K &N &Q &f &i &k &m &o &r &u &w &y &| &~ &� &� &� &� ' '& 'L 'p '� '� '� '� '� '� '� '� '� '� '� '� '� '� '� (  ( ( ( ( ( (  (# (% (B (e (r (w (y (| (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� (� )  )A )J )W )j )l )u )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� )� *  * * *	 * *! *# *% *' ** *- */ *1 *4 *6 *8 *U *t *� *� *� + +( +G +f +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� +� , ,' ,, ,/ ,1 ,6 ,9 ,; ,= ,L ,O ,\ ,c ,f ,i ,l ,s ,v ,y ,{ ,} ,� ,� ,� - - -" -$ -- -: -M -O -\ -a -c -f -k -m -p -r -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� -� . ., .P .r .� .� .� .� / /; /D /G /J /L /U /^ /` /b /d /f /h /u /z /} /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� /� 0 0 0 0 0 0 0% 0( 0+ 0- 0/ 0X 0� 0� 0� 0� 0� 0� 0� 0� 0� 1 1 1 1 1 1 1 1" 1$ 13 1@ 1U 1X 1[ 1^ 1a 1d 1g 1j 1m 1p 1s 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 1� 2 2# 2G 2m 2� 2� 2� 2� 2� 2� 2� 2� 3 3 3 3 3 3 3 3& 3+ 3. 31 36 39 3< 3> 3[ 3~ 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 3� 4
 4< 4] 4f 4s 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 4� 5 5
 5 5 5 5 5 5 5 5" 5% 5: 5= 5? 5A 5C 5F 5I 5K 5M 5P 5R 5T 5q 5� 5� 5� 5� 6  6D 6c 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 6� 7 71 7> 7E 7H 7K 7N 7U 7X 7[ 7] 7_ 7� 7� 7� 7� 7� 8 8 8 8 8/ 81 8> 8C 8F 8H 8M 8O 8Q 8S 8b 8o 8t 8v 8y 8~ 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 8� 9 9 9 9	 9 9 9* 9I 9m 9� 9� 9� : :( :G :f :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� :� ; ;( ;- ;/ ;2 ;7 ;9 ;< ;> ;M ;P ;] ;d ;g ;j ;m ;t ;w ;z ;| ;~ ;� ;� ;� < < <# <% <. <; <N <P <] <j <m <p <s <v <y <| <� <� <� <� <� <� <� <� <� <� <� =
 =& =H =U =Z =] =` =e =h =k =m =z =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� =� > >
 > > > > > > > >" >% >: >= >? >A >C >F >I >L >N >Q >S >U >r >� >� >� >� ?! ?E ?d ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� ?� @ @7 @D @K @N @Q @T @[ @^ @a @c @e @� @� @� @� @� A
 A A A" A5 A7 AD AI AL AN AS AV AX AZ Ai Al An A{ A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� A� B B B B	 B B B B- BL Bp B� B� B� C  C C> C[ Cd Cg Cj Cl Cu C~ C� C� C� C� C� C� C� C� C� C� C� C� C� C� C� C� C� D D	 D D D D D D D! D# DL D~ D� D� D� D� D� D� D� D� D� E E E	 E E E E E E' E* E, E9 E> E@ EC EH EJ EM EO E^ Ek E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� E� F
 F. FP Ft F� F� F� F� G G" G% G( G* G3 G< G? GA GD GF GH GJ GW G\ G_ Gb Gg Gj Gm Go G� G� G� G� G� G� G� G� G� G� G� G� G� G� G� G� H H H H H H H; Hm H� H� H� H� H� H� H� H� H� H� H� H� H� H� I I I I	 I I% I: I= I@ IC IF II IL IO IR IU IX Im Ip Ir It Iv Iy I| I~ I� I� I� I� I� I� I� J	 J- JS Jw J� J� J� J� J� J� J� J� J� J� J� J� K K K K K K K  K# K& K( KE Kh Ku Kz K} K K� K� K� K� K� K� K� K� K� K� K� K� K� K� K� K� K� L& LG LP L] Lp Lr L{ L� L� L� L� L� L� L� L� L� L� L� L� L� L� L� L� L� L� M  M M M	 M M M$ M' M) M+ M- M0 M2 M4 M6 M9 M; M= MZ My M� M� M� N	 N- NL Nk N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� N� O O' O, O. O1 O6 O9 O< O> OM OZ Oa Od Og Oj Oq Ot Ow Oy O{ O� O� O� P  P P  P" P+ P8 PK PM PZ P_ Pa Pd Pi Pl Po Pq P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� P� Q Q, QP Qr Q� Q� Q� Q� R R; RD RG RJ RL RU R^ Ra Rd Rg Ri Rk Rm Rz R R� R� R� R� R� R� R� R� R� R� R� R� R� R� R� R� S S S S S S! S$ S+ S. S1 S3 S5 S^ S� S� S� S� S� S� S� S� T T T	 T T T T  T% T' T* T, T; TH T] T` Tc Tf Ti Tl To Tr Tu Tx T{ T� T� T� T� T� T� T� T� T� T� T� T� T� T� U
 U, UP Uv U� U� U� U� U� V V V V V V V V! V$ V& V( V5 V: V= V@ VE VH VK VM Vp V� V� V� V� V� V� V� V� V� V� V� V� W W7 W@ WM W` Wb Wk Wx W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� W� X X X X X X" X% X( X+ X. X1 X4 XI XL XN XP XR XU XW XY X[ X^ X` Xb X X� X� X� Y Y. YR Yq Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Y� Z" Z? ZL ZS ZV ZY Z\ Zc Zf Zi Zk Zm Z� Z� Z� Z� Z� [ [ [ [* [= [? [L [Q [S [V [[ [] [` [b [q [~ [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� [� \ \ \ \ \
 \ \ \ \. \M \q \� \� \� ] ]  ]? ]\ ]e ]h ]k ]m ]v ] ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ]� ^ ^ ^ ^ ^ ^ ^ ^ ^ ^E ^w ^� ^� ^� ^� ^� ^� ^� ^� ^� ^� _  _ _ _
 _ _ _ _  _# _0 _5 _7 _: _? _A _D _F _U _b _w _z _} _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� _� `  `$ `F `j `� `� `� `� a a a a a  a) a2 a5 a7 a: a< a> aK aP aS aV a[ a^ aa ac a� a� a� a� a� a� a� a� a� a� a� a� a� a� a� a� a� a� b  b b b b0 bb b� b� b� b� b� b� b� b� b� b� b� b� b� b� b� b� b� c c c. c1 c4 c7 c: c= c@ cC cF cI cL ca cd cf ch cj cm cp cs cu cx cz c| c� c� c� c� d" dH dl d� d� d� d� d� d� d� d� d� d� d� d� d� d� d� d� e e e e e e e e  e= e` em er eu ew e| e e� e� e� e� e� e� e� e� e� e� e� e� e� e� e� f f? fH fU fh fj fs f� f� f� f� f� f� f� f� f� f� f� f� f� f� f� f� f� f� f� g g g g
 g g g g( g+ g- g/ g1 g4 g7 g: g< g? gA gC g` g g� g� g� h h3 hR hq h� h� h� h� h� h� h� h� h� h� h� h� h� h� h� h� h� h� h� h� i i$ i1 i6 i8 i; i@ iB iE iG iV iY if im ip is iv i} i� i� i� i� i� i� j j j j, j. j7 jD jW jY jb jo jt jv jy j~ j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� j� k  k k k> kb k� k� k� k� l l0 lM lV lY l\ l^ lg lp ls lv ly l| l~ l� l� l� l� l� l� l� l� l� l� l� l� l� l� l� m m	 m m m m m< mn m� m� m� m� m� m� m� m� m� m� m� m� m� n n n n n n n' n, n. n1 n6 n8 n; n= nL nY nn nq nt nw nz n} n� n� n� n� n� n� n� n� n� n� n� n� n� n� n� n� n� n� n� o o= oa o� o� o� o� p p p p p p  p) p, p. p1 p3 p5 pB pG pJ pM pR pU pX pZ pw p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� p� q& qX qy q� q� q� q� q� q� q� q� q� q� q� q� q� q� q� q� r r r# r& r) r, r/ r2 r5 r8 r; r> rA rV rY r[ r] r_ rb rd rf rh rk rm ro r� r� r� r� s s; s_ s~ s� s� s� s� s� s� s� s� s� s� s� s� s� s� s� s� s� t t t
 t t) tL tY t^ ta tc th tk tm to t~ t� t� t� t� t� t� t� t� t� t� t� u u( u1 u> uQ uS u\ ui u| u~ u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� u� v v v
 v v v v v v v v v v< v[ v v� v� v� w w. wM wj ws wv wy w{ w� w� w� w� w� w� w� w� w� w� w� w� w� w� w� w� x  x x x x x x x! x# x2 x? xF xI xL xO xV xY x\ x^ x` x� x� x� x� x� y y y y y0 y2 y? yD yF yI yN yP yS yU yd yq y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� y� z z2 zT zx z� z� z� {  { {& {) {, {. {7 {@ {C {F {I {L {N {[ {` {c {f {k {n {q {s {� {� {� {� {� {� {� {� {� {� {� {� |
 |< |] |f |s |� |� |� |� |� |� |� |� |� |� |� |� |� |� |� |� |� |� |� |� } } }	 } } }' }< }? }B }E }H }K }N }Q }T }W }Z }o }r }t }v }x }{ }~ }� }� }� }� }� }� }� }� ~ ~0 ~V ~z ~� ~� ~� ~� ~� ~� ~� ~� ~� ~� ~�         $ ' * , I l y ~ � � � � � � � � � � � � � � � � � � � �* �K �T �a �t �v � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � �	 � � � � �* �- �/ �1 �3 �6 �8 �: �< �? �A �C �` � �� �� �� � �3 �R �q �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �" �/ �6 �9 �< �? �F �I �L �N �P �y �� �� �� �� �� �� �  � �  �" �/ �4 �6 �9 �> �@ �C �E �T �W �d �i �k �n �s �u �x �z �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �4 �X �z �� �� �� � �& �C �L �O �R �T �] �f �i �k �n �p �r �t �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � �0 �b �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �  �" �% �* �, �/ �1 �@ �M �b �e �h �k �n �q �t �w �z �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �0 �T �z �� �� �� �� � � � �
 � � � �" �% �( �* �7 �< �? �B �G �J �M �O �l �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �C �l �u �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � �  �# �& �) �, �/ �2 �5 �J �M �O �Q �S �V �X �Z �\ �_ �a �c �� �� �� �� �	 �/ �S �r �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � �@ �M �R �T �W �\ �^ �a �c �r � �� �� �� �� �� �� �� �� �� �� �� � �% �2 �E �G �P �] �p �r � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � �	 � � � � �/ �N �r �� �� �� � �! �@ �] �f �i �l �n �w �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �	 � � � � � � �> �p �� �� �� �� �� �� �� �� �� �� � � � � � � � �" �% �2 �7 �9 �< �A �D �G �I �X �e �z �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �( �J �n �� �� �� �� � � � �" �$ �- �6 �9 �< �? �B �D �Q �V �Y �\ �a �d �g �i �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �	 �2 �d �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � �1 �4 �7 �: �= �@ �C �F �I �L �O �d �g �i �k �m �p �s �u �w �z �| �~ �� �� �� �  �$ �J �n �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � �B �_ �l �s �v �y �| �� �� �� �� �� �� �� �	 � � �2 �4 �= �J �] �_ �l �q �s �v �{ �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � � �  �" �$ �' �* �, �. �1 �3 �5 �R �q �� �� �� � �% �D �c �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �! �( �+ �. �1 �8 �; �> �@ �B �k �� �� �� �� �� �� �� �� � � �! �& �( �+ �0 �2 �5 �7 �F �I �V �[ �] �` �e �g �j �l �{ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �% �I �k �� �� �� �� � �4 �= �@ �C �E �N �W �Y �[ �^ �` �b �d �q �v �y �| �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  �R �s �| �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � �- �: �O �R �U �X �[ �^ �a �d �g �j �m �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �C �i �� �� �� �� �� �� �� �� � � � � � � � � �) �. �1 �4 �9 �< �? �A �d �� �� �� �� �� �� �� �� �� �� �� �� �
 �+ �4 �A �T �V �_ �l � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � �! �$ �' �* �- �B �E �G �I �K �N �Q �S �U �X �Z �\ �y �� �� �� � �( �L �k �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �= �J �O �Q �T �Y �[ �^ �` �o �| �� �� �� �� �� �� �� �� �� �� �� � �" �/ �B �D �M �Z �m �o �x �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � � � � �7 �V �z �� �� �� �
 �) �H �e �n �q �t �v � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � �- �: �A �D �G �J �Q �T �W �Y �[ �� �� �� �� �� �  � � � �+ �- �6 �8 �E �J �L �O �T �V �Y �[ �j �w �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �9 �[ � �� �� �� � �$ �- �0 �3 �5 �> �G �I �K �M �O �Q �^ �c �f �i �n �q �t �v �� �� �� �� �� �� �� �� �� �� �� �� � �? �` �i �v �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �	 � � �' �< �? �B �E �H �K �N �Q �T �W �Z �o �r �t �v �x �{ �} � �� �� �� �� �� �� �� �
 �. �T �x �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � � � �  �# �% �B �e �r �w �z �| �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  �A �J �W �j �l �u �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � �	 � �! �# �% �' �* �, �. �0 �3 �5 �7 �T �s �� �� �� � �' �F �e �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �! �( �+ �. �1 �8 �; �> �@ �B �k �� �� �� �� �� �� �� �� � � �! �& �) �+ �0 �3 �5 �7 �F �S �X �Z �] �b �d �g �i �x �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �# �G �i �� �� �� �� � �2 �; �> �A �C �L �U �W �Y �[ �] �_ �l �q �t �w �| � �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �M �n �w �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �
 � � � � � �( �5 �J �M �P �S �V �Y �\ �_ �b �e �h �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �> �d �� �� �� �� �� �� �� �� �� � �	 � � � � � �# �( �+ �. �3 �6 �9 �; �X �{ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �9 �Z �c �p �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � �" �% �( �+ �@ �C �E �G �I �L �O �R �T �W �Y �[ �x �� �� �� � �' �K �j �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �8 �E �L �O �R �U �\ �_ �b �d �f  �� �� �� �� � � � �# �6 �8 �E �J �M �O �T �W �Y �[ �j �w �| �~ Á Æ È Ë Í Ü é þ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � �
 � �) �H �l Ď Ĳ �� �� � �: �W �` �c �f �h �q �z �} ŀ Ń Ņ Ň ŉ Ŗ ś Ş š Ŧ ũ Ŭ Ů �� �� �� �  � � �
 � � � �  �# �0 �7 �: �= �@ �G �J �M �O �Q �z Ƭ �� �� �� �� �� � � �! �# �% �2 �7 �9 �< �A �C �F �H �W �d �y �| � ǂ ǅ ǈ ǋ ǎ Ǒ ǔ Ǘ Ǭ ǯ Ǳ ǳ ǵ Ǹ Ǻ Ǽ Ǿ �� �� �� �� � �% �G �k ȑ ȵ �� �� � � � � �! �* �3 �5 �7 �9 �; �= �J �O �R �U �Z �] �` �b Ʌ ɢ ɯ ɶ ɹ ɼ ɿ �� �� �� �� �� �� �+ �L �U �b �u �w ʀ ʍ ʠ ʢ ʯ ʴ ʷ ʹ ʾ �� �� �� �� �� �� �� �� �� �� �� �� � � �( �+ �. �1 �4 �7 �: �= �@ �C �F �[ �^ �` �b �d �g �j �m �o �r �t �v ˓ ˲ �� �� � �B �f ̅ ̤ �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � �> �[ �h �o �r �u �x � ͂ ͅ ͇ ͉ Ͳ �� � � � �. �0 �9 �F �Y �[ �h �m �o �r �w �y �| �~ ΍ ΐ Ν ΢ Τ Χ ά ή α γ �� �� �� �� �� �� �� �� �� �� �� �� � � � � � �  �# �& �) �+ �. �0 �2 �O �n ϒ ϴ �� �� �" �A �` �} І Љ Ќ Ў З Р У Ц Щ Ь Ю а н �� �� �� �� �� �� �� �� � �" �) �, �/ �2 �9 �< �? �A �C �l ў ѿ �� �� �� �� �� �  � � �" �' �) �, �1 �3 �6 �8 �G �J �L �Y �^ �` �c �h �j �m �o �~ ҋ Ҡ ң Ҧ ҩ Ҭ ү Ҳ ҵ Ҹ һ Ҿ �� �� �� �� �� �� �� �� �� �� �� �� �
 �) �M �o ӓ ӹ �� �� � �8 �A �D �G �I �R �[ �^ �a �d �f �h �u �z �} Ԁ ԅ Ԉ ԋ ԍ ԰ �� �� �� �� �� �� �� �� �� �� �� �$ �V �w Հ Ս ՠ բ ի ո �� �� �� �� �� �� �� �� �� �� �� � � � � � � �  �# �% �4 �A �V �Y �\ �_ �b �e �h �k �n �q �t ։ ֌ ֎ ֐ ֒ ֕ ֘ ֚ ֜ ֟ ֡ ֣ �� �� � �% �I �o ד ײ �� �� �� �� �� �� � � � � � � � �( �- �0 �3 �8 �; �> �@ �] ؀ ؍ ؒ ؕ ؗ ؜ ؟ ء أ ز ؿ �� �� �� �� �� �� �� �� �� �	 �; �\ �e �r م ه ِ ٝ ٰ ٲ ٿ �� �� �� �� �� �� �� �� �� � �	 � � � � � � � �! �$ �9 �< �> �@ �B �E �H �K �M �P �R �T �q ڐ ڴ �� �� �  �D �c ۂ ۟ ۨ ۫ ۮ ۰ ۹ �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �1 �> �E �H �K �N �U �X �[ �] �_ ܈ ܺ �� �� �� � � � � �/ �1 �> �C �E �H �M �O �R �T �c �l �y �~ ݀ ݃ ݈ ݊ ݍ ݏ ݞ ݫ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �
 � � �+ �J �n ސ ޴ �� �� � �< �Y �b �e �h �j �s �| �~ ߀ ߂ ߄ ߆ ߓ ߘ ߛ ߞ ߣ ߦ ߩ ߫ �� �� �� �� �� � � �
 � � � �+ �2 �5 �8 �; �B �D �G �J �L �m �� �� �� �� �� �� �� �	 � � �' �4 �9 �; �> �C �F �I �K �Z �g �j �m �p �s �u � � � � � �� �� �� �� �� �� �� �� �� �� �� �� � � � �  �# �& �( �5 �7 �9 �< � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �  �3 �N �\ �o � � � � � � �� �� �� � �	 � � � � �8 �X �a �n �o �p �r � � � � � � � � � � � � � �� �� �� �� �� �� �� �� �� �� �� � � � �
 � � � � �  � � � � � �� � �	 � � �e �r �y �| � � � � � � � �� �� � �$ �' �* �, �= �G �J �M �P �S �� �� �� �� �� �� �� �� �� �� �� �� � � �( �H �O �o � � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � �2 �` � � �� � �B �Y �f �g �h �j � � � � � � � � � � � � � �� �� �� �� �� �& �/ �< �A �D �G �L �O �R �T �\ �h � � � � � � � � � � � � � �� �� �� �� �� �( �W � � � �� �� �� �� �� �� �� �� �� �D �M �Z �a �d �g �j �q �t �w �z �| � � �� �� �� �� �� �� �� �� �� �� �  � � � � � � � � �! �# �H �d � � �� �� �  � � �	 � � � �` �i �{ � � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �
 � � � � � � � �" �% �( �+ �. �1 �4 �7 �: �= �@ �C �F �I �L �O �R �U �X �[ �^ �a �d �g �j �m �p �s �v �y �| � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � �	 � � � � � � � �! �$ �' �* �- �0 �3 �6 �9 �< �? �B �E �H �K �N �Q �T �W �Z �] �` �c �f �i �l �o �r �u �x �{ �~ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � � � �  �# �& �) �, �/ �2 �5 �8 �; �> �A �D �G �J �M �P �S �V �Y �\ �_ �b �e �h �k �n �q �t �w �z �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �
 � � � � � � � �" �% �( �+ �. �1 �4 �7 �: �= �@ �C �F �I �L �O �R �U �X �[ �^ �a �d �g �j �m �p �s �v �y �| � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � �	 � � � � � � � �! �$ �' �* �- �0 �3 �6 �9 �< �? �B �E �H �K �N �Q �T �W �Z �] �` �c �f �i �l �o �r �u �x �{ �~ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � � � � �  �# �& �) �, �/ �2 �5 �8 �; �> �A �D �G �J �M �P �S �V �Y �\ �_ �b �e �h �k �n �q �t �w �z �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �  �) �, �F �O �R �s �| � �� �� �� �� �� �� � � �" �H �Q �T �� �� �� �� �� �� �� �� �� � � � �F �O �R �� �� �� �� �� �� �
 � � �7 �@ �C �a �j �m �� �� �� �� �� �� �� � � �) �2 �5 �c �l �o �� �� �� �� �� �� � � � �E �N �Q �~ �� �� �� �� ��  
      A  J  M  �  �  �  �  �  � 	   / 8 ; Z c f � � � � � � � � �    F O R u ~ � � � � � � �     = F I f o r � � � � � � � � �  # & B K N n w z � � � � � � �    ) 2 5 _ h k � � � � � � �    , 5 8 ] f i � � � � � �  ' * c l o � � �    ? H K � � � � � � 	 	 	 	[ 	d 	g 	� 	� 	� 	� 	� 	� 
* 
3 
6 
n 
w 
z 
� 
� 
�    R [ ^ � � � �   P Y \ � � � � � � 0 9 < � � � � � � $ - 0 m v y � � �    ] f i � � � �   ] f i � � � �   D M P � � � � � � / 8 ; � � � � � � 5 > A � � � � � � # , / l u x � � �    g p s � � � 
   Y b e � � � �   D M P � � � � � � + 4 7 � � � � � � - 6 9 | � � � � �    U ^ a � � � � � �  ' * o x { � � �  & ) g p s � � �    V _ b � � � 
   S \ _ � � � � � � / 8 ; ~ � � � � � � �       )  ,  J  S  V  w  �  �  �  �  �  �  �  � ! ! !" !N !W !Z !� !� !� !� !� !� " "
 " "9 "B "E "s "| " "� "� "� "� "� "� # # # #< #E #H #k #t #w #� #� #� #� #� #� $ $ $ $: $C $F $j $s $v $� $� $� $� $� $� % % % %^ %g %j %� %� %� %� %� %� & & & &K &T &W &� &� &� &� &� &� &� &� &� ' '' '* 'U '^ 'a '� '� '� '� '� '� ( ( (! (N (W (Z (� (� (� (� (� (� ) ) ) )C )L )O )w )� )� )� )� )� )� )� )� *  *) *, *I *R *U *� *� *� *� *� *� *� *� *� + + +! +E +N +Q +q +z +} +� +� +� +� +� +� ,	 , , ,; ,D ,G ,x ,� ,� ,� ,� ,� ,� ,� ,� - - - -7 -@ -C -m -v -y -� -� -� -� -� -� . . . .6 .? .B .] .f .i .� .� .� .� .� .� .� / / /( /1 /4 /^ /g /j /� /� /� /� /� /� /� /� 0 0# 0, 0/ 0n 0w 0z 0� 0� 0� 0� 0� 1  1, 15 18 1W 1` 1c 1� 1� 1� 1� 1� 1� 1� 1� 1� 2" 2+ 2. 2J 2S 2V 2s 2| 2 2� 2� 2� 2� 2� 2� 3 3
 3 32 3; 3> 3a 3j 3m 3� 3� 3� 3� 3� 3� 3� 3� 4 4, 45 48 4h 4q 4t 4� 4� 4� 4� 4� 4� 5 5 5 5L 5U 5X 5� 5� 5� 5� 5� 5� 5� 6 6 66 6? 6B 6q 6z 6} 6� 6� 6� 6� 6� 6� 7 7' 7* 7N 7W 7Z 7� 7� 7� 7� 7� 7� 7� 7� 7� 8 8 8! 8E 8N 8Q 8x 8� 8� 8� 8� 8� 8� 8� 8� 9 9 9 95 9> 9A 9d 9m 9p 9� 9� 9� 9� 9� 9� : :$ :' :f :o :r :� :� :� :� :� :� ;> ;G ;J ;� ;� ;� ;� ;� ;� < <! <$ <f <o <r <� <� <� <� <� <� =& =/ =2 =h =q =t =� =� =� =� =� =� >/ >8 >; >m >v >y >� >� >� >� ? ? ?A ?J ?M ?� ?� ?� ?� ?� ?� @  @	 @ @> @G @J @v @ @� @� @� @� @� @� A A0 A9 A< A~ A� A� A� A� A� B B B BG BP BS B� B� B� B� B� B� C  C	 C C> CG CJ C� C� C� C� C� C� D D( D+ De Dn Dq D� D� D� E E E Ea Ej Em E� E� E� E� E� F  F; FD FG F� F� F� F� F� F� G+ G4 G7 Gu G~ G� G� G� G� H H H Hc Hl Ho H� H� H� I1 I: I= I} I� I� I� I� I� J J J Je Jn Jq J� J� J� K K  K# Kq Kz K} K� K� K� L L L! Lm Lv Ly L� L� L� M M M MY Mb Me M� M� M� N  N	 N NR N[ N^ N� N� N� N� O O OY Ob Oe O� O� O� P P P P] Pf Pi P� P� P� Q Q Q Qh Qq Qt Q� Q� Q� R
 R R RZ Rc Rf R� R� R� S S" S% S} S� S� S� S� S� T' T0 T3 Tz T� T� T� T� T� U! U* U- Un Uw Uz U� U� U� VD VM VP V� V� V� V� V� W WH WQ WT W� W� W� W� W� W� X8 XA XD X� X� X� X� X� X� Y  Y) Y, Yo Yx Y{ Y� Y� Y� Z Z Z ZP ZY Z\ Z� Z� Z� Z� Z� Z� [5 [> [A [| [� [� [� [� [� \ \& \) \e \n \q \� \� \� ] ] ] ]p ]y ]| ]� ]� ]� ^& ^/ ^2 ^} ^� ^� ^� ^� ^� _! _* _- _� _� _� _� _� _� `$ `- `0 `s `| ` `� `� `� a8 aA aD a� a� a� a� b b bD bM bP b� b� b� b� c  c cP cY c\ c� c� c� d d d dN dW dZ d� d� d� d� d� d� eC eL eO e� e� e� f f  f# fv f f� f� f� f� g= gF gI g� g� g� g� g� g� hA hJ hM h� h� h� h� h� h� i, i5 i8 i� i� i� i� i� i� jP jY j\ j� j� j� k k k kl ku kx k� k� k� k� l l lX la ld l� l� l� l� m m mG mP mS m� m� m� m� m� m� n n% n( n\ ne nh n� n� n� n� n� n� oG oP oS o� o� o� o� o� o� p1 p: p= p� p� p� p� p� p� q q$ q' qr q{ q~ q� q� q� r r( r+ r r� r� r� r� r� s5 s> sA s} s� s� s� s� s� t. t7 t: t� t� t� t� t� t� u7 u@ uC u� u� u� u� u� u� v& v/ v2 v� v� v� v� v� v� w. w7 w: w� w� w� w� w� w� x5 x> xA x� x� x� x� x� y  yV y_ yb y� y� y� z z z zo zx z{ z� z� z� {% {. {1 {� {� {� {� {� {� |1 |: |= |� |� |� |� |� |� } } } }U }^ }a }� }� }� }� }� }� ~ ~ ~ ~V ~_ ~b ~� ~� ~� ~� ~� ~� " + . p y | � � � � � � �D �M �P �� �� �� �� �� �� � � � �T �] �` �� �� �� � � �! �y �� �� �� �� �� �E �N �Q �� �� �� � � � �r �{ �~ �� �� �� �/ �8 �; �� �� �� �� �� �� �3 �< �? �} �� �� �� �� �� �� � � � �	 � � � � �" �$ �2 �> �C �F �O �V �[ �d �g �i �k �x �{ �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �G �T �Y �\ �_ �d �g �j �l �x �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �R �[ �^ �� �� �� �� �� �� �� �� �� � �
 � �, �/ �2 �5 �8 �; �> �A �D �G �J �_ �a �d �f �h �k �n �q �t �w �z �| �� �� �� �� �� �� � � �. �F �X �j �� �� �� �� �� �� �� �� �� �� �  � � �	 � � � � � �  �9 �T �o �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �' �U �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �
 � � � � � �; �` �� �� �� �� �� �� �� � � � � �" �% �( �+ �. �1 �B �D �G �I �K �M �P �S �U �W �o �~ �� �� �� � �V �q �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �" �6 �I �] �j �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �1 �C �i �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � �- �F �a �| �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �Z �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �	 �1 �T �w �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �5 �B �E �H �K �N �P �k �� �� � � � � � � �. �7 �T �W �Z �� �� �� �� �� �� �� �� �� � � � �� �� �� �� �� �� �� �� �� �� �� � � � � � � � �" �% �' �F �[ �g � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �
 � �' �? �H �i �n �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �, �G �T �W �Z �] �` �b �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � �	 �$ �> �X �s �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  �F �g � �� �� � � �& �' �* �3 �Z �] �` �c �f �i �l �o �r �u �x �{ �~ �� �� �� �� �� �� �� �� �� �� � �  �, �? �f �� �� �� �� �� �� �& �M �Y �� �� �� �� �� �� �� �� �� �� �� �� � � �  �# �, �9 �< �? �B �D �F �d �q �v �y �| �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � �  �" �% �( �+ �. �1 �4 �7 �: �= �? �b �� �� �� �� �� � �# �F �Z �i �v �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �	 � � �* �@ �N �Z �z �| �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �( �D �h �z �� �� �� �� �� �� �� �� �� �� �� �� � � �( �@ �S �o �r �u �x �{ �~ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � �# �B �G �f �s �v �x �{ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  � � � � �� �� �� �� �� �� �� �� �� �d �m �y �� �� �� �� �� �� � � �H �U �j �m �p �s �v �y �| �       ¢ ¤ ¦ © ¬ ® ° ³ µ · �� �� � �9 �] Ã ç �� �� � � � � � � �% �( �* �- �/ �1 �> �C �F �I �N �Q �T �V �s Ė ģ Ĩ ī ĭ Ĳ ĵ ķ Ĺ �� �� �� �� �� �� �� �� �� �� �� � �Q �r �{ ň ś ŝ Ŧ ų �� �� �� �� �� �� �� �� �� �� �� � � � �6 �W �Z �] �` �c �f �i �k �n Ƈ Ɗ ƍ Ɛ Ɠ Ɩ Ƙ ơ Ʈ Ʊ ƴ Ʒ ƺ ƽ �� �� �� �� �� �� �� �� �� �� � � � � � � �* �- �0 �3 �@ �C �E �H �U �X �Z �] �i �v �y �| �~ � � �- �0 �3 �6 �9 �< �? �B �E �H �K �` �c �e �g �i �l �o �q �s �v �x �z ȗ ȶ �� �� �  �F �j ɉ ɨ �� �� �� �� �� �� �� �� �� �� �� �� � � �	 � � � � � �< �Y �f �m �p �s �v �} ʀ ʃ ʅ ʇ ʰ �� � � � �, �. �7 �D �W �Y �f �k �m �p �u �w �z �| ˋ ˘ ˝ ˟ ˢ ˧ ˩ ˬ ˮ ˽ �� �� �� �� �� �� �� �� �� � � � � � � � �( �5 �8 �; �> �A �D �G �I �V �Y �[ �^ �q �~ ́ ̄ ̇ ̌ ̙ ̜ ̞ ̡ ̮ ̱ ̴ ̷ �� �� �� �� �� �� �� �� �� �� �� �  � ͋ ͘ ͭ Ͱ ͳ Ͷ ͹ ͼ Ϳ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �7 �[ �} Ρ �� �� �
 �) �F �O �R �U �W �` �i �l �o �r �u �w τ ω ό Ϗ ϔ ϗ Ϛ Ϝ Ϲ �� �� �� �� �� �� �� �� �� � � �" �% �( �+ �2 �5 �8 �: �< �e З и �� �� �� �� �� �� � � � �  �" �% �* �, �/ �1 �@ �U �v �y �| � т х ш ъ э Ѧ ѩ Ѭ ѯ Ѳ ѵ ѷ �� �� �� �� �� �� �� �� �� �� �� � � � � � �, �/ �1 �4 �K �X �[ �^ �` �� �" �/ �D �G �J �M �P �S �V �Y �\ �_ �b �w �z �| �~ Ӏ Ӄ Ӆ Ӈ Ӊ ӌ ӎ Ӑ ӭ �� �� � �6 �\ Ԁ ԟ Ծ �� �� �� �� �� �� �� �  � � � � � � � �  �% �( �+ �- �J �m �z � Ձ Մ Չ Ջ Վ Ր ՟ լ ճ ն չ ռ �� �� �� �� �� �� �( �I �R �_ �r �t �} ֊ ֝ ֟ ֬ ֱ ֳ ֶ ֻ ֽ �� �� �� �� �  � � �	 � � � � � �0 �3 �6 �9 �< �? �A �J �U �X �[ �^ �a �d �f �s �v �y �| ה ס פ צ ש ׮ ׻ ׾ �� �� �� �� �� �� �� �� �� �� �� �� � � � � ؘ إ غ ؽ �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �# �B �f و ٬ �� �� � �4 �Q �Z �] �` �b �k �t �v �x �z �| �~ ڋ ڐ ړ ږ ڛ ڞ ڡ ڣ �� �� �� �� �� �� �  � �
 � � � �: �l ۍ ۖ ۣ ۶ ۸ �� �� �� �� �� �� �� �� �� � � � � �" �' �) �, �1 �3 �6 �8 �G �h �k �n �q �t �w �z �| � ܘ ܛ ܞ ܡ ܤ ܧ ܩ ܲ ܿ �� �� �� �� �� �� �� �� �� �� �� � � � � � �! �. �1 �3 �6 �C �F �H �K �X �[ �] �` �m �p �r �u ݁ ݎ ݑ ݔ ݖ �' �4 �I �L �O �R �U �X �[ �^ �a �d �g �| � ށ ރ ޅ ވ ފ ތ ގ ޑ ޓ ޕ ޲ �� �� � �; �a ߅ ߤ �� �� �� �� �� �� �� � � �	 � � � � �# �& �) �. �1 �4 �6 �S �v �� �� �� �� �� �� �� �� � � � � �� �� �� �� �� �� �� �� �1 �R �[ �h �{ �} � � � � � � � � �� �� �� �� �� �� � � � � � � � � � �8 �; �> �A �D �G �I �R �_ �b �e �h �k �n �q �s � � � � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �  � � � � �! �. �1 �4 �6 �� �� �� �� �� �� �� �� �� �� � � � � � �! �# �% �( �* �, �. �1 �3 �5 �R �q � � �� � �% �D �c � � � � � � � � � � � � � � �� �� �� �� �� �� �� � � �& �) �, �/ �6 �9 �< �> �@ �i � � �� �� �� �� �� �� � � � �$ �' �) �. �1 �3 �5 �D �Q �V �X �[ �` �b �e �g �v � � � � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � � � �/ �< �? �A �D �K �X �[ �] �` �m �p �r �u � � � � � � � � � � � � �� �� �� �� �� �c �p � � � � � � � � � � � � � � � �� �� �� �� �� �� �� �� �� � �1 �S �w � �� �� �� � �% �( �+ �- �6 �? �A �C �E �G �I �V �[ �^ �a �f �i �l �n � � � �� �� �� �� �� �� �� �� �� � �7 �X �a �n � � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � �3 �6 �9 �< �? �B �E �G �J �c �f �i �l �o �r �t �} � � � � � � � � � � � �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � �# �0 �3 �6 �8 � �� �� �� �� �� �� �� �� �� �� �� �� �
 � � � � � � � � �! �# �% �B �a � � �� �� � �4 �S �p �y �| � �� �� � � � � � � � � � � � �� �� �� �� � � � � � �" �% �' �) �8 �E �L �O �R �U �\ �_ �b �d �f � �� �� �� �� � � � �# �6 �8 �E �J �L �O �T �V �Y �[ �j � � � � � � � � � � �� �� �� �� �� �� �� �� �� �� � � � � � � � �  �" �% �3 �@ �C �F �I �V �Y �[ �^ �k �n �q �t � � � � � � � � � � � � � �E �R �g �j �m �p �s �v �y �| � � � �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �7 �[ �� �� �� �� �  �	 � � � � �# �% �' �) �+ �- �: �? �B �E �J �M �P �R �o �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �M �n �w �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � �5 �8 �; �\ �_ �b �e �h �k �n �p �s �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � � � � � �# �& �( �+ �7 �D �G �J �L �Y �n �q �t �w �z �} �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �= �a �� �� �� �� � � � � � �  �) �, �/ �2 �5 �7 �D �I �L �O �T �W �Z �\ �y �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �% �W �x �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �  �
 � �+ �0 �H �Z �s �v �y �| � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� �
 � � � � �+ �. �1 �3 �@ �I �L �O �R �U �^ �a �d �g �j �l �u � �� �� �� �� �� �� �� �� �� �� �� �� �� �� �� � �5 �N �[ �l �o �r �u �x �{ �~ �� �� �� �� �� �� �� �� �� �� �� �� ��    2  a  �  �  �  * - 0 3 6 8 L U Z ] ` c p w z } � � � � � � � � � � � � � � � � � � � � � 	     ! # 0 3 6 9 < > R [ ` c f i v } � � � � � � � � � � � � � � � � � � � � � �            <�             �

/== GameCatalog.xcodeproj/project.xcworkspace/xcuserdata/gilangramadhan.xcuserdatad/UserInterfaceState.xcuserstate
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

/== GameCatalog.xcodeproj/xcuserdata/ben.xcuserdatad/xcschemes/xcschememanagement.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>SchemeUserState</key>
	<dict>
		<key>GameCatalog.xcscheme_^#shared#^_</key>
		<dict>
			<key>orderHint</key>
			<integer>0</integer>
		</dict>
		<key>GameCatalog.xcscheme_^#shared#^_</key>
		<dict>
			<key>orderHint</key>
			<integer>0</integer>
		</dict>
	</dict>
</dict>
</plist>


/== GameCatalog.xcodeproj/xcuserdata/gilangramadhan.xcuserdatad/xcdebugger/Breakpoints_v2.xcbkptlist
<?xml version="1.0" encoding="UTF-8"?>
<Bucket
   uuid = "2E0E5556-CFAC-425D-A534-44E4169CD24C"
   type = "1"
   version = "2.0">
</Bucket>


/== GameCatalog.xcodeproj/xcuserdata/gilangramadhan.xcuserdatad/xcschemes/xcschememanagement.plist
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
		<key>GameCatalog.xcscheme_^#shared#^_</key>
		<dict>
			<key>orderHint</key>
			<integer>0</integer>
		</dict>
	</dict>
</dict>
</plist>


/== README.md
# Game Catalog Expert

## Continuous Integration / Continuous Deployment

This project uses Codemagic for CI/CD pipeline. The build status can be seen below:

![Codemagic Build Status](build-codemagic.png)


/== codemagic.yaml
workflows:
  ios-project-debug: # workflow ID
    name: iOS debug # workflow name
    environment:
      xcode: latest
      cocoapods: default
      vars:
          XCODE_PROJECT: "GameCatalog.xcodeproj" # Isikan nama project Anda.
          XCODE_SCHEME: "GameCatalog" # Isikan nama scheme project Anda.
    scripts:
      - name: Run build only (skip tests)
        script: |
          xcodebuild \
          -project "$XCODE_PROJECT" \
          -scheme "$XCODE_SCHEME" \
          -sdk iphonesimulator \
          -destination 'platform=iOS Simulator,name=iPhone 16 Pro,OS=18.2' \
          clean build CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO
      - name: Build debug app
        script: |
          xcodebuild build -project "$XCODE_PROJECT" \
          -scheme "$XCODE_SCHEME" \
          CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED=NO
    artifacts:
      - $HOME/Library/Developer/Xcode/DerivedData/**/Build/**/*.app
    publishing:
      email:
        recipients:
          - dk.harryadi@gmail.com
          
  ios-project-test: # new workflow for testing
    name: iOS Test # workflow name
    environment:
      xcode: latest
      cocoapods: default
      vars:
          XCODE_PROJECT: "GameCatalog.xcodeproj" # Isikan nama project Anda.
          XCODE_SCHEME: "GameCatalogTests" # Test scheme name - adjust if different
    scripts:
      - name: Run tests
        script: |
          xcodebuild \
          -project "$XCODE_PROJECT" \
          -scheme "$XCODE_SCHEME" \
          -sdk iphonesimulator \
          -destination 'platform=iOS Simulator,name=iPhone 16 Pro,OS=18.2' \
          test CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO
    publishing:
      email:
        recipients:
          - dk.harryadi@gmail.com

