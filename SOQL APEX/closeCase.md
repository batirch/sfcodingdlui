```java

trigger closeCase on Case (after insert) {
    List<Case> toUpdate = new List<Case>();
    for (Case myCase : Trigger.new){

        //Get related Info
        Case getConInfo = [Select Id, AccountId, ContactId, Status
                          From Case
                          Where Id = :myCase.Id];

        //List other cases on same contact created today
        List<Case> findConCases = [Select Id
                               From Case
                               Where ContactId = :getConInfo.ContactId
                               AND CreatedDate = Today];

        System.debug(findConCases.size() + 'contact cases found');

        //List other cases on same account created today
        List<Case> findAccCases = [Select Id
                               From Case
                               Where AccountId = :getConInfo.AccountId
                               AND CreatedDate = Today];

        System.debug(findAccCases.size() + 'account cases found');

        if (findconCases.size() > 2){         
       for (Case closeCase : Trigger.new){
            Case newCase = new Case();
            newCase.Status = 'Closed';
            toUpdate.add(newCase);

        }

    }


  }
      insert toUpdate;    

}
