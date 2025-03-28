# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why it is necessary for this case, and explain why we do not use Mutex<> instead?

RwLock dipilih karena sifat alami akses data dalam sistem notifikasi ini. Saat ada operasi seperti menampilkan daftar subscriber, banyak thread mungkin perlu membaca data secara bersamaan. RwLock mengizinkan banyak thread membaca data sekaligus selama tidak ada thread yang menulis. Jika menggunakan Mutex, setiap thread harus menunggu giliran meskipun hanya ingin membaca data, yang akan memperlambat sistem. Mutex lebih cocok digunakan ketika operasi baca dan tulis sama seringnya atau ketika operasi tulis lebih dominan. Dalam kasus ini, karena operasi baca jauh lebih sering terjadi, RwLock memberikan performa yang lebih baik.

2. In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static” variable. Compared to Java where we can mutate the content of a static variable via a static function, why did not Rust allow us to do so?

Rust mencegah mutasi langsung variabel static karena bahasa ini dirancang untuk menjamin keamanan memori tanpa perlu garbage collector. Di Java, variabel static bisa diubah langsung karena Java memiliki runtime yang menangani konkurensi dan memori secara otomatis. Rust memilih pendekatan berbeda dengan memaksa developer untuk secara eksplisit mengelola akses thread-safe ke data global menggunakan primitif seperti Mutex atau RwLock. lazy_static digunakan karena menyediakan cara aman untuk inisialisasi variabel global yang thread-safe. Ini mencegah masalah seperti race condition yang bisa terjadi jika variabel static diubah secara langsung oleh banyak thread.

#### Reflection Subscriber-2

1. Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not, explain why you did not do so. If yes, explain things that you have learned from those other parts of code.

Saya telah mempelajari bagian lib.rs yang bertugas sebagai inti konfigurasi dan error handling. Dari sini, saya memahami cara Rust mengelola environment variables menggunakan dotenvy dan Figment untuk menggabungkan konfigurasi default dengan variabel lingkungan. Struktur AppConfig yang menggunakan Getters dari library getset memungkinkan akses aman ke nilai konfigurasi tanpa risiko modifikasi tidak sengaja. Selain itu, implementasi lazy_static untuk REQWEST_CLIENT dan APP_CONFIG mengajarkan cara membuat instance global yang diinisialisasi sekali (thread-safe) — sesuatu yang krusial untuk koneksi HTTP yang dipakai di banyak tempat. Error handling dengan tipe kustom ErrorResponse juga memberi contoh konkret cara menyusun respons error yang konsisten.

2. Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?

Observer Pattern mempermudah penambahan subscriber karena sistem hanya perlu memanggil endpoint /subscribe/<product_type> dengan URL receiver baru. Contoh: Jika ada 5 instance receiver berjalan di port berbeda (8001-8005), masing-masing bisa subscribe ke publisher dengan mengirim URL unik mereka. Publisher akan menyimpan semua URL di DashMap, dan setiap notifikasi akan dikirim ke seluruh subscriber terdaftar secara paralel. Jika ada instance Main app baru, selama mereka mengimplementasikan endpoint/receive yang sesuai, integrasi tetap mudah tanpa perlu modifikasi kode publisher — cukup tambahkan URL mereka via subscribe.

3. Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).

- Subscribe/unsubscribe dengan berbagai product_type
- Mengirim notifikasi manual via /receive
- Simulasi error seperti unsubscribe ke product_type yang tidak ada

Fitur Environment Variables di Postman sangat membantu untuk menguji di lingkungan berbeda tanpa mengubah URL manual. Automated testing dengan Newman berguna untuk menjalankan skenario uji secara konsisten, terutama saat ada perubahan kode.