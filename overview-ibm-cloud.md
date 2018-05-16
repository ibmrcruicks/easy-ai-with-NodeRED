<div style="page-break-after: always;"></div>
## Things to know about your account

`Lite (Free)`
+ 256M runtime memory, up to 100 service instances
  * sample applications tend to be created with 256M allocated, so will need adjusted if you want to run more than one application at a time
  * service instances by IBM (database, messaging, Watson, etc) will be created with what is call a *Lite Plan*, only one each of which can defined to your account at any time; for example, trying to create a second Watson Speech to Text service instance will fail
  * third party services which have no IBM Cloud charging plan (twilio, elephantSQL, rediscloud, ...) can be added, subject to the provider's restrictions
  * sample applications available in the catalog (shown as "Boilerplates") may try and create instances of services (usually Cloudant database), regardless of whether a similar instance already exists

`Pay-As-You-Go`
+ 512M runtime memory, up to 100 service instances
  * keep using the free services (*Lite Plan*), as well as adding other services which may incur charges after any free use allocation

`Trial (Free)`
+ you have applied a promotion code to your account, usually to experiment with services for which the `Lite (Free)` option is too restrictive; this type of account is time-limited, and must be converted to `Pay-As-You-Go` (or `Subscription`) by the end of the trial period, or it will be suspended.

`Subscription`
+ you've elected to pay monthly upfront - you probably know what you're doing ...
