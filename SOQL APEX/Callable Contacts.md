//This trigger finds callable Contacts of Accounts based on Phone Number.

```java
trigger CallableContacts1 on Contact (after update, after delete) {
	String accUpdate;
    Integer noOfCons;

if (trigger.isUpdate){
 for(Contact myCon : Trigger.new){
   if(myCon.AccountId != null){
 //Account Info Query
 System.debug(myCon.AccountId + ' update contact account found');
 accUpdate = myCon.AccountId;
    }
 }
}

if (trigger.isDelete){
 for(Contact myCon : Trigger.old){
   if(myCon.AccountId != null){
 //Account Info Query
 System.debug(myCon.AccountId + ' update contact account found');
 accUpdate = myCon.AccountId;
		}
 	}
}

//List of Callable Contacts
List<Contact> listOfUpdates = [Select Id
                              From Contact
                              Where Phone != NULL
                              AND AccountId = :accUpdate];

System.debug(listOfUpdates.size() + ' contacts found');
noOfCons = listOfUpdates.size();

// Add number of contacts in Account field
 Account myAcc = [SELECT Callable_Contacts__c,
                   (SELECT id FROM Contacts Where Phone != NULL)
                   FROM Account WHERE id = :accUpdate];
 myAcc.Callable_Contacts__c = noOfCons;
  try {
    update myAcc;
  } catch (DmlException e) {
      // Process exception here
  }
}
