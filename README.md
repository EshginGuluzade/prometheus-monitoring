# Part 1 - Prometheus and Node Exporter

## 1. Setting Up Prometheus 

**What is Prometheus?**

- It is an open-source systems monitoring and alerting toolkit which collects and aggregates metrics as time series data. It stores a variety of events such as memory consumption, CPU and network uitilization, indiviudal incoming/outgoing requets and etc. 

I will install prometheus in my Linux Machine - CentOS which runs in [Google Cloud](https://cloud.google.com/) but you can use any Linux distribution that is on the cloud or in your local envrionment. Promtheus has different ways for installation but for a sake of simplicity, I will use pre-compiled binary files. Let's start.

1.


**What is Node Exporter?** 

- There are a lot of exporters which are provided by Prometheus itself. Exporters are used to expose metrics so that Prometheus can collect. Node Exporter is one of the simple exporters out there and it is used to collect basic system metrics from the Linux machine. You can also write your own exporter to expose your application's metrics but it is out of scope of this tutorial.


