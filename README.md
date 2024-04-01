# Android Message SDK

## Requirements

- **IDE**: Android Studio 3.0 or later
- **minSdkVersion**: 24
- **targetSdkVersion**: 34
- **Kotlin Version**: 1.9.x or later
- **Android Gradle Plugin**: 8.1.0 or lower

## Integration

### In your settings.gradle file, `dependencyResolutionManagement` sections:

```groovy
dependencyResolutionManagement {
  repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
  repositories {
    google()
    mavenCentral()
    //add below
    maven {
      url = uri("https://maven.pkg.github.com/blendvision/Android-Messaing-SDK")
      credentials {
        username = "bv-github-access"
        password = "ghp_GiNfne8EixUKMJf@yeUy5otI6oGTtVc3hyoXQ" // Please remove "@" char
      }
    }

  }
}
```

### Add the dependencies for the Messaging SDK to your module's app-level Gradle file, normally app/build.gradle:

```groovy
dependencies {
  implementation 'com.blendvision.chat:messaging:1.0.0'
}
```

### excludes `META-INF` to your module's app-level Gradle file

```groovy
android {

  //add below
  packaging {
    resources {
      excludes += "/META-INF/*"
    }
  }

}
```

## Usage

### 1. Implement `MessageListener`

- When successfully connected to the chatroom, you will be able to receive messages from the chatroom through onReceiveMessage.

```kotlin
   private val messageListener = object : MessageListener {
  override fun onReceiveMessage(messages: List<MessageInfo>) {
    // Handle messages.

    /**
    ex.
    val messageInfo = messages[index]
    when(messageInfo.type){
        is MessageType.INTERACTION_TYPE_TEXT ->TODO
        is MessageType.INTERACTION_TYPE_MUTE ->TODO
    .....
    }
     **/

  }
}
```

### 2. Implement `EventListener`

```kotlin
   private val eventListener = object : EventListener {

       // ConnectionState.CONNECTING - Connecting
       // ConnectionState.CONNECTED - Connected successfully
       // ConnectionState.DISCONNECTING - Disconnecting
       // ConnectionState.DISCONNECTED - Disconnected successfully
       override fun onChatRoomConnectionChanged(connectionState: ConnectionState) {
           //When the connectionState is ConnectionState.CONNECTED, you can retrieve the chatroomInfo from the connectionState.
       }

       //This method is optional and can be overridden if needed.
       override fun onMuteChatRoomSuccess() {}

       //This method is optional and can be overridden if needed.
       override fun onUnmuteChatRoomSuccess() {}

       //This method is optional and can be overridden if needed.
       override fun onBlockChatRoomUserSuccess(userId: String) {}

       //This method is optional and can be overridden if needed.
       override fun onUnblockChatRoomUserSuccess(userId: String) {}

       //This method is optional and can be overridden if needed.
       override fun onGetChatHistorySuccess(messages: List<MessageInfo>) {}

       //This method is optional and can be overridden if needed.
       override fun onDeleteMessageSuccess(messageId: String) {}

       //This method is optional and can be overridden if needed.
       override fun onPinMessageSuccess() {}

       //This method is optional and can be overridden if needed.
       override fun onUnpinMessageSuccess() {}

       //This method is optional and can be overridden if needed.
       override fun onUpdateViewerInfoSuccess() {}

       //This method is optional and can be overridden if needed.
       override fun onUpdateUserSuccess() {}

       override fun onError(exception: MessageException) {
           // Error exception
       }
   }
```

### 3. Create `BVMessageManager`

- The `apiToken` and `bvOrgId` can be obtained from the BlendVision console.

```kotlin
  val messageManager = BVMessageManager.Builder(apiToken, orgId)
  .setEventListener(eventListener)
  .setMessageListener(messageListener)
  .build()
```

### 4. Connect to ChatRoom

- The role types are: `ChatRoomRole.ROLE_UNSPECIFIED`, `ChatRoomRole.ROLE_VIEWER`, and `ChatRoomRole.ROLE_ADMIN`.
- Upon successful connection, the status callback for `ConnectionState.CONNECTED` will be received in the `onChatRoomConnectionChanged` method.
- Optional: The `UserName` parameter is optional.

```kotlin
 //Example usage:
 //Guest
 messageManager.connect(chatRoomId = chatRoomId, chatRoomRole = ChatRoomRole.ROLE_VIEWER,userName = null)
 //Setting UserName
 messageManager.connect(chatRoomId = chatRoomId, chatRoomRole = ChatRoomRole.ROLE_VIEWER,userName = "Anthony")
 //Admin
 messageManager.connect(chatRoomId = chatRoomId, chatRoomRole = ChatRoomRole.ROLE_ADMIN,userName = "Isaac")
```

### 5. Publish message to ChatRoom

- When the publish message is successful,new message will be received in the `onReceiveMessage` method.

```kotlin
 messageManager.publishMessage(message)
```

### 6. Publish custom message to ChatRoom

- When the publish custom message is successful,new message will be received in the `onReceiveMessage` method.

```kotlin
 messageManager.publishCustomMessage(customMessage)
```

### 7. Disconnect from ChatRoom

- When the disconnection is successful, the status callback of `ConnectionState.DISCONNECTED` will be received in the `onChatRoomConnectionChanged`
  method.

```kotlin
messageManager.disconnect()
```

## Others BVMessageManager API

```kotlin

// When mute the chat room is successful, will be received in the `onMuteChatRoomSuccess` method.
messageManager.muteChatRoom()

// When unmute the chat room is successful, will be received in the `onUnmuteChatRoomSuccess` method.
messageManager.unmuteChatRoom()

// When block user is successful, will be received in the `onBlockChatRoomUserSuccess` method.
messageManager.blockUser(userId, userDeviceId, userCustomName)

// When unblock user is successful, will be received in the `onUnblockChatRoomUserSuccess` method.
messageManager.unblockUser(userId)

// When delete message is successful, will be received in the `onDeleteMessageSuccess` method.
messageManager.deleteMessage(messageId)

// When pin message is successful, will be received in the `onPinMessageSuccess` method.
messageManager.pinMessage(messageId, text, userId, userDeviceId, userCustomName)

// When unpin message is successful, will be received in the `onUnpinMessageSuccess` method.
messageManager.unpinMessage(messageId)

// When update viewer info is successful, will be received in the `onUpdateViewerInfoSuccess` method.
messageManager.updateViewerInfo(enabled, customName)

// When update user is successful, will be received in the `onUpdateUserSuccess` method.
messageManager.updateUser(customName)

// When get chat history is successful, will be received in the `onGetChatHistorySuccess` method.
messageManager.getChatHistory(beforeAt, limit)

```
