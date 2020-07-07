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
}
    @isTest static void CreateLeadMultipleDupes(){
        //test code
    }
