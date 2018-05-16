<div style="page-break-after: always;"></div>
## Challenge

Now you have a mechanism for processing web requests, handling real-time events from external services, and invoking Watson cognitive services -- what else other easy extensions could you make?

+ check tweets for attached images, and pass the images through the `Watson Visual Recognition` service to identify/classify the image content;

+ add `Watson Text to Speech` to enable the application to read messages out loud as they arrive, as well as the responses from the chatbot, using different voices

+ use the tweet user information to build a profile using `Personality Insights`, and generate tweets to them when "like-minded" twitter profiles are identified.

+ store tweets and responses in data store service (noSQL like Cloudant, or SQL like postgresql/DB2/MySQL/etc)

+ take what you've built in the IBM Cloud, and deploy into a Node-RED application on your Windows/Linux/MacOS laptop, or a [Raspberry PI](https://raspberrypi.org)

+ ...
