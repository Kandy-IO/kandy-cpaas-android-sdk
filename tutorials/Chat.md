---
  topics:
  - title: Fetching Chat Messages
    body: In this section you will learn how to fetch chat messages from $KANDY$ using $KANDY$ Mobile SDK.
  - title: Sending Chat Messages
    body: In this section you will learn how to send a chat message using $KANDY$ Mobile SDK.
  - title: Receiving Chat Messages
    body: In this section you will learn how to receive a chat message using $KANDY$ Mobile SDK.
  - title: Deleting Chat Messages
    body: In this section you will learn how to delete a chat message using $KANDY$ Mobile SDK.
  - title: Group Chat
    body: In this section you will learn how to manage group chat conversations using $KANDY$ Mobile SDK.
---
# Chat

Chat messaging is managed by the Chat Service which can be called from the `CPaaS` instance. In order to receive and send events, the `CPaaS` instance should be connected first. To see how to connect and set configurations, check **Login** and **Configurations** sections.

### Initialize the Chat Service

In order to use the Chat service, the service provider object must be properly initialized. When properly initialized, the application will be registered to receive Chat notifications from the server.

```java
// build up the list of services for subscriptions
List<ServiceInfo> services = new ArrayList<>();
services.add(new ServiceInfo(ServiceType.CHAT, true));

CPaaS cpaas = new CPaaS(services);

ChatService chatService = cpaas.getChatService();
```

### Implement and Set Chat Listener

In order for the Mobile SDK to notify the application of notifications received from the server, an Chat listener must be set.

```java
chatService.setChatListener(this);

@Override
public void inboundChatMessageReceived(InboundMessage message) {
    Log.i("CPaaS.ChatService", "New message from " + message.getSenderAddress());

    // handle incoming Chat Message
}

@Override
public void chatDeliveryStatusChanged(String participant, String deliveryStatus, String messageID) {
  Log.i("CPaaS.ChatService", "Message delivery status changed to " + deliveryStatus);

  // handle delivery status for Chat Message
}

@Override
public void outboundChatMessageSent(OutboundMessage message) {
    Log.i("CPaaS.ChatService", "Message is sent to " + message.getDestinationAddress());

    // handle outgoing Chat Message
}
```

## Fetching Chat Messages
Chat messages are grouped into conversations with other users. A list of established conversations can be fetched from the server. For each conversation, the messages within that conversation may be fetched.

### Fetch all conversations from the server

Retrieve a list of all conversation objects from the server. Use this list to populate the application model with existing conversations.

```java
chatService.fetchConversations(new FetchConversationsCallback() {
    @Override
    public void onSuccess(List<Conversation> conversations) {
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle success
    }
});
```

### Fetch all messages from the server

Retrieve a list of all message objects from the server.

```java
chatConversation.fetchMessages(new FetchCallback<List<Message>>() {
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

chatConversation.fetchMessages(options, new FetchCallback<List<Message>>() {
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

## Sending Chat Messages
The Mobile SDK provides the ability to send text messages as well as messages with file attachments.

### Send a Chat message to a single destination

Send a Chat message to a single destination specified as the conversation participant with message content specified by withText. An implementer should specify a completion block to handle any response whether error or successful response.

```java
String participant = "user@domain.com";
ChatConversation chatConversation = (ChatConversation) chatService.createConversation(participant);

String txt = "Hello world";
OutboundMessage message = chatService.createMessage(txt);

chatConversation.send(message, new MessagingCallback() {
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


### Send a Chat message with a file attachment

Send a Chat message with a file attachment to a single destination specified as the conversation participant with message content specified by "text" and attachment specified by "withFile". An implementer may specify a code block for indicating progress to the user. An implementer should specify a completion block to handle any response whether error or successful response.

```java
String participant = "user@domain.com";
ChatConversation chatConversation = (ChatConversation) chatService.createConversation(participant);

String txt = "Hello world";
OutboundMessage message = chatService.createMessage(txt);

// an Attachment object is created after a file is uploaded with chatService.uploadAttachment()
for (Attachment attachment : attachments) {
    message.attachFile(attachment);
}

chatConversation.send(message, new MessagingCallback() {
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


### Download a file attachment from the server

After receiving or fetching a chat message with an attachment download the attachment file. The application may implement a progress handling code block for the purposes of indicating download progress to the user. An implementer should appropriately handle the downloaded file in the completion handler.

```java
Attachment attachment = message.getParts().get(1).getFile();

String url = attachment.getLink();
String filename = attachment.getName();
String folder = // download folder

TransferProgressListener progressCallback = new TransferProgressListener() {
    @Override
    public void reportProgress(long bytes, long totalBytes) {
        Log.i("CPaaS.ChatService", "Downloaded " + bytes + " of " + totalBytes + " bytes");
    }
};

DownloadCompleteListener downloadCompleteListener = new DownloadCompleteListener() {
    @Override
    public void downloadSuccess(String path) {
        // handle downloaded file
    }

    @Override
    public void downloadFail(String error) {
        // handle fail
    }
};

TransferRequestHandle handle = chatService.downloadAttachment(url, folder, filename, progressCallback, downloadCompleteListener);
```

## Receiving Chat Messages
Receiving chat messages is an even driven process for new messages and an application requested event for message history. See the application listener method [**inboundMessageReceived**](#implement-and-set-chat-listener) from the initialization procedure for receiving new chat messages via notifications. See the Conversation [**fetchMessages**](#fetching-chat-messages) method from the fetching chat messages procedure.

## Deleting Chat Messages
An application may find it necessary to delete historical chat conversations. Messages can be deleted individually or an entire conversation may be deleted.

### Delete a conversation from the server

Delete an entire message thread from the server. This method will delete all messages associated with a particular participant.

```java
chatService.deleteConversation(participant, new MessagingCallback() {
    @Override
    public void onSuccess() {
        // handle success
    }

    @Override
    public void onFail(MobileError error) {
        // handle fail
    }
});
```

### Delete a message from the server

Delete a single message from a message thread. Each message has a unique identifier which can be used for erasing the message.

```java
String messageID = message.getMessageID();
chatConversation.deleteMessage(messageID, new MessagingCallback() {
    @Override
    public void onSuccess() {
        // handle success
    }

    @Override
    public void onFail(MobileError error) {
        // handle fail
    }
});
```

## Group Chat

Group Chat messaging is managed by the Chat Service which can be called from the `CPaaS` instance. In order to receive and send events, the `CPaaS` instance should be connected first. To see how to connect and set configurations, check **Login** and **Configurations** sections.

### Receiving joined groups list

After the app succesfully connected to backend and authenticated,  already joined groups can be pulled from backend.

```java
protected void fetchGroups() {
  chatService.fetchAllGroups(new FetchGroupsCallback() {

    @Override
    public void onSuccess(List<ChatGroup> groups) {
      // handle success
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

### Receiving joined groups icons

After successfully receiving the list of groups which user has joined, the app should download the icons of the related groups from backend server.

```java
public void downloadAttachment(Attachment attachment, ImageView imageView) {
  String url = attachment.getLink();
  String folder = DOWNLOAD_FOLDER + File.separator;
  String filename = attachment.getName();

  TransferProgressListener progressCallback = new TransferProgressListener() {
    @Override
    public void reportProgress(long bytes, long totalBytes) {
      Log.d(TAG, "Downloaded " + bytes + " of " + totalBytes + " bytes");
    }
  };


  DownloadCompleteListener downloadCompleteListener = new DownloadCompleteListener() {
      @Override
      public void downloadSuccess(String path) {
          // handle downloaded file
      }

      @Override
      public void downloadFail(String error) {
          // handle fail
      }
  };
}
```

### Upload Group Chat Image

If the creator of the group wants to add an image before creating the group, the image file needs to be uploaded to server by using chat service.

```java
public void uploadAttachment(Uri uri) {
  TransferProgressListener transferProgressListener = new TransferProgressListener() {
    @Override
    public void reportProgress(long bytes, long totalBytes) {
      Log.d(TAG, "Uploaded " + bytes + " of " + totalBytes + " bytes");
    }
  };

  UploadCompleteListener uploadCompleteListener = new UploadCompleteListener() {
    @Override
    public void uploadSuccess(Attachment attachment) {
      // handle success
    }

    @Override
    public void uploadFail(String error) {
      // handle fail
    }
  };

  TransferRequestHandle handle = chatService.uploadAttachment(uri, transferProgressListener, uploadCompleteListener);
}
```

### Create a Chat Group

When group name and subject is decided, createGroupChat method of ChatGroup service must be called in order to create a chat group and invite the participants. Upon succesfull creation of group, user creates the group conversation.

```java
private void createChatGroup() {
  String groupName = nameEditText.getText().toString();
  String subject = subjectEditText.getText().toString();
  String image = "https://imagelink";
  boolean isOpen = false;

  chatService.createGroup(subject, image, groupName, isOpen, memberList, new FetchGroupCallback() {
    @Override
    public void onSuccess(group) {
      // handle success
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

### Add Participants To Already Created Group

When the admin priviliged user wanted to add member to a current group, groupChat object's add method can be called. GroupChat object is initialized by Mobile SDK when createGroupChat method is called.

```java
public void addGroupMember(View view) {
  List<ChatGroupParticipant> memberList = new ArrayList<>();
  String member = input.getText().toString();
  ChatGroupParticipant chatGroupParticipant = chatService.createChatGroupParticipant(member);

  if (newGroup) {
    memberList.add(chatGroupParticipant);
    runOnUiThread(() -> groupMembersAdapter.notifyDataSetChanged());
  } else {
    chatService.addParticipantToGroup(groupId, member, new MessagingCallback() {
      @Override
      public void onSuccess() {
        memberList.add(chatGroupParticipant);
        runOnUiThread(() -> groupMembersAdapter.notifyDataSetChanged());
      }

      @Override
      public void onFail(MobileError error) {
        // handle fail
      }
    });
  }
}
```

### Remove Participants From Already Created Group

When the admin priviliged user wants to remove a member from a current group, groupChat object's remove method must be called.
GroupChat object is initialized by Mobile SDK when createGroupChat method is called.

```java
private void removeParticipantFromGroup(ChatGroupParticipant chatGroupParticipant) {
  String participant = chatGroupParticipant.getAddress();
  if (groupId.length() > 0) {
    // existing group, remove the participant from the group on the server
    chatService.removeParticipantFromGroup(groupId, participant, new MessagingCallback() {
      @Override
      public void onSuccess() {
        memberList.remove(chatGroupParticipant);
      }

      @Override
      public void onFail(MobileError error) {
        // handle fail
      }
    });
  } else {
    // new group, just remove the participant from the list
    memberList.remove(chatGroupParticipant);
  }
}
```

### Accept invitation from a group

When a user is added to a group an invitation message is sent. Upon receiving the invitation, user either accepts it and joins the group or decline it remove self from the group.
For accepting the invitation call the Mobile SDK group chat accept method.

```java
private void acceptInvitation(ChatGroup chatGroup) {
  String groupId = chatGroup.getId();
  chatService.acceptGroupInvitation(groupId, new MessagingCallback() {
    @Override
    public void onSuccess() {
      // handle success
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

### Decline invitation from a group

When a user added to a group, an invitation message is sent. Upon receiving the invitation, user either accepts it and joins the group or declines the invitation and removed from group. For declining the invitation call the Mobile SDK group chat decline method.

```java
private void declineInvitation(ChatGroup chatGroup) {
  String groupId = chatGroup.getId();
  chatService.declineGroupInvitation(groupId, new MessagingCallback() {
    @Override
    public void onSuccess() {
      groupList.remove(chatGroup);
      adapter.setGroupList(groupList);
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

### Delete chat group permanently

If an admin privileged user wants to delete the chat group, deleteChatGroup method must be called.

```java
private void deleteChatGroup(ChatGroup chatGroup) {
  String groupId = chatGroup.getId();
  chatService.deleteGroup(groupId, new MessagingCallback() {
    @Override
    public void onSuccess() {
      groupList.remove(chatGroup);
      adapter.setGroupList(groupList);
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

### Leave chat group permanently

If an user wants to leave the chat group, leaveChatGroup method must be called.

```java
private void leaveChatGroup(ChatGroup chatGroup) {
  String groupId = chatGroup.getId();
  chatService.leaveGroup(groupId, new MessagingCallback() {
    @Override
    public void onSuccess() {
      groupList.remove(chatGroup);
      adapter.setGroupList(groupList);
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

### Receiving invitations

When the current user added to a group, this listener method will be triggered.
Invitation parameter contains an invitation object that contains a copy of the group to be joined. The invitation object has methods for accept and decline.

```java
public class ChatManager implements ChatListener {

  ...

  @Override
  public void groupChatSessionInvitation(List<ChatGroupParticipant> chatGroupParticipants, String groupId, String groupName) {
    // handle group session invitations
  }

}
```

### Receiving group participants status

When a new user added to a group or removed from group, or any other status update happened about a group participant. This method will be trigered
Participants parameter contains an array of chat group members that have updated status

```java
public class ChatManager implements ChatListener {

  ...

  @Override
    public void chatParticipantStatusChanged(ChatGroupParticipant chatGroupParticipant, String groupId) {
      // handle group participant status changes
    }

}
```

### Group chat event notification

This listener method is called when a group chat event has received. groupID parameter indicates the chat group for which the event notification is received.

```java
public class ChatManager implements ChatListener {

  ...

  @Override
    public void groupChatEventNotification(String type, String description, String groupId) {
      // handle chat group events
    }

}
```
