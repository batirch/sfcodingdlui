///This code sets OwnerId of the Contact record as Account OwnerId ///
```java

trigger MatchAccOwner on Contact (before insert) {
    for(Contact myCon : Trigger.new){
        if (myCon.AccountId != null){
            List<Account> accs = [Select Id,
                                 	Ownerid
                                 From Account
                                 Where Id = :myCon.AccountId];
/// Another option of code without using  get(0) is LIMIT 1 command at SOQL Query
            myCon.OwnerId = accs.get(0).OwnerId;
        }

    }

}
