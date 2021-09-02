---
layout: post
title: Streaming real-time Telegraf metrics using Grafana Live
---

A typical IoT application with any physical system or process in the field can have hundreds of on-site sensors generating copious amounts of data every second and possibly communicating in several different protocols.

Most of the time it is very useful to have a short-term, high resolution stream of those sensors’ data to a visualization tool like Grafana, but at the same time scraping and <!-- more -->aggregating that data in real time along with the ability to store them to a time series database like InfluxDB is also necessary.

Most of the applications for this use case leverage the Telegraf agent for the scraping and metricising of the sensor data via its ecosystem of input plugins. However, there was no easy way to stream those Telegraf metrics to Grafana until the recent v8.0 release, which introduced the HTTP Push end-point under the Grafana Live feature.

In a previous blog post, we used an IMU-based sensor system communicating over the MQTT protocol to stream real-time sensor data to Grafana Live. Here, we will use the same setup to demonstrate the ease of use of the HTTP Push end-point to stream Telegraf metrics using Grafana Live.

[Continue reading...](https://grafana.com/blog/2021/08/16/streaming-real-time-telegraf-metrics-using-grafana-live/)
