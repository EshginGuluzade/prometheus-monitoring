# Part 1 - Prometheus and Node Exporter

## 1. Setting Up Prometheus 

### What is Prometheus?

- It is an open-source systems monitoring and alerting toolkit which collects and aggregates metrics as time series data. It stores a variety of events such as memory consumption, CPU and network uitilization, indiviudal incoming/outgoing requets and etc. 

I will install prometheus in my *Linux Machine - CentOS* which runs in [Google Cloud](https://cloud.google.com/) but you can use any Linux distribution that is on the cloud or in your local envrionment. Promtheus has different ways for installation but for a sake of simplicity, I will use **pre-compiled binary files**. Let's start.

### Installation Steps

**1.** From [Prometheus Download](https://prometheus.io/download/) page, download latest stable version. At the time of writing this tutorial, latest stable version is 2.37.2.

![Download Page](https://user-images.githubusercontent.com/67023632/200174808-2a09e7f1-d069-4c04-bd12-b2754adc1c2d.png)

You can use `wget` command to download the compressed file. Copy the link address of the file that you want to download and then run the command in your terminal.

![install binary](https://user-images.githubusercontent.com/67023632/200175277-5909e240-4ad1-4d1a-a743-50b407c5f183.png)

![Screenshot 2022-11-06 at 16 08 13](https://user-images.githubusercontent.com/67023632/200175512-4244151c-62a0-45be-9e89-59b55f839d8c.png)




**2.**  Create directory called **prometheus** and extract the downloaded file to that directory.

`mkdir prometheus`. 
`tar xvfz prometheus-2.37.2.linux-amd64.tar.gz -C prometheus`. 

![Screenshot 2022-11-06 at 16 13 30](https://user-images.githubusercontent.com/67023632/200175793-77bd4764-cb3f-4e57-9489-1a658a59f3b3.png)

**What is Node Exporter?** 

- There are a lot of exporters which are provided by Prometheus itself. Exporters are used to expose metrics so that Prometheus can collect. Node Exporter is one of the simple exporters out there and it is used to collect basic system metrics from the Linux machine. You can also write your own exporter to expose your application's metrics but it is out of scope of this tutorial.


