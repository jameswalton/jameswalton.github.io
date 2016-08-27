---
layout: post
title: "Publishing temperature reading from a DS18B20 1-Wire digital temperature sensor using a Raspberry Pi and node.js"
date: 2013-04-30 14:07
comments: true
categories:
- RaspberryPi
- temperature
- DS18B20
- node.js
---
This is a follow up to my previous post detailing how to get temperature readings using a Pi and the DS18B20 digital temperature sensor. For this part I am going to expose the temperature readings via a JSON feed using node.js to serve the response.

On a request for /temperature.json the application reads the contents of w1_slave as utf-8, which is the data of the 1-Wire digital thermometer connected to pin #4. A regex is then used to extract the temperature reading before forming an application/json response for the temperature in both celcius and fahrenheit.

{% highlight js %}

var http = require('http');
var fs = require('fs');
var url = require('url');
var fileLocation = '/sys/bus/w1/devices/28-000003b74282/w1_slave';

http.createServer(function (req, res) {
  var request_url = url.parse(req.url).pathname;

  if (request_url == '/temperature.json') {
    fs.readFile(fileLocation, 'utf8', function(err, data) {
      if (err) throw err;
      matches = data.match(/t=([0-9]+)/);
      temperatureC = parseInt(matches[1]) / 1000;
      temperatureF = ((temperatureC * 1.8) + 32).toFixed(3);
      output = '{ "temperature": { "celcius": '+ temperatureC +', "fahrenheit": '+ temperatureF +' } }';
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(output);
    });
  } else {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('No route');
  }
}).listen(8124, "0.0.0.0");

console.log('Server running on port 8124');
{% endhighlight %}

A working example, providing my Pi hasn't been fried, can be found at [http://pi.jam.im:8124/temperature.json](http://pi.jam.im:8124/temperature.json).
