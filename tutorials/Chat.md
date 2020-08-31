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

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatService;

// build up the list of services for subscriptions
List<ServiceInfo> services = new ArrayList<>();
services.add(new ServiceInfo(ServiceType.CHAT, true));

CPaaS cpaas = new CPaaS(services);

ChatService chatService = cpaas.getChatService();
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatService;

// build up the list of services for subscriptions
val services = mutableListOf<ServiceInfo>()
services.add(ServiceInfo(ServiceType.CHAT,true))

val cpaas = CPaaS(services)

val chatService: ChatService = cpaas.chatService
```
<!-- tabs:end -->

### Implement and Set Chat Listener

In order for the Mobile SDK to notify the application of notifications received from the server, an Chat listener must be set.

<!-- tabs:start -->

#### ** Java Code **

```java
chatService.setChatListener(this);

@Override
public void inboundChatMessageReceived(InboundMessage message) {
    Log.i("CPaaS.ChatService", "New message from " + message.getSenderAddress());

    // handle incoming Chat Message
}

@Override
public void chatDeliveryStatusChanged(String participant, MessageDeliveryStatus deliveryStatus, String messageID) {
  Log.i("CPaaS.ChatService", "Message delivery status changed to " + deliveryStatus.getString());

  // handle delivery status for Chat Message
}

@Override
public void outboundChatMessageSent(OutboundMessage message) {
    Log.i("CPaaS.ChatService", "Message is sent to " + message.getDestinationAddress());

    // handle outgoing Chat Message
}
```

#### ** Kotlin Code **

```kotlin
chatService.setChatListener(this)

override fun inboundChatMessageReceived(message: InboundMessage?) {
    Log.i("CPaaS.ChatService", "New message from " + message?.senderAddress)

    // handle incoming Chat Message
}

override fun chatDeliveryStatusChanged(participant: String?, deliveryStatus: MessageDeliveryStatus, messageId: String?) {
    Log.i("CPaaS.ChatService", "Message delivery status changed to $deliveryStatus.getString()");

    // handle delivery status for Chat Message
}

override fun outboundChatMessageSent(message: OutboundMessage?) {
       Log.i("CPaaS.ChatService", "Message is sent to " + message?.destinationAddress);

       // handle outgoing Chat Message
}
```
<!-- tabs:end -->

## Fetching Chat Messages
Chat messages are grouped into conversations with other users. A list of established conversations can be fetched from the server. For each conversation, the messages within that conversation may be fetched.

### Fetch all conversations from the server

Retrieve a list of all conversation objects from the server. Use this list to populate the application model with existing conversations.

<!-- tabs:start -->

#### ** Java Code **

```java
chatService.fetchConversations(new FetchConversationsCallback() {
    @Override
    public void onSuccess(List<Conversation> conversations) {
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle fail
    }
});
```

#### ** Kotlin Code **

```kotlin
chatService.fetchConversations(object:FetchConversationsCallback{
    override fun onSuccess(conversations: MutableList<Conversation>?) {
        //handle success
    }

    override fun onFail(error: MobileError?) {
        //handle fail
    }

})
```
<!-- tabs:end -->

### Fetch all messages from the server

Retrieve a list of all message objects from the server.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
chatConversation.fetchMessages(object:FetchCallback<List<Message>>{
               override fun onSuccess(result: FetchResult<List<Message>>?) {
                   //handle success
               }

               override fun onFail(error: MobileError?) {
                   //handle fail
               }

           })
```
<!-- tabs:end -->

### Fetch a number of messages from the server

Retrieve a list of message objects by filter from the server.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.api.FetchOptions;

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

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.api.FetchOptions

val options = FetchOptions()
options.max(50)

chatConversation.fetchMessages(options, object:FetchCallback<List<Message>>{
    override fun onSuccess(result: FetchResult<List<Message>>?) {
        //handle success
    }

    override fun onFail(error: MobileError?) {
        //handle fail
    }

})
```
<!-- tabs:end -->

## Sending Chat Messages
The Mobile SDK provides the ability to send text messages as well as messages with file attachments.

### Send a Chat message to a single destination

Send a Chat message to a single destination specified as the conversation participant with message content specified by withText. An implementer should specify a completion block to handle any response whether error or successful response.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatConversation;
import com.rbbn.cpaas.mobile.messaging.OutboundMessage;

String participant = "user@domain.com";
ChatConversation chatConversation = (ChatConversation) chatService.createConversation(participant);

String txt = "Hello world";
OutboundMessage message = chatService.createMessage(txt);

chatConversation.send(message, new SendMessageCallback() {
    @Override
    public void onSuccess(OutboundMessage message) {
        Log.d("SMS", "Sending SMS succeeded. Message id: " + message.messageId);
        //handle success
    }

    @Override
    public void onFail(MobileError error) {
        //handle fail
    }
});
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatConversation
import com.rbbn.cpaas.mobile.messaging.OutboundMessage

val participant = "user@domain.com"
val chatConversation: ChatConversation = chatService.createConversation(participant) as ChatConversation

val txt = "Hello world"
val message:OutboundMessage = chatService.createMessage(txt)

chatConversation.send(message,object:SendMessageCallback{
    override fun onSuccess(message: OutboundMessage?) {
        Log.d("SMS", "Sending SMS succeeded. Message id: ${message?.messageId}")
        //handle success
    }

    override fun onFail(error: MobileError?) {
        //handle fail
    }

})
```
<!-- tabs:end -->

### Send a Chat message with a file attachment

Send a Chat message with a file attachment to a single destination specified as the conversation participant with message content specified by "text" and attachment specified by "withFile". An implementer may specify a code block for indicating progress to the user. An implementer should specify a completion block to handle any response whether error or successful response.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.OutboundMessage;
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatConversation;

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

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.OutboundMessage
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatConversation

val participant = "user@domain.com"
val chatConversation: ChatConversation = chatService.createConversation(participant) as ChatConversation

val txt = "Hello world"
val message:OutboundMessage = chatService.createMessage(txt)

// an Attachment object is created after a file is uploaded with chatService.uploadAttachment()
for(attachment:Attachment in attachments){
message.attachFile(attachment)
}

chatConversation.send(message,object:MessagingCallback{
    override fun onSuccess() {
        //handle success
    }

    override fun onFail(error: MobileError?) {
        //handle fail
    }

})
```
<!-- tabs:end -->

### Download a file attachment from the server

After receiving or fetching a chat message with an attachment download the attachment file. The application may implement a progress handling code block for the purposes of indicating download progress to the user. An implementer should appropriately handle the downloaded file in the completion handler.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.model.Attachment;
import com.rbbn.cpaas.mobile.messaging.chat.api.TransferProgressListener;
import com.rbbn.cpaas.mobile.messaging.chat.api.DownloadCompleteListener;
import com.rbbn.cpaas.mobile.messaging.chat.api.TransferRequestHandle;

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

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.model.Attachment
import com.rbbn.cpaas.mobile.messaging.chat.api.TransferProgressListener
import com.rbbn.cpaas.mobile.messaging.chat.api.DownloadCompleteListener
import com.rbbn.cpaas.mobile.messaging.chat.api.TransferRequestHandle

val attachment:Attachment = message.parts[1].file

val url = attachment.link
val filename = attachment.name
val folder = //download folder

val progressCallback = object:TransferProgressListener{
    override fun reportProgress(bytes: Long, totalBytes: Long) {
        Log.i("CPaaS.ChatService", "Downloaded $bytes of $totalBytes bytes")
    }

}

val downloadCompleteListener = object:DownloadCompleteListener{
    override fun downloadSuccess(path: String?) {
        // handle downloaded file
    }
    override fun downloadFail(error: String?) {
        // handle fail
    }

}

val handle:TransferRequestHandle = chatService.downloadAttachment(url, folder, filename, progressCallback, downloadCompleteListener)
```
<!-- tabs:end -->

## Receiving Chat Messages
Receiving chat messages is an even driven process for new messages and an application requested event for message history. See the application listener method [**inboundMessageReceived**](#implement-and-set-chat-listener) from the initialization procedure for receiving new chat messages via notifications. See the Conversation [**fetchMessages**](#fetching-chat-messages) method from the fetching chat messages procedure.

## Deleting Chat Messages
An application may find it necessary to delete historical chat conversations. Messages can be deleted individually or an entire conversation may be deleted.

### Delete a conversation from the server

Delete an entire message thread from the server. This method will delete all messages associated with a particular participant.

<!-- tabs:start -->

#### ** Java Code **

```java
chatConversation.deleteConversation(new MessagingCallback() {
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

#### ** Kotlin Code **

```kotlin
chatConversation.deleteConversation(object:MessagingCallback{
                   override fun onSuccess() {
                       //handle success
                   }

                   override fun onFail(p0: MobileError?) {
                       //handle fail
                   }

               })
```
<!-- tabs:end -->

### Delete a message from the server

Delete a single message from a message thread. Each message has a unique identifier which can be used for erasing the message.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
val messageId = message.messageId
chatConversation.deleteMessage(messageId,object:MessagingCallback{
    override fun onSuccess() {
        //handle success
    }

    override fun onFail(error: MobileError?) {
        //handle fail
    }

})
```
<!-- tabs:end -->

## Group Chat

Group Chat messaging is managed by the Chat Service which can be called from the `CPaaS` instance. In order to receive and send events, the `CPaaS` instance should be connected first. To see how to connect and set configurations, check **Login** and **Configurations** sections.

### Receiving joined groups list

After the app succesfully connected to backend and authenticated,  already joined groups can be pulled from backend.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
private fun fetchGroups(){
   chatService.fetchAllGroups(object:FetchGroupsCallback{
       override fun onSuccess(groups: MutableList<ChatGroup>?) {
           //handle success
       }

       override fun onFail(error: MobileError?) {
           //handle fail
       }

   })
}
```
<!-- tabs:end -->

### Receiving joined groups icons

After successfully receiving the list of groups which user has joined, the app should download the icons of the related groups from backend server.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
fun downloadAttachment(attachment: Attachment,imageView: ImageView){
    val url = attachment.link
    val folder = DOWNLOAD_FOLDER + File.separator
    val filename = attachment.name

    val progressCallback = object:TransferProgressListener{
       override fun reportProgress(bytes: Long, totalBytes: Long) {
           Log.i("CPaaS.ChatService", "Downloaded $bytes of $totalBytes bytes")
       } 
   }

    val downloadCompleteListener = object:DownloadCompleteListener{
       override fun downloadSuccess(path: String?) {
        // handle downloaded file
       }
       override fun downloadFail(error: String?) {
        // handle fail
       }

    }
}
```
<!-- tabs:end -->

### Upload Group Chat Image

If the creator of the group wants to add an image before creating the group, the image file needs to be uploaded to server by using chat service.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
fun uploadAttachment(uri: Uri){
    val transferProgressListener = object:TransferProgressListener{
        override fun reportProgress(bytes: Long, totalBytes: Long) {
            Log.d(TAG, "Uploaded $bytes of $totalBytes bytes")
        }
    }
    val uploadCompleteListener = object:UploadCompleteListener{
        override fun uploadSuccess(attachment: Attachment?) {
            //handle success
        }
        override fun uploadFail(error: String?) {
            //handle fail
        }
    }
    val transferRequestHandle:TransferRequestHandle = chatService.uploadAttachment(uri,transferProgressListener,uploadCompleteListener)
}
```
<!-- tabs:end -->

### Create a Chat Group

When group name and subject is decided, createGroupChat method of ChatGroup service must be called in order to create a chat group and invite the participants. Upon succesfull creation of group, user creates the group conversation.

<!-- tabs:start -->

#### ** Java Code **

```java
private void createChatGroup() {
  String groupName = nameEditText.getText().toString();
  String subject = subjectEditText.getText().toString();
  String image = "https://imagelink";
  boolean isOpen = false;

  chatService.createGroup(subject, image, groupName, isOpen, memberList, new FetchGroupCallback() {
    @Override
    public void onSuccess(ChatGroup group) {
      // handle success
    }

    @Override
    public void onFail(MobileError error) {
      // handle fail
    }
  });
}
```

#### ** Kotlin Code **

```kotlin
private fun createChatGroup(){
   val groupName = nameEditText.text.toString()
   val subject = subjectEditText.text.toString()
   val image = "https://imagelink"
   val isOpen = false

   chatService.createGroup(subject, image, groupName, isOpen, memberList, object:FetchGroupCallback{
       override fun onSuccess(group: ChatGroup?) {
          //handle success
       }

       override fun onFail(error: MobileError?) {
          //handle fail
       }

   })
}
```
<!-- tabs:end -->

### Add Participants To Already Created Group

When the admin priviliged user wanted to add member to a current group, groupChat object's add method can be called. GroupChat object is initialized by Mobile SDK when createGroupChat method is called.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatGroupParticipant;

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

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatGroupParticipant

fun addGroupMember(view: View){
    val memberList = mutableListOf<ChatGroupParticipant>()
    val member = input.text.toString()

    val chatGroupParticipant = chatService.createChatGroupParticipant(member)

    if(newGroup){
        memberList.add(chatGroupParticipant)
        runOnUiThread {
            groupMembersAdapter.notifyDataSetChanged()
        }
    } else {
        chatService.addParticipantToGroup(groupId, member, object:MessagingCallback{
            override fun onSuccess() {
                memberList.add(chatGroupParticipant)
                runOnUiThread {
                    groupMembersAdapter.notifyDataSetChanged()
                }
            }

            override fun onFail(p0: MobileError?) {
                //handle fail
            }

        })
    }
}
```
<!-- tabs:end -->

### Remove Participants From Already Created Group

When the admin priviliged user wants to remove a member from a current group, groupChat object's remove method must be called.
GroupChat object is initialized by Mobile SDK when createGroupChat method is called.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
private fun removeParticipantFromGroup(chatGroupParticipant: ChatGroupParticipant) {
  val participant = chatGroupParticipant.address
  if(groupId.length() > 0) {
  // existing group, remove the participant from the group on the server
  chatService.removeParticipantFromGroup(groupId, participant, object:MessagingCallback{
      override fun onSuccess() {
        memberList.remove(chatGroupParticipant)
      }

      override fun onFail(error: MobileError?) {
        //handle fail
      }

  })
  } else {
    // new group, just remove the participant from the list
    memberList.remove(chatGroupParticipant)
  }
}
```
<!-- tabs:end -->

### Accept invitation from a group

When a user is added to a group an invitation message is sent. Upon receiving the invitation, user either accepts it and joins the group or decline it remove self from the group.
For accepting the invitation call the Mobile SDK group chat accept method.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
private fun acceptInvitation(chatGroup: ChatGroup) {
  val groupId = chatGroup.id
  chatService.acceptGroupInvitation(groupId, object:MessagingCallback{
         override fun onSuccess() {
           //handle success
         }

         override fun onFail(error: MobileError?) {
           //handle fail
         }
     })
}
```
<!-- tabs:end -->

### Decline invitation from a group

When a user added to a group, an invitation message is sent. Upon receiving the invitation, user either accepts it and joins the group or declines the invitation and removed from group. For declining the invitation call the Mobile SDK group chat decline method.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
private fun declineInvitation(chatGroup: ChatGroup) {
  val groupId = chatGroup.id
  chatService.declineGroupInvitation(groupId, object:MessagingCallback{
        override fun onSuccess() {
          groupList.remove(chatGroup)
          adapter.setGroupList(groupList)
        }

        override fun onFail(error: MobileError?) {
          //handle fail
        }

  })
}
```
<!-- tabs:end -->

### Delete chat group permanently

If an admin privileged user wants to delete the chat group, deleteChatGroup method must be called.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
private fun deleteChatGroup(chatGroup:ChatGroup){
  val groupId = chatGroup.id
  chatService.deleteGroup(groupId, object:MessagingCallback{
        override fun onSuccess() {
          groupList.remove(chatGroup)
          adapter.setGroupList(groupList)
        }

        override fun onFail(error: MobileError?) {
          //handle fail
        }

  })
}
```
<!-- tabs:end -->

### Leave chat group permanently

If an user wants to leave the chat group, leaveChatGroup method must be called.

<!-- tabs:start -->

#### ** Java Code **

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

#### ** Kotlin Code **

```kotlin
private fun leaveChatGroup(chatGroup: ChatGroup) {
  val groupId = chatGroup.id
  chatService.leaveGroup(groupId, object:MessagingCallback{
      override fun onSuccess() {
        groupList.remove(chatGroup)
        adapter.setGroupList(groupList)
      }

      override fun onFail(error: MobileError?) {
        //handle fail
      }
  })
}
```
<!-- tabs:end -->

### Receiving invitations

When the current user added to a group, this listener method will be triggered.
Invitation parameter contains an invitation object that contains a copy of the group to be joined. The invitation object has methods for accept and decline.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatListener;

//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view
public class ChatManager implements ChatListener {
  ...

  @Override
  public void groupChatSessionInvitation(List<ChatGroupParticipant> chatGroupParticipants, String groupId, String groupName) {
    // handle group session invitations
  }
}
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.messaging.chat.api.ChatListener

//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view
class ChatManager: ChatListener {

    ...
    
    override fun groupChatSessionInvitation(chatGroupParticipants: MutableList<ChatGroupParticipant>?, groupId: String?, groupName: String?) {
       // handle group session invitations
}
```
<!-- tabs:end -->

### Receiving group participants status

When a new user added to a group or removed from group, or any other status update happened about a group participant. This method will be trigered
Participants parameter contains an array of chat group members that have updated status

<!-- tabs:start -->

#### ** Java Code **

```java
//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view
public class ChatManager implements ChatListener {

  ...

  @Override
    public void chatParticipantStatusChanged(ChatGroupParticipant chatGroupParticipant, String groupId) {
      // handle group participant status changes
    }

}
```

#### ** Kotlin Code **

```kotlin
//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view

class ChatManager: ChatListener {
    ...
    
    override fun chatParticipantStatusChanged(chatGroupParticipant: ChatGroupParticipant?, groupId: String?) {
        // handle group participant status changes
    }
}
```
<!-- tabs:end -->

### Group chat event notification

This listener method is called when a group chat event has received. groupID parameter indicates the chat group for which the event notification is received.

<!-- tabs:start -->

#### ** Java Code **

```java
//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view
public class ChatManager implements ChatListener {
  ...
  
  @Override
    public void groupChatEventNotification(String type, String description, String groupId) {
      // handle chat group events
    }
}
```

#### ** Kotlin Code **

```kotlin
//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view

class ChatManager: ChatListener {
    ...

    override fun groupChatEventNotification(type: String?, description: String?, groupId: String?) {
       // handle chat group events
    }
}
```
<!-- tabs:end -->
