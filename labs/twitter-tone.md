<div style="page-break-after: always;"></div>
### Lab - twitter emtional tone

This time, you can build a fun, simple application that uses the Watson Tone Analyzer service.

You will need an instance of the Watson Tone Analyzer service to created from the IBM Cloud catalog.
Switch to the IBM Cloud dashboard, select `Catalog` from the menu bar (top-right), and add " Tone" to the filter search argument.

![tone-select](img/qcon-watson-tone-analyzer.png)

Click on `Tone Analyzer` and create a new instance, accepting all defaults.

When the instance has been provisioned, you'll be presented with the service management panel -- select the `Service credentials` option from the left navigator.
![tone-menu](img/qcon-tone-analyzer-menu.png).

Then find and open the `View credentials` option:
![tone-creds](img/qcon-tone-credentials.png)

Take a note of where the `username` and `password` fields are, as you will need to copy/paste these values into the Node-RED Tone Analyzer node, shortly.

The basic application flow is based on the following Cognitive Application Reference Architecture:

![CogRefArch](img/CogRefArch.jpg)


Let’s say we want to know about people’s general opinion on a specific topic on Twitter. We can use a `twitter in` node to obtain tweet feeds, then send the content to Watson Tone Analyzer service. When we get the analytic result, we will keep track of the result over time and visualize the average score of each index - similar to the following:

![dash-sample](img/qcon-dash-sample-fred.png)

To produce the sample chart, you will need an additional package to be added to the Node-RED palette - `node-red-dashboard`.

From the Node-RED menu ![menu](img/node-red-menu-hamburger.png) (top-right), select `Manage palette`.

Click on the `Install` tab and enter "node-red-dashboard" in the `search modules` field.

![dash-install](img/qcon-dash-install.png)

Click the `install` button.

All being well, you should see the additonal nodes being dynamically added to the list of available nodes:
![dash-add](img/qcon-dash-install-result.png)

You'll find these new nodes towards the bottom of the left-side palette menu.

Once more, create a new flow on the canvas with the following nodes:

![stage3-flow](img/qcon-stage3-flow.png)

The first time you use a `twitter` node, the application will need to be authorized to use Twitter (inbound and, optionally, outbound) using your Twitter credentials. This can be very simple if you have used Twitter through the same browser as you're using to access Node-RED:

1. in the `twitter` configuration panel, click the edit ![edit-twitter](img/qcon-config-edit-icon.png) icon
1. when you see following prompt in a new tab or window, click  `Click here to authenicate with Twitter`
![edit-auth-add](img/qcon-twitter-auth-add.png)
1. you'll be asked to authorise the Node-RED application - click `Authorize app`
![edit-auth-app](img/qcon-twitter-auth-app.png)
1. you should receive a message similar to `Authorised - you can close this window and return to Node-RED`  -- close the tab or window, and return to the Node-RED editor view
1. click `Add`
1. click `Done` and you're good to go with Twitter integration.

For this Lab we will use **#LinuxingInLondon** as the Twitter topic to monitor.

Since this topic is VERY active at the moment, we have to avoid the flood of the incoming tweets overwhelming the Node-RED instance. Add a `delay` node after the `twitter` node and setting the node action to “Limit rate to” 1 message per 1 seconds - this should leave sufficient time for the Watson Tone Analyzer service to handle each request.

You will need to fill in the service credentials (username and password) from the Watson Tone Analyzer service that you created earlier.

In this example, the purpose is to extract emotion tones, so select the `Emotion` option in the `Tones` field.

![tone-config](img/qcon-tone-config.png)

Since the result from the Watson Tone Analyzer will be in `msg.response`, we can add a `change` node to move `msg.response` to `msg.payload` to make it easier to process in subsequent nodes.

In a `function` node named “Add tweet scores to total”, the scores can be accummulated using the following code:

```
var defaultResult = {
  "emotion_tone":{
    "Anger":  0,
    "Disgust":0,
    "Joy":    0,
    "Sadness":0,
    "Fear":   0
  },
  "count":  0
}

if(msg.payload) {

  var result = context.get('twitterAnalysis')||defaultResult;

  msg.payload.document_tone.tone_categories.forEach(function(toneCategory){
    if(toneCategory.tones){
      toneCategory.tones.forEach(function(tone){
        result[toneCategory.category_id][tone.tone_name] += tone.score;
      })
    }
  })
  result.count += 1;

  context.set('twitterAnalysis', result);

  return {payload:result};
}
```

Here, we add the scores of each tone and then save into a context variable named “twitterAnalysis”. A track of the count is kept, for the next node to use in calculating avergages.

The next `function` node calculates averages with this code:

```
if(msg.payload.count){

  msg.type = "newMsg";

  for (var toneCategory in msg.payload) {
    if (!msg.payload.hasOwnProperty(toneCategory)) continue;
    var obj = msg.payload[toneCategory];
    for (var prop in obj) {
      if(!obj.hasOwnProperty(prop)) continue;
      obj[prop] = obj[prop]/msg.payload.count;
    }
  }
}

return msg;
```

In order to show the scores of each tone, each score needs to be moved into different topics before sending to the dashboard `chart` node. We use the `change` node to do this; here is an example for the "Anger" score:

![tone-score-anger](img/qcon-tone-change-score.png)

Note that the second `Set` section has been added using the `+ add` button lower-left.

Modify the other `change` nodes in a similar way to reflect "Joy", "Sadness", "Disgust" and "Fear"

The `chart` node will need some configuration to allow it to display the rolling average of the 5 different emotion categories.

![chart-config](img/qcon-chart-config.png)

Click the edit ![edit-icon](img/qcon-config-edit-icon.png) button to establish a default dashboard for the chart.

![dash-default](img/qcon-dashboard-config-default.png)

and click `Add`.

Make sure the `Legend` option is updated to `Show` so you can distinguish between the various lines.

![chart-show](img/qcon-chart-config-show.png)

Once you redeploy the application, following the link to the Dashboard

![dash-link](img/qcon-dash-link.png)

and you should see the chart beginning to build:

![chart-results](img/qcon-emotion-chart.png)

*_Congratulations!_* - you have completed the process of integrating live Twitter with Watson's natural language processing capabilities for identifying emtional tone.
