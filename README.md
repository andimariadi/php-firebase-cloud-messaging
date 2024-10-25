# PHP Firebase Cloud Messaging

PHP API for Firebase Cloud Messaging from Google.

Currently this app server library only supports sending Messages/Notifications via HTTP.

See original Firebase docs: https://firebase.google.com/docs/

# Migrate from legacy FCM APIs to HTTP v1

See original Firebase Migrate: https://firebase.google.com/docs/cloud-messaging/migrate-v1

- Update the server endpoint

```
POST https://fcm.googleapis.com/v1/projects/{PROJECT_ID}/messages:send
```

# Setup

Install via Composer:

```
composer require andimariadi/php-firebase-cloud-messaging
```

Or add this to your composer.json and run "composer update":

```
"require": {
    "andimariadi/php-firebase-cloud-messaging": "1.*"
}
```

# Send message to **one or multiple** Devices

```
use sngrl\PhpFirebaseCloudMessaging\Client;
use sngrl\PhpFirebaseCloudMessaging\Message;
use sngrl\PhpFirebaseCloudMessaging\Recipient\Device;
use sngrl\PhpFirebaseCloudMessaging\Notification;

$server_key = '_YOUR_SERVER_KEY_';
$server_url = 'https://fcm.googleapis.com/v1/projects/{PROJECT_ID}/messages:send';
$client = new Client();
$client->setApiKey($server_key);
$client->setProxyApiUrl($server_url);
$message = new Message();
$message->addRecipient(new Device('_YOUR_DEVICE_TOKEN_'));
$message
    ->setNotification(new Notification('some title', 'some body'))
    ->setData(['key' => 'value'])
;

$response = $client->send($message);
var_dump($response->getStatusCode());
var_dump($response->getBody()->getContents());
```

# Send message to Topic

Currently sending to topics only supports a single topic as recipient. Mutliple topic as outlined
in the google docs don't seem to work, yet.

```
use sngrl\PhpFirebaseCloudMessaging\Client;
use sngrl\PhpFirebaseCloudMessaging\Message;
use sngrl\PhpFirebaseCloudMessaging\Recipient\Topic;
use sngrl\PhpFirebaseCloudMessaging\Notification;

$server_key = '_YOUR_SERVER_KEY_';
$server_url = 'https://fcm.googleapis.com/v1/projects/{PROJECT_ID}/messages:send';
$client = new Client();
$client->setApiKey($server_key);
$client->setProxyApiUrl($server_url);

$message = new Message();
$message->addRecipient(new Topic('_YOUR_TOPIC_'));
$message
    ->setNotification(new Notification('some title', 'some body'))
    ->setData(['key' => 'value'])
;

$response = $client->send($message);
var_dump($response->getStatusCode());
var_dump($response->getBody()->getContents());
```

# Subscribe user to the topic

```
use sngrl\PhpFirebaseCloudMessaging\Client;

$server_key = '_YOUR_SERVER_KEY_';
$client = new Client();
$client->setApiKey($server_key);

$response = $client->addTopicSubscription('_SOME_TOPIC_ID_', ['_FIRST_TOKEN_', '_SECOND_TOKEN_']);
var_dump($response->getStatusCode());
var_dump($response->getBody()->getContents());
```

# Remove user subscription to the topic

```
use sngrl\PhpFirebaseCloudMessaging\Client;

$server_key = '_YOUR_SERVER_KEY_';
$client = new Client();
$client->setApiKey($server_key);

$response = $client->removeTopicSubscription('_SOME_TOPIC_ID_', ['_FIRST_TOKEN_', '_SECOND_TOKEN_']);
var_dump($response->getStatusCode());
var_dump($response->getBody()->getContents());
```

# Interpreting responses

Responses given on the HTTP requests are standard according to the FCM documentations. You may find detailed specifications in this links:

- https://firebase.google.com/docs/cloud-messaging/http-server-ref#interpret-downstream
- https://firebase.google.com/docs/cloud-messaging/http-server-ref#error-codes
