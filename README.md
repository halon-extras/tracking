# User engagement tracking

Rewrite URL:s and add tracking pixels to track user engagement.

This HSL module is compatible with the [payload format](https://docs.halon.io/submission/tracking_payload.html) used by the extended version of the [HTTP submission API](https://docs.halon.io/submission/).



## Installation

Follow the [instructions](https://docs.halon.io/manual/comp_install.html#installation) in our manual to add our package repository and then run the below command.

### Ubuntu

```
apt-get install halon-extras-tracking
```

### RHEL

```
yum install halon-extras-tracking
```

## Exported functions

These functions needs to be [imported](https://docs.halon.io/hsl/structures.html#import) from the `extras://tracking` module path.

### enable_tracking(mail, recipient, tracking)

Enables user engagement tracking for the recipient.

**Params**

- mail `MailMessage` - The mail object **Required**
- recipient `string` - The recipient **Required**
- tracking `array` - The tracking settings **Required**

The following properties are available in the **tracking** array.

- url `string` - The base url that should be used for the rewritten links and tracking pixels **Required**
- secret `array` - The secret settings **Required**
- click `boolean` - If click tracking should be enabled
- open `boolean` - If open tracking should be enabled
- unsubscribe `boolean` - If one-click list unsubscribe should be enabled
- additional `array` - Additional data to store in the link

The following properties are available in the **secret** array.

- id `number` - The ID of the secret that should be used to generate the hash of the tracking payload. This must be an integer between `1` and `254` **Required**
- key `string` - The key for the secret that should be used to generate the hash of the tracking payload **Required**

## Example (EOD)

```
import { enable_tracking } from "extras://tracking";

$tracking = [
    "click" => true,
    "open" => true,
    "unsubscribe" => false,
    "url" => "http://example.com",
    "secret" => [
        "id" => 1,
        "key" => "badsecret"
    ],
    "additional" => ["foo" => "bar"]
];

// Queue message for all recipients
$id = $mail->snapshot();
foreach ($recipients as $recipient) {
    enable_tracking($mail, $recipient["recipient"], $tracking);
    $mail->queue($sender, $recipient["address"], $recipient["transportid"]);
    $mail->restore($id);
}
```

> [!WARNING]
> This requires multipart processing to be enabled on your message flow in `smtpd-app.yaml` - see [here](https://docs.halon.io/manual/config_tuning.html?highlight=multipart#servers-phases-data-multipart). 