//This code is for DedupLeadTest

```java
@isTest
private Class DedupLeadTest {

    @isTest static void CreateLeadNoDupes(){
     String leadOwner = UserInfo.getUserId();
          //Create required records
          Lead myLead = new Lead();
          myLead.FirstName   = 'Uno';
          myLead.LastName    = 'Bella';
          myLead.Company     = 'MamaSita';
          myLead.Email       = 'unobella@mamasita.com';
          myLead.Description = 'Put you in the mix';
          myLead.OwnerId     = leadOwner;
          insert myLead;
          //Use SOQL to find latest value
          Lead updatedLead = [Select OwnerId, Description
                              From Lead
                              Where Id = :myLead.Id];

          //Assert the results are as expected

          System.assertEquals(leadOwner, updatedLead.OwnerId);
          System.assertEquals(myLead.Description, updatedLead.Description);
    }

    @isTest static void CreateLeadMultipleDupes(){
        //Create required records

    //Create two contacts
    String email = 'unobella@mamasita.com';
    List<Contact> dupeContacts = new List<Contact>();
    Contact firstDupe = new Contact();
    firstdupe.FirstName   = 'Uno';
    firstdupe.LastName    = 'Bella';
    firstdupe.Email       = Email;
    dupeContacts.add(firstDupe);

    Contact secondDupe = new Contact();
    secondDupe.FirstName   = 'Uno';
    secondDupe.LastName    = 'Bella';
    secondDupe.Email       = Email;
    dupeContacts.add(secondDupe);
    insert dupeContacts;

        //Create dupe lead
    Lead myLead = new Lead();
    myLead.FirstName   = 'Uno';
    myLead.LastName    = 'Bella';
    myLead.Company     = 'MamaSita';
    myLead.Description = 'Put you in the mix';
    myLead.Email       = email;
    myLead.OwnerId     = UserInfo.getUserId();
    insert myLead;

        //Use SOQL to find latest value
    Lead updatedLead = [Select OwnerId, Description
                            From Lead
                            Where Id = :myLead.Id];

        //Find Data Quality Group
    Group dataQualityQueue = [Select Id
                              From Group
                              Where DeveloperName = 'Data_Quality'
                              Limit 1];

        //Assert the results are as expected
    System.assertEquals(dataQualityQueue.Id, updatedLead.OwnerId,'Lead Owner is not Data Quality Group');
    System.assert(updatedLead.Description.contains(firstDupe.Id),'First Dupe Id Didnt Work');
    System.assert(updatedLead.Description.contains(secondDupe.Id),'Second Dupe Id Didnt Work');


    }
}
