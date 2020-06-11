# Presence

Presence publishing and watching are managed by the Presence Service which can be called from the `CPaaS` instance. In order to receive and send events, the `CPaaS` instance should be connected first. To see how to connect and set configurations, check **Login** and **Configurations** sections.

### Initialize the Presence Service

In order to use the Presence service, the service provider object must be properly initialized. When properly initialized, the application will be registered to receive presence notifications from the server.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.CPaaS;
import com.rbbn.cpaas.mobile.presence.api.PresenceService;

// build up the list of services for notification subscriptions
List<ServiceInfo> services = new ArrayList<>();
services.add(new ServiceInfo(ServiceType.PRESENCE, true));

CPaaS cpaas = new CPaaS(services);

PresenceService presenceService = cpaas.getPresenceService();
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.CPaaS
import com.rbbn.cpaas.mobile.presence.api.PresenceService

// build up the list of services for notification subscriptions
val services = mutableListOf<ServiceInfo>()
services.add(ServiceInfo(ServiceType.PRESENCE,true))

val cpaas = CPaaS(services)

val presenceService:PresenceService = cpaas.presenceService
```
<!-- tabs:end -->

### Implement and Set Presence Listener

In order for the Mobile SDK to notify the application of notifications received from the server, a presence listener must be set.

<!-- tabs:start -->

#### ** Java Code **

```java
presenceService.setPresenceListener(this);

@Override
public void presenceNotification(String userId, PresenceActivity presenceActivity) {
	for (PresenceList presenceList : presenceLists) {
		List<Presentity> presentities = presenceList.getPresentities();
		if (presentities != null) {
			for (Presentity presentity : presentities) {
				if (presentity.getUserId().equals(userId)) {
					// Replace the current PresenceActivity with the new one
					presentity.setActivity(presenceActivity);
					adapter.update();
					return;
				}
			}
		}
	}
}

@Override
public void presenceListNotification(String listId, PresenceList presenceList) {
	Log.d(TAG, "Presence list notification for " + listId);
}
```

#### ** Kotlin Code **

```kotlin
presenceService.setPresenceListener(this)

override fun presenceNotification(userId: String?, presenceActivity: PresenceActivity?) {
    for(presenceList:PresenceList in presenceLists) {
        var presentities = mutableListOf<Presentity>()
        presentities = presenceList.presentities
        if(presentities != null){
            for (presentity: Presentity in presentities) {
                if (presentity.userId.equals(userId)) {
                    // Replace the current PresenceActivity with the new one
                    presentity.activity = presenceActivity
                    adapter.update()
                    return
                }
            }
        }
    }
}

override fun presenceListNotification(listId: String?, presenceList: PresenceList?) {
    Log.d(TAG, "Presence list notification for $listId")
}
```
<!-- tabs:end -->

## Publishing Presence
An application publishes the status of a user by creating and updating a PresenceSource.

### Publish Presence Source

Publish the presence activity of the local user by creating new PresenceSource or updating an existing PresenceSource.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.utilities.services.PresenceEnums;


presenceService.createPresenceSource(86400, new FetchPresenceSourceCallback() {
	@Override
	public void onSuccess(PresenceSource presenceSource) {
		updateMyPresence(presenceSource);
	}

	@Override
	public void onFail(MobileError error) {
		Log.d(TAG, error.getErrorMessage());
		presenceItemImageView.setImageResource(android.R.drawable.presence_offline);
		presenceStatusTextView.setText("Offline");

	}
});

void updateMyPresence(PresenceSource presenceSource) {
	if (presenceSource == null) {
		createPresenceSource();
		return;
	}

	String sourceID = myPresenceSource.getSourceId();
	int duration = 86400;
	PresenceEnums activity = (PresenceEnums) spinner.getSelectedItem();
	String status = (String) availabilitySpinner.getSelectedItem();
	String other = otherMessageEditText.getText().toString();

	presenceService.updatePresenceSource(sourceID, duration, activity, status, other,
		new FetchPresenceSourceCallback() {
			@Override
			public void onSuccess(PresenceSource presenceSource) {
				updateMyPresence(presenceSource);
			}

			@Override
			public void onFail(MobileError error) {
			}
		}
	);
}
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.utilities.services.PresenceEnums

presenceService.createPresenceSource(86400,object:FetchPresenceSourceCallback{
               override fun onSuccess(presenceSource: PresenceSource?) {
                   updateMyPresence(presenceSource)
               }

               override fun onFail(error: MobileError?) {
                   Log.d(TAG, error?.errorMessage)
                   presenceItemImageView.setImageResource(android.R.drawable.presence_offline)
                   presenceStatusTextView.setText("Offline")
               }
 })
 
 fun updateMyPresence(presenceSource:PresenceSource?){
     if(presenceSource == null){
         createPresenceSource()
         return
     }
     val sourceID = presenceSource.sourceId
     val duration = 86400
     val activity:PresenceEnums = spinner.selectedItem as PresenceEnums
     val status = availabilitySpinner.selectedItem as String
     val other = otherMessageEditText.text.toString()

     presenceService.updatePresenceSource(sourceID, duration, activity, status, other , object:FetchPresenceSourceCallback{
         override fun onSuccess(presenceSource: PresenceSource?) {
             updateMyPresence(presenceSource)
         }

         override fun onFail(error: MobileError?) {

         }

     })

 }
```
<!-- tabs:end -->

## Watching User Presence
An application watches the presence status of other users by creating a presentity list and subscribing for notifications concerning changes to that users in that list. An application may modify the membership of the presentity watch list after creation.

### Watch Presentity List

Watch the status of users by creating a PresentityList with the contacts to watch. Then subscribe for notifications about changes to the PresentityList.

<!-- tabs:start -->

#### ** Java Code **

```java
presenceService.createPresenceList(listID, new FetchPresenceListCallback() {
	@Override
	public void onSuccess(PresenceList presenceList) {
		presenceLists.add(presenceList);
		adapter.setPresenceLists(presenceLists);
		subscribeForPresenceUpdates(presenceList);
	}

	@Override
	public void onFail(MobileError error) {
	}
});
```

#### ** Kotlin Code **

```kotlin
presenceService.createPresenceList(listID, object:FetchPresenceListCallback{
    override fun onSuccess(presenceList: PresenceList?) {
        presenceLists.add(presenceList)
        adapter.setPresenceLists(presenceLists)
        subscribeForPresenceUpdates(presenceList)
    }
 
    override fun onFail(error: MobileError?) {
        
    }

})
```
<!-- tabs:end -->

### Add User to Presentity List

An application may choose add contacts to an existing PresentityList. No re-subscription to the list is necessary.

<!-- tabs:start -->

#### ** Java Code **

```java
presenceService.addPresentityToList(presenceListKey, presentityID, new AddPresentityToListCallback() {
	@Override
	public void onSuccess(Presentity presentity) {
		presenceList.getPresentities().add(presentity);
		adapter.update();
	}

	@Override
	public void onFail(MobileError error) {
	}
});
```

#### ** Kotlin Code **

```kotlin
presenceService.addPresentityToList(presenceListKey,presentityID, object:AddPresentityToListCallback{
    override fun onSuccess(presentity: Presentity?) {
        presenceList.getPresentities().add(presentity)
        adapter.update()
    }

    override fun onFail(error: MobileError?) {
        
    }

})
```
<!-- tabs:end -->

### Remove User from Presentity List

An application may choose remove contacts from a PresentityList.

<!-- tabs:start -->

#### ** Java Code **

```java
presenceService.deletePresentityFromList(presenceListKey, presentityId, new PresenceCallback() {
	@Override
	public void onSuccess() {
		List<Presentity> presentities = presenceList.getPresentities();
		if (presentities != null) {
			for (Presentity p : presentities) {
				if (p.getUserId().equals(presentityId)) {
					presentities.remove(p);
					break;
				}
			}
			adapter.update();
		}
	}

	@Override
	public void onFail(MobileError error) {
	}
});
```
#### ** Kotlin Code **

```kotlin
presenceService.deletePresenceList(presenceListKey, presentityId, object:PresenceCallback{
    override fun onSuccess() {
        val presenceList:PresenceList
        var presentities = mutableListOf<Presentity>()
        presentities = presenceList.presentities
        if(presentities != null){
            for(p:Presentity in presentities){
                if(p.userId.equals(presentityId)){
                    presentities.remove(p)
                    break
                }
            }
            adapter.update
        }
    }

    override fun onFail(error: MobileError?) {
        
    }
})
```
<!-- tabs:end -->
