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


**2.**  Create directory called **prometheus** and extract the downloaded file to that directory. Then you can delete zip file.

``` bash
mkdir prometheus
tar xvfz prometheus-2.37.2.linux-amd64.tar.gz -C prometheus
rm -f  prometheus-2.37.2.linux-amd64.tar.gz
```

![Screenshot 2022-11-06 at 16 13 30](https://user-images.githubusercontent.com/67023632/200175793-77bd4764-cb3f-4e57-9489-1a658a59f3b3.png)


**3.** Go to extracted directory and start the prometheus server using this command `./prometheus`.

``` bash
cd prometheus/prometheus-2.37.2.linux-amd64/
./prometheus
```

![Screenshot 2022-11-06 at 16 27 16](https://user-images.githubusercontent.com/67023632/200176521-56727b15-8954-40e9-abbf-82f674a9a697.png)

**4.** Prometheus will start listening for connections at default port **9090**. 

**5.** If you use local linux machine you can access Prometheus UI here `http://localhost:9090/`. For this tutorial, I used google cloud that's why prometheus will be accessed using external IP of the instance. In my case, it is `https://34.88.246.220:9090/`

![Screenshot 2022-11-06 at 16 57 20](https://user-images.githubusercontent.com/67023632/200178177-0a58d9f3-76f0-49c4-b02b-05449314b4a9.png)

**6.** However, none of these adresses will not work because regardless of VM is on the cloud or not you need to add firewall rule to allow port 9090.

If you use local linux machine, run below commands:

``` bash
firewall-cmd --add-port 9090/tcp
firewall-cmd --permanent --add-port 9090/tcp
systemctl restart firewalld
```

If you use google cloud, follow these steps:

1. Go to VPC network/Firewall.

![Screenshot 2022-11-06 at 16 51 20](https://user-images.githubusercontent.com/67023632/200177832-289e253a-f356-401f-838a-1a01248d6471.png)


2. Create a new firewall rule.

![Screenshot 2022-11-06 at 16 55 48](https://user-images.githubusercontent.com/67023632/200178090-3def84a5-058a-46e7-9662-f4b60da748bd.png)

3. Use below configuration and press *create* button.

![Screenshot 2022-11-06 at 17 02 55](https://user-images.githubusercontent.com/67023632/200178509-7f811553-5937-4a89-810b-1735f4bcf2d2.png)

4. Go to external IP of the instance using port 9090. In my case, it is `https://34.88.246.220:9090/`

![Screenshot 2022-11-06 at 17 07 15](https://user-images.githubusercontent.com/67023632/200178751-9c666d63-8c6e-4fa4-8715-e5e52d9b0e1c.png)


**What is Node Exporter?** 

- There are a lot of exporters which are provided by Prometheus itself. Exporters are used to expose metrics so that Prometheus can collect. Node Exporter is one of the simple exporters out there and it is used to collect basic system metrics from the Linux machine. You can also write your own exporter to expose your application's metrics but it is out of scope of this tutorial.


