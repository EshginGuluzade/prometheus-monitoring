# Part 1 - Prometheus

## 1. Setting Up Prometheus 

### What is Prometheus?

- It is an open-source systems monitoring and alerting toolkit which collects and aggregates metrics as time series data. It stores a variety of events such as memory consumption, CPU and network uitilization, indiviudal incoming/outgoing requets and etc. 

I will install prometheus in my *Linux Machine - CentOS* which runs in [Google Cloud](https://cloud.google.com/) but you can use any Linux distribution that is on the cloud or in your local envrionment. Promtheus has different ways for installation but for a sake of simplicity, I will use **pre-compiled binary files**. Let's start.

### Installation Steps

**1.** From [Prometheus Download](https://prometheus.io/download/) page, download latest stable version. At the time of writing this tutorial, latest stable version is 2.37.2.

![Download Page](https://user-images.githubusercontent.com/67023632/200174808-2a09e7f1-d069-4c04-bd12-b2754adc1c2d.png)

You can use `wget` command to download the compressed file. Copy the link address of the file that you want to download and then run the command in your terminal.


**2.**  Create directory called **prometheus** and extract the downloaded file to that directory. Then you can delete zip file.

``` bash
mkdir prometheus
tar xvfz prometheus-2.37.2.linux-amd64.tar.gz -C prometheus
rm -f  prometheus-2.37.2.linux-amd64.tar.gz
```


**3.** Go to extracted directory and start the prometheus server using this command `./prometheus`.

``` bash
cd prometheus/prometheus-2.37.2.linux-amd64/
./prometheus
```

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

- Go to VPC network/Firewall.

![Screenshot 2022-11-06 at 16 51 20](https://user-images.githubusercontent.com/67023632/200177832-289e253a-f356-401f-838a-1a01248d6471.png)


- Create a new firewall rule.

![Screenshot 2022-11-06 at 16 55 48](https://user-images.githubusercontent.com/67023632/200178090-3def84a5-058a-46e7-9662-f4b60da748bd.png)

- Use below configuration and press *create* button.

![Screenshot 2022-11-06 at 17 02 55](https://user-images.githubusercontent.com/67023632/200178509-7f811553-5937-4a89-810b-1735f4bcf2d2.png)

- Go to external IP of the instance using port 9090. In my case, it is `https://34.88.246.220:9090/`

![Screenshot 2022-11-06 at 17 07 15](https://user-images.githubusercontent.com/67023632/200178751-9c666d63-8c6e-4fa4-8715-e5e52d9b0e1c.png)

- If you execute `up` command, it will show you instances which are in UP state. Right now, we only have one instance and since the prometheus runs on this instance, it will display this nessage `up{instance="localhost:9090", job="prometheus"}`. If we add IP adresses of other instances to configuration file of prometheus, it will display those instances,as well. We will add another instance when we install node exporter in part 2.

![Screenshot 2022-11-06 at 17 17 38](https://user-images.githubusercontent.com/67023632/200179171-44b832a1-92f0-4202-8c03-94ee9daab096.png)

- You can also access Prometheus metrics by visiting `http://localhost:9090/` for local machine or `https://34.88.246.220:9090/` for google cloud instance.

![Screenshot 2022-11-06 at 17 20 24](https://user-images.githubusercontent.com/67023632/200179262-acdbb2cb-a8f6-4683-b326-114b43409287.png)

## 2. Setup Prometheus as Systemd Service [Optional]

**1.** In step 3, we said that in order to start prometheus server we need to use command `./prometheus` from the extracted directory. The problem with this method is that it will keep your terminal running and you will not be able to run other commands in the current terminal session. If you want to run other commands you will need to stop it by `Ctrl + C`. Therefore, to remove this issue, it is better to setup Prometheus as **Systemd service**.

**2.** Create prometheus user, required directories and make prometheus user as owner of the directories.

``` bash
useradd --no-create-home --shell /bin/false prometheus
mkdir /etc/prometheus
mkdir /var/lib/prometheus
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```

**3.** Copy *prometheus* and *promtool* from the extracted directory to */usr/local/bin* and change the ownership to prometheus.

``` bash
cp /root/prometheus/prometheus-2.37.2.linux-amd64/prometheus /usr/local/bin/
cp /root/prometheus/prometheus-2.37.2.linux-amd64/promtool /usr/local/bin/
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```

**4.** Copy *consoles*, *console_libraries* and *prometheus.yml* file from the extracted directory to */etc/prometheus* and change the ownership to prometheus.

``` bash
cp -r /root/prometheus/prometheus-2.37.2.linux-amd64/consoles /etc/prometheus
cp -r /root/prometheus/prometheus-2.37.2.linux-amd64/console_libraries /etc/prometheus
cp /root/prometheus/prometheus-2.37.2.linux-amd64/prometheus.yml /etc/prometheus
chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries
chown prometheus:prometheus /etc/prometheus/prometheus.yml
```

**5.** Create Prometheus service file.

``` bash
vi /etc/systemd/system/prometheus.service
```

**6.** Copy the following content to that file and save.

``` 
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

**7.** Reload the systemd service, enable service to start at boot time and start the service.

``` bash
systemctl daemon-reload
systemctl enable prometheus
systemctl start prometheus
```
**8.** Go to Prometheus Web UI address - `http://localhost:9090/` for local machine or `https://34.88.246.220:9090/` for google cloud instance.


# Part 2 - Node Exporter

## 1. Setting Up Node Exporter

**What is Node Exporter?** 

- There are a lot of exporters which are provided by Prometheus itself. Exporters are used to expose metrics so that Prometheus can collect. Node Exporter is one of the simple exporters out there and it is used to collect basic system metrics from the Linux machine. You can also write your own exporter to expose your application's metrics but it is out of scope of this tutorial.

### Installation Steps

**1.** From [Prometheus Download](https://prometheus.io/download/) page, download latest stable version of node exporter. At the time of writing this tutorial, latest stable version is 1.4.0.

![Screenshot 2022-11-20 at 17 48 59](https://user-images.githubusercontent.com/67023632/202911828-5b8e72a3-dabe-4187-99b9-64b8ec3ef28b.png)

You can use `wget` command to download the compressed file. Copy the link address of the file that you want to download and then run the command in your terminal.

**2.**  Create directory called **node-exporter** and extract the downloaded file to that directory. Then you can delete zip file.

``` bash
mkdir node-exporter
tar xvfz node_exporter-1.4.0.linux-amd64.tar.gz -C node-exporter
rm -f  node_exporter-1.4.0.linux-amd64.tar.gz
```

**3.** Go to extracted directory and start the node-exporter using this command `./node-exporter`.

``` bash
cd node-exporter/node_exporter-1.4.0.linux-amd64/
./node_exporter
```

**4.** Node-exporter will start listening for connections at default port **9100**. 

**5.** If you use local linux machine you can access Node-exporter metrics here `http://localhost:9100/`. For this tutorial, I used google cloud that's why node-exporter will be accessed using external IP of the instance. In my case, it is `https://35.228.93.221:9100/`

![Screenshot 2022-11-20 at 18 04 53](https://user-images.githubusercontent.com/67023632/202912613-6a4b3264-a3cd-49aa-8360-6c343b3dcdfb.png)

**6.** However, none of these adresses will not work because regardless of VM is on the cloud or not you need to add firewall rule to allow port 9100.

If you use local linux machine, run below commands:

``` bash
firewall-cmd --add-port 9100/tcp
firewall-cmd --permanent --add-port 9100/tcp
systemctl restart firewalld
```

If you use google cloud, follow these steps:

- Go to VPC network/Firewall.

![Screenshot 2022-11-06 at 16 51 20](https://user-images.githubusercontent.com/67023632/200177832-289e253a-f356-401f-838a-1a01248d6471.png)


- Create a new firewall rule.

![Screenshot 2022-11-06 at 16 55 48](https://user-images.githubusercontent.com/67023632/200178090-3def84a5-058a-46e7-9662-f4b60da748bd.png)

- Use below configuration and press *create* button.

![Screenshot 2022-11-20 at 18 08 27](https://user-images.githubusercontent.com/67023632/202912871-64653bb7-46c4-48cf-b67a-4dfb297c252b.png)

- Go to external IP of the instance using port 9100. In my case, it is `https://35.228.93.221:9100/`

![Screenshot 2022-11-20 at 18 12 06](https://user-images.githubusercontent.com/67023632/202912985-088ddbd7-59fb-4479-ad57-15e4da752b76.png)

- You can also access Node Exporter by visiting `http://localhost:9100/` for local machine or `https://35.228.93.221:9100/` for google cloud instance.

## 2. Setup Node-exporter as Systemd Service [Optional]

**1.** In step 3, we said that in order to start node-exporter we need to use command `./node_exporter` from the extracted directory. The problem with this method is that it will keep your terminal running and you will not be able to run other commands in the current terminal session. If you want to run other commands you will need to stop it by `Ctrl + C`. Therefore, to remove this issue, it is better to setup Node-exporter as **Systemd service**.

**2.** Create node_exporter user, required directory and make node-exporter user as owner of the directory.

``` bash
useradd --no-create-home --shell /bin/false node_exporter
mkdir /etc/node_exporter
chown node_exporter:node_exporter /etc/node_exporter
```

**3.** Copy *node_exporter* from the extracted directory to */usr/local/bin* and change the ownership to prometheus.

``` bash
cp /root/node-exporter/node_exporter-1.4.0.linux-amd64/node_exporter /usr/local/bin/
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

**4.** Create node_exporter service file.

``` bash
vi /etc/systemd/system/node_exporter.service
```

**5.** Copy the following content to that file and save.

``` 
[Unit]
Description=Node-Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

**6.** Reload the systemd service, enable service to start at boot time and start the service.

``` bash
systemctl daemon-reload
systemctl enable node_exporter
systemctl start node_exporter
```
**7.** Go to Node Exporter address - `http://localhost:9090/` for local machine or `https://35.228.93.221:9090/` for google cloud instance.

## 3. Configure Node Exporter Host as Target on Prometheus Host

**1.** Open `/etc/prometheus/prometheus.yml` file and paste following content to that file

``` bash
  - job_name: "node-exporter"
    scrape_interval: 5s
    static_configs:
      - targets: ["35.228.98.148:9100"]
```

**2.** Restart prometheus service

``` bash
systemctl restart prometheus
```

