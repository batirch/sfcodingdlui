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
                                        AND id != :opp.Id];
    System.debug(comparableOpps.size() + ' opps found');
  }
      //For each comparable opp created a record

}
