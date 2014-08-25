Pushwoosh BlackBerry 10 Cascades SDK
=====================

The SDK contains source code for BlackBerry 10 CasCades platform.  

The guilde for SDK integration is available on Pushwoosh website:  
Coming soon.  

1.In your main App add push notifications service:  


    PushNotificationService m_pushNotificationService;  

2.Initialize push service:  


    m_pushNotificationService.initializePushService(BLACKBERRY_APPID, PPG_URL, PUSHWOOSH_APP_ID, INVOKE_TARGET_KEY_PUSH, INVOKE_TARGET_KEY_OPEN);  

Note that INVOKE_TARGET_KEY_PUSH needs to match the invoke target specified in bar-descriptor.xml  
This is Invoke target key for receiving new push notifications  
(ex: com.pushwoosh.example.invoke.push)  

Note that INVOKE_TARGET_KEY_OPEN needs to match the invoke target specified in bar-descriptor.xml  
This is Invoke target key when selecting a notification in the BlackBerry Hub  
(ex: com.pushwoosh.example.invoke.open)  

3.Connect the push notification service signals and slots:  


    QObject::connect(&m_pushNotificationService, SIGNAL(registeredForPushNotifications(const QString &)),  this, SLOT(registeredForPushNotifications(const QString &)));  
    
    QObject::connect(&m_pushNotificationService, SIGNAL(errorRegisteringForPushNotifications(const QString &)), this, SLOT(errorRegisteringForPushNotifications(const QString &)));  

Example of callback functions:  


    void App::registeredForPushNotifications(const QString & token)  
    {  
        qDebug() << "subscribed for push with token: " << token;  
    }  
    
    void App::errorRegisteringForPushNotifications(const QString & error)  
    {  
        qDebug() << "error subscribing for push: " << error;  
    }  

4.Initialize push session:  


    m_pushNotificationService.createSession();  

5.Subscribe for push notifications:  


    m_pushNotificationService.registerForPushNotifications();  

6.Handle push notifications:  


    void App::onInvoked(const InvokeRequest &request)  
    {  
        if (request.action().compare(BB_PUSH_INVOCATION_ACTION) == 0) {  
            qDebug() << "Received push action";  
            // Received an incoming push  
            // Extract it from the invoke request and then process it  
            PushPayload payload(request);  
            if (payload.isValid()) {  
            	//call pushwoosh handler for stats etc.  
                m_pushNotificationService.pushNotificationHandler(pushPayload);  
    
                //handle push  
            }  
        } else if (request.action().compare(BB_OPEN_INVOCATION_ACTION) == 0){  
            qDebug() << "Received open action";  
            // Received an invoke request to open an existing push (ie. from a notification in the BlackBerry Hub)  
            // The payload from the open invoke is the seqnum for the push in the database  
            // Process opened push here  
        }  
    }  

7.In bar-description.xml add permissions for push and device info:  


    <permission system="true">_sys_use_consumer_push</permission>  
    <permission>read_device_identifying_information</permission>  
    <permission>post_notification</permission>  

Also add Invoke actions:  


    <!-- Need to put an invoke entry here for push -->  
    <!-- The id here must match the invokeTargetID in the PushService constructor -->  
    <invoke-target id="INVOKE_TARGET_KEY_PUSH">  
      <type>APPLICATION</type>  
      <filter>  
         <action>bb.action.PUSH</action>  
         <mime-type>application/vnd.push</mime-type>  
      </filter>  
    </invoke-target>  

    <!-- Have an invoke entry here for when a notification is clicked on in the BlackBerry Hub -->  
  	<!-- This will cause the application to be opened -->  
    <invoke-target id="INVOKE_TARGET_KEY_OPEN">  
      <type>APPLICATION</type>  
      <filter>  
         <action>bb.action.OPEN</action>  
         <mime-type>text/plain</mime-type>  
      </filter>  
    </invoke-target>  


Pushwoosh team  
http://www.pushwoosh.com  
