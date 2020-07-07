//This code is for all test classes

```java
@isTest
private Class comparableOppsTest {
    @isTest static void noCompOpp(){
          //create records
    //create Account
    Account myAcc = new Account();
    myAcc.Industry = 'Music Recording';
    myAcc.Name = 'Shazom';
    insert myAcc;

    //create Opportunity
    Opportunity myOpp = new Opportunity();
    myOpp.Name = 'Yardi';
    myOpp.Amount = 750000;
    myOpp.AccountId = myAcc.Id;
    myOpp.StageName = 'Closed Won';
    myOpp.CloseDate = Date.today();
    insert myOpp;

    //Query Comparable__c object
    List <Comparable__c> comparables = [Select Id
                                  From Comparable__c
                                  Where Base_opportunity__r.Id = :myOpp.Id];

    //assert
    System.assertEquals(0, comparables.size(), 'Nope. They arent Equal.');


    }
    @isTest static void multipleCompOpp(){
        //test code
    }
}
