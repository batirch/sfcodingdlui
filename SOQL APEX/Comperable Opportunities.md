//This trigger finds similar Opportunities. And links them in comparables object in the org before creation.

```java

trigger orgCompareOpp on Opportunity (after insert) {

  for(Opportunity opp : Trigger.new){
    //Account Info Query
    Opportunity oppAccInfo = [Select Id, Account.Industry
                              From Opportunity
                              Where Id = :opp.Id];
    //Min and Max Amount Variables
    Decimal minAmount = opp.Amount *0.9;
    Decimal maxAmount = opp.Amount *1.1;
    //Search for comparable Opportunities
    List<Opportunity> comparableOpps = [Select Id
                                        From Opportunity
                                        Where Amount > :minAmount
                                        AND Amount < :maxAmount
                                        AND Account.Industry = :oppAccInfo.Account.Industry
                                        AND StageName = 'Closed Won'
                                        AND CloseDate >= Last_N_Days:365
                                        AND id != :opp.Id
                                        AND Owner.Position_start_date__c >= Last_N_Days:365];

    System.debug(comparableOpps.size() + ' opps found');

    // I misunderstood requirement. Was trying to set ownership for
    //junction object. ignore the line below
    /*
    List<User> assignUsers = [Select Id
                              From User
                              WHERE Position_start_date__c >= Last_N_Days:365];
    */
      //For each comparable opp created a record
    List<Comparable__c> junctionObjsInsert = new List<Comparable__c>();
    for (Opportunity comp : comparableOpps) {
    	Comparable__c junctionObjs  = new Comparable__c();
    	junctionObjs.Base_opportunity__c = opp.Id;
    	junctionObjs.Comparable_Opportunity__c = comp.Id;
    /*  junctionObjs.OwnerId__c = assignUsers.get(0).Id; */
    	junctionObjsInsert.add(junctionObjs);
  }
  insert junctionObjsInsert;
  }
}
