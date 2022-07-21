# CloudEvents

CloudEvents is a specification for describing event data in common formats to provide interoperability across services, platforms and systems.
[see spec](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md)

Mandatory fields include:
* specversion
* source
* type
* id

## Proposed Mapping for Eclipse Ditto

An example of an incoming CloudEvent to modify a thing in ditto could be

```
{

   "specversion" : "1.0",
    "type" : "com.something.ditto",
    "source" : "http://sensor.com/sensor01",
    "subject" : "commands/modify",
	"id" : "my.sensor:sensor01",
    "datacontenttype": "application/CloudEvents"
    “data”:{  
	  "channel":"twin",
	  "entity":"things",   
      "path":"/features/car/properties/temperature/value",  
      "value": 54  
  }

}
```


which would be mapped to ditto protocol and look like this:

```
{  
      "topic": "my.sensors/sensor01/things/twin/commands/modified",  
      "headers": { "content-type": "application/CloudEvents" },  
      "path":"/features/car/properties/temperature/value",  
      "value": 54  
  }

```

A similar spec can be made for modifying policy

An incoming CloudEvent for modifying policy could be:
```
{

   "specversion" : "1.0",
    "type" : "com.something.ditto",
    "source" : "http://sensor.com/sensor01",
    "subject" : "commands/modify",
	"id" : "org.eclipse.ditto:the_policy_id",
    "datacontenttype": "application/CloudEvents"
    “data”:{  
	  "channel":"twin",
	  "entity":"policies",   
      "path":"/",  
      "value":{
         "entries": {
            "the_label": {
                "subjects": {
                     "google:the_subjectid": {
                        "type": "yourSubjectTypeDescription"
                         }
                    },
      "resources": {
          "thing:/the_resource_path": {
            "grant": [
              "READ",
              "WRITE"
            ],
            "revoke": []
          }
        }
      }
     }
   }
}
```

Would look like

```
{
  "topic": "org.eclipse.ditto/the_policy_id/policies/commands/modify",
  "headers": {
    "correlation-id": "<command-correlation-id>"
  },
  "path": "/",
  "value": {
    "policyId": "org.eclipse.ditto:the_policy_id",
    "entries": {
      "the_label": {
        "subjects": {
          "google:the_subjectid": {
            "type": "yourSubjectTypeDescription"
          }
        },
        "resources": {
          "thing:/the_resource_path": {
            "grant": [
              "READ",
              "WRITE"
            ],
            "revoke": []
          }
        }
      }
    }
  }
```