# Address Book and Directory

$KANDY$ offers set of APIs to store, manage and search contacts through address books, lists and directories.

## Managing Address Book Contacts

$KANDY$ Mobile SDK provides and use `Contact` model for all of the Address Book operations. A `Contact` object should contain an unique ID and set of attributes. An unique ID can be provided while constructing and object, but by default $KANDY$ Mobile SDK supplies a default random String.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.addressbook.model.Contact;

Contact contactWithSDKProvidedId = new Contact();
Contact contactWithExternallyProvidedId = new Contact("hereIsMyUniqueId");

contactWithSDKProvidedId.getContactId(); // It's some random UUID
contactWithExternallyProvidedId.getContactId(); // It's "hereIsMyUniqueId"
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.addressbook.model.Contact
val contactWithSDKProvidedId = Contact()
val contactWithExternallyProvidedId = Contact("hereIsMyUniqueId")

contactWithSDKProvidedId.contactId // It's some random UUID
contactWithExternallyProvidedId.contactId // It's "hereIsMyUniqueId"
```
<!-- tabs:end -->

A Contact object can contain attributes like primary contact, first name, last name, email address, business phone number, home phone number, mobile number, fax number, pager number and buddy value (All available fields can be found in the `AttributeName` enum). A contact's "primary contact" attribute corresponds to "User Id", which can be used with call, chat and presence services. Each attribute can contain only one value.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.addressbook.model.Contact;

Contact exampleContact = new Contact();
exampleContact.setPrimaryContact("test@test.com");
exampleContact.setFirstName("Test");
exampleContact.setLastName("LastTest");
exampleContact.setEmailAddress("test@test.com");
exampleContact.setBusinessPhoneNumber("455533394");
exampleContact.setHomePhoneNumber("455533394");
exampleContact.setMobilePhoneNumber("455533394");
exampleContact.setBuddy(true);
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.addressbook.model.Contact

val exampleContact = Contact()
exampleContact.primaryContact = "test@test.com"
exampleContact.firstName = "Test"
exampleContact.lastName = "LastTest"
exampleContact.emailAddress = "test@test.com"
exampleContact.businessPhoneNumber = "455533394"
exampleContact.homePhoneNumber = "455533394"
exampleContact.mobilePhoneNumber = "455533394"
exampleContact.isBuddy = true
```
<!-- tabs:end -->

Note that, $KANDY$ automatically appends the domain address (e.g. @test.com) to primary contact attribute when it's not provided while creating or updating a contact.

### Adding a new Contact

$KANDY$ Mobile SDK provides `addContact` ability to add `Contact` objects to user's address book. `addContact` operation requires a new `Contact` object with unique Contact ID that $KANDY$ Mobile SDK provides by default.

<!-- tabs:start -->

#### ** Java Code **

```java
Contact exampleContact = new Contact();
// ... setting Contact's attributes
CPaaS.getAddressBookService().addContact(exampleContact, "default", new AddContactCallback() {
    @Override
    public void onSuccess(Contact contact) {
        // exampleContact successfully added to user's "default" address book and AddContactCallback returns uploaded contact object value.
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val exampleContact = Contact()
// ... setting Contact's attributes
CPaaS.addressBookService.addContact(exampleContact, "default", object:AddContactCallback{
    override fun onSuccess(contact: Contact?) {
        // exampleContact successfully added to user's "default" address book and AddContactCallback returns uploaded contact object value.
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Updating a contact

$KANDY$ Mobile SDK provides `updateContact` ability to update an existing `Contact` on the user's specified address book. A contact's attributes can be updated through each setter methods of the `Contact` object. Update operation only changes provided modifications and giving empty string value can be used as a remove operation.

<!-- tabs:start -->

#### ** Java Code **

```java
Contact existingContact = ExampleContactProvider.getSomeExistingContact();
existingContact.setBuddy(false);
existingContact.setEmailAddress("test2@test.com");
existingContact.setFirstName(""); // removing the first name attribute (if exists) by giving empty string.

CPaaS.getAddressBookService().updateContact(existingContact, "default", new UpdateContactCallback() {
    @Override
    public void onSuccess(Contact contact) {
        // existingContact successfully updated on the user's "default" address book and UpdateContactCallback returns the final state of the contact object.
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val existingContact:Contact = ExampleContactProvider.someExistingContact
existingContact.isBuddy = false
existingContact.emailAddress = "test2@test.com"
existingContact.firstName = "" // removing the first name attribute (if exists) by giving empty string.

CPaaS.addressBookService.updateContact(existingContact, "default", object:UpdateContactCallback{
    override fun onSuccess(contact: Contact?) {
        // existingContact successfully updated on the user's "default" address book and UpdateContactCallback returns the final state of the contact object.
    }

    override fun onFail(error: MobileError?) {
        //Operation failed.
    }

})
```
<!-- tabs:end -->

### Getting all contacts

In order to fetch all contacts from a specific adddress book, $KANDY$ Mobile SDK provides `retrieveContactList` API.

Example usage of the `retrieveContactList` API:

<!-- tabs:start -->

#### ** Java Code **

```java
CPaaS.getAddressBookService().retrieveContactList("default", new RetrieveContactsCallback() {
    @Override
    public void onSuccess(List<Contact> contacts) {
        // List of contacts that available in the "default" address book.

    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
CPaaS.addressBookService.retrieveContactList("default", object:RetrieveContactsCallback{
    override fun onSuccess(contacts: MutableList<Contact>?) {
        // List of contacts that available in the "default" address book.
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Getting a contact

In order to get a single contact from a specific adddress book, $KANDY$ Mobile SDK provides `getContact` API.

Example usage of the `getContact` API:

<!-- tabs:start -->

#### ** Java Code **

```java
String contactId = someContact.getContactId();

CPaaS.getAddressBookService().getContact(contactId, "default", new GetContactCallback() {
    @Override
    public void onSuccess(Contact contact) {
        // The contact object that identified with the specified contactId
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val contactId = someContact.contactId

CPaaS.addressBookService.getContact(contactId, "default", object:GetContactCallback{
    override fun onSuccess(contact: Contact?) {
        // The contact object that identified with the specified contactId
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Deleting a contact

In order to delete a single contact from a specific adddress book, $KANDY$ Mobile SDK provides `deleteContact` API.

Example usage of the `deleteContact` API:

<!-- tabs:start -->

#### ** Java Code **

```java
String contactId = someContactToDelete.getContactId();

CPaaS.getAddressBookService().deleteContact(contactId, "default", new DeleteContactCallback() {
    @Override
    public void onSuccess() {
        // Contact successfully deleted
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val contactId = someContactToDelete.contactId
v
CPaaS.addressBookService.deleteContact(contactId, "default", object:DeleteContactCallback{
    override fun onSuccess() {
        // Contact successfully deleted
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Directly updating or removing contact attributes

$KANDY$ Mobile SDK provides ability to updating or removing a specific contact attribute. Attribute operations can be performed by using `Attribute` and `AttributeName` enum classes.

In order to update an attribute, you should create `Attribute` object with `AttributeName` and `String` pair:

<!-- tabs:start -->

#### ** Java Code **

```java
Attribute updatedAttribute = new Attribute(AttributeName.MOBILE, "+0024949214");
```

#### ** Kotlin Code **

```kotlin
val updatedAttribute = Attribute(AttributeName.MOBILE, "+0024949214")
```
<!-- tabs:end -->

Then you can pass that object to AddressBookService to update that specific attribute:

<!-- tabs:start -->

#### ** Java Code **

```java
CPaaS.getAddressBookService().updateAttribute(updatedAttribute, "default", contactId, new UpdateAttributeCallback() {
    @Override
    public void onSuccess(Attribute attribute) {
        // Returns the updated Attribute object.

    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
CPaaS.addressBookService.updateAttribute(updatedAttribute, "default", contactId, object:UpdateAttributeCallback{
    override fun onSuccess(attribute: Attribute?) {
        // Returns the updated Attribute object.
    }

    override fun onFail(error ": MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

To remove an attribute, you need to provide ID of the attribute (`AttributeName`) to AddressBookService:

<!-- tabs:start -->

#### ** Java Code **

```java
CPaaS.getAddressBookService().removeAttribute(AttributeName.MOBILE, "default", contactId, new RemoveAttributeCallback() {
    @Override
    public void onSuccess() {
        // Attribute successfully removed!

    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
CPaaS.addressBookService.removeAttribute(AttributeName.MOBILE, "default", contactId, object:RemoveAttributeCallback{
    override fun onSuccess() {
        // Attribute successfully removed!
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

Note that, contact attribute updates and removals can also be performed with updateContact APIs.

## Managing Address Book Lists

$KANDY$ Mobile SDK offers ability to manage address book lists.


### Adding a new address book list

$KANDY$ Mobile SDK provides `addAddressBookList` ability to add `AddressBookList` objects to user's address book lists. `addAddressBookList` operation requires a new `AddressBookList` object with unique list ID.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.addressbook.model.AddressBookList;

AddressBookList exampleList = new AddressBookList();
// ... setting Contact's attributes
CPaaS.getAddressBookService().addAddressBookList(exampleList, "default", new AddAddressBookListCallback() {
    @Override
    public void onSuccess(AddressBookList list) {
        // exampleList successfully added to user's "default" address book and AddAddressBookListCallback returns uploaded exampleList object value.
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.addressbook.model.AddressBookList

val exampleList = AddressBookList()
//... setting Contact's attributes
cpaas.addressBookService.addAddressBookList(exampleList, "default", object:AddAddressBookListCallback{
    override fun onSuccess(list: AddressBookList?) {
        // exampleList successfully added to user's "default" address book and AddAddressBookListCallback returns uploaded exampleList object value.
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Adding or updating contact's address book list membership

Address Book list membership of the contact can be managed by `Contact` object's `setAddressBookListIds` and `getAddressBookListIds` methods. A contact can contain multiple address book list membership, but this number can vary between address book providers. Current implementation of the $KANDY$ Address Book service only allows one adress book list membership.

Example usage of adding or updating a contact to list on $KANDY$ Mobile SDK:

<!-- tabs:start -->

#### ** Java Code **

```java
Contact existingContact = ExampleContactProvider.getSomeExistingContact();
existingContact.setAddressBookListIds(Collections.singletonList("exampleList")); // Setting single list membership to contact

CPaaS.getAddressBookService().updateContact(existingContact, "default", new UpdateContactCallback() {
    @Override
    public void onSuccess(Contact contact) {
        // existingContact successfully updated on the user's "default" address book and UpdateContactCallback returns the final state of the contact object.
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val existingContact:Contact = ExampleContactProvider.someExistingContact
existingContact.addressBookListIds = Collections.singletonList("exampleList") //Setting single list membership to contact

CPaaS.addressBookService.updateContact(existingContact, "default", object:UpdateContactCallback{
    override fun onSuccess(contact: Contact?) {
        // existingContact successfully updated on the user's "default" address book and UpdateContactCallback returns the final state of the contact object.
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

Note that, this operation requires using `updateContact` operation, $KANDY$ Mobile SDK does not provide separate address book list API. Also, this operation will create a new address book list if that's not created previosly.

### Updating an address book list

$KANDY$ Mobile SDK provides `updateAddressBookList` ability to update an existing `AddressBookList`s while maintaining its membership information.

<!-- tabs:start -->

#### ** Java Code **

```java
AddressBookList someExitingList = ExampleABListProvider.getSomeExistingList();
String oldListId = someExitingList.getListId();
AddressBookList updatedList = new AddressBookList("New list name");

CPaaS.getAddressBookService().updateAddressBookList(updatedList, oldListId, "default", new UpdateAddressBookListCallback() {
    @Override
    public void onSuccess(AddressBookList list) {
        // Old list successfully updated
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val someExistingList : AddressBookList = ExampleABListProvider.someExistingList
val oldListId = someExistingList.listId
val updatedList = AddressBookList("New list name")

CPaaS.addressBookService.updateAddressBookList(updatedList, oldListId, "default", object:UpdateAddressBookListCallback{
    override fun onSuccess(list: AddressBookList?) {
        // Old list successfully updated
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Getting all address book lists

In order to fetch all address book lists from a specific adddress book, $KANDY$ Mobile SDK provides `retrieveAddressBookLists` API.

<!-- tabs:start -->

#### ** Java Code **

```java
CPaaS.getAddressBookService().retrieveAddressBookLists("default", new RetrieveAddressBookListsCallback() {
    @Override
    public void onSuccess(List<AddressBookList> addressBookLists) {
        // List of address book lists that available in the "default" address book.

    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
CPaaS.addressBookService.retrieveAddressBookLists("default", object:RetrieveAddressBookListsCallback{
    override fun onSuccess(addressBookLists: MutableList<AddressBookList>?) {
        // List of address book lists that available in the "default" address book.
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Getting an address book list

In order to get a address book list from a specific adddress book, $KANDY$ Mobile SDK provides `getAddressBookList` API.

Example usage of the `getAddressBookList` API:

<!-- tabs:start -->

#### ** Java Code **

```java
String listId = someAddressBookList.getListId();

CPaaS.getAddressBookService().getAddressBookList(listId, "default", new GetAddressBookListCallback() {
    @Override
    public void onSuccess(AddressBookList addressBookList) {
        // The addressBookList object that identified with the specified contactId
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val listId = someAddressBookList.listId

CPaaS.addressBookService.getAddressBookList(listId, "default", object:GetAddressBookListCallback{
    override fun onSuccess(addressBookList: AddressBookList?) {
        // The addressBookList object that identified with the specified contactId
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

### Deleting an address book list

In order to delete a address book list, $KANDY$ Mobile SDK provides `deleteAddressBookList` API.

Example usage of the `deleteAddressBookList` API:

<!-- tabs:start -->

#### ** Java Code **

```java
String listId = someAddressBookList.getListId();

CPaaS.getAddressBookService().deleteAddressBookList(listId, "default", new DeleteAddressBookListCallback() {
    @Override
    public void onSuccess() {
        // List successfully deleted
    }

    @Override
    public void onFail(MobileError error) {
        // Operation failed.
    }
});
```

#### ** Kotlin Code **

```kotlin
val listId = someAddressBookList.listId

CPaaS.addressBookService.deleteAddressBookList(listId, "default", object:DeleteAddressBookListCallback{
    override fun onSuccess() {
        // List successfully deleted
    }

    override fun onFail(error: MobileError?) {
        // Operation failed.
    }

})
```
<!-- tabs:end -->

## Directory Search

By using $KANDY$ Mobile SDK, valid contacts in the cloud can be found by performing search operation in directory. This document explains how to use search operation on the directory.

$KANDY$ performs search operation by taking `Search` object as a parameter. `Search` contains:

* `SearchFilter`: Mandatory value that contains `FieldType` and field `value` pair
* `directoryId: `: Specifies which directory will be searched. Default value is "default"
* `orderBy`: `SearchOrder` object that orders results according to the specified type. Default value is `SearchOrder.ASCENDING`.
* `sortBy`: Sorts the result according to the specified `FieldType` value
* `limit`: Maximum number of ascontacts will return from operation

### Filter (`SearchFilter`)

#### Value (Keyword)

$KANDY$ searches keyword parameter in the specified `FieldType`. Keyword supports `String` and it shouldn't be an empty string.

#### Type (`FieldType`)

Field type points to search keyword in which fields of the contacts in the directory. Available types are specified by `FieldType` enum, and available values are:

* name (Searches keyword in both first name and last name fields of contacts)
* firstname (Searches keyword in first name field of contacts)
* lastname (Searches keyword in last name field of contacts)
* username (Searches keyword in username field of contacts)
* phoneNumber (Searches keyword in phone number fields of contacts)

### Order Type

The list of contacts which returned from the search operation will be ordered by the specified order type. Types are `ASCENDING` and `DESCENDING` and specied by `SearchOrder` enum.

### Sorting Rule

Specifies to $KANDY$ how to sort results. Available types are specified by `FieldType` enum:

* name
* firstname
* lastname
* username
* phoneNumber

If Sorting rule is not specified, operation will be performed by using same type used in the `SearchFilter` object when passed as an argument to `Search`.

### Limit

Points how many results should be returned from the operation and takes interger as an argument. `0` shouldn't be used, which will cause failure.

If pagination or similar mechanism will be used which will need to divide results, limit should be set to positive integer larger than `0` and `getNext` method can be used which will defined in the `CPSearchResult` object.

### Directory Identifier

Directory Identifier points to $KANDY$ where search operation will be done. Currently $KANDY$ only supports `default` identifier.

### SearchResult

When directory operation is completed successfuly, it will return `SearchResult` object as an argument. `SearchResult` contains relevant contacts if there is any, and some handy properties.

#### Contacts

Returns relevant contacts searched with the specified parameters as an array. If operation succeeds even no relevant contacts have been found, this array won't be nil.

#### hasNext

This property is only meaningful if `limit` value is defined in the `Search` object which was used in directory search operation. If query result has more contacts than specified `limit`, `hasNext` will return `true`, in all other cases will return `false`. To get next chunk of contacts of the operation `getNext(SearchCallback callback)` can be used.

#### getNext(SearchCallback callback)

Returns next chunk of contacts of the succeded directory search operation. If `hasNext` returns `false`, `getNext(SearchCallback callback)` will be failed and

### Example usages

Example of usage of search:

<!-- tabs:start -->

#### ** Java Code **

```java
private void searchResult(String criteria) {
    showProgressDialog("Search directory for " + criteria);

    Search search = new Search(new SearchFilter(FieldType.NAME, criteria));
    search.sortBy(FieldType.FIRST_NAME);
    search.order(SearchOrder.ASCENDING);
    search.limit(10);
    search.setDirectoryId("default"); // That's the only id currently supported by $KANDY$

    CPaaS.getAddressBookService().search(search, new SearchCallback() {
            @Override
            public void onSuccess(SearchResult result) {
                // obtain SearchResult to get gathered contacts and it may contain more results, use hasNext to check that
            }

            @Override
            public void onFail(MobileError error) {
                // Error occured
            }
        }
    );
}
```

#### ** Kotlin Code **

```kotlin
private fun searchResult(criteria:String){
    showProgressDialog("Search directory for $criteria")

    val search = Search(SearchFilter(FieldType.NAME, criteria))
    search.sortBy(FieldType.FIRST_NAME)
    search.order(SearchOrder.ASCENDING)
    search.limit(10)
    search.directoryId("default") // That's the only id currently supported by $KANDY$

    CPaaS.addressBookService.search(search, object:SearchCallback{
        override fun onSuccess(result: SearchResult?) {
            // obtain SearchResult to get gathered contacts and it may contain more results, use hasNext to check that
        }

        override fun onFail(error: MobileError?) {
            // Error occured
        }
    })
}
```
<!-- tabs:end -->

### Obtaining more results from `SearchResult`:

<!-- tabs:start -->

#### ** Java Code **

```java
private void nextSearchResults(SearchResult searchResult) {
    if (searchResult != null && searchResult.hasNext()) {
        searchResult.getNext(new SearchCallback() {
            @Override
            public void onSuccess(SearchResult result) {
                // Next results are obtained
            }

            @Override
            public void onFail(MobileError error) {
                // Operation failed
            }
        });
    }
}
```

#### ** Kotlin Code **

```kotlin
private fun nextSearchResults(searchResult: SearchResult){
    if(searchResult != null && searchResult.hasNext()){
        searchResult.getNext(object:SearchCallback{
            override fun onSuccess(result: SearchResult?) {
                // Next results are obtained
            }

            override fun onFail(error: MobileError?) {
                // Operation failed
            }

        })
    }
}
```
<!-- tabs:end -->
