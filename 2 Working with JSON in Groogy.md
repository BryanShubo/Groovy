* Parsing JSON messages with JsonSlurper
* Constructing JSON messages with JsonBuilder
* Modifying JSON messages
* Validating JSON messages
* Converting JSON message to XML
* Converting JSON message to Groovy Bean
* Using JSON to configure your scripts


####1. Parsing JSON messages with JsonSlurper

```
{
    "items":[
        {   "type":"chart",
            "height":270,
            "width":319,
            "animate":true,
            "insetPadding":20,
            "axes":[
                {
                    "type":"Time",
                    "fields":[ "x" ],
                    "position":"left",
                    "title":"Time"
                },
                {
                    "type": "Numeric",
                    "fields": [ "y" ],
                    "position": "bottom",
                    "title": "Profit in EUR"
                }
                ],
            "series":[
                {
                    "type":"bar",
                    "label":{
                        "display":"insideEnd",
                        "field":"y",
                        "color":"#333",
                        "text-anchor":"middle"
                    },
                    "axis":"bottom",
                    "xField":"x",
                    "yField":[ "y" ]
                }
            ]
        }
    ]
}
```

```

```
