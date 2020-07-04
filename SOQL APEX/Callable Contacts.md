//This trigger finds callable Contacts of Accounts based on Phone Number.

```java
trigger CallableContacts1 on Contact (after update) {

  for(Contact myCon : Trigger.new){
    //Account Info Query
    Contact conAccInfo = [Select account.id
                              From Contact
                              Where Id = :myCon.Id];

    //List of Callable Contacts
    List<Contact> listOfUpdates = [Select Id
                                  From Contact
                                  Where Phone != NULL
                                  AND AccountId = :conAccInfo.id];

    System.debug(listOfUpdates.size() + ' contacts found');   

    // Add number of contacts in Account field
    conAccInfo.Account.Callable_Contacts__c = listOfUpdates.size();  
  }

}
