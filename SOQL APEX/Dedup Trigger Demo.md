//This trigger finds deduplicate Leads in the org before creation.

```java

trigger DedupLeads on Lead (before insert) {
      // Get dataQuality team ID. It is as a list in case of NULL error
      List<Group> dataQuality = [Select Id
                           From Group
                           Where DeveloperName = 'Data_Quality'
                           Limit 1];



    for (Lead myLead : Trigger.new) {
        if (myLead.Email != null){
            String firstNameMatch;
            if(myLead.FirstName!=null){
                firstNameMatch = myLead.FirstName.subString(0,1) + '%';
            }
            String companyMatch = '%' + myLead.Company + '%';

            List<Contact> myCon = [Select FirstName, Id, LastName,
                                   Account.Name
                                  FROM Contact
                                  Where (Email!= null
                                  AND Email = :myLead.Email)
                                  OR (FirstName != null
                                  AND FirstName LIKE :firstNameMatch
                                  AND LastName = :myLead.LastName
                                  AND Account.Name LIKE :companyMatch)];
        System.debug(myCon.size() + ' contacts found');



            if (!myCon.isEmpty()){
				//Assign Data Quality team on Lead
				myLead.OwnerId = dataQuality.get(0).id;

                // Add dupe contact ids in Lead Description
                String dupeContactsMsg = 'Duplicate contact(s) found: \n';
                for (Contact matchingCon : myCon){
                    dupeContactsMsg +=matchingCon.FirstName+ ' ' + ' ('
                       				+ matchingCon.Id + ')';
                }
                myLead.Description = dupeContactsMsg + '\n' + myLead.Description;

        }  
        }

    }
}
