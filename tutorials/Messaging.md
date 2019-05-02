---
  topics:
  - title: Fetching SMS Messages
    body: In this section you will learn how to fetch SMS messages using $KANDY$ Mobile SDK.
  - title: Sending SMS Messages
    body: In this section you will learn how to send an SMS messages using $KANDY$ Mobile SDK.
  - title: Receiving SMS Messages
    body: In this section you will learn how to receive an SMS messages using $KANDY$ Mobile SDK.
---
# SMS Messaging

SMS messaging is managed by the SMS Service which can be called from the `CPaaS` instance. In order to receive and send events, the `CPaaS` instance should be connected first. To see how to connect and set configurations, check **Login** and **Configurations** sections.

### Initialize the SMS Service

In order to use the SMS service, the service provider object must be properly initialized. When properly initialized, the application will be registered to receive SMS notifications from the server.

```java
// build up the list of services for subscriptions
List<ServiceInfo> services = new ArrayList<>();
services.add(new ServiceInfo(ServiceType.SMS, true));

CPaaS cpaas = new CPaaS(services);

SMSService smsService = cpaas.getSMSService();
```

### Implement and Set Sms Listener

In order for the Mobile SDK to notify the application of notifications received from the server, an SMS listener must be set.

```java
smsService.setSMSListener(this);

@Override
public void inboundSMSMessageReceived(InboundMessage message) {
    Log.i("CPaaS.SMSService", "New message from " + message.getSenderAddress());

    // handle incoming SMS Message
}

@Override
public void SMSDeliveryStatusChanged(String participant, String deliveryStatus, String messageID) {
    Log.i("CPaaS.SMSService", "Message delivery status changed to " + deliveryStatus);

    // handle delivery status for SMS Message
}

@Override
public void outboundSMSMessageSent(OutboundMessage message) {
    Log.i("CPaaS.SMSService", "Message is sent to " + message.getDestinationAddress());

    // handle outgoing SMS Message
}
```

## Fetching SMS Messages
SMS messages are grouped into conversations with other users. A list of established conversations can be fetched from the server. For each conversation, the messages within that conversation may be fetched.

### Fetch all conversations from the server

Retrieve a list of all conversation objects from the server. Use this list to populate the application model with existing conversations.

```java
smsService.fetchConversations(new FetchCallback<List<Conversation>>() {
    @Override
    public void onSuccess(FetchResult<List<Conversation>> result) {
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle fail
    }
});
```

### Fetch conversations by criteria from the server

Retrieve a list of relevant conversation objects from the server. Using FetchCriteria, users can filter the participant, sender phone number and options.
Options filter has provided search parameters; max, last message time and is a new message.

```java
FetchCriteria criteria = new FetchCriteria();
criteria.participant("+19725551212");

FetchOptions options = new FetchOptions();
options.max(10);
options.lastMessageTime(Long.parseLong("1552460233"));

criteria.fetchOptions(options);

smsService.fetchConversations(criteria, new FetchCallback<List<Conversation>>() {
    @Override
    public void onSuccess(FetchResult<List<Conversation>> result) {
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle fail
    }
});
```

### Fetch all messages from the server

Retrieve a list of all message objects from the server.

```java
smsConversation.fetchMessages(new FetchCallback<List<Message>>() {
    @Override
    public void onSuccess(FetchResult<List<Message>> result) {
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle fail
    }
});
```

### Fetch a number of messages from the server

Retrieve a list of message objects by filter from the server.

```java
FetchOptions options = new FetchOptions();
options.max(50);

smsConversation.fetchMessages(options, new FetchCallback<List<Message>>() {
    @Override
    public void onSuccess(FetchResult<List<Message>> result) {
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle fail
    }
});
```

## Sending SMS Messages
The Mobile SDK provides the ability to send SMS text messages.

### Send an SMS message to a single destination

Send an SMS message from a local address specified by the SMS Service within a list of available numbers to a single destination specified as the conversation participant with message content specified by OutboundMessage. An implementer should specify a MessagingCallback to handle any response whether error or successful response.

```java
String participant = "+19725551212";
List<String> localAdressList = smsService.getLocalAddressList();
String localAddress = //choose one address from the list above
SMSConversation smsConversation = (SMSConversation) smsService.createConversation(participant, localAddress);

String txt = "Hello world";
OutboundMessage message = smsService.createMessage(txt);

smsConversation.send(message, new MessagingCallback() {
	@Override
	public void onSuccess() {
		//handle success
	}

	@Override
	public void onFail(MobileError error) {
		//handle fail
	}
});
```

## Receiving SMS Messages

Receiving SMS messages is an event driven process for new messages. See the application listener method [**inboundMessageReceived**](#implement-and-set-sms-listener) from the initialization procedure for receiving new SMS messages via notifications.
