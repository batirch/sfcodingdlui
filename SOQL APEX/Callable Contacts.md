//This trigger finds callable Contacts of Accounts based on Phone Number.

```java
trigger CallableContacts1 on Contact (after update) {
  for(Contact myCon : Trigger.new){
      if(myCon.AccountId != null){
    //Account Info Query
    Contact conAccInfo = [Select account.id, account.Callable_Contacts__c
                              From Contact
                              Where Id = :myCon.Id];
    System.debug(conAccInfo.AccountId + ' account found');
    //List of Callable Contacts
    List<Contact> listOfUpdates = [Select Id
                                  From Contact
                                  Where Phone != NULL
                                  AND AccountId = :conAccInfo.account.id];

    System.debug(listOfUpdates.size() + ' contacts found');   

    // Add number of contacts in Account field
     Account myAcc = [SELECT Callable_Contacts__c,
                       (SELECT id FROM Contacts Where Phone != NULL)
                       FROM Account WHERE id = :conAccInfo.account.id];
     myAcc.Callable_Contacts__c = listOfUpdates.size();
			try {
    		update myAcc;
			} catch (DmlException e) {
    			// Process exception here
			}
      }
  }
}
