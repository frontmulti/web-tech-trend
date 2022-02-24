# Firebase Push 구현하기

FCM(Firebase Cloud Messaging)을 사용해 Push 서비스를 구현할 수 있다.

---

# 작동원리

FCM 구현에는 송수신을 위한 두 가지 주요 구성요소가 포함된다.

1. Firebase용 Cloud Functions 또는 앱 서버와 같이 메시지를 작성, 타겟팅, 전송할 수 있는 신뢰할 수 있는 환경
2. 해당 플랫폼별 전송 서비스를 통해 메시지를 수신하는 Apple, Android 또는 웹(자바스크립트) 클라이언트 앱

참고 : [https://firebase.google.com/docs/cloud-messaging?hl=ko](https://firebase.google.com/docs/cloud-messaging?hl=ko)

---

# FCM 아키텍처 개요

![Untitled](Firebase%20P%205b277/Untitled.png)

---

# 실습

React Native Firebase 를 사용 : [https://rnfirebase.io/messaging/usage](https://rnfirebase.io/messaging/usage)

설치

```bash
# Install & setup the app module
yarn add @react-native-firebase/app

# Install the messaging module
yarn add @react-native-firebase/messaging

# If you're developing your app using iOS, run this command
cd ios/ && pod install
```

<aside>
💡 FMC Token 등록이 필요한데, React Native Firebase Messaging을 사용하면 자동으로 FMC Token을 등록해준다.
추가 설정을 원하는 경우 ‘firebase.json’에 필요한 내용을 등록할 수 있다.

</aside>

ios의 경우 permissions이 필요하여 permissions을 요청하는 코드 작성

```jsx
// **iOS - Requesting permissions
// App.js

async function requestUserPermission() {
  const authStatus = await messaging().requestPermission();
  const enabled = 
    authStatus === messaging.AuthorizationStatus.AUTHORIZED || 
    authStatus === messaging.AuthorizationStatus.PROVISIONAL;

  if (enabled) {
    console.log('Authorization Status:', authStatus);
  }
}

class App extends Component {
    
  constructor() {
    requestUserPermission();
  }
	// 생략
}**

```

BackgroudMessageHandler를 등록하여 message를 console.log로 확인하는 코드 작성

```jsx

class App extends Component {
    
  constructor() {
    requestUserPermission();
    messaging().setBackgroundMessageHandler(async remoteMessage => {
      console.log('Message handled in the background!', remoteMessage);
    });
  }
	// 생략
}
```


메시지 보내보기

Firebase Console > Cloud Messaging > Send your first message 선택

![Untitled](Firebase%20P%205b277/Untitled%201.png)

![Untitled](Firebase%20P%205b277/Untitled%202.png)

테스트 메시지 전송 선택

![Untitled](Firebase%20P%205b277/Untitled%203.png)

테스트 기기의 FCM Token이 필요하다.

FCM Token을 얻기 위한 코드를 작성한다.

```jsx
async function getFcmToken() {
  const fcmToken = await messaging().getToken();
  if (fcmToken) {
    console.log('fcmToken : ', fcmToken);
  } else {
    console.log('No Instance ID token available. Request permission to generate one.');
  }
}

class App extends Component {
    
  constructor() {
    super();
    getFcmToken();
  }
}
```

```jsx
fcmToken : XXX
```

얻은 FCM Token을 입력하고 테스트 메시지 전송

![Untitled](Firebase%20P%205b277/Untitled%204.png)

알림 확인

![Untitled](Firebase%20P%205b277/Untitled%205.png)

타겟 항목에서 *사용자 세그먼트, 주제*를 선택하여 메시지를 보낼 수 있다.

![Untitled](Firebase%20P%205b277/Untitled%206.png)

<aside>
💡 사용자들의 FCM Token을 서버에 저장하고 push 메시지를 보내야할 것으로 예상했으나 앱 단위로 메시지를 전달할 수 있는듯 보임.
이 부분은 backend에서 push 메시지를 전달할때도 해당 항목을 선택해서 보낼 수도 있을 것으로 보이는데, 확인이 필요해보임.

</aside>
