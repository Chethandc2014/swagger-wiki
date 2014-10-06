# Routes
# This file defines all application routes (Higher priority routes first)
# ~~~~

# Home page
GET     /                           controllers.Application.index()


# USERS
# -----

GET     /users                              controllers.User.getUsers()
POST    /users                              controllers.User.addUser()

GET     /users/:userId/feeds                controllers.User.getFeeds(userId)
POST    /users/:userId/feeds/:feedId        controllers.User.addFeedToUser(userId, feedId)
DELETE  /users/:userId/feeds/:feedId        controllers.User.remove(userId, feedId)


# FEEDS
# -----

GET     /feeds                              controllers.Feed.getAll()
GET     /feeds/:feedId                      controllers.Feed.getOne(feedId)
POST    /feeds                              controllers.Feed.add()
POST    /feeds/:feedId/categories/:catId    controllers.Feed.addCategory(feedId, catId)
GET     /feeds/:feedId/categories           controllers.Feed.getCategories(feedId)
DELETE  /feeds/:feedId                      controllers.Feed.remove(feedId)
DELETE  /feeds/:feedId/categories/:catId    controllers.Feed.removeCategory(feedId, catId)


# CATEGORIES
# ----------

GET     /categories                         controllers.Category.getAll()
GET     /categories/:catId                  controllers.Category.getOne(catId)
GET     /categories/:catId/feeds            controllers.Category.getByCategory(catId)
POST    /categories                         controllers.Category.add()
DELETE  /categories/:catId                  controllers.Category.remove(catId)


# Map static resources from the /public folder to the /assets URL path
GET     /assets/*file               controllers.Assets.at(path="/public", file)
