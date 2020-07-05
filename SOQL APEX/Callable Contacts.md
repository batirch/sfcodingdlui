//This trigger finds callable Contacts of Accounts based on Phone Number.

```java
trigger CallableContacts1 on Contact (after update) {

  for(Contact myCon : Trigger.new){
    //Account Info Query
    Contact conAccInfo = [Select account.id, account.Callable_Contacts__c
                              From Contact
                              Where Id = :myCon.Id];

    //List of Callable Contacts
    List<Contact> listOfUpdates = [Select Id
                                  From Contact
                                  Where Phone != NULL
                                  AND AccountId = :conAccInfo.account.id];

    System.debug(listOfUpdates.size() + ' contacts found');   
}
    // Add number of contacts in Account field
  List<Account> accUpdateList = new List<Account>();
    For(Account acc : [SELECT Callable_Contacts__c,(SELECT id FROM Contacts Where Phone != NULL) FROM Account WHERE id =:conAccInfo.account.id]){
        acc.Callable_Contacts__c = acc.Contacts.size();
        accUpdateList.add(acc);
  }
}
