# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

> 1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model `struct` is enough? 

Dalam kasus BambangShop, satu Model struct sudah cukup karena semua subscriber memiliki perilaku yang sama: menerima notifikasi melalui HTTP request ke URL mereka masing-masing. Berbeda dengan Observer pattern pada umumnya, di sini tidak ada variasi perilaku antar subscriber yang memerlukan abstraksi tambahan. Namun jika ke depannya ada jenis subscriber baru dengan cara menerima notifikasi yang berbeda (misalnya lewat email atau SMS), barulah trait diperlukan agar kode tetap mengikuti Open-Closed Principle.

> 2. `id` in `Program` and `url` in `Subscriber` is intended to be unique. Explain based on your understanding, is using `Vec` (list) sufficient or using `DashMap` (map/dictionary) like we currently use is necessary for this case? 

DashMap lebih tepat digunakan di sini karena id pada Program dan url pada Subscriber bersifat unik, sehingga struktur map (key-value) lebih sesuai daripada list. Dengan DashMap, pencarian dan penghapusan subscriber berdasarkan url bisa dilakukan dalam O(1), sedangkan dengan Vec harus melakukan iterasi linear O(n). Selain itu, DashMap mendukung concurrent access secara thread-safe, yang penting karena aplikasi ini menangani banyak request secara bersamaan.

> 3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (`SUBSCRIBERS`) static variable, we used the `DashMap` external library for **thread safe** `HashMap`. Explain based on your understanding of design patterns, do we still need `DashMap` or we can implement Singleton pattern instead? 

Keduanya sebenarnya diperlukan sekaligus dan memenuhi peran yang berbeda. Singleton pattern menjamin bahwa hanya ada satu instance dari daftar SUBSCRIBERS sepanjang lifetime aplikasi, sehingga semua bagian program mengakses data yang sama. Namun Singleton saja tidak menjamin keamanan akses dari banyak thread secara bersamaan, sehingga DashMap tetap diperlukan sebagai struktur data yang thread-safe di dalam Singleton tersebut. Dengan kata lain, Singleton menyelesaikan masalah "satu instance", sedangkan DashMap menyelesaikan masalah "akses concurrent yang aman".

#### Reflection Publisher-2

> 1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Dalam MVC original, Model menanggung terlalu banyak tanggung jawab sekaligus: menyimpan data, menjalankan business logic, dan mengakses database. Hal ini melanggar Single Responsibility Principle, karena sebuah class seharusnya hanya punya satu alasan untuk berubah. Dengan memisahkan Service (business logic) dan Repository (akses data), setiap lapisan hanya bertanggung jawab pada satu hal, sehingga perubahan pada cara penyimpanan data tidak mempengaruhi business logic, dan sebaliknya. Pemisahan ini juga membuat masing-masing lapisan jauh lebih mudah diuji secara independen.

> 2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (`Program`, `Subscriber`, `Notification`) affect the code complexity for each model? 

Jika semua logika ditumpuk ke dalam Model, maka setiap Model akan sangat bergantung satu sama lain karena harus saling memanggil untuk menyelesaikan satu proses bisnis. Misalnya, Model Program harus tahu cara membuat Notification sekaligus cara mengirimkannya ke Subscriber, sehingga ketiga model tersebut menjadi sangat tightly coupled. Kompleksitas kode akan meningkat drastis karena setiap perubahan kecil pada satu Model berpotensi mempengaruhi Model lainnya. Akibatnya, kode menjadi sangat sulit dibaca, diuji, dan dikembangkan seiring bertambahnya fitur.

> 3. Have you explored more about **Postman**? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects. 

Postman sangat membantu dalam menguji endpoint HTTP secara manual tanpa perlu membuat frontend terlebih dahulu, sehingga kita bisa langsung memverifikasi apakah response dari server sudah sesuai ekspektasi. Fitur Collections memungkinkan kita menyimpan dan mengorganisir semua request dalam satu tempat, yang sangat berguna agar tidak perlu mengetik ulang URL dan body request setiap kali melakukan pengujian. Ke depannya, fitur automated testing di Postman juga terlihat menjanjikan untuk diintegrasikan ke dalam alur pengujian di Group Project.

#### Reflection Publisher-3

> 1. Observer Pattern has two variations: **Push model** (publisher pushes data to subscribers) and **Pull model** (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use? 

Tutorial ini menggunakan variasi Push model. Hal ini terlihat dari cara kerja fungsi notify() di NotificationService yang secara aktif mengirimkan data notifikasi ke setiap Subscriber melalui HTTP POST request tanpa menunggu Subscriber meminta terlebih dahulu. Publisher (BambangShop) yang memegang kendali penuh atas kapan dan data apa yang dikirimkan ke Subscriber.

> 2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull) 

Keuntungan Pull model adalah Subscriber memiliki kendali penuh atas kapan mereka ingin mengambil data, sehingga tidak akan terjadi penumpukan notifikasi yang tidak sempat diproses jika Subscriber sedang sibuk atau offline. Selain itu, Publisher menjadi lebih sederhana karena tidak perlu menyimpan detail kontak setiap Subscriber dan tidak perlu aktif mengirim data. Namun kerugiannya adalah Subscriber harus terus-menerus melakukan polling ke Publisher untuk mengecek apakah ada data baru, yang menyebabkan banyak request yang sia-sia jika tidak ada perubahan. Dalam konteks BambangShop yang membutuhkan notifikasi real-time saat produk dibuat atau dihapus, Pull model kurang cocok karena ada jeda waktu antara kejadian dan saat Subscriber mengetahuinya.

> 3. Explain what will happen to the program if we decide to not use multi-threading in the notification process. 

Tanpa multi-threading, proses pengiriman notifikasi ke seluruh Subscriber akan dilakukan secara sekuensial satu per satu dalam satu thread yang sama dengan thread utama aplikasi. Artinya, selama proses notifikasi berlangsung, server tidak bisa merespons request lain dari pengguna, sehingga aplikasi terasa lambat atau bahkan tidak responsif sama sekali. Jika salah satu Subscriber membutuhkan waktu lama untuk merespons (misalnya karena jaringannya lambat atau sedang down), seluruh proses notifikasi akan tertahan dan semua Subscriber berikutnya ikut menunggu. Dengan multi-threading seperti yang diterapkan di tutorial ini, setiap notifikasi dikirim di thread terpisah sehingga proses pengiriman ke banyak Subscriber bisa berjalan paralel tanpa memblokir thread utama.