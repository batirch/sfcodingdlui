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

            List<Contact> myCon = [Select FirstName, Id
                                  FROM Contact
                                  Where Email = :myLead.Email];
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
