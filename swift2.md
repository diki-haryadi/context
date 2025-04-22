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
            let emojiStatus = statusCode >= 200 && statusCode < 300 ? "‚úÖ" : "‚ùå"
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
    
    print("üîç Searching games with query: '\(trimmedQuery)'")
    
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
            print("  ‚îî Underlying error: \(underlyingError)")
        }
        
        if let responseCode = afError.responseCode {
            print("  ‚îî Response code: \(responseCode)")
        }
        
        return NetworkError.serverError
    } else if let decodingError = error as? DecodingError {
        print("\(operation) - Decoding Error: \(decodingError)")
        
        // Provide detailed context for decoding errors
        switch decodingError {
        case .keyNotFound(let key, let context):
            print("  ‚îî Missing key: \(key.stringValue)")
            print("  ‚îî Context: \(context.debugDescription)")
            print("  ‚îî Coding path: \(context.codingPath.map { $0.stringValue })")
        case .typeMismatch(let type, let context):
            print("  ‚îî Type mismatch: expected \(type)")
            print("  ‚îî Context: \(context.debugDescription)")
            print("  ‚îî Coding path: \(context.codingPath.map { $0.stringValue })")
        case .valueNotFound(let type, let context):
            print("  ‚îî Value missing: expected \(type)")
            print("  ‚îî Context: \(context.debugDescription)")
        case .dataCorrupted(let context):
            print("  ‚îî Data corrupted: \(context.debugDescription)")
        @unknown default:
            print("  ‚îî Unknown decoding error")
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
            print("üì∏ Decoded backgroundImage for \(name): \(backgroundImage ?? "nil")")
        } catch {
            print("‚ö†Ô∏è Error decoding backgroundImage for \(name): \(error)")
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
        
        var logMessage = "üì± Response: \(statusCode) for \(url)"
        
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
            
            print("ImageUtility: URL required encoding: \(urlString) ‚Üí \(encodedUrlString)")
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
        print("üßπMemory cache cleared")
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
bplist00‘        
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—  	UStateÄØR      9 : ; < = > ? @ A B C D E F G T Z [ a e f j k o p t u y z ~  É Ñ à â ç é ë £ § • ¶ ß ® © ™ ≤ ∂ ∑ ª º ¬ √ « — ◊ € ﬂ „ Ë Ï Ú Û ˜ ˝  './0189@GOPW^_fgnu{Äòôöõúùûü†°¢¶≠ ŒØ∑∏π¡¬√ƒ«…”‘’÷⁄›·‚ÍÎÏÌµπ∫æø√ƒ»…ÕŒ“”◊ÿ‹›·‚ÊÁÎÏÒıˆ˙˚ˇ 	
"#'(,-1267;<@AEFJKOPTUYZ^_cdhimnrswx|}ÅÇÜáãåêëïñöõü†§•©™ÆØ≥¥∏πΩæ¬√«»ÃÕ—“÷◊€‹‡·ÂÊÍÎÔÙı˘˙˛ˇ	!"&'+,0156:;?@DEIJNOSTXY]^bcghlmÖÜáàâäãåçéèìö¢£§¨≠Æ∏π∫ªø√ƒ≈ÕŒÊÁËÈÍÎÏÌÓÔÙ˚¸˝˛%&'()12JKLMNOPQRSTX_ ‘`aijkuvwx|Äàâäãåîï≠ÆØ∞±≤≥¥µ∂∑ª¬√ÀÃÕ’÷◊·‚„‰ËÏÌıˆ#$%-./789CDEFJNVWopqrstuvwxy}ÑÖçéèóò¢£§•©≠µ∂Œœ–—“”‘’÷◊ÿ‹„‰ÂÌÓÔ˘˙˚¸ 23456789:;<@GHIQRS[\fghimqrs{|îïñóòôöõúùû¢©™´≥¥µΩæø… ÀÃ–‘‹›ıˆ˜¯˘˙˚¸˝˛ˇ
)*+,0456>?WXYZ[\]^_`aeltuv~Ääãåçëïùû∂∑∏π∫ªºΩæø¿ƒÀÃ‘’÷‡·‚„ÁÎÛÙı˝˛											 	$	+	3	4	5	?	@	A	B	F	J	R	S	T	\	]	u	v	w	x	y	z	{	|	}	~		É	ä	í	ì	î	ú	ù	û	®	©	™	´	Ø	≥	ª	º	‘	’	÷	◊	ÿ	Ÿ	⁄	€	‹	›	ﬁ	‚	È	Ò	Ú	Û	˝	˛	ˇ
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
î
ï
ñ
ó
ò
ô
ö
õ
ú
ù
û
¢
©
±
≤
≥
Ω
æ
ø
¿
ƒ
»
–
—
“
⁄
€
Û
Ù
ı
ˆ
˜
¯
˘
˙
˚
¸
˝	
 !%)1234<=UVWXYZ[\]^_cjrst|}~àâäãèìõú¥µ∂∑∏π∫ªºΩæ¬… À”‘’›ﬁﬂÈÍÎÏÙı˝˛ $+345?@ABFJRST\]uvwxyz{|}~Éäíìîûü†°•©±≤≥¥ºΩ’÷◊ÿŸ⁄€‹›ﬁﬂ„ÍÚÛÙ¸˝˛	
456789:;<=>BIJRST\]^hijkos{|îïñóòôöõúùû¢©±≤≥ªºΩ«»… Œ“⁄€ÛÙıˆ˜¯˘˙˚¸˝&'()-19:RSTUVWXYZ[\`ghpqrz{|Üáàâçëôö≤≥¥µ∂∑∏π∫ªº¿«»…—“”›ﬁﬂ‡‰ËÒÚ˙˚!(012<=>?CGOPQYZrstuvwxyz{|Äáàêëíúùûü£ßØ∞±π∫“”‘’÷◊ÿŸ⁄€‹‡ÁÔÒ˘˙˚123456789:;?FGOPQYZ[efghlpxyëíìîïñóòôöõü¶ÆØ∞∏π∫ƒ≈∆«Àœ◊ÿÒÚÛÙıˆ˜¯˘˙˛&'()-19:RSTUVWXYZ[\`gopq{|}~ÇÜéèóò≤≥¥µ∂∑∏π∫ªºΩ¡»–—“⁄€‹ÊÁËÈÌÒ ()ABCDEFGHIJKOVW_`aklmnrv~ÄÅâä¢£§•¶ß®©™´¨∞∑ø¿¡ÀÃÕŒ“÷ﬁﬂÁË 	
'()3456:>FG_`abcdefghimtuv~Ääãåçëïùûüß®¿¡¬√ƒ≈∆«»… Œ’›ﬁﬂÈÍÎÏÙ¸˝ !"#$%&'(,3;<=EFPQRSW[cd|}~ÄÅÇÉÑÖÜäëíöõú¶ß®©≠±π∫ª√ƒ‹›ﬁﬂ‡·‚„‰ÂÊÍÒÚ˙˚¸	$%=>?@ABCDEFGKRZ[\defpqrsw{|ÑÖùûü†°¢£§•¶ß´≤≥¥ºΩæ∆«»“”‘’Ÿ›ﬁﬂÁË 	
 !)*+5678<@HIabcdefghijkovwÄÅãåçéíñûü†®©¡¬√ƒ≈∆«»… Àœ÷◊ÿ‡·‚ÍÎıˆ˜¯¸ 	!"#$%&'()*+/67?@AIJKUVWX\`hiÅÇÉÑÖÜáàâäãèñûü†®©™¥µ∂∑ªø«»‡·‚„‰ÂÊÁËÈÍÓı˝˛ˇ	&'?@ABCDEFGHIMT\]^hijkos{|ÑÖü†°¢£§•¶ß®©™Æµ∂æø¿»… ‘’÷◊€ﬂÔÒÚÛÙı˝˛	*+,-./012348?@HIJTUVW[_ghijrsãåçéèêëíìîïô†°¢™´¨∂∑∏πΩ¡… ÀÃ‘’ÌÓÔÒÚÛÙıˆ˜˚!"#$(,-56NOPQRSTUVWX\cklmuvwÅÇÉÑàåîï≠ÆØ∞±≤≥¥µ∂∑ª¬ ÀÃ‘’ﬂ‡·‚ÊÍÚÛ !)*+345?@ABFJKSTlmnopqrstuvzÅÇäãåñóòôù°©™´≥¥ÃÕŒœ–—“”‘’÷⁄·ÈÍÎıˆ˜¯¸ 	*+,-./012348?GHISTUVZ^fghpqâäãåçéèêëíìóû¶ß®∞±≤ºΩæø√«œ–ËÈÍÎÏÌÓÔÒÚˆ˝˛ˇ	 $()*23KLMNOPQRSTUY`hijrst~ÄÅÖâäíì´¨≠ÆØ∞±≤≥¥µπ¿¡… À’÷◊ÿ‹‡ËÈÍÚÛ ()*234>?@AEIQRjklmnopqrstxáàâëíúùûü£ßØ∞»… ÀÃÕŒœ–—“÷›ÂÊÁÔ˙˚¸˝&'()*+,-./04;CDEOPQRVZbcdlmÖÜáàâäãåçéèìöõ£§•≠ÆØπ∫ªº¿ƒ≈ÕŒÊÁËÈÍÎÏÌÓÔÙ˚          " # $ , - E F G H I J K L M N O S Z [ c d e o p q r v z Ç É Ñ å ç • ¶ ß ® © ™ ´ ¨ ≠ Æ Ø ≥ ∫ ¬ √ ƒ Ã Õ ◊ ÿ Ÿ ⁄ ﬁ ‚ Í Î!!!!!!!	!
!!!!!! !!!"!*!+!5!6!7!8!<!@!H!I!a!b!c!d!e!f!g!h!i!j!k!o!v!~!!Ä!ä!ã!å!ç!ë!ï!ù!û!ü!ß!®!¿!¡!¬!√!ƒ!≈!∆!«!»!…! !Œ!’!›!ﬁ!ﬂ!Á!Ë!Ú!Û!Ù!ı!˘!˝!˛"""" "!"""#"$"%"&"'"(")"-"4"<"=">"H"I"J"K"O"S"["\"]"e"f"~""Ä"Å"Ç"É"Ñ"Ö"Ü"á"à"å"ì"õ"ú"ù"ß"®"©"™"Æ"≤"∫"ª"º"ƒ"≈"›"ﬁ"ﬂ"‡"·"‚"„"‰"Â"Ê"Á"Î"Ú"Û"˚"¸"˝###	#
#######$#<#=#>#?#@#A#B#C#D#E#F#J#Q#R#Z#[#\#f#g#h#i#m#q#y#z#{#|#Ñ#Ö#ù#û#ü#†#°#¢#£#§#•#¶#ß#´#≤#∫#ª#º#ƒ#≈#œ#–#—#“#÷#⁄#€#„#‰#¸#˝#˛#ˇ$ $$$$$$$
$$$$$#$$$.$/$0$1$5$9$:$;$C$D$\$]$^$_$`$a$b$c$d$e$f$j$q$y$z${$Ö$Ü$á$à$å$ê$ò$ô$°$¢$∫$ª$º$Ω$æ$ø$¿$¡$¬$√$ƒ$»$œ$◊$ÿ$Ÿ$·$‚$Ï$Ì$Ó$Ô$Û$˜$ˇ% %%%%%%%%% %!%"%&%-%5%6%7%A%B%C%D%H%L%T%U%]%^%v%w%x%y%z%{%|%}%~%%Ä%Ñ%ã%ì%î%ï%ü%†%°%¢%¶%™%≤%≥%ª%º%‘%’%÷%◊%ÿ%Ÿ%⁄%€%‹%›%ﬁ%‚%È%Í%Ú%Û%Ù%¸%˝%˛&&	&
&&&&&&&5&6&7&8&9&:&;&<&=&>&?&C&J&R&S&T&^&_&`&a&e&i&q&r&z&{&ì&î&ï&ñ&ó&ò&ô&ö&õ&ú&ù&°&®&©&±&≤&≥&ª&º&Ω&«&»&…& &Œ&“&”&€&‹&Ù&ı&ˆ&˜&¯&˘&˙&˚&¸&˝&˛''	''''''' '$'('0'1'9':'R'S'T'U'V'W'X'Y'Z'['\'`'g'h'i'q'r's'}'~''Ä'Ñ'à'ê'ë'í'ö'õ'≥'¥'µ'∂'∑'∏'π'∫'ª'º'Ω'¡'»'…'—'“'”'›'ﬁ'ﬂ'‡'‰'Ë''Ò'Ú'Û'˚'¸(((((((((((("()(1(2(3(=(>(?(@(D(H(P(Q(R(Z([(s(t(u(v(w(x(y(z({(|(}(Å(à(ê(ë(í(ö(õ(•(¶(ß(®(¨(∞(∏(π(—(“(”(‘(’(÷(◊(ÿ(Ÿ(⁄(€(ﬂ(Ê(Ó(Ô((˙(˚(¸(˝))))))))0)1)2)3)4)5)6)7)8)9):)>)E)M)N)O)W)X)b)c)d)e)i)m)n)v)w)})Å)Ç)å)ç)é)è)ï)ô)ö)Æ)Ø)∞)±)≤)≥)¥)µ)∂)∫)ª)ø)√)ƒ)≈)∆) )–)—)’)÷)ÿ)Ÿ)›)Á)Î)Ï))Ò)ı)ˆ* ************* *!*%*7*8*9*:*;*<*=*>*?*C*D*H*L*P*U*V*W*_*`*a*b*f*r*s*t*u*v*w*x*|*Å*Ç*É*ç*é*è*ê*î*§*•*¶*ß*®*©*™*Æ*≤*∂*π,¿,√,∆,…,Ã,œ,“,’,ÿ,€,ﬁ,·,‰,Á,Í,Ì,,Û,ˆ,˘,¸,ˇ----------- -#-&-)-,-/-2-5-8-;->-A-D-G-J-M-P-S-V-Y-\-_-b-e-h-k-n-q-t-w-z-}-Ä-É-Ü-â-å-è-í-ï-ò-õ-û-°-§-ß-™-≠-∞-≥-∂-π-º-ø-¬-≈-»-À-Œ-—-‘-◊-⁄-›-‡-„-Ê-È-Ï-Ô-Ú-ı-¯-˚-˛....
........".%.(.+...1.4.7.:.=.@.C.F.I.L.O.R.U.X.[.^.a.d.g.j.m.p.s.v.y.|..Ç.Ö.à.ã.é.ë.î.ó.ö.ù.†.£.¶.©.¨.Ø.≤.µ.∏.ª.æ.¡.ƒ.«. .Õ.–.”.÷.Ÿ.‹.ﬂ.‚.Â.Ë.Î.Ó.Ò.Ù.˜.˙.˝/ ///	////////!/$/'/*/-/0/3/6/9/</?/B/E/H/K/N/Q/T/W/Z/]/`/c/f/i/l/o/r/u/x/{/~/Å/Ñ/á/ä/ç/ê/ì/ñ/ô/ú/ü/¢/•/®/´/Æ/±/¥/∑/∫/Ω/¿/√/∆/…/Ã/œ/“/’/ÿ/€/ﬁ/·/‰/Á/Í/Ì//Û/ˆ/˘/¸/ˇ00000000000 0#0&0)0,0/0205080;0>0A0D0G0J0M0P0S0V0Y0\0_0b0e0h0k0n0q0t0w0z0}0Ä0É0Ü0â0å0è0í0ï0ò0õ0û0°0§0ß0™0≠0∞0≥0∂0π0º0ø0¬0≈0»0À0Œ0—0‘0◊0⁄0›0‡0„0Ê0È0Ï0Ô0Ú0ı0¯0˚0˛1111
11111111"1%1(1+1.1114171:1=1@1C1F1I1L1O1R1U1X1[1^1a1d1g1j1m1p1s1v1y1|11Ç1Ö1à1ã1é1ë1î1ó1ö1ù1†1£1¶1©1¨1Ø1≤1µ1∏1ª1æ1¡1ƒ1«1 1Õ1–1”1÷1Ÿ1‹1ﬂ1‚1Â1Ë1Î1Ó1Ò1Ù1˜1˙1˝2 222	22222222!2$2'2*2-202326292<2?2B2E2H2K2N2Q2T2W2Z2]2`2c2f2i2l2o2r2u2x2{2~2Å2Ñ2á2ä2ç2ê2ì2ñ2ô2ú2ü2¢2•2®2´2Æ2±2¥2∑2∫2Ω2¿2√2∆2…2Ã2–2÷2◊2ÿ2⁄2›2·2Á2Î2ı2ˆ2˜2¯3 3333	3333333"3#3&3>3?3@3A3B3C3D3E3F3G3H3I3J3K3L3M3N3O3R3^3_3`3a3b3c3d3e3k3u3v3w3x3y3É3Ñ3á3ó3ò3ô3ö3õ3ú3ü3†3¥3µ3∂3∑3∏3π3∫3ª3º3Ω3”3‘3’3÷3◊3ÿ3Ÿ3⁄3€3‹3Ó3Ô33Ò3Ú3Û3Ù3ı4444444444444&4'4(4)4*4+4,48494:4;4<4B4C4D4E4K4L4P4Q4W4X4\4]4e4f4g4q4r4s4t4u4}4Ç4Ö4â4ä4é4è4õ4ú4ù4û4ü4†4¶4ß4´4¨4º4Ω4æ4ø4¿4¡4¬4‘4’4÷4◊4ÿ4Ÿ4⁄4€4‹4ﬂ4Ù4ı4ˆ4˜4¯4˘4˙4˚4¸4˝4˛4ˇ5 5555555555555555%5&5'5A5B5C5D5E5F5G5H5I5J5K5L5O5P5Q5R5V5f5g5h5i5j5k5l5m5u5v5w5Ñ5î5†5°5™5∞5±5µ5π5∫5æ5ø5√5ƒ5»5…5Õ5Œ5“5”5◊5€5‹5·5Â5Ê5Á5ˇ6 666666666	666666&6'61626364686<6D6E6I6R6Y6b6f6g6k6l6p6t6x6|6}6Å6Ç6ö6õ6ú6ù6û6ü6†6°6¢6£6§6®6Ø6∑6∏6π6√6ƒ6≈6∆6 6Œ6÷6◊6ﬂ6‡6È66˘6˝6˛7777777777172737475767778797:7;7?7F7N7O7P7X7Y7c7d7e7f7j7n7v7w7x7Å7à7é7í7ì7ó7ò7ú7ù7°7¢7£7ª7º7Ω7æ7ø7¿7¡7¬7√7ƒ7≈7…7–7ÿ7Ÿ7⁄7‚7„7Ì7Ó7Ô77Ù7¯8 88888888#8$8(8)8-818286878O8P8Q8R8S8T8U8V8W8X8Y8]8d8l8m8n8x8y8z8{88É8ã8å8î8ï8û8•8Æ8≤8≥8∑8∏8º8¿8ƒ8»8…8Õ8Œ8Ê8Á8Ë8È8Í8Î8Ï8Ì8Ó8Ô88Ù8˚99999999999#9+9,9-969=9F9J9K9O9P9T9X9\9`9a9e9f9~99Ä9Å9Ç9É9Ñ9Ö9Ü9á9à9å9ì9õ9ú9ù9ß9®9©9™9Æ9≤9∫9ª9√9ƒ9Õ9‘9ﬁ9‚9„9Á9Ë9Ï99Ù9¯9¸9˝::::::::: :!:":#:$:(:/:7:8:9:C:D:E:F:J:N:V:W:_:`:i:p:x:|:}:Å:Ç:Ü:ä:é:è:ì:î:¨:≠:Æ:Ø:∞:±:≤:≥:¥:µ:∂:∫:¡:…: :À:”:‘:ﬁ:ﬂ:‡:·:Â:È:Ò:Ú:Û:¸;;;;;;;;!;%;&;*;+;C;D;E;F;G;H;I;J;K;L;M;Q;X;`;a;b;j;k;u;v;w;x;|;Ä;Å;â;ä;é;í;õ;¢;™;Æ;≤;∂;∫;æ;ø;√;€;‹;›;ﬁ;ﬂ;‡;·;‚;„;‰;Â;È;;¯;˘;˙<<<<<<<<< <!<"<&<'<.<6<:<><B<F<J<K<O<[<\<]<^<_<k<l<m<n<o<É<Ñ<Ö<Ü<á<à<â<ä<ã<ë<í<ó<°<¢<£<§<•<¶<∞<±<≤<∂<º<Ω<¬<Ã<Õ<Œ<ÿ<Ÿ<⁄U$null”      WNS.keysZNS.objectsV$class¢  ÄÄ¢  ÄÅ±Ä|_IDEWorkspaceDocument_$9229D32F-C4FF-43F2-A95F-816FAA71C582”     ) 8Æ        ! " # $ % & ' (ÄÄÄÄÄ	Ä
ÄÄÄÄÄÄÄÄÆ * + , - . / 0 1 2 3 - 5 6 0ÄÄ*ÅkÅnÄrÅpÄsÅqÅsÅÖÅnÅóÅòÄsÄ@_RecentEditorDocumentURLs_DefaultEditorStatesForURLs\ActiveScheme_ActiveProjectSetIdentifierKey_$RunDestinationArchitectureVisibility_DocumentWindows_EnableThreadGallery_WindowArrangementDebugInfo_RunContextRecents_ActiveRunDestination_ActiveProjectSetNameKey_SelectedWindows_0LastCompletedPersistentSchemeBasedActivityReport_BreakpointsActivated“   H S™ I J K L M N O P Q RÄÄÄÄÄÄÄ!Ä#Ä%Ä'Ä)” U  V W X YWNS.base[NS.relativeÄ ÄÄ_zfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/DI/Injection.swift“ \ ] ^ _Z$classnameX$classesUNSURL¢ ^ `XNSObject” U  V W X dÄ ÄÄ_ãfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/Entity/GameEntity.swift” U  V W X iÄ ÄÄ_éfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/LocaleGameDataSource.swift” U  V W X nÄ ÄÄ_éfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Remote/RemoteGameDataSource.swift” U  V W X sÄ ÄÄ_Çfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/GameRespository.swift” U  V W X xÄ ÄÄ _ófile:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/SDWebImageSwiftUI/Package.swift” U  V W X }Ä ÄÄ"_êfile:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/SDWebImage/Package.swift” U  V W X ÇÄ ÄÄ$_Üfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/DetailView.swift” U  V W X áÄ ÄÄ&_êfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Presenter/DetailPresenter.swift” U  V W X åÄ ÄÄ(_äfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Router/DetailRouter.swift“ \ ] è êWNSArray¢ è `”    í ö 8ß ì î ï ñ ó ò ôÄ+Ä,Ä-Ä.Ä/Ä0Ä1ß õ ú ù û ü † °Ä2ÄGÄbÄâÅ˚ÅÅÄ@_'Xcode.IDEKit.EditorDocument.LogDocument_2Xcode.IDEKit.EditorDocument.DebugHierarchyDocument_:Xcode.IDEKit.EditorDocument.PegasusNonFileSystemSourceCode_-Xcode.IDEKit.EditorDocument.PegasusSourceCode_7Xcode.IDEKit.EditorDocument.MemoryGraphDebuggerDocument_(Xcode.IDEKit.EditorDocument.AssetCatalog_7Xcode.Xcode3ProjectSupport.EditorDocument.Xcode3Project”    ´ Æ 8¢ ¨ ≠Ä3Ä5¢ Ø ∞Ä7ÄAÄ@” U  V W X µÄ ÄÄ4_2x-xcode-log://DE4259B5-7817-44E7-8091-BC59E4CDE608” U  V W X ∫Ä ÄÄ6_2x-xcode-log://55CEB2A9-122B-4C7C-8B4C-1B6CFB5F6402”    Ω ø 8° æÄ8° ¿Ä9Ä@_SelectedDocumentLocations“   ƒ S° ≈Ä:Ä)’  » …   À Ã W Œ µ –Ytimestamp_expandTranscript[documentURLYindexPathÄ?Ä Ä4Ä;” “ ”  ‘ ’ ÷_NSIndexPathLength_NSIndexPathDataÄ<Ä>“ ÿ  Ÿ ⁄WNS.dataBÄ=“ \ ] ‹ ›]NSMutableData£ ‹ ﬁ `VNSData“ \ ] ‡ ·[NSIndexPath¢ ‚ `[NSIndexPath“ \ ] ‰ Â_IDELogDocumentLocation£ Ê Á `_IDELogDocumentLocation_DVTDocumentLocation“ \ ] È Í_NSMutableDictionary£ È Î `\NSDictionary”    Ì Ô 8° ÓÄB° ÄCÄ@_SelectedDocumentLocations“   Ù S° ıÄDÄ)’  » …   À Ã W Œ ∫ ¸Ä?Ä Ä6ÄE” “ ”  ‘ ˛ ÷ÄFÄ>“ ÿ  ⁄B Ä=”    8¢ÄHÄJ¢	ÄLÄYÄ@” U  V W XÄ ÄÄI_#x-xcode-debug-views:///7fe9b75fc020” U  V W XÄ ÄÄK_#x-xcode-debug-views:///7fe9a34c3dd0”    8¢ÄMÄN¢ÄOÄPÄ@TzoomXrotation#?Œ‡è∏#Ó	“  ! S§"#$%ÄQÄUÄWÄXÄ)“  ( S§)**,ÄRÄSÄSÄTÄ)#?      #        #ø‡sº@   “  2 S§*)*6ÄSÄRÄSÄVÄ)#?€òLÄ   “  : S§**)*ÄSÄSÄRÄSÄ)“  A S§***)ÄSÄSÄSÄRÄ)”   HK 8¢ÄMÄN¢LMÄZÄ[Ä@#?Œ‡è∏#Ó	“  Q S§RSTUÄ\Ä^Ä`ÄaÄ)“  X S§)**\ÄRÄSÄSÄ]Ä)#øÚR√¿   “  ` S§*)*dÄSÄRÄSÄ_Ä)#?ˆg    “  h S§**)*ÄSÄSÄRÄSÄ)“  o S§***)ÄSÄSÄSÄRÄ)”   vx 8°wÄc°yÄeÄ@” U  V W X~Ä ÄÄd_Nx-xcode-module://Foundation?language=Xcode.SourceCodeLanguage.Swift&swift-framework-paths=/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator/PackageFrameworks%20/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator&swift-header-paths=/Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/Index.noindex/Build/Products/Debug-iphonesimulator&swift-module-cache-path=/Users/ben/Library/Developer/Xcode/DerivedData/ModuleCache.noindex&swift-sdk=iphonesimulator18.2&swift-target=x86_64-apple-ios16.1-simulator&toolchains=com.apple.dt.toolchain.XcodeDefault”   Åå 8™ÇÉÑÖÜáàâäãÄfÄgÄhÄiÄjÄkÄlÄmÄnÄo™ç 0 0 0ë) . 0ïñÄpÄsÄsÄsÄtÄRÄrÄsÄÖÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  £ S°§ÄqÄ)“  ß S§ . . . .ÄrÄrÄrÄrÄ) ”   ∞≥ ¢±≤ÄuÄv¢¥µÄwÄ}Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ∫Ω ¢ªºÄxÄy¢æøÄzÄ{Ä|^documentLength[lineIndexes“  ≈ S†Ä)“ \ ] Î»¢ Î `”    Œ £ÀÃÕÄ~ÄÄÄ£œ–—ÄÅÄÉÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“◊ ÿŸ\NS.uuidbytesOˆ£Wx&`@íÖGÁ[õxÄÇ“ \ ]€‹VNSUUID¢€ `“ﬁ ﬂŸ\NS.uuidbytesOÂ’˜¸ªD{®áˇaz6bµÄÇ#@d†     ”   „Ê ¢‰ÂÄÜÄá¢øæÄ{ÄzÄ|Ufolds^documentLength	”   ÓQ 8ØbÔÒÚÛÙ Qˆ˜¯˘˙˚¸˝ P O  J I	
 K !"#$%&'( N*+,- R/012345678 L: M<=>?@ABCDEFGHIJKLMNOPÄäÄåÄéÄêÄíÄîÄ%ÄñÄòÄöÄúÄûÄ†Ä¢Ä§Ä#Ä!Ä¶ÄÄ®Ä™Ä¨ÄÆÄÄ∞Ä≤Ä¥Ä∂Ä∏Ä∫ÄºÄæÄ¿Ä¬ÄƒÄ∆Ä»Ä ÄÃÄŒÄ–ÄÄ“Ä‘Ä÷ÄÿÄ⁄Ä‹ÄﬁÄ‡Ä‚Ä‰ÄÊÄËÄÍÄÏÄÓÄÄÄÚÄÙÄˆÄ¯Ä'Ä˙Ä¸Ä˛Å ÅÅÅÅÅ
ÅÄÅÄÅÅÅÅÅÅÅÅÅ Å"Å$Å&Å(Å*Å,Å.Å0Å2Å4Å6Å8ØbRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~ÄÅÇÉÑÖÜáàâäãåçéèêëíìîïñóòôöõúùûü†°¢£§•¶ß®©™´¨≠ÆØ∞±≤≥Å:ÅWÅwÅóÅ¥Å—ÅÏÅ
Å(ÅEÅcÅ~ÅöÅµÅ–ÅÍÅÅ#ÅAÅ\ÅzÅïÅ±ÅÃÅËÅÅÅ:ÅWÅrÅéÅ©Å≈Å‡Å˛ÅÅEÅbÅ|ÅóÅ¥ÅŒÅËÅÅ!Å=Å\ÅyÅïÅ±ÅÕÅËÅÅÅFÅcÅÅÅûÅπÅ”ÅÅÅ&ÅAÅ\Å{ÅóÅ≥ÅŒÅËÅ	Å	Å	:Å	UÅ	qÅ	ãÅ	•Å	¿Å	€Å	ˆÅ
Å
,Å
IÅ
dÅ
ÄÅ
öÅ
¥Å
ŒÅ
ËÅÅÅ<ÅVÅsÅêÅ´Å≈Å‡Ä@” U  V W X∏Ä ÄÄã_çfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Domain/UseCase/DetailInteractor.swift” U  V W XΩÄ ÄÄç_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteDataSource.swift” U  V W X¬Ä ÄÄè_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleGameDataSource.swift” U  V W X«Ä ÄÄë_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/TheMealsAppApp.swift” U  V W XÃÄ ÄÄì_ëfile:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/realm-swift/Package.swift” U  V W X—Ä ÄÄï_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/ErrorLoggingDelegate.swift” U  V W X÷Ä ÄÄó_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Presenter/DetailPresenter.swift” U  V W X€Ä ÄÄô_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/HomeInteractor.swift” U  V W X‡Ä ÄÄõ_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteRow.swift” U  V W XÂÄ ÄÄù_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Remote/Response/CategoriesResponse.swift” U  V W XÍÄ ÄÄü_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/IngredientModel.swift” U  V W XÔÄ ÄÄ°_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Remote/Response/MealsResponse.swift” U  V W XÙÄ ÄÄ£_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/CustomIcon.swift” U  V W X˘Ä ÄÄ•_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Remote/RemoteDataSource.swift” U  V W X˛Ä ÄÄß_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/IngredientMapper.swift” U  V W XÄ ÄÄ©_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/View/SearchRow.swift” U  V W XÄ ÄÄ´_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Router/SearchRouter.swift” U  V W XÄ ÄÄ≠_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/MealsResponse.swift” U  V W XÄ ÄÄØ_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Router/HomeRouter.swift” U  V W XÄ ÄÄ±_ñfile:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/GameCatalogAbout/Package.swift” U  V W XÄ ÄÄ≥_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Extensions/CustomeError+Ext.swift” U  V W X!Ä ÄÄµ_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/MealInteractor.swift” U  V W X&Ä ÄÄ∑_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GamesResponse.swift” U  V W X+Ä ÄÄπ_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Module/Untitled.swift” U  V W X0Ä ÄÄª_|file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Presenter/FavoritePresenter.swift” U  V W X5Ä ÄÄΩ_hfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/TabItem.swift” U  V W X:Ä ÄÄø_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/HomeView.swift” U  V W X?Ä ÄÄ¡_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel%202.swift” U  V W XDÄ ÄÄ√_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/GameDetailResponse.swift” U  V W XIÄ ÄÄ≈_}file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/Response/CategoriesResponse.swift” U  V W XNÄ ÄÄ«_{file:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/About/About.swift” U  V W XSÄ ÄÄ…_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/DI/Injection.swift” U  V W XXÄ ÄÄÀ_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Network/URLSession+Extension.swift” U  V W X]Ä ÄÄÕ_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/LocaleDataSource.swift” U  V W XbÄ ÄÄœ_bfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/DI/Injection.swift” U  V W XgÄ ÄÄ—_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/App/GameCatalogApp.swift” U  V W XlÄ ÄÄ”_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/App/ContentView.swift” U  V W XqÄ ÄÄ’_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity.swift” U  V W XvÄ ÄÄ◊_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/View/MealRow.swift” U  V W X{Ä ÄÄŸ_tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/Presenter/HomePresenter.swift” U  V W XÄÄ ÄÄ€_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/GameMapper.swift” U  V W XÖÄ ÄÄ›_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/View/SearchView.swift” U  V W XäÄ ÄÄﬂ_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/LocaleDataSource.swift” U  V W XèÄ ÄÄ·_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/MealRepository.swift” U  V W XîÄ ÄÄ„_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Domain/Model/CategoryModel.swift” U  V W XôÄ ÄÄÂ_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/Entity/MealEntity.swift” U  V W XûÄ ÄÄÁ_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/Untitled.swift” U  V W X£Ä ÄÄÈ_cfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Module/About/About.swift” U  V W X®Ä ÄÄÎ_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Meal/View/MealView.swift” U  V W X≠Ä ÄÄÌ_`file:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/App/ContentView.swift” U  V W X≤Ä ÄÄÔ_efile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/AppLogger.swift” U  V W X∑Ä ÄÄÒ_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/IngredientEntity.swift” U  V W XºÄ ÄÄÛ_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/MealMapper.swift” U  V W X¡Ä ÄÄı_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameModel.swift” U  V W X∆Ä ÄÄ˜_|file:///Users/ben/belajar/ios-dicoding/project-expert/GameCatalogExpert/GameCatalog/Core/Domain/UseCase/HomeInteractor.swift” U  V W XÀÄ ÄÄ˘_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/Entity/CategoryEntity.swift” U  V W X–Ä ÄÄ˚_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Remote/RemoteGameDataSource.swift” U  V W X’Ä ÄÄ˝_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/GameDetailModel.swift” U  V W X⁄Ä ÄÄˇ_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/GameEntity%202.swift” U  V W XﬂÄ ÄÅ_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/FavoriteInteractor.swift” U  V W X‰Ä ÄÅ_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/MealEntity.swift” U  V W XÈÄ ÄÅ_Üfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Home/Router/HomeRouter.swift” U  V W XÓÄ ÄÅ_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Utils/Mapper/CategoryMapper.swift” U  V W XÛÄ ÄÅ	_tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Meal/Presenter/MealPresenter.swift” U  V W X¯Ä ÄÅ_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/CustomEmptyView.swift” U  V W X˝Ä ÄÅ_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/View/FavoriteView.swift” U  V W XÄ ÄÅ_qfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Mapper/CategoryMapper.swift” U  V W XÄ ÄÅ_nfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/View/DetailView.swift” U  V W XÄ ÄÅ_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/SearchInteractor.swift” U  V W XÄ ÄÅ_vfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Favorite/Router/FavoriteRouter.swift” U  V W XÄ ÄÅ_ufile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/UseCase/DetailInteractor.swift” U  V W XÄ ÄÅ_ñfile:///Users/ben/Library/Developer/Xcode/DerivedData/TheMealsApp-gwcsedjaotlplfhegxeejsxcyzrl/SourcePackages/checkouts/Alamofire/Source/Session.swift” U  V W X Ä ÄÅ_rfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Detail/Router/DetailRouter.swift” U  V W X%Ä ÄÅ_jfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/GameRespository.swift” U  V W X*Ä ÄÅ_wfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Data/Locale/Entity/CategoryEntity.swift” U  V W X/Ä ÄÅ!_sfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Domain/UseCase/HomeInteractor.swift” U  V W X4Ä ÄÅ#_ãfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Domain/UseCase/HomeInteractor.swift” U  V W X9Ä ÄÅ%_pfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/CategoryModel.swift” U  V W X>Ä ÄÅ'_kfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/Network/APICall.swift” U  V W XCÄ ÄÅ)_Çfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Home/View/HomeView.swift” U  V W XHÄ ÄÅ+_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Home/View/CategoryRow.swift” U  V W XMÄ ÄÅ-_ifile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/MealRepository.swift” U  V W XRÄ ÄÅ/_xfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Module/Search/Presenter/SearchPresenter.swift” U  V W XWÄ ÄÅ1_mfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Utils/View/ImageUtility.swift” U  V W X\Ä ÄÅ3_lfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Core/Domain/Model/MealModel.swift” U  V W XaÄ ÄÅ5_yfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog/Core/Data/Locale/Entity/IngredientEntity.swift” U  V W XfÄ ÄÅ7_xfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/App/ContentView.swift” U  V W XkÄ ÄÅ9_Éfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/MealRow.swift”   ny 8™opqrstuvwxÅ;Å<Å=Å>Å?Å@ÅAÅBÅCÅD™zñ 0 0~Ä 0ÇñÅEÄàÄsÄsÅGÅSÅTÄsÅUÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ê S°ëÅFÄ)“  î S§ . . . .ÄrÄrÄrÄrÄ)”   õû ¢úùÅHÅI¢ü†ÅJÅMÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   •® ¢¶ºÅKÄy¢©øÅLÄ{Ä|^documentLength‰”   Ø≥ £∞±≤ÅNÅOÅP£¥µ—ÅQÅRÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“º ΩŸ\NS.uuidbytesO!úçøJﬂïâWÃ≈h∑ÄÇ“¿ ¡Ÿ\NS.uuidbytesO5‘
6K¡∑ÔXµ ÄÇ#@1      ”   ∆… ¢‰»ÄÜÅV¢ø©Ä{ÅLÄ|^documentLength”   œ⁄ 8™–—“”‘’÷◊ÿŸÅXÅYÅZÅ[Å\Å]Å^Å_Å`Åa™€ñ 0 0ﬂ‡· 0„ñÅbÄàÄsÄsÅgÅsÅtÄsÅuÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ò S°ÚÅcÄ)“  ı S§ˆ˜ˆ˘ÅdÅeÅdÅfÄ)	”   ˇ ¢ ÅhÅi¢ÅjÅpÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   	 £
ÅkÅlÅm£—ÅnÅoÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ Ÿ\NS.uuidbytesO5¸ÆñAÍM·É†¨ëèÄÇ“ Ÿ\NS.uuidbytesO2à∑[G0≤ñÏV¶∂qÄÇ”   ! ¢º ÄyÅq¢ø#Ä{ÅrÄ|^documentLengthn#@(      Q”   *- ¢‰,ÄÜÅv¢ø#Ä{ÅrÄ|^documentLength”   3> 8™456789:;<=ÅxÅyÅzÅ{Å|Å}Å~ÅÅÄÅÅ™?ñ 0 0CDE 0GñÅÇÄàÄsÄsÅáÅìÅîÄsÅïÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  U S°VÅÉÄ)“  Y S§Z[\ .ÅÑÅÖÅÜÄrÄ)eq”   be ¢cdÅàÅâ¢fgÅäÅêÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   lp £mnoÅãÅåÅç£qr—ÅéÅèÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“y zŸ\NS.uuidbytesO–éÔ¿ËNŒÑz∂¡£ÑUgÄÇ“} ~Ÿ\NS.uuidbytesO˛f≤0{Baà¥º#ÄÇ”   ÅÑ ¢ÇºÅëÄy¢ÖøÅíÄ{Ä|^documentLengthï#@0Ä     ^”   çê ¢‰èÄÜÅñ¢øÖÄ{ÅíÄ|^documentLength”   ñ° 8™óòôöõúùûü†ÅòÅôÅöÅõÅúÅùÅûÅüÅ†Å°™¢ñ 0 0¶ß . 0™ñÅ¢ÄàÄsÄsÅ•Å±ÄrÄsÅ≤ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∏ S°πÅ£Ä)“  º S§ΩæΩæÅ§ÄzÅ§ÄzÄ)”   ƒ« ¢≈∆Å¶Åß¢»…Å®Å´Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   Œ— ¢œºÅ©Äy¢“øÅ™Ä{Ä|^documentLengthV”   ÿ‹ £Ÿ⁄€Å¨Å≠ÅÆ£›ﬁ—ÅØÅ∞ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Â ÊŸ\NS.uuidbytesO%”Í—AJ≥E-E0µøÄÇ“È ÍŸ\NS.uuidbytesOË≥u~EéD≈⁄Ù¶œÄÇ#ø      ”   ÓÒ ¢‰ÄÜÅ≥¢ø“Ä{Å™Ä|^documentLength”   ˜ 8™¯˘˙˚¸˝˛ˇ ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩÅæ™ñ 0 0* . 0ñÅøÄàÄsÄsÅ√ÄSÄrÄsÅœÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°Å¿Ä)“   S§Å¡Å¬Å¡Å¬Ä)
!”   &) ¢'(ÅƒÅ≈¢*+Å∆Å…Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   03 ¢º2ÄyÅ«¢ø5Ä{Å»Ä|^documentLength.!”   :> £;<=Å ÅÀÅÃ£?@—ÅÕÅŒÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“G HŸ\NS.uuidbytesO8Â–ÄöB≠Ç·Ä`	´ìÄÇ“K LŸ\NS.uuidbytesOA, ’ëúL©ä%ƒKŸÓÄÇ”   OR ¢‰QÄÜÅ–¢ø5Ä{Å»Ä|^documentLength”   Xc 8™YZ[\]^_`abÅ“Å”Å‘Å’Å÷Å◊ÅÿÅŸÅ⁄Å€™dñ 0 0hß . 0lñÅ‹ÄàÄsÄsÅﬂÅ±ÄrÄsÅÍÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  z S°{Å›Ä)“  ~ S§[[ÅﬁÅÖÅﬁÅÖÄ)”   Üâ ¢áàÅ‡Å·¢äãÅ‚Å‰Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   êì ¢ºíÄyÅ„¢ø·Ä{ÅtÄ|^documentLength”   ôù £öõúÅÂÅÊÅÁ£ûü—ÅËÅÈÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“¶ ßŸ\NS.uuidbytesOô±Õ–cúB–≠bËAqˇá|ÄÇ“™ ´Ÿ\NS.uuidbytesOog·)
K<ç]%‹ãéOïÄÇ”   Æ± ¢‰∞ÄÜÅÎ¢ø·Ä{ÅtÄ|^documentLength”   ∑¬ 8™∏π∫ªºΩæø¿¡ÅÌÅÓÅÔÅÅÒÅÚÅÛÅÙÅıÅˆ™√ñ 0 0«ß . 0ÀñÅ˜ÄàÄsÄsÅ˚Å±ÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ÿ S°⁄Å¯Ä)“  › S§ﬁ .‡ .Å˘ÄrÅ˙ÄrÄ)K”   ÊÈ ¢ÁËÅ¸Å˝¢ÍÎÅ˛ÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   Ù £ÒÚÛÅˇÅ Å£ıˆ—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“˝ ˛Ÿ\NS.uuidbytesO√Ë‹!˝åIªÑ◊∫3º4ÄÇ“ Ÿ\NS.uuidbytesOÎ/©eîr@¶®/∏:…P∂8ÄÇ”    ¢ºÄyÅ¢	
ÅÅÄ|^documentLength“   S†Ä)q”    ¢‰ÄÜÅ	¢	
ÅÅÄ|^documentLength”   & 8™ !"#$%ÅÅÅÅÅÅÅÅÅÅ™'ñ 0 0+,- 0/ñÅÄàÄsÄsÅÅ$Å%ÄsÅ&ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  = S°>ÅÄ)“  A S§BCBCÅÅÅÅÄ)”   JM ¢KLÅÅ¢NOÅÅÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   TW ¢ºVÄyÅ¢ø
Ä{ÅÄ|^documentLength”   ]a £^_`ÅÅ Å!£bc—Å"Å#ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“j kŸ\NS.uuidbytesOA˝bMO®õõU{¸{ÅÄÇ“n oŸ\NS.uuidbytesOE‚≈´=’L¢Ö1Ÿ;‡Ï√ÄÇ#@0Ä     8”   tw ¢‰vÄÜÅ'¢ø
Ä{ÅÄ|^documentLength”   }à 8™~ÄÅÇÉÑÖÜáÅ)Å*Å+Å,Å-Å.Å/Å0Å1Å2™âñ 0 0çß . 0ëñÅ3ÄàÄsÄsÅ7Å±ÄrÄsÅCÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ü S°†Å4Ä)“  £ S§§•§•Å5Å6Å5Å6Ä)”   ¨Ø ¢≠ÆÅ8Å9¢∞±Å:Å=Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ∂π ¢º∏ÄyÅ;¢øªÄ{Å<Ä|^documentLength””   ¿ƒ £¡¬√Å>Å?Å@£≈∆—ÅAÅBÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Õ ŒŸ\NS.uuidbytesOpÛÃ-r@C–óvPöm/ÄÇ“— “Ÿ\NS.uuidbytesO˘Ô“wÜáAîñd»°3nÄÇ”   ’ÿ ¢‰◊ÄÜÅD¢øªÄ{Å<Ä|^documentLength”   ﬁÈ 8™ﬂ‡·‚„‰ÂÊÁËÅFÅGÅHÅIÅJÅKÅLÅMÅNÅO™Íñ 0 0ÓÔ 0ÚñÅPÄàÄsÄsÅSÅ_Å`ÄsÅaÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“    S°ÅQÄ)“   S§ÄÄ .ÅTÅTÅRÄrÄ)D”    ¢ÅTÅU¢ÅVÅYÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢ºÅWÄy¢øÅXÄ{Ä|^documentLength:”    $ £!"#ÅZÅ[Å\£%&—Å]Å^ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“- .Ÿ\NS.uuidbytesO|X#√¥NJMôŸ{’ÄGÿÄÇ“1 2Ÿ\NS.uuidbytesOj‚ZÅJ‰ë‡mñy<6¢ÄÇ#@      -”   7: ¢‰9ÄÜÅb¢øÄ{ÅXÄ|^documentLength”   @K 8™ABCDEFGHIJÅdÅeÅfÅgÅhÅiÅjÅkÅlÅm™Lñ 0 0P* . 0TñÅnÄàÄsÄsÅpÄSÄrÄsÅ|ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  b S°cÅoÄ)“  f S§ . . . .ÄrÄrÄrÄrÄ)”   mp ¢noÅqÅr¢qrÅsÅvÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   wz ¢xºÅtÄy¢{øÅuÄ{Ä|^documentLength”   ÅÖ £ÇÉÑÅwÅxÅy£Üá—ÅzÅ{ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“é èŸ\NS.uuidbytesO6ô|t^K¡ûËä≠F)ÄÇ“í ìŸ\NS.uuidbytesO‡JÉYBG~àrèè2SµÄÇ”   ñô ¢‰òÄÜÅ}¢ø{Ä{ÅuÄ|^documentLength”   ü™ 8™†°¢£§•¶ß®©ÅÅÄÅÅÅÇÅÉÅÑÅÖÅÜÅáÅà™´ñ 0 0Øß . 0≥ñÅâÄàÄsÄsÅåÅ±ÄrÄsÅòÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ¡ S°¬ÅäÄ)“  ≈ S§Ä[» .ÅTÅÖÅãÄrÄ)”   Õ– ¢ŒœÅçÅé¢—“ÅèÅïÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ◊€ £ÿŸ⁄ÅêÅëÅí£‹›—ÅìÅîÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“‰ ÂŸ\NS.uuidbytesOΩlxÚLù≠ü—u\j∞ÚÄÇ“Ë ÈŸ\NS.uuidbytesOÆ–]º`LEX™`‚›n-À«ÄÇ”   ÏÔ ¢ÌºÅñÄy¢øÅóÄ{Ä|^documentLengthı”   ˆ˘ ¢‰¯ÄÜÅô¢øÄ{ÅóÄ|^documentLength”   ˇ	
 8™	 										ÅõÅúÅùÅûÅüÅ†Å°Å¢Å£Å§™	ñ 0 0	* . 0	ñÅ•ÄàÄsÄsÅßÄSÄrÄsÅ≥ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	! S°	"Å¶Ä)“  	% S§ . . . .ÄrÄrÄrÄrÄ)”   	,	/ ¢	-	.Å®Å©¢	0	1Å™Å∞Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   	6	: £	7	8	9Å´Å¨Å≠£	;	<—ÅÆÅØÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“	C 	DŸ\NS.uuidbytesOaná!ˆ{Gµcå{´Ÿ’ÇÄÇ“	G 	HŸ\NS.uuidbytesO˛VñR†‘C~úÄÖá«{-ÄÇ”   	K	N ¢	LºÅ±Äy¢	OøÅ≤Ä{Ä|^documentLengthË”   	U	X ¢‰	WÄÜÅ¥¢ø	OÄ{Å≤Ä|^documentLength”   	^	i 8™	_	`	a	b	c	d	e	f	g	hÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩÅæÅø™	jñ 0 0	n* . 0	rñÅ¿ÄàÄsÄsÅ¬ÄSÄrÄsÅŒÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	Ä S°	ÅÅ¡Ä)“  	Ñ S§ . . . .ÄrÄrÄrÄrÄ)”   	ã	é ¢	å	çÅ√Åƒ¢	è	êÅ≈Å»Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   	ï	ò ¢º	óÄyÅ∆¢ø	öÄ{Å«Ä|^documentLengthÃ”   	ü	£ £	†	°	¢Å…Å ÅÀ£	§	•—ÅÃÅÕÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“	¨ 	≠Ÿ\NS.uuidbytesOÒ«’]AM.™!Õ˝§_ËzÄÇ“	∞ 	±Ÿ\NS.uuidbytesOº¿/E√G)øG‘,«vC∆ÄÇ”   	¥	∑ ¢‰	∂ÄÜÅœ¢ø	öÄ{Å«Ä|^documentLength”   	Ω	» 8™	æ	ø	¿	¡	¬	√	ƒ	≈	∆	«Å—Å“Å”Å‘Å’Å÷Å◊ÅÿÅŸÅ⁄™	…ñ 0 0	Õ* . 0	—ñÅ€ÄàÄsÄsÅ›ÄSÄrÄsÅËÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  	ﬂ S°	‡Å‹Ä)“  	„ S§ . . . .ÄrÄrÄrÄrÄ)”   	Í	Ì ¢	Î	ÏÅﬁÅﬂ¢	Ó	ÔÅ‡ÅÊÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   	Ù	¯ £	ı	ˆ	˜Å·Å‚Å„£	˘	˙—Å‰ÅÂÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“
 
Ÿ\NS.uuidbytesO’5oÌmDI|¢w˛&ûùOﬁÄÇ“
 
Ÿ\NS.uuidbytesOtxéÈŒKM∑ÖÅø√ïg»ÄÇ”   
	
 ¢º
ÄyÅÁ¢ø#Ä{ÅrÄ|^documentLength”   

 ¢‰
ÄÜÅÈ¢ø#Ä{ÅrÄ|^documentLength”   

& 8™




 
!
"
#
$
%ÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚÅÛÅÙ™
'ñ 0 0
+
,
- 0
/ñÅıÄàÄsÄsÅ¯ÅÅÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
= S°
>ÅˆÄ)“  
A S§
BΩ
BΩÅ˜Å§Å˜Å§Ä)õ”   
I
L ¢
J
KÅ˘Å˙¢
M
NÅ˚ÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   
S
W £
T
U
VÅ¸Å˝Å˛£
X—
ZÅˇÄÑÅ Ä|_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“
` 
aŸ\NS.uuidbytesO6)åßﬁ?Auí&
ÌØ1Ó)ÄÇ“
d 
eŸ\NS.uuidbytesOôZ3^=àC∂è\$ÓÕÄÇ”   
h
k ¢
iºÅÄy¢
l	ÅÅÄ|^documentLength#@$      ì”   
t
w ¢‰
vÄÜÅ¢	
lÅÅÄ|^documentLength”   
}
à 8™
~

Ä
Å
Ç
É
Ñ
Ö
Ü
áÅ	Å
ÅÅÅÅÅÅÅÅ™
âñ 0 0
ç* . 0
ëñÅÄàÄsÄsÅÄSÄrÄsÅ!ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
ü S°
†ÅÄ)“  
£ S§ . . . .ÄrÄrÄrÄrÄ)”   
™
≠ ¢
´
¨ÅÅ¢
Æ
ØÅÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   
¥
∏ £
µ
∂
∑ÅÅÅ£
π—
ªÅÄÑÅÄ|_&SourceCodeEditor.playgroundResultStore_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController“
¡ 
¬Ÿ\NS.uuidbytesOƒéäØ-≠D‡±K˚Ô¥VÄÇ“
≈ 
∆Ÿ\NS.uuidbytesOΩ’áq+Fñ™AQ9ˆöUÄÇ”   
…
Ã ¢º
ÀÄyÅ¢	
ŒÅÅ Ä|^documentLength}”   
”
÷ ¢‰
’ÄÜÅ"¢	
ŒÅÅ Ä|^documentLength”   
‹
Á 8™
›
ﬁ
ﬂ
‡
·
‚
„
‰
Â
ÊÅ$Å%Å&Å'Å(Å)Å*Å+Å,Å-™
Ëñ 0 0
ÏÔ
Ó 0
ñÅ.ÄàÄsÄsÅ2Å_Å>ÄsÅ?ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  
˛ S°
ˇÅ/Ä)“   S§Ä .ÅTÅ0Å1ÄrÄ)v”    ¢Å3Å4¢Å5Å;Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”    £Å6Å7Å8£—Å9Å:ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“" #Ÿ\NS.uuidbytesO:õù˜¸ÍGgπô§=W'ÅÄÇ“& 'Ÿ\NS.uuidbytesOiåú7TN∏±àäé©”ÄÇ”   *- ¢º,ÄyÅ<¢ø/Ä{Å=Ä|^documentLength“_”   58 ¢‰7ÄÜÅ@¢ø/Ä{Å=Ä|^documentLength”   >I 8™?@ABCDEFGHÅBÅCÅDÅEÅFÅGÅHÅIÅJÅK™Jñ 0 0N* . 0RñÅLÄàÄsÄsÅNÄSÄrÄsÅZÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ` S°aÅMÄ)“  d S§ . . . .ÄrÄrÄrÄrÄ)”   kn ¢lmÅOÅP¢opÅQÅTÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ux ¢vºÅRÄy¢y	ÅSÅÄ|^documentLength”   É £ÄÅÇÅUÅVÅW£ÑÖ—ÅXÅYÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“å çŸ\NS.uuidbytesOı?¸;ÙK:•5ò•øWÅ˚ÄÇ“ê ëŸ\NS.uuidbytesOvµ_…]CÃóì∏‡· ÁÄÇ”   îó ¢‰ñÄÜÅ[¢	yÅÅSÄ|^documentLength”   ù® 8™ûü†°¢£§•¶ßÅ]Å^Å_Å`ÅaÅbÅcÅdÅeÅf™©ñ 0 0≠
,Ø 0±ñÅgÄàÄsÄsÅkÅÅwÄsÅxÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ø S°¿ÅhÄ)“  √ S§ƒ≈ƒ≈ÅiÅjÅiÅjÄ)>”   Ãœ ¢ÕŒÅlÅm¢–—ÅnÅqÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ÷Ÿ ¢ºÿÄyÅo¢ø€Ä{ÅpÄ|^documentLength∞”   ‡‰ £·‚„ÅrÅsÅt£ÂÊ—ÅuÅvÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ì ÓŸ\NS.uuidbytesOR>¢¥)A©˘`~:®ÄÇ“Ò ÚŸ\NS.uuidbytesOŒï>±4Eû¢7ÎŸ¨™ÄÇ”   ˆ˘ ¢‰¯ÄÜÅy¢ø€Ä{ÅpÄ|^documentLength”   ˇ
 8™ 	Å{Å|Å}Å~ÅÅÄÅÅÅÇÅÉÅÑ™ñ 0 0ß . 0ñÅÖÄàÄsÄsÅáÅ±ÄrÄsÅìÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ! S°"ÅÜÄ)“  % S§CæCæÅÄzÅÄzÄ)”   ,/ ¢-.ÅàÅâ¢01ÅäÅêÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   6: £789ÅãÅåÅç£;<—ÅéÅèÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“C DŸ\NS.uuidbytesO=…-—Ó@[´ı∆óNﬁIÄÇ“G HŸ\NS.uuidbytesOr®ŒqMˇÑt≈ˆód‚–ÄÇ”   KN ¢ºMÄyÅë¢øPÄ{ÅíÄ|^documentLengthf”   UX ¢‰WÄÜÅî¢øPÄ{ÅíÄ|^documentLength”   ^i 8™_`abcdefghÅñÅóÅòÅôÅöÅõÅúÅùÅûÅü™jñ 0 0nop 0rñÅ†ÄàÄsÄsÅ¢Å≠ÅÆÄsÅØÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ä S°ÅÅ°Ä)“  Ñ S§ . . . .ÄrÄrÄrÄrÄ)”   ãé ¢åçÅ£Å§¢èêÅ•Å´Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ïô £ñóòÅ¶ÅßÅ®£öõ—Å©Å™ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“¢ £Ÿ\NS.uuidbytesO&˜íõqyI„à@d—dë≈ÄÇ“¶ ßŸ\NS.uuidbytesOû`˛)ÎûBÖö4cÿ)˝ÄÇ”   ™≠ ¢º¨ÄyÅ¨¢ø	OÄ{Å≤Ä|^documentLength#@0Ä     g”   µ∏ ¢‰∑ÄÜÅ∞¢ø	OÄ{Å≤Ä|^documentLength”   æ… 8™ø¿¡¬√ƒ≈∆«»Å≤Å≥Å¥ÅµÅ∂Å∑Å∏ÅπÅ∫Åª™ ñ 0 0Œß . 0“ñÅºÄàÄsÄsÅæÅ±ÄrÄsÅ ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ‡ S°·ÅΩÄ)“  ‰ S§»[»[ÅãÅÖÅãÅÖÄ)”   ÎÓ ¢ÏÌÅøÅ¿¢ÔÅ¡ÅƒÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ı¯ ¢º˜ÄyÅ¬¢ø˙Ä{Å√Ä|^documentLengthb”   ˇ £ Å≈Å∆Å«£—Å»Å…ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ Ÿ\NS.uuidbytesO◊lHn˘åN[òg•≥ö·g)ÄÇ“ Ÿ\NS.uuidbytesOên9âÅL{éÓÀ,„ÄÇ”    ¢‰ÄÜÅÀ¢ø˙Ä{Å√Ä|^documentLength”   ( 8™ !"#$%&'ÅÕÅŒÅœÅ–Å—Å“Å”Å‘Å’Å÷™)ñ 0 0-ß . 01ñÅ◊ÄàÄsÄsÅ⁄Å±ÄrÄsÅÊÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ? S°@ÅÿÄ)“  C S§DˆDˆÅŸÅdÅŸÅdÄ)*”   KN ¢LMÅ€Å‹¢OPÅ›Å‡Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   UX ¢VºÅﬁÄy¢Y	ÅﬂÅÄ|^documentLengthV”   _c £`abÅ·Å‚Å„£—efÄÑÅ‰ÅÂÄ|_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore“l mŸ\NS.uuidbytesOKÛhùïÚEÕñùë∏\"ÊÄÇ“p qŸ\NS.uuidbytesO˛Ë\ı9L’µj3∑¢˙ÄÇ”   tw ¢‰vÄÜÅÁ¢	YÅÅﬂÄ|^documentLength”   }à 8™~ÄÅÇÉÑÖÜáÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚ™âñ 0 0ç* . 0ëñÅÛÄàÄsÄsÅıÄSÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ü S°†ÅÙÄ)“  £ S§ . . . .ÄrÄrÄrÄrÄ)”   ™≠ ¢´¨ÅˆÅ˜¢ÆØÅ¯Å˚Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ¥∑ ¢µºÅ˘Äy¢∏øÅ˙Ä{Ä|^documentLengthg”   æ¬ £ø¿¡Å¸Å˝Å˛£√ƒ—ÅˇÅ ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“À ÃŸ\NS.uuidbytesOhvéÜPêH'ØIKÌØ«ÏÄÇ“œ –Ÿ\NS.uuidbytesOY˜#« Bﬂ´A·v
<JPÄÇ”   ”÷ ¢‰’ÄÜÅ¢ø∏Ä{Å˙Ä|^documentLength”   ‹Á 8™›ﬁﬂ‡·‚„‰ÂÊÅÅÅÅÅÅ	Å
ÅÅÅ™Ëñ 0 0Ï* . 0ñÅÄàÄsÄsÅÄSÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ˛ S°ˇÅÄ)“   S§ΩΩÅ§ÅﬁÅ§ÅﬁÄ)”   	 ¢
ÅÅ¢ÅÅÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢ºÄyÅ¢øÄ{ÅÄ|^documentLength≥”   ! £ ÅÅÅ£"#—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“* +Ÿ\NS.uuidbytesO∂∞ûónD)¥ å“„<´_ÄÇ“. /Ÿ\NS.uuidbytesOÈê¨§>EÿîHÚ1ã†GÄÇ”   25 ¢‰4ÄÜÅ¢øÄ{ÅÄ|^documentLength”   ;F 8™<=>?@ABCDEÅÅ Å!Å"Å#Å$Å%Å&Å'Å(™Gñ 0 0Kß . 0OñÅ)ÄàÄsÄsÅ,Å±ÄrÄsÅ8ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ] S°^Å*Ä)“  a S§b[b[Å+ÅÖÅ+ÅÖÄ)7”   il ¢jkÅ-Å.¢mnÅ/Å2Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   sv ¢ºuÄyÅ0¢øxÄ{Å1Ä|^documentLength·”   }Å £~ÄÅ3Å4Å5£ÇÉ—Å6Å7ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ä ãŸ\NS.uuidbytesO˜ãY…E’¢^ÉóäÖ}ÄÇ“é èŸ\NS.uuidbytesOO±BB„H≤§—ÜdVÍ% ÄÇ”   íï ¢‰îÄÜÅ9¢øxÄ{Å1Ä|^documentLength”   õ¶ 8™úùûü†°¢£§•Å;Å<Å=Å>Å?Å@ÅAÅBÅCÅD™ßñ 0 0´ß . 0ØñÅEÄàÄsÄsÅIÅ±ÄrÄsÅUÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ω S°æÅFÄ)“  ¡ S§¬√¬√ÅGÅHÅGÅHÄ)41”    Õ ¢ÀÃÅJÅK¢ŒœÅLÅRÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ‘ÿ £’÷◊ÅMÅNÅO£Ÿ⁄—ÅPÅQÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“· ‚Ÿ\NS.uuidbytesOË@Ç<™ÕE3¨eKI†ƒ∆ÄÇ“Â ÊŸ\NS.uuidbytesO˚'Bf∫®ÓÖsu>ÄÇ”   ÈÏ ¢ÍºÅSÄy¢ÌøÅTÄ{Ä|^documentLength˜”   Ûˆ ¢‰ıÄÜÅV¢øÌÄ{ÅTÄ|^documentLength”   ¸ 8™˝˛ˇ ÅXÅYÅZÅ[Å\Å]Å^Å_Å`Åa™ñ 0 0ß . 0ñÅbÄàÄsÄsÅdÅ±ÄrÄsÅpÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅcÄ)“  " S§ . . . .ÄrÄrÄrÄrÄ)”   ), ¢*+ÅeÅf¢-.ÅgÅmÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   37 £456ÅhÅiÅj£89—ÅkÅlÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“@ AŸ\NS.uuidbytesOÒ,ﬂÖcD9Ö»Œx…‹@ÄÇ“D EŸ\NS.uuidbytesO…pg±AfáK"Ç)ÄÇ”   HK ¢ºJÄyÅn¢øMÄ{ÅoÄ|^documentLengthM”   RU ¢‰TÄÜÅq¢øMÄ{ÅoÄ|^documentLength”   [f 8™\]^_`abcdeÅsÅtÅuÅvÅwÅxÅyÅzÅ{Å|™gñ 0 0kß . 0oñÅ}ÄàÄsÄsÅÄÅ±ÄrÄsÅåÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  } S°~Å~Ä)“  Å S§ÇæÇæÅÄzÅÄzÄ)2”   âå ¢äãÅÅÅÇ¢çéÅÉÅâÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ìó £îïñÅÑÅÖÅÜ£òô—ÅáÅàÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“† °Ÿ\NS.uuidbytesOü Ì∂≥EøöRÒîVäó:ÄÇ“§ •Ÿ\NS.uuidbytesO:h
ÊEﬁ£ ã™˜sıÄÇ”   ®´ ¢º™ÄyÅä¢ø≠Ä{ÅãÄ|^documentLength	”   ≤µ ¢‰¥ÄÜÅç¢ø≠Ä{ÅãÄ|^documentLength”   ª∆ 8™ºΩæø¿¡¬√ƒ≈ÅèÅêÅëÅíÅìÅîÅïÅñÅóÅò™«ñ 0 0À* . 0œñÅôÄàÄsÄsÅõÄSÄrÄsÅßÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  › S°ﬁÅöÄ)“  · S§ . . . .ÄrÄrÄrÄrÄ)”   ËÎ ¢ÈÍÅúÅù¢ÏÌÅûÅ°Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   Úı ¢ºÙÄyÅü¢ø˜Ä{Å†Ä|^documentLength”   ¸  £˝˛ˇÅ¢Å£Å§£—Å•Å¶ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“	 
Ÿ\NS.uuidbytesOêÉ¡y£ÍJ)è]èù$EÄÇ“ Ÿ\NS.uuidbytesOwuÕ•≈MOøS€ÍD-º
ÄÇ”    ¢‰ÄÜÅ®¢ø˜Ä{Å†Ä|^documentLength”   % 8™ !"#$Å™Å´Å¨Å≠ÅÆÅØÅ∞Å±Å≤Å≥™&ñ 0 0*ß . 0.ñÅ¥ÄàÄsÄsÅ∑Å±ÄrÄsÅ√ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  < S°=ÅµÄ)“  @ S§A¬A¬Å∂ÅGÅ∂ÅGÄ)P”   HK ¢IJÅ∏Åπ¢LMÅ∫ÅΩÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   RU ¢ºTÄyÅª¢øWÄ{ÅºÄ|^documentLength ”   \` £]^_ÅæÅøÅ¿£ab—Å¡Å¬ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“i jŸ\NS.uuidbytesO}v6˘BÅäwx$RgßBÄÇ“m nŸ\NS.uuidbytesO&WfØ}@Ù±ÿr…î¯œŸÄÇ”   qt ¢‰sÄÜÅƒ¢øWÄ{ÅºÄ|^documentLength”   zÖ 8™{|}~ÄÅÇÉÑÅ∆Å«Å»Å…Å ÅÀÅÃÅÕÅŒÅœ™Üñ 0 0äß . 0éñÅ–ÄàÄsÄsÅ“Å±ÄrÄsÅﬁÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ú S°ùÅ—Ä)“  † S§ . . . .ÄrÄrÄrÄrÄ)”   ß™ ¢®©Å”Å‘¢´¨Å’ÅÿÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ±¥ ¢º≥ÄyÅ÷¢ø∂Ä{Å◊Ä|^documentLength±”   ªø £ºΩæÅŸÅ⁄Å€£¿¡—Å‹Å›ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“» …Ÿ\NS.uuidbytesO&˛,ïîãKÃ°”˛MT°EÔÄÇ“Ã ÕŸ\NS.uuidbytesOú∆#Ôè?JÉ¢Û¯ËÇ—,ÄÇ”   –” ¢‰“ÄÜÅﬂ¢ø∂Ä{Å◊Ä|^documentLength”   Ÿ‰ 8™⁄€‹›ﬁﬂ‡·‚„Å·Å‚Å„Å‰ÅÂÅÊÅÁÅËÅÈÅÍ™Âñ 0 0Èß . 0ÌñÅÎÄàÄsÄsÅÅ±ÄrÄsÅ¸ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ˚ S°¸ÅÏÄ)“  ˇ S§  ÅÌÅÓÅÌÅÔÄ)(”   	 ¢
ÅÒÅÚ¢ÅÛÅˆÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢ºÅÙÄy¢øÅıÄ{Ä|^documentLength”   ! £ Å˜Å¯Å˘£"#—Å˙Å˚ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“* +Ÿ\NS.uuidbytesO≤“ZD F§´Ve‚>°√ÄÇ“. /Ÿ\NS.uuidbytesOUÚµı–A¸¨;—Á.VÄÇ”   25 ¢‰4ÄÜÅ˝¢øÄ{ÅıÄ|^documentLength”   ;F 8™<=>?@ABCDEÅˇÅ ÅÅÅÅÅÅÅÅ™Gñ 0 0Kß . 0OñÅ	ÄàÄsÄsÅÅ±ÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ] S°^Å
Ä)“  a S§ . . . .ÄrÄrÄrÄrÄ)”   hk ¢ijÅÅ¢lmÅÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   rv £stuÅÅÅ£wx—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ ÄŸ\NS.uuidbytesO;d1∂I∂ë˝LÂX5ÄÇ“É ÑŸ\NS.uuidbytesOp&â 1∫JƒàŒ[1ôí€ÄÇ”   áä ¢ºâÄyÅ¢ø{Ä{ÅuÄ|^documentLength”   êì ¢‰íÄÜÅ¢ø{Ä{ÅuÄ|^documentLength”   ô• 8´öõúùûü†°¢£§ÅÅÅÅÅÅÅÅ Å!Å"Å#´¶ñ 0 0™ß¨ . 0ØñÅ$ÄàÄsÄsÅ&Å±Å2ÄrÄsÅCÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  æ S°øÅ%Ä)“  ¬ S§ˆΩˆΩÅdÅ§ÅdÅ§Ä)”   …Ã ¢ ÀÅ'Å(¢ÕŒÅ)Å,Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ”÷ ¢º’ÄyÅ*¢	ÿÅÅ+Ä|^documentLength%”   ›· £ﬁﬂ‡Å-Å.Å/£—„‰ÄÑÅ0Å1Ä|_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“Í ÎŸ\NS.uuidbytesOT¨FÕÃ…A¿É˝¨ﬂf√¨ÄÇ“Ó ÔŸ\NS.uuidbytesOsMô€™DuñÒÇ¸^kÁÄÇ”   Ú˘ 8¶ÛÙıˆ˜¯Å3Å4Å5Å6Å7Å8¶) 0¸˝˛ˇÄRÄsÅ9Å>Å?ÅBÄ@_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_Previews.Editor.CanvasMode_XcodePreviews.PinnedPreview_Previews.Editor.DestinationMode_Previews.Editor.Overrides”    ¢	
Å:Å;¢Å<Å=Ä|Vtarget\discriminantUlocalTliveP”    °Å@°ÅAÄ|\discriminantYautomatic”    ††Ä|”   !$ ¢‰#ÄÜÅD¢	ÿÅÅ+Ä|^documentLength”   *5 8™+,-./01234ÅFÅGÅHÅIÅJÅKÅLÅMÅNÅO™6ñ 0 0:;< 0>ñÅPÄàÄsÄsÅSÅ^Å_ÄsÅ`ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  L S°MÅQÄ)“  P S§QÄQ§ÅRÅTÅRÅ5Ä),”   X[ ¢YZÅTÅU¢\]ÅVÅ\Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   bf £cdeÅWÅXÅY£gh—ÅZÅ[ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“o pŸ\NS.uuidbytesO	⁄≤í˚NàØë+ãÄÇ“s tŸ\NS.uuidbytesO¸Ωÿ’Mèß“56ên{˛ÄÇ”   wz ¢ºyÄyÅ]¢øYÄ{ÅﬂÄ|^documentLength#@.      ”   ÇÖ ¢‰ÑÄÜÅa¢øYÄ{ÅﬂÄ|^documentLength”   ãñ 8™åçéèêëíìîïÅcÅdÅeÅfÅgÅhÅiÅjÅkÅl™óñ 0 0õß . 0üñÅmÄàÄsÄsÅoÅ±ÄrÄsÅzÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ≠ S°ÆÅnÄ)“  ± S§ﬁ .ﬁ .Å˘ÄrÅ˘ÄrÄ)”   ∏ª ¢π∫ÅpÅq¢ºΩÅrÅxÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ¬∆ £√ƒ≈ÅsÅtÅu£«»—ÅvÅwÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“œ –Ÿ\NS.uuidbytesOO◊sYM∆¥†˛†QIµÄÇ“” ‘Ÿ\NS.uuidbytesO±E ¬L)à‡˚Zr¡ñÄÇ”   ◊⁄ ¢ÿºÅyÄy¢·øÅtÄ{Ä|^documentLength”   ‡„ ¢‰‚ÄÜÅ{¢ø·Ä{ÅtÄ|^documentLength”   ÈÙ 8™ÍÎÏÌÓÔÒÚÛÅ}Å~ÅÅÄÅÅÅÇÅÉÅÑÅÖÅÜ™ıñ 0 0˘* . 0˝ñÅáÄàÄsÄsÅâÄSÄrÄsÅïÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅàÄ)“   S§ . . . .ÄrÄrÄrÄrÄ)”    ¢ÅäÅã¢ÅåÅèÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    # ¢!ºÅçÄy¢$øÅéÄ{Ä|^documentLength3Ù”   *. £+,-ÅêÅëÅí£/0—ÅìÅîÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“7 8Ÿ\NS.uuidbytesO∂~ïèRk@åó¬ëvKÕÑÄÇ“; <Ÿ\NS.uuidbytesO'^#¿J|çQWÏåﬂÂÄÇ”   ?B ¢‰AÄÜÅñ¢ø$Ä{ÅéÄ|^documentLength”   HS 8™IJKLMNOPQRÅòÅôÅöÅõÅúÅùÅûÅüÅ†Å°™Tñ 0 0XY§ 0\ñÅ¢ÄàÄsÄsÅ¶Å±Å5ÄsÅ≤ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  j S°kÅ£Ä)“  n S§opopÅ§Å•Å§Å•Ä)5”   wz ¢xyÅßÅ®¢{|Å©ÅØÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ÅÖ £ÇÉÑÅ™Å´Å¨£Üá—Å≠ÅÆÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“é èŸ\NS.uuidbytesOØtB ˜ÑBxº|^¬y;ÄÇ“í ìŸ\NS.uuidbytesO€vBRËLäπ +a›JÈÄÇ”   ñô ¢ºòÄyÅ∞¢øYÄ{ÅﬂÄ|^documentLength#@      ”   †£ ¢‰¢ÄÜÅ≥¢øYÄ{ÅﬂÄ|^documentLength”   ©¥ 8™™´¨≠ÆØ∞±≤≥ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩÅæ™µñ 0 0π* . 0ΩñÅøÄàÄsÄsÅ¡ÄSÄrÄsÅÃÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  À S°ÃÅ¿Ä)“  œ S§CoCoÅÅ§ÅÅ§Ä)”   ÷Ÿ ¢◊ÿÅ¬Å√¢⁄€ÅƒÅ Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ‡‰ £·‚„Å≈Å∆Å«£ÂÊ—Å»Å…ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ì ÓŸ\NS.uuidbytesO™àF-ÿG∑êa&ÚeƒDﬁÄÇ“Ò ÚŸ\NS.uuidbytesO/Æ±‘ßf@b†ÃçW\_˜lÄÇ”   ı¯ ¢º˜ÄyÅÀ¢ø“Ä{Å™Ä|^documentLength”   ˛ ¢‰ ÄÜÅÕ¢ø“Ä{Å™Ä|^documentLength”    8™	
ÅœÅ–Å—Å“Å”Å‘Å’Å÷Å◊Åÿ™ñ 0 0* . 0ñÅŸÄàÄsÄsÅ€ÄSÄrÄsÅÊÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ) S°*Å⁄Ä)“  - S§ . . . .ÄrÄrÄrÄrÄ)”   47 ¢56Å‹Å›¢89ÅﬁÅ‡Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   >A ¢º@ÄyÅﬂ¢	ÖÅÅíÄ|^documentLength”   GK £HIJÅ·Å‚Å„£—MNÄÑÅ‰ÅÂÄ|_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“T UŸ\NS.uuidbytesOÏt9≥+G•ê±£@à`ƒäÄÇ“X YŸ\NS.uuidbytesO‡]–Õ{ JZßì&ÔˆÚOÄÇ”   \_ ¢‰^ÄÜÅÁ¢	ÖÅÅíÄ|^documentLength”   ep 8™fghijklmnoÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚ™qñ 0 0uY 0yñÅÛÄàÄsÄsÅˆÅ±Å¬ÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  á S°àÅÙÄ)“  ã S§å˜å˜ÅıÅeÅıÅeÄ)#”   ìñ ¢îïÅ˜Å¯¢óòÅ˘ÅˇÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ù° £ûü†Å˙Å˚Å¸£¢£—Å˝Å˛ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“™ ´Ÿ\NS.uuidbytesOdı—ôOﬁ∏Øòi√¨≤ÄÇ“Æ ØŸ\NS.uuidbytesO« qèp∂Ho§ú[£ÆY¯ÄÇ”   ≤µ ¢≥ºÅ Äy¢∂øÅÄ{Ä|^documentLength†”   ºø ¢‰æÄÜÅ¢ø∂Ä{ÅÄ|^documentLength”   ≈– 8™∆«»… ÀÃÕŒœÅÅÅÅÅ	Å
ÅÅÅÅ™—ñ 0 0’÷◊ 0ŸñÅÄàÄsÄsÅÅÅÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Á S°ËÅÄ)“  Î S§ÏÇÏÇÅÅÅÅÄ))”   Ûˆ ¢ÙıÅÅ¢˜¯ÅÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ˝ £˛ˇ ÅÅÅ£—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“
 Ÿ\NS.uuidbytesO”§Û>H3∞ı&éÚÔ•ÌÄÇ“ Ÿ\NS.uuidbytesO˝µ»&DÙC¢ööÌ
¥ºÄÇ”    ¢ºÅÄy¢yøÅSÄ{Ä|^documentLength#@       ”     ¢‰ÄÜÅ ¢øyÄ{ÅSÄ|^documentLength”   &1 8™'()*+,-./0Å"Å#Å$Å%Å&Å'Å(Å)Å*Å+™2ñ 0 06Ô8 0:ñÅ,ÄàÄsÄsÅ.Å_Å:ÄsÅ;ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  H S°IÅ-Ä)“  L S§ﬁ[p .Å˘ÅÖÅ•ÄrÄ)”   SV ¢TUÅ/Å0¢WXÅ1Å4Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ]` ¢^ºÅ2Äy¢aøÅ3Ä{Ä|^documentLength{”   gk £hijÅ5Å6Å7£lm—Å8Å9ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“t uŸ\NS.uuidbytesO§¸ˆÿWD¬ñ1HaÛ√ÎÄÇ“x yŸ\NS.uuidbytesO›se}AbO‚û;çt'∑˜WÄÇ”   }Ä ¢‰ÄÜÅ<¢øaÄ{Å3Ä|^documentLength”   Üë 8™áàâäãåçéèêÅ>Å?Å@ÅAÅBÅCÅDÅEÅFÅG™íñ 0 0ñóò 0öñÅHÄàÄsÄsÅLÅXÅYÄsÅZÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ® S°©ÅIÄ)“  ¨ S§≠Æ≠ÆÅJÅKÅJÅKÄ)0;”   µ∏ ¢∂∑ÅMÅN¢π∫ÅOÅRÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ø¬ ¢º¡ÄyÅP¢øƒÄ{ÅQÄ|^documentLength—”   …Õ £ ÀÃÅSÅTÅU£Œœ—ÅVÅWÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“÷ ◊Ÿ\NS.uuidbytesO°ˇ[ö‡ÿBõå5≠Ñ≤?üÄÇ“⁄ €Ÿ\NS.uuidbytesOeÆÛ!îıC µüí¨≤Ÿ,ÄÇ#@0Ä      ”   ‡„ ¢‰‚ÄÜÅ[¢øƒÄ{ÅQÄ|^documentLength”   ÈÙ 8™ÍÎÏÌÓÔÒÚÛÅ]Å^Å_Å`ÅaÅbÅcÅdÅeÅf™ıñ 0 0˘ß . 0˝ñÅgÄàÄsÄsÅkÅ±ÄrÄsÅwÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅhÄ)“   S§ÅiÅjÅiÅjÄ)@”    ¢ÅlÅm¢ÅnÅqÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   "% ¢º$ÄyÅo¢ø'Ä{ÅpÄ|^documentLengthG”   ,0 £-./ÅrÅsÅt£12—ÅuÅvÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“9 :Ÿ\NS.uuidbytesO¢‚G®AŒµ∫g˛‰UÃZÄÇ“= >Ÿ\NS.uuidbytesOf‚O]kGß ÄXq ≠˜ÄÇ”   AD ¢‰CÄÜÅx¢ø'Ä{ÅpÄ|^documentLength”   JU 8™KLMNOPQRSTÅzÅ{Å|Å}Å~ÅÅÄÅÅÅÇÅÉ™Vñ 0 0Zß . 0^ñÅÑÄàÄsÄsÅáÅ±ÄrÄsÅìÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  l S°mÅÖÄ)“  p S§qåqåÅÜÅıÅÜÅıÄ)”   x{ ¢yzÅàÅâ¢|}ÅäÅêÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ÇÜ £ÉÑÖÅãÅåÅç£áà—ÅéÅèÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“è êŸ\NS.uuidbytesO}ja˚µJ≈ÉBÿ;4*3ÅÄÇ“ì îŸ\NS.uuidbytesO0ñ#ÜÜA òj\3∑óWÄÇ”   óö ¢ºôÄyÅë¢øúÄ{ÅíÄ|^documentLengthE”   °§ ¢‰£ÄÜÅî¢øúÄ{ÅíÄ|^documentLength”   ™µ 8™´¨≠ÆØ∞±≤≥¥ÅñÅóÅòÅôÅöÅõÅúÅùÅûÅü™∂ñ 0 0∫ß . 0æñÅ†ÄàÄsÄsÅ§Å±ÄrÄsÅØÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ã S°ÕÅ°Ä)“  – S§—“—“Å¢Å£Å¢Å£Ä)°+”   Ÿ‹ ¢⁄€Å•Å¶¢›ﬁÅßÅ©Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   „Ê ¢ºÂÄyÅ®¢ø$Ä{ÅéÄ|^documentLength”   Ï £ÌÓÔÅ™Å´Å¨£ÒÚ—Å≠ÅÆÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“˘ ˙Ÿ\NS.uuidbytesO"–qBG‹ΩXw…wîjÄÇ“˝ ˛Ÿ\NS.uuidbytesOê}2’—∫N¯â+ì¯KqˇmÄÇ”    ¢‰ÄÜÅ∞¢ø$Ä{ÅéÄ|^documentLength”   
 8™Å≤Å≥Å¥ÅµÅ∂Å∑Å∏ÅπÅ∫Åª™ñ 0 0ß . 0ñÅºÄàÄsÄsÅøÅ±ÄrÄsÅÀÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  , S°-ÅΩÄ)“  0 S§ﬁ[3 .Å˘ÅÖÅæÄrÄ)≠”   8; ¢9:Å¿Å¡¢<=Å¬Å≈Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   BE ¢CºÅ√Äy¢FøÅƒÄ{Ä|^documentLengthq”   LP £MNOÅ∆Å«Å»£QR—Å…Å ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Y ZŸ\NS.uuidbytesOØüﬂ‰pJº≥iŸ˙‹‚‘√ÄÇ“] ^Ÿ\NS.uuidbytesO¢ó ÛÉÍOÄÓïﬂ3˝˜›ÄÇ”   ad ¢‰cÄÜÅÃ¢øFÄ{ÅƒÄ|^documentLength”   ju 8™klmnopqrstÅŒÅœÅ–Å—Å“Å”Å‘Å’Å÷Å◊™vñ 0 0zß . 0~ñÅÿÄàÄsÄsÅ⁄Å±ÄrÄsÅÊÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  å S°çÅŸÄ)“  ê S§ . . . .ÄrÄrÄrÄrÄ)”   óö ¢òôÅ€Å‹¢õúÅ›Å‡Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   °§ ¢º£ÄyÅﬁ¢ø¶Ä{ÅﬂÄ|^documentLength¯”   ´Ø £¨≠ÆÅ·Å‚Å„£∞±—Å‰ÅÂÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“∏ πŸ\NS.uuidbytesO´(níOyN™ã≠òËd—sÄÇ“º ΩŸ\NS.uuidbytesOÉÌ_i	ñAä∏W]f≠ÒÄÇ”   ¿√ ¢‰¬ÄÜÅÁ¢ø¶Ä{ÅﬂÄ|^documentLength”   …‘ 8™ ÀÃÕŒœ–—“”ÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚ™’ñ 0 0Ÿ* . 0›ñÅÛÄàÄsÄsÅıÄSÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Î S°ÏÅÙÄ)“  Ô S§ . . . .ÄrÄrÄrÄrÄ)”   ˆ˘ ¢˜¯ÅˆÅ˜¢˙˚Å¯Å˚Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”     ¢ºÅ˘Äy¢øÅ˙Ä{Ä|^documentLengthU”   
 £Å¸Å˝Å˛£—ÅˇÅ ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ Ÿ\NS.uuidbytesO6¯–ìœ@ûµ1ki’~LÄÇ“ Ÿ\NS.uuidbytesO™ B≥OC¶»ÆgÜÇzÄÇ”   " ¢‰!ÄÜÅ¢øÄ{Å˙Ä|^documentLength”   (3 8™)*+,-./012ÅÅÅÅÅÅ	Å
ÅÅÅ™4ñ 0 08ß . 0<ñÅÄàÄsÄsÅÅ±ÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  J S°KÅÄ)“  N S§ . . . .ÄrÄrÄrÄrÄ)”   UX ¢VWÅÅ¢YZÅÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   _c £`abÅÅÅ£de—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“l mŸ\NS.uuidbytesO*ExVÂE Ö®>˚ DÄÇ“p qŸ\NS.uuidbytesOo{Q'πƒOÛü4Z ùV9∞ÄÇ”   tw ¢uºÅÄy¢æøÄzÄ{Ä|^documentLength”   }Ä ¢‰ÄÜÅ¢øæÄ{ÄzÄ|^documentLength”   Üí 8´áàâäãåçéèêëÅÅÅ Å!Å"Å#Å$Å%Å&Å'Å(´ìñ 0 0óôÏ 0úñÅ)ÄàÄsÄsÅ,ÅSÅ8ÅÄsÅDÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_(IDEEditorPreviewModeAuxiliaryEditorState_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ´ S°¨Å*Ä)“  Ø S§≈±≈±ÅjÅ+ÅjÅ+Ä)”   ∑∫ ¢∏πÅ-Å.¢ªºÅ/Å2Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ¡ƒ ¢º√ÄyÅ0¢ø∆Ä{Å1Ä|^documentLength é”   Àœ £ÃÕŒÅ3Å4Å5£–——Å6Å7ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ÿ ŸŸ\NS.uuidbytesOA∞ä’ºItî≤y™"aÏÄÇ“‹ ›Ÿ\NS.uuidbytesO/eáACÂØQ„*c˘t ÄÇ”   ‡Á 8¶·‚„‰ÂÊÅ9Å:Å;Å<Å=Å>¶) 0Í˝ÏÌÄRÄsÅ?Å>ÅAÅBÄ@_XcodePreviews.Zoom_XcodePreviews.ShowViewBounds_Previews.Editor.CanvasMode_XcodePreviews.PinnedPreview_Previews.Editor.Overrides_Previews.Editor.DestinationMode”   ˆ˘ ¢	¯Å:Å@¢Å<Å=Ä|\discriminant”   ˇ  ††Ä|”    °ÅC°ÅAÄ|\discriminant”   
 ¢‰ÄÜÅE¢ø∆Ä{Å1Ä|^documentLength”    8™ÅGÅHÅIÅJÅKÅLÅMÅNÅOÅP™ñ 0 0#Y% 0'ñÅQÄàÄsÄsÅTÅ±Å`ÄsÅaÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  5 S°6ÅRÄ)“  9 S§ﬁ[< .Å˘ÅÖÅSÄrÄ)é”   AD ¢BCÅUÅV¢EFÅWÅ]Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   KO £LMNÅXÅYÅZ£PQ—Å[Å\ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“X YŸ\NS.uuidbytesO=dªÇ¸Mˆ§˛O–•ëBÄÇ“\ ]Ÿ\NS.uuidbytesO—G5∆¿JË∫TrkÕ€ÆÄÇ”   `c ¢aºÅ^Äy¢døÅ_Ä{Ä|^documentLengthm”   kn ¢‰mÄÜÅb¢ødÄ{Å_Ä|^documentLength”   t 8™uvwxyz{|}~ÅdÅeÅfÅgÅhÅiÅjÅkÅlÅm™Äñ 0 0ÑÔÜ 0àñÅnÄàÄsÄsÅrÅ_Å~ÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ñ S°óÅoÄ)“  ö S§ÇùûÅÅ¡ÅpÅqÄ)6”   £¶ ¢§•ÅsÅt¢ß®ÅuÅ{Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   ≠± £ÆØ∞ÅvÅwÅx£≤≥—ÅyÅzÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“∫ ªŸ\NS.uuidbytesO∫·Ü+9A±∑»C\`kcÄÇ“æ øŸ\NS.uuidbytesOµf@ŒàA◊ÅåU+uYM∂ÄÇ”   ¬≈ ¢ºƒÄyÅ|¢ø«Ä{Å}Ä|^documentLengthK”   Õ– ¢‰œÄÜÅÄ¢ø«Ä{Å}Ä|^documentLength”   ÷· 8™◊ÿŸ⁄€‹›ﬁﬂ‡ÅÇÅÉÅÑÅÖÅÜÅáÅàÅâÅäÅã™‚ñ 0 0Ê‡Ë 0ÍñÅåÄàÄsÄsÅèÅsÅõÄsÅúÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ¯ S°˘ÅçÄ)“  ¸ S§˝æ˝æÅéÄzÅéÄzÄ)u”    ¢ÅêÅë¢	ÅíÅïÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ¢ºÄyÅì¢øÄ{ÅîÄ|^documentLength∂”    £ÅñÅóÅò£—ÅôÅöÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“% &Ÿ\NS.uuidbytesO‰Ï˚XZ∑M∑GòáïôZ2ÄÇ“) *Ÿ\NS.uuidbytesO/z~g*K"∏Ó!–1-˛ÄÇc”   .1 ¢‰0ÄÜÅù¢øÄ{ÅîÄ|^documentLength”   7B 8™89:;<=>?@AÅüÅ†Å°Å¢Å£Å§Å•Å¶ÅßÅ®™Cñ 0 0Gß . 0KñÅ©ÄàÄsÄsÅ´Å±ÄrÄsÅ∑ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Y S°ZÅ™Ä)“  ] S§òòÅ¡ÅYÅ¡ÅYÄ)”   dg ¢efÅ¨Å≠¢hiÅÆÅ±Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   nq ¢oºÅØÄy¢røÅ∞Ä{Ä|^documentLength”   x| £yz{Å≤Å≥Å¥£}~—ÅµÅ∂ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ö ÜŸ\NS.uuidbytesO‘Ì˝-¢bF1âS!œXNÄÇ“â äŸ\NS.uuidbytesO¡u”ÒsMHE≠¨_®+πà&ÄÇ”   çê ¢‰èÄÜÅ∏¢ørÄ{Å∞Ä|^documentLength”   ñ° 8™óòôöõúùûü†Å∫ÅªÅºÅΩÅæÅøÅ¿Å¡Å¬Å√™¢ñ 0 0¶* . 0™ñÅƒÄàÄsÄsÅ∆ÄSÄrÄsÅ—ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∏ S°πÅ≈Ä)“  º S§ . . . .ÄrÄrÄrÄrÄ)”   √∆ ¢ƒ≈Å«Å»¢«»Å…ÅÀÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   Õ– ¢ºœÄyÅ ¢	≠ÅÅãÄ|^documentLength”   ÷⁄ £◊ÿŸÅÃÅÕÅŒ£€‹—ÅœÅ–ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“„ ‰Ÿ\NS.uuidbytesO∏5CoñJ§öG/π‚QÄÇ“Á ËŸ\NS.uuidbytesO3çÊ;-¿O_∏’¶€1/˛ÄÇ”   ÎÓ ¢‰ÌÄÜÅ“¢	≠ÅÅãÄ|^documentLength”   Ùˇ 8™ıˆ˜¯˘˙˚¸˝˛Å‘Å’Å÷Å◊ÅÿÅŸÅ⁄Å€Å‹Å›™ ñ 0 0Ô 0ñÅﬁÄàÄsÄsÅ·Å_ÅÌÄsÅÓÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅﬂÄ)“   S§Ä .ÅTÅ¡Å‡ÄrÄ)Ø”   "% ¢#$Å‚Å„¢&'Å‰ÅÁÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ,/ ¢º.ÄyÅÂ¢ø1Ä{ÅÊÄ|^documentLength–”   6: £789ÅËÅÈÅÍ£;<—ÅÎÅÏÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“C DŸ\NS.uuidbytesOYKÓ~kﬂI¯óÓŒIlQ;ÄÇ“G HŸ\NS.uuidbytesO˝Iºk≤˙B‰©}'5“¨ÄÇò”   LO ¢‰NÄÜÅÔ¢ø1Ä{ÅÊÄ|^documentLength”   U` 8™VWXYZ[\]^_ÅÒÅÚÅÛÅÙÅıÅˆÅ˜Å¯Å˘Å˙™añ 0 0eß . 0iñÅ˚ÄàÄsÄsÅ˛Å±ÄrÄsÅ
ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  w S°xÅ¸Ä)“  { S§|√|√Å˝ÅHÅ˝ÅHÄ)"”   ÉÜ ¢ÑÖÅˇÅ ¢áàÅÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   çë £éèêÅÅÅ£íì—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ö õŸ\NS.uuidbytesOÄ”∆Ø-NzÆ22qY◊:ÈÄÇ“û üŸ\NS.uuidbytesOe\á$´I+π+Á}+µãÄÇ”   ¢• ¢£ºÅÄy¢¶øÅ	Ä{Ä|^documentLengthˇ”   ¨Ø ¢‰ÆÄÜÅ¢ø¶Ä{Å	Ä|^documentLength”   µ¿ 8™∂∑∏π∫ªºΩæøÅÅÅÅÅÅÅÅÅÅ™¡ñ 0 0≈* . 0…ñÅÄàÄsÄsÅÄSÄrÄsÅ$ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ◊ S°ÿÅÄ)“  € S§ . . . .ÄrÄrÄrÄrÄ)”   ‚Â ¢„‰ÅÅ¢ÊÁÅÅ"Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   Ï £ÌÓÔÅÅÅ£ÒÚ—Å Å!ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“˘ ˙Ÿ\NS.uuidbytesOñÚ NMB≥ûnùSÄÇ“˝ ˛Ÿ\NS.uuidbytesO‹Ô⁄–øD,ÜÂ˝Ãºd~ÄÇ”    ¢ºÄyÅ#¢øªÄ{Å<Ä|^documentLength”   
 ¢‰ÄÜÅ%¢øªÄ{Å<Ä|^documentLength”    8™Å'Å(Å)Å*Å+Å,Å-Å.Å/Å0™ñ 0 0#* . 0'ñÅ1ÄàÄsÄsÅ3ÄSÄrÄsÅ?ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  5 S°6Å2Ä)“  9 S§ . . . .ÄrÄrÄrÄrÄ)”   @C ¢ABÅ4Å5¢DEÅ6Å<Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   JN £KLMÅ7Å8Å9£OP—Å:Å;ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“W XŸ\NS.uuidbytesO=Uı≤ÌFçú˚ùûò£¡ÓÄÇ“[ \Ÿ\NS.uuidbytesOb;2“ŸEª∫≤/sÔÛuÄÇ”   _b ¢`ºÅ=Äy¢cøÅ>Ä{Ä|^documentLengthÑ”   il ¢‰kÄÜÅ@¢øcÄ{Å>Ä|^documentLength”   r} 8™stuvwxyz{|ÅBÅCÅDÅEÅFÅGÅHÅIÅJÅK™~ñ 0 0Çß . 0ÜñÅLÄàÄsÄsÅNÅ±ÄrÄsÅZÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  î S°ïÅMÄ)“  ò S§Ä .˘ .ÅTÄrÅfÄrÄ)”   ü¢ ¢†°ÅOÅP¢£§ÅQÅTÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ©¨ ¢º´ÄyÅR¢	ÆÅÅSÄ|^documentLengthá”   ≥∑ £¥µ∂ÅUÅVÅW£∏π—ÅXÅYÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“¿ ¡Ÿ\NS.uuidbytesO,Qã°3TD¯†∑”µj-gÄÇ“ƒ ≈Ÿ\NS.uuidbytesOœ4π^ÓTAﬁÜ÷{´P9ÄÇ”   »À ¢‰ ÄÜÅ[¢	ÆÅÅSÄ|^documentLength”   —‹ 8™“”‘’÷◊ÿŸ⁄€Å]Å^Å_Å`ÅaÅbÅcÅdÅeÅf™›ñ 0 0·‚„ 0ÂñÅgÄàÄsÄsÅkÅwÅxÄsÅyÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Û S°ÙÅhÄ)“  ˜ S§¯ .¯˚ÅiÄrÅiÅjÄ)H:”     ¢ÅlÅm¢ÅnÅqÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   
 ¢ºÅoÄy¢øÅpÄ{Ä|^documentLength1c”    £ÅrÅsÅt£—ÅuÅvÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“! "Ÿ\NS.uuidbytesOª£O-ÏHÀ¥ÛCeˇÎøÄÇ“% &Ÿ\NS.uuidbytesO0Æc4FD†â¥≥™∞4<˙ÄÇ#@      B”   +. ¢‰-ÄÜÅz¢øÄ{ÅpÄ|^documentLength”   4? 8™56789:;<=>Å|Å}Å~ÅÅÄÅÅÅÇÅÉÅÑÅÖ™@ñ 0 0DEC 0HñÅÜÄàÄsÄsÅàÅîÅÄsÅïÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  V S°WÅáÄ)“  Z S§CCÅÅjÅÅjÄ)”   ad ¢bcÅâÅä¢efÅãÅéÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   kn ¢ºmÄyÅå¢øpÄ{ÅçÄ|^documentLengthÚ”   uy £vwxÅèÅêÅë£z{—ÅíÅìÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ç ÉŸ\NS.uuidbytesOö‡$≈¡üO8ÆtÀbŸ,UÄÇ“Ü áŸ\NS.uuidbytesOâR/ıP]@kåóÕú¸P≥ÄÇ#@0Ä     ”   ãé ¢‰çÄÜÅñ¢øpÄ{ÅçÄ|^documentLength”   îü 8™ïñóòôöõúùûÅòÅôÅöÅõÅúÅùÅûÅüÅ†Å°™†ñ 0 0§* . 0®ñÅ¢ÄàÄsÄsÅ•ÄSÄrÄsÅ±ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ∂ S°∑Å£Ä)“  ∫ S§ººÅ¡Å§Å¡Å§Ä)”   ¬≈ ¢√ƒÅ¶Åß¢∆«Å®ÅÆÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   Ã– £ÕŒœÅ©Å™Å´£—“—Å¨Å≠ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ÿ ⁄Ÿ\NS.uuidbytesOËU^q3[B∫à	.ii ◊ÄÇ“› ﬁŸ\NS.uuidbytesO£É‘bçàOpØ·ÛŸ¸a≤DÄÇ”   ·‰ ¢‚ºÅØÄy¢ÂøÅ∞Ä{Ä|^documentLength$”   ÎÓ ¢‰ÌÄÜÅ≤¢øÂÄ{Å∞Ä|^documentLength”   Ùˇ 8™ıˆ˜¯˘˙˚¸˝˛Å¥ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩ™ ñ 0 0ß . 0ñÅæÄàÄsÄsÅ¿Å±ÄrÄsÅÃÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   S°ÅøÄ)“   S§±æ±æÅ+ÄzÅ+ÄzÄ)”   !$ ¢"#Å¡Å¬¢%&Å√Å∆Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   +. ¢º-ÄyÅƒ¢ø0Ä{Å≈Ä|^documentLength˚”   59 £678Å«Å»Å…£:;—Å ÅÀÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“B CŸ\NS.uuidbytesOUFz˙["Jøõ≤∑Å7YÄÇ“F GŸ\NS.uuidbytesO3ÆÃπ+ÁC/∑ú}ﬂäÓæ®ÄÇ”   JM ¢‰LÄÜÅÕ¢ø0Ä{Å≈Ä|^documentLength”   S^ 8™TUVWXYZ[\]ÅœÅ–Å—Å“Å”Å‘Å’Å÷Å◊Åÿ™_ñ 0 0c* . 0gñÅŸÄàÄsÄsÅ€ÄSÄrÄsÅÊÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  u S°vÅ⁄Ä)“  y S§ . . . .ÄrÄrÄrÄrÄ)”   ÄÉ ¢ÅÇÅ‹Å›¢ÑÖÅﬁÅ‡Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   äç ¢ãºÅﬂÄy¢øÅ˙Ä{Ä|^documentLength”   ìó £îïñÅ·Å‚Å„£òô—Å‰ÅÂÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“† °Ÿ\NS.uuidbytesOôü°¢¿Hó™œ†èƒﬁËÄÇ“§ •Ÿ\NS.uuidbytesOüC˜”≤K‡ì†züwöwÑÄÇ”   ®´ ¢‰™ÄÜÅÁ¢øÄ{Å˙Ä|^documentLength”   ±º 8™≤≥¥µ∂∑∏π∫ªÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚ™Ωñ 0 0¡ß . 0≈ñÅÛÄàÄsÄsÅıÅ±ÄrÄsÅ	 ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ” S°‘ÅÙÄ)“  ◊ S§ØºØåÅwÅ§ÅwÅıÄ)”   ﬁ· ¢ﬂ‡ÅˆÅ˜¢‚„Å¯Å˙Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ËÎ ¢ÈºÅ˘Äy¢˙øÅ√Ä{Ä|^documentLength”   Òı £ÚÛÙÅ˚Å¸Å˝£ˆ˜—Å˛ÅˇÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“˛ ˇŸ\NS.uuidbytesOµ„MJsK†ßs¬¬˛π˙ÄÇ“ Ÿ\NS.uuidbytesOY$0A@√®ï√…Î¢πkÄÇ”   	 ¢‰ÄÜÅ	¢ø˙Ä{Å√Ä|^documentLength”    8™Å	Å	Å	Å	Å	Å	Å		Å	
Å	Å	™ñ 0 0* . 0#ñÅ	ÄàÄsÄsÅ	ÄSÄrÄsÅ	ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  1 S°2Å	Ä)“  5 S§88Å0Å:Å0Å:Ä)”   <? ¢=>Å	Å	¢@AÅ	Å	Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   FJ £GHIÅ	Å	Å	£KL—Å	Å	ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“S TŸ\NS.uuidbytesO1∞'˚J?∞+í¬ú„AâÄÇ“W XŸ\NS.uuidbytesO!*ÆïôI ©ÅÎ8µY∆ÆÄÇ”   [^ ¢\ºÅ	Äy¢_øÅ	Ä{Ä|^documentLength˛”   eh ¢‰gÄÜÅ	¢ø_Ä{Å	Ä|^documentLength”   ny 8™opqrstuvwxÅ	Å	Å	 Å	!Å	"Å	#Å	$Å	%Å	&Å	'™zñ 0 0~ÔÄ 0ÇñÅ	(ÄàÄsÄsÅ	+Å_Å	7ÄsÅ	8ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ê S°ëÅ	)Ä)“  î S§ﬁ[ó .Å˘ÅÖÅ	*ÄrÄ)=”   úü ¢ùûÅ	,Å	-¢†°Å	.Å	1Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ¶© ¢º®ÄyÅ	/¢ø´Ä{Å	0Ä|^documentLengthM”   ∞¥ £±≤≥Å	2Å	3Å	4£µ∂—Å	5Å	6ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“Ω æŸ\NS.uuidbytesO4kÄ õπDW¢«˛˜‡iËÄÇ“¡ ¬Ÿ\NS.uuidbytesO¨¥I mC∏∂ñüÌ;næÄÇ&”   ∆… ¢‰»ÄÜÅ	9¢ø´Ä{Å	0Ä|^documentLength”   œ⁄ 8™–—“”‘’÷◊ÿŸÅ	;Å	<Å	=Å	>Å	?Å	@Å	AÅ	BÅ	CÅ	D™€ñ 0 0ﬂ* . 0„ñÅ	EÄàÄsÄsÅ	GÄSÄrÄsÅ	SÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  Ò S°ÚÅ	FÄ)“  ı S§BæBæÅÄzÅÄzÄ)”   ¸ˇ ¢˝˛Å	HÅ	I¢   Å	JÅ	PÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”     
 £   	Å	KÅ	LÅ	M£  —Å	NÅ	OÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“   Ÿ\NS.uuidbytesOÑ∞@˙∫GÊ∂Vû”è«ÄÇ“   Ÿ\NS.uuidbytesOßâ Ó≤OÚì—}WKE§ÄÇ”      ¢º ÄyÅ	Q¢ø  Ä{Å	RÄ|^documentLengthˆ”    % ( ¢‰ 'ÄÜÅ	T¢ø  Ä{Å	RÄ|^documentLength”    . 9 8™ / 0 1 2 3 4 5 6 7 8Å	VÅ	WÅ	XÅ	YÅ	ZÅ	[Å	\Å	]Å	^Å	_™ :ñ 0 0 >ß . 0 BñÅ	`ÄàÄsÄsÅ	cÅ±ÄrÄsÅ	oÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   P S° QÅ	aÄ)“   T S§ Uæ UæÅ	bÄzÅ	bÄzÄ)J”    \ _ ¢ ] ^Å	dÅ	e¢ ` aÅ	fÅ	lÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”    f j £ g h iÅ	gÅ	hÅ	i£ k l—Å	jÅ	kÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“ s  tŸ\NS.uuidbytesO¥C
èA∑èNsîw:BÇÄÇ“ w  xŸ\NS.uuidbytesOìcb…¬K∏ìÜt&Q˚"ÄÇ”    { ~ ¢º }ÄyÅ	m¢ø ÄÄ{Å	nÄ|^documentLength⁄”    Ö à ¢‰ áÄÜÅ	p¢ø ÄÄ{Å	nÄ|^documentLength”    é ô 8™ è ê ë í ì î ï ñ ó òÅ	rÅ	sÅ	tÅ	uÅ	vÅ	wÅ	xÅ	yÅ	zÅ	{™ öñ 0 0 û* . 0 ¢ñÅ	|ÄàÄsÄsÅ	~ÄSÄrÄsÅ	âÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“   ∞ S° ±Å	}Ä)“   ¥ S§ooooÅ§Å§Å§Å§Ä)”    ª æ ¢ º ΩÅ	Å	Ä¢ ø ¿Å	ÅÅ	ÉÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”    ≈ » ¢º «ÄyÅ	Ç¢	ÅÅpÄ|^documentLength”    Œ “ £ œ – —Å	ÑÅ	ÖÅ	Ü£— ‘ ’ÄÑÅ	áÅ	àÄ|_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore“ €  ‹Ÿ\NS.uuidbytesO)]ôG√∂€÷?o'\ÄÇ“ ﬂ  ‡Ÿ\NS.uuidbytesOí◊ë%8b@™ü,Ò-D˝∏ÅÄÇ”    „ Ê ¢‰ ÂÄÜÅ	ä¢	ÅÅpÄ|^documentLength”    Ï ˜ 8™ Ì Ó Ô  Ò Ú Û Ù ı ˆÅ	åÅ	çÅ	éÅ	èÅ	êÅ	ëÅ	íÅ	ìÅ	îÅ	ï™ ¯ñ 0 0 ¸* . 0! ñÅ	ñÄàÄsÄsÅ	òÄSÄrÄsÅ	£ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ! S°!Å	óÄ)“  ! S§ . . . .ÄrÄrÄrÄrÄ)”   !! ¢!!Å	ôÅ	ö¢!!Å	õÅ	ùÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   !#!& ¢º!%ÄyÅ	ú¢ø_Ä{Å	Ä|^documentLength”   !,!0 £!-!.!/Å	ûÅ	üÅ	†£!1!2—Å	°Å	¢ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“!9 !:Ÿ\NS.uuidbytesO&ƒ1j˛¶BàÜÇﬂÛÌCÍbÄÇ“!= !>Ÿ\NS.uuidbytesOïßÿ˚∫L´BÅ∫õÄÇ”   !A!D ¢‰!CÄÜÅ	§¢ø_Ä{Å	Ä|^documentLength”   !J!U 8™!K!L!M!N!O!P!Q!R!S!TÅ	¶Å	ßÅ	®Å	©Å	™Å	´Å	¨Å	≠Å	ÆÅ	Ø™!Vñ 0 0!Z* . 0!^ñÅ	∞ÄàÄsÄsÅ	≤ÄSÄrÄsÅ	æÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  !l S°!mÅ	±Ä)“  !p S§ . . . .ÄrÄrÄrÄrÄ)”   !w!z ¢!x!yÅ	≥Å	¥¢!{!|Å	µÅ	ªÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   !Å!Ö £!Ç!É!ÑÅ	∂Å	∑Å	∏£—!á!àÄÑÅ	πÅ	∫Ä|_SourceCodeEditor.sidebar.width_/SourceCodeEditor.playgroundToyDisplayController_&SourceCodeEditor.playgroundResultStore“!é !èŸ\NS.uuidbytesO0ÌïåHÅ∑‰Cgü:®ÄÇ“!í !ìŸ\NS.uuidbytesOÜ0q‘ΩÿJè¨ñF1∂q~%ÄÇ”   !ñ!ô ¢º!òÄyÅ	º¢	!õÅÅ	ΩÄ|^documentLengthÍ”   !†!£ ¢‰!¢ÄÜÅ	ø¢	!õÅÅ	ΩÄ|^documentLength”   !©!¥ 8™!™!´!¨!≠!Æ!Ø!∞!±!≤!≥Å	¡Å	¬Å	√Å	ƒÅ	≈Å	∆Å	«Å	»Å	…Å	 ™!µñ 0 0!π‚!ª 0!ΩñÅ	ÀÄàÄsÄsÅ	ÕÅwÅ	ÿÄsÅ	ŸÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  !À S°!ÃÅ	ÃÄ)“  !œ S§A•A•Å∂Å6Å∂Å6Ä)”   !÷!Ÿ ¢!◊!ÿÅ	ŒÅ	œ¢!⁄!€Å	–Å	“Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   !‡!„ ¢º!‚ÄyÅ	—¢ø
lÄ{ÅÄ|^documentLength”   !È!Ì £!Í!Î!ÏÅ	”Å	‘Å	’£!Ó!Ô—Å	÷Å	◊ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“!ˆ !˜Ÿ\NS.uuidbytesO≥_˚ãaN©÷`‚5º ÄÇ“!˙ !˚Ÿ\NS.uuidbytesOƒoDBÑ.∑h)6'jÄÇO”   !ˇ" ¢‰"ÄÜÅ	⁄¢ø
lÄ{ÅÄ|^documentLength”   "" 8™"	"
""""""""Å	‹Å	›Å	ﬁÅ	ﬂÅ	‡Å	·Å	‚Å	„Å	‰Å	Â™"ñ 0 0"ß . 0"ñÅ	ÊÄàÄsÄsÅ	ËÅ±ÄrÄsÅ	ÙÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  "* S°"+Å	ÁÄ)“  ". S§§§Å¡Å5Å¡Å5Ä)”   "5"8 ¢"6"7Å	ÈÅ	Í¢"9":Å	ÎÅ	ÒÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   "?"C £"@"A"BÅ	ÏÅ	ÌÅ	Ó£"D"E—Å	ÔÅ	ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“"L "MŸ\NS.uuidbytesOÈz§ t@OØæé°‡G‹2ÄÇ“"P "QŸ\NS.uuidbytesON4ó=C‹IPßÀVwà\Ï¥ÄÇ”   "T"W ¢º"VÄyÅ	Ú¢ø"YÄ{Å	ÛÄ|^documentLength
”   "^"a ¢‰"`ÄÜÅ	ı¢ø"YÄ{Å	ÛÄ|^documentLength”   "g"r 8™"h"i"j"k"l"m"n"o"p"qÅ	˜Å	¯Å	˘Å	˙Å	˚Å	¸Å	˝Å	˛Å	ˇÅ
 ™"sñ 0 0"wß . 0"{ñÅ
ÄàÄsÄsÅ
Å±ÄrÄsÅ
ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  "â S°"äÅ
Ä)“  "ç S§CæCæÅÄzÅÄzÄ)”   "î"ó ¢"ï"ñÅ
Å
¢"ò"ôÅ
Å
Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   "û"¢ £"ü"†"°Å
Å
Å
	£"£"§—Å

Å
ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“"´ "¨Ÿ\NS.uuidbytesOÂ¿M8∆¬IÔ∫9S„‚«%3ÄÇ“"Ø "∞Ÿ\NS.uuidbytesO ËT ),AÄÇ$πŸ˛k-lÄÇ”   "≥"∂ ¢º"µÄyÅ
¢ø"∏Ä{Å
Ä|^documentLengthj”   "Ω"¿ ¢‰"øÄÜÅ
¢ø"∏Ä{Å
Ä|^documentLength”   "∆"— 8™"«"»"…" "À"Ã"Õ"Œ"œ"–Å
Å
Å
Å
Å
Å
Å
Å
Å
Å
™"“ñ 0 0"÷* . 0"⁄ñÅ
ÄàÄsÄsÅ
ÄSÄrÄsÅ
*ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  "Ë S°"ÈÅ
Ä)“  "Ï S§ . ."Ô .ÄrÄrÅ
ÄrÄ)3”   "Ù"˜ ¢"ı"ˆÅ
 Å
!¢"¯"˘Å
"Å
(Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   "˛# £"ˇ# #Å
#Å
$Å
%£##—Å
&Å
'ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“# #Ÿ\NS.uuidbytesO¡ê	‚°ëGb†ÅﬁvÅW,ÄÇ“# #Ÿ\NS.uuidbytesOæï{ëßÊKµ≥åWtw+ÌÄÇ”   ## ¢º#ÄyÅ
)¢ø©Ä{ÅLÄ|^documentLength”   ## ¢‰#ÄÜÅ
+¢ø©Ä{ÅLÄ|^documentLength”   #%#0 8™#&#'#(#)#*#+#,#-#.#/Å
-Å
.Å
/Å
0Å
1Å
2Å
3Å
4Å
5Å
6™#1ñ 0 0#5Ô#7 0#9ñÅ
7ÄàÄsÄsÅ
:Å_Å
FÄsÅ
GÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  #G S°#HÅ
8Ä)“  #K S§#Lq#LqÅ
9ÅÜÅ
9ÅÜÄ)”   #S#V ¢#T#UÅ
;Å
<¢#W#XÅ
=Å
CÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   #]#a £#^#_#`Å
>Å
?Å
@£#b#c—Å
AÅ
BÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“#j #kŸ\NS.uuidbytesOe0wÌ'@g∑ÿà'¥&˙ÄÇ“#n #oŸ\NS.uuidbytesOÌhZﬁl`F`ñCµæl÷ÄÇ”   #r#u ¢º#tÄyÅ
D¢ø#wÄ{Å
EÄ|^documentLength ]”   #}#Ä ¢‰#ÄÜÅ
H¢ø#wÄ{Å
EÄ|^documentLength”   #Ü#ë 8™#á#à#â#ä#ã#å#ç#é#è#êÅ
JÅ
KÅ
LÅ
MÅ
NÅ
OÅ
PÅ
QÅ
RÅ
S™#íñ 0 0#ñ#ó . 0#öñÅ
TÄàÄsÄsÅ
VÅ
aÄrÄsÅ
bÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  #® S°#©Å
UÄ)“  #¨ S§ﬁﬁÅjÅ˘ÅjÅ˘Ä)”   #≥#∂ ¢#¥#µÅ
WÅ
X¢#∑#∏Å
YÅ
[Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   #Ω#¿ ¢º#øÄyÅ
Z¢øÆÄ{ÅSÄ|^documentLength”   #∆#  £#«#»#…Å
\Å
]Å
^£#À#Ã—Å
_Å
`ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“#” #‘Ÿ\NS.uuidbytesOLíªÇ_ãD™ã\∏¿∆„ÅÄÇ“#◊ #ÿŸ\NS.uuidbytesO¨™úΩ˝ëL`á‚ù5v.ÂÄÇ#ø‡      ”   #‹#ﬂ ¢‰#ﬁÄÜÅ
c¢øÆÄ{ÅSÄ|^documentLength”   #Â# 8™#Ê#Á#Ë#È#Í#Î#Ï#Ì#Ó#ÔÅ
eÅ
fÅ
gÅ
hÅ
iÅ
jÅ
kÅ
lÅ
mÅ
n™#Òñ 0 0#ı#ˆ#˜ 0#˘ñÅ
oÄàÄsÄsÅ
qÅ
|Å
}ÄsÅ
~ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  $ S°$Å
pÄ)“  $ S§%%Å`ÅÓÅ`ÅÓÄ)”   $$ ¢$$Å
rÅ
s¢$$Å
tÅ
vÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   $$ ¢$ºÅ
uÄy¢!õøÅ	ΩÄ{Ä|^documentLength”   $%$) £$&$'$(Å
wÅ
xÅ
y£$*$+—Å
zÅ
{ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“$2 $3Ÿ\NS.uuidbytesO˙Õy#¿9FDØjÂxYU&»ÄÇ“$6 $7Ÿ\NS.uuidbytesO*ykWÉûBmàÅL]ï	äÄÇ#@0Ä     l”   $<$? ¢‰$>ÄÜÅ
¢ø!õÄ{Å	ΩÄ|^documentLength”   $E$P 8™$F$G$H$I$J$K$L$M$N$OÅ
ÅÅ
ÇÅ
ÉÅ
ÑÅ
ÖÅ
ÜÅ
áÅ
àÅ
âÅ
ä™$Qñ 0 0$Uß . 0$YñÅ
ãÄàÄsÄsÅ
çÅ±ÄrÄsÅ
òÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  $g S°$hÅ
åÄ)“  $k S§ . . . .ÄrÄrÄrÄrÄ)”   $r$u ¢$s$tÅ
éÅ
è¢$v$wÅ
êÅ
ñÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   $|$Ä £$}$~$Å
ëÅ
íÅ
ì£$Å$Ç—Å
îÅ
ïÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“$â $äŸ\NS.uuidbytesOòzÜ¬QåHÎ∫Üñ˜8 DLÄÇ“$ç $éŸ\NS.uuidbytesO·∫tEIøgü'"ådƒÄÇ”   $ë$î ¢$íºÅ
óÄy¢cøÅ>Ä{Ä|^documentLength”   $ö$ù ¢‰$úÄÜÅ
ô¢øcÄ{Å>Ä|^documentLength”   $£$Æ 8™$§$•$¶$ß$®$©$™$´$¨$≠Å
õÅ
úÅ
ùÅ
ûÅ
üÅ
†Å
°Å
¢Å
£Å
§™$Øñ 0 0$≥* . 0$∑ñÅ
•ÄàÄsÄsÅ
ßÄSÄrÄsÅ
≤ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  $≈ S°$∆Å
¶Ä)“  $… S§ . . . .ÄrÄrÄrÄrÄ)”   $–$” ¢$—$“Å
®Å
©¢$‘$’Å
™Å
¨Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   $⁄$› ¢$€ºÅ
´Äy¢ªøÅ<Ä{Ä|^documentLength”   $„$Á £$‰$Â$ÊÅ
≠Å
ÆÅ
Ø£$Ë$È—Å
∞Å
±ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“$ $ÒŸ\NS.uuidbytesOS`¸ÅGNÏé`;®◊’¿3ÄÇ“$Ù $ıŸ\NS.uuidbytesO≤ä=m≥CÑ∑;ûöÊﬁÄÇ”   $¯$˚ ¢‰$˙ÄÜÅ
≥¢øªÄ{Å<Ä|^documentLength”   %% 8™%%%%%%%%	%
%Å
µÅ
∂Å
∑Å
∏Å
πÅ
∫Å
ªÅ
ºÅ
ΩÅ
æ™%ñ 0 0%* . 0%ñÅ
øÄàÄsÄsÅ
¡ÄSÄrÄsÅ
ÃÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  %# S°%$Å
¿Ä)“  %' S§ . . . .ÄrÄrÄrÄrÄ)”   %.%1 ¢%/%0Å
¬Å
√¢%2%3Å
ƒÅ
 Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   %8%< £%9%:%;Å
≈Å
∆Å
«£%=%>—Å
»Å
…ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“%E %FŸ\NS.uuidbytesOÕ∆oß>O!í(v¨VVBÄÇ“%I %JŸ\NS.uuidbytesO8¿ófxÙ@&û2Gë1ô±ÄÇ”   %M%P ¢%NºÅ
ÀÄy¢ªøÅ<Ä{Ä|^documentLength”   %V%Y ¢‰%XÄÜÅ
Õ¢øªÄ{Å<Ä|^documentLength”   %_%j 8™%`%a%b%c%d%e%f%g%h%iÅ
œÅ
–Å
—Å
“Å
”Å
‘Å
’Å
÷Å
◊Å
ÿ™%kñ 0 0%oß . 0%sñÅ
ŸÄàÄsÄsÅ
€Å±ÄrÄsÅ
ÊÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  %Å S°%ÇÅ
⁄Ä)“  %Ö S§ . . . .ÄrÄrÄrÄrÄ)”   %å%è ¢%ç%éÅ
‹Å
›¢%ê%ëÅ
ﬁÅ
‰Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   %ñ%ö £%ó%ò%ôÅ
ﬂÅ
‡Å
·£%õ%ú—Å
‚Å
„ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“%£ %§Ÿ\NS.uuidbytesO±[‡ﬁ⁄°KO∏/Â¢~ŒCÄÇ“%ß %®Ÿ\NS.uuidbytesOª%ø‘»ç@˚õ√ˆ<*"K3ÄÇ”   %´%Æ ¢º%≠ÄyÅ
Â¢ø¶Ä{ÅﬂÄ|^documentLength”   %¥%∑ ¢‰%∂ÄÜÅ
Á¢ø¶Ä{ÅﬂÄ|^documentLength”   %Ω%» 8™%æ%ø%¿%¡%¬%√%ƒ%≈%∆%«Å
ÈÅ
ÍÅ
ÎÅ
ÏÅ
ÌÅ
ÓÅ
ÔÅ
Å
ÒÅ
Ú™%…ñ 0 0%Õ%Œ± 0%—ñÅ
ÛÄàÄsÄsÅ
ˆÅÅ+ÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  %ﬂ S°%‡Å
ÙÄ)“  %„ S§%‰%‰Å
ıÅ0Å
ıÅ0Ä)'”   %Î%Ó ¢%Ï%ÌÅ
˜Å
¯¢%Ô%Å
˘Å
¸Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   %ı%¯ ¢º%˜ÄyÅ
˙¢ø%˙Ä{Å
˚Ä|^documentLength¯”   %ˇ& £& &&Å
˝Å
˛Å
ˇ£&&—Å ÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“& &Ÿ\NS.uuidbytesOá£≤äáI¥GŒõµoé˙ÄÇ“& &Ÿ\NS.uuidbytesO˘°R€+@;§º•j‹€OÄÄÇ#@       ”   && ¢‰&ÄÜÅ¢ø%˙Ä{Å
˚Ä|^documentLength”   &&) 8™&& &!&"&#&$&%&&&'&(ÅÅÅÅ	Å
ÅÅÅÅÅ™&*ñ 0 0&.‡
Ó 0&2ñÅÄàÄsÄsÅÅsÅ>ÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  &@ S°&AÅÄ)“  &D S§ . . . .ÄrÄrÄrÄrÄ)”   &K&N ¢&L&MÅÅ¢&O&PÅÅÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   &U&Y £&V&W&XÅÅÅ£&Z&[—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“&b &cŸ\NS.uuidbytesOR"u˘u~LKÑ∂éŒ_àcÄÇ“&f &gŸ\NS.uuidbytesO≠‘Wä#DôeÂX™àÙÅÄÇ”   &j&m ¢&kºÅÄy¢WøÅºÄ{Ä|^documentLength”   &s&v ¢‰&uÄÜÅ¢øWÄ{ÅºÄ|^documentLength”   &|&á 8™&}&~&&Ä&Å&Ç&É&Ñ&Ö&ÜÅ Å!Å"Å#Å$Å%Å&Å'Å(Å)™&àñ 0 0&åÔ&é 0&êñÅ*ÄàÄsÄsÅ-Å_Å9ÄsÅ:ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  &û S°&üÅ+Ä)“  &¢ S§Ä[&• .ÅTÅÖÅ,ÄrÄ)E”   &™&≠ ¢&´&¨Å.Å/¢&Æ&ØÅ0Å3Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   &¥&∑ ¢º&∂ÄyÅ1¢ø&πÄ{Å2Ä|^documentLength”   &æ&¬ £&ø&¿&¡Å4Å5Å6£&√&ƒ—Å7Å8ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“&À &ÃŸ\NS.uuidbytesO—‡zrúFñQ_Äq8ÄÇ“&œ &–Ÿ\NS.uuidbytesO’£Ç≈WBFx¢	1lH“œ˙ÄÇ.”   &‘&◊ ¢‰&÷ÄÜÅ;¢ø&πÄ{Å2Ä|^documentLength”   &›&Ë 8™&ﬁ&ﬂ&‡&·&‚&„&‰&Â&Ê&ÁÅ=Å>Å?Å@ÅAÅBÅCÅDÅEÅF™&Èñ 0 0&Ì* . 0&ÒñÅGÄàÄsÄsÅIÄSÄrÄsÅTÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  &ˇ S°' ÅHÄ)“  ' S§ . . . .ÄrÄrÄrÄrÄ)”   '
' ¢''ÅJÅK¢''ÅLÅRÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   '' £'''ÅMÅNÅO£''—ÅPÅQÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“'! '"Ÿ\NS.uuidbytesO∂∏êøWRGcÖ≥[_`Ø"áÄÇ“'% '&Ÿ\NS.uuidbytesO[êMÕÑ∫}…ì
÷ÄÇ”   ')', ¢'*ºÅSÄy¢FøÅƒÄ{Ä|^documentLength”   '2'5 ¢‰'4ÄÜÅU¢øFÄ{ÅƒÄ|^documentLength”   ';'F 8™'<'='>'?'@'A'B'C'D'EÅWÅXÅYÅZÅ[Å\Å]Å^Å_Å`™'Gñ 0 0'K÷ 0'OñÅaÄàÄsÄsÅeÅÅRÄsÅqÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  '] S°'^ÅbÄ)“  'a S§'b'c'b'cÅcÅdÅcÅdÄ)[A”   'j'm ¢'k'lÅfÅg¢'n'oÅhÅnÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   't'x £'u'v'wÅiÅjÅk£'y'z—ÅlÅmÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“'Å 'ÇŸ\NS.uuidbytesO0'\&3FC®Ø€™´Áä÷ÄÇ“'Ö 'ÜŸ\NS.uuidbytesO˘Ã„‡RBL∂Y%àÈ‹ÄÇ”   'â'å ¢º'ãÄyÅo¢ø'éÄ{ÅpÄ|^documentLengthÊ”   'ì'ñ ¢‰'ïÄÜÅr¢ø'éÄ{ÅpÄ|^documentLength”   'ú'ß 8™'ù'û'ü'†'°'¢'£'§'•'¶ÅtÅuÅvÅwÅxÅyÅzÅ{Å|Å}™'®ñ 0 0'¨%Œ'Æ 0'∞ñÅ~ÄàÄsÄsÅÅÅÅçÄsÅéÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  'æ S°'øÅÄ)“  '¬ S§'√C'√CÅÄÅÅÄÅÄ)æ”   ' 'Õ ¢'À'ÃÅÇÅÉ¢'Œ'œÅÑÅäÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   '‘'ÿ £'’'÷'◊ÅÖÅÜÅá£'Ÿ'⁄—ÅàÅâÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“'· '‚Ÿ\NS.uuidbytesOº“| ¸zBnåæÅ‚’erÄÇ“'Â 'ÊŸ\NS.uuidbytesOπõ»·EıÇö6˝n¬™UÄÇ”   'È'Ï ¢º'ÎÄyÅã¢ø'ÓÄ{ÅåÄ|^documentLength!ò∆”   'Ù'˜ ¢‰'ˆÄÜÅè¢ø'ÓÄ{ÅåÄ|^documentLength”   '˝( 8™'˛'ˇ( (((((((ÅëÅíÅìÅîÅïÅñÅóÅòÅôÅö™(	ñ 0 0(ß . 0(ñÅõÄàÄsÄsÅùÅ±ÄrÄsÅ©ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ( S°( ÅúÄ)“  (# S§ﬁ[§ .Å˘ÅÖÅ5ÄrÄ)”   (*(- ¢(+(,ÅûÅü¢(.(/Å†Å¶Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   (4(8 £(5(6(7Å°Å¢Å££(9(:—Å§Å•ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“(A (BŸ\NS.uuidbytesOﬂFõpÅ–Hµî>°èÊ≈ÄÇ“(E (FŸ\NS.uuidbytesO%Rœ2≠N¬ôiXFˇ~lÄÇ”   (I(L ¢º(KÄyÅß¢ø(NÄ{Å®Ä|^documentLength·”   (S(V ¢‰(UÄÜÅ™¢ø(NÄ{Å®Ä|^documentLength”   (\(g 8™(](^(_(`(a(b(c(d(e(fÅ¨Å≠ÅÆÅØÅ∞Å±Å≤Å≥Å¥Åµ™(hñ 0 0(lß . 0(pñÅ∂ÄàÄsÄsÅ∏Å±ÄrÄsÅ√ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  (~ S°(Å∑Ä)“  (Ç S§ . . . .ÄrÄrÄrÄrÄ)”   (â(å ¢(ä(ãÅπÅ∫¢(ç(éÅªÅΩÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   (ì(ñ ¢(îºÅºÄy¢røÅ∞Ä{Ä|^documentLength”   (ú(† £(ù(û(üÅæÅøÅ¿£(°(¢—Å¡Å¬ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“(© (™Ÿ\NS.uuidbytesOP û0óBÛø´ƒOk¢¡#ÄÇ“(≠ (ÆŸ\NS.uuidbytesOôµø—–I
ÜéQÿG‘>öÄÇ”   (±(¥ ¢‰(≥ÄÜÅƒ¢ørÄ{Å∞Ä|^documentLength”   (∫(≈ 8™(ª(º(Ω(æ(ø(¿(¡(¬(√(ƒÅ∆Å«Å»Å…Å ÅÀÅÃÅÕÅŒÅœ™(∆ñ 0 0( (À˜ 0(ŒñÅ–ÄàÄsÄsÅ“Å›ÅeÄsÅﬁÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  (‹ S°(›Å—Ä)“  (‡ S§ . . . .ÄrÄrÄrÄrÄ)”   (Á(Í ¢(Ë(ÈÅ”Å‘¢(Î(ÏÅ’Å€Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   (Ò(ı £(Ú(Û(ÙÅ÷Å◊Åÿ£(ˆ(˜—ÅŸÅ⁄ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“(˛ (ˇŸ\NS.uuidbytesOF¸µbÎdO`ûñÛfBŒÄÇ“) )Ÿ\NS.uuidbytesOÄˇˆÌ?ÌM-è÷íW ∫qÄÇ”   ))	 ¢º)ÄyÅ‹¢ø«Ä{Å}Ä|^documentLength#@&      ”   )) ¢‰)ÄÜÅﬂ¢ø«Ä{Å}Ä|^documentLength”   ))$ 8™))))))) )!)")#Å·Å‚Å„Å‰ÅÂÅÊÅÁÅËÅÈÅÍ™)%ñ 0 0)))*8 0)-ñÅÎÄàÄsÄsÅÌÅ¯Å:ÄsÅ˘ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ); S°)<ÅÏÄ)“  )? S§ . . . .ÄrÄrÄrÄrÄ)”   )F)I ¢)G)HÅÓÅÔ¢)J)KÅÅÚÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   )P)S ¢º)RÄyÅÒ¢	aÅÅ3Ä|^documentLength”   )Y)] £)Z)[)\ÅÛÅÙÅı£—)_)`ÄÑÅˆÅ˜Ä|_SourceCodeEditor.sidebar.width_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController“)f )gŸ\NS.uuidbytesO÷π./#Bπ¢3ùdGÓfåÄÇ“)j )kŸ\NS.uuidbytesO˘˛+¶Ö†Mqè|C¸_ù2ÄÇ#@#      ”   )o)r ¢‰)qÄÜÅ˙¢	aÅÅ3Ä|^documentLength”   )x)z 8°)yÅ¸°){Å˛Ä@” U  V W X)ÄÄ ÄÅ˝_*x-xcode-debug-memory-graph:///7fe9a34c3dd0”   )É)á 8£)Ñ)Ö)ÜÅˇÅ Å£ø**Ä{ÄSÄSÄ@^disclosedNodes]cameraPanPosX]cameraPanPosY”   )ê)í 8°)ëÅ°)ìÅÄ@” U  V W X)òÄ ÄÅ_Zfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp/Assets.xcassets”   )õ)§ 8®)ú)ù)û)ü)†)°)¢)£ÅÅÅÅ	Å
ÅÅÅ®)•)¶)ß)®)©)™)´)¨ÅÅÅÅÅÅÅÅÄ@_selectedItemIdentifiers[sourceItems_detailController_preferredSourceListWidth]sclicing-area_source-list-area_lastFocusedArea]overview-area“  )∑)π°)∏ÅÅ_B./assetNoFavorite.imageset/[universal][][][1x][][][][][][][][][][]“ \ ])º)Ω\NSMutableSet£)º)æ `UNSSet“  )¿)π°)¡ÅÅ_./assetNoFavorite.imageset_IBICCatalogOverviewController#@l¿     ”   )«)» 8††Ä@”   )À)Õ 8°)ÃÅ°)ŒÅÄ@_expandedItemIDs“  )“)‘°)”ÅÅQ.“ \ ])æ)◊¢)æ `ZdetailArea”   )⁄)€ 8††Ä@”   )ﬁ)‚ 8£)ﬂ)‡)·ÅÅ Å"£)„)‰)ÂÅ$Å4ÅJÄ@” U  V W X)ÍÄ ÄÅ_lfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog.xcodeproj” U  V W X)ÔÄ ÄÅ!_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj” U  V W X)ÙÄ ÄÅ#_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog.xcodeproj”   )˜)˚ 8£)¯)˘)˙Å%Å&Å'£)¸)˝)˛Å(Å2Å3Ä@_,Xcode3ProjectEditorSelectedDocumentLocations_,Xcode3ProjectEditorPreviousTargetEditorClass_&Xcode3ProjectEditor_Xcode3TargetEditor“  * S°*Å)Ä)‘* »   *	*
**YselectionÅ,Å+Å*Å1_lfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog.xcodeproj#A∆◊2m0<Í”   ** 8¢**Å-Å.¢**Å/Å0Ä@VEditorVTarget_Xcode3TargetEditor[GameCatalog“ \ ]**_Xcode3ProjectDocumentLocation£** `_Xcode3ProjectDocumentLocation_DVTDocumentLocation_Xcode3TargetEditor”   *"*# 8††Ä@”   *&*. 8ß*'*(*)***+*,*-Å5Å6Å7Å8Å9Å:Å;ß*/*0*1*2*3*4*5Å<Å=Å>Å?Å@ÅAÅIÄ@_-Xcode3ProjectEditorPreviousProjectEditorClass_+Xcode3ProjectEditor_Xcode3ProjectInfoEditor_,Xcode3ProjectEditorPreviousTargetEditorClass_-Xcode3ProjectEditor_Xcode3BuildSettingsEditor_(Xcode3ProjectEditor_Xcode3PackagesEditor_,Xcode3ProjectEditorSelectedDocumentLocations_&Xcode3ProjectEditor_Xcode3TargetEditor_Xcode3PackagesEditor”   *@*A 8††Ä@_Xcode3TargetEditor”   *E*F 8††Ä@”   *I*J 8††Ä@“  *M S°*NÅBÄ)‘* »   *Q*R*S*ÅEÅDÅCÅ1_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/TheMealsApp.xcodeproj#A∆œwU˘<>”   *X*[ 8¢*Y*ÅFÅ-¢*\*]ÅGÅHÄ@WProject[TheMealsApp_Xcode3PackagesEditor”   *c*d 8††Ä@”   *g*l 8§*h*i*j*kÅKÅLÅMÅN§*m*n*o*pÅOÅPÅQÅjÄ@_-Xcode3ProjectEditorPreviousProjectEditorClass_,Xcode3ProjectEditorPreviousTargetEditorClass_,Xcode3ProjectEditorSelectedDocumentLocations_-Xcode3ProjectEditor_Xcode3BuildSettingsEditor_Xcode3BuildSettingsEditor_Xcode3BuildSettingsEditor“  *y S°*zÅRÄ)‘* »   *}*~**ÅUÅTÅSÅ1_Tfile:///Users/ben/belajar/ios-dicoding/example-ios/TheMealsApp/GameCatalog.xcodeproj#A∆œ|'^#:”   *Ñ*à 8£*Y**áÅFÅ-ÅV£*â*ä*ãÅWÅXÅYÄ@_"Xcode3BuildSettingsEditorLocations[GameCatalog_Xcode3BuildSettingsEditor“  *ë S°*íÅZÄ)”   *ï*ú 8¶*ñ*ó*ò*ô*ö*õÅ[Å\Å]Å^Å_Å`¶æ*û . .*° .ÄzÅaÄrÄrÅeÄrÄ@_"Xcode3BuildPropertyNameDisplayMode_Selected Build Properties_$Xcode3BuildSettingsEditorDisplayMode_#Xcode3BuildPropertyValueDisplayMode_#Collapsed Build Property Categories_Xcode3BuildSettingsEditorMode“  *´*≠°*¨ÅbÅd“ *Ø*∞*±YNS.stringÅc_>Apple Clang - Code Generation||CLANG_OPTIMIZATION_PROFILE_FILE“ \ ]*≥*¥_NSMutableString£*≥*µ `XNSString“ \ ]*∑*∏^NSMutableArray£*∑ è `“  *∫*≠Ø*ª*º*Ω*æ*ø*¿*¡*¬*√*ƒ*≈*∆*«*»*…* *À*Ã*Õ*Œ*œ*–*—*“*”*‘*’*÷*◊*ÿ*Ÿ*⁄*€*‹*›*ﬁ*ﬂ*‡*·*‚*„*‰*Â*Ê*Á*Ë*È*Í*Î*Ï*Ì*Ó*Ô**Ò*Ú*Û*Ù*ı*ˆ*˜*¯*˘*˙*˚*¸*˝*˛*ˇ+ +++++++++	+
++++++++++++++++++++++ +!+"+#+$+%+&+'+(+)+*+++,+-+.+/+0+1+2+3+4+5+6+7+8+9+:+;+<+=+>+?+@+A+B+C+D+E+F+G+H+I+J+K+L+M+N+O+P+Q+R+S+T+U+V+W+X+Y+Z+[+\+]+^+_+`+a+b+c+d+e+f+g+h+i+j+k+l+m+n+o+p+q+r+s+t+u+v+w+x+y+z+{+|+}+~++Ä+Å+Ç+É+Ñ+Ö+Ü+á+à+â+ä+ã+å+ç+é+è+ê+ë+í+ì+î+ï+ñ+ó+ò+ô+ö+õ+ú+ù+û+ü+†+°+¢+£+§+•+¶+ß+®+©+™+´+¨+≠+Æ+Ø+∞+±+≤+≥+¥+µ+∂+∑+∏+π+∫+ª+º+Ω+æ+ø+¿+¡+¬+√+ƒ+≈+∆+«+»+…+ +À+Ã+Õ+Œ+œ+–+—+“+”+‘+’+÷+◊+ÿ+Ÿ+⁄+€+‹+›+ﬁ+ﬂ+‡+·+‚+„+‰+Â+Ê+Á+Ë+È+Í+Î+Ï+Ì+Ó+Ô++Ò+Ú+Û+Ù+ı+ˆ+˜+¯+˘+˙+˚+¸+˝+˛+ˇ, ,,,,,,,,,	,
,,,,,,,,,,,,,,,,,,,,,, ,!,",#,$,%,&,',(,),*,+,,,-,.,/,0,1,2,3,4,5,6,7,8,9,:,;,<,=,>,?,@,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z,[,\,],^,_,`,a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,{,|,},~,,Ä,Å,Ç,É,Ñ,Ö,Ü,á,à,â,ä,ã,å,ç,é,è,ê,ë,í,ì,î,ï,ñ,ó,ò,ô,ö,õ,ú,ù,û,ü,†,°,¢,£,§,•,¶,ß,®,©,™,´,¨,≠,Æ,Ø,∞,±,≤,≥,¥,µ,∂,∑,∏,π,∫,ª,º,Ω,æÅfÅgÅhÅiÅjÅkÅlÅmÅnÅoÅpÅqÅrÅsÅtÅuÅvÅwÅxÅyÅzÅ{Å|Å}Å~ÅÅÄÅÅÅÇÅÉÅÑÅÖÅÜÅáÅàÅâÅäÅãÅåÅçÅéÅèÅêÅëÅíÅìÅîÅïÅñÅóÅòÅôÅöÅõÅúÅùÅûÅüÅ†Å°Å¢Å£Å§Å•Å¶ÅßÅ®Å©Å™Å´Å¨Å≠ÅÆÅØÅ∞Å±Å≤Å≥Å¥ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩÅæÅøÅ¿Å¡Å¬Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀÅÃÅÕÅŒÅœÅ–Å—Å“Å”Å‘Å’Å÷Å◊ÅÿÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁÅËÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚÅÛÅÙÅıÅˆÅ˜Å¯Å˘Å˙Å˚Å¸Å˝Å˛ÅˇÅ ÅÅÅÅÅÅÅÅÅ	Å
ÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅ Å!Å"Å#Å$Å%Å&Å'Å(Å)Å*Å+Å,Å-Å.Å/Å0Å1Å2Å3Å4Å5Å6Å7Å8Å9Å:Å;Å<Å=Å>Å?Å@ÅAÅBÅCÅDÅEÅFÅGÅHÅIÅJÅKÅLÅMÅNÅOÅPÅQÅRÅSÅTÅUÅVÅWÅXÅYÅZÅ[Å\Å]Å^Å_Å`ÅaÅbÅcÅdÅeÅfÅgÅhÅiÅjÅkÅlÅmÅnÅoÅpÅqÅrÅsÅtÅuÅvÅwÅxÅyÅzÅ{Å|Å}Å~ÅÅÄÅÅÅÇÅÉÅÑÅÖÅÜÅáÅàÅâÅäÅãÅåÅçÅéÅèÅêÅëÅíÅìÅîÅïÅñÅóÅòÅôÅöÅõÅúÅùÅûÅüÅ†Å°Å¢Å£Å§Å•Å¶ÅßÅ®Å©Å™Å´Å¨Å≠ÅÆÅØÅ∞Å±Å≤Å≥Å¥ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩÅæÅøÅ¿Å¡Å¬Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀÅÃÅÕÅŒÅœÅ–Å—Å“Å”Å‘Å’Å÷Å◊ÅÿÅŸÅ⁄Å€Å‹Å›ÅﬁÅﬂÅ‡Å·Å‚Å„Å‰ÅÂÅÊÅÁÅËÅÈÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚÅÛÅÙÅıÅˆÅ˜Å¯Å˘Å˙Å˚Å¸Å˝Å˛ÅˇÅ ÅÅÅÅÅÅÅÅÅ	Å
ÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅÅ Å!Å"Å#Å$Å%Å&Å'Å(Å)Å*Å+Å,Å-Å.Å/Å0Å1Å2Å3Å4Å5Å6Å7Å8Å9Å:Å;Å<Å=Å>Å?Å@ÅAÅBÅCÅDÅEÅFÅGÅHÅIÅJÅKÅLÅMÅNÅOÅPÅQÅRÅSÅTÅUÅVÅWÅXÅYÅZÅ[Å\Å]Å^Å_Å`ÅaÅbÅcÅdÅeÅfÅgÅhÅiÅd“ *Ø*∞,¬Åc_Architectures||ADDITIONAL_SDKS“ *Ø*∞,≈Åc_Architectures||ARCHS“ *Ø*∞,»Åc_Architectures||SDKROOT“ *Ø*∞,ÀÅc_Architectures||EXCLUDED_ARCHS“ *Ø*∞,ŒÅc_#Architectures||SUPPORTED_PLATFORMS“ *Ø*∞,—Åc_'Assets||ASSET_PACK_MANIFEST_URL_PREFIX“ *Ø*∞,‘Åc_,Assets||EMBED_ASSET_PACKS_IN_PRODUCT_BUNDLE“ *Ø*∞,◊Åc_#Assets||ENABLE_ON_DEMAND_RESOURCES“ *Ø*∞,⁄Åc_1Assets||ON_DEMAND_RESOURCES_INITIAL_INSTALL_TAGS“ *Ø*∞,›Åc_+Assets||ON_DEMAND_RESOURCES_PREFETCH_ORDER“ *Ø*∞,‡Åc_Build Locations||SYMROOT“ *Ø*∞,„Åc_Build Locations||OBJROOT“ *Ø*∞,ÊÅc_%Build Locations||SHARED_PRECOMPS_DIR“ *Ø*∞,ÈÅc_4Build Options||ALLOW_TARGET_PLATFORM_SPECIALIZATION“ *Ø*∞,ÏÅc_5Build Options||ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES“ *Ø*∞,ÔÅc_.Build Options||BUILD_LIBRARY_FOR_DISTRIBUTION“ *Ø*∞,ÚÅc_Build Options||BUILD_VARIANTS“ *Ø*∞,ıÅc_Build Options||GCC_VERSION“ *Ø*∞,¯Åc_)Build Options||DEBUG_INFORMATION_VERSION“ *Ø*∞,˚Åc_Build Options||EAGER_LINKING“ *Ø*∞,˛Åc_$Build Options||ENABLE_CODE_COVERAGE“ *Ø*∞-Åc_"Build Options||ENABLE_DEBUG_DYLIB“ *Ø*∞-Åc_+Build Options||COMPILER_INDEX_STORE_ENABLE“ *Ø*∞-Åc_+Build Options||ENABLE_TESTING_SEARCH_PATHS“ *Ø*∞-
Åc_*Build Options||EXCLUDED_SOURCE_FILE_NAMES“ *Ø*∞-Åc_'Build Options||GENERATE_PROFILING_CODE“ *Ø*∞-Åc_*Build Options||IMPLICIT_DEPENDENCY_DOMAIN“ *Ø*∞-Åc_*Build Options||INCLUDED_SOURCE_FILE_NAMES“ *Ø*∞-Åc_@Build Options||PRECOMPS_INCLUDE_HEADERS_FROM_BUILT_PRODUCTS_DIR“ *Ø*∞-Åc_.Build Options||APPLICATION_EXTENSION_API_ONLY“ *Ø*∞-Åc_(Build Options||FUSE_BUILD_SCRIPT_PHASES“ *Ø*∞-Åc_2Build Options||SCAN_ALL_SOURCE_FILES_FOR_INCLUDES“ *Ø*∞-"Åc_<Build Options||TREAT_MISSING_SCRIPT_PHASE_OUTPUTS_AS_ERRORS“ *Ø*∞-%Åc_-Build Options||ENABLE_USER_SCRIPT_SANDBOXING“ *Ø*∞-(Åc_Deployment||STRIPFLAGS“ *Ø*∞-+Åc_Deployment||ALTERNATE_GROUP“ *Ø*∞-.Åc_Deployment||ALTERNATE_OWNER“ *Ø*∞-1Åc_Deployment||ALTERNATE_MODE“ *Ø*∞-4Åc_(Deployment||ALTERNATE_PERMISSIONS_FILES“ *Ø*∞-7Åc_Deployment||MARKETPLACES“ *Ø*∞-:Åc_)Deployment||ALTERNATIVE_DISTRIBUTION_WEB“ *Ø*∞-=Åc_ Deployment||DEPLOYMENT_LOCATION“ *Ø*∞-@Åc_&Deployment||DEPLOYMENT_POSTPROCESSING“ *Ø*∞-CÅc_$Deployment||DEVELOPMENT_ASSET_PATHS“ *Ø*∞-FÅc_(Deployment||DRIVERKIT_DEPLOYMENT_TARGET“ *Ø*∞-IÅc_Deployment||INSTALL_GROUP“ *Ø*∞-LÅc_Deployment||INSTALL_OWNER“ *Ø*∞-OÅc_Deployment||INSTALL_MODE_FLAG“ *Ø*∞-RÅc_Deployment||DSTROOT“ *Ø*∞-UÅc_Deployment||INSTALL_PATH“ *Ø*∞-XÅc_-Deployment||RESOURCES_TARGETED_DEVICE_FAMILY“ *Ø*∞-[Åc_Deployment||SKIP_INSTALL“ *Ø*∞-^Åc_Deployment||COPY_PHASE_STRIP“ *Ø*∞-aÅc_$Deployment||STRIP_INSTALLED_PRODUCT“ *Ø*∞-dÅc_Deployment||STRIP_STYLE“ *Ø*∞-gÅc_ Deployment||STRIP_SWIFT_SYMBOLS“ *Ø*∞-jÅc_#Deployment||TARGETED_DEVICE_FAMILY“ *Ø*∞-mÅc_'Deployment||IPHONEOS_DEPLOYMENT_TARGET“ *Ø*∞-pÅc_%Deployment||MACOSX_DEPLOYMENT_TARGET“ *Ø*∞-sÅc_#Deployment||TVOS_DEPLOYMENT_TARGET“ *Ø*∞-vÅc_#Deployment||XROS_DEPLOYMENT_TARGET“ *Ø*∞-yÅc_&Deployment||WATCHOS_DEPLOYMENT_TARGET“ *Ø*∞-|Åc_"Headers||COPY_HEADERS_RUN_UNIFDEF“ *Ø*∞-Åc_$Headers||COPY_HEADERS_UNIFDEF_FLAGS“ *Ø*∞-ÇÅc_CInfo.plist Values||INFOPLIST_KEY_WKRunsIndependentlyOfCompanionApp“ *Ø*∞-ÖÅc_-Info.plist Values||INFOPLIST_KEY_WKWatchOnly“ *Ø*∞-àÅc_6Info.plist Values||INFOPLIST_KEY_NSMainStoryboardFile“ *Ø*∞-ãÅc_;Info.plist Values||INFOPLIST_KEY_LSApplicationCategoryType“ *Ø*∞-éÅc_GInfo.plist Values||INFOPLIST_KEY_UIApplicationSceneManifest_Generation“ *Ø*∞-ëÅc_-Info.plist Values||INFOPLIST_KEY_LSUIElement“ *Ø*∞-îÅc_2Info.plist Values||INFOPLIST_KEY_LSBackgroundOnly“ *Ø*∞-óÅc_5Info.plist Values||INFOPLIST_KEY_CFBundleDisplayName“ *Ø*∞-öÅc_?Info.plist Values||INFOPLIST_KEY_CLKComplicationPrincipalClass“ *Ø*∞-ùÅc_:Info.plist Values||INFOPLIST_KEY_NSHumanReadableCopyright“ *Ø*∞-†Åc_;Info.plist Values||INFOPLIST_KEY_UILaunchScreen_Generation“ *Ø*∞-£Åc_8Info.plist Values||INFOPLIST_KEY_UILaunchStoryboardName“ *Ø*∞-¶Åc_/Info.plist Values||INFOPLIST_KEY_NSMainNibFile“ *Ø*∞-©Åc_5Info.plist Values||INFOPLIST_KEY_MetalCaptureEnabled“ *Ø*∞-¨Åc_2Info.plist Values||INFOPLIST_KEY_NSPrincipalClass“ *Ø*∞-ØÅc_FInfo.plist Values||INFOPLIST_KEY_NSFileProviderDomainUsageDescription“ *Ø*∞-≤Åc_?Info.plist Values||INFOPLIST_KEY_NSAppleEventsUsageDescription“ *Ø*∞-µÅc_CInfo.plist Values||INFOPLIST_KEY_NSBluetoothAlwaysUsageDescription“ *Ø*∞-∏Åc_GInfo.plist Values||INFOPLIST_KEY_NSBluetoothPeripheralUsageDescription“ *Ø*∞-ªÅc_GInfo.plist Values||INFOPLIST_KEY_NSBluetoothWhileInUseUsageDescription“ *Ø*∞-æÅc_=Info.plist Values||INFOPLIST_KEY_NSCalendarsUsageDescription“ *Ø*∞-¡Åc_:Info.plist Values||INFOPLIST_KEY_NSCameraUsageDescription“ *Ø*∞-ƒÅc_<Info.plist Values||INFOPLIST_KEY_NSContactsUsageDescription“ *Ø*∞-«Åc_AInfo.plist Values||INFOPLIST_KEY_NSDesktopFolderUsageDescription“ *Ø*∞- Åc_CInfo.plist Values||INFOPLIST_KEY_NSDocumentsFolderUsageDescription“ *Ø*∞-ÕÅc_CInfo.plist Values||INFOPLIST_KEY_NSDownloadsFolderUsageDescription“ *Ø*∞-–Åc_:Info.plist Values||INFOPLIST_KEY_OSBundleUsageDescription“ *Ø*∞-”Åc_:Info.plist Values||INFOPLIST_KEY_NSFaceIDUsageDescription“ *Ø*∞-÷Åc_AInfo.plist Values||INFOPLIST_KEY_NSFallDetectionUsageDescription“ *Ø*∞-ŸÅc_HInfo.plist Values||INFOPLIST_KEY_NSFileProviderPresenceUsageDescription“ *Ø*∞-‹Åc_?Info.plist Values||INFOPLIST_KEY_NSFocusStatusUsageDescription“ *Ø*∞-ﬂÅc_@Info.plist Values||INFOPLIST_KEY_NSGKFriendListUsageDescription“ *Ø*∞-‚Åc_TInfo.plist Values||INFOPLIST_KEY_NSHealthClinicalHealthRecordsShareUsageDescription“ *Ø*∞-ÂÅc_?Info.plist Values||INFOPLIST_KEY_NSHealthShareUsageDescription“ *Ø*∞-ËÅc_@Info.plist Values||INFOPLIST_KEY_NSHealthUpdateUsageDescription“ *Ø*∞-ÎÅc_;Info.plist Values||INFOPLIST_KEY_NSHomeKitUsageDescription“ *Ø*∞-ÓÅc_<Info.plist Values||INFOPLIST_KEY_NSIdentityUsageDescription“ *Ø*∞-ÒÅc_@Info.plist Values||INFOPLIST_KEY_NSLocalNetworkUsageDescription“ *Ø*∞-ÙÅc_BInfo.plist Values||INFOPLIST_KEY_NSLocationAlwaysUsageDescription“ *Ø*∞-˜Åc_NInfo.plist Values||INFOPLIST_KEY_NSLocationAlwaysAndWhenInUseUsageDescription“ *Ø*∞-˙Åc_OInfo.plist Values||INFOPLIST_KEY_NSLocationTemporaryUsageDescriptionDictionary“ *Ø*∞-˝Åc_<Info.plist Values||INFOPLIST_KEY_NSLocationUsageDescription“ *Ø*∞. Åc_EInfo.plist Values||INFOPLIST_KEY_NSLocationWhenInUseUsageDescription“ *Ø*∞.Åc_>Info.plist Values||INFOPLIST_KEY_NSAppleMusicUsageDescription“ *Ø*∞.Åc_>Info.plist Values||INFOPLIST_KEY_NSMicrophoneUsageDescription“ *Ø*∞.	Åc_:Info.plist Values||INFOPLIST_KEY_NSMotionUsageDescription“ *Ø*∞.Åc_;Info.plist Values||INFOPLIST_KEY_NFCReaderUsageDescription“ *Ø*∞.Åc_NInfo.plist Values||INFOPLIST_KEY_NSNearbyInteractionAllowOnceUsageDescription“ *Ø*∞.Åc_EInfo.plist Values||INFOPLIST_KEY_NSNearbyInteractionUsageDescription“ *Ø*∞.Åc_BInfo.plist Values||INFOPLIST_KEY_NSNetworkVolumesUsageDescription“ *Ø*∞.Åc_CInfo.plist Values||INFOPLIST_KEY_NSPhotoLibraryAddUsageDescription“ *Ø*∞.Åc_@Info.plist Values||INFOPLIST_KEY_NSPhotoLibraryUsageDescription“ *Ø*∞.Åc_=Info.plist Values||INFOPLIST_KEY_NSRemindersUsageDescription“ *Ø*∞.!Åc_DInfo.plist Values||INFOPLIST_KEY_NSRemovableVolumesUsageDescription“ *Ø*∞.$Åc_=Info.plist Values||INFOPLIST_KEY_NSSensorKitPrivacyPolicyURL“ *Ø*∞.'Åc_=Info.plist Values||INFOPLIST_KEY_NSSensorKitUsageDescription“ *Ø*∞.*Åc_8Info.plist Values||INFOPLIST_KEY_NSSiriUsageDescription“ *Ø*∞.-Åc_EInfo.plist Values||INFOPLIST_KEY_NSSpeechRecognitionUsageDescription“ *Ø*∞.0Åc_HInfo.plist Values||INFOPLIST_KEY_NSSystemAdministrationUsageDescription“ *Ø*∞.3Åc_CInfo.plist Values||INFOPLIST_KEY_NSSystemExtensionUsageDescription“ *Ø*∞.6Åc_JInfo.plist Values||INFOPLIST_KEY_NSVideoSubscriberAccountUsageDescription“ *Ø*∞.9Åc_@Info.plist Values||INFOPLIST_KEY_NSUserTrackingUsageDescription“ *Ø*∞.<Åc_8Info.plist Values||INFOPLIST_KEY_NSVoIPUsageDescription“ *Ø*∞.?Åc_>Info.plist Values||INFOPLIST_KEY_UIRequiredDeviceCapabilities“ *Ø*∞.BÅc_6Info.plist Values||INFOPLIST_KEY_UIRequiresFullScreen“ *Ø*∞.EÅc_3Info.plist Values||INFOPLIST_KEY_UIStatusBarHidden“ *Ø*∞.HÅc_2Info.plist Values||INFOPLIST_KEY_UIStatusBarStyle“ *Ø*∞.KÅc_7Info.plist Values||INFOPLIST_KEY_NSStickerSharingLevel“ *Ø*∞.NÅc_BInfo.plist Values||INFOPLIST_KEY_UISupportedInterfaceOrientations“ *Ø*∞.QÅc_GInfo.plist Values||INFOPLIST_KEY_UISupportedInterfaceOrientations_iPad“ *Ø*∞.TÅc_IInfo.plist Values||INFOPLIST_KEY_UISupportedInterfaceOrientations_iPhone“ *Ø*∞.WÅc_;Info.plist Values||INFOPLIST_KEY_UISupportsDocumentBrowser“ *Ø*∞.ZÅc_IInfo.plist Values||INFOPLIST_KEY_NSSupportsLiveActivitiesFrequentUpdates“ *Ø*∞.]Åc_EInfo.plist Values||INFOPLIST_KEY_GCSupportsControllerUserInteraction“ *Ø*∞.`Åc_4Info.plist Values||INFOPLIST_KEY_GCSupportsGameMode“ *Ø*∞.cÅc_JInfo.plist Values||INFOPLIST_KEY_UIApplicationSupportsIndirectInputEvents“ *Ø*∞.fÅc_LInfo.plist Values||INFOPLIST_KEY_WKSupportsLiveActivityLaunchAttributeTypes“ *Ø*∞.iÅc_:Info.plist Values||INFOPLIST_KEY_NSSupportsLiveActivities“ *Ø*∞.lÅc_CInfo.plist Values||INFOPLIST_KEY_LSSupportsOpeningDocumentsInPlace“ *Ø*∞.oÅc_6Info.plist Values||INFOPLIST_KEY_UIMainStoryboardFile“ *Ø*∞.rÅc_6Info.plist Values||INFOPLIST_KEY_UIUserInterfaceStyle“ *Ø*∞.uÅc_@Info.plist Values||INFOPLIST_KEY_WKCompanionAppBundleIdentifier“ *Ø*∞.xÅc_>Info.plist Values||INFOPLIST_KEY_WKExtensionDelegateClassName“ *Ø*∞.{Åc_Kernel Module||MODULE_NAME“ *Ø*∞.~Åc_Kernel Module||MODULE_START“ *Ø*∞.ÅÅc_Kernel Module||MODULE_STOP“ *Ø*∞.ÑÅc_Kernel Module||MODULE_VERSION“ *Ø*∞.áÅc_!Linking - General||BUNDLE_LOADER“ *Ø*∞.äÅc_"Linking - General||LD_CLIENT_NAME“ *Ø*∞.çÅc_/Linking - General||DYLIB_COMPATIBILITY_VERSION“ *Ø*∞.êÅc_)Linking - General||DYLIB_CURRENT_VERSION“ *Ø*∞.ìÅc_'Linking - General||DEAD_CODE_STRIPPING“ *Ø*∞.ñÅc_1Linking - General||LINKER_DISPLAYS_MANGLED_NAMES“ *Ø*∞.ôÅc_.Linking - General||LD_DYLIB_ALLOWABLE_CLIENTS“ *Ø*∞.úÅc_)Linking - General||LD_DYLIB_INSTALL_NAME“ *Ø*∞.üÅc_+Linking - General||DYLIB_INSTALL_NAME_BASE“ *Ø*∞.¢Åc_"Linking - General||LD_ENVIRONMENT“ *Ø*∞.•Åc_%Linking - General||LD_EXPORT_SYMBOLS“ *Ø*∞.®Åc_)Linking - General||EXPORTED_SYMBOLS_FILE“ *Ø*∞.´Åc_Linking - General||LD_NO_PIE“ *Ø*∞.ÆÅc_ Linking - General||INIT_ROUTINE“ *Ø*∞.±Åc_0Linking - General||LINK_WITH_STANDARD_LIBRARIES“ *Ø*∞.¥Åc_Linking - General||MACH_O_TYPE“ *Ø*∞.∑Åc_Linking - General||ORDER_FILE“ *Ø*∞.∫Åc_&Linking - General||OTHER_LIBTOOLFLAGS“ *Ø*∞.ΩÅc_!Linking - General||OTHER_LDFLAGS“ *Ø*∞.¿Åc_/Linking - General||GENERATE_MASTER_OBJECT_FILE“ *Ø*∞.√Åc_ Linking - General||PRELINK_LIBS“ *Ø*∞.∆Åc_(Linking - General||KEEP_PRIVATE_EXTERNS“ *Ø*∞.…Åc_ALinking - General||LD_QUOTE_LINKER_ARGUMENTS_FOR_COMPILER_DRIVER“ *Ø*∞.ÃÅc_.Linking - General||REEXPORTED_FRAMEWORK_NAMES“ *Ø*∞.œÅc_,Linking - General||REEXPORTED_LIBRARY_NAMES“ *Ø*∞.“Åc_,Linking - General||REEXPORTED_LIBRARY_PATHS“ *Ø*∞.’Åc_+Linking - General||LD_RUNPATH_SEARCH_PATHS“ *Ø*∞.ÿÅc_(Linking - General||SEPARATE_SYMBOL_EDIT“ *Ø*∞.€Åc_!Linking - General||PRELINK_FLAGS“ *Ø*∞.ﬁÅc_#Linking - General||SECTORDER_FLAGS“ *Ø*∞.·Åc_+Linking - General||UNEXPORTED_SYMBOLS_FILE“ *Ø*∞.‰Åc_(Linking - General||LD_GENERATE_MAP_FILE“ *Ø*∞.ÁÅc_1Linking - Mergeable Libraries||MERGEABLE_LIBRARY“ *Ø*∞.ÍÅc_2Linking - Mergeable Libraries||MERGED_BINARY_TYPE“ *Ø*∞.ÌÅc_0Linking - Warnings||LD_WARN_DUPLICATE_LIBRARIES“ *Ø*∞.Åc_*Linking - Warnings||LD_WARN_UNUSED_DYLIBS“ *Ø*∞.ÛÅc_,Localization||LOCALIZATION_EXPORT_SUPPORTED“ *Ø*∞.ˆÅc_3Localization||LOCALIZATION_PREFERS_STRING_CATALOGS“ *Ø*∞.˘Åc_+Localization||LOCALIZED_STRING_MACRO_NAMES“ *Ø*∞.¸Åc_/Localization||LOCALIZED_STRING_SWIFTUI_SUPPORT“ *Ø*∞.ˇÅc_%Localization||SWIFT_EMIT_LOC_STRINGS“ *Ø*∞/Åc_)Packaging||STRINGS_FILE_INFOPLIST_RENAME“ *Ø*∞/Åc_%Packaging||APPLY_RULES_IN_COPY_FILES“ *Ø*∞/Åc_.Packaging||CREATE_INFOPLIST_SECTION_IN_BINARY“ *Ø*∞/Åc_Packaging||DEFINES_MODULE“ *Ø*∞/Åc_(Packaging||DONT_GENERATE_INFOPLIST_FILE“ *Ø*∞/Åc_ Packaging||EXECUTABLE_EXTENSION“ *Ø*∞/Åc_Packaging||EXECUTABLE_PREFIX“ *Ø*∞/Åc_+Packaging||INFOPLIST_EXPAND_BUILD_SETTINGS“ *Ø*∞/Åc_!Packaging||GENERATE_PKGINFO_FILE“ *Ø*∞/Åc_Packaging||FRAMEWORK_VERSION“ *Ø*∞/ Åc_#Packaging||GENERATE_INFOPLIST_FILE“ *Ø*∞/#Åc_Packaging||INFOPLIST_FILE“ *Ø*∞/&Åc_.Packaging||INFOPLIST_OTHER_PREPROCESSOR_FLAGS“ *Ø*∞/)Åc_#Packaging||INFOPLIST_OUTPUT_FORMAT“ *Ø*∞/,Åc_.Packaging||INFOPLIST_PREPROCESSOR_DEFINITIONS“ *Ø*∞//Åc_#Packaging||INFOPLIST_PREFIX_HEADER“ *Ø*∞/2Åc_Packaging||MODULEMAP_FILE“ *Ø*∞/5Åc_ Packaging||INFOPLIST_PREPROCESS“ *Ø*∞/8Åc_&Packaging||COPYING_PRESERVES_HFS_DATA“ *Ø*∞/;Åc_'Packaging||PRIVATE_HEADERS_FOLDER_PATH“ *Ø*∞/>Åc_"Packaging||MODULEMAP_PRIVATE_FILE“ *Ø*∞/AÅc_'Packaging||APPLY_RULES_IN_COPY_HEADERS“ *Ø*∞/DÅc_%Packaging||PRODUCT_BUNDLE_IDENTIFIER“ *Ø*∞/GÅc_Packaging||PRODUCT_MODULE_NAME“ *Ø*∞/JÅc_Packaging||PRODUCT_NAME“ *Ø*∞/MÅc_$Packaging||PLIST_FILE_OUTPUT_FORMAT“ *Ø*∞/PÅc_&Packaging||PUBLIC_HEADERS_FOLDER_PATH“ *Ø*∞/SÅc_(Packaging||STRINGS_FILE_OUTPUT_ENCODING“ *Ø*∞/VÅc_Packaging||WRAPPER_EXTENSION“ *Ø*∞/YÅc_'Search Paths||ALWAYS_SEARCH_USER_PATHS“ *Ø*∞/\Åc_%Search Paths||FRAMEWORK_SEARCH_PATHS“ *Ø*∞/_Åc_"Search Paths||HEADER_SEARCH_PATHS“ *Ø*∞/bÅc_#Search Paths||LIBRARY_SEARCH_PATHS“ *Ø*∞/eÅc_Search Paths||REZ_SEARCH_PATHS“ *Ø*∞/hÅc_<Search Paths||EXCLUDED_RECURSIVE_SEARCH_PATH_SUBDIRECTORIES“ *Ø*∞/kÅc_<Search Paths||INCLUDED_RECURSIVE_SEARCH_PATH_SUBDIRECTORIES“ *Ø*∞/nÅc_,Search Paths||SYSTEM_FRAMEWORK_SEARCH_PATHS“ *Ø*∞/qÅc_)Search Paths||SYSTEM_HEADER_SEARCH_PATHS“ *Ø*∞/tÅc_Search Paths||USE_HEADERMAP“ *Ø*∞/wÅc_'Search Paths||USER_HEADER_SEARCH_PATHS“ *Ø*∞/zÅc_ Signing||CODE_SIGN_ENTITLEMENTS“ *Ø*∞/}Åc_Signing||CODE_SIGN_IDENTITY“ *Ø*∞/ÄÅc_,Signing||CODE_SIGN_INJECT_BASE_ENTITLEMENTS“ *Ø*∞/ÉÅc_Signing||CODE_SIGN_STYLE“ *Ø*∞/ÜÅc_Signing||DEVELOPMENT_TEAM“ *Ø*∞/âÅc_Signing||ENABLE_APP_SANDBOX“ *Ø*∞/åÅc_!Signing||ENABLE_HARDENED_RUNTIME“ *Ø*∞/èÅc_$Signing||ENABLE_USER_SELECTED_FILES“ *Ø*∞/íÅc_"Signing||LAUNCH_CONSTRAINT_PARENT“ *Ø*∞/ïÅc_ Signing||LAUNCH_CONSTRAINT_SELF“ *Ø*∞/òÅc_'Signing||LAUNCH_CONSTRAINT_RESPONSIBLE“ *Ø*∞/õÅc_!Signing||LIBRARY_LOAD_CONSTRAINT“ *Ø*∞/ûÅc_Signing||OTHER_CODE_SIGN_FLAGS“ *Ø*∞/°Åc_(Signing||PROVISIONING_PROFILE_SPECIFIER“ *Ø*∞/§Åc_-Siri||APP_SHORTCUTS_ENABLE_FLEXIBLE_MATCHING“ *Ø*∞/ßÅc_Testing||TEST_HOST“ *Ø*∞/™Åc_2Testing||TREAT_MISSING_BASELINES_AS_TEST_FAILURES“ *Ø*∞/≠Åc_7Text-Based API||GENERATE_INTERMEDIATE_TEXT_BASED_STUBS“ *Ø*∞/∞Åc_,Text-Based API||TAPI_ENABLE_PROJECT_HEADERS“ *Ø*∞/≥Åc_*Text-Based API||GENERATE_TEXT_BASED_STUBS“ *Ø*∞/∂Åc_*Text-Based API||INLINE_PRIVATE_FRAMEWORKS“ *Ø*∞/πÅc_-Text-Based API||TAPI_EXCLUDE_PRIVATE_HEADERS“ *Ø*∞/ºÅc_-Text-Based API||TAPI_EXCLUDE_PROJECT_HEADERS“ *Ø*∞/øÅc_,Text-Based API||TAPI_EXCLUDE_PUBLIC_HEADERS“ *Ø*∞/¬Åc_+Text-Based API||TAPI_EXTRA_PRIVATE_HEADERS“ *Ø*∞/≈Åc_+Text-Based API||TAPI_EXTRA_PROJECT_HEADERS“ *Ø*∞/»Åc_*Text-Based API||TAPI_EXTRA_PUBLIC_HEADERS“ *Ø*∞/ÀÅc_!Text-Based API||OTHER_TAPI_FLAGS“ *Ø*∞/ŒÅc_(Text-Based API||SUPPORTS_TEXT_BASED_API“ *Ø*∞/—Åc_Text-Based API||TAPI_DEMANGLE“ *Ø*∞/‘Åc_'Text-Based API||TAPI_LANGUAGE_STANDARD“ *Ø*∞/◊Åc_Text-Based API||TAPI_LANGUAGE“ *Ø*∞/⁄Åc_!Text-Based API||TAPI_VERIFY_MODE“ *Ø*∞/›Åc_$Versioning||CURRENT_PROJECT_VERSION“ *Ø*∞/‡Åc_Versioning||VERSION_INFO_FILE“ *Ø*∞/„Åc_%Versioning||VERSION_INFO_EXPORT_DECL“ *Ø*∞/ÊÅc_Versioning||MARKETING_VERSION“ *Ø*∞/ÈÅc_ Versioning||VERSION_INFO_PREFIX“ *Ø*∞/ÏÅc_ Versioning||VERSION_INFO_SUFFIX“ *Ø*∞/ÔÅc_Versioning||VERSIONING_SYSTEM“ *Ø*∞/ÚÅc_!Versioning||VERSION_INFO_BUILDER“ *Ø*∞/ıÅc_KApple Clang - Address Sanitizer||CLANG_ADDRESS_SANITIZER_CONTAINER_OVERFLOW“ *Ø*∞/¯Åc_<Apple Clang - Code Generation||CLANG_DEBUG_INFORMATION_LEVEL“ *Ø*∞/˚Åc_<Apple Clang - Code Generation||CLANG_X86_VECTOR_INSTRUCTIONS“ *Ø*∞/˛Åc_2Apple Clang - Code Generation||GCC_STRICT_ALIASING“ *Ø*∞0Åc_=Apple Clang - Code Generation||GCC_GENERATE_DEBUGGING_SYMBOLS“ *Ø*∞0Åc_?Apple Clang - Code Generation||GCC_GENERATE_TEST_COVERAGE_FILES“ *Ø*∞0Åc_1Apple Clang - Code Generation||GCC_DYNAMIC_NO_PIC“ *Ø*∞0
Åc_=Apple Clang - Code Generation||GCC_INLINES_ARE_PRIVATE_EXTERN“ *Ø*∞0Åc_?Apple Clang - Code Generation||GCC_INSTRUMENT_PROGRAM_FLOW_ARCS“ *Ø*∞0Åc_<Apple Clang - Code Generation||GCC_ENABLE_KERNEL_DEVELOPMENT“ *Ø*∞0Åc_'Apple Clang - Code Generation||LLVM_LTO“ *Ø*∞0Åc_0Apple Clang - Code Generation||GCC_REUSE_STRINGS“ *Ø*∞0Åc_3Apple Clang - Code Generation||GCC_NO_COMMON_BLOCKS“ *Ø*∞0Åc_,Apple Clang - Code Generation||GCC_FAST_MATH“ *Ø*∞0Åc_5Apple Clang - Code Generation||GCC_THREADSAFE_STATICS“ *Ø*∞0"Åc_9Apple Clang - Code Generation||GCC_SYMBOLS_PRIVATE_EXTERN“ *Ø*∞0%Åc_/Apple Clang - Code Generation||GCC_UNROLL_LOOPS“ *Ø*∞0(Åc_=Apple Clang - Code Generation||CLANG_USE_OPTIMIZATION_PROFILE“ *Ø*∞0+Åc_1Apple Clang - Custom Compiler Flags||OTHER_CFLAGS“ *Ø*∞0.Åc_9Apple Clang - Custom Compiler Flags||OTHER_CPLUSPLUSFLAGS“ *Ø*∞01Åc_3Apple Clang - Custom Compiler Flags||WARNING_CFLAGS“ *Ø*∞04Åc_1Apple Clang - Language||GCC_CHAR_IS_UNSIGNED_CHAR“ *Ø*∞07Åc_.Apple Clang - Language||GCC_ENABLE_ASM_KEYWORD“ *Ø*∞0:Åc_/Apple Clang - Language||GCC_C_LANGUAGE_STANDARD“ *Ø*∞0=Åc_)Apple Clang - Language||GCC_CW_ASM_SYNTAX“ *Ø*∞0@Åc_*Apple Clang - Language||GCC_INPUT_FILETYPE“ *Ø*∞0CÅc_7Apple Clang - Language||GCC_LINK_WITH_DYNAMIC_LIBRARIES“ *Ø*∞0FÅc_,Apple Clang - Language||GCC_ENABLE_TRIGRAPHS“ *Ø*∞0IÅc_?Apple Clang - Language||GCC_ENABLE_FLOATING_POINT_LIBRARY_CALLS“ *Ø*∞0LÅc_?Apple Clang - Language||GCC_INCREASE_PRECOMPILED_HEADER_SHARING“ *Ø*∞0OÅc_4Apple Clang - Language||GCC_PRECOMPILE_PREFIX_HEADER“ *Ø*∞0RÅc_)Apple Clang - Language||GCC_PREFIX_HEADER“ *Ø*∞0UÅc_4Apple Clang - Language||GCC_ENABLE_BUILTIN_FUNCTIONS“ *Ø*∞0XÅc_1Apple Clang - Language||GCC_ENABLE_PASCAL_STRINGS“ *Ø*∞0[Åc_'Apple Clang - Language||GCC_SHORT_ENUMS“ *Ø*∞0^Åc_/Apple Clang - Language||CLANG_USE_RESPONSE_FILE“ *Ø*∞0aÅc_:Apple Clang - Language||GCC_USE_STANDARD_INCLUDE_SEARCHING“ *Ø*∞0dÅc_9Apple Clang - Language - C++||CLANG_CXX_LANGUAGE_STANDARD“ *Ø*∞0gÅc_AApple Clang - Language - C++||CLANG_ENABLE_CPP_STATIC_DESTRUCTORS“ *Ø*∞0jÅc_7Apple Clang - Language - C++||GCC_ENABLE_CPP_EXCEPTIONS“ *Ø*∞0mÅc_1Apple Clang - Language - C++||GCC_ENABLE_CPP_RTTI“ *Ø*∞0pÅc_WApple Clang - Language - Modules||CLANG_ALLOW_NON_MODULAR_INCLUDES_IN_FRAMEWORK_MODULES“ *Ø*∞0sÅc_GApple Clang - Language - Modules||CLANG_MODULES_DISABLE_PRIVATE_WARNING“ *Ø*∞0vÅc_?Apple Clang - Language - Modules||CLANG_ENABLE_MODULE_DEBUGGING“ *Ø*∞0yÅc_6Apple Clang - Language - Modules||CLANG_ENABLE_MODULES“ *Ø*∞0|Åc_8Apple Clang - Language - Modules||CLANG_MODULES_AUTOLINK“ *Ø*∞0Åc_FApple Clang - Language - Objective-C||CLANG_ENABLE_OBJC_ARC_EXCEPTIONS“ *Ø*∞0ÇÅc_@Apple Clang - Language - Objective-C||GCC_ENABLE_OBJC_EXCEPTIONS“ *Ø*∞0ÖÅc_=Apple Clang - Language - Objective-C||CLANG_LINK_OBJC_RUNTIME“ *Ø*∞0àÅc_;Apple Clang - Language - Objective-C||CLANG_ENABLE_OBJC_ARC“ *Ø*∞0ãÅc_<Apple Clang - Language - Objective-C||CLANG_ENABLE_OBJC_WEAK“ *Ø*∞0éÅc_7Apple Clang - Preprocessing||ENABLE_STRICT_OBJC_MSGSEND“ *Ø*∞0ëÅc_NApple Clang - Preprocessing||GCC_PREPROCESSOR_DEFINITIONS_NOT_USED_IN_PRECOMPS“ *Ø*∞0îÅc_VApple Clang - Undefined Behavior Sanitizer||CLANG_UNDEFINED_BEHAVIOR_SANITIZER_INTEGER“ *Ø*∞0óÅc_ZApple Clang - Undefined Behavior Sanitizer||CLANG_UNDEFINED_BEHAVIOR_SANITIZER_NULLABILITY“ *Ø*∞0öÅc_=Apple Clang - Warning Policies||GCC_WARN_INHIBIT_ALL_WARNINGS“ *Ø*∞0ùÅc_1Apple Clang - Warning Policies||GCC_WARN_PEDANTIC“ *Ø*∞0†Åc_<Apple Clang - Warning Policies||GCC_TREAT_WARNINGS_AS_ERRORS“ *Ø*∞0£Åc_NApple Clang - Warnings - All languages||CLANG_WARN_BLOCK_CAPTURE_AUTORELEASING“ *Ø*∞0¶Åc_HApple Clang - Warnings - All languages||GCC_WARN_CHECK_SWITCH_STATEMENTS“ *Ø*∞0©Åc_LApple Clang - Warnings - All languages||CLANG_WARN_COMPLETION_HANDLER_MISUSE“ *Ø*∞0¨Åc_KApple Clang - Warnings - All languages||GCC_WARN_ABOUT_DEPRECATED_FUNCTIONS“ *Ø*∞0ØÅc_IApple Clang - Warnings - All languages||CLANG_WARN_DOCUMENTATION_COMMENTS“ *Ø*∞0≤Åc_=Apple Clang - Warnings - All languages||CLANG_WARN_EMPTY_BODY“ *Ø*∞0µÅc_IApple Clang - Warnings - All languages||GCC_WARN_FOUR_CHARACTER_CONSTANTS“ *Ø*∞0∏Åc_7Apple Clang - Warnings - All languages||GCC_WARN_SHADOW“ *Ø*∞0ªÅc_BApple Clang - Warnings - All languages||CLANG_WARN_BOOL_CONVERSION“ *Ø*∞0æÅc_FApple Clang - Warnings - All languages||CLANG_WARN_CONSTANT_CONVERSION“ *Ø*∞0¡Åc_BApple Clang - Warnings - All languages||CLANG_WARN_ENUM_CONVERSION“ *Ø*∞0ƒÅc_GApple Clang - Warnings - All languages||CLANG_WARN_IMPLICIT_FALLTHROUGH“ *Ø*∞0«Åc_CApple Clang - Warnings - All languages||CLANG_WARN_FLOAT_CONVERSION“ *Ø*∞0 Åc_AApple Clang - Warnings - All languages||CLANG_WARN_INT_CONVERSION“ *Ø*∞0ÕÅc_NApple Clang - Warnings - All languages||CLANG_WARN_NON_LITERAL_NULL_CONVERSION“ *Ø*∞0–Åc_KApple Clang - Warnings - All languages||CLANG_WARN_IMPLICIT_SIGN_CONVERSION“ *Ø*∞0”Åc_EApple Clang - Warnings - All languages||CLANG_WARN_INFINITE_RECURSION“ *Ø*∞0÷Åc_PApple Clang - Warnings - All languages||GCC_WARN_INITIALIZER_NOT_FULLY_BRACKETED“ *Ø*∞0ŸÅc_BApple Clang - Warnings - All languages||GCC_WARN_ABOUT_RETURN_TYPE“ *Ø*∞0‹Åc_DApple Clang - Warnings - All languages||GCC_WARN_MISSING_PARENTHESES“ *Ø*∞0ﬂÅc_QApple Clang - Warnings - All languages||GCC_WARN_ABOUT_MISSING_FIELD_INITIALIZERS“ *Ø*∞0‚Åc_IApple Clang - Warnings - All languages||GCC_WARN_ABOUT_MISSING_PROTOTYPES“ *Ø*∞0ÂÅc_FApple Clang - Warnings - All languages||GCC_WARN_ABOUT_MISSING_NEWLINE“ *Ø*∞0ËÅc_>Apple Clang - Warnings - All languages||CLANG_WARN_ASSIGN_ENUM“ *Ø*∞0ÎÅc_AApple Clang - Warnings - All languages||CLANG_WARN_PRIVATE_MODULE“ *Ø*∞0ÓÅc_IApple Clang - Warnings - All languages||GCC_WARN_ABOUT_POINTER_SIGNEDNESS“ *Ø*∞0ÒÅc_XApple Clang - Warnings - All languages||CLANG_WARN_FRAMEWORK_INCLUDE_PRIVATE_FROM_PUBLIC“ *Ø*∞0ÙÅc_UApple Clang - Warnings - All languages||CLANG_WARN_QUOTED_INCLUDE_IN_FRAMEWORK_HEADER“ *Ø*∞0˜Åc_OApple Clang - Warnings - All languages||CLANG_WARN_SEMICOLON_BEFORE_METHOD_BODY“ *Ø*∞0˙Åc_=Apple Clang - Warnings - All languages||GCC_WARN_SIGN_COMPARE“ *Ø*∞0˝Åc_DApple Clang - Warnings - All languages||CLANG_WARN_STRICT_PROTOTYPES“ *Ø*∞1 Åc_8Apple Clang - Warnings - All languages||CLANG_WARN_COMMA“ *Ø*∞1Åc_QApple Clang - Warnings - All languages||CLANG_WARN_SUSPICIOUS_IMPLICIT_CONVERSION“ *Ø*∞1Åc_>Apple Clang - Warnings - All languages||CLANG_WARN_PRAGMA_PACK“ *Ø*∞1	Åc_^Apple Clang - Warnings - All languages||GCC_TREAT_INCOMPATIBLE_POINTER_TYPE_WARNINGS_AS_ERRORS“ *Ø*∞1Åc_ZApple Clang - Warnings - All languages||GCC_TREAT_IMPLICIT_FUNCTION_DECLARATIONS_AS_ERRORS“ *Ø*∞1Åc_JApple Clang - Warnings - All languages||GCC_WARN_TYPECHECK_CALLS_TO_PRINTF“ *Ø*∞1Åc_IApple Clang - Warnings - All languages||CLANG_WARN_UNGUARDED_AVAILABILITY“ *Ø*∞1Åc_DApple Clang - Warnings - All languages||GCC_WARN_UNINITIALIZED_AUTOS“ *Ø*∞1Åc_@Apple Clang - Warnings - All languages||GCC_WARN_UNKNOWN_PRAGMAS“ *Ø*∞1Åc_CApple Clang - Warnings - All languages||CLANG_WARN_UNREACHABLE_CODE“ *Ø*∞1Åc_@Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_FUNCTION“ *Ø*∞1!Åc_=Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_LABEL“ *Ø*∞1$Åc_AApple Clang - Warnings - All languages||GCC_WARN_UNUSED_PARAMETER“ *Ø*∞1'Åc_=Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_VALUE“ *Ø*∞1*Åc_@Apple Clang - Warnings - All languages||GCC_WARN_UNUSED_VARIABLE“ *Ø*∞1-Åc_5Apple Clang - Warnings - C++||CLANG_WARN_VEXING_PARSE“ *Ø*∞10Åc_@Apple Clang - Warnings - C++||CLANG_WARN_DELETE_NON_VIRTUAL_DTOR“ *Ø*∞13Åc_?Apple Clang - Warnings - C++||CLANG_WARN__EXIT_TIME_DESTRUCTORS“ *Ø*∞16Åc_=Apple Clang - Warnings - C++||GCC_WARN_NON_VIRTUAL_DESTRUCTOR“ *Ø*∞19Åc_?Apple Clang - Warnings - C++||GCC_WARN_HIDDEN_VIRTUAL_FUNCTIONS“ *Ø*∞1<Åc_<Apple Clang - Warnings - C++||CLANG_WARN_RANGE_LOOP_ANALYSIS“ *Ø*∞1?Åc_8Apple Clang - Warnings - C++||CLANG_WARN_SUSPICIOUS_MOVE“ *Ø*∞1BÅc_CApple Clang - Warnings - C++||GCC_WARN_ABOUT_INVALID_OFFSETOF_MACRO“ *Ø*∞1EÅc_@Apple Clang - Warnings - C++||CLANG_WARN_ATOMIC_IMPLICIT_SEQ_CST“ *Ø*∞1HÅc_9Apple Clang - Warnings - C++||CLANG_WARN_CXX0X_EXTENSIONS“ *Ø*∞1KÅc_FApple Clang - Warnings - Objective-C||CLANG_WARN_DIRECT_OBJC_ISA_USAGE“ *Ø*∞1NÅc_HApple Clang - Warnings - Objective-C||CLANG_WARN__DUPLICATE_METHOD_MATCH“ *Ø*∞1QÅc_PApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_IMPLICIT_ATOMIC_PROPERTIES“ *Ø*∞1TÅc_HApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_LITERAL_CONVERSION“ *Ø*∞1WÅc_PApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_MISSING_PROPERTY_SYNTHESIS“ *Ø*∞1ZÅc_HApple Clang - Warnings - Objective-C||GCC_WARN_ALLOW_INCOMPLETE_PROTOCOL“ *Ø*∞1]Åc_EApple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_INTERFACE_IVARS“ *Ø*∞1`Åc_AApple Clang - Warnings - Objective-C||CLANG_WARN_MISSING_NOESCAPE“ *Ø*∞1cÅc_PApple Clang - Warnings - Objective-C||CLANG_WARN_DEPRECATED_OBJC_IMPLEMENTATIONS“ *Ø*∞1fÅc_DApple Clang - Warnings - Objective-C||GCC_WARN_STRICT_SELECTOR_MATCH“ *Ø*∞1iÅc_BApple Clang - Warnings - Objective-C||GCC_WARN_UNDECLARED_SELECTOR“ *Ø*∞1lÅc_@Apple Clang - Warnings - Objective-C||CLANG_WARN_OBJC_ROOT_CLASS“ *Ø*∞1oÅc_UApple Clang - Warnings - Objective-C and ARC||CLANG_WARN_OBJC_EXPLICIT_OWNERSHIP_TYPE“ *Ø*∞1rÅc_RApple Clang - Warnings - Objective-C and ARC||CLANG_WARN_OBJC_IMPLICIT_RETAIN_SELF“ *Ø*∞1uÅc_RApple Clang - Warnings - Objective-C and ARC||CLANG_WARN_OBJC_REPEATED_USE_OF_WEAK“ *Ø*∞1xÅc_PApple Clang - Warnings - Objective-C and ARC||CLANG_WARN__ARC_BRIDGE_CAST_NONARC“ *Ø*∞1{Åc_=Apple Clang Module Verifier - Options||ENABLE_MODULE_VERIFIER“ *Ø*∞1~Åc_BApple Clang Module Verifier - Options||OTHER_MODULE_VERIFIER_FLAGS“ *Ø*∞1ÅÅc_SApple Clang Module Verifier - Options||MODULE_VERIFIER_SUPPORTED_LANGUAGE_STANDARDS“ *Ø*∞1ÑÅc_JApple Clang Module Verifier - Options||MODULE_VERIFIER_SUPPORTED_LANGUAGES“ *Ø*∞1áÅc_OAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_ALTERNATE_APPICON_NAMES“ *Ø*∞1äÅc_HAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_LAUNCHIMAGE_NAME“ *Ø*∞1çÅc_:Asset Catalog Compiler - Options||ASSETCATALOG_OTHER_FLAGS“ *Ø*∞1êÅc_>Asset Catalog Compiler - Options||ENABLE_ONLY_ACTIVE_RESOURCES“ *Ø*∞1ìÅc_<Asset Catalog Compiler - Options||ENABLE_INCREMENTAL_DISTILL“ *Ø*∞1ñÅc_NAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GENERATE_ASSET_SYMBOLS“ *Ø*∞1ôÅc_^Asset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GENERATE_SWIFT_ASSET_SYMBOL_EXTENSIONS“ *Ø*∞1úÅc_XAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GENERATE_ASSET_SYMBOL_FRAMEWORKS“ *Ø*∞1üÅc_PAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_GLOBAL_ACCENT_COLOR_NAME“ *Ø*∞1¢Åc_RAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_INCLUDE_ALL_APPICON_ASSETS“ *Ø*∞1•Åc_WAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_INCLUDE_INFOPLIST_LOCALIZATIONS“ *Ø*∞1®Åc_OAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_INCLUDE_STICKER_CONTENT“ *Ø*∞1´Åc_DAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_OPTIMIZATION“ *Ø*∞1ÆÅc_DAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_APPICON_NAME“ *Ø*∞1±Åc_6Asset Catalog Compiler - Options||ASSETCATALOG_NOTICES“ *Ø*∞1¥Åc_7Asset Catalog Compiler - Options||ASSETCATALOG_WARNINGS“ *Ø*∞1∑Åc_QAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_SKIP_APP_STORE_DEPLOYMENT“ *Ø*∞1∫Åc_PAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_STANDALONE_ICON_BEHAVIOR“ *Ø*∞1ΩÅc_VAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_STICKER_PACK_IDENTIFIER_PREFIX“ *Ø*∞1¿Åc_QAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_TARGET_STICKERS_ICON_ROLE“ *Ø*∞1√Åc_TAsset Catalog Compiler - Options||ASSETCATALOG_COMPILER_WIDGET_BACKGROUND_COLOR_NAME“ *Ø*∞1∆Åc_MDesigned for iPhone & iPad - Deployment||SUPPORTS_XR_DESIGNED_FOR_IPHONE_IPAD“ *Ø*∞1…Åc_NDesigned for iPhone & iPad - Deployment||SUPPORTS_MAC_DESIGNED_FOR_IPHONE_IPAD“ *Ø*∞1ÃÅc_<Documentation Compiler - Options||RUN_DOCUMENTATION_COMPILER“ *Ø*∞1œÅc_9Documentation Compiler - Options||DOCC_ENABLE_CXX_SUPPORT“ *Ø*∞1“Åc_JDocumentation Compiler - Options||DOCC_EXTRACT_SWIFT_INFO_FOR_OBJC_SYMBOLS“ *Ø*∞1’Åc_JDocumentation Compiler - Options||DOCC_EXTRACT_OBJC_INFO_FOR_SWIFT_SYMBOLS“ *Ø*∞1ÿÅc_8Documentation Compiler - Options||DOCC_HOSTING_BASE_PATH“ *Ø*∞1€Åc_@Documentation Compiler - Options||DOCC_EXTRACT_EXTENSION_SYMBOLS“ *Ø*∞1ﬁÅc_2Documentation Compiler - Options||OTHER_DOCC_FLAGS“ *Ø*∞1·Åc_GMac Catalyst - Deployment||DERIVE_MACCATALYST_PRODUCT_BUNDLE_IDENTIFIER“ *Ø*∞1‰Åc_/Mac Catalyst - Deployment||SUPPORTS_MACCATALYST“ *Ø*∞1ÁÅc_1OSACompile - Build Options||OTHER_OSACOMPILEFLAGS“ *Ø*∞1ÍÅc_3OSACompile - Build Options||OSACOMPILE_EXECUTE_ONLY“ *Ø*∞1ÌÅc_<Static Analysis - Analysis Policy||RUN_CLANG_STATIC_ANALYZER“ *Ø*∞1Åc_OStatic Analysis - Analysis Policy||CLANG_STATIC_ANALYZER_MODE_ON_ANALYZE_ACTION“ *Ø*∞1ÛÅc_=Static Analysis - Analysis Policy||CLANG_STATIC_ANALYZER_MODE“ *Ø*∞1ˆÅc_AStatic Analysis - Generic Issues||CLANG_ANALYZER_NULL_DEREFERENCE“ *Ø*∞1˘Åc_?Static Analysis - Generic Issues||CLANG_ANALYZER_DIVIDE_BY_ZERO“ *Ø*∞1¸Åc_BStatic Analysis - Generic Issues||CLANG_ANALYZER_MEMORY_MANAGEMENT“ *Ø*∞1ˇÅc_CStatic Analysis - Generic Issues||CLANG_TIDY_BUGPRONE_INFINITE_LOOP“ *Ø*∞2Åc_8Static Analysis - Generic Issues||CLANG_ANALYZER_NONNULL“ *Ø*∞2Åc_HStatic Analysis - Generic Issues||CLANG_TIDY_BUGPRONE_ASSERT_SIDE_EFFECT“ *Ø*∞2Åc_KStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_OSOBJECT_C_STYLE_CAST“ *Ø*∞2Åc_DStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_OBJC_NSCFERROR“ *Ø*∞2Åc_QStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_LOCALIZABILITY_NONLOCALIZED“ *Ø*∞2Åc_RStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_LOCALIZABILITY_EMPTY_CONTEXT“ *Ø*∞2Åc_FStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_OBJC_COLLECTIONS“ *Ø*∞2Åc_9Static Analysis - Issues - Apple APIs||CLANG_ANALYZER_GCD“ *Ø*∞2Åc_EStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_GCD_PERFORMANCE“ *Ø*∞2Åc_NStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_NUMBER_OBJECT_CONVERSION“ *Ø*∞2 Åc_JStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_LIBKERN_RETAIN_COUNT“ *Ø*∞2#Åc_EStatic Analysis - Issues - Apple APIs||CLANG_ANALYZER_MIG_CONVENTIONS“ *Ø*∞2&Åc_MStatic Analysis - Issues - C++||CLANG_TIDY_BUGPRONE_MOVE_FORWARDING_REFERENCE“ *Ø*∞2)Åc_=Static Analysis - Issues - C++||CLANG_ANALYZER_USE_AFTER_MOVE“ *Ø*∞2,Åc_BStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_ATSYNC“ *Ø*∞2/Åc_CStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_DEALLOC“ *Ø*∞22Åc_OStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_INCOMP_METHOD_TYPES“ *Ø*∞25Åc_DStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_GENERICS“ *Ø*∞28Åc_HStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_UNUSED_IVARS“ *Ø*∞2;Åc_EStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_SELF_INIT“ *Ø*∞2>Åc_HStatic Analysis - Issues - Objective-C||CLANG_ANALYZER_OBJC_RETAIN_COUNT“ *Ø*∞2AÅc_MStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_FLOATLOOPCOUNTER“ *Ø*∞2DÅc_IStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_KEYCHAIN_API“ *Ø*∞2GÅc_XStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_UNCHECKEDRETURN“ *Ø*∞2JÅc_SStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_GETPW_GETS“ *Ø*∞2MÅc_PStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_MKSTEMP“ *Ø*∞2PÅc_MStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_RAND“ *Ø*∞2SÅc_OStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_STRCPY“ *Ø*∞2VÅc_NStatic Analysis - Issues - Security||CLANG_ANALYZER_SECURITY_INSECUREAPI_VFORK“ *Ø*∞2YÅc_JStatic Analysis - Issues - Unused Code||CLANG_ANALYZER_DEADCODE_DEADSTORES“ *Ø*∞2\Åc_LStatic Analysis - Issues - Unused Code||CLANG_TIDY_MISC_REDUNDANT_EXPRESSION“ *Ø*∞2_Åc_VStatic Analysis - Issues - Unused Code||CLANG_TIDY_BUGPRONE_REDUNDANT_BRANCH_CONDITION“ *Ø*∞2bÅc_=Swift Compiler - Code Generation||SWIFT_DISABLE_SAFETY_CHECKS“ *Ø*∞2eÅc_@Swift Compiler - Code Generation||SWIFT_ENFORCE_EXCLUSIVE_ACCESS“ *Ø*∞2hÅc_0Swift Compiler - Custom Flags||OTHER_SWIFT_FLAGS“ *Ø*∞2kÅc_>Swift Compiler - General||_EXPERIMENTAL_SWIFT_EXPLICIT_MODULES“ *Ø*∞2nÅc_:Swift Compiler - General||SWIFT_OBJC_INTERFACE_HEADER_NAME“ *Ø*∞2qÅc_3Swift Compiler - General||SWIFT_INSTALL_OBJC_HEADER“ *Ø*∞2tÅc_.Swift Compiler - General||SWIFT_INSTALL_MODULE“ *Ø*∞2wÅc_0Swift Compiler - General||SWIFT_MODULES_AUTOLINK“ *Ø*∞2zÅc_4Swift Compiler - General||SWIFT_OBJC_BRIDGING_HEADER“ *Ø*∞2}Åc_,Swift Compiler - General||SWIFT_PACKAGE_NAME“ *Ø*∞2ÄÅc_:Swift Compiler - General||SWIFT_PRECOMPILE_BRIDGING_HEADER“ *Ø*∞2ÉÅc_9Swift Compiler - General||SWIFT_REFLECTION_METADATA_LEVEL“ *Ø*∞2ÜÅc_?Swift Compiler - General||SWIFT_SKIP_AUTOLINKING_ALL_FRAMEWORKS“ *Ø*∞2âÅc_;Swift Compiler - General||SWIFT_SKIP_AUTOLINKING_FRAMEWORKS“ *Ø*∞2åÅc_:Swift Compiler - General||SWIFT_SKIP_AUTOLINKING_LIBRARIES“ *Ø*∞2èÅc_2Swift Compiler - Language||SWIFT_OBJC_INTEROP_MODE“ *Ø*∞2íÅc_9Swift Compiler - Language||SWIFT_ENABLE_EMIT_CONST_VALUES“ *Ø*∞2ïÅc_(Swift Compiler - Language||SWIFT_VERSION“ *Ø*∞2òÅc_2Swift Compiler - Search Paths||SWIFT_INCLUDE_PATHS“ *Ø*∞2õÅc_ASwift Compiler - Upcoming Features||SWIFT_ENABLE_BARE_SLASH_REGEX“ *Ø*∞2ûÅc_MSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_CONCISE_MAGIC_FILE“ *Ø*∞2°Åc_VSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_INTERNAL_IMPORTS_BY_DEFAULT“ *Ø*∞2§Åc_USwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_DEPRECATE_APPLICATION_MAIN“ *Ø*∞2ßÅc_ZSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_DISABLE_OUTWARD_ACTOR_ISOLATION“ *Ø*∞2™Åc_TSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_FORWARD_TRAILING_CLOSURES“ *Ø*∞2≠Åc_USwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_IMPLICIT_OPEN_EXISTENTIALS“ *Ø*∞2∞Åc_TSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_IMPORT_OBJC_FORWARD_DECLS“ *Ø*∞2≥Åc_WSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_INFER_SENDABLE_FROM_CAPTURES“ *Ø*∞2∂Åc_RSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_ISOLATED_DEFAULT_VALUES“ *Ø*∞2πÅc_MSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_GLOBAL_CONCURRENCY“ *Ø*∞2ºÅc_QSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_REGION_BASED_ISOLATION“ *Ø*∞2øÅc_JSwift Compiler - Upcoming Features||SWIFT_UPCOMING_FEATURE_EXISTENTIAL_ANY“ *Ø*∞2¬Åc_<Swift Compiler - Upcoming Features||SWIFT_STRICT_CONCURRENCY“ *Ø*∞2≈Åc_;Swift Compiler - Warnings Policies||SWIFT_SUPPRESS_WARNINGS“ *Ø*∞2»Åc_BSwift Compiler - Warnings Policies||SWIFT_TREAT_WARNINGS_AS_ERRORS“ *Ø*∞2ÀÅc_User-Defined||MTL_FAST_MATH”   2Õ2Œ 8††Ä@”   2—2” °2“Ål°2‘ÅmÄ|]IDENameString[GameCatalog— 2ŸÅo“ \ ]2€2‹VNSNull¢2€ `“  2ﬁ S° ÄÄ)”   2‚2‰ 8° Ä°2ÂÅrÄ@“  2Ë*≠° ÄÅd”   2Ï2 £2Ì2Ó2ÔÅtÅuÅv£2Ò2Ú2ÛÅwÅ}ÅÇÄ|_IDERunContextRecentsSchemesKey_5IDERunContextRecentsLastUsedRunDestinationBySchemeKey_&IDERunContextRecentsRunDestinationsKey”   2˘2¸ 8¢2˙2˚ÅxÅy¢2˝2˛ÅzÅ|Ä@[TheMealsApp[GameCatalog“3 33WNS.time#A∆ŒË0ÓøÅ{“ \ ]33VNSDate¢3 `“3 3
3#A∆€ˇhˆrCÅ{”   33 8¢33Å~Å¢33ÅÄÅÅÄ@[TheMealsApp[GameCatalog“ *Ø*∞3Åc_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64“ *Ø*∞3Åc_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64”   33 8°3ÅÉ°3 ÅÑÄ@_;7A60B588-4926-4A88-9679-4FA36F2ECDD0_iphonesimulator_x86_64“3 3$3#A∆€ˇj‹HÅ{”   3'32 ™3(3)3*3+3,3-3.3/3031ÅÜÅáÅàÅâÅäÅãÅåÅçÅéÅè™ñ34ñ 03738393:3;3<ÄàÅêÄàÄsÅëÅíÅìÅîÅïÅñÄ|ZisEligible_targetDevicePlatform_targetDeviceIsConcrete_targetDeviceIsWireless_targetSDKVariant_targetDeviceLocation_targetArchitectureYtargetSDK_targetDeviceFamily_targetDeviceModelCode_iphonesimulator_iphonesimulator_>dvtdevice-iphonesimulator:7A60B588-4926-4A88-9679-4FA36F2ECDD0Vx86_64_iphonesimulator18.2ViPhoneZiPhone17,1“  3P*≠†Åd”   3S3X 8§3T3U3V3WÅôÅöÅõÅú§3Y3Z3[3\ÅùÅûÅüÅ†Ä@_IDEActivityReportTitle_IDEActivityReportVersion_IDEActivityReportOptions_0IDEActivityReportCompletionSummaryStringSegmentsUBuildX16C5032aj“  3f*≠£3g3h3iÅ°Å¶Å©Åd”   3l3p 8£3m3n3oÅ¢Å£Å§£%Œ3r3YÅÅ•ÅùÄ@_&IDEActivityReportStringSegmentPriority_+IDEActivityReportStringSegmentBackSeparator_)IDEActivityReportStringSegmentStringValueQ ”   3z3~ 8£3m3n3oÅ¢Å£Å§£)3Ä3ÅÄRÅßÅ®Ä@c  %  “ ÿ 3Ö ⁄Obplist00‘
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—	TrootÄ™%&,/U$null”XNSStringV$class\NSAttributesÄÄ	ÄYSucceeded”WNS.keysZNS.objects°Ä°ÄÄVNSFont‘ !"#$VNSSizeXNSfFlagsVNSName#@&      ÄÄ_.AppleSystemUIFontBold“'()*Z$classnameX$classesVNSFont¢)+XNSObject“'(-.\NSDictionary¢-+“'(01_NSAttributedString¢2+_NSAttributedString    $ ) 2 7 I L Q S ^ d k t { à ä å é ò ü ß ≤ ¥ ∂ ∏ ∫ º √ Ã ” ‹ „ Ï Ô Ò Û%,/8=JMRgj             3              Ä=”   3à3è 8¶3m3ä3ã3o3ç3éÅ¢Å™Å´Å§Å¨Å≠¶3êæ3í3ìææÅÆÄzÅØÅ∞ÄzÄzÄ@_"IDEActivityReportStringSegmentType_"IDEActivityReportStringSegmentDate_'IDEActivityReportStringSegmentDateStyle_'IDEActivityReportStringSegmentTimeStyle#@      “3 3ù3#A∆◊2˛GîßÅ{o 1 5 / 0 4 / 2 5   a t   5 . 0 1 / P M”   3°3™ 8®3¢3£3§3•3¶3ß3®3©Å≤Å≥Å¥ÅµÅ∂Å∑Å∏Åπ®ñ3¨ññ 03®3± 0ÄàÅ∫ÄàÄàÄsÅ∏ÅªÄsÄ@_IDEWindowIsFullScreen^IDEWindowFrame_-IDEHasMigratedValuesFromNSRestorableStateData_IDEWindowToolbarIsVisible_&IDEWindowTabBarWasVisibleWithSingleTab_IDEActiveWorkspaceTabController_>IDEWorkspaceTabController_75DACF4F-9477-44F2-97BD-12677AFE5FB7_IDEWindowTabBarIsVisible_209 109 1400 900 0 0 1680 1025 ”   3æ3» 8©3ø3¿3¡3¬3√3ƒ3≈3∆3«ÅºÅΩÅæÅøÅ¿Å¡Å¬Å√Åƒ©ñ3 3À3Ã3Õ3Œ3œ 03—ÄàÅ≈Å/Å0Å4Å5Å7ÄsÅ:Ä@_IDEShowNavigator_IDENavigatorArea_IDEUtilitiesWidth_IDEInspectorArea_IDENavigatorWidth\ViewDebugger_MemoryGraphDebugger_IDEShowUtilities]IDEEditorArea”   3›3Â 8ß3ﬁ3ﬂ3‡3·3‚3„3‰Å∆Å«Å»Å…Å ÅÀÅÃß3Ê3Á3Ë3È3Í3Î3ÏÅÕÅÿÅŸÅ‡ÅÒÅ Å
Ä@_"Xcode.IDEKit.Navigator.Test.Modern_SelectedNavigator_GroupSelections_#Xcode.IDENoticesKit.NoticeNavigator_Xcode.IDEKit.Navigator.Find_ Xcode.IDEKit.Navigator.Workspace_Xcode.IDEKit.Navigator.Debug”   3ˆ3˚ 8§3˜3¯3˘3˙ÅŒÅœÅ–Å—§Ï3˝3˛ÏÅAÅ“Å”ÅAÄ@_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey_IDENavigatorModeSolitary”   44	 °4Å‘°4
Å’Ä|_IDENavigatorModeSolitary“ 444_codablePlistRepresentationÅ◊Å÷O≤bplist00‘_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆Œù¨’Öæ††¢	¢
#        #¿$      ¢#@q      #@å¯     $.<KTUVY\enqz                            É“ \ ]44_&ExplorableOutlineViewArchivableUIState£44 `_&ExplorableOutlineViewArchivableUIState_b_TtGCV16DVTExplorableKit26ExplorableOutlineViewTypes7UIState_VS_31ExplorableStateSavingIdentifier__ Xcode.IDEKit.Navigator.Workspace”   44 8•44444Å⁄Å€Å‹Å›Åﬁ•4 3‰3Á3‚3ﬁÅﬂÅÃÅÿÅ Å∆Ä@_"Xcode.IDEKit.NavigatorGroup.Issues_!Xcode.IDEKit.NavigatorGroup.Debug_%Xcode.IDEKit.NavigatorGroup.Structure_ Xcode.IDEKit.NavigatorGroup.Find_ Xcode.IDEKit.NavigatorGroup.Test_#Xcode.IDENoticesKit.NoticeNavigator”   4-42 8§4.4/4041Å·Å‚Å„Å‰§43444546ÅÂÅËÅÈÅÌÄ@_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey”   4=4? °4>ÅÊ°4@ÅÁÄ|_IDENavigatorModeSolitaryOnbplist00‘
X$versionY$archiverT$topX$objects Ü†_NSKeyedArchiver—	TrootÄ©!$'+,U$null’V$class\operatorType_tokenFieldObjectValue_recentFilterItems_enabledButtonFilterIdentifiersÄ ÄÄÄ“ZNS.objects†Ä“Z$classnameX$classesWNSArray¢ XNSObject“"#†Ä“%&^NSMutableArray£% “(#°)ÄÄ_IDEFilterIdentifier_NoticeError“-.__DVTFilterExpressionStateValue§/01 __DVTFilterExpressionStateValue__DVTFilterTokenStateValue__DVTFilterTokenFieldValue    $ ) 2 7 I L Q S ] c n u Ç ö Æ œ — ” ’ ◊ Ÿ ﬁ È Í Ï Ò ¸!&59>@BDfkåë≤Œ             2              Í_IDENavigatorModeSolitary”   4F4H °4GÅÍ°4IÅÎÄ|_IDENavigatorModeSolitary“ 4M44O_codablePlistRepresentationÅ◊ÅÏO_bplist00‘_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆œ~ˆRH§“	
TpathYindexHint°[TheMealsApp “^expansionState3A∆œS=9Âé“	¢_$Missing package product 'RealmSwift'“3A∆œS=9Â~†¢¢#        #¿$      ¢#@q      #@çÿ        $ . < K T Y ^ c m o { } Ç ë ö ü ¢ … Œ ◊ ÿ € ﬁ Á  Û ¸                           ”   4R4T °4SÅÓ°4UÅÔÄ|_IDENavigatorModeSolitary“ 4Y44[_codablePlistRepresentationÅ◊ÅOÜbplist00‘_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆œ~ˆRz¶“	
TpathYindexHint¢[TheMealsApp_SearchView.swift “^expansionState3A∆œ~±ß®“	°“3A∆œ~±ßè“	¢_RemoteDataSource.swift“3A∆œ~±ß††¢¢#        #¿$      ¢ !#@zp     #@çÿ        $ . < K T [ ` e o r ~ ë ì ò ß ∞ µ ∑ º ≈   Õ Ê Î Ù ı ¯ ˚             "              "”   4^4a 8¢4_4`ÅÚÅÛ¢4b˝ÅÙÅ>Ä@_queryParametersController]filterPattern”   4h4l 8£4i4j4kÅıÅˆÅ˜£4m4n4oÅ¯Å˘ÅˇÄ@_lastEasyToInitiateQueryClass_querySpecification[queryAction_IDEBatchFindTextQuery‘4v4w4x 4y W4{4|YqueryTermZnamedScopeZqueryClassÅ˙Ä Å˝Å˛” 4~44Ä4Å≠TtextXtermTypeÅ¸Å˚“ *Ø*∞4ÑÅcP“ \ ]4Ü4á_IDEBatchFindQueryTerm¢4à `_IDEBatchFindQueryTerm_IDEBatchFindTextQuery“ \ ]4ã4å_IDEBatchFindQuerySpecification¢4ç `_IDEBatchFindQuerySpecificationTfind”   4ê4ï 8§4ë4í4ì4îÅÅÅÅ§ˇ4ó4òˇÅBÅÅÅBÄ@_FilterStateByModeKey_LastNavigatorMode_UnfilterStateByModeKey_FilteredUIStateByModeKey_IDENavigatorModeSolitary”   4°4£ °4¢Å°4§ÅÄ|_IDENavigatorModeSolitary“ 4®44™_codablePlistRepresentationÅ◊Å	Obplist00‘      ef_lastAccessedDateYitemState]selectedItems^scrollPosition3A∆‹,n (™Ø†       ! % ' ) + / 1 3 5 8 : = ? B D F H J K N P S U W Y [ ] _ ` b d g i k m o q t v y { }  Ç É Ö á ä å é ê í î ñ ò ú û † ° £ • ß ® ™ ¨ Æ ∞ ≤ ≥ µ ∑ π ª Ω ø ¡ √ ≈ « … À Õ œ — ” ÷ ÿ ⁄ ‹ ﬁ ‡ „ Â Á È Î Ì Ú Ù ˆ ¯ ˚ ¸ ˇ	!#%')*,.01358:<=?ACDFHJLOQSUWY[]_ac“  	 
 TpathYindexHint•     [TheMealsAppTCoreUUtilsZExtensions“    ^expansionState3A∆€ˇiØ¥“  	  £   [GameCatalog_Preview Content“    3A∆€ˇiÁãi“  	  •     WNetwork“     3A∆€ˇiØÕ “  	 "  §   # $VModuleVDetail“   &  3A∆€ˇiØˇ“  	 ( •     “   *  3A∆€ˇi∞“  	 , .§    -VDomain“   0  3A∆€ˇi∞)“  	 2 .§    -“   4 3A∆‹*Í∂=“  	 6  §   # 7ZNew Folder“   9  3A∆€ˇi∞:“  	 ; .•     <VMapper“   >  3A∆€ˇi∞B“  	 @  •    - AUModel“   C 3A∆€ˇiÊ]Ö“  	 E  •    - A“   G  3A∆€ˇi∞S“  	 I •     <“   G  “  	 L .§   # MTHome“   O  3A∆€ˇi∞d“  	 Q ° R_Package Dependencies“   T  3A∆€ˇrJE“  	 V  ¢  “   X  3A∆€ˇi∞l“  	 Z  ¢  “   \  3A∆€ˇi∞u“  	 ^ •     “   \  “  	 a  ° “   c  3A∆€ˇi∞}“  	 e •   # $ fVRouter“   h  3A∆‹ a£±é“  	 j •   # M f“   l  3A∆€ˇi≤Pú“  	 n •   # M f“   p  3A∆€ˇi∞é“  	 r •   # s fVSearch“   u  3A∆€ˇi∞ñ“  	 w •   # x fXFavorite“   z  3A∆€ˇi∞ü“  	 | •   # $ f“   ~  3A∆€ˇi∞ß“  	 Ä  £   ÅSApp“   ~  “  	 Ñ  £   Å“   Ü 3A∆‹*ÏË™“  	 à •    - âWUseCase“   ã  3A∆€ˇi≤&W“  	 ç •    - â“   è  3A∆€ˇi∞∏“  	 ë .§   # x“   ì 3A∆€ˇiÁ∞“  	 ï •     <“   ó  3A∆€ˇi∞¿“  	 ô •   # ö õTMealTView“   ù  3A∆€ˇi∞…“  	 ü •     õ“   ù  “  	 ¢  £   Å“   §  3A∆€ˇi∞—“  	 ¶ .•   # s õ“   §  “  	 © .•   # M õ“   ´  3A∆€ˇi∞Ÿ“  	 ≠ .•   # $ õ“   Ø  3A∆€ˇi∞‚“  	 ± .•   # x õ“   Ø  “  	 ¥ .•     õ“   ∂  3A∆€ˇi∞Í“  	 ∏ .•   # $ õ“   ∫  3A∆‹ a@ˆÒ“  	 º .•   # M õ“   æ  3A∆€ˇi≤Z¥“  	 ¿  •     õ“   ¬  3A∆€ˇi∞˚“  	 ƒ £   “   ∆  3A∆€ˇi±“  	 » §    “      3A∆€ˇi±“  	 Ã §    “   Œ 3A∆€ˇiÊ◊A“  	 – £   “   “ 3A∆‹*Ï@‰*“  	 ‘ ¢ R ’URealm“   ◊ 3A∆€ˇi±“  	 Ÿ §   # $“   €  3A∆‹ `<‚Î“  	 › §   # M“   ﬂ 3A∆‹ 
«“  	 · ‚•     “   ‰  3A∆€ˇi±%“  	 Ê §   # s“   Ë  3A∆€ˇi±-“  	 Í §   # ö“   Ï  3A∆€ˇi±6“  	 Ó ¶    Ô  ÒTDataVRemoteXResponse“   Û  3A∆‹‰ü¿<“  	 ı ¶    Ô  Ò“   ˜  3A∆€ˇi±>“  	 ˘  •    Ô ˙VLocale“   ˜  “  	 ˝ ¢ R ˛_GameCatalogAbout“    3A∆€ˇr4±ˇ“  	  ¶    Ô  Ò“   3A∆€ˇi±O“  	  •    Ô ˙“    3A∆‹*€r≤Ö“  	
 .£   #“    3A∆€ˇi±W“  	 .£   #“    3A∆€ˇi≤:‚“  	  §   #UAbout“   3A∆‹ 	Õ¯“  	  •   # sYPresenter“    3A∆€ˇi±h“  	  •   # $“    3A∆€ˇ~&`ë“  	   •   # x“  "  3A∆€ˇi±p“  	$  •   # ö“  &  3A∆€ˇi±y“  	(  •   # $“  &  “  	+  •   # M“  -  3A∆€ˇi±Å“  	/  §    Ô“  -  “  	2  §    Ô“  4  3A∆‹*“¬Ì“  	6  ¶    Ô ˙7VEntity“  9 3A∆€ˇi±í“  	; .•     “  9  “  	> •    Ô “  @  3A∆€ˇi±ö“  	B §   # x“  @  “  	E •    Ô “  G  3A∆‹*·ÓT&“  	I §   # ö“  K 3A∆€ˇiÁk¬“  	M §   NRDI“  P 3A∆‹*Îîë!“  	R §   N“  T  3A∆€ˇi±≥“  	V  ° “  X  3A∆€ˇi±Ü˝“  	Z  ¢  “  \  3A∆€ˇi±–˙“  	^ ¶    Ô ˙7“  `  3A∆€ˇi±ƒ“  	b ¶    Ô ˙7“  d  3A∆‹ÂèYh†¢gj¢hi#        #¿$      ¢kl#@zp     #@çÿ        , 6 D S \ü®≠∑¬Œ”Ÿ‰ÊÔ˛	%79BKT_gpy{Ñçîõ§≠∂¡ ”‹ÂÏÓ˜ 	$-6AJS\gnwÄâîö£¨µ¿…“€ÊÔ¯!$;DMV[dmv{Ñçñ°™≥∂ø»—‹„Ïı˛	$/8AJU\enwÇãîù¶±∫√Ã”◊‡È˘'09DMV_hqzÉéó†©¥πæ«–Ÿ‰Ìˆ˝#,5@IR[foxÅåïû©≤ªƒœÿ·Íı˛$-6=FOXajs|Öéó†ß∞π¬«Õ÷ﬂËÒ˙					'	0	;	=	F	O	X	a	j	s	|	Ö	é	ó	†	≠	≤	π	¬	À	‘	›	Í	Û	¸
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
Ñ
è
ò
°
™
±
∫
√
Ã
”
‹
Â
Ó
˜
˝#-6?HS\enyÇãîü®±∫≈Œ◊‚ÎÙ˝!*3<IPYbkvàìú•Æ∑¿…‘›ÊÔ¯
(1:CLU^ajs|Åäìú©≤ªƒ—⁄„‰ÈÓ˜             m              ”   4≠4¥ 8¶4Æ4Ø4∞4±4≤4≥ÅÅÅÅÅÅ¶[4∂•ñ4π 0ÅÖÅÅ6ÄàÅ.ÄsÄ@_IDEStackCompressionValue_IDEDebugTransientStates_DBGNavigatorContentMode_IDEShowOnlyRunningBlocks^IDEVisibleRect_IDEShowOnlyInterestingContent”   4√4À 8ß4ƒ4≈4∆4«4»4…4 ÅÅÅÅÅÅÅß .4Õ4Œ4œñññÄrÅÅÅÄàÄàÄàÄ@_(IDENavigatorModeBeforeViewDebuggingState_#IDECurrentLaunchSessionReferenceKey_IDEDebugSelectedNavigableItems_IDEDebugExpandedItems_1IDEHaveInitiallyExpandedCPUDebuggingChildrenState_2IDEHaveInitiallyExpandedViewDebuggingChildrenState_%IDEDebugAlreadyShownForNewPausedState\7fe9a34c3dd0“  4›*≠†Åd“  4‡)πØ4·4‚4„4‰4Â4Ê4Á4Ë4È4Í4Î4Ï4Ì4Ó4Ô44Ò4ÚÅÅÅÅÅ Å!Å"Å#Å$Å%Å&Å'Å(Å)Å*Å+Å,Å-Å_$E23CE460-B15E-4390-8F2A-14A44EEE78EC_$C336DFA3-A769-4502-891E-6AF332AD83E8[0x10d70c200_$AEB1D2B1-3C08-4FFE-ADCF-8EE2C54AAE78[0x10d410fe0[0x10d70f950_TheMealsApp %% 1_$1B15B918-8A69-44DF-B4DC-AA2889999165_$B27FB6E6-B2C5-40B7-859A-FBC765C10454[0x11000dd40_$9384F16E-0C51-4EBF-9D13-0963BC9D2684[TheMealsApp_$0A9225E6-0FCF-486A-8C89-F69765CD8DDD[0x11000ec70_$8A43148C-6250-4C9A-B852-65B746959956_$B124E38B-7701-44C5-AFEC-9A2876A893A6[0x10f82fc00_$9D43F377-2E6C-4E55-A5D1-CE95B01D0E12_{{0, -10}, {335.5, 955}}#@p@     ”   5	5 8¢5
5Å1Å2¢ø5Ä{Å3Ä@_'userPreferredInspectorGroupExtensionIDs_!userPreferredCategoryExtensionIDs“  5*≠†Åd#@zp     ”   55 8°5Å6°ñÄàÄ@_ShowsOnlyVisibleViewObjects”   55! 8¢55 Å8Å9¢ 0 0ÄsÄsÄ@_ShowsOnlyLeakedBlocks_XRShowsOnlyContentFromWorkspace”   5(54 8´5)5*5+5,5-5.5/50515253Å;Å<Å=Å>Å?Å@ÅAÅBÅCÅDÅE´55 .5758595:5;5<5=5>5?ÅFÄrÅGÅHÅIÅJÅÅ(ÅGÅHÅQÄ@_ SelectedEditorAreaSplitIndexPath_*BeforeComparisonMode_UserVisibleEditorMode_NavigationStyleZEditorMode_ EditorMultipleSplitPrimaryLayout_EditorAreaSplitStates_#primaryEditorArchivedRepresentation_IDEDefaultDebugArea_ DefaultPersistentRepresentations_DebuggerSplitView^MaximizedState” “5M √≠ ÷_NSIndexPathValueÄ>ZOpenInTabs _Layout_LeftToRight“  5S*≠°5TÅKÅd”   5W5^ 8¶5X5Y5Z5[5\5]ÅLÅMÅNÅOÅPÅQ¶5_5`5a5_5c 0ÅRÅSÅˆÅRÅÄsÄ@ZEditorMode_EditorTabBarState_EditorHistoryStacks]EditorMode13+[ItemKindKey_ShouldShowPullRequestComments”   5n5q 8¢5o5pÅTÅU¢5rˆÅVÅdÄ@_TabsAsHistoryItems_SelectedTabIndex“  5x S™5y5z5{5|5}5~55Ä5Å5ÇÅWÅåÅ¥Å›ÅÅ,ÅUÅ}Å¶ÅŒÄ)ÿ5Ö5Ü5á5à 5â5ä5ã5å5ç5é5è5ê5å Q5ì_documentNavigableItemName_!fileDataType.stringRepresentation_stateDictionary_navigableItemRepresentation_navigableItemName[documentURL_documentExtensionIdentifierÅ\ÅoÅqÅXÅãÅ\Ä%Åp÷5ï5ñ5ó 5ò5ô5ö5õ5ú5ù5û ._DocumentLocation^IdentifierPath_WorkspaceRootFilePath_DomainIdentifier_IndexOfDocumentIdentifierÅhÅZÅkÅnÅYÄr_/Xcode.IDENavigableItemDomain.WorkspaceStructure“  5¢ S¶5£5§5•5¶5ß5®Å[Å^Å`ÅbÅdÅfÄ)”5´5¨ 5å .5ØZIdentifierUIndexÅ\ÄrÅ]_DetailPresenter.swift“ \ ]5≤5≥_IDEArchivableStringIndexPair¢5¥ `_IDEArchivableStringIndexPair”5´5¨ 5∂ .5ØÅ_ÄrÅ]YPresenter”5´5¨ 5ªæ5ØÅaÄzÅ]VDetail”5´5¨ 5¿[5ØÅcÅÖÅ]VModule”5´5¨ 5≈ .5ØÅeÄrÅ][GameCatalog”5´5¨ 5  .5ØÅgÄrÅ][GameCatalog”    »5œ5– WÅiÅjÄ _êfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Presenter/DetailPresenter.swift“ \ ]5‘5’_DVTDocumentLocation¢5÷ `_DVTDocumentLocation“ 5ÿ5Ÿ5⁄ZpathStringÅmÅl_e/Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog.xcodeproj“ \ ]5›5ﬁ[DVTFilePath£5ﬂ5‡ `[DVTFilePath_PackedPathEntry“ \ ]5‚5„_(IDENavigableItemArchivableRepresentation¢5‰ `_(IDENavigableItemArchivableRepresentation_public.swift-source_-Xcode.IDEKit.EditorDocument.PegasusSourceCode”   5Ë5Û 8™5È5Í5Î5Ï5Ì5Ó5Ô55Ò5ÚÅrÅsÅtÅuÅvÅwÅxÅyÅzÅ{™5Ùñ 0 05¯ß . 05¸ñÅ|ÄàÄsÄsÅ~Å±ÄrÄsÅâÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  6
 S°6Å}Ä)“  6 S§ﬁ .‡ .Å˘ÄrÅ˙ÄrÄ)”   66 ¢66ÅÅÄ¢66ÅÅÅÉÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   66" ¢6 ºÅÇÄy¢
øÅÄ{Ä|^documentLength”   6(6, £6)6*6+ÅÑÅÖÅÜ£6-6.—ÅáÅàÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“65 66Ÿ\NS.uuidbytesO√Ë‹!˝åIªÑ◊∫3º4ÄÇ“69 6:Ÿ\NS.uuidbytesOÎ/©eîr@¶®/∏:…P∂8ÄÇ”   6=6@ ¢‰6?ÄÜÅä¢ø
Ä{ÅÄ|^documentLength“ \ ]6F6G_IDEEditorHistoryItem¢6H `_IDEEditorHistoryItemÿ5Ö5Ü5á5à 5â5ä5ã6J5ç6L6M5ê6J R5ìÅêÅoÅöÅçÅãÅêÄ'Åp÷5ï5ñ5ó 5ò5ô6S6T5ú5ù5û .ÅòÅéÅkÅnÅYÄr“  6Z S¶6[6\6]6^6_6`ÅèÅëÅìÅîÅïÅñÄ)”5´5¨ 6J .5ØÅêÄrÅ]_DetailRouter.swift”5´5¨ 6hæ5ØÅíÄzÅ]VRouter”5´5¨ 5ªæ5ØÅaÄzÅ]”5´5¨ 5¿[5ØÅcÅÖÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ 6y .5ØÅóÄrÅ][GameCatalog”    »6~5– WÅôÅjÄ _äfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/Router/DetailRouter.swift”   6É6é 8™6Ñ6Ö6Ü6á6à6â6ä6ã6å6çÅõÅúÅùÅûÅüÅ†Å°Å¢Å£Å§™6èñ 0 06ìß . 06óñÅ•ÄàÄsÄsÅßÅ±ÄrÄsÅ≤ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  6• S°6¶Å¶Ä)“  6© S§Ä .˘ .ÅTÄrÅfÄrÄ)”   6∞6≥ ¢6±6≤Å®Å©¢6¥6µÅ™Å∞Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   6∫6æ £6ª6º6ΩÅ´Å¨Å≠£6ø6¿—ÅÆÅØÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“6« 6»Ÿ\NS.uuidbytesO,Qã°3TD¯†∑”µj-gÄÇ“6À 6ÃŸ\NS.uuidbytesOœ4π^ÓTAﬁÜ÷{´P9ÄÇ”   6œ6“ ¢º6—ÄyÅ±¢øÆÄ{ÅSÄ|^documentLength”   6ÿ6€ ¢‰6⁄ÄÜÅ≥¢øÆÄ{ÅSÄ|^documentLengthÿ5Ö5Ü5á5à 5â5ä5ã6·5ç6„6‰5ê6Ê P5ìÅ∏ÅoÅ¬ÅµÅãÅ‹Ä#Åp÷5ï5ñ5ó 5ò5ô6Í6Î5ú5ù5û .Å¿Å∂ÅkÅnÅYÄr“  6Ò S¶6Ú6Û6Ù6ı6ˆ6˜Å∑ÅπÅªÅºÅΩÅæÄ)”5´5¨ 6· .5ØÅ∏ÄrÅ]_DetailView.swift”5´5¨ 6ˇ[5ØÅ∫ÅÖÅ]TView”5´5¨ 5ªæ5ØÅaÄzÅ]”5´5¨ 5¿[5ØÅcÅÖÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ 7 .5ØÅøÄrÅ][GameCatalog”    »75– WÅ¡ÅjÄ _Üfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/DetailView.swift”   77% 8™777777 7!7"7#7$Å√ÅƒÅ≈Å∆Å«Å»Å…Å ÅÀÅÃ™7&ñ 0 07*
,
- 07.ñÅÕÄàÄsÄsÅœÅÅÄsÅ⁄ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  7< S°7=ÅŒÄ)“  7@ S§
BΩ
BΩÅ˜Å§Å˜Å§Ä)”   7G7J ¢7H7IÅ–Å—¢7K7LÅ“Å‘Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   7Q7T ¢º7SÄyÅ”¢ø
lÄ{ÅÄ|^documentLength”   7Z7^ £7[7\7]Å’Å÷Å◊£7_7`—ÅÿÅŸÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“7g 7hŸ\NS.uuidbytesO6)åßﬁ?Auí&
ÌØ1Ó)ÄÇ“7k 7lŸ\NS.uuidbytesOôZ3^=àC∂è\$ÓÕÄÇ”   7o7r ¢‰7qÄÜÅ€¢ø
lÄ{ÅÄ|^documentLength_detailsSection(_:)ÿ5Ö5Ü5á5à 5â5ä5ã7y7z7{7|5ê7y N5ìÅÅËÅÈÅﬁÅãÅÄÅp÷5ï5ñ5ó 5ò5ô7Ç7É5ú5ù5û .ÅÊÅﬂÅkÅnÅYÄr“  7â S£7ä7ã7åÅ‡Å‚Å‰Ä)”5´5¨ 7èæ5ØÅ·ÄzÅ]]Package.swift”5´5¨ 7î5ØÅ„ÅﬁÅ]_SDWebImageSwiftUI”5´5¨ 7ôæ5ØÅÂÄzÅ]_Package Dependencies”    »7û5– WÅÁÅjÄ _ófile:///Users/ben/Library/Developer/Xcode/DerivedData/GameCatalog-eemcjuyaiffcflapnqatxnpoyvyk/SourcePackages/checkouts/SDWebImageSwiftUI/Package.swift_%com.apple.dt.swiftpm-package-manifest”   7§7Ø 8™7•7¶7ß7®7©7™7´7¨7≠7ÆÅÍÅÎÅÏÅÌÅÓÅÔÅÅÒÅÚÅÛ™7∞ñ 0 07¥* . 07∏ñÅÙÄàÄsÄsÅˆÄSÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  7∆ S°7«ÅıÄ)“  7  S§ . . . .ÄrÄrÄrÄrÄ)”   7—7‘ ¢7“7”Å˜Å¯¢7’7÷Å˘Å˚Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   7€7ﬁ ¢º7›ÄyÅ˙¢ø≠Ä{ÅãÄ|^documentLength”   7‰7Ë £7Â7Ê7ÁÅ¸Å˝Å˛£7È7Í—ÅˇÅ ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“7Ò 7ÚŸ\NS.uuidbytesO∏5CoñJ§öG/π‚QÄÇ“7ı 7ˆŸ\NS.uuidbytesO3çÊ;-¿O_∏’¶€1/˛ÄÇ”   7˘7¸ ¢‰7˚ÄÜÅ¢ø≠Ä{ÅãÄ|^documentLength]Package.swiftÿ5Ö5Ü5á5à 5â5ä5ã85ç885ê8 M5ìÅÅoÅÅÅãÅÄÅp÷5ï5ñ5ó 5ò5ô885ú5ù5û .ÅÅÅkÅnÅYÄr“  8 S•88888ÅÅ	ÅÅÅÄ)”5´5¨ 8[5ØÅÅÖÅ]_GameRespository.swift”5´5¨ 8  .5ØÅ
ÄrÅ]TData”5´5¨ 8%æ5ØÅÄzÅ]TCore”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ 8. .5ØÅÄrÅ][GameCatalog”    »835– WÅÅjÄ _Çfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/GameRespository.swift”   888C 8™898:8;8<8=8>8?8@8A8BÅÅÅÅÅÅÅÅÅÅ™8Dñ 0 08H* . 08LñÅÄàÄsÄsÅÄSÄrÄsÅ*ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  8Z S°8[ÅÄ)“  8^ S§ . . . .ÄrÄrÄrÄrÄ)”   8e8h ¢8f8gÅ Å!¢8i8jÅ"Å(Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   8o8s £8p8q8rÅ#Å$Å%£8t8u—Å&Å'ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“8| 8}Ÿ\NS.uuidbytesOÜ0q‘ΩÿJè¨ñF1∂q~%ÄÇ“8Ä 8ÅŸ\NS.uuidbytesO0ÌïåHÅ∑‰Cgü:®ÄÇ”   8Ñ8á ¢º8ÜÄyÅ)¢ø!õÄ{Å	ΩÄ|^documentLength”   8ç8ê ¢‰8èÄÜÅ+¢ø!õÄ{Å	ΩÄ|^documentLengthÿ5Ö5Ü5á5à 5â5ä5ã8ñ5ç8ò8ô5ê8õ L5ìÅ0ÅoÅ:Å-ÅãÅTÄÅp÷5ï5ñ5ó 5ò5ô8ü8†5ú5ù5û .Å8Å.ÅkÅnÅYÄr“  8¶ S¶8ß8®8©8™8´8¨Å/Å1Å3Å4Å5Å6Ä)”5´5¨ 8ñ .5ØÅ0ÄrÅ]_RemoteGameDataSource.swift”5´5¨ 8¥æ5ØÅ2ÄzÅ]VRemote”5´5¨ 8  .5ØÅ
ÄrÅ]”5´5¨ 8%æ5ØÅÄzÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ 8≈ .5ØÅ7ÄrÅ][GameCatalog”    »8 5– WÅ9ÅjÄ _éfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Remote/RemoteGameDataSource.swift”   8œ8⁄ 8™8–8—8“8”8‘8’8÷8◊8ÿ8ŸÅ;Å<Å=Å>Å?Å@ÅAÅBÅCÅD™8€ñ 0 08ﬂ* . 08„ñÅEÄàÄsÄsÅGÄSÄrÄsÅRÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  8Ò S°8ÚÅFÄ)“  8ı S§ooooÅ§Å§Å§Å§Ä)”   8¸8ˇ ¢8˝8˛ÅHÅI¢9 9ÅJÅLÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   99	 ¢9ºÅKÄy¢øÅpÄ{Ä|^documentLength”   99 £999ÅMÅNÅO£99—ÅPÅQÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“9 9Ÿ\NS.uuidbytesOí◊ë%8b@™ü,Ò-D˝∏ÅÄÇ“9  9!Ÿ\NS.uuidbytesO)]ôG√∂€÷?o'\ÄÇ”   9$9' ¢‰9&ÄÜÅS¢øÄ{ÅpÄ|^documentLength\NetworkErrorÿ5Ö5Ü5á5à 5â5ä5ã9.5ç90915ê9. K5ìÅYÅoÅcÅVÅãÅYÄÅp÷5ï5ñ5ó 5ò5ô97985ú5ù5û .ÅaÅWÅkÅnÅYÄr“  9> S¶9?9@9A9B9C9DÅXÅZÅ\Å]Å^Å_Ä)”5´5¨ 9. .5ØÅYÄrÅ]_LocaleGameDataSource.swift”5´5¨ 9L .5ØÅ[ÄrÅ]VLocale”5´5¨ 8  .5ØÅ
ÄrÅ]”5´5¨ 8%æ5ØÅÄzÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ 9] .5ØÅ`ÄrÅ][GameCatalog”    »9b5– WÅbÅjÄ _éfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/LocaleGameDataSource.swift”   9g9r 8™9h9i9j9k9l9m9n9o9p9qÅdÅeÅfÅgÅhÅiÅjÅkÅlÅm™9sñ 0 09w* . 09{ñÅnÄàÄsÄsÅpÄSÄrÄsÅ{ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  9â S°9äÅoÄ)“  9ç S§ . . . .ÄrÄrÄrÄrÄ)”   9î9ó ¢9ï9ñÅqÅr¢9ò9ôÅsÅyÄ|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   9û9¢ £9ü9†9°ÅtÅuÅv£9£9§—ÅwÅxÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“9´ 9¨Ÿ\NS.uuidbytesOÏt9≥+G•ê±£@à`ƒäÄÇ“9Ø 9∞Ÿ\NS.uuidbytesO‡]–Õ{ JZßì&ÔˆÚOÄÇ”   9≥9∂ ¢9¥ºÅzÄy¢ÖøÅíÄ{Ä|^documentLength”   9º9ø ¢‰9æÄÜÅ|¢øÖÄ{ÅíÄ|^documentLengthÿ5Ö5Ü5á5à 5â5ä5ã9≈5ç9«9»5ê9≈ J5ìÅÅÅoÅåÅ~ÅãÅÅÄÅp÷5ï5ñ5ó 5ò5ô9Œ9œ5ú5ù5û .ÅäÅÅkÅnÅYÄr“  9’ Sß9÷9◊9ÿ9Ÿ9⁄9€9‹ÅÄÅÇÅÑÅÖÅÜÅáÅàÄ)”5´5¨ 9≈ .5ØÅÅÄrÅ]_GameEntity.swift”5´5¨ 9‰æ5ØÅÉÄzÅ]VEntity”5´5¨ 9L .5ØÅ[ÄrÅ]”5´5¨ 8  .5ØÅ
ÄrÅ]”5´5¨ 8%æ5ØÅÄzÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ 9˘ .5ØÅâÄrÅ][GameCatalog”    »9˛5– WÅãÅjÄ _ãfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/Data/Locale/Entity/GameEntity.swift”   :: 8™::::::	:
:::ÅçÅéÅèÅêÅëÅíÅìÅîÅïÅñ™:ñ 0 0:* . 0:ñÅóÄàÄsÄsÅôÄSÄrÄsÅ§ÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  :% S°:&ÅòÄ)“  :) S§ . . . .ÄrÄrÄrÄrÄ)”   :0:3 ¢:1:2ÅöÅõ¢:4:5ÅúÅ¢Ä|_ PlaygroundResultsLayoutExtension_DeltaEditorLayoutExtension”   :::> £:;:<:=ÅùÅûÅü£:?:@—Å†Å°ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“:G :HŸ\NS.uuidbytesOı?¸;ÙK:•5ò•øWÅ˚ÄÇ“:K :LŸ\NS.uuidbytesOvµ_…]CÃóì∏‡· ÁÄÇ”   :O:R ¢º:QÄyÅ£¢øyÄ{ÅSÄ|^documentLength”   :X:[ ¢‰:ZÄÜÅ•¢øyÄ{ÅSÄ|^documentLengthÿ5Ö5Ü5á5à 5â5ä5ã:a5ç:c:d5ê:f I5ìÅ™ÅoÅ≥ÅßÅãÅÕÄÅp÷5ï5ñ5ó 5ò5ô:j:k5ú5ù5û .Å±Å®ÅkÅnÅYÄr“  :q S•:r:s:t:u:vÅ©Å´Å≠ÅÆÅØÄ)”5´5¨ :a .5ØÅ™ÄrÅ]_Injection.swift”5´5¨ :~æ5ØÅ¨ÄzÅ]RDI”5´5¨ 8%æ5ØÅÄzÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ :ã .5ØÅ∞ÄrÅ][GameCatalog”    »:ê5– WÅ≤ÅjÄ _zfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Core/DI/Injection.swift”   :ï:† 8™:ñ:ó:ò:ô:ö:õ:ú:ù:û:üÅ¥ÅµÅ∂Å∑Å∏ÅπÅ∫ÅªÅºÅΩ™:°ñ 0 0:•‡q 0:©ñÅæÄàÄsÄsÅ¿ÅsÅÜÄsÅÀÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  :∑ S°:∏ÅøÄ)“  :ª S§8û8Å:ÅqÅ:ÅÓÄ)”   :¬:≈ ¢:√:ƒÅ¡Å¬¢:∆:«Å√Å≈Ä|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   :Ã:œ ¢:ÕºÅƒÄy¢YøÅﬂÄ{Ä|^documentLength”   :’:Ÿ £:÷:◊:ÿÅ∆Å«Å»£:⁄:€—Å…Å ÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“:‚ :„Ÿ\NS.uuidbytesO˛Ë\ı9L’µj3∑¢˙ÄÇ“:Ê :ÁŸ\NS.uuidbytesOKÛhùïÚEÕñùë∏\"ÊÄÇ”   :Í:Ì ¢‰:ÏÄÜÅÃ¢øYÄ{ÅﬂÄ|^documentLength_provideDetail(gameId:)ÿ5Ö5Ü5á5à 5â5ä5ã:Ù5ç:ˆ:˜5ê:ÙP5ìÅ“ÅoÅ€ÅœÅãÅ“Å8Åp÷5ï5ñ5ó 5ò5ô:˝:˛5ú5ù5û .ÅŸÅ–ÅkÅnÅYÄr“  ; S¶;;;;;	;
Å—Å”Å‘Å’Å÷Å◊Ä)”5´5¨ :Ùæ5ØÅ“ÄzÅ]]MealRow.swift”5´5¨ 6ˇ[5ØÅ∫ÅÖÅ]”5´5¨ 5ªæ5ØÅaÄzÅ]”5´5¨ 5¿[5ØÅcÅÖÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ ;" .5ØÅÿÄrÅ][GameCatalog”    »;'5– WÅ⁄ÅjÄ _Éfile:///Users/ben/belajar/ios-dicoding/project-expert/Modularization/GameCatalogExpert/GameCatalog/Module/Detail/View/MealRow.swift”   ;,;7 8™;-;.;/;0;1;2;3;4;5;6Å‹Å›ÅﬁÅﬂÅ‡Å·Å‚Å„Å‰ÅÂ™;8ñ 0 0;<;=8 0;@ñÅÊÄàÄsÄsÅËÅÛÅ:ÄsÅÙÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ;N S°;OÅÁÄ)“  ;R S§ . . . .ÄrÄrÄrÄrÄ)”   ;Y;\ ¢;Z;[ÅÈÅÍ¢;];^ÅÎÅÌÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ;c;f ¢;dºÅÏÄy¢aøÅ3Ä{Ä|^documentLength”   ;l;p £;m;n;oÅÓÅÔÅ£;q;r—ÅÒÅÚÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“;y ;zŸ\NS.uuidbytesO÷π./#Bπ¢3ùdGÓfåÄÇ“;} ;~Ÿ\NS.uuidbytesO˘˛+¶Ö†Mqè|C¸_ù2ÄÇ#@#      ”   ;Ç;Ö ¢‰;ÑÄÜÅı¢øaÄ{Å3Ä|^documentLength“  ;ã*≠°;åÅ˜Åd“;è ;ê;ë_currentEditorHistoryItemÅ¯Åÿ5Ö5Ü5á5à 5â5ä5ã:a5ç;ï;ñ5ê;ò I5ìÅ™ÅoÅÅ˘ÅãÅÄÅp÷5ï5ñ5ó 5ò5ô;ú;ù5ú5ù5û .ÅÅ˙ÅkÅnÅYÄr“  ;£ S•;§;•;¶;ß;®Å˚Å¸Å˝Å˛ÅˇÄ)”5´5¨ :a .5ØÅ™ÄrÅ]”5´5¨ :~æ5ØÅ¨ÄzÅ]”5´5¨ 8%æ5ØÅÄzÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ ;ª .5ØÅ ÄrÅ][GameCatalog”    »:ê5– WÅ≤ÅjÄ ”   ;ƒ;œ 8™;≈;∆;«;»;…; ;À;Ã;Õ;ŒÅÅÅÅÅÅÅ	Å
ÅÅ™;–ñ 0 0;‘ß . 0;ÿñÅÄàÄsÄsÅÅ±ÄrÄsÅÄàÄ@_SourceCodeEditor.selection_SourceCodeEditor.showMinimap_!SourceCodeEditor.showCodeCoverage_SourceCodeEditor.showInvisibles_!SourceCodeEditor.layoutExtensions_#SourceCodeEditor.scrollState.offset_!SourceCodeEditor.scrollState.line_SourceCodeEditor.showAuthors_SourceCodeEditor.codeFolding_SourceCodeEditor.wrapLines“  ;Ê S°;ÁÅÄ)“  ;Í S§DˆDˆÅŸÅdÅŸÅdÄ)”   ;Ò;Ù ¢;Ú;ÛÅÅ¢;ı;ˆÅÅÄ|_DeltaEditorLayoutExtension_ PlaygroundResultsLayoutExtension”   ;˚;˛ ¢;¸ºÅÄy¢YøÅﬂÄ{Ä|^documentLength”   << £<<<ÅÅÅ£<	<
—ÅÅÄÑÄ|_&SourceCodeEditor.playgroundResultStore_/SourceCodeEditor.playgroundToyDisplayController_SourceCodeEditor.sidebar.width“< <Ÿ\NS.uuidbytesO˛Ë\ı9L’µj3∑¢˙ÄÇ“< <Ÿ\NS.uuidbytesOKÛhùïÚEÕñùë∏\"ÊÄÇ”   << ¢‰<ÄÜÅ¢øYÄ{ÅﬂÄ|^documentLengthYInjection“ \ ]<#<$_IDEEditorHistoryStack¢<% `_IDEEditorHistoryStack_ItemKind_Editor÷5ï5ñ5ó 5ò5ô<(<)5ú5ù5û .Å'Å ÅkÅnÅYÄr“  </ S•<0<1<2<3<4Å!Å"Å#Å$Å%Ä)”5´5¨ :a .5ØÅ™ÄrÅ]”5´5¨ :~æ5ØÅ¨ÄzÅ]”5´5¨ 8%æ5ØÅÄzÅ]”5´5¨ 5≈ .5ØÅeÄrÅ]”5´5¨ <G .5ØÅ&ÄrÅ][GameCatalog”    »:ê5– WÅ≤ÅjÄ ”   <P<U 8§<Q<R<S<TÅ)Å*Å+Å,§<V<W[<YÅ-Å2ÅÖÅ;Ä@XLeftViewYRightViewZLayoutMode_IDESplitViewDebugArea”   <`<e 8§<a<b<c<dÅ.Å/Å0Å1§ 0 .øñÄsÄrÄ{ÄàÄ@_VariablesViewShowsRawValues_VariablesViewSelectedScope_ VariablesViewViewSortDescriptors_VariablesViewShowsType”   <p<y 8®<q<r<s<t<u<v<w<xÅ3Å4Å5Å6Å7Å8Å9Å:® 0 0 0ñ 0 0 0 0ÄsÄsÄsÄàÄsÄsÄsÄsÄ@_+IDEStructuredConsoleAreaLibraryEnabledState_-IDEStructuredConsoleAreaTimestampEnabledState_*IDEStructuredConsoleAreaPIDTIDEnabledState_,IDEStructuredConsoleAreaMetadataEnabledState_(IDEStructuredConsoleAreaTypeEnabledState_-IDEStructuredConsoleAreaSubsystemEnabledState_/IDEStructuredConsoleAreaProcessNameEnabledState_,IDEStructuredConsoleAreaCategoryEnabledState”   <å<é 8°<çÅ<°<èÅ=Ä@_DVTSplitViewItems“  <ì*≠¢<î<ïÅ>ÅDÅd”   <ò<ú £<ô<ö<õÅ?Å@ÅA£<ù 0<üÅBÄsÅCÄ|]DVTIdentifier\DVTIsVisible_DVTViewMagnitudeXLeftView#@ã‡     ”   <ß<´ £<ô<ö<õÅ?Å@ÅA£<¨ñ<ÆÅEÄàÅFÄ|YRightView#@|(     ”   <≥<¥ 8††Ä@”   <∑<π 8°<∏ÅI°<∫ÅJÄ@_DVTSplitViewItems“  <æ*≠¢<ø<¿ÅKÅNÅd”   <√<« £<ô<ö<õÅ?Å@ÅA£<»ñ< ÅLÄàÅMÄ|YIDEEditor#@åÿ     ”   <œ<” £<ô<ö<õÅ?Å@ÅA£<‘ñ<÷ÅOÄàÅPÄ|_IDEDebuggerArea#@_¿               "   ,   1   :   ?   Q   V   \   ^  #  #  #  #!  #,  #3  #8  #:  #<  #A  #C  #F  #H  #_  #Ü  #ì  #∞  #≤  #¥  #∂  #∏  #∫  #º  #æ  #¿  #¬  #ƒ  #∆  #»  #   #Ã  #È  #Î  #Ì  #  #Û  #ı  #¯  #˙  #˝  $   $  $  $	  $  $  $  $+  $H  $U  $u  $ú  $Æ  $ƒ  $·  $ı  %  %&  %8  %k  %Ç  %ã  %†  %¢  %§  %¶  %®  %™  %¨  %Æ  %∞  %≤  %¥  %∂  %√  %À  %◊  %Ÿ  %€  %›  &Z  &c  &n  &w  &}  &Ç  &ã  &ò  &ö  &ú  &û  ',  '9  ';  '=  '?  '–  '›  'ﬂ  '·  '„  (t  (Å  (É  (Ö  (á  )  )  )  )  )  )π  )∆  )»  )   )Ã  *_  *l  *n  *p  *r  *˚  +  +
  +  +  +°  +Æ  +∞  +≤  +¥  ,A  ,J  ,R  ,W  ,d  ,s  ,u  ,w  ,y  ,{  ,}  ,  ,Å  ,ê  ,í  ,î  ,ñ  ,ò  ,õ  ,û  ,°  ,£  ,Õ  -  -?  -o  -©  -‘  .  .  .   ."  .$  .)  .+  .-  ./  .<  .>  .@  .B  .w  .Ñ  .Ü  .à  .ä  .ø  .Ã  .œ  .—  .‘  .÷  .ÿ  .Ù  .˝  /   /  /  /  /#  /6  /B  /L  /N  /P  /Q  /S  /U  /b  /v  /à  /ä  /å  /é  /ó  /ü  /¢  /§  /≠  /ª  /¬  /…  /“  /ﬁ  /„  /Ô  /¯  0  0  01  0G  0P  0f  0m  0z  0á  0ä  0å  0è  0ë  0ì  0Ø  0∏  0ª  0Ω  0ø  0‘  0÷  0ÿ  0Ÿ  0€  0›  0Í  0Ï  0Ó  0˜  0˙  0¸  1	  1  1  1  1  1  1  1  1*  1,  1.  10  1V  1c  1e  1g  1i  1è  1ú  1°  1£  1•  1™  1¨  1Æ  1∞  1µ  1æ  1«  1–  1Ÿ  1€  1›  1ﬂ  1·  1„  1Ï  1ı  1˜  1˘  1˚  1˝  1ˇ  2  2  2  2#  2,  2.  20  22  24  26  2?  2H  2Q  2S  2U  2W  2Y  2[  2d  2m  2o  2q  2s  2u  2w  2Ñ  2â  2ã  2ç  2í  2î  2ñ  2ò  2°  2™  2≥  2µ  2∑  2π  2ª  2Ω  2∆  2œ  2—  2”  2’  2◊  2Ÿ  2‚  2Î  2Ù  2ˆ  2¯  2˙  2¸  2˛  3  3  3  3  3  3  3!  3#  3,  35  37  39  3;  3=  3?  3L  3O  3Q  3T  3V  3X  3e  3g  3i  3k  :Ω  :   :ﬂ  :·  :„  :Â  :Á  :È  :Î  :Ì  :Ô  :Ò  :Û  ;  ;
  ;  ;  ;  ;  ;  ;  ;  ;  ;  ;  ;;  ;Z  ;~  ;†  ;ƒ  ;Í  <  <-  <L  <i  <r  <u  <w  <y  <Ç  <ã  <ç  <è  <ë  <ì  <ï  <ó  <ò  <•  <™  <¨  <Æ  <≥  <µ  <∑  <π  <÷  <˘  =  =  =  =  =  =  =  =  =)  =5  =7  =@  =A  =C  =L  =Q  =^  =e  =g  =i  =k  =r  =t  =v  =x  =z  =£  =’  =ˆ  =ˇ  >  >  >!  >*  >1  >6  >?  >L  >_  >a  >j  >w  >|  >~  >Ä  >Ö  >á  >â  >ã  >ë  >†  >°  >Æ  ?u  ?w  ?y  ?{  ?}  ?  ?Å  ?É  ?Ö  ?á  ?â  ?ã  ?ç  ?è  ?ë  ?ì  ?ï  ?ó  ?ô  ?õ  ?ù  ?ü  ?°  ?£  ?•  ?ß  ?©  ?´  ?≠  ?Ø  ?±  ?≥  ?µ  ?∑  ?π  ?ª  ?Ω  ?ø  ?¡  ?√  ?≈  ?«  ?…  ?À  ?Õ  ?œ  ?—  ?”  ?’  ?◊  ?Ÿ  ?€  ?›  ?ﬂ  ?·  ?„  ?Â  ?Á  ?È  ?Î  ?Ì  ?Ô  ?Ò  ?Û  ?ı  ?˜  ?˘  ?˚  ?˛  @  @  @  @
  @  @  @  @  @  @  @  @   @#  @&  @)  @,  @/  @2  @5  @8  @;  @>  @A  @D  @G  @J  @M  @P  @S  @V  A  A   A#  A&  A)  A,  A/  A2  A5  A8  A;  A>  AA  AD  AG  AJ  AM  AP  AS  AV  AY  A\  A_  Ab  Ae  Ah  Ak  An  Aq  At  Aw  Az  A}  AÄ  AÉ  AÜ  Aâ  Aå  Aè  Aí  Aï  Aò  Aõ  Aû  A°  A§  Aß  A™  A≠  A∞  A≥  A∂  Aπ  Aº  Aø  A¬  A≈  A»  AÀ  AŒ  A—  A‘  A◊  A⁄  A›  A‡  A„  AÊ  AÈ  AÏ  AÔ  AÚ  Aı  A¯  A˚  A˛  B  B  B  B
  B  B  B  B  B  B  B  B"  B%  B(  B+  B.  B1  B4  B7  B:  B=  B@  BC  BE  BR  BT  BV  BX  BË  Bı  B˜  B˘  B˚  Cp  C}  C  CÅ  CÉ  C¸  D	  D  D  D  Du  DÇ  DÑ  DÜ  Dà  E  E)  E+  E-  E/  E¢  EØ  E±  E≥  Eµ  F0  F=  F?  FA  FC  Fπ  F∆  F»  F   FÃ  G@  GM  GO  GQ  GS  G”  G‡  G‚  G‰  GÊ  H[  Hh  Hj  Hl  Hn  HÈ  Hˆ  H¯  H˙  H¸  Ij  Iw  Iy  I{  I}  IÚ  Iˇ  J  J  J  J{  Jà  Jä  Jå  Jé  J˛  K  K  K  K  KÜ  Kì  Kï  Kó  Kô  L  L!  L#  L%  L'  Lò  L•  Lß  L©  L´  MD  MQ  MS  MU  MW  M—  Mﬁ  M‡  M‚  M‰  NZ  Ng  Ni  Nk  Nm  NË  Nı  N˜  N˘  N˚  O^  Ok  Om  Oo  Oq  O  O˝  Oˇ  P  P  Pn  P{  P}  P  PÅ  PÓ  P˚  P˝  Pˇ  Q  Qt  QÅ  QÉ  QÖ  Qá  R  R  R  R  R  Rö  Rß  R©  R´  R≠  S+  S8  S:  S<  S>  S£  S∞  S≤  S¥  S∂  T1  T>  T@  TB  TD  Tπ  T∆  T»  T   TÃ  U1  U>  U@  UB  UD  U™  U∑  Uπ  Uª  UΩ  V   V-  V/  V1  V3  V©  V∂  V∏  V∫  Vº  W*  W7  W9  W;  W=  W¥  W¡  W√  W≈  W«  X7  XD  XF  XH  XJ  Xª  X»  X   XÃ  XŒ  YC  YP  YR  YT  YV  Y¬  Yœ  Y—  Y”  Y’  ZH  ZU  ZW  ZY  Z[  Z—  Zﬁ  Z‡  Z‚  Z‰  [X  [e  [g  [i  [k  [—  [ﬁ  [‡  [‚  [‰  \Q  \^  \`  \b  \d  \«  \‘  \÷  \ÿ  \⁄  ]B  ]O  ]Q  ]S  ]U  ]—  ]ﬁ  ]‡  ]‚  ]‰  ^T  ^a  ^c  ^e  ^g  ^÷  ^„  ^Â  ^Á  ^È  _h  _u  _w  _y  _{  _ı  `  `  `  `  `Å  `é  `ê  `í  `î  a	  a  a  a  a  añ  a£  a•  aß  a™  b$  b1  b3  b5  b8  bÆ  bª  bΩ  bø  b¬  cK  cX  cZ  c\  c_  c”  c‡  c‚  c‰  cÁ  d^  dk  dm  do  dr  dÂ  dÚ  dÙ  dˆ  d˘  en  e{  e}  e  eÇ  eˆ  f  f  f  f
  f{  fà  fä  få  fè  g  g  g  g  g  gî  g°  g£  g•  g®  h   h-  h/  h1  h4  hÕ  h⁄  h‹  hﬁ  h·  iV  ic  ie  ig  ij  i◊  i‰  iÊ  iË  iÎ  je  jr  jt  jv  jy  jÔ  j¸  j˛  k   k  kë  kû  k†  k¢  k•  l  l%  l'  l)  l,  lö  lß  l©  l´  lÆ  m3  m@  mB  mD  mG  m∑  mƒ  m∆  m»  mÀ  n7  nD  nF  nH  nK  n∆  n”  n’  n◊  n⁄  oJ  oW  oY  o[  o^  oÕ  o⁄  o‹  oﬁ  o·  p]  pj  pl  pn  pq  pÏ  p˘  p˚  p˝  q   qÜ  qì  q®  q´  qÆ  q±  q¥  q∑  q∫  qΩ  q¿  q√  q∆  q€  qﬁ  q‡  q‚  q‰  qÁ  qÍ  qÌ  qÔ  qÚ  qÙ  qˆ  r  r2  rV  rx  rú  r¬  rÊ  s  s$  sA  sJ  sM  sP  sR  s[  sd  sf  sh  sj  sl  sn  s{  sÄ  sÉ  sÜ  sã  sé  së  sì  s∞  s”  s‡  sÂ  sË  sÍ  sÔ  sÚ  sÙ  sˆ  t  t  t  t  t  t"  t%  t,  t/  t2  t4  t6  t_  të  t≤  tª  t»  t€  t›  tÊ  tÛ  u  u  u  u  u   u%  u'  u*  u/  u1  u4  u6  uE  uR  ug  uj  um  up  us  uv  uy  u|  u  uÇ  uÖ  uö  uù  uü  u°  u£  u¶  u©  u¨  uÆ  u±  u≥  uµ  u“  uÒ  v  v7  v[  vÅ  v•  vƒ  v„  w   w	  w  w  w  w  w#  w&  w)  w,  w/  w1  w3  w5  w7  wD  wI  wL  wO  wT  wW  wZ  w\  w  wú  w©  w∞  w≥  w∂  wπ  w¿  w√  w∆  w»  w   wÛ  x%  xF  xO  x\  xo  xq  xz  xá  xö  xú  x©  xÆ  x∞  x≥  x∏  x∫  xΩ  xø  xŒ  x—  x⁄  x‹  xÈ  xÓ  x  xÛ  x¯  x˙  x˝  xˇ  y  y  y0  y3  y6  y9  y<  y?  yB  yE  yH  yK  yN  yc  yf  yh  yj  yl  yo  yr  yu  yw  yz  y|  y~  yõ  y∫  yﬁ  z   z$  zJ  zn  zç  z¨  z…  z“  z’  zÿ  z⁄  z„  zÏ  zÔ  zÚ  zı  z˜  z˘  z˚  z˝  {
  {  {  {  {  {  {   {"  {E  {b  {o  {v  {y  {|  {  {Ü  {â  {å  {é  {ê  {π  {Î  |  |  |"  |5  |7  |@  |M  |`  |b  |o  |t  |w  |y  |~  |Å  |É  |Ö  |î  |ó  |†  |¢  |Ø  |¥  |∂  |π  |æ  |¿  |√  |≈  |‘  |·  |ˆ  |˘  |¸  |ˇ  }  }  }  }  }  }  }  })  },  }.  }0  }2  }5  }8  }:  }<  }?  }A  }C  }`  }  }£  }≈  }È  ~  ~3  ~R  ~q  ~é  ~ó  ~ö  ~ù  ~ü  ~®  ~±  ~¥  ~∂  ~π  ~ª  ~Ω  ~ø  ~Ã  ~—  ~‘  ~◊  ~‹  ~ﬂ  ~‚  ~‰    $  1  6  9  ;  @  C  E  G  V  Y  f  m  p  s  v  }  Ä  É  Ö  á  ∞  ‚  Ä  Ä  Ä  Ä,  Ä.  Ä7  ÄD  ÄW  ÄY  Äb  Äo  Ät  Äv  Äy  Ä~  ÄÄ  ÄÉ  ÄÖ  Äî  Ä°  Ä∂  Äπ  Äº  Äø  Ä¬  Ä≈  Ä»  ÄÀ  ÄŒ  Ä—  Ä‘  ÄÈ  ÄÏ  ÄÓ  Ä  ÄÚ  Äı  Ä˜  Ä˘  Ä˚  Ä˛  Å   Å  Å  Å>  Åb  ÅÑ  Å®  ÅŒ  ÅÚ  Ç  Ç0  ÇM  ÇV  ÇY  Ç\  Ç^  Çg  Çp  Çs  Çv  Çy  Ç|  Ç~  ÇÄ  ÇÇ  Çè  Çî  Çó  Çö  Çü  Ç¢  Ç•  Çß  Çƒ  ÇÁ  ÇÙ  Ç˘  Ç˚  Ç˛  É  É  É  É
  É  É  É)  É0  É3  É6  É9  É@  ÉC  ÉF  ÉH  ÉJ  És  É•  É∆  Éœ  É‹  ÉÔ  ÉÒ  É˙  Ñ  Ñ  Ñ  Ñ)  Ñ.  Ñ0  Ñ3  Ñ8  Ñ:  Ñ=  Ñ?  ÑN  Ñ[  Ñp  Ñs  Ñv  Ñy  Ñ|  Ñ  ÑÇ  ÑÖ  Ñà  Ñã  Ñé  Ñ£  Ñ¶  Ñ®  Ñ™  Ñ¨  ÑØ  Ñ≤  Ñ¥  Ñ∂  Ñπ  Ñª  ÑΩ  Ñ⁄  Ñ˘  Ö  Ö?  Öc  Öâ  Ö≠  ÖÃ  ÖÎ  Ü  Ü  Ü  Ü  Ü  Ü"  Ü+  Ü.  Ü1  Ü4  Ü7  Ü9  Ü;  ÜH  ÜM  ÜP  ÜS  ÜX  Ü[  Ü^  Ü`  Ü}  Ü†  Ü≠  Ü≤  Ü¥  Ü∑  Üº  Üæ  Ü¡  Ü√  Ü“  Üﬂ  ÜÊ  ÜÈ  ÜÏ  ÜÔ  Üˆ  Ü˘  Ü¸  Ü˛  á   á)  á[  á|  áÖ  áí  á•  áß  á∞  áΩ  á–  á“  áﬂ  á‰  áÊ  áÈ  áÓ  á  áÛ  áı  à  à  à&  à)  à,  à/  à2  à5  à8  à;  à>  àA  àD  àY  à\  à^  à`  àb  àe  àh  àj  àl  ào  àq  às  àê  àØ  à”  àı  â  â?  âc  âÇ  â°  âæ  â«  â   âÕ  âœ  âÿ  â·  â‰  âÊ  âÈ  âÎ  âÌ  âÔ  âÒ  â˛  ä  ä  ä	  ä  ä  ä  ä  ä9  äV  äc  äj  äm  äp  äs  äz  ä}  äÄ  äÇ  äÑ  ä≠  äﬂ  ã   ã	  ã  ã)  ã+  ã4  ãA  ãT  ãV  ãc  ãh  ãj  ãm  ãr  ãu  ãx  ãz  ãâ  ãí  ãì  ãï  ãò  ã•  ã™  ã¨  ãØ  ã¥  ã∑  ã∫  ãº  ãÀ  ãÿ  ãÌ  ã  ãÛ  ãˆ  ã˘  ã¸  ãˇ  å  å  å  å  å   å#  å%  å'  å)  å,  å/  å2  å4  å7  å9  å;  åX  åw  åõ  åΩ  å·  ç  ç+  çJ  çi  çÜ  çè  çí  çï  çó  ç†  ç©  ç¨  çØ  ç≤  çµ  ç∑  çπ  çª  ç»  çÕ  ç–  ç”  çÿ  ç€  çﬁ  ç‡  ç˝  é   é-  é2  é4  é7  é<  é>  éA  éC  éR  é_  éf  éi  él  éo  év  éy  é|  é~  éÄ  é©  é€  é¸  è  è  è%  è'  è0  è=  èP  èR  è[  è]  èj  èo  èq  èt  èy  è{  è~  èÄ  èè  èú  è±  è¥  è∑  è∫  èΩ  è¿  è√  è∆  è…  èÃ  èœ  è‰  èÁ  èÈ  èÎ  èÌ  è  èÛ  èı  è˜  è˙  è¸  è˛  ê  ê:  ê^  êÄ  ê§  ê   êÓ  ë  ë,  ëI  ëR  ëU  ëX  ëZ  ëc  ël  ëo  ër  ëu  ëx  ëz  ë|  ë~  ëã  ëê  ëì  ëñ  ëõ  ëû  ë°  ë£  ë¿  ë„  ë  ëı  ë˜  ë˙  ëˇ  í  í  í  í  í  í%  í,  í/  í2  í5  í<  í?  íB  íD  íF  ío  í°  í¬  íÀ  íÿ  íÎ  íÌ  íˆ  ì  ì  ì  ì%  ì*  ì,  ì/  ì4  ì6  ì9  ì;  ìJ  ìW  ìl  ìo  ìr  ìu  ìx  ì{  ì~  ìÅ  ìÑ  ìá  ìä  ìü  ì¢  ì§  ì¶  ì®  ì´  ìÆ  ì±  ì≥  ì∂  ì∏  ì∫  ì◊  ìˆ  î  î<  î`  îÜ  î™  î…  îË  ï  ï  ï  ï  ï  ï  ï(  ï+  ï.  ï1  ï3  ï5  ï7  ïD  ïI  ïL  ïO  ïT  ïW  ïZ  ï\  ïy  ïú  ï©  ïÆ  ï±  ï≥  ï∏  ïª  ïΩ  ïø  ïŒ  ï—  ïﬁ  ïÂ  ïË  ïÎ  ïÓ  ïı  ï¯  ï˚  ï˝  ïˇ  ñ(  ñZ  ñ{  ñÑ  ñë  ñ§  ñ¶  ñØ  ñº  ñœ  ñ—  ñ⁄  ñ‹  ñÈ  ñÓ  ñ  ñÛ  ñ¯  ñ˙  ñ˝  ñˇ  ó  ó  ó0  ó3  ó6  ó9  ó<  ó?  óB  óE  óH  óK  óN  óc  óf  óh  ój  ól  óo  óq  ós  óu  óx  óz  ó|  óô  ó∏  ó‹  ó˛  ò"  òH  òl  òã  ò™  ò«  ò–  ò”  ò÷  òÿ  ò·  òÍ  òÏ  òÓ  ò  òÚ  òÙ  ô  ô  ô	  ô  ô  ô  ô  ô  ô6  ôY  ôf  ôk  ôn  ôp  ôu  ôx  ôz  ô|  ôã  ôé  ôõ  ô¢  ô•  ô®  ô´  ô≤  ôµ  ô∏  ô∫  ôº  ôÂ  ö  ö8  öA  öN  öa  öc  öl  öy  öå  öé  öõ  ö†  ö¢  ö•  ö™  ö¨  öØ  ö±  ö¿  öÕ  ö‚  öÂ  öË  öÎ  öÓ  öÒ  öÙ  ö˜  ö˙  ö˝  õ   õ  õ  õ  õ  õ  õ!  õ$  õ&  õ(  õ+  õ-  õ/  õL  õk  õè  õ±  õ’  õ˚  ú  ú>  ú]  úz  úÉ  úÜ  úâ  úã  úî  úù  ú†  ú£  ú¶  ú®  ú™  ú¨  úπ  úæ  ú¡  úƒ  ú…  úÃ  úœ  ú—  úÙ  ù  ù  ù%  ù(  ù+  ù.  ù5  ù8  ù;  ù=  ù?  ùh  ùö  ùª  ùƒ  ù—  ù‰  ùÊ  ùÔ  ù¸  û  û  û  û#  û&  û(  û-  û0  û2  û4  ûC  ûE  ûR  ûW  ûY  û\  ûa  ûc  ûf  ûh  ûw  ûÑ  ûô  ûú  ûü  û¢  û•  û®  û´  ûÆ  û±  û¥  û∑  ûÃ  ûœ  û—  û”  û’  ûÿ  û⁄  û‹  ûﬁ  û·  û„  ûÂ  ü  ü!  üE  üg  üã  ü±  ü’  üÙ  †  †0  †9  †<  †?  †A  †J  †S  †U  †W  †Y  †[  †]  †j  †o  †r  †u  †z  †}  †Ä  †Ç  †•  †¬  †œ  †÷  †Ÿ  †‹  †ﬂ  †Ê  †È  †Ï  †Ó  †  °  °K  °l  °u  °Ç  °ï  °ó  °†  °≠  °¿  °¬  °œ  °‘  °◊  °Ÿ  °ﬁ  °·  °„  °Â  °Ù  °˜  ¢  ¢	  ¢  ¢  ¢  ¢  ¢  ¢  ¢)  ¢6  ¢K  ¢N  ¢Q  ¢T  ¢W  ¢Z  ¢]  ¢`  ¢c  ¢f  ¢i  ¢~  ¢Å  ¢É  ¢Ö  ¢á  ¢ä  ¢å  ¢é  ¢ê  ¢ì  ¢ï  ¢ó  ¢¥  ¢”  ¢˜  £  £=  £c  £á  £¶  £≈  £‚  £Î  £Ó  £Ò  £Û  £¸  §  §  §	  §  §  §  §  §!  §$  §'  §,  §/  §2  §4  §Q  §t  §Å  §Ü  §à  §ã  §ê  §í  §ï  §ó  §¶  §©  §∂  §Ω  §¿  §√  §∆  §Õ  §–  §”  §’  §◊  •   •2  •S  •\  •i  •|  •~  •á  •î  •ß  •©  •∂  •ª  •Ω  •¿  •≈  •«  •   •Ã  •€  •Ë  •˝  ¶   ¶  ¶  ¶	  ¶  ¶  ¶  ¶  ¶  ¶  ¶0  ¶3  ¶5  ¶7  ¶9  ¶<  ¶>  ¶@  ¶B  ¶E  ¶G  ¶I  ¶f  ¶Ö  ¶©  ¶À  ¶Ô  ß  ß9  ßX  ßw  ßî  ßù  ß†  ß£  ß•  ßÆ  ß∑  ßπ  ßª  ßΩ  ßø  ß¡  ßŒ  ß”  ß÷  ßŸ  ßﬁ  ß·  ß‰  ßÊ  ®	  ®&  ®3  ®:  ®=  ®@  ®C  ®J  ®M  ®P  ®R  ®T  ®}  ®Ø  ®–  ®Ÿ  ®Ê  ®˘  ®˚  ©  ©  ©$  ©&  ©3  ©8  ©:  ©=  ©B  ©D  ©G  ©I  ©X  ©e  ©j  ©l  ©o  ©t  ©v  ©y  ©{  ©ä  ©ó  ©¨  ©Ø  ©≤  ©µ  ©∏  ©ª  ©æ  ©¡  ©ƒ  ©«  ©   ©ﬂ  ©‚  ©‰  ©Ê  ©Ë  ©Î  ©Ó  ©Ò  ©Û  ©ˆ  ©¯  ©˙  ™  ™6  ™Z  ™|  ™†  ™∆  ™Í  ´	  ´(  ´E  ´N  ´Q  ´T  ´V  ´_  ´h  ´k  ´n  ´q  ´t  ´v  ´x  ´Ö  ´ä  ´ç  ´ê  ´ï  ´ò  ´õ  ´ù  ´¿  ´›  ´Í  ´Ò  ´Ù  ´˜  ´˙  ¨  ¨  ¨  ¨	  ¨  ¨4  ¨U  ¨á  ¨ê  ¨ù  ¨∞  ¨≤  ¨ª  ¨»  ¨€  ¨›  ¨Í  ¨Ô  ¨Ú  ¨Ù  ¨˘  ¨¸  ¨ˇ  ≠  ≠  ≠  ≠  ≠  ≠+  ≠0  ≠2  ≠5  ≠:  ≠=  ≠@  ≠B  ≠Q  ≠^  ≠s  ≠v  ≠y  ≠|  ≠  ≠Ç  ≠Ö  ≠à  ≠ã  ≠é  ≠ë  ≠¶  ≠©  ≠´  ≠≠  ≠Ø  ≠≤  ≠¥  ≠∂  ≠∏  ≠ª  ≠Ω  ≠ø  ≠‹  ≠˚  Æ  ÆA  Æe  Æã  ÆØ  ÆŒ  ÆÌ  Ø
  Ø  Ø  Ø  Ø  Ø$  Ø-  Ø/  Ø1  Ø3  Ø5  Ø7  ØD  ØI  ØL  ØO  ØT  ØW  ØZ  Ø\  Ø  Øú  Ø©  Ø∞  Ø≥  Ø∂  Øπ  Ø¿  Ø√  Ø≈  Ø»  Ø   ØÛ  ∞  ∞F  ∞O  ∞\  ∞o  ∞q  ∞z  ∞á  ∞ö  ∞ú  ∞©  ∞Æ  ∞∞  ∞≥  ∞∏  ∞ª  ∞æ  ∞¿  ∞œ  ∞“  ∞ﬂ  ∞‰  ∞Ê  ∞È  ∞Ó  ∞Ò  ∞Ù  ∞ˆ  ±  ±  ±'  ±*  ±-  ±0  ±3  ±6  ±9  ±<  ±?  ±B  ±E  ±Z  ±]  ±_  ±a  ±c  ±f  ±i  ±l  ±n  ±q  ±s  ±u  ±í  ±±  ±’  ±˜  ≤  ≤A  ≤e  ≤Ñ  ≤£  ≤¿  ≤…  ≤Ã  ≤œ  ≤—  ≤⁄  ≤„  ≤Ê  ≤È  ≤Ï  ≤Ó  ≤  ≤Ú  ≤Ù  ≥  ≥  ≥	  ≥  ≥  ≥  ≥  ≥  ≥<  ≥Y  ≥f  ≥m  ≥p  ≥s  ≥v  ≥}  ≥Ä  ≥É  ≥Ö  ≥á  ≥∞  ≥‚  ¥  ¥  ¥  ¥,  ¥.  ¥7  ¥D  ¥W  ¥Y  ¥f  ¥k  ¥m  ¥p  ¥u  ¥w  ¥z  ¥|  ¥ã  ¥é  ¥ê  ¥ù  ¥¢  ¥§  ¥ß  ¥¨  ¥Æ  ¥±  ¥≥  ¥¬  ¥œ  ¥‰  ¥Á  ¥Í  ¥Ì  ¥  ¥Û  ¥ˆ  ¥˘  ¥¸  ¥ˇ  µ  µ  µ  µ  µ  µ   µ#  µ%  µ'  µ)  µ,  µ.  µ0  µM  µl  µê  µ≤  µ÷  µ¸  ∂   ∂?  ∂^  ∂{  ∂Ñ  ∂á  ∂ä  ∂å  ∂ï  ∂û  ∂†  ∂¢  ∂§  ∂¶  ∂®  ∂µ  ∂∫  ∂Ω  ∂¿  ∂≈  ∂»  ∂À  ∂Õ  ∂Í  ∑  ∑  ∑  ∑"  ∑$  ∑)  ∑,  ∑/  ∑1  ∑@  ∑C  ∑P  ∑W  ∑Z  ∑]  ∑`  ∑g  ∑j  ∑m  ∑o  ∑q  ∑ö  ∑Ã  ∑Ì  ∑ˆ  ∏  ∏  ∏  ∏!  ∏.  ∏A  ∏C  ∏P  ∏U  ∏W  ∏Z  ∏_  ∏b  ∏e  ∏g  ∏v  ∏É  ∏ò  ∏õ  ∏û  ∏°  ∏§  ∏ß  ∏™  ∏≠  ∏∞  ∏≥  ∏∂  ∏À  ∏Œ  ∏–  ∏“  ∏‘  ∏◊  ∏⁄  ∏›  ∏ﬂ  ∏‚  ∏‰  ∏Ê  π  π"  πF  πh  πå  π≤  π÷  πı  ∫  ∫1  ∫:  ∫=  ∫@  ∫B  ∫K  ∫T  ∫W  ∫Z  ∫]  ∫`  ∫b  ∫d  ∫f  ∫s  ∫x  ∫{  ∫~  ∫É  ∫Ü  ∫â  ∫ã  ∫®  ∫À  ∫ÿ  ∫›  ∫ﬂ  ∫‚  ∫Á  ∫È  ∫Ï  ∫Ó  ∫˝  ª   ª  ª  ª  ª  ª  ª$  ª'  ª*  ª,  ª.  ªW  ªâ  ª™  ª≥  ª¿  ª”  ª’  ªﬁ  ªÎ  ª˛  º   º  º  º  º  º  º  º   º#  º%  º4  ºA  ºV  ºY  º\  º_  ºb  ºe  ºh  ºk  ºn  ºq  ºt  ºâ  ºå  ºé  ºê  ºí  ºï  ºò  ºö  ºú  ºü  º°  º£  º¿  ºﬂ  Ω  Ω%  ΩI  Ωo  Ωì  Ω≤  Ω—  ΩÓ  Ω˜  Ω˙  Ω˝  Ωˇ  æ  æ  æ  æ  æ  æ  æ  æ*  æ/  æ2  æ5  æ:  æ=  æ@  æB  æe  æÇ  æè  æñ  æô  æú  æü  æ¶  æ©  æ¨  æÆ  æ∞  æŸ  ø  ø,  ø5  øB  øU  øW  ø`  øm  øÄ  øÇ  øè  øî  øñ  øô  øû  ø†  ø£  ø•  ø¥  ø∑  øƒ  ø…  øÀ  øŒ  ø”  ø’  øÿ  ø⁄  øÈ  øˆ  ¿  ¿  ¿  ¿  ¿  ¿  ¿  ¿   ¿#  ¿&  ¿)  ¿>  ¿A  ¿C  ¿E  ¿G  ¿J  ¿M  ¿P  ¿R  ¿U  ¿W  ¿Y  ¿v  ¿ï  ¿π  ¿€  ¿ˇ  ¡%  ¡I  ¡h  ¡á  ¡§  ¡≠  ¡∞  ¡≥  ¡µ  ¡æ  ¡«  ¡…  ¡À  ¡Õ  ¡œ  ¡—  ¡ﬁ  ¡„  ¡Ê  ¡È  ¡Ó  ¡Ò  ¡Ù  ¡ˆ  ¬  ¬6  ¬C  ¬J  ¬M  ¬P  ¬S  ¬Z  ¬]  ¬`  ¬b  ¬d  ¬ç  ¬ø  ¬‡  ¬È  ¬ˆ  √	  √  √  √!  √4  √6  √C  √H  √J  √M  √R  √T  √W  √Y  √h  √q  √s  √Ä  √Ö  √á  √ä  √è  √ë  √î  √ñ  √•  √≤  √«  √   √Õ  √–  √”  √÷  √Ÿ  √‹  √ﬂ  √‚  √Â  √˙  √˝  √ˇ  ƒ  ƒ  ƒ  ƒ	  ƒ  ƒ  ƒ  ƒ  ƒ  ƒ1  ƒP  ƒt  ƒñ  ƒ∫  ƒ‡  ≈  ≈#  ≈B  ≈_  ≈h  ≈k  ≈n  ≈p  ≈y  ≈Ç  ≈Ö  ≈à  ≈ã  ≈é  ≈ê  ≈ù  ≈¢  ≈•  ≈®  ≈≠  ≈∞  ≈≥  ≈µ  ≈“  ≈ı  ∆  ∆  ∆	  ∆  ∆  ∆  ∆  ∆  ∆'  ∆*  ∆7  ∆>  ∆A  ∆D  ∆G  ∆N  ∆Q  ∆T  ∆V  ∆X  ∆Å  ∆≥  ∆‘  ∆›  ∆Í  ∆˝  ∆ˇ  «  «  «(  «*  «7  «<  «>  «A  «F  «H  «K  «M  «\  «i  «~  «Å  «Ñ  «á  «ä  «ç  «ê  «ì  «ñ  «ô  «ú  «±  «¥  «∂  «∏  «∫  «Ω  «¿  «¬  «ƒ  ««  «…  «À  «Ë  »  »+  »M  »q  »ó  »ª  »⁄  »˘  …  …  …"  …%  …'  …0  …9  …<  …?  …B  …E  …G  …I  …V  …[  …^  …a  …f  …i  …l  …n  …ã  …Æ  …ª  …¿  …√  …≈  …   …Õ  …–  …“  …·  …‰  …Ò  …¯  …˚  …˛         
            3   e   é   ó   §   ∑   π   ¬   œ   ‚   ‰   Ò   ˆ   ¯   ˚  À   À  À  À  À  À$  À9  À<  À?  ÀB  ÀE  ÀH  ÀK  ÀN  ÀQ  ÀT  ÀW  Àl  Ào  Àq  Às  Àu  Àx  Àz  À|  À~  ÀÅ  ÀÉ  ÀÖ  À¢  À¡  ÀÂ  Ã  Ã+  ÃQ  Ãu  Ãî  Ã≥  Ã–  ÃŸ  Ã‹  Ãﬂ  Ã·  ÃÍ  ÃÛ  Ãı  Ã˜  Ã˘  Ã˚  Ã˝  Õ
  Õ  Õ  Õ  Õ  Õ  Õ   Õ"  Õ?  Õb  Õo  Õt  Õw  Õy  Õ~  ÕÅ  ÕÉ  ÕÖ  Õî  Õó  Õ§  Õ´  ÕÆ  Õ±  Õ¥  Õª  Õæ  Õ¡  Õ√  Õ≈  ÕÓ  Œ   ŒA  ŒJ  ŒW  Œj  Œl  Œu  ŒÇ  Œï  Œó  Œ§  Œ©  Œ´  ŒÆ  Œ≥  Œµ  Œ∏  Œ∫  Œ…  Œ÷  ŒÎ  ŒÓ  ŒÒ  ŒÙ  Œ˜  Œ˙  Œ˝  œ   œ  œ  œ	  œ  œ!  œ#  œ%  œ'  œ*  œ,  œ.  œ0  œ3  œ5  œ7  œT  œs  œó  œπ  œ›  –  –'  –F  –e  –Ç  –ã  –é  –ë  –ì  –ú  –•  –®  –´  –Æ  –±  –≥  –¿  –≈  –»  –À  ––  –”  –÷  –ÿ  –ı  —  —%  —*  —,  —/  —4  —6  —9  —;  —J  —M  —Z  —a  —d  —g  —j  —q  —t  —w  —y  —{  —§  —÷  —˜  “   “  “   “"  “+  “8  “K  “M  “Z  “_  “a  “d  “i  “k  “n  “p  “  “å  “°  “§  “ß  “™  “≠  “∞  “≥  “∂  “π  “º  “ø  “‘  “◊  “Ÿ  “€  “›  “‡  “„  “Â  “Á  “Í  “Ï  “Ó  ”  ”*  ”N  ”p  ”î  ”∫  ”ﬁ  ”˝  ‘  ‘9  ‘B  ‘E  ‘H  ‘J  ‘S  ‘\  ‘_  ‘b  ‘e  ‘h  ‘j  ‘l  ‘y  ‘~  ‘Å  ‘Ñ  ‘â  ‘å  ‘è  ‘ë  ‘Æ  ‘—  ‘ﬁ  ‘„  ‘Â  ‘Ë  ‘Ì  ‘Ô  ‘Ú  ‘Ù  ’  ’  ’  ’  ’  ’   ’#  ’*  ’-  ’0  ’2  ’4  ’]  ’è  ’∞  ’π  ’∆  ’Ÿ  ’€  ’‰  ’Ò  ÷  ÷  ÷  ÷  ÷  ÷  ÷"  ÷$  ÷'  ÷)  ÷8  ÷E  ÷Z  ÷]  ÷`  ÷c  ÷f  ÷i  ÷l  ÷o  ÷r  ÷u  ÷x  ÷ç  ÷ê  ÷í  ÷î  ÷ñ  ÷ô  ÷ú  ÷û  ÷†  ÷£  ÷•  ÷ß  ÷ƒ  ÷„  ◊  ◊)  ◊M  ◊s  ◊ó  ◊∂  ◊’  ◊Ú  ◊˚  ◊˛  ÿ  ÿ  ÿ  ÿ  ÿ  ÿ  ÿ  ÿ!  ÿ#  ÿ%  ÿ'  ÿ4  ÿ9  ÿ<  ÿ?  ÿD  ÿG  ÿJ  ÿL  ÿo  ÿå  ÿô  ÿ†  ÿ£  ÿ¶  ÿ©  ÿ∞  ÿ≥  ÿ∂  ÿ∏  ÿ∫  ÿ„  Ÿ  Ÿ6  Ÿ?  ŸL  Ÿ_  Ÿa  Ÿj  Ÿw  Ÿä  Ÿå  Ÿô  Ÿû  Ÿ°  Ÿ£  Ÿ®  Ÿ´  Ÿ≠  ŸØ  Ÿæ  Ÿ¡  ŸŒ  Ÿ”  Ÿ’  Ÿÿ  Ÿ›  Ÿﬂ  Ÿ‚  Ÿ‰  ŸÛ  ⁄   ⁄  ⁄  ⁄  ⁄  ⁄!  ⁄$  ⁄'  ⁄*  ⁄-  ⁄0  ⁄3  ⁄H  ⁄K  ⁄M  ⁄O  ⁄Q  ⁄T  ⁄W  ⁄Y  ⁄[  ⁄^  ⁄`  ⁄b  ⁄  ⁄û  ⁄¬  ⁄‰  €  €.  €R  €q  €ê  €≠  €∂  €π  €º  €æ  €«  €–  €“  €‘  €÷  €ÿ  €⁄  €Á  €Ï  €Ô  €Ú  €˜  €˙  €˝  €ˇ  ‹"  ‹?  ‹L  ‹S  ‹V  ‹Y  ‹\  ‹c  ‹f  ‹i  ‹k  ‹m  ‹ñ  ‹»  ‹È  ‹Ú  ‹ˇ  ›  ›  ›  ›*  ›=  ›?  ›L  ›Q  ›S  ›V  ›[  ›]  ›`  ›b  ›q  ›s  ›Ä  ›Ö  ›á  ›ä  ›è  ›ë  ›î  ›ñ  ›•  ›≤  ›«  ›   ›Õ  ›–  ›”  ›÷  ›Ÿ  ›‹  ›ﬂ  ›‚  ›Â  ›˙  ›˝  ›ˇ  ﬁ  ﬁ  ﬁ  ﬁ	  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ  ﬁ1  ﬁP  ﬁt  ﬁñ  ﬁ∫  ﬁ‡  ﬂ  ﬂ#  ﬂB  ﬂ_  ﬂh  ﬂk  ﬂn  ﬂp  ﬂy  ﬂÇ  ﬂÖ  ﬂá  ﬂä  ﬂå  ﬂé  ﬂê  ﬂù  ﬂ¢  ﬂ•  ﬂ®  ﬂ≠  ﬂ∞  ﬂ≥  ﬂµ  ﬂÿ  ﬂı  ‡  ‡	  ‡  ‡  ‡  ‡  ‡  ‡  ‡!  ‡#  ‡L  ‡~  ‡ü  ‡®  ‡µ  ‡»  ‡   ‡”  ‡‡  ‡Û  ‡ı  ·  ·  ·	  ·  ·  ·  ·  ·  ·'  ·*  ·7  ·<  ·>  ·A  ·F  ·H  ·K  ·M  ·\  ·i  ·~  ·Å  ·Ñ  ·á  ·ä  ·ç  ·ê  ·ì  ·ñ  ·ô  ·ú  ·±  ·¥  ·∂  ·∏  ·∫  ·Ω  ·ø  ·¡  ·√  ·∆  ·»  ·   ·Á  ‚  ‚*  ‚L  ‚p  ‚ñ  ‚∫  ‚Ÿ  ‚¯  „  „  „!  „$  „&  „/  „8  „:  „<  „>  „@  „B  „O  „T  „W  „Z  „_  „b  „e  „g  „Ñ  „ß  „¥  „π  „ª  „æ  „√  „≈  „»  „   „Ÿ  „‹  „È  „  „Û  „ˆ  „˘  ‰   ‰  ‰  ‰  ‰
  ‰3  ‰e  ‰Ü  ‰è  ‰ú  ‰Ø  ‰±  ‰∫  ‰«  ‰⁄  ‰‹  ‰È  ‰Ó  ‰  ‰Û  ‰¯  ‰˙  ‰˝  ‰ˇ  Â  Â  Â0  Â3  Â6  Â9  Â<  Â?  ÂB  ÂE  ÂH  ÂK  ÂN  Âc  Âf  Âh  Âj  Âl  Âo  Âr  Ât  Âv  Ây  Â{  Â}  Âö  Âπ  Â›  Âˇ  Ê#  ÊI  Êm  Êå  Ê´  Ê»  Ê—  Ê‘  Ê◊  ÊŸ  Ê‚  ÊÎ  ÊÓ  ÊÒ  ÊÙ  Ê˜  Ê˘  Ê˚  Á  Á  Á  Á  Á  Á  Á  Á   Á=  Á`  Ám  Ár  Át  Áw  Á|  Á~  ÁÅ  ÁÉ  Áí  Áï  Á¢  Á©  Á¨  ÁØ  Á≤  Áπ  Áº  Áø  Á¡  Á√  ÁÏ  Ë  Ë?  ËH  ËU  Ëh  Ëj  Ës  ËÄ  Ëì  Ëï  Ë¢  Ëß  Ë©  Ë¨  Ë±  Ë≥  Ë∂  Ë∏  Ë«  Ë‘  ËÈ  ËÏ  ËÔ  ËÚ  Ëı  Ë¯  Ë˚  Ë˛  È  È  È  È  È  È!  È#  È%  È(  È+  È-  È/  È2  È4  È6  ÈS  Èr  Èñ  È∏  È‹  Í  Í&  ÍE  Íd  ÍÅ  Íä  Íç  Íê  Íí  Íõ  Í§  Í¶  Í®  Í™  Í¨  ÍÆ  Íª  Í¿  Í√  Í∆  ÍÀ  ÍŒ  Í—  Í”  Í  Î  Î   Î%  Î'  Î*  Î/  Î1  Î4  Î6  ÎE  ÎH  ÎU  Î\  Î_  Îb  Îe  Îl  Îo  Îr  Ît  Îv  Îü  Î—  ÎÚ  Î˚  Ï  Ï  Ï  Ï&  Ï3  ÏF  ÏH  ÏU  ÏZ  Ï\  Ï_  Ïd  Ïf  Ïi  Ïk  Ïz  Ïá  Ïú  Ïü  Ï¢  Ï•  Ï®  Ï´  ÏÆ  Ï±  Ï¥  Ï∑  Ï∫  Ïœ  Ï“  Ï‘  Ï÷  Ïÿ  Ï€  Ïﬁ  Ï‡  Ï‚  ÏÂ  ÏÁ  ÏÈ  Ì  Ì%  ÌI  Ìk  Ìè  Ìµ  ÌŸ  Ì¯  Ó  Ó4  Ó=  Ó@  ÓC  ÓE  ÓN  ÓW  ÓZ  Ó]  Ó`  Óc  Óe  Óg  Ói  Ók  Óx  Ó}  ÓÄ  ÓÉ  Óà  Óã  Óé  Óê  Ó≠  Ó–  Ó›  Ó‚  ÓÂ  ÓÁ  ÓÏ  ÓÔ  ÓÒ  ÓÛ  Ô  Ô  Ô  Ô  Ô  Ô  Ô"  Ô)  Ô,  Ô/  Ô1  Ô3  Ô\  Ôé  ÔØ  Ô∏  Ô≈  Ôÿ  Ô⁄  Ô„  Ô              !  #  &  (  7  D  Y  \  _  b  e  h  k  n  q  t  w  å  è  ë  ì  ï  ò  õ  ù  ü  ¢  §  ¶  √  ‚  Ò  Ò(  ÒL  Òr  Òñ  Òµ  Ò‘  ÒÒ  Ò˙  Ò˝  Ú   Ú  Ú  Ú  Ú  Ú  Ú  Ú  Ú  Ú+  Ú0  Ú3  Ú6  Ú;  Ú>  ÚA  ÚC  Úf  ÚÉ  Úê  Úó  Úö  Úù  Ú†  Úß  Ú™  Ú≠  ÚØ  Ú±  Ú⁄  Û  Û-  Û6  ÛC  ÛV  ÛX  Ûa  Ûn  ÛÅ  ÛÉ  Ûê  Ûï  Ûó  Ûö  Ûü  Û°  Û§  Û¶  Ûµ  Û¬  Û«  Û…  ÛÃ  Û—  Û”  Û÷  Ûÿ  ÛÁ  ÛÙ  Ù  Ù  Ù  Ù  Ù  Ù  Ù  Ù   Ù#  Ù&  Ù)  Ù,  ÙC  ÙF  ÙH  ÙJ  ÙL  ÙO  ÙR  ÙU  ÙW  ÙY  Ù\  Ù^  Ù`  Ù}  Ùú  Ù¿  Ù‚  ı  ı,  ıW  ı{  ıö  ıπ  ı÷  ıﬂ  ı‚  ıÂ  ıÁ  ı  ı˘  ı¸  ıˇ  ˆ  ˆ  ˆ  ˆ  ˆ  ˆ  ˆ  ˆ$  ˆ'  ˆ*  ˆ,  ˆI  ˆl  ˆy  ˆ~  ˆÄ  ˆÉ  ˆà  ˆã  ˆé  ˆê  ˆü  ˆ¢  ˆØ  ˆ∂  ˆπ  ˆº  ˆø  ˆ∆  ˆ»  ˆÀ  ˆŒ  ˆ–  ˆÒ  ˜  ˜L  ˜U  ˜b  ˜u  ˜w  ˜Ä  ˜ç  ˜†  ˜¢  ˜Ø  ˜º  ˜ø  ˜¬  ˜≈  ˜»  ˜À  ˜Œ  ˜€  ˜›  ˜ﬂ  ˜‚  ˜Â  ˜Ë  ˜Î  ˜Ì  ¯  ¯!  ¯>  ¯\  ¯~  ¯ö  ¯ß  ¯¨  ¯Ø  ¯≤  ¯∑  ¯∫  ¯Ω  ¯ø  ¯∆  ¯”  ¯Ÿ  ¯ﬁ  ¯ﬂ  ¯Ï  ¯Ô  ¯Ú  ¯ı  ¯¯  ¯˙  ˘  ˘  ˘  ˘  ˘   ˘"  ˘/  ˘4  ˘6  ˘9  ˘>  ˘A  ˘D  ˘F  ˘U  ˘b  ˘w  ˘z  ˘}  ˘Ä  ˘É  ˘Ü  ˘â  ˘å  ˘è  ˘í  ˘ï  ˘™  ˘≠  ˘Ø  ˘±  ˘≥  ˘∂  ˘π  ˘º  ˘æ  ˘¡  ˘√  ˘≈  ˘‚  ˙  ˙%  ˙G  ˙k  ˙ë  ˙µ  ˙‘  ˙Û  ˚  ˚  ˚  ˚  ˚!  ˚*  ˚3  ˚6  ˚9  ˚<  ˚?  ˚A  ˚C  ˚P  ˚U  ˚X  ˚[  ˚`  ˚c  ˚f  ˚h  ˚ã  ˚®  ˚µ  ˚º  ˚ø  ˚¬  ˚≈  ˚Ã  ˚œ  ˚“  ˚‘  ˚÷  ˚ˇ  ¸1  ¸R  ¸[  ¸h  ¸{  ¸}  ¸Ü  ¸ì  ¸¶  ¸®  ¸µ  ¸∫  ¸º  ¸ø  ¸ƒ  ¸∆  ¸…  ¸À  ¸⁄  ¸„  ¸Â  ¸Ú  ¸˜  ¸˘  ¸¸  ˝  ˝  ˝  ˝  ˝  ˝$  ˝9  ˝<  ˝?  ˝B  ˝E  ˝H  ˝K  ˝N  ˝Q  ˝T  ˝W  ˝l  ˝o  ˝q  ˝s  ˝u  ˝x  ˝{  ˝}  ˝  ˝Ç  ˝Ñ  ˝Ü  ˝£  ˝¬  ˝Ê  ˛  ˛,  ˛R  ˛v  ˛ï  ˛¥  ˛—  ˛⁄  ˛›  ˛‡  ˛‚  ˛Î  ˛Ù  ˛˜  ˛˘  ˛¸  ˛˛  ˇ   ˇ  ˇ  ˇ  ˇ  ˇ  ˇ   ˇ#  ˇ%  ˇH  ˇe  ˇr  ˇy  ˇ|  ˇ  ˇÇ  ˇâ  ˇå  ˇè  ˇë  ˇì  ˇº  ˇÓ      %  8  :  C  P  c  e  r  w  z  |  Å  Ñ  Ü  à  ó  §  ©  ´  Æ  ≥  µ  ∏  ∫  …  ÷  Î  Ó  Ò  Ù  ˜  ˙  ˝     	  ! # % ' * , . 0 3 5 7 T s ó π ›  ' F e Ç ã é ë ì ú • ß © ´ ≠ Ø º ¡ ƒ « Ã œ “ ‘ Ò  ! & ) + 0 3 5 7 F I V ] ` c f m p s u w † “ Û ¸ 	   ' 4 G I V [ ] ` e g j l { à ù † £ ¶ © ¨ Ø ≤ µ ∏ ª – ” ’ ◊ Ÿ ‹ ﬂ ‚ ‰ Á È Î  ' K m ë ∑ € ˙  6 ? B E G P Y \ _ b e g i k x } Ä É à ã é ê ≥ – › ‰ Á Í Ì Ù ˜ ˙ ¸ ˛ ' Y z É ê £ • Æ ª Œ – › ‚ ‰ Á Ï Ó Ò Û      " ' ) , . = J _ b e h k n q t w z } í ï ó ô õ û † ¢ § ß © ´ » Á 	 	- 	Q 	w 	õ 	∫ 	Ÿ 	ˆ 	ˇ 
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
å 
ô 
† 
£ 
¶ 
© 
∞ 
≥ 
∂ 
∏ 
∫ 
„  6 ? L _ a j w ä å ô û † £ ® ™ ≠ Ø æ À – “ ’ ⁄ ‹ ﬂ ·  ˝      ! $ ' * - 0 E H J L N Q S U W Z \ ^ { ö æ ‡  * N m å © ≤ µ ∏ ∫ √ Ã Œ – “ ‘ ÷ „ Ë Î Ó Û ˆ ˘ ˚  ; H M O R W Z ] _ n { Ç Ö à ã í î ó ö ú Ω Ê  ! . A C L Y l n { Ä Ç Ö ä ç ê í ° Æ √ ∆ … Ã œ “ ’ ÿ € ﬁ · ˆ ˘ ˚ ˝ ˇ    
    . M q ì ∑ ›    ? \ e h k m v  Ç Ö à ã ç è ú ° § ß ¨ Ø ≤ ¥ ◊ Ù           " K } û ß ¥ « … “ ﬂ Ú Ù   	      & ) 6 ; = @ E G J L [ h } Ä É Ü â å è í ï ò õ ∞ ≥ µ ∑ π º ø ¬ ƒ « … À Ë  + M q ó ª ⁄ ˘   " % ' 0 9 < ? B E G I V [ ^ a f i l n ë Æ ª ¬ ≈ » À “ ’ ÿ ⁄ ‹  7 X a n Å É å ô ¨ Æ ª ¿ √ ≈   Õ œ — ‡ È Î ¯ ˝ ˇ   	    * ? B E H K N Q T W Z ] r u w y { ~ Å Ñ Ü â ã ç ™ … Ì  3 Y } ú ª ÿ · ‰ Á È Ú ˚ ˛          % ( + - J m z  Ç Ñ â å é ê ü ¢ Ø ∂ π º ø ∆ … Ã Œ – ˘ + L U b u w Ä ç † ¢ § ± ∂ ∏ ª ¿ ¬ ≈ « ÷ „ ¯ ˚ ˛    
     + . 0 2 4 7 : = ? B D F c Ç ¶ » Ï  6 U t ë ö ù † ¢ ´ ¥ ∑ ∫ Ω ¿ ¬ ƒ ∆ ” ÿ € ﬁ „ Ê È Î  + 8 = ? B G I L N ] ` m t w z } Ñ á ä å é ∑ È 
    3 5 > K ^ ` i k x }  Ç á â å é ù ™ ø ¬ ≈ » À Œ — ‘ ◊ ⁄ › Ú ı ˜ ˘ ˚ ˛     
  ) H l é ≤ ÿ ¸    :  W  `  c  f  h  q  z  }  Ä  É  Ü  à  ä  å  ô  û  °  §  ©  ¨  Ø  ±  Œ  Ò  ˛ ! ! ! ! ! ! ! !# !& !3 !: != !@ !C !J !M !P !R !T !} !Ø !– !Ÿ !Ê !˘ !˚ " " "$ "& "3 "8 ": "= "B "D "G "I "X "e "z "} "Ä "É "Ü "â "å "è "í "ï "ò "≠ "∞ "≤ "¥ "∂ "π "º "æ "¿ "√ "≈ "« "‰ # #' #I #m #ì #∑ #÷ #ı $ $ $ $! $# $, $5 $8 $; $> $A $C $E $R $W $Z $] $b $e $h $j $ç $™ $∑ $æ $¡ $ƒ $« $Œ $— $‘ $÷ $ÿ % %3 %T %] %j %} % %à %ï %® %™ %∑ %º %æ %¡ %∆ %» %À %Õ %‹ %ﬂ %Ï %Ò %Û %ˆ %˚ %˝ &  & & & &3 &6 &9 &< &? &B &E &H &K &N &Q &f &i &k &m &o &r &u &w &y &| &~ &Ä &ù &º &‡ ' '& 'L 'p 'è 'Æ 'À '‘ '◊ '⁄ '‹ 'Â 'Ó 'Ò 'Ù '˜ '˙ '¸ '˛ (  ( ( ( ( ( (  (# (% (B (e (r (w (y (| (Å (É (Ü (à (ó (§ (´ (Æ (± (¥ (ª (æ (¡ (√ (≈ (Ó )  )A )J )W )j )l )u )Ç )ï )ó )§ )© )´ )Æ )≥ )µ )∏ )∫ )… )÷ )Î )Ó )Ò )Ù )˜ )˙ )˝ *  * * *	 * *! *# *% *' ** *- */ *1 *4 *6 *8 *U *t *ò *∫ *ﬁ + +( +G +f +É +å +è +í +î +ù +¶ +© +¨ +Ø +± +≥ +µ +¬ +« +  +Õ +“ +’ +ÿ +⁄ +˜ , ,' ,, ,/ ,1 ,6 ,9 ,; ,= ,L ,O ,\ ,c ,f ,i ,l ,s ,v ,y ,{ ,} ,¶ ,ÿ ,˘ - - -" -$ -- -: -M -O -\ -a -c -f -k -m -p -r -Å -é -£ -¶ -© -¨ -Ø -≤ -µ -∏ -ª -æ -¡ -÷ -Ÿ -€ -› -ﬂ -‚ -Â -Á -È -Ï -Ó - . ., .P .r .ñ .º .‡ .ˇ / /; /D /G /J /L /U /^ /` /b /d /f /h /u /z /} /Ä /Ö /à /ã /ç /™ /Õ /⁄ /ﬂ /· /‰ /È /Î /Ó / /ˇ 0 0 0 0 0 0 0% 0( 0+ 0- 0/ 0X 0ä 0´ 0¥ 0¡ 0‘ 0÷ 0ﬂ 0Ï 0ˇ 1 1 1 1 1 1 1 1" 1$ 13 1@ 1U 1X 1[ 1^ 1a 1d 1g 1j 1m 1p 1s 1à 1ã 1ç 1è 1ë 1î 1ñ 1ò 1ö 1ù 1ü 1° 1æ 1› 2 2# 2G 2m 2ë 2∞ 2œ 2Ï 2ı 2¯ 2˚ 2˝ 3 3 3 3 3 3 3 3& 3+ 3. 31 36 39 3< 3> 3[ 3~ 3ã 3ê 3ì 3ï 3ö 3ù 3ü 3° 3∞ 3≥ 3¿ 3« 3  3Õ 3– 3◊ 3⁄ 3› 3ﬂ 3· 4
 4< 4] 4f 4s 4Ü 4à 4ë 4û 4± 4≥ 4¿ 4≈ 4« 4  4œ 4— 4‘ 4÷ 4Â 4Ú 5 5
 5 5 5 5 5 5 5 5" 5% 5: 5= 5? 5A 5C 5F 5I 5K 5M 5P 5R 5T 5q 5ê 5¥ 5÷ 5˙ 6  6D 6c 6Ç 6ü 6® 6´ 6Æ 6∞ 6π 6¬ 6ƒ 6∆ 6» 6  6Ã 6Ÿ 6ﬁ 6· 6‰ 6È 6Ï 6Ô 6Ò 7 71 7> 7E 7H 7K 7N 7U 7X 7[ 7] 7_ 7à 7∫ 7€ 7‰ 7Ò 8 8 8 8 8/ 81 8> 8C 8F 8H 8M 8O 8Q 8S 8b 8o 8t 8v 8y 8~ 8Ä 8Ç 8Ñ 8ì 8† 8∑ 8∫ 8Ω 8¿ 8√ 8∆ 8… 8Ã 8œ 8“ 8’ 8ÿ 8Ô 8Ú 8Ù 8ˆ 8¯ 8˚ 8˛ 9 9 9 9	 9 9 9* 9I 9m 9è 9≥ 9Ÿ : :( :G :f :É :å :è :í :î :ù :¶ :© :¨ :Ø :≤ :¥ :∂ :√ :» :À :Œ :” :÷ :Ÿ :€ :¯ ; ;( ;- ;/ ;2 ;7 ;9 ;< ;> ;M ;P ;] ;d ;g ;j ;m ;t ;w ;z ;| ;~ ;ß ;Ÿ ;˙ < < <# <% <. <; <N <P <] <j <m <p <s <v <y <| <â <ã <ç <ê <ì <ñ <ô <õ <∞ <œ <Ï =
 =& =H =U =Z =] =` =e =h =k =m =z =á =à =â =ã =ò =õ =û =° =§ =¶ =≥ =¿ =≈ =« =  =œ =— =‘ =÷ =Â =Ú > >
 > > > > > > > >" >% >: >= >? >A >C >F >I >L >N >Q >S >U >r >ë >µ >◊ >˚ ?! ?E ?d ?É ?† ?© ?¨ ?Ø ?± ?∫ ?√ ?∆ ?… ?Ã ?Œ ?– ?“ ?ﬂ ?‰ ?Á ?Í ?Ô ?Ú ?ı ?˜ @ @7 @D @K @N @Q @T @[ @^ @a @c @e @é @¿ @· @Í @˜ A
 A A A" A5 A7 AD AI AL AN AS AV AX AZ Ai Al An A{ AÄ AÇ AÖ Aä Aå Aè Aë A† A≠ A¬ A≈ A» AÀ AŒ A— A‘ A◊ A⁄ A› A‡ Aı A¯ A˙ A¸ A˛ B B B B	 B B B B- BL Bp Bí B∂ B‹ C  C C> C[ Cd Cg Cj Cl Cu C~ CÅ CÑ Cá Cä Cå Cé Cê Cù C¢ C• C® C≠ C∞ C≥ Cµ Cÿ Cı D D	 D D D D D D D! D# DL D~ Dü D® Dµ D» D  D” D‡ DÛ Dı E E E	 E E E E E E' E* E, E9 E> E@ EC EH EJ EM EO E^ Ek EÄ EÉ EÜ Eâ Eå Eè Eí Eï Eò Eõ Eû E≥ E∂ E∏ E∫ Eº Eø E¬ E≈ E« E  EÃ EŒ EÎ F
 F. FP Ft Fö Fæ F› F¸ G G" G% G( G* G3 G< G? GA GD GF GH GJ GW G\ G_ Gb Gg Gj Gm Go Gå GØ Gº G¡ G√ G∆ GÀ GÕ G– G“ G· G‰ GÒ G¯ G˚ G˛ H H H H H H H; Hm Hé Hó H§ H∑ Hπ H¬ Hœ H‚ H‰ HÊ HÛ H¯ H˙ H˝ I I I I	 I I% I: I= I@ IC IF II IL IO IR IU IX Im Ip Ir It Iv Iy I| I~ IÄ IÉ IÖ Iá I§ I√ IÁ J	 J- JS Jw Jñ Jµ J“ J€ Jﬁ J· J„ JÏ Jı J¯ J˚ J˛ K K K K K K K  K# K& K( KE Kh Ku Kz K} K KÑ Ká Kâ Kã Kö Kù K™ K± K¥ K∑ K∫ K¡ Kƒ K« K… KÀ KÙ L& LG LP L] Lp Lr L{ Là Lõ Lù L™ LØ L± L¥ Lπ Lª Læ L¿ Lœ L‹ LÒ LÙ L˜ L˙ L˝ M  M M M	 M M M$ M' M) M+ M- M0 M2 M4 M6 M9 M; M= MZ My Mù Mø M„ N	 N- NL Nk Nà Në Nî Nó Nô N¢ N´ N≠ NØ N± N≥ Nµ N¬ N« N  NÕ N“ N’ Nÿ N⁄ N˜ O O' O, O. O1 O6 O9 O< O> OM OZ Oa Od Og Oj Oq Ot Ow Oy O{ O§ O÷ O˜ P  P P  P" P+ P8 PK PM PZ P_ Pa Pd Pi Pl Po Pq PÄ Pç P¢ P• P® P´ PÆ P± P¥ P∑ P∫ PΩ P¿ P’ Pÿ P⁄ P‹ Pﬁ P· P‰ PÁ PÈ PÏ PÓ P Q Q, QP Qr Qñ Qº Q‡ Qˇ R R; RD RG RJ RL RU R^ Ra Rd Rg Ri Rk Rm Rz R RÇ RÖ Rä Rç Rê Rí RØ R“ Rﬂ R‰ RÊ RÈ RÓ R RÛ Rı S S S S S S! S$ S+ S. S1 S3 S5 S^ Sê S± S∫ S« S⁄ S‹ SÂ SÚ T T T	 T T T T  T% T' T* T, T; TH T] T` Tc Tf Ti Tl To Tr Tu Tx T{ Tê Tì Tï Tó Tô Tú Tü T° T£ T¶ T® T™ T« TÊ U
 U, UP Uv Uö Uπ Uÿ Uı U˛ V V V V V V V V! V$ V& V( V5 V: V= V@ VE VH VK VM Vp Vç Vö V° V§ Vß V™ V± V¥ V∑ Vπ Vª V‰ W W7 W@ WM W` Wb Wk Wx Wã Wç Wö Wü W¢ W§ W© W¨ WÆ W∞ Wø W¬ Wœ W‘ W÷ WŸ Wﬁ W‡ W„ WÂ WÙ X X X X X X" X% X( X+ X. X1 X4 XI XL XN XP XR XU XW XY X[ X^ X` Xb X Xû X¬ X‰ Y Y. YR Yq Yê Y≠ Y∂ Yπ Yº Yæ Y« Y– Y“ Y‘ Y÷ Yÿ Y⁄ YÁ YÏ YÔ YÚ Y˜ Y˙ Y˝ Yˇ Z" Z? ZL ZS ZV ZY Z\ Zc Zf Zi Zk Zm Zñ Z» ZÈ ZÚ Zˇ [ [ [ [* [= [? [L [Q [S [V [[ [] [` [b [q [~ [É [Ö [à [ç [è [í [î [£ [∞ [≈ [» [À [Œ [— [‘ [◊ [⁄ [› [‡ [„ [¯ [˚ [˝ [ˇ \ \ \ \ \
 \ \ \ \. \M \q \ì \∑ \› ] ]  ]? ]\ ]e ]h ]k ]m ]v ] ]Å ]É ]Ö ]á ]â ]ñ ]õ ]û ]° ]¶ ]© ]¨ ]Æ ]— ]Ó ]˚ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^E ^w ^ò ^° ^Æ ^¡ ^√ ^Ã ^Ÿ ^Ï ^Ó ^˚ _  _ _ _
 _ _ _ _  _# _0 _5 _7 _: _? _A _D _F _U _b _w _z _} _Ä _É _Ü _â _å _è _í _ï _™ _≠ _Ø _± _≥ _∂ _π _ª _Ω _¿ _¬ _ƒ _· `  `$ `F `j `ê `¥ `” `Ú a a a a a  a) a2 a5 a7 a: a< a> aK aP aS aV a[ a^ aa ac aÄ a£ a∞ aµ a∑ a∫ aø a¬ a≈ a« a÷ aŸ aÊ aÌ a aÛ aˆ a˝ b  b b b b0 bb bÉ bå bô b¨ bÆ b∑ bƒ b◊ bŸ bÊ bÎ bÌ b bı b¯ b˚ b˝ c c c. c1 c4 c7 c: c= c@ cC cF cI cL ca cd cf ch cj cm cp cs cu cx cz c| cô c∏ c‹ c˛ d" dH dl dã d™ d« d– d” d÷ dÿ d· dÍ dÌ dÔ dÚ dı d˜ d˘ d˚ e e e e e e e e  e= e` em er eu ew e| e eÅ eÉ eí eï e¢ e© e¨ eØ e≤ eπ eº eø e¡ e√ eÏ f f? fH fU fh fj fs fÄ fì fï fû f° fÆ f≥ fµ f∏ fΩ fø f¬ fƒ f” f‡ fı f¯ f˚ f˛ g g g g
 g g g g( g+ g- g/ g1 g4 g7 g: g< g? gA gC g` g g£ g≈ gÈ h h3 hR hq hé hó hö hù hü h® h± h¥ h∑ h∫ hΩ hø hÃ h— h‘ h◊ h‹ hﬂ h‚ h‰ i i$ i1 i6 i8 i; i@ iB iE iG iV iY if im ip is iv i} iÄ iÉ iÖ iá i∞ i‚ j j j j, j. j7 jD jW jY jb jo jt jv jy j~ jÄ jÉ jÖ jî j° j∂ jπ jº jø j¬ j≈ j» jÀ jŒ j— j‘ jÈ jÏ jÓ j jÚ jı j˜ j˘ j˚ j˛ k  k k k> kb kÑ k® kŒ kÚ l l0 lM lV lY l\ l^ lg lp ls lv ly l| l~ lÄ lç lí lï lò lù l† l£ l• l» lÂ lÚ l˘ l¸ lˇ m m	 m m m m m< mn mè mò m• m∏ m∫ m√ m– m„ mÂ mÚ m˜ m˙ m¸ n n n n n n n' n, n. n1 n6 n8 n; n= nL nY nn nq nt nw nz n} nÄ nÉ nÜ nâ nå n° n§ n¶ n® n™ n≠ n∞ n≤ n¥ n∑ nπ nª nÿ n˜ o o= oa oá o´ o  oÈ p p p p p p  p) p, p. p1 p3 p5 pB pG pJ pM pR pU pX pZ pw pö pß p¨ pÆ p± p∂ p∏ pª pΩ pÃ pœ p‹ p„ pÊ pÈ pÏ pÛ pˆ p˘ p˚ p˝ q& qX qy qÇ qè q¢ q§ q≠ q∫ qÕ qœ q‹ q· q„ qÊ qÎ qÌ q qÚ r r r# r& r) r, r/ r2 r5 r8 r; r> rA rV rY r[ r] r_ rb rd rf rh rk rm ro rå r´ rœ rÒ s s; s_ s~ sù s∫ s√ s∆ s… sÀ s‘ s› sﬂ s· s„ sÂ sÁ sÙ s˘ s¸ sˇ t t t
 t t) tL tY t^ ta tc th tk tm to t~ tã tí tï tò tõ t¢ t• t® t™ t¨ t’ u u( u1 u> uQ uS u\ ui u| u~ uã uê uí uï uö uú uü u° u∞ uΩ u“ u’ uÿ u€ uﬁ u· u‰ uÁ uÍ uÌ u v v v
 v v v v v v v v v v< v[ v v° v≈ vÎ w w. wM wj ws wv wy w{ wÑ wç wê wì wñ wô wõ w® w≠ w∞ w≥ w∏ wª wæ w¿ w› x  x x x x x x x! x# x2 x? xF xI xL xO xV xY x\ x^ x` xâ xª x‹ xÂ xÚ y y y y y0 y2 y? yD yF yI yN yP yS yU yd yq yÜ yâ yå yè yí yï yò yõ yû y° y§ yπ yº yæ y¿ y¬ y≈ y« y… yÀ yŒ y– y“ yÔ z z2 zT zx zû z¬ z· {  { {& {) {, {. {7 {@ {C {F {I {L {N {[ {` {c {f {k {n {q {s {ñ {≥ {¿ {« {  {Õ {– {◊ {⁄ {› {ﬂ {· |
 |< |] |f |s |Ü |à |ë |û |± |≥ |¿ |≈ |» |  |œ |“ |‘ |÷ |Â |Ë |ı |˙ |¸ |ˇ } } }	 } } }' }< }? }B }E }H }K }N }Q }T }W }Z }o }r }t }v }x }{ }~ }Å }É }Ü }à }ä }ß }∆ }Í ~ ~0 ~V ~z ~ô ~∏ ~’ ~ﬁ ~· ~‰ ~Ê ~Ô ~¯ ~˚ ~˛         $ ' * , I l y ~ Ä É à ä ç è û ° Æ µ ∏ ª æ ≈ » À Õ œ ¯ Ä* ÄK ÄT Äa Ät Äv Ä Äå Äü Ä° Ä£ Ä∞ Äµ Ä∑ Ä∫ Äø Ä¡ Äƒ Ä∆ Ä’ Ä‚ Ä˜ Ä˙ Ä˝ Å  Å Å Å	 Å Å Å Å Å* Å- Å/ Å1 Å3 Å6 Å8 Å: Å< Å? ÅA ÅC Å` Å Å£ Å≈ ÅÈ Ç Ç3 ÇR Çq Çé Çó Çö Çù Çü Ç® Ç± Ç¥ Ç∂ Çπ Çª ÇΩ Ç  Çœ Ç“ Ç’ Ç⁄ Ç› Ç‡ Ç‚ É É" É/ É6 É9 É< É? ÉF ÉI ÉL ÉN ÉP Éy É´ ÉÃ É’ É‚ Éı É˜ Ñ  Ñ Ñ  Ñ" Ñ/ Ñ4 Ñ6 Ñ9 Ñ> Ñ@ ÑC ÑE ÑT ÑW Ñd Ñi Ñk Ñn Ñs Ñu Ñx Ñz Ñâ Ññ Ñ´ ÑÆ Ñ± Ñ¥ Ñ∑ Ñ∫ ÑΩ Ñ¿ Ñ√ Ñ∆ Ñ… Ñﬁ Ñ· Ñ„ ÑÂ ÑÁ ÑÍ ÑÌ ÑÔ ÑÒ ÑÙ Ñˆ Ñ¯ Ö Ö4 ÖX Öz Öû Öƒ ÖË Ü Ü& ÜC ÜL ÜO ÜR ÜT Ü] Üf Üi Ük Ün Üp Ür Üt ÜÅ ÜÜ Üâ Üå Üë Üî Üó Üô Üº ÜŸ ÜÊ ÜÌ Ü ÜÛ Üˆ Ü˝ á  á á á á0 áb áÉ áå áô á¨ áÆ á∑ áƒ á◊ áŸ áÊ áÎ áÌ á áı á˜ á˙ á¸ à à à à  à" à% à* à, à/ à1 à@ àM àb àe àh àk àn àq àt àw àz à} àÄ àï àò àö àú àû à° à£ à• àß à™ à¨ àÆ àÀ àÍ â â0 âT âz âû âΩ â‹ â˘ ä ä ä ä
 ä ä ä ä" ä% ä( ä* ä7 ä< ä? äB äG äJ äM äO äl äè äú ä° ä£ ä¶ ä´ äÆ ä± ä≥ ä¬ äœ ä÷ äŸ ä‹ äﬂ äÊ äË äÎ äÓ ä ã ãC ãl ãu ãÇ ãï ãó ã† ã≠ ã¿ ã¬ ãœ ã‘ ã÷ ãŸ ãﬁ ã· ã‰ ãÊ ãı å å å å å  å# å& å) å, å/ å2 å5 åJ åM åO åQ åS åV åX åZ å\ å_ åa åc åÄ åü å√ åÂ ç	 ç/ çS çr çë çÆ ç∑ ç∫ çΩ çø ç» ç— ç” ç’ ç◊ çŸ ç€ çË çÌ ç çÛ ç¯ ç˚ ç˛ é  é é@ éM éR éT éW é\ é^ éa éc ér é éÜ éâ éå éè éñ éô éú éû é† é… é˚ è è% è2 èE èG èP è] èp èr è èÑ èÜ èâ èé èê èì èï è§ è± è∆ è… èÃ èœ è“ è’ èÿ è€ èﬁ è· è‰ è˘ è¸ è˛ ê  ê ê ê ê	 ê ê ê ê ê/ êN êr êî ê∏ êﬁ ë ë! ë@ ë] ëf ëi ël ën ëw ëÄ ëÇ ëÑ ëÜ ëà ëä ëó ëú ëü ë¢ ëß ë™ ë≠ ëØ ë“ ëÔ ë¸ í í í	 í í í í í í í> íp íô í¢ íØ í¬ íƒ íÕ í⁄ íÌ íÔ í¸ ì ì ì ì ì ì ì ì" ì% ì2 ì7 ì9 ì< ìA ìD ìG ìI ìX ìe ìz ì} ìÄ ìÉ ìÜ ìâ ìå ìè ìí ìï ìò ì≠ ì∞ ì≤ ì¥ ì∂ ìπ ìº ìø ì¡ ìƒ ì∆ ì» ìÂ î î( îJ în îî î∏ î◊ îˆ ï ï ï ï" ï$ ï- ï6 ï9 ï< ï? ïB ïD ïQ ïV ïY ï\ ïa ïd ïg ïi ïÜ ï© ï∂ ïª ïΩ ï¿ ï≈ ï« ï  ïÃ ï€ ïË ïÔ ïÚ ïı ï¯ ïˇ ñ ñ ñ ñ	 ñ2 ñd ñÖ ñé ñõ ñÆ ñ∞ ñπ ñ∆ ñŸ ñ€ ñ› ñÍ ñÔ ñÒ ñÙ ñ˘ ñ˚ ñ˛ ó  ó ó ó1 ó4 ó7 ó: ó= ó@ óC óF óI óL óO ód óg ói ók óm óp ós óu ów óz ó| ó~ óõ ó∫ óﬁ ò  ò$ òJ òn òç ò¨ ò… ò“ ò’ òÿ ò⁄ ò„ òÏ òÔ òÚ òı ò¯ ò˙ ô ô ô ô ô ô ô ô ôB ô_ ôl ôs ôv ôy ô| ôÉ ôÜ ôâ ôã ôç ô∂ ôË ö	 ö ö ö2 ö4 ö= öJ ö] ö_ öl öq ös öv ö{ ö} öÄ öÇ öë öî ö° ö¶ ö® ö´ ö∞ ö≤ öµ ö∑ ö∆ ö” öË öÎ öÓ öÒ öÙ ö˜ ö˙ ö˝ õ  õ õ õ õ õ  õ" õ$ õ' õ* õ, õ. õ1 õ3 õ5 õR õq õï õ∑ õ€ ú ú% úD úc úÄ úâ úå úè úë úö ú£ ú¶ ú® ú´ ú≠ úØ úº ú¡ úƒ ú« úÃ úœ ú“ ú‘ ú˜ ù ù! ù( ù+ ù. ù1 ù8 ù; ù> ù@ ùB ùk ùù ùæ ù« ù‘ ùÁ ùÈ ùÚ ùˇ û û û! û& û( û+ û0 û2 û5 û7 ûF ûI ûV û[ û] û` ûe ûg ûj ûl û{ ûà ûù û† û£ û¶ û© û¨ ûØ û≤ ûµ û∏ ûª û– û” û’ û◊ ûŸ û‹ ûﬁ û‡ û‚ ûÂ ûÁ ûÈ ü ü% üI ük üè üµ üŸ ü¯ † †4 †= †@ †C †E †N †W †Y †[ †^ †` †b †d †q †v †y †| †Å †Ñ †á †â †¨ †… †÷ †› †‡ †„ †Ê †Ì † †Û †ı †˜ °  °R °s °| °â °ú °û °ß °¥ °« °… °÷ °€ °› °‡ °Â °Á °Í °Ï °˚ ¢ ¢ ¢ ¢ ¢ ¢ ¢ ¢ ¢- ¢: ¢O ¢R ¢U ¢X ¢[ ¢^ ¢a ¢d ¢g ¢j ¢m ¢Ç ¢Ö ¢á ¢â ¢ã ¢é ¢ë ¢î ¢ñ ¢ô ¢õ ¢ù ¢∫ ¢Ÿ ¢˝ £ £C £i £ç £¨ £À £Ë £Ò £Ù £˜ £˘ § § § § § § § § §) §. §1 §4 §9 §< §? §A §d §Å §é §ï §ò §õ §û §• §® §´ §≠ §Ø §ÿ •
 •+ •4 •A •T •V •_ •l • •Å •é •ì •ï •ò •ù •ü •¢ •§ •≥ •∏ •ª •» •Õ •œ •“ •◊ •Ÿ •‹ •ﬁ •Ì •˙ ¶ ¶ ¶ ¶ ¶ ¶ ¶! ¶$ ¶' ¶* ¶- ¶B ¶E ¶G ¶I ¶K ¶N ¶Q ¶S ¶U ¶X ¶Z ¶\ ¶y ¶ò ¶º ¶ﬁ ß ß( ßL ßk ßä ßß ß∞ ß≥ ß∂ ß∏ ß¡ ß  ßÕ ß– ß” ß÷ ßÿ ßÂ ßÍ ßÌ ß ßı ß¯ ß˚ ß˝ ® ®= ®J ®O ®Q ®T ®Y ®[ ®^ ®` ®o ®| ®É ®Ü ®â ®å ®ì ®ñ ®ô ®õ ®ù ®∆ ®¯ © ©" ©/ ©B ©D ©M ©Z ©m ©o ©x ©Ö ©ä ©å ©è ©î ©ñ ©ô ©õ ©™ ©∑ ©Ã ©œ ©“ ©’ ©ÿ ©€ ©ﬁ ©· ©‰ ©Á ©Í ©ˇ ™ ™ ™ ™ ™ ™ ™ ™ ™ ™ ™ ™7 ™V ™z ™ú ™¿ ™Ê ´
 ´) ´H ´e ´n ´q ´t ´v ´ ´à ´ã ´é ´ë ´î ´ñ ´£ ´® ´´ ´Æ ´≥ ´∂ ´π ´ª ´ÿ ´˚ ¨ ¨ ¨ ¨ ¨ ¨ ¨ ¨ ¨- ¨: ¨A ¨D ¨G ¨J ¨Q ¨T ¨W ¨Y ¨[ ¨Ñ ¨∂ ¨◊ ¨‡ ¨Ì ≠  ≠ ≠ ≠ ≠+ ≠- ≠6 ≠8 ≠E ≠J ≠L ≠O ≠T ≠V ≠Y ≠[ ≠j ≠w ≠å ≠è ≠í ≠ï ≠ò ≠õ ≠û ≠° ≠§ ≠ß ≠™ ≠ø ≠¬ ≠ƒ ≠∆ ≠» ≠À ≠Œ ≠– ≠“ ≠’ ≠◊ ≠Ÿ ≠ˆ Æ Æ9 Æ[ Æ Æ• Æ… ÆË Ø Ø$ Ø- Ø0 Ø3 Ø5 Ø> ØG ØI ØK ØM ØO ØQ Ø^ Øc Øf Øi Øn Øq Øt Øv Øô Ø∂ Ø√ Ø  ØÕ Ø– Ø” Ø⁄ Ø› Ø‡ Ø‚ Ø‰ ∞ ∞? ∞` ∞i ∞v ∞â ∞ã ∞î ∞° ∞¥ ∞∂ ∞√ ∞» ∞À ∞Õ ∞“ ∞’ ∞◊ ∞Ÿ ∞Ë ∞ı ∞˙ ∞¸ ∞ˇ ± ± ±	 ± ± ±' ±< ±? ±B ±E ±H ±K ±N ±Q ±T ±W ±Z ±o ±r ±t ±v ±x ±{ ±} ± ±Å ±Ñ ±Ü ±à ±• ±ƒ ±Ë ≤
 ≤. ≤T ≤x ≤ó ≤∂ ≤” ≤‹ ≤ﬂ ≤‚ ≤‰ ≤Ì ≤ˆ ≤¯ ≤˙ ≤¸ ≤˛ ≥  ≥ ≥ ≥ ≥ ≥ ≥  ≥# ≥% ≥B ≥e ≥r ≥w ≥z ≥| ≥Å ≥Ñ ≥Ü ≥à ≥ó ≥§ ≥´ ≥Æ ≥± ≥¥ ≥ª ≥æ ≥¡ ≥√ ≥≈ ≥Ó ¥  ¥A ¥J ¥W ¥j ¥l ¥u ¥Ç ¥ï ¥ó ¥§ ¥© ¥´ ¥Æ ¥≥ ¥µ ¥∏ ¥∫ ¥… ¥÷ ¥Î ¥Ó ¥Ò ¥Ù ¥˜ ¥˙ ¥˝ µ  µ µ µ	 µ µ! µ# µ% µ' µ* µ, µ. µ0 µ3 µ5 µ7 µT µs µó µπ µ› ∂ ∂' ∂F ∂e ∂Ç ∂ã ∂é ∂ë ∂ì ∂ú ∂• ∂ß ∂© ∂´ ∂≠ ∂Ø ∂º ∂¡ ∂ƒ ∂« ∂Ã ∂œ ∂“ ∂‘ ∂˜ ∑ ∑! ∑( ∑+ ∑. ∑1 ∑8 ∑; ∑> ∑@ ∑B ∑k ∑ù ∑æ ∑« ∑‘ ∑Á ∑È ∑Ú ∑ˇ ∏ ∏ ∏! ∏& ∏) ∏+ ∏0 ∏3 ∏5 ∏7 ∏F ∏S ∏X ∏Z ∏] ∏b ∏d ∏g ∏i ∏x ∏Ö ∏ö ∏ù ∏† ∏£ ∏¶ ∏© ∏¨ ∏Ø ∏≤ ∏µ ∏∏ ∏Õ ∏– ∏“ ∏‘ ∏÷ ∏Ÿ ∏‹ ∏ﬁ ∏‡ ∏„ ∏Â ∏Á π π# πG πi πç π≥ π◊ πˆ ∫ ∫2 ∫; ∫> ∫A ∫C ∫L ∫U ∫W ∫Y ∫[ ∫] ∫_ ∫l ∫q ∫t ∫w ∫| ∫ ∫Ç ∫Ñ ∫ß ∫ƒ ∫— ∫ÿ ∫€ ∫ﬁ ∫· ∫Ë ∫Î ∫Ó ∫ ∫Ú ª ªM ªn ªw ªÑ ªó ªô ª¢ ªØ ª¬ ªƒ ª— ª÷ ªÿ ª€ ª‡ ª‚ ªÂ ªÁ ªˆ º º º
 º º º º º º( º5 ºJ ºM ºP ºS ºV ºY º\ º_ ºb ºe ºh º} ºÄ ºÇ ºÑ ºÜ ºâ ºå ºè ºë ºî ºñ ºò ºµ º‘ º¯ Ω Ω> Ωd Ωà Ωß Ω∆ Ω„ ΩÏ ΩÔ ΩÚ ΩÙ Ω˝ æ æ	 æ æ æ æ æ æ# æ( æ+ æ. æ3 æ6 æ9 æ; æX æ{ æà æç æè æí æó æô æú æû æ≠ æ∞ æΩ æƒ æ« æ  æÕ æ‘ æ◊ æ⁄ æ‹ æﬁ ø ø9 øZ øc øp øÉ øÖ øé øõ øÆ ø∞ øπ ø∆ øÀ øÕ ø– ø’ ø◊ ø⁄ ø‹ øÎ ø¯ ¿ ¿ ¿ ¿ ¿ ¿ ¿ ¿" ¿% ¿( ¿+ ¿@ ¿C ¿E ¿G ¿I ¿L ¿O ¿R ¿T ¿W ¿Y ¿[ ¿x ¿ó ¿ª ¿› ¡ ¡' ¡K ¡j ¡â ¡¶ ¡Ø ¡≤ ¡µ ¡∑ ¡¿ ¡… ¡À ¡Õ ¡œ ¡— ¡” ¡‡ ¡Â ¡Ë ¡Î ¡ ¡Û ¡ˆ ¡¯ ¬ ¬8 ¬E ¬L ¬O ¬R ¬U ¬\ ¬_ ¬b ¬d ¬f ¬è ¬¡ ¬‚ ¬Î ¬¯ √ √ √ √# √6 √8 √E √J √M √O √T √W √Y √[ √j √w √| √~ √Å √Ü √à √ã √ç √ú √© √æ √¡ √ƒ √« √  √Õ √– √” √÷ √Ÿ √‹ √Ò √Ù √ˆ √¯ √˙ √˝ ƒ  ƒ ƒ ƒ ƒ
 ƒ ƒ) ƒH ƒl ƒé ƒ≤ ƒÿ ƒ¸ ≈ ≈: ≈W ≈` ≈c ≈f ≈h ≈q ≈z ≈} ≈Ä ≈É ≈Ö ≈á ≈â ≈ñ ≈õ ≈û ≈° ≈¶ ≈© ≈¨ ≈Æ ≈À ≈Ó ≈˚ ∆  ∆ ∆ ∆
 ∆ ∆ ∆ ∆  ∆# ∆0 ∆7 ∆: ∆= ∆@ ∆G ∆J ∆M ∆O ∆Q ∆z ∆¨ ∆Õ ∆÷ ∆„ ∆ˆ ∆¯ « « «! «# «% «2 «7 «9 «< «A «C «F «H «W «d «y «| « «Ç «Ö «à «ã «é «ë «î «ó «¨ «Ø «± «≥ «µ «∏ «∫ «º «æ «¡ «√ «≈ «‚ » »% »G »k »ë »µ »‘ »Û … … … … …! …* …3 …5 …7 …9 …; …= …J …O …R …U …Z …] …` …b …Ö …¢ …Ø …∂ …π …º …ø …∆ …… …Ã …Œ …– …˘  +  L  U  b  u  w  Ä  ç  †  ¢  Ø  ¥  ∑  π  æ  ¡  √  ≈  ‘  ·  Ê  Ë  Î    Ú  ı  ˜ À À À( À+ À. À1 À4 À7 À: À= À@ ÀC ÀF À[ À^ À` Àb Àd Àg Àj Àm Ào Àr Àt Àv Àì À≤ À÷ À¯ Ã ÃB Ãf ÃÖ Ã§ Ã¡ Ã  ÃÕ Ã– Ã“ Ã€ Ã‰ ÃÁ ÃÍ ÃÌ Ã ÃÚ ÃÙ Ãˆ Õ Õ Õ Õ Õ Õ Õ Õ Õ> Õ[ Õh Õo Õr Õu Õx Õ ÕÇ ÕÖ Õá Õâ Õ≤ Õ‰ Œ Œ Œ Œ. Œ0 Œ9 ŒF ŒY Œ[ Œh Œm Œo Œr Œw Œy Œ| Œ~ Œç Œê Œù Œ¢ Œ§ Œß Œ¨ ŒÆ Œ± Œ≥ Œ¬ Œœ Œ‰ ŒÁ ŒÍ ŒÌ Œ ŒÛ Œˆ Œ˘ Œ¸ Œˇ œ œ œ œ œ œ  œ# œ& œ) œ+ œ. œ0 œ2 œO œn œí œ¥ œÿ œ˛ –" –A –` –} –Ü –â –å –é –ó –† –£ –¶ –© –¨ –Æ –∞ –Ω –¬ –≈ –» –Õ –– –” –’ –¯ — —" —) —, —/ —2 —9 —< —? —A —C —l —û —ø —» —’ —Ë —Í —Û “  “ “ “" “' “) “, “1 “3 “6 “8 “G “J “L “Y “^ “` “c “h “j “m “o “~ “ã “† “£ “¶ “© “¨ “Ø “≤ “µ “∏ “ª “æ “” “÷ “ÿ “⁄ “‹ “ﬂ “‚ “‰ “Ê “È “Î “Ì ”
 ”) ”M ”o ”ì ”π ”› ”¸ ‘ ‘8 ‘A ‘D ‘G ‘I ‘R ‘[ ‘^ ‘a ‘d ‘f ‘h ‘u ‘z ‘} ‘Ä ‘Ö ‘à ‘ã ‘ç ‘∞ ‘Õ ‘⁄ ‘· ‘‰ ‘Á ‘Í ‘Ò ‘Ù ‘˜ ‘˘ ‘˚ ’$ ’V ’w ’Ä ’ç ’† ’¢ ’´ ’∏ ’À ’Õ ’⁄ ’ﬂ ’· ’‰ ’È ’Î ’Ó ’ ’ˇ ÷ ÷ ÷ ÷ ÷ ÷ ÷  ÷# ÷% ÷4 ÷A ÷V ÷Y ÷\ ÷_ ÷b ÷e ÷h ÷k ÷n ÷q ÷t ÷â ÷å ÷é ÷ê ÷í ÷ï ÷ò ÷ö ÷ú ÷ü ÷° ÷£ ÷¿ ÷ﬂ ◊ ◊% ◊I ◊o ◊ì ◊≤ ◊— ◊Ó ◊˜ ◊˙ ◊˝ ◊ˇ ÿ ÿ ÿ ÿ ÿ ÿ ÿ ÿ( ÿ- ÿ0 ÿ3 ÿ8 ÿ; ÿ> ÿ@ ÿ] ÿÄ ÿç ÿí ÿï ÿó ÿú ÿü ÿ° ÿ£ ÿ≤ ÿø ÿ∆ ÿ… ÿÃ ÿœ ÿ÷ ÿŸ ÿ‹ ÿﬁ ÿ‡ Ÿ	 Ÿ; Ÿ\ Ÿe Ÿr ŸÖ Ÿá Ÿê Ÿù Ÿ∞ Ÿ≤ Ÿø Ÿƒ Ÿ∆ Ÿ… ŸŒ Ÿ– Ÿ” Ÿ’ Ÿ‰ ŸÒ ⁄ ⁄	 ⁄ ⁄ ⁄ ⁄ ⁄ ⁄ ⁄ ⁄! ⁄$ ⁄9 ⁄< ⁄> ⁄@ ⁄B ⁄E ⁄H ⁄K ⁄M ⁄P ⁄R ⁄T ⁄q ⁄ê ⁄¥ ⁄÷ ⁄˙ €  €D €c €Ç €ü €® €´ €Æ €∞ €π €¬ €ƒ €∆ €» €  €Ã €Ÿ €ﬁ €· €‰ €È €Ï €Ô €Ò ‹ ‹1 ‹> ‹E ‹H ‹K ‹N ‹U ‹X ‹[ ‹] ‹_ ‹à ‹∫ ‹€ ‹‰ ‹Ò › › › › ›/ ›1 ›> ›C ›E ›H ›M ›O ›R ›T ›c ›l ›y ›~ ›Ä ›É ›à ›ä ›ç ›è ›û ›´ ›¿ ›√ ›∆ ›… ›Ã ›œ ›“ ›’ ›ÿ ›€ ›ﬁ ›Û ›ˆ ›¯ ›˙ ›¸ ›ˇ ﬁ ﬁ ﬁ ﬁ
 ﬁ ﬁ ﬁ+ ﬁJ ﬁn ﬁê ﬁ¥ ﬁ⁄ ﬁ˛ ﬂ ﬂ< ﬂY ﬂb ﬂe ﬂh ﬂj ﬂs ﬂ| ﬂ~ ﬂÄ ﬂÇ ﬂÑ ﬂÜ ﬂì ﬂò ﬂõ ﬂû ﬂ£ ﬂ¶ ﬂ© ﬂ´ ﬂ» ﬂÎ ﬂ¯ ﬂ˝ ﬂˇ ‡ ‡ ‡
 ‡ ‡ ‡ ‡+ ‡2 ‡5 ‡8 ‡; ‡B ‡D ‡G ‡J ‡L ‡m ‡ñ ‡» ‡— ‡ﬁ ‡Ò ‡Û ‡¸ ·	 · · ·' ·4 ·9 ·; ·> ·C ·F ·I ·K ·Z ·g ·j ·m ·p ·s ·u ·Ç ·Ñ ·Ü ·â ·∂ ·√ ·  ·Õ ·– ·” ·⁄ ·‹ ·ﬁ ·‡ ·‚ ·Ò ·ˇ ‚ ‚ ‚ ‚  ‚# ‚& ‚( ‚5 ‚7 ‚9 ‚< ‚ô ‚¶ ‚∑ ‚∫ ‚Ω ‚¿ ‚√ ‚∆ ‚… ‚Ã ‚œ ‚‡ ‚„ ‚Ê ‚È ‚Ï ‚Ô ‚Ú ‚ı ‚¯ ‚˙ „ „  „3 „N „\ „o „Å „è „ò „õ „û „° „Ê „Ô „¸ ‰ ‰	 ‰ ‰ ‰ ‰ ‰8 ‰X ‰a ‰n ‰o ‰p ‰r ‰ ‰Ç ‰Ö ‰à ‰ã ‰ç ‰ü ‰® ‰´ ‰Æ ‰± ‰≥ ‰º ‰¡ ‰Ã ‰Ÿ ‰⁄ ‰€ ‰› ‰Í ‰Ò ‰Ù ‰˜ ‰˙ Â Â Â Â
 Â Â Â Â Â  Âè Âú Âû Â† Â£ Â˙ Ê Ê	 Ê Ê Êe Êr Êy Ê| Ê ÊÇ Êâ Êå Êè Êí Êî Ê√ ÊÚ Á Á$ Á' Á* Á, Á= ÁG ÁJ ÁM ÁP ÁS Á¬ ÁÀ Áÿ Á› Á‡ Á„ ÁË ÁÎ ÁÓ Á Á˜ Á˛ Ë Ë Ë( ËH ËO Ëo ËÖ Ëö Ëß Ë® Ë© Ë´ Ë∏ Ë« Ë  ËÕ Ë– Ë” Ë÷ ËŸ Ë‹ ËÎ ËÓ ËÒ ËÙ Ë˜ Ë˙ Ë˝ È  È È2 È` Èè Èø ÈÍ Í ÍB ÍY Íf Íg Íh Íj Í Íå Íç Íé Íê Íù Íû Íü Í° Í™ Í≠ Í∞ Í≤ Í√ Í∆ Í… ÍÃ Íœ Î& Î/ Î< ÎA ÎD ÎG ÎL ÎO ÎR ÎT Î\ Îh Î Îå Îç Îé Îê Îù Î¶ Î© Î¨ ÎØ Î≤ Îª Îæ Î¡ Îƒ Î« Î… Î˘ Ï( ÏW Ïá Ï£ Ïø Ï» ÏÀ ÏŒ Ï– Ï· Ï‰ ÏÁ ÏÍ ÏÌ ÌD ÌM ÌZ Ìa Ìd Ìg Ìj Ìq Ìt Ìw Ìz Ì| Ì° Ì≠ Ì… Ì“ Ì’ Ìÿ Ì⁄ ÌÁ ÌÙ Ì˜ Ì˙ Ì˝ Ó  Ó Ó Ó Ó Ó Ó Ó Ó Ó! Ó# ÓH Ód Óã Ó± Ó◊ Ó˜ Ô  Ô Ô Ô	 Ô Ô Ô Ô` Ôi Ô{ ÔÇ Ôã Ôî Ô£ Ô™ Ô≥ Ûø Û¬ Û≈ Û» ÛÀ ÛŒ Û— Û‘ Û◊ Û⁄ Û› Û‡ Û„ ÛÊ ÛÈ ÛÏ ÛÔ ÛÚ Ûı Û¯ Û˚ Û˛ Ù Ù Ù Ù
 Ù Ù Ù Ù Ù Ù Ù Ù" Ù% Ù( Ù+ Ù. Ù1 Ù4 Ù7 Ù: Ù= Ù@ ÙC ÙF ÙI ÙL ÙO ÙR ÙU ÙX Ù[ Ù^ Ùa Ùd Ùg Ùj Ùm Ùp Ùs Ùv Ùy Ù| Ù ÙÇ ÙÖ Ùà Ùã Ùé Ùë Ùî Ùó Ùö Ùù Ù† Ù£ Ù¶ Ù© Ù¨ ÙØ Ù≤ Ùµ Ù∏ Ùª Ùæ Ù¡ Ùƒ Ù« Ù  ÙÕ Ù– Ù” Ù÷ ÙŸ Ù‹ Ùﬂ Ù‚ ÙÂ ÙË ÙÎ ÙÓ ÙÒ ÙÙ Ù˜ Ù˙ Ù˝ ı  ı ı ı	 ı ı ı ı ı ı ı ı! ı$ ı' ı* ı- ı0 ı3 ı6 ı9 ı< ı? ıB ıE ıH ıK ıN ıQ ıT ıW ıZ ı] ı` ıc ıf ıi ıl ıo ır ıu ıx ı{ ı~ ıÅ ıÑ ıá ıä ıç ıê ıì ıñ ıô ıú ıü ı¢ ı• ı® ı´ ıÆ ı± ı¥ ı∑ ı∫ ıΩ ı¿ ı√ ı∆ ı… ıÃ ıœ ı“ ı’ ıÿ ı€ ıﬁ ı· ı‰ ıÁ ıÍ ıÌ ı ıÛ ıˆ ı˘ ı¸ ıˇ ˆ ˆ ˆ ˆ ˆ ˆ ˆ ˆ ˆ ˆ ˆ  ˆ# ˆ& ˆ) ˆ, ˆ/ ˆ2 ˆ5 ˆ8 ˆ; ˆ> ˆA ˆD ˆG ˆJ ˆM ˆP ˆS ˆV ˆY ˆ\ ˆ_ ˆb ˆe ˆh ˆk ˆn ˆq ˆt ˆw ˆz ˆ} ˆÄ ˆÉ ˆÜ ˆâ ˆå ˆè ˆí ˆï ˆò ˆõ ˆû ˆ° ˆ§ ˆß ˆ™ ˆ≠ ˆ∞ ˆ≥ ˆ∂ ˆπ ˆº ˆø ˆ¬ ˆ≈ ˆ» ˆÀ ˆŒ ˆ— ˆ‘ ˆ◊ ˆ⁄ ˆ› ˆ‡ ˆ„ ˆÊ ˆÈ ˆÏ ˆÔ ˆÚ ˆı ˆ¯ ˆ˚ ˆ˛ ˜ ˜ ˜ ˜
 ˜ ˜ ˜ ˜ ˜ ˜ ˜ ˜" ˜% ˜( ˜+ ˜. ˜1 ˜4 ˜7 ˜: ˜= ˜@ ˜C ˜F ˜I ˜L ˜O ˜R ˜U ˜X ˜[ ˜^ ˜a ˜d ˜g ˜j ˜m ˜p ˜s ˜v ˜y ˜| ˜ ˜Ç ˜Ö ˜à ˜ã ˜é ˜ë ˜î ˜ó ˜ö ˜ù ˜† ˜£ ˜¶ ˜© ˜¨ ˜Ø ˜≤ ˜µ ˜∏ ˜ª ˜æ ˜¡ ˜ƒ ˜« ˜  ˜Õ ˜– ˜” ˜÷ ˜Ÿ ˜‹ ˜ﬂ ˜‚ ˜Â ˜Ë ˜Î ˜Ó ˜Ò ˜Ù ˜˜ ˜˙ ˜˝ ¯  ¯ ¯ ¯	 ¯ ¯ ¯ ¯ ¯ ¯ ¯ ¯! ¯$ ¯' ¯* ¯- ¯0 ¯3 ¯6 ¯9 ¯< ¯? ¯B ¯E ¯H ¯K ¯N ¯Q ¯T ¯W ¯Z ¯] ¯` ¯c ¯f ¯i ¯l ¯o ¯r ¯u ¯x ¯{ ¯~ ¯Å ¯Ñ ¯á ¯ä ¯ç ¯ê ¯ì ¯ñ ¯ô ¯ú ¯ü ¯¢ ¯• ¯® ¯´ ¯Æ ¯± ¯¥ ¯∑ ¯∫ ¯Ω ¯¿ ¯√ ¯∆ ¯… ¯Ã ¯œ ¯“ ¯’ ¯ÿ ¯€ ¯ﬁ ¯· ¯‰ ¯Á ¯Í ¯Ì ¯ ¯Û ¯ˆ ¯˘ ¯¸ ¯ˇ ˘ ˘ ˘ ˘ ˘ ˘ ˘ ˘ ˘ ˘ ˘  ˘# ˘& ˘) ˘, ˘/ ˘2 ˘5 ˘8 ˘; ˘> ˘A ˘D ˘G ˘J ˘M ˘P ˘S ˘V ˘Y ˘\ ˘_ ˘b ˘e ˘h ˘k ˘n ˘q ˘t ˘w ˘z ˘} ˘Ä ˘É ˘Ü ˘â ˘å ˘è ˘í ˘ï ˘ò ˘õ ˘û ˘° ˘§ ˘ß ˘™ ˘≠ ˘∞ ˘≥ ˘∂ ˘π ˘º ˘ø ˘¬ ˘≈ ˘» ˘À ˘Œ ˘◊ ˘⁄ ˘¸ ˙ ˙ ˙  ˙) ˙, ˙F ˙O ˙R ˙s ˙| ˙ ˙• ˙Æ ˙± ˙€ ˙‰ ˙Á ˚ ˚ ˚" ˚H ˚Q ˚T ˚à ˚ë ˚î ˚¬ ˚À ˚Œ ˚Í ˚Û ˚ˆ ¸ ¸ ¸ ¸F ¸O ¸R ¸â ¸í ¸ï ¸Õ ¸÷ ¸Ÿ ˝
 ˝ ˝ ˝7 ˝@ ˝C ˝a ˝j ˝m ˝ô ˝¢ ˝• ˝≈ ˝Œ ˝— ˝¯ ˛ ˛ ˛) ˛2 ˛5 ˛c ˛l ˛o ˛ù ˛¶ ˛© ˛÷ ˛ﬂ ˛‚ ˇ ˇ ˇ ˇE ˇN ˇQ ˇ~ ˇá ˇä ˇÕ ˇ÷ ˇŸ  
      A  J  M  Ç  ã  é  Õ  ÷  Ÿ 	   / 8 ; Z c f Ö é ë Ø ∏ ª Ê Ô Ú    F O R u ~ Å ™ ≥ ∂ › Ê È     = F I f o r ì ú ü ∂ ø ¬ ﬁ Á Í  # & B K N n w z ° ™ ≠ » — ‘ ˜    ) 2 5 _ h k ì ú ü ≈ Œ — ˜    , 5 8 ] f i ê ô ú ‚ Î Ó  ' * c l o ≠ ∂ π    ? H K Ä â å ƒ Õ – 	 	 	 	[ 	d 	g 	• 	Æ 	± 	Ï 	ı 	¯ 
* 
3 
6 
n 
w 
z 
Ø 
∏ 
ª    R [ ^ § ≠ ∞ ˙   P Y \ ú • ® Â Ó Ò 0 9 < Ä â å “ € ﬁ $ - 0 m v y ∂ ø ¬    ] f i ´ ¥ ∑ ˙   ] f i ´ ¥ ∑ ˙   D M P è ò õ ﬁ Á Í / 8 ; å ï ò Í Û ˆ 5 > A â í ï ÷ ﬂ ‚ # , / l u x ∂ ø ¬    g p s ∏ ¡ ƒ 
   Y b e • Æ ± ¯   D M P ê ô ú ◊ ‡ „ + 4 7 Ç ã é ‘ › ‡ - 6 9 | Ö à √ Ã œ    U ^ a ó † £ ÿ · ‰  ' * o x { ≈ Œ —  & ) g p s ø » À    V _ b Ø ∏ ª 
   S \ _ • Æ ± Í Û ˆ / 8 ; ~ á ä À ‘ ◊ ı ˛       )  ,  J  S  V  w  Ä  É  ß  ∞  ≥  ÿ  ·  ‰ ! ! !" !N !W !Z !Ñ !ç !ê !ƒ !Õ !– " "
 " "9 "B "E "s "| " "§ "≠ "∞ "ÿ "· "‰ # # # #< #E #H #k #t #w #™ #≥ #∂ #ÿ #· #‰ $ $ $ $: $C $F $j $s $v $® $± $¥ $◊ $‡ $„ % % % %^ %g %j %õ %§ %ß %÷ %ﬂ %‚ & & & &K &T &W &Ç &ã &é &≤ &ª &æ &‰ &Ì & ' '' '* 'U '^ 'a 'ï 'û '° '÷ 'ﬂ '‚ ( ( (! (N (W (Z (â (í (ï (À (‘ (◊ ) ) ) )C )L )O )w )Ä )É )Ø )∏ )ª )„ )Ï )Ô *  *) *, *I *R *U *Ä *â *å *Ø *∏ *ª *€ *‰ *Á + + +! +E +N +Q +q +z +} +£ +¨ +Ø +Ã +’ +ÿ ,	 , , ,; ,D ,G ,x ,Å ,Ñ ,™ ,≥ ,∂ ,” ,‹ ,ﬂ - - - -7 -@ -C -m -v -y -û -ß -™ -‘ -› -‡ . . . .6 .? .B .] .f .i .ê .ô .ú .≈ .Œ .— .¸ / / /( /1 /4 /^ /g /j /í /õ /û /√ /Ã /œ /ı /˛ 0 0# 0, 0/ 0n 0w 0z 0π 0¬ 0≈ 0Ù 0˝ 1  1, 15 18 1W 1` 1c 1ç 1ñ 1ô 1º 1≈ 1» 1Á 1 1Û 2" 2+ 2. 2J 2S 2V 2s 2| 2 2û 2ß 2™ 2Œ 2◊ 2⁄ 3 3
 3 32 3; 3> 3a 3j 3m 3ó 3† 3£ 3« 3– 3” 3ı 3˛ 4 4, 45 48 4h 4q 4t 4ä 4ì 4ñ 4À 4‘ 4◊ 5 5 5 5L 5U 5X 5Ö 5é 5ë 5æ 5« 5  5˙ 6 6 66 6? 6B 6q 6z 6} 6´ 6¥ 6∑ 6Â 6Ó 6Ò 7 7' 7* 7N 7W 7Z 7Ö 7é 7ë 7≤ 7ª 7æ 7Ë 7Ò 7Ù 8 8 8! 8E 8N 8Q 8x 8Å 8Ñ 8• 8Æ 8± 8Ÿ 8‚ 8Â 9 9 9 95 9> 9A 9d 9m 9p 9ë 9ö 9ù 9¡ 9  9Õ : :$ :' :f :o :r :± :∫ :Ω :Ú :˚ :˛ ;> ;G ;J ;å ;ï ;ò ;Ã ;’ ;ÿ < <! <$ <f <o <r <± <∫ <Ω <Á < <Û =& =/ =2 =h =q =t =£ =¨ =Ø =Á = =Û >/ >8 >; >m >v >y >π >¬ >≈ >˘ ? ? ?A ?J ?M ?É ?å ?è ?√ ?Ã ?œ @  @	 @ @> @G @J @v @ @Ç @Ø @∏ @ª @ı @˛ A A0 A9 A< A~ Aá Aä AÃ A’ Aÿ B B B BG BP BS Bä Bì Bñ B  B” B÷ C  C	 C C> CG CJ Cá Cê Cì Cœ Cÿ C€ D D( D+ De Dn Dq D• DÆ D± E E E Ea Ej Em EØ E∏ Eª EÙ E˝ F  F; FD FG Fê Fô Fú Fﬂ FË FÎ G+ G4 G7 Gu G~ GÅ G¿ G… GÃ H H H Hc Hl Ho H» H— H‘ I1 I: I= I} IÜ Iâ IΩ I∆ I… J J J Je Jn Jq Jº J≈ J» K K  K# Kq Kz K} K… K“ K’ L L L! Lm Lv Ly L≥ Lº Lø M M M MY Mb Me M™ M≥ M∂ N  N	 N NR N[ N^ N¢ N´ NÆ Nˇ O O OY Ob Oe O≠ O∂ Oπ P P P P] Pf Pi P∞ Pπ Pº Q Q Q Qh Qq Qt QΩ Q∆ Q… R
 R R RZ Rc Rf R≤ Rª Ræ S S" S% S} SÜ Sâ S€ S‰ SÁ T' T0 T3 Tz TÉ TÜ T¡ T  TÕ U! U* U- Un Uw Uz U€ U‰ UÁ VD VM VP Vù V¶ V© Vı V˛ W WH WQ WT Wó W† W£ WÈ WÚ Wı X8 XA XD XÑ Xç Xê X‘ X› X‡ Y  Y) Y, Yo Yx Y{ Y≥ Yº Yø Z Z Z ZP ZY Z\ Zú Z• Z® ZÍ ZÛ Zˆ [5 [> [A [| [Ö [à [Œ [◊ [⁄ \ \& \) \e \n \q \∫ \√ \∆ ] ] ] ]p ]y ]| ]« ]– ]” ^& ^/ ^2 ^} ^Ü ^â ^— ^⁄ ^› _! _* _- _Ä _â _å _” _‹ _ﬂ `$ `- `0 `s `| ` `◊ `‡ `„ a8 aA aD aô a¢ a• a¯ b b bD bM bP bï bû b° b˜ c  c cP cY c\ cÆ c∑ c∫ d d d dN dW dZ dõ d§ dß dÊ dÔ dÚ eC eL eO e∞ eπ eº f f  f# fv f fÇ f◊ f‡ f„ g= gF gI gõ g§ gß gÓ g˜ g˙ hA hJ hM hÜ hè hí hÃ h’ hÿ i, i5 i8 iã iî ió i i˘ i¸ jP jY j\ j≥ jº jø k k k kl ku kx k∑ k¿ k√ kˇ l l lX la ld l± l∫ lΩ l¯ m m mG mP mS mà më mî mﬁ mÁ mÍ n n% n( n\ ne nh nû nß n™ nÈ nÚ nı oG oP oS oì oú oü o„ oÏ oÔ p1 p: p= pÇ pã pé p‘ p› p‡ q q$ q' qr q{ q~ qÃ q’ qÿ r r( r+ r rà rã r‡ rÈ rÏ s5 s> sA s} sÜ sâ s— s⁄ s› t. t7 t: tá tê tì t€ t‰ tÁ u7 u@ uC uÉ uå uè u‘ u› u‡ v& v/ v2 vÑ vç vê v◊ v‡ v„ w. w7 w: wÇ wã wé wŸ w‚ wÂ x5 x> xA xç xñ xô xÙ x˝ y  yV y_ yb yµ yæ y¡ z z z zo zx z{ zÃ z’ zÿ {% {. {1 {Ä {â {å {Â {Ó {Ò |1 |: |= |Ä |â |å |ø |» |À } } } }U }^ }a }ó }† }£ }‘ }› }‡ ~ ~ ~ ~V ~_ ~b ~ë ~ö ~ù ~⁄ ~„ ~Ê " + . p y | ∫ √ ∆ Ä Ä Ä ÄD ÄM ÄP Äå Äï Äò Ä√ ÄÃ Äœ Å Å Å ÅT Å] Å` Å∞ Åπ Åº Ç Ç Ç! Çy ÇÇ ÇÖ Ç‚ ÇÎ ÇÓ ÉE ÉN ÉQ É© É≤ Éµ Ñ Ñ Ñ Ñr Ñ{ Ñ~ Ñ” Ñ‹ Ñﬂ Ö/ Ö8 Ö; Öè Öò Öõ ÖË ÖÒ ÖÙ Ü3 Ü< Ü? Ü} ÜÜ Üâ ÜŒ Ü◊ Ü⁄ Ü¯ á á á á	 á á á á á" á$ á2 á> áC áF áO áV á[ ád ág ái ák áx á{ á} áÄ áÉ áÖ áé áë áì áñ á£ á™ á≠ á∞ á≥ á∫ áΩ á¿ á√ á≈ áÊ à àG àT àY à\ à_ àd àg àj àl àx àÑ àç àï àû à° à™ à± à∂ àø à» àÀ àÿ à› à‡ à„ àË àÎ àÓ à à¸ â â â âR â[ â^ âú â© â¨ âØ â≤ âµ â∑ âı â˛ ä ä
 ä ä, ä/ ä2 ä5 ä8 ä; ä> äA äD äG äJ ä_ äa äd äf äh äk än äq ät äw äz ä| äá äû ä∑ ä– ä„ ä˙ ã ã ã. ãF ãX ãj ã´ ã≤ ã» ãœ ã⁄ ã„ ã‰ ãÁ ãÙ ã˝ å  å å å	 å å å å å å  å9 åT åo å¢ å® å± å≥ åº å√ å∆ å… åÃ åœ å‹ å„ åÊ åÈ åÏ åÛ åˆ å˘ å¸ å˛ ç' çU çÅ çÉ çê çó çö çù ç† çß ç© ç¨ çØ ç± ç∏ ç¡ è  èÃ èŸ èÊ èÈ èÏ èÔ èÚ èı è¯ ê ê ê
 ê ê ê ê ê ê; ê` êä ê¥ êΩ ê∆ êœ ê“ ê˚ ë ë ë ë ë" ë% ë( ë+ ë. ë1 ëB ëD ëG ëI ëK ëM ëP ëS ëU ëW ëo ë~ ëÆ ë  ëÛ í íV íq íì í† í≥ í∂ íπ íº íø í¬ í≈ í» íÀ íŒ í· í„ íÊ íÈ íÏ íÔ íÚ íı í˜ í˙ í¸ ì ì" ì6 ìI ì] ìj ìÄ ìì ì° ìÆ ìΩ ì¿ ì√ ì∆ ì… ìÃ ìœ ì“ ì· ì‰ ìÁ ìÍ ìÌ ì ìÛ ìˆ ì¯ î î1 îC îi îá î™ î… î÷ îﬂ î‚ îÂ îË îÎ îÙ î˜ î˙ î˝ ï  ï ï ï- ïF ïa ï| ïâ ïå ïè ïí ïï ïó ï≤ ïª ïÿ ï€ ïﬁ ñì ñú ñ≈ ñÃ ñı óZ ó} óä óï óò óõ óû ó° ó§ óØ ó≤ óµ ó∏ óª óæ ó¿ óÂ ò	 ò1 òT òw òù ò™ ò≥ ò∂ òπ òº òø ò» òÀ òŒ ò— ò‘ ò÷ òÌ ô ô ô5 ôB ôE ôH ôK ôN ôP ôk õ› õ¯ ú ú ú ú ú ú ú. ú7 úT úW úZ ùΩ ù  ùÕ ù– ù” ù÷ ùÿ ùÛ ù¸ û û û ü© ü∂ üª üæ ü¡ ü∆ ü… üÃ üŒ üÍ ü¯ † † † † † † † †" †% †' †F †[ †g † †ê †ö †• †∞ †≥ †µ †∏ †ª †» †Õ †÷ †Ÿ †‹ †Â †Ë †È †Ú °
 ° °' °? °H °i °n °è °î °° °™ °≠ °∞ °≥ °∂ °ø °¬ °≈ °» °À °Õ °‰ °¯ ¢ ¢, ¢G ¢T ¢W ¢Z ¢] ¢` ¢b ¢} ¢Ü ¢£ ¢¶ ¢© ≥æ ≥À ≥ÿ ≥€ ≥ﬁ ≥· ≥‰ ≥Á ≥Í ≥˜ ≥˙ ≥˝ ¥  ¥ ¥ ¥ ¥	 ¥$ ¥> ¥X ¥s ¥Ç ¥¢ ¥Ø ¥æ ¥¡ ¥ƒ ¥« ¥  ¥Õ ¥– ¥” ¥‚ ¥‰ ¥Á ¥Í ¥Ì ¥Ô ¥Ò ¥Û ¥ı µ  µF µg µ µ≥ µË ∂ ∂ ∂& ∂' ∂* ∂3 ∂Z ∂] ∂` ∂c ∂f ∂i ∂l ∂o ∂r ∂u ∂x ∂{ ∂~ ∂Å ∂Ñ ∂á ∂ä ∂ç ∂ê ∂ì ∂∫ ∂· ∂Ì ∑ ∑  ∑, ∑? ∑f ∑ç ∑ô ∑¿ ∑Ã ∑Û ∑ˇ ∏& ∏M ∏Y ∏Ä ∏õ ∏§ ∏± ∏∂ ∏π ∏º ∏¡ ∏√ ∏∆ ∏» ∏Ú π π π  π# π, π9 π< π? πB πD πF πd πq πv πy π| πÅ πÉ πÖ πá πü π¡ πŒ πÂ πË πÎ πÓ πÒ πÙ π˜ π˙ π˝ ∫  ∫ ∫ ∫ ∫  ∫" ∫% ∫( ∫+ ∫. ∫1 ∫4 ∫7 ∫: ∫= ∫? ∫b ∫è ∫° ∫¨ ∫œ ∫Á ª ª# ªF ªZ ªi ªv ªâ ªã ªñ ªò ª≠ ª∂ ªπ ªº ªø ªÃ ªŸ ª‹ ªﬂ ª‚ ªÂ ªË ªÎ ª¯ ª˚ ª˛ º º º º	 º º º* º@ ºN ºZ ºz º| ºâ ºé ºë ºî ºô ºú ºü º° º∂ º… º“ ºÁ ºÍ ºÌ º ºÛ ºˆ º˘ º¸ ºˇ Ω Ω Ω Ω( ΩD Ωh Ωz Ωò Ω¨ Ω∏ Ω÷ ΩŸ Ω‹ Ωﬂ Ω‚ ΩÂ ΩË ΩÍ ΩÌ æ æ æ( æ@ æS æo ær æu æx æ{ æ~ æÄ æ≤ æª æ» æÀ æŒ æ— æ‘ æ◊ æ⁄ æ‹ æÈ æÙ æ˙ æ˝ æˇ ø ø ø# øB øG øf øs øv øx ø{ øÖ øí øï øó øö ø° øÆ ø± ø¥ ø∑ øæ øÀ øŒ ø– ø” øﬂ øÏ øÔ øÒ øÙ ¿  ¿ ¿ ¿ ¿ ¿® ¿± ¿« ¿Ã ¿‚ ¿Î ¿ˆ ¿˘ ¿¸ ¡d ¡m ¡y ¡Ä ¡å ¡û ¡ß ¡“ ¡◊ ¬ ¬ ¬H ¬U ¬j ¬m ¬p ¬s ¬v ¬y ¬| ¬ ¬Ç ¬Ö ¬à ¬ù ¬† ¬¢ ¬§ ¬¶ ¬© ¬¨ ¬Æ ¬∞ ¬≥ ¬µ ¬∑ ¬‘ ¬Û √ √9 √] √É √ß √∆ √Â ƒ ƒ ƒ ƒ ƒ ƒ ƒ% ƒ( ƒ* ƒ- ƒ/ ƒ1 ƒ> ƒC ƒF ƒI ƒN ƒQ ƒT ƒV ƒs ƒñ ƒ£ ƒ® ƒ´ ƒ≠ ƒ≤ ƒµ ƒ∑ ƒπ ƒ» ƒ’ ƒ‹ ƒﬂ ƒ‚ ƒÂ ƒÏ ƒÔ ƒÚ ƒÙ ƒˆ ≈ ≈Q ≈r ≈{ ≈à ≈õ ≈ù ≈¶ ≈≥ ≈∆ ≈» ≈’ ≈⁄ ≈‹ ≈ﬂ ≈‰ ≈Ê ≈È ≈Î ≈˙ ∆ ∆ ∆ ∆6 ∆W ∆Z ∆] ∆` ∆c ∆f ∆i ∆k ∆n ∆á ∆ä ∆ç ∆ê ∆ì ∆ñ ∆ò ∆° ∆Æ ∆± ∆¥ ∆∑ ∆∫ ∆Ω ∆¿ ∆¬ ∆œ ∆“ ∆‘ ∆◊ ∆Ï ∆˘ ∆¸ ∆˛ « « « « « « «* «- «0 «3 «@ «C «E «H «U «X «Z «] «i «v «y «| «~ » » »- »0 »3 »6 »9 »< »? »B »E »H »K »` »c »e »g »i »l »o »q »s »v »x »z »ó »∂ »⁄ »¸ …  …F …j …â …® …≈ …Œ …— …‘ …÷ …ﬂ …Ë …Î …Ì … …Ú …Ù      	            <  Y  f  m  p  s  v  }  Ä  É  Ö  á  ∞  ‚ À À À À, À. À7 ÀD ÀW ÀY Àf Àk Àm Àp Àu Àw Àz À| Àã Àò Àù Àü À¢ Àß À© À¨ ÀÆ ÀΩ Àﬁ À· À‰ ÀÁ ÀÍ ÀÌ À ÀÚ Àı Ã Ã Ã Ã Ã Ã Ã Ã( Ã5 Ã8 Ã; Ã> ÃA ÃD ÃG ÃI ÃV ÃY Ã[ Ã^ Ãq Ã~ ÃÅ ÃÑ Ãá Ãå Ãô Ãú Ãû Ã° ÃÆ Ã± Ã¥ Ã∑ Ãƒ Ã« Ã… ÃÃ ÃŸ Ã‹ Ãﬁ Ã· ÃÌ Ã˙ Ã˝ Õ  Õ Õã Õò Õ≠ Õ∞ Õ≥ Õ∂ Õπ Õº Õø Õ¬ Õ≈ Õ» ÕÀ Õ‡ Õ„ ÕÂ ÕÁ ÕÈ ÕÏ ÕÔ ÕÚ ÕÙ Õ˜ Õ˘ Õ˚ Œ Œ7 Œ[ Œ} Œ° Œ« ŒÎ œ
 œ) œF œO œR œU œW œ` œi œl œo œr œu œw œÑ œâ œå œè œî œó œö œú œπ œ‹ œÈ œÓ œ œÛ œ¯ œ˙ œ˝ œˇ – – –" –% –( –+ –2 –5 –8 –: –< –e –ó –∏ –¡ –Œ –· –„ –Ï –˘ — — — —  —" —% —* —, —/ —1 —@ —U —v —y —| — —Ç —Ö —à —ä —ç —¶ —© —¨ —Ø —≤ —µ —∑ —¿ —« —  —Õ —– —“ —ﬂ —‚ —‰ —Á —ı “ “ “ “ “ “, “/ “1 “4 “K “X “[ “^ “` “˙ ”" ”/ ”D ”G ”J ”M ”P ”S ”V ”Y ”\ ”_ ”b ”w ”z ”| ”~ ”Ä ”É ”Ö ”á ”â ”å ”é ”ê ”≠ ”Ã ” ‘ ‘6 ‘\ ‘Ä ‘ü ‘æ ‘€ ‘‰ ‘Á ‘Í ‘Ï ‘ı ‘˛ ’  ’ ’ ’ ’ ’ ’ ’ ’  ’% ’( ’+ ’- ’J ’m ’z ’ ’Å ’Ñ ’â ’ã ’é ’ê ’ü ’¨ ’≥ ’∂ ’π ’º ’√ ’∆ ’… ’À ’Õ ’ˆ ÷( ÷I ÷R ÷_ ÷r ÷t ÷} ÷ä ÷ù ÷ü ÷¨ ÷± ÷≥ ÷∂ ÷ª ÷Ω ÷¿ ÷¬ ÷— ÷ﬂ ◊  ◊ ◊ ◊	 ◊ ◊ ◊ ◊ ◊ ◊0 ◊3 ◊6 ◊9 ◊< ◊? ◊A ◊J ◊U ◊X ◊[ ◊^ ◊a ◊d ◊f ◊s ◊v ◊y ◊| ◊î ◊° ◊§ ◊¶ ◊© ◊Æ ◊ª ◊æ ◊¿ ◊√ ◊» ◊’ ◊ÿ ◊⁄ ◊› ◊Í ◊Ì ◊Ô ◊Ú ◊˛ ÿ ÿ ÿ ÿ ÿò ÿ• ÿ∫ ÿΩ ÿ¿ ÿ√ ÿ∆ ÿ… ÿÃ ÿœ ÿ“ ÿ’ ÿÿ ÿÌ ÿ ÿÚ ÿÙ ÿˆ ÿ˘ ÿ˚ ÿ˝ ÿˇ Ÿ Ÿ Ÿ Ÿ# ŸB Ÿf Ÿà Ÿ¨ Ÿ“ Ÿˆ ⁄ ⁄4 ⁄Q ⁄Z ⁄] ⁄` ⁄b ⁄k ⁄t ⁄v ⁄x ⁄z ⁄| ⁄~ ⁄ã ⁄ê ⁄ì ⁄ñ ⁄õ ⁄û ⁄° ⁄£ ⁄∆ ⁄„ ⁄ ⁄˜ ⁄˙ ⁄˝ €  € €
 € € € €: €l €ç €ñ €£ €∂ €∏ €¡ €Œ €· €„ € €ı €˜ €˙ €ˇ ‹ ‹ ‹ ‹ ‹" ‹' ‹) ‹, ‹1 ‹3 ‹6 ‹8 ‹G ‹h ‹k ‹n ‹q ‹t ‹w ‹z ‹| ‹ ‹ò ‹õ ‹û ‹° ‹§ ‹ß ‹© ‹≤ ‹ø ‹¬ ‹≈ ‹» ‹À ‹Œ ‹— ‹” ‹‡ ‹„ ‹Â ‹Ë › › › › › ›! ›. ›1 ›3 ›6 ›C ›F ›H ›K ›X ›[ ›] ›` ›m ›p ›r ›u ›Å ›é ›ë ›î ›ñ ﬁ' ﬁ4 ﬁI ﬁL ﬁO ﬁR ﬁU ﬁX ﬁ[ ﬁ^ ﬁa ﬁd ﬁg ﬁ| ﬁ ﬁÅ ﬁÉ ﬁÖ ﬁà ﬁä ﬁå ﬁé ﬁë ﬁì ﬁï ﬁ≤ ﬁ— ﬁı ﬂ ﬂ; ﬂa ﬂÖ ﬂ§ ﬂ√ ﬂ‡ ﬂÈ ﬂÏ ﬂÔ ﬂÒ ﬂ˙ ‡ ‡ ‡	 ‡ ‡ ‡ ‡ ‡# ‡& ‡) ‡. ‡1 ‡4 ‡6 ‡S ‡v ‡É ‡à ‡ã ‡ç ‡í ‡ï ‡ó ‡ô ‡® ‡µ ‡º ‡ø ‡¬ ‡≈ ‡Ã ‡œ ‡“ ‡‘ ‡÷ ‡ˇ ·1 ·R ·[ ·h ·{ ·} ·Ü ·ì ·¶ ·® ·µ ·∫ ·º ·ø ·ƒ ·∆ ·… ·À ·⁄ ·Á ‚ ‚ ‚ ‚ ‚ ‚ ‚ ‚ ‚ ‚8 ‚; ‚> ‚A ‚D ‚G ‚I ‚R ‚_ ‚b ‚e ‚h ‚k ‚n ‚q ‚s ‚Ä ‚É ‚Ö ‚à ‚• ‚≤ ‚µ ‚∑ ‚∫ ‚¡ ‚Œ ‚— ‚” ‚÷ ‚„ ‚Ê ‚Ë ‚Î ‚¯ ‚˚ ‚˝ „  „ „ „ „ „! „. „1 „4 „6 „« „‘ „È „Ï „Ô „Ú „ı „¯ „˚ „˛ ‰ ‰ ‰ ‰ ‰ ‰! ‰# ‰% ‰( ‰* ‰, ‰. ‰1 ‰3 ‰5 ‰R ‰q ‰ï ‰∑ ‰€ Â Â% ÂD Âc ÂÄ Ââ Âå Âè Âë Âö Â£ Â• Âß Â© Â´ Â≠ Â∫ Âø Â¬ Â≈ Â  ÂÕ Â– Â“ Âı Ê Ê Ê& Ê) Ê, Ê/ Ê6 Ê9 Ê< Ê> Ê@ Êi Êõ Êº Ê≈ Ê“ ÊÂ ÊÁ Ê Ê˝ Á Á Á Á$ Á' Á) Á. Á1 Á3 Á5 ÁD ÁQ ÁV ÁX Á[ Á` Áb Áe Ág Áv Áó Áö Áù Á† Á£ Á¶ Á© Á´ ÁÆ Á« Á  ÁÕ Á– Á” Á÷ Áÿ Á· Á ÁÛ Áˆ Á˘ Á¸ Áˇ Ë Ë Ë Ë Ë Ë Ë Ë/ Ë< Ë? ËA ËD ËK ËX Ë[ Ë] Ë` Ëm Ëp Ër Ëu ËÇ ËÖ Ëá Ëä Ëó Ëö Ëú Ëü Ë¨ ËØ Ë± Ë¥ Ë¿ ËÕ Ë– Ë” Ë’ Èc Èp ÈÖ Èà Èã Èé Èë Èî Èó Èö Èù È† È£ È∏ Èª ÈΩ Èø È¡ Èƒ È∆ È» È  ÈÕ Èœ È— ÈÓ Í Í1 ÍS Íw Íù Í¡ Í‡ Íˇ Î Î% Î( Î+ Î- Î6 Î? ÎA ÎC ÎE ÎG ÎI ÎV Î[ Î^ Îa Îf Îi Îl În Îë ÎÆ Îª Î¬ Î≈ Î» ÎÀ Î“ Î’ Îÿ Î⁄ Î‹ Ï Ï7 ÏX Ïa Ïn ÏÅ ÏÉ Ïå Ïô Ï¨ ÏÆ Ïª Ï¿ Ï¬ Ï≈ Ï  ÏÃ Ïœ Ï— Ï‡ ÏÌ ÏÚ ÏÙ Ï˜ Ï¸ Ï˛ Ì Ì Ì Ì3 Ì6 Ì9 Ì< Ì? ÌB ÌE ÌG ÌJ Ìc Ìf Ìi Ìl Ìo Ìr Ìt Ì} Ìà Ìã Ìé Ìë Ìî Ìó Ìô Ì¶ Ì© Ì´ ÌÆ Ì¿ ÌÕ Ì– Ì“ Ì’ Ìÿ ÌÂ ÌË ÌÍ ÌÌ Ì˙ Ì˝ Ìˇ Ó Ó Ó Ó Ó Ó# Ó0 Ó3 Ó6 Ó8 Óµ Ó¬ Ó◊ Ó⁄ Ó› Ó‡ Ó„ ÓÊ ÓÈ ÓÏ ÓÔ ÓÚ Óı Ô
 Ô Ô Ô Ô Ô Ô Ô Ô Ô! Ô# Ô% ÔB Ôa ÔÖ Ôß ÔÀ ÔÒ  4 S p y |  Å ä ì ñ ô ú ü ° Æ ≥ ∂ π æ ¡ ƒ ∆ „ Ò Ò Ò Ò Ò Ò" Ò% Ò' Ò) Ò8 ÒE ÒL ÒO ÒR ÒU Ò\ Ò_ Òb Òd Òf Òè Ò¡ Ò‚ ÒÎ Ò¯ Ú Ú Ú Ú# Ú6 Ú8 ÚE ÚJ ÚL ÚO ÚT ÚV ÚY Ú[ Új ÚÉ Ú§ Úß Ú™ Ú≠ Ú∞ Ú≥ Ú∂ Úπ Úº Ú’ Úÿ Ú€ Úﬁ Ú· Ú‰ ÚÊ ÚÔ Ú¸ Úˇ Û Û Û Û Û Û Û Û  Û" Û% Û3 Û@ ÛC ÛF ÛI ÛV ÛY Û[ Û^ Ûk Ûn Ûq Ût ÛÅ ÛÑ ÛÜ Ûâ Ûñ Ûô Ûõ Ûû Û™ Û∑ Û∫ ÛΩ Ûø ÙE ÙR Ùg Ùj Ùm Ùp Ùs Ùv Ùy Ù| Ù ÙÇ ÙÖ Ùö Ùù Ùü Ù° Ù£ Ù¶ Ù© Ù¨ ÙÆ Ù± Ù≥ Ùµ Ù“ ÙÒ ı ı7 ı[ ıÅ ı• ıƒ ı„ ˆ  ˆ	 ˆ ˆ ˆ ˆ ˆ# ˆ% ˆ' ˆ) ˆ+ ˆ- ˆ: ˆ? ˆB ˆE ˆJ ˆM ˆP ˆR ˆo ˆí ˆü ˆ§ ˆß ˆ© ˆÆ ˆ± ˆ≥ ˆµ ˆƒ ˆ— ˆÿ ˆ€ ˆﬁ ˆ· ˆË ˆÎ ˆÓ ˆ ˆÚ ˜ ˜M ˜n ˜w ˜Ñ ˜ó ˜ô ˜¢ ˜Ø ˜¬ ˜ƒ ˜Õ ˜⁄ ˜ﬂ ˜· ˜‰ ˜È ˜Î ˜Ó ˜ ˜ˇ ¯ ¯ ¯ ¯ ¯ ¯5 ¯8 ¯; ¯\ ¯_ ¯b ¯e ¯h ¯k ¯n ¯p ¯s ¯å ¯è ¯í ¯ï ¯ò ¯õ ¯ù ¯¶ ¯± ¯¥ ¯∑ ¯∫ ¯Ω ¯¿ ¯¬ ¯œ ¯“ ¯‘ ¯◊ ¯‰ ¯Á ¯È ¯Ï ¯˘ ¯¸ ¯˛ ˘ ˘ ˘ ˘ ˘ ˘# ˘& ˘( ˘+ ˘7 ˘D ˘G ˘J ˘L ˘Y ˘n ˘q ˘t ˘w ˘z ˘} ˘Ä ˘É ˘Ü ˘â ˘å ˘° ˘§ ˘¶ ˘® ˘™ ˘≠ ˘∞ ˘≤ ˘¥ ˘∑ ˘π ˘ª ˘ÿ ˘˜ ˙ ˙= ˙a ˙á ˙´ ˙  ˙È ˚ ˚ ˚ ˚ ˚ ˚  ˚) ˚, ˚/ ˚2 ˚5 ˚7 ˚D ˚I ˚L ˚O ˚T ˚W ˚Z ˚\ ˚y ˚ú ˚© ˚Æ ˚± ˚≥ ˚∏ ˚ª ˚Ω ˚ø ˚Œ ˚€ ˚‚ ˚Â ˚Ë ˚Î ˚Ú ˚ı ˚¯ ˚˙ ˚¸ ¸% ¸W ¸x ¸Å ¸é ¸° ¸£ ¸¨ ¸π ¸Ã ¸Œ ¸€ ¸‡ ¸‚ ¸Â ¸Í ¸Ï ¸Ô ¸Ò ˝  ˝
 ˝ ˝+ ˝0 ˝H ˝Z ˝s ˝v ˝y ˝| ˝ ˝Ç ˝Ñ ˝ç ˝ò ˝õ ˝û ˝° ˝§ ˝ß ˝© ˝∂ ˝π ˝ª ˝æ ˝À ˝Œ ˝– ˝” ˝‡ ˝„ ˝Â ˝Ë ˝ı ˝¯ ˝˙ ˝˝ ˛
 ˛ ˛ ˛ ˛ ˛+ ˛. ˛1 ˛3 ˛@ ˛I ˛L ˛O ˛R ˛U ˛^ ˛a ˛d ˛g ˛j ˛l ˛u ˛ ˛ä ˛¢ ˛Ø ˛∏ ˛ª ˛æ ˛¡ ˛ƒ ˛Õ ˛œ ˛— ˛” ˛’ ˛◊ ˛ı ˇ ˇ5 ˇN ˇ[ ˇl ˇo ˇr ˇu ˇx ˇ{ ˇ~ ˇÅ ˇÑ ˇï ˇó ˇô ˇõ ˇù ˇü ˇ° ˇ£ ˇ• ˇß ˇ’    2  a  å  º  Ó  * - 0 3 6 8 L U Z ] ` c p w z } Ä á ä å è ë ü ¨ ø » — ﬁ Â Ë Î Ó ı ¯ ˙ ˝ ˇ 	     ! # 0 3 6 9 < > R [ ` c f i v } Ä É Ü ç ê í ï ó ° ™ ∑ æ ¡ ƒ « Œ — ” ÷ ÿ Í Û            <€             ı

/== GameCatalog.xcodeproj/project.xcworkspace/xcuserdata/gilangramadhan.xcuserdatad/UserInterfaceState.xcuserstate
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

