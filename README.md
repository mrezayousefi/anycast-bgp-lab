# 🌐 Anycast + BGP Simulation with Docker & FRRouting

این پروژه یک محیط شبیه‌سازی‌شده برای یادگیری و تست **Anycast IP** همراه با **BGP Routing** به کمک ابزارهای **Docker** و **FRRouting (FRR)** است.

## 🧩 سناریو

```
                      +------------------+
                      |     Client       |
                      |  (Docker Node)   |
                      +--------+---------+
                               |
                        Routed to Anycast IP
                               |
        +----------------------+----------------------+
        |                                             |
+---------------+                           +----------------+
|  Router 1     |                           |  Router 2      |
|  (FRRouting)  |                           |  (FRRouting)   |
+------+--------+                           +--------+-------+
       |                                             |
+------v------+                              +-------v------+
|  Web Server 1|                              | Web Server 2 |
|   (NGINX)    |                              |   (NGINX)    |
+-------------+                              +--------------+

Both Web Servers have the same Anycast IP.
```

## 📁 ساختار پروژه

```
anycast-bgp-lab/
├── dockerfile/            # ساخت image nginx
│   ├── Dockerfile
│   └── start.sh
├── client/                # کلاینت برای ارسال درخواست HTTP
│   └── docker-compose.yml
├── nginx/                 # شامل دو وب‌سرور با IP مشترک
│   ├── docker-compose.yml
│   ├── web1/
│   │   ├── html/index.html
│   │   └── nginx.conf
│   └── web2/
│       ├── html/index.html
│       └── nginx.conf
└── routers/               # شامل دو روتر BGP با FRRouting
    ├── docker-compose.yml
    ├── router1/
    │   ├── frr.conf
    │   ├── daemons
    │   └── vtysh.conf
    └── router2/
        ├── frr.conf
        ├── daemons
        └── vtysh.conf
```

## ⚙️ پیش‌نیازها

- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- آشنایی مقدماتی با networking، BGP و مفاهیم Anycast

## 🚀 نحوه اجرا

### 0. ساخت ایمیج NGINX با نام `nginxnew`

قبل از اجرای کامپوز‌ها، ابتدا باید یک ایمیج NGINX اختصاصی با نام `nginxnew` بسازید. این ایمیج برای راه‌اندازی وب‌سرورهای Anycast استفاده می‌شود.

```bash
cd dockerfile
docker build -t nginx-devops .
```

### 1. اجرای سرورهای NGINX

```bash
cd ../nginx
docker-compose up -d
```



### 2. اجرای روترها


```bash
cd ../routers
docker-compose up -d
```


### 3. اجرای کلاینت

```bash
cd ../client
docker-compose up -d
```

## 🧪 تست عملکرد

### اتصال از داخل کلاینت

```bash
docker exec -it client bash
curl http://192.168.10.100
```

بر اساس مسیرهای BGP، کلاینت به نزدیک‌ترین سرور هدایت می‌شود.

## 📡 تعریف Anycast؟

مفهوم Anycast به حالتی گفته می‌شود که چندین سرور یک IP یکسان دارند، و بسته‌ها به‌صورت **مسیر کوتاه‌تر** به نزدیک‌ترین سرور از نظر BGP ارسال می‌شوند.


## 🛰️بررسی  FRRouting و BGP


یک پکیج مسیریابی منبع‌باز است که از پروتکل‌هایی مثل BGP، OSPF، و IS-IS پشتیبانی می‌کند.

## 🧠 موارد قابل آزمایش

- خاموش کردن یک روتر و مشاهده failover
- تغییر دادن local preference یا AS path برای تاثیر بر انتخاب مسیر
- بررسی اینکه در صورت delay مصنوعی، کدام مسیر انتخاب می‌شود


## 👨‍💻 توسعه‌دهنده

این پروژه توسط **محمدرضا** طراحی شده است – با هدف یادگیری عملی مفاهیم شبکه و BGP به کمک ابزارهای روز مثل Docker.
