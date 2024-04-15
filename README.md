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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. Menurut saya interface subscriber dibutuhkan bila terdapat beberapa jenis subscriber atau model yang akan menerima notifikasi dari publisher dengan caranya masing-masing. Dalam kasus ini, interface sangat membantu karena publisher tidak perlu mengetahui teknis pemrorsesan notifikasi dari masing-masing jenis penerima notifikasi. Selain itu, ini akan menegakkan prinsip Dependency Inversion karena semua ketergantungan dihandle oleh interface dan bukan concrete class, dan Prinsip Open-Closed karena penambahan jenis penerima notifikasi tidak perlu mengutak-atik interface Subscriber yang sudah ada. Di sisi lain, jika dapat dipastikan bahwa subscriber adalah satu-satunya penerima informasi dari publisher, maka menurut saya interface tidak terlalu dibutuhkan. Namun, saya tetap merekomendasikan menggunakan interface untuk menjunjung tinggi best practice dan mengantisipasi penambahan fitur di waktu yang akan datang.

2. Untuk penggunaan Vec saya rasa sudah cukup (sufficient), karena dalam konteks ini Vec digunakan untuk menyimpan semua product atau subscriber yang ada sebagai return value dari function listAll(). Namun jika diperlukan operasi-operasi pada beberapa instance dalam list tersebut, terutama jika menggunakan id, mungkin eksekusinya kurang efisien (O(n)), karena harus traverse masing-masing instance sampai mendapatkan instance yang diinginkan. Jika demikian, maka mungkin lebih baik menggunakan DashMap, agar pencarian memakan waktu O(1). Untuk penggunaan DashMap sebagai struktur data utama pada repository saya merasa ini tidak necessary, karena penggunakan Vec (list) sudah cukup untuk menyimpan data yang ada. Namun DashMap ada untungnya, yaitu sangat efisien untuk dilakukan searching atau pencarian berdasarkan key yang ditentukan. Jika akan banyak melakukan searching atau get instance, makan DashMap adalah pilihan struktur data yang baik.

3. Menurut saya penggunaan Singleton Pattern dapat dikolaborasikan dengan penggunaan DashMap. DashMap diperlukan agar Map dapat diakses dan diupdate oleh beberapa thread sekaligus tanpa harus memperhatikan isu sinkronisasi. Sementara itu Singleton Pattern memastikan hanya ada satu instance SUBSCRIBERS yang ada. Kesimpulannya, jika kita tahu program kita tidak akan menggunakan multiple threads, DashMap tidak perlu digunakan. Namun jika akan dilakukan multiple threading, maka DashMap perlu digunakan. Untuk Singleton Pattern menurut saya lebih baik jika tetap digunakan dalam kasus apapun, untuk menjaga-jaga agar tidak ada lebih dari satu instance SUBSCRIBER di setiap waktu.


#### Reflection Publisher-2

1. Pemisahan antara Model, Service, dan Repository berkaitan erat dengan Single Responsibility Principle. Model mencakup representasi data dan business logic yang berkaitan dengan representasi data. Service mencakup business logic yang tidak berkaitan langsung dengan representasi data, misal interaksi antar beberapa model yang berbeda, atau melakukan komputasi/validasi data sebelum dimasukkan dalam model. Repository berkaitan dengan penyimpanan semua instance dari model yang ada, serta menghandle CRUD dari model tersebut. SRP membuat aplikasi terlihat lebih modular dan mudah dipahami. Selain itu, akan terbentuk abstraksi sehingga masing-masing bagian dapat fokus pada tasknya masing-masing tanpa harus memikirkan implementasi pada bagian lain yang berkaitan.

2. Mungkin karena saya sudah cukup terbiasa dengan adanya model, repository, dan service, imajinasi saya jika ketiga bagian ini digabungkan dalam satu file tetap bersifat modular dan terpisah. Pertama, harus ada class untuk masing-masing model. Ini berisi getter, setter, dan semua function/method yang mempengaruhi data pada instance class tersebut. Lalu ada struktur data collections bagi masing-masing model untuk menyimpan semua instance model yang ada. Terakhir ada function-function (bukan class) yang berisi business logic dari aplikasi, termasuk CRUD. Function-function ini dapat berinteraksi langsung dengan class model dan struktur data collections. Mengenai kompleksitas code, menurut saya Model tanpa repository dan service lebih sulit dipahami, karena adanya penggabungan antara representasi data model, penyimpana data, dan business logic aplikasi. Akan lebih baik jika modul CRUD dibuat terpisah (dengan adanya repository), sehingga function-function business logic (service) dapat menggunakan modul CRUD tersebut untuk berinteraksi dengan data (model).

3. Postman cukup membantu saya dalam menguji apakah saya sudah menghandle request dengan tepat. Karena modul kali ini berhubungan dengan backend, maka saya dapat melihat apakah data yang dikembalikan sebagai respons sudah sesuai dengan yang saya inginkan atau tidak. Contoh sederhana adalah jika request mengembalikan response 404, padahal ekspektasi saya akan mengembalikan data JSON. Saya akan kembali melihat code saya dan mencari tahu penyebab masalahnya. Menurut saya fitur ini sangat bermanfaat untuk proyek akhir karena seperti yang diketahui pengerjaan frontend dan backend dipisah, bahkan beberapa modul backend dipisah juga. Oleh karena itu postman baik digunakan untuk memeriksa apakah pemrosesan data request sudah dihandle dengan tepat.

#### Reflection Publisher-3
