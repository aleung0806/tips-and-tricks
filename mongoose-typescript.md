### Typescript with Mongoose

#### In mongo:

```collections``` are tables \
```documents``` are records, or rows

### Basics

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
```Model``` is the interface to which you query the database. It has many built-in static helper functions such as ```find()``` and ```create()``` for this purpose. 
```
const query = UserModel.find({id: userId})
```
When you use these querying functions on ```Model```, it returns a ```Query```.
##### Query
```Query``` seems to return a promise that resolves with a ```Document```.
```
const document:Document = await UserModel.find({id: userId}).select('name')
```

Actually, ```Query``` is 'thenable', meaning it includes a ```then()``` method so it can be used as if it was a promise. But it also has other methods such as ```select()``` that allows chaining syntax. 

```
const query:Query = UserModel.find({id: userId})
query.select('name')
const document:Document = query.exec()
```
##### Document
```Document``` is an instance of ```Model```
```
const document = new UserModel()
```
Changes to that specific document in mongodb can be made by manipulating the ```Document```. 
```
document.name = 'New Name'
await document.save()
```
This is the same as just updating the document through the ```Model``` with a query at the collection level. 
```
await UserMode.findByIdAndUpdate(userId, {name: 'New Name'})

```

### Adding Types
```
interface User {
  id: string
  username: string
  password: string
}
``` 