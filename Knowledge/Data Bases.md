## Trade-offs in DB Design
![[Pasted image 20250619123059.png]]
![[Pasted image 20250619124130.png]]

## Trade-offs in Transactions
- One transaction might read data that another is about to update. 
- Two users might try to reserve the same inventory slot. 
- A background job might lock a record moments before a customer clicks "Confirm." 
Such scenarios can result in conflicts, race conditions, and deadlocks that stall the system entirely.
# Notes
## 1 -  [[Relational Data Bases]]
+ SQL, Relational DB Design, indexes, etc.

# Firebase
+ auth
+ databases
+ file storage
+ security rules
+ analytics


# Mongo DB
## Data Modeling
+ The key idea of MongoDB is to identify your access (query) patterns **then** design data model to support it
+ ==Data access together shoud be stored together==
---
+ entities stored in documents and collections
+ JSON-like format
---
+ Polymorphism: store documents with different structures or fields within the same collection
+ Embedding: store documents with other documents (sub-documents)
+ Normalize data: referencing other documents from a main document

---
+ Schema validation: Defines rules and constraints
+ Executed on the database layer
---
**For modelling**
+ Identify entities
+ Identify read and write operations and what entities are involved for business needs (functional requirements)
	+ quantify on-average read and writes operations
---
### Use referencing or embedding

Relationship should be implemente using one of these strategies
+ **Reference**: Separate doucments linked using a key
+ **Embbeding**: subdocuments / things stored in the same document:
	+ ==Data access together shoud be stored together==


##### Guidelines to decide if embed or reference
 https://learn.mongodb.com/learn/course/relational-to-document-model/relational-to-document-model/design-relationships?page=2
+ simplicity? is more simple to store both entities together? -> If yes embed
+ are the same documents updated together?
+ are the same documents written at a different times in write-heavy workload?
+ Document size? would the combined size of the piece of information take too much memory or transfer bandwidth? If not -> embed
+ Document Growth? If we embend would the document possibly growth without boundary? If not -> embed
+ Individuality? For the child side of the relationship can the parent pieces exist by themselves without a parent? If not -> embed (e.g an author could not exits in the app without a book)
+ query atomicity? both entities are query together?
+ Data duplication would be very difficult to manage?

> [!IMPORTANT]
> if there is a possibility of and un-bounded array in a document then avoid embeddings

## Implementation of relationships

### One-to-one relationships
two strategies:

**1. Embed**
1. embed the as a subdocument
```json
// publishers
{
	"name": "Publisher Inc."
	// embedded sub-document
	"headquarters": {
		"zip": 23402948,
		"street": "St bla bla"
	}
}
```

**2. Reference**
1. a has a reference to b
2. b has a reference to a
3. bi-directional reference
Choose depending of access-patterns of your app

### One-to-many relationship

Two strategies
**1. embed**
1. embed the "many" side as an array of sub-documents in the "one" side (prefered)
2. create a sub-document within the parent document
```json
{
"books": {
	// sub-document for the many side
	"reviews": {
		"user-123": {
			"rating": 3.0,
			"content": "Mid book"
		},
		"user-236": {
			"rating": 4.0,
			"content": "dfajfal jfadl"
		},
		// ....
	}
}
```

**2. references**
(Use this if the array is un-bounded or very-large)

1. Use an array of references (ids) within the parent document 
2. bi-directional references

### Many-to-Many relationship

**1. embed**
1. embed the document in the parent side, this means duplicate for each new parent -> **Data duplication**
![[Pasted image 20250622225429.png]]
duplication is a matter of trade-offs   


 **2. Reference**
 + array of references 
+ Don't use bi-directional references

## Validate Schemas
+ Validations rules
+ Validations levels
+ validations actions
---
+ Ensures there are no un-intented or mal-formed fields once you know your schema
+ is a contract
---
+ Validations rules are implemented using $jsonSchema standard
+ there are two options when define schema validation rules, validation level options:
	+ strict
	+ moderated: only apply rules for new documents

