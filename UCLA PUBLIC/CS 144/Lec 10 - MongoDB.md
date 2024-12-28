# MongoDB
- Document store, stored in BSON
- Allows creating multiple databases, with multiple collections of documents
	- Documents schema less
### JSON
- Limited data types (no dates or binary data), not fixed length fields, UTF-8
	- String, Boolean, Number, Array, Object, null
### BSON
- No double quotes for field names
- `_id` is the primary key (unique, any type except array, automatic if not provided)
- Same data types but specific numerical types (Integer, Float, Long), dates, BinData
- Optimized (wired tiger reads it)
- Similar to Protocol Buffers or Thrift
## Commands
- `mongo` to start
- `use dbName`: change to database
- `show dbs`, `show collections`, `db.someCollection.drop()`, `db.dropDatabase()`, `db.collection.countDocuments(filter)`
- Create: `insertOne()`, `insertMany()`
- Retrieve: `findOne()`, `find()`
- Update: `updateOne()`, `updateMany()`
- Delete: `deleteOne()`, `deleteMany()`
### Find
```js
db.Books.find(
// Selection condition (WHERE). Find programming books.
	{ "category": "programming" },
	// Projection operator. Hide _id, show title (0/1)
	{ "_id": 0, "title": 1 }
)
// sort by title ascending (vs -1 descending), and limit.
.sort({ "title": 1 })
.limit(5);
```
Aggregate:
- Must use `$` when using fields in aggregate
```js
db.Books.aggregate( [
// Match stage (WHERE).
{
"$match": { "category": "programming" }
},
// Group stage (GROUP BY). Can use multiple functions.
// use _id: null for global aggregate.
{
"$group": { "_id": "$language", "AvgPrice": { "$avg": "$price" } }
},
//optional Projection
{ "$project": { "_id": 1, "AvgPrice": 1} },
// optional Sort stage
{ "$sort" : { "AvgPrice": -1 } },
// optional Limit stage
{ "$limit" : 10 }
] );
```
Group by multiple fields:
```
"_id": {
"language": "$language",
"publisher": "$publisher"
}
```
### Update
```js
db.collection.updateOne(
{ code: "CS 143"}, 
{
	$set: { grader: ["Cats"] }, // replace array
	$push: {ta: "Hughes"} // push to array
})
```
## Architecture
- Distributed architecture
- MongoDB uses horizontal scaling through sharding + replication
- Shards: each replica set, and contains subset of data
	- Sharded at collection level, groups of documents within collection distributed across shareds
	- Shard key controls which shard a dicoument is stored, based on one or more fields
	- to shard collection, must be index (CreateIndex)
	- Within each shard, documents are **chunked** containing ranges of shard key values
	- **Balancer** migrates data around between shards
- Mongos: query routers, interface between client and cluster
- Config servers: store metadata and config settings, deployed as replica set
- **Replica set**: HA and redundancy via replication, contains multiple copies
	- Contains set of data nodes, each node belongs to one replica set
	- Each set consists of several data bearing nodes and an arbiter
	- One primary node, all others are secondary
		- All r/w on primary, then async copied to secondary
	- Write is successful only if majority of data bearing nodes acknowledge write
		- **heartbeat** used
**automatic failover** for up to 12s
- **Elections**:
	- Election:
		- If secondaries can't communicate with primary, adding new node, new replica set created, admin flatting primary is undergoing maintenance
	- eligible secondary calls for election, nominates itself to become primary w/ already known priorities. Replica set members with higher priorities keep calling election and becoming primary until it converges
	- Can configure to allow reads from secondary replica sets, writes won't happen during 
	- Up to 50 members/nodes in replica set, only 7 have voting power
- Partition tolerance:
	- If partitioned into minority node count (3 or fewer voting members), primary steps down
	- Member that can communicate with majority holds new election
## Advantages
- Indexing for geospatial data
- Full text search
- each aggregation pipelines
- user can fix the size of a collection
- Collections can expire (TTL)
## Disadvantages
- three data access mechanisms: query, mapreduce, and aggregate
- feel built piecemeal
- security holes
- Not usable for duplicating critical data or any joins, or links between documents
# Mongoose
Object Relational Mapping (ORM) for interfacing with a relational database without writing SQL
Object Document Model (ODM) for interfacing with document databases.
Object Graph Mapper (OGM) for interfacing with graph atabases.
- Most popular ODM is Mongoose
	- CRUD is done async
## Connecting
```js
import mongoose from 'mongoose';
mongoose.set("strictQuery", false);
const mongoDB = "mongodb://127.0.0.1/lec10";
async function main() {
	await mongoose.connect(mongoDB);
}
main().catch((err) => console.log(err));
```
## Models
```
const Schema = mongoose.Schema
let airbnbSchema = new Schema({
	_someId: Schema.Types.ObjectId,
	listing_url: { type: String, lowercase: true, trim: true, required: true },
	name: String,
	accommodates: Number,
	last_scraped: { type: Date, default: Date.now() }
});
const AirbnbListingModel = mongoose.model("AirbnbListing", airbnbSchema);
// The collection will be called airbnblistings
```
- Schema: SchemaTypes, default values, built-in validators (min/max) and custom functions, required, string processing (lowercase, trim, etc), virtual properties (e.g. concatenate strings)
```js
const airbnb_instance = new AirbnbListingModel({
	"listing_url": "http://www.airbnb.com/surf-shack",
	"name": "Ryan's Surf Shack",
	"accommodates": 8,
});
// Save the new model instance asynchronously
await airbnb_instance.save();
```
- You only need the fields in the model that you want to work with.
- `.save()` saves into db
```js
const AirbnbListing = mongoose.model("AirbnbListing", airbnbSchema);
const listings = await AirbnbListing.find({
	accommodates: { $gt: 2 },
	bedrooms: { $gt: 1 } }).exec();
// OR
const listings_pipe = await AirbnbListing.find()
.where("accommodates")
.gt(2)
.where("bedrooms")
.gte(2)
.where("name")
.equals("Ryan's Surf Shack")
.limit(5)
.sort({ "price": -1 })
.select("name price")
.exec();
```
### Updates
```js
// Two ways. Vanilla JS:
const SurfShack = mongoose.model("AirbnbListing", airbnbSchema);
const shack_listing = await SurfShack.findOne({ "name": "Ryan's Surf Shack"}).exec();
shack_listing.name = "Ryan's Mountain Bike Paradise";
await shack_listing.save();
// save gives us access to a lot of validation and middleware.
// shack_listing.validate();
// Using a query
await AirbnbListing.updateOne( { name: "Ryan's Mountain Bike Paradise"}, { name: "Ryan's Mountain Biking Hideaway" }
);
```
### Notes
- 
