# Cosmos Keeper Utilities

A Go library providing Active Record pattern implementations for the Cosmos SDK, featuring auto-incrementing IDs, string-keyed storage, and one-to-many associations.

## Features

- **Auto-incrementing ID management** - Uint64KeyedIterableKeeper for sequential ID tracking
- **String-keyed storage** - StringKeyedKeeper for flexible key-based storage
- **One-to-many associations** - Uint64AssociationKeeper for relational data modeling
- **Active Record pattern** - Simplified object persistence and retrieval

## Installation

```bash
go get github.com/shanev/cosmos-keeper-utilities
```

## Quick Start

### Basic Usage

```go
import (
    "github.com/cosmos/cosmos-sdk/types"
    keeperutil "github.com/xerion0712/cosmos-keeper-utilities"
)

type AppKeeper struct {
    keeperutil.RecordKeeper
}

// Initialize keeper
appKeeper := AppKeeper{
    RecordKeeper: keeperutil.NewRecordKeeper(storeKey, codec),
}
```

### Record Management

```go
// Add a new record (auto-increments ID)
record := MyRecord{Name: "Example", Value: 100}
id := appKeeper.Add(ctx, record)

// Retrieve a record
var retrievedRecord MyRecord
appKeeper.Get(ctx, id, &retrievedRecord)

// Update a record
updatedRecord := MyRecord{Name: "Updated", Value: 200}
appKeeper.Update(ctx, id, updatedRecord)

// Delete a record
appKeeper.Delete(ctx, id)
```

### String-keyed Storage

```go
// Set with string key
appKeeper.StringSet(ctx, "user:alice", userRecord)

// Get with string key
var user UserRecord
appKeeper.StringGet(ctx, "user:alice", &user)
```

### One-to-Many Associations

```go
// Create association between records
appKeeper.Push(ctx, usersStoreKey, postsStoreKey, userID, postID)

// Iterate through associated records
appKeeper.Map(ctx, postsStoreKey, userID, func(postID uint64) {
    // Process each associated post
})
```

### Iteration

```go
// Iterate through all records
appKeeper.Each(ctx, func(recordBytes []byte) bool {
    var record MyRecord
    appKeeper.codec.MustUnmarshalBinaryLengthPrefixed(recordBytes, &record)
    // Process record
    return true // continue iteration
})
```

## Advanced Usage

### Manual ID Management

```go
// Manually set ID and record
customID := appKeeper.IncrementID(ctx)
appKeeper.Set(ctx, customID, MyRecord{Data: "custom"})
```

### Association Management

```go
// Add multiple associations
appKeeper.Push(ctx, parentStore, childStore, parentID, childID1)
appKeeper.Push(ctx, parentStore, childStore, parentID, childID2)

// Check if association exists
hasAssociation := appKeeper.Has(ctx, parentStore, parentID, childID1)

// Remove association
appKeeper.DeleteAssociation(ctx, parentStore, parentID, childID1)
```

## API Reference

### Core Methods

- `Add(ctx types.Context, record interface{}) uint64` - Add record with auto-increment ID
- `Get(ctx types.Context, id uint64, record interface{})` - Retrieve record by ID
- `Set(ctx types.Context, id uint64, record interface{})` - Set record with specific ID
- `Update(ctx types.Context, id uint64, record interface{})` - Update existing record
- `Delete(ctx types.Context, id uint64)` - Delete record by ID
- `Each(ctx types.Context, callback func([]byte) bool)` - Iterate through all records

### String Key Methods

- `StringSet(ctx types.Context, key string, record interface{})`
- `StringGet(ctx types.Context, key string, record interface{})`
- `StringHas(ctx types.Context, key string) bool`
- `StringDelete(ctx types.Context, key string)`

### Association Methods

- `Push(ctx types.Context, parentStore, childStore types.StoreKey, parentID, childID uint64)`
- `Map(ctx types.Context, childStore types.StoreKey, parentID uint64, callback func(uint64))`
- `Has(ctx types.Context, parentStore types.StoreKey, parentID, childID uint64) bool`
- `DeleteAssociation(ctx types.Context, parentStore types.StoreKey, parentID, childID uint64)`

## License

Apache-2.0 License - see LICENSE file for details.
