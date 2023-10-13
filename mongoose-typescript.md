### Typescript with Mongoose

### DocType
We will need several types for the ```DocType``` that will help Mongoose type the `Schema` and `Model` for a collection. 

1. We need an interface that reflects the structure of the resource. 

```
interface User {
  id: string
  username: string
  password: string
}
```
2. We need another interface for the Mongo document. This will extend the resource interface and the `Document` interface provided by Mongoose.
```
interface UserDocument extends User, Document {}
```

### Typing `Schema` and `Model`
The ```Schema``` constructor has 4 optional type parameters: ```DocType```, ```M```, ```TInstanceMethods```, ```TQueryHelpers```.

```
new Schema<User, UserModel, UserMethods, QueryMethods>({ ... })
```

The ```Model``` must be typed as well it is created with `model`. `model` takes 2 optional type parameters: ```DocType``` and  ```M```
```
const Model = model<User, UserModel>('user', userSchema)
```

#### DocType

```DocType``` is the type of the resource. You can supply your own type or have Mongoose infer a type from the schema. 
```
interface User {
  id: string
  username: string
  password: string
}

new Schema<User>({...})
``` 
#### Instance methods and query helpers.
```M``` is the Mongoose model type. Pass it if you are using query helpers or instance methods. The ```Model``` type takes 3 type parameters: ```DocType```, ```TQueryHelpers```, and ```TInstanceMethods```.

For example, if you need instance methods: 
1. Write a type for your methods.
```
interface UserMethods {
  passwordMatches(password: string): Promise<boolean>
}
```
2. Make ```M```, the mongoose ```Model``` type
```
interface UserModel =  Model<User, {}, UserMethods>
```

3. Make the ```Schema``` with all the appropriate parameters.
```
new Schema<User, UserModel, UserMethods>({...})
``` 
Note that ```UserMethods``` is passed to both the ```Model``` type and ```Schema``` type.

#### Static methods
To attach a static method to the Model, just add it to the ```Model``` type before passing it to the ```Schema``` constructor. 
```
interface UserModel extends Model<UserDocument, {}, UserMethods> {
  emailExists(email: string): Promise<boolean>
}
```


#### In mongo:

```collections``` are tables \
```documents``` are records, or rows

### Mongoose basics

##### Schema
```Schema``` defines the shape of a document within a collection.
```
const userSchema = new Schema({
  username: String,
  password: String
})
```
They are used to compile into a ```Model```.
```
const UserModel = mongoose.model('user', userSchema)
```
##### Model
```Model``` is the interface to which you query the database. It has many built-in static functions such as ```find()``` and ```create()``` for this purpose. 
```
const query = UserModel.find({id: userId})
```
When you use these querying functions on ```Model```, it returns a ```Query```.
##### Query
```Query``` seems to return a promise that resolves with a ```Document```.
```
const document:Document = await UserModel.find({id: userId}).select('name')
```

But it is actually 'thenable', meaning it includes a ```then()``` method so it can be used as if it were a promise. But unlike a promise it also has other methods such as ```select()``` that allows chaining syntax. 

```
const query:Query = UserModel.find({id: userId})
query.select('name')
const document:Document = query.exec()
```
##### Document
```Document``` is an instance of ```Model```.
```
const document = new UserModel()
```
Changes to that specific document in mongodb can be made by manipulating ```Document```. 
```
document.name = 'New Name'
await document.save()
```
This is the same as just updating the document through the ```Model``` with a query at the collection level. 
```
await UserMode.findByIdAndUpdate(userId, {name: 'New Name'})

```
