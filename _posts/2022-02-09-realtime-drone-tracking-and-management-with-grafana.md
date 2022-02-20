---
layout: post
title: Real-time drone tracking and management with Grafana
---

The number of internet-connected assets around us that are powering services and utilities in a wide array of sectors is rising at an exponential rate. As a result, it’s becoming critical <!-- more -->for businesses that provide such services and utilities to have an observability stack tailored to the type of physical hardware devices that are generally deployed in swarms. Historically, two major features supporting such applications — real-time sensor data ingestion and manual remote control of hardware — have not been supported natively in Grafana. Those capabilities, however, are absolutely essential for physical observability applications.

Addressing that issue is the goal of the Edge team here at Grafana Labs. 

Looking at the world of software observability (which is inspired by the concept of system observability in dynamic mechanical systems), we can easily draw key parallels to hardware observability when it comes to a number of applications: autonomous inspection drones, delivery robots, manufacturing rigs, distributed power plants, and more. The golden triangle of observability for software monitoring — metrics, logs, and traces — roughly translates to real-time sensor data, long-term aggregate data retention, and threshold-based alarms for hardware-based applications. An additional and distinct necessity is supervisory control for the hardware, which has traditionally not been a major part of the observability stack in the world of software monitoring.

[Continue reading...](https://grafana.com/blog/2022/02/09/real-time-drone-tracking-and-management-with-grafana/)
