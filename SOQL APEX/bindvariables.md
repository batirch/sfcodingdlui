///This code sets OwnerId of the Contact record as Account OwnerId ///
```java

trigger MatchAccOwner on Contact (before insert) {
    for(Contact myCon : Trigger.new){
        if (myCon.AccountId != null){
            List<Account> accs = [Select Id,
                                 	Ownerid
                                 From Account
                                 Where Id = :myCon.AccountId];
            myCon.OwnerId = accs.get(0).OwnerId;
        }

    }

}
