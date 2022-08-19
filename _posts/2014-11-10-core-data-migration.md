---
title: "Core Data Migration"
date: "2014-11-10"
tags: 
  - "core-data"
  - "example-code"
  - "frameworks"
  - "ios"
  - "ios-library"
  - "iphone"
  - "migration"
  - "sqlite"
  - "xcode"
---

## **Core Data**

A framework which manages, where data is stored, how it is stored, data caching, and memory management. It provides APIs to handle our data like insertion, update, deletion and for data validation. Basically these core data APIs takes care of all data management rules of sqlite.

**When did it come?** It was ported to the iPhone from Mac OS X with the 3.0 iPhone SDK release.

### **Why Core Data?**

Why we might want to use Core Data with SQLite for storage over property lists, a custom XML format, or direct SQLite database access? Here we have some reasons:

1. It allows developers to create and use a database, perform queries using SQL-less conditions (without SQLite).
2. We interact with SQLite in Objective-C or in objects way. And we don’t have to worry about connections or managing the database schema. It’s basically a fully object-oriented API, to store data in a database. So we can say, Object oriented database on top of SQL database.
3. The main benefit to this approach is that it reduces the development time and simplifies the process. It can reduce the memory overhead of our app, increase responsiveness, and save us from writing a lot of boilerplate code. Otherwise writing complex SQL queries and handling SQLite operations are very difficult.

### **How does it work?**

There is a tool in Xcode, which we use for creating a visual mapping between the objects (actually NSManagedObject Subclasses) that we are gonna stored in our database, and then Core Data manages all the interaction in between.

Once we have this visual mapping created in Xcode then we can create new objects, put them in the database, query for objects in the database which is having an SQL database behind it. Core Data manages all the communication behind the scenes, all we see is Object Oriented side of it.

## **Core Data Terms**

**[![CoreDataTerms](https://izeeshan.files.wordpress.com/2014/11/coredataterms.png?w=273)](https://izeeshan.files.wordpress.com/2014/11/coredataterms.png)**

### **A Model** (Managed Object Model / xcdatamodeld)

A model that has all tables information. It’s schema or a database schema - a collection of all the tables (Entities, Data Models) that we use in our application. NSManagedObjectModel is a class that contains definitions for each of the table objects (also called “Entities”) that we have in our database. Usually, we use the visual editor (xcdatamodeld) to set up what tables are in the database, what their columns, and how they relate to each other. However, we can do this with code too!

### **A Sore** (Persistent Store) - sqlite file

A file or a database file (with .sqlite extension) stored in our application's document directory.

### **A Coordinator** (Persistent Store Coordinator)

A coordinator, who first associate store (sqlite file) with model, then coordinates (mediate) between store(s) and context(s).

It's a database connection, where we set up the actual names and locations of what databases will be used to store the objects, and any time a managed object context needs to save something it goes through this single coordinator.

### **A Context** (Managed Object Context)

The job of a context is to use coordinator to first retrieve model information, then save our data into store. A context without a coordinator is of no use as it cannot access a model except through a coordinator. Its primary responsibility is to manage a collection of managed objects.

We can think of this as a “scratch pad” for objects that come from the database. It’s also the most important of the three for us, because we’ll be working with this the most. Basically, whenever we need to get objects, insert objects, or delete objects, we call methods on the managed object context (or at least most of the time!)

Only a coordinator can access the model. Not context.

Only a context can access the coordinator. Not Us.

So, We access context.

## **Problem & Solution**

The problem is we can’t add / remove / modify any column in any table at runtime. We can’t alter it by anyway once it's shipped. And to do the same we need to create something called Migration. Or we can say, when we change our data model, then we also need to move the data in existing stores to new version — changing the store format is known as migration.

### **When migration is required?**

The easiest answer to this common question is “when we need to make changes to the data model.” When the Managed Object Model (mom/xcdatamodel) does not match, a migration is REQUIRED (an instance of NSMigrationManager).

### **How does it work?** 

The migration process updates data created with a previous version of the Data Model to match the current Data Model. Before going into migration process, lets see how core data initialisation works.

### **Initializing Core Data**

We initialize our Core Data stack by calling Context (NSManagedObjectContext), and then context initiate the rest of the work.

1. Initialises model with momd (compiled xcdatamodeld).
2. Initialises persistent store coordinator with model.
3. Add a store to persistent store coordinator. (Here Core Data checks for versioning.)
4. Allocate the context, then sets the persistent store coordinator to it.

When we encounter these steps, Core Data does a few things in-between just prior to adding the store to the coordinator.

1. Core Data analyzes the store’s model version the one which we are passing to add into coordinator.
2. It compares this version to the coordinator’s configured data model if there is already one configured earlier.
3. If the store’s model version and the coordinator’s model version don’t match, then Core Data will perform a migration, when enabled.
4. If migrations aren’t enabled, and the store is incompatible with the model, Core Data will simply not attach the store to the coordinator and specify an error with an appropriate reason code.

### **The Migration Process**

To start the migration process, Core Data needs the original data model (ver 1) and the destination model (ver 2). It uses these two versions to load or create a mapping model for the migration. Then It uses mapping model to convert data in ver 1 (in the original store) to data that it can store to ver 2 (in the new or destination store). Once Core Data determines the mapping model, the migration process can start in earnest.

During migration, Core Data creates two stacks, one for the source store and one for the destination store. Core Data then fetches objects from the source stack and inserts the appropriate corresponding objects into the destination stack.

Basically, Migrations happen in three steps: First, Core Data copies over all the objects from one data store to the next. Next, Core Data connects and relates all the objects according to the relationship mapping. Enforce any data validation in the destination model. Core Data disables destination model validation during the data copy.

### **Changes that do not require Migration:**

Basically anything that doesn’t change the underlying SQLite backing store, including:

1. Changing the name of an NSManagedObject subclass
2. Adding or removing a transient property
3. Making changes to the user info dictionary
4. Changing validation rules.

### **Requirements for the Migration Process:** 

Migration of a persistent store is performed by an instance of NSMigrationManager. To migrate a store, the migration manager requires several things:

1. The managed object model for the destination store. (This is the persistent store coordinator’s model.)
2. A managed object model that it can use to open the existing store.
3. Typically, a mapping model that defines a transformation from the source (the store’s) model to the destination model.

You don’t need a mapping model if you’re able to use lightweight migration.

## **Core Data Migration Ways**

### **Primary ways to create a migration**:

- Automatic (aka lightweight),
- Manual, and
- Custom code.

But in reality, the migration process may involve one or more of these techniques.

The golden rule when it comes to Core Data migrations is, choose lightweight whenever possible. Manual migrations and migrations requiring custom code are a magnitude more complex and memory intensive.

##### **NOTE** - Core Data does not perform migration linearly. It’ll update the app with whatever available like ver 1 to ver 3.

### **Types of Migrations:**

These are not official categories of migration.

1. Lightweight migrations
2. Manual migrations
3. Custom manual migrations
4. Fully manual migrations

**Lightweight migrations:** A lightweight migration is Apple’s term for the migration with the least amount of work involved on your part. If you just make simple changes to your model (such as adding a new attribute to an entity), Core Data can perform automatic data migration, referred to as lightweight migration.

Lightweight migration is fundamentally the same as ordinary migration, except that instead of you providing a mapping model, Core Data infers one from differences between the source and destination managed object models.

**Manual migrations:** Manual migrations involve a little more work on our part. We need to specify how to map the old set of data onto the new set, but we get the benefit of an explicit mapping model file to configure. Setting up a mapping model in Xcode is much like setting up a data model, with similar GUI tools and some automation.

**Custom manual migrations:** If the transformation (add/remove properties or entities to your existing model) is more complex, however, you might need to create a subclass of NSEntityMigrationPolicy to perform the transformation.

This is level 3 of the migration complexity index. You still use a mapping model, but add to that custom code with the ability to also specify custom transformation logic on data. In this case, custom entity transformation logic involves creating an NSEntityMigrationPolicy subclass and performing custom transformations there.

**Fully manual migrations:** Fully manual migrations are for those times when even specifying custom transformation logic isn’t enough to fully migrate data from one model version to another. In this case, custom version detection logic and custom handling of the migration process are necessary.

## **Lightweight Migration**

### **What lightweight migration can do.**

Lightweight migrations can handle the following changes:

1. Adding or removing an entity, attribute, or relationship
2. Making an attribute non-optional with a default value
3. Making a non-optional attribute optional
4. Renaming an entity or attribute using a renaming identifier

### **Enable Lightweight Migrations** -

You need to pass a dictionary containing two keys to the **options** parameter of the method that adds the persistent store to the coordinator. These keys are:

- NSMigratePersistentStoresAutomaticallyOption – attempt to automatically migrate versioned stores
- NSInferMappingModelAutomaticallyOption – attempt to create the mapping model automatically

### **Steps** -

1. Open your .xcdatamodeld file
2. Click on Editor in Menu Bar
3. Select Add Model Version….
4. Add a new version of your model (the new group of datamodels added)
5. Select the main file, open File Inspector (Right-Hand Panel)
6. And under Model Version core data model select your new version of data model for current data model from drop down.
7. That's not all, We need to perform so-called "light migration” with code.
8. Go to your Core Data stack manager and find where the persistentStoreCoordinator is being created.
9. Find this line if (!\[\_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error\])
10. Replace nil options value with @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES} (actually provided in the commented code in that method)

Here you go, have fun!

## **Manual Migration**

If you want to make a change to the new model that’s not supported by lightweight migrations, you need to create a mapping model. A mapping model needs a source and a destination data model. When you add a new version of your data model, you are asked to select the model on which it should be based.

### **Adding a version to data models:**

1. Open your .xcdatamodeld file
2. Click on Editor in Menu Bar
3. Select Add Model Version….
4. Add a new version of your model (the new group of datamodels added)
5. Select the main file, open File Inspector (Right-Hand Panel)
6. And under Model Version core data model select your new version of data model for current data model from drop down.
7. Make the desired changes in Newly created Data Models (xcdatamodel file).

### **Adding a mapping model for versions:**

1. Select New, File... from File menu on menu bar.
2. Select Core Data from left panel and Mapping Model from right options then click Next.
3. Select Source Data Model (Version 1) from list of xcdatamodel files then click Next.
4. Choose Target Data Model (Destination or Next Version) from same list of xcdatamodel files then click Next.

### **Now, let’s perform Manual Migration with Code**

1. Go to your Core Data stack manager and find where the persistentStoreCoordinator is being created.
2. Find this line if (!\[\_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error\])
3. Replace nil options value with @{NSInferMappingModelAutomaticallyOption:@YES}.

**Is migration needed?** Migration is needed if destinationModel is NOT compatible with store meta data. Let’s check the same.

- Get metadata for source store from its url with given type (NSSQLiteStoreType).
- Get the destination managed object model from xcdatamodeld url.
- Call 'compatible with store meta data’ function on destination managed object model. It returns bool value.
- We need migration if above function returns false.

\- (BOOL)isMigrationNeeded {
 NSError \*error = nil;   NSDictionary \*sourceMetadata = \[NSPersistentStoreCoordinator metadataForPersistentStoreOfType:NSSQLiteStoreType URL:\[self sourceStoreURL\] error:&error\];
 BOOL isMigrationNeeded = NO;
 if (sourceMetadata != nil) {
 NSManagedObjectModel \*destinationModel = \[self managedObjectModel\];
// Migration is needed if destinationModel is NOT compatible 
      isMigrationNeeded = !\[destinationModel isConfiguration:nil compatibleWithStoreMetadata:sourceMetadata\]; 
   }   
 NSLog(@"isMigrationNeeded: %@", (isMigrationNeeded == YES) ? @"YES" : @"NO"); 
 return isMigrationNeeded;
}

### **The Migration Method**

1. Get metadata for source store from its url with given type (NSSQLiteStoreType).
2. Create managed object model from source meta data.
3. Get the destination managed object model from xcdatamodeld url.
4. Get Mapping Model from bundle with source and destination managed object model.
5. Create a destination store url (a different sqlite file path).
6. Initialise a Migration Manager with source and destination managed object model.
7. Now, NSMigrationManager can infer the mapping model between two models. So, call a migrate store function on manager with mapping model from source store url to destination store url.
8. Remove old store file(s) (shm or wal) and then copy the destination store url to old source store location.

\- (BOOL)migrate {

    NSURL \*sourceUrl = \[self sourceStoreURL\];
 // 1. Get metadata for source store from its URL with given type.
    NSError \*error = nil;
 NSDictionary \*sourceMetadata = \[NSPersistentStoreCoordinator metadataForPersistentStoreOfType:NSSQLiteStoreType URL:sourceUrl error:&error\];     if (sourceMetadata == NO) {
 NSLog(@"FAILED to create source meta data");         return NO;
    }

 // 2. Create model from source store meta deta.
 NSManagedObjectModel \*sourceModel = \[NSManagedObjectModel mergedModelFromBundles:@\[\[NSBundle mainBundle\]\] forStoreMetadata:sourceMetadata\];
    if (sourceModel == nil) {
 NSLog(@"FAILED to create source model, something wrong with source xcdatamodel.");         return NO;
    }

    // 3. Get the destination managed object model from xcdatamodeld url.
    NSManagedObjectModel \*destinationModel = \[self managedObjectModel\];

    // 4. Get Mapping model from bundle with source and destination managed object model.
 NSMappingModel \*mappingModel = \[NSMappingModel mappingModelFromBundles:@\[\[NSBundle mainBundle\]\] forSourceModel:sourceModel destinationModel:destinationModel\];

 // 5. Create the destination store url (a different sqlite/database file path)
 NSString \*fileName = @"ZKManualMigration\_V2.sqlite";  NSURL \*destinationStoreURL =  \[\[\[\[NSFileManager defaultManager\] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask\] lastObject\] URLByAppendingPathComponent:fileName\];  // 6. Migrate from source to latest matched destination model,
    NSMigrationManager \*manager = \[\[NSMigrationManager alloc\] initWithSourceModel:sourceModel destinationModel:destinationModel\];
    BOOL didMigrate = \[manager migrateStoreFromURL:sourceUrl                                              type:NSSQLiteStoreType                                                    options:nil                                 withMappingModel:mappingModel                                             toDestinationURL:destinationStoreURL                                      destinationType:NSSQLiteStoreType                                         destinationOptions:nil                                                    error:&error\];
    if (!didMigrate) {
        return NO;
    }
    NSLog(@"Migrating from source: %@ ===To=== %@", sourceUrl.path, destinationStoreURL.path);
 // 7. Delete old sqlite file
    NSError \*err = nil;
 NSFileManager \*fm = \[NSFileManager defaultManager\];     if (!\[fm removeItemAtURL:sourceUrl error:&err\]) {
 NSLog(@"File delete failed.");         return NO;
    }
    NSString \*str1 = \[NSString stringWithFormat:@"%@-shm",sourceUrl.path\];
 \[fm removeItemAtURL:\[NSURL fileURLWithPath:str1\] error:&err\];     str1 = \[NSString stringWithFormat:@"%@-wal",sourceUrl.path\];
 \[fm removeItemAtURL:\[NSURL fileURLWithPath:str1\] error:&err\];  // Copy into new location
    if (!\[fm moveItemAtURL:destinationStoreURL toURL:sourceUrl error:&err\]) {
 NSLog(@"File move failed.");         return NO;
    }

 NSLog(@"Migration successful");     return didMigrate;
}

#####  [Sample project](https://github.com/zeeshankhan/ZKManualMigration "Sample project") on Manual Migration.

Thanks for reading, cheers... 👍
