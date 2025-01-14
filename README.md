# todo_app

A new Flutter project.

## Getting Started

This project is a starting point for a Flutter application.

A few resources to get you started if this is your first Flutter project:

- [Lab: Write your first Flutter app](https://docs.flutter.dev/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://docs.flutter.dev/cookbook)

For help getting started with Flutter development, view the
[online documentation](https://docs.flutter.dev/), which offers tutorials,
samples, guidance on mobile development, and a full API reference.

# to_do

1. The first two commits are a for the the Go_Route setup and the adaptive screen layout.

2. Commit 4-6 are related to mock data and domain folder todo_repository_mock.dart was created under the data folder, entities, failueres repositories and use_cases. In the core folder we created the the core set of use cases under the core main folder.1_Domain and 0_Data.

3. In commit 7 we built the bloc via a cubit and created the view_states. The overview has been updated. The main.dart has also been updated to access the repository.

Packages and where to install them
https://pub.dev/packages/cubit -- deperecated do not install
https://pub.dev/packages/either_dart
https://pub.dev/packages/uuid/install
https://pub.dev/packages/flutter_adaptive_scaffold
https://pub.dev/packages/go_router
https://pub.dev/packages/image_picker

https://m3.material.io/foundations/layout/understanding-layout/overview
https://m2.material.io/design/layout/responsive-layout-grid.html#grid-customization

4. 1. Commit 8 in the todo_repository_mock.dart
      The ToDoRepositoryMock class is a mock implementation of the ToDoRepository interface, designed for testing purposes. Here's a summary of its structure and functionality:

Attributes
toDoEntries:

A list of 100 mock ToDoEntry objects.
Each entry has a unique EntryId, a description, and a default isDone state of false.
toDoCollections:

A list of 10 mock ToDoCollection objects.
Each collection has a unique CollectionId, a title, and a ToDoColor.
Methods
readToDoCollections():

Returns the list of ToDoCollection objects wrapped in a Right (success).
Simulates a 200ms delay for asynchronous behavior.
Returns a Left (failure) with a ServerFailure if an exception occurs.
readToDoEntry(CollectionId collectionId, EntryId entryId):

Fetches a specific ToDoEntry based on its EntryId.
Simulates a 200ms delay for asynchronous behavior.
Returns a Left (failure) with a ServerFailure if an exception occurs.
readToDoEntryIds(CollectionId collectionId):

Retrieves a list of EntryIds associated with a CollectionId.
Assumes that each collection contains 10 entries.
Simulates a 300ms delay for asynchronous behavior.
Returns a Left (failure) with a ServerFailure if an exception occurs.
Purpose
The mock repository provides predefined, predictable data for testing the application logic.
It simulates network or database delays and failure scenarios without relying on actual data sources.

4. 2. 1*domain > repositories > todo * repository.dart
      The ToDoRepository interface defines the contract for a repository that handles operations related to ToDo collections and ToDo entries. Here's a breakdown of its structure and functionality:

Methods in ToDoRepository
readToDoCollections()

Purpose: Fetches a list of ToDoCollection objects.
Return Type:
Either Failure, List ToDoCollection> :
Returns a Right containing the list of collections on success.
Returns a Left with a Failure object on failure.
Use Case: This is used to load all the available ToDo collections in the application.
readToDoEntry(CollectionId collectionId, EntryId entryId)

Parameters:
collectionId: Identifies the collection containing the entry.
entryId: Identifies the specific entry to fetch.
Purpose: Fetches a specific ToDoEntry from a given collection.
Return Type:
Either Failure, ToDoEntry :
Returns a Right containing the ToDoEntry on success.
Returns a Left with a Failure object on failure.
Use Case: Used to view or process details of a specific ToDo entry.
readToDoEntryIds(CollectionId collectionId)

Parameters:
collectionId: Identifies the collection for which entry IDs are fetched.
Purpose: Fetches a list of EntryIds associated with a specific collection.
Return Type:
// Either Failure, List EntryId>>:
Returns a Right containing the list of entry IDs on success.
Returns a Left with a Failure object on failure.
Use Case: Used to fetch all entry IDs in a specific collection, possibly for further processing or navigation.
General Characteristics
Abstract Nature: The repository provides an abstraction, meaning its implementation could vary (e.g., local database, remote API, or mock data).
Error Handling: By using Either, the interface ensures that error cases are explicitly handled with Failure objects.
Scalability: Additional methods can be added to the repository as needed, such as adding, updating, or deleting entries or collections.
This design follows clean architecture principles, where the repository serves as a bridge between the domain layer and data sources. It keeps the domain layer independent of specific implementations.

core > use_case.dart
This code defines a clean architecture pattern for use cases in the application, with a generic and reusable design. Here's a breakdown of the components:

Classes and Their Responsibilities

1. UseCaseType, Params (Abstract Base Class)
   Purpose:
   A generic base class for defining use cases.
   Each use case represents a single piece of business logic.
   Type Parameters:
   Type: The return type of the use case.
   Params: The input parameters required by the use case.
   Method:
   call(Params params): Executes the use case logic with the given parameters and returns a Future of an Either:
   RightFailure, Type on success.
   Left Failure, Type on failure.
2. Params (Abstract Class)
   Purpose: A base class for passing parameters to a use case.
   Extends: Equatable, to ensure value comparison between parameter objects.
   Usage: Specific use case parameters extend this class to define required fields.
3. NoParams
   Purpose:
   Represents a use case with no parameters.
   Used for actions where no additional data is needed (e.g., fetching all items).
   Overrides:
   props: Returns an empty list since no fields exist.
4. ToDoEntryIdsParam
   Purpose:
   Represents parameters required to fetch entry IDs for a specific ToDo collection and entry.
   Fields:
   entryId: The ID of the entry.
   collectionId: The ID of the collection.
   Overrides:
   props: Returns a list containing entryId and collectionId, ensuring value comparison.
5. CollectionIdParam
   Purpose:
   Represents parameters required for operations specific to a collection.
   Fields:
   collectionId: The ID of the collection.
   Overrides:
   props: Returns a list containing collectionId for value comparison.
   How This Fits Together
   UseCase Implementation:

Concrete use cases implement the UseCase class and define specific logic in the call method.

Example:

dart
Copy code
class FetchToDoCollectionsUseCase extends UseCaseListoDoCollection>, NoParams> {
final ToDoRepository repository;

FetchToDoCollectionsUseCase(this.repository);

@override
FutureEitherFailure, ListToDoCollection>>> call(NoParams params) {
return repository.readToDoCollections();
}
}
Params Usage:

Parameters specific to the use case are passed as instances of classes extending Params.

Example:

dart
Copy code
class FetchToDoEntryIdsUseCase extends UseCaseListEntryId, CollectionIdParam> {
final ToDoRepository repository;

FetchToDoEntryIdsUseCase(this.repository);

@override
FutureEitherFailure, ListEntryId call(CollectionIdParam params) {
return repository.readToDoEntryIds(params.collectionId);
}
}
Calling the Use Case:

Use cases are invoked by passing the appropriate Params or NoParams.

Example:

dart
Copy code
final useCase = FetchToDoEntryIdsUseCase(repository);
final result = await useCase(CollectionIdParam(collectionId: someCollectionId));
Advantages of This Design
Reusability: Generic UseCase and Params classes allow you to standardize logic across different use cases.
Testability: Use cases are independent, making them easy to unit test.
Separation of Concerns: Each use case encapsulates a single piece of business logic, improving code maintainability.
Consistency: By following a standardized pattern, the codebase remains consistent and easier to scale.
Let me know if you'd like a specific example or further elaboration!

The LoadToDoEntryIdsForCollection class is a concrete implementation of the UseCase interface. It encapsulates the business logic for loading entry IDs for a specific ToDo collection. Here's a detailed breakdown:

Class Purpose
The LoadToDoEntryIdsForCollection class defines a use case to:

Retrieve a list of EntryId objects associated with a specific CollectionId.
Handle potential failures during the data retrieval process.
Isolate and standardize the application logic for this specific operation.

he ToDoEntry class represents an individual task or entry in a ToDo application. Here's a detailed breakdown of its design and functionality:

Class Overview
Attributes
id:

Type: EntryId
A unique identifier for the ToDo entry, encapsulated in the EntryId value object for better type safety and immutability.
description:

Type: String
A textual description of the ToDo entry (e.g., "Buy groceries").
isDone:

Type: bool
Represents the completion status of the entry:
true if the task is completed.
false otherwise.
