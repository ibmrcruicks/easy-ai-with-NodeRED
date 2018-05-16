<div style="page-break-after: always;"></div>
### Lab - enable Node-RED as a server application

Now you have a working Node-RED instance, you can add support for external applications (browsers, other applications on mobile devices, cloud apps, etc) to invoke services within Node-RED, to exchange information.

In this Lab, the goal will be to add support for incoming HTTP requests, from a browser, to request data be returned to display in the browser (i.e basic HTML).

1. Add the necessary nodes
1. Configure the nodes to support inbound HTTP and return content
1. Deploy
1. Test and validate via **_https://{{your-node-red-hostname}}_/stage1**

Using the techniques from the previous Lab, add a new flow to the canvas:
![stage1-flow](img/qcon-hello-world-stage1-flow.png).

Configure the `http` (inbound) node to accept the url _/stage1_:
![http-config](img/qcon-hello-world-http-stage1.png)

Configure the `change` node to set the response text, by chnaging `msg.payload` to the desired message.
![http-config](img/qcon-hello-world-change-stage1.png)

Configure the `template` node to be some simple HTML, and imbed the msg content from the flow, using [mustache templating](https://mustache.github.io/):

![template-stage1](img/qcon-hello-world-template-stage1.png).

The `http response` node does not usually need configuration, but is required as a final stage for returning data to a waiting http requester.

Now `Deploy` the changes.
To test this flow, open a new browser tab or window and load **_https://{{your-node-red-hostname}}_/stage1**.
All being well, you should receive the following response:
![stage1-result](img/qcon-hello-world-stage1-result.png)

Note that if you click on the `Show me more` link, you will receive a response like `Cannot GET /stage2` - that's because "stage2" will be added in the next Lab.

*_Congratulations!_* - you have completed the first stage of establishing Node-RED as a web server application, and responding to HTTP requests with static data.
