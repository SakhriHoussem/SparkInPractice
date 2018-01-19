# Spark in Practice

## Learn How Spark Work in Simple Examples

```shell
# Spark startup commande
spark-shell
```

### Example1 :

#### Build a RDD from text file :
```scala
val Alice= sc.textFile("file:<file path>/input/Alice.txt")
```

#### Count the number of RDD elements :

```scala
Alice.count()
```

#### View the first element of RDD :

```scala
Alice.first()
```

#### Return a new RDD containing only the lines with a specific word :

```scala
// get lines with a specific word
val lignes = Alice.filter(line => line.contains("walking"))
// save in folder
val sup = lignes.saveAsTextFile("file:/home/cloudera/walking")
lignes.collect()
```

#### Count the number of occurrences of a specific word :

```scala
// split text on words
val words = Alice.flatMap(line => line.split(" "))
// count the number of occurrences of a specific word "was"
words.filter(l => l.contains("was")).count()
```

#### Return the largest line in the text:

```scala
// get for each line his length
val longueursLignes = Alice.map(l => (l.length,l))
// ordered desc by length and get the first (longest)
longueursLignes.sortByKey(false,1).first()
```

#### Show anagram words list : 

```scala
// transform each line to lowercase and remove character special 
val textPure= Alice.map(line => (line.toLowerCase().replace(":"," ").replace("*"," ").replace("/"," ").replace("["," ").replace("]"," ").replace(","," ").replace("\""," ").replace("-"," ").replace("`"," ").replace("'"," ").replace("_"," ").replace(";"," ").replace("."," ").replace("!"," ").replace("?"," ")))
// split text on words
val words = textPure.flatMap(line => line.split(" "))
// delete the words double
val word = words.distinct()
// generate (key,word)
val wordl = word.map(line => (line.toList.sorted,line))
// group words that have the same key
val anag = wordl.reduceByKey((a,b) => if(a.contains(b)) a else a + ", " + b)
// view RDD content
anag.collect
// save in folder
val w = anag.saveAsTextFile("file:/home/cloudera/anagram3")
  ```

### Example2 : 

#### Build a RDD from text file (twitter compte followed by another compte) :

```scala
val twitter = sc.textFile("file:/home/cloudera/twitter.txt")
```

#### Get the number of arcs between twitter uers :

```scala
twitter.count()
```

#### Count the number of Twitter users in this file :

```scala
// split text on users
val usersDouble = twitter.flatMap(line => line.split(" "))
// delete the user ID double
val users = usersDouble.distinct().count()
 ```

#### Get number of follower of each user :

```scala
// generate (user ID , 1)
val degres = twitter.map(x=>(x.split(" ")(0),1))
// reduce by Key
val degre = degres.reduceByKey(_ + _)
// save in folder
val w = degre.saveAsTextFile("file:/home/cloudera/degre")
```

#### Get twitter user who has 1000 followers :

```scala
// generate (user ID , 1)
val degres = twitter.map(x=>(x.split(" ")(0),1))
// reduce by Key
val degre = degres.reduceByKey(_ + _)
// filerd users how has 1000 followers
val filtred = degre.filter{case(x,y)=>y>=1000}
// count users how has 1000 followers
filtred.count()
// save users how has 1000 followers
val sup = filtred.saveAsTextFile("file:/home/cloudera/1000")
```
