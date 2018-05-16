<div style="page-break-after: always;"></div>
### Lab - remote data presentation

Now your Node-RED application can service web requests, you can add support for integrating local and remote data into your responses.

In this Lab, the goal will be to take an incoming HTTP request, from a browser, retrieve data from a remote application API, and return part of the data (reformatted) to display in the browser (i.e basic HTML).

1. Add the necessary nodes
1. Configure the nodes to support inbound HTTP, invoke API, and return content
1. Deploy
1. Test and validate via **_https://{{your-node-red-hostname}}_/stage2**

The source for the remote data is a very handy, simple JSON API server provided by [@typicode](http://twitter.com/typicode)
**https://jsonplaceholder.typicode.com/** - the /users API returns a reasonable amount of data for demonstration purposes:

![typicode-users](img/qcon-jsonserver-users.png)

As before, add a new flow to the canvas:
![stage2-flow](img/qcon-stage2-flow.png)

A small, but important difference to note this time - instead of setting the `msg.payload` property, the `change` node is placing the target API url into the `msg.url` property. This is used to dynamically configure the `http request` node.
![stage2-change](img/qcon-stage2-change-url.png)

The `http request` node does not need any specific configuration for this flow.

The response from https://jsonplaceholder.typicode.com/users is a string; to process more easily and efficently in Node-RED, we pass it through a `JSON` node which will parse the string into a JSON object (in this case an array of *user* objects).

To add a little sophistication to the response content, the two `template` nodes allow generation of dynamic HTML, and support the creation of inline CSS.

The first `template` sets a msg property `msg.css` to some simple CSS for imbedding in the returned HTML:

![stage2-css](img/qcon-stage2-template-css.png)
```
.col {
    display: inline-block;
    width: 150px;
    vertical-align: top;
}
.address {
    display: inline-block;
    width: 200px;
}
.title {
    font-weight: bold;
}
```

The second `template` node creates a tabular response from the users array, and imbeds the CSS:
![stage2-html](img/qcon-stage2-template-html.png)


    <style> {{{css}}} </style> <!-- imbed styling from msg.css -->
    <div>
      <span class="col title user">Username</span>
      <span class="col title address">Location</span>
      <span class="col title website">Website</span>
    </div>
    {{#payload}}
    <div>
      <span class="col user">{{username}}</span>
      <span class="col address">
      {{#address}}
      <details>
        <summary>
        <a target="_blank"
        href="https://www.google.com/maps/search/?api=1&query={{geo.lat}},{{geo.lng}}">
        {{city}}
        </a>
        </summary>
        {{street}}<br>{{city}}<br>{{zipcode}}
      </details>
      {{/address}}
    </span>
    <span class="col website">
    <a href="http://{{{website}}}">{{website}}</a></span>
    </div>
    {{/payload}}


Note that although the response will generate links to **Google Maps** locations, the data appears to point to random places.

And again, use the ![deploy-button](img/qcon-deploy-go.png) button to update the runtime.

Now to test ...
**_https://{{your-node-red-hostname}}_/stage2**

You should see a response like:
![stage2-result](img/qcon-stage2-result.png)

*_Congratulations!_* - you have completed the second stage of establishing Node-RED as a web server application, and responding to HTTP requests with dynamically sourced data.
