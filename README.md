## REFLECTION MODULE 9

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable? <br>
    a. Unary merupakan interaksi yang mengirim 1 *request* dan menerima 1 *response*. Unary paling cocok digunakan pada operasi sederhana tidak memerlukan pertukaran data yang *continous*, seperti mengambil suatu item dari basis data, otentikasi pengguna, dan permintaan-permintaan yang berdiri sendiri di mana client hanya perlu mengetahui hasilnya setelah operasi selesai. Contoh pada tutorial ini adalah Payment Service<br>

    b. Server Streaming RPC merupakan interaksi dimana Client memberikan 1 *request* dan server mengirim balik stream respons. Server Streaming RPC paling sesuai digunakan untuk situasi dimana server perlu mengirimkan data secara berkelanjutan atau dalam jumlah besar ke client, seperti pembaruan *real-time* atau pengiriman data yang besar dan terus-menerus. Contoh pada tutorial ini adalah Transaction Service. <br> 

    c. Bi-directional Streaming RPC merupakan interaksi antara client dan server dimana keduanya dapat mengirim stream data satu sama lain dalam suatu sesi yang sama. Bi-directional Streaming RPC paling cocok digunakan untuk komunikasi interaktif real-time yang membutuhkan komunikasi 2 arah yang berkesinambungan. Contoh pada tutorial ini adalah Chat Service. <br>


2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption? <br>
 
    Dalam implementasi gRPC service di Rust, potensial keamanan dari step otentikasi hingga data enkripsi sangatlah penting. Otentikasi adalah tahap pertama untuk mengetahui siapa yang bisa mengakses sistem. Mekanisme seperti token JWT atau otentikasi dua arah dengan TLS harus diterapkan untuk memverifikasi identitas sebelum mengizinkan akses ke layanan. Setelah itu, otorisasi perlu diperkuat untuk menentukan bahwa pengguna memiliki hak untuk melakukan aksi tertentu, seperti mengakses atau memodifikasi sumber daya. Untuk implementasi otorisasi bisa menggunakan middleware. Selain 2 tahapan di atas, enkripsi data melalui TLS juga penting untuk mengamankan proses komunikasi antara client dan server. 

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications? <br>

    Dalam handling bidirectional streaming di Rust gRPC, terutama untuk aplikasi chat terdapat berbagai tantangan atau isu yang dapat muncul seperti: <br>
    a. Pengelolaan memory dan asinkronus karena pada Rust pengelolaan kepemilikan dan *life cycle* data perlu dikelola dengan baik agar tidak terjadi *memory leak* atau *race conditions* <br>
    b. Menjaga keamanan (Enkripsi dan Autentikasi) karena data sensitif seperti pesan pribadi terus menerus dikirim <br>
    c. Concurrency untuk memastikan aplikasi dapat menangani banyak pesan bukanlah hal yang mudah <br>

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services? <br>
    Keuntungan: <br>
    a. Integrasi dengan Tokio: ReceiverStream terintegrasi dengan baik dengan ekosistem Tokio <br> 
    b. Pengelolaan data secara async <br>

    Kerugian: <br>
    a. Potensi *overhead* <br>
    b. Menambah kompleksitas <br>
    c. Ketergantungan pada tokio karena ReceiverStream merupakan bagian dari ekosistem Tokio sehingga penggunaannya mengharuskan seluruh aplikasi untuk berjalan pada runtime Tokio <br>


5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time? <br>

    1. Pemisahan Modul: Pisahkan fungsi dan layanan gRPC ke dalam modul atau crate yang berbeda. Misalnya, definisi layanan gRPC (`services.proto`), implementasi server (`grpc_server.rs`), dan klien (`grpc_client.rs`) dapat dikelola dalam modul yang berbeda. Ini memudahkan pengelolaan kode dan reuse. <br>
    2. Penggunaan trait untuk mendefinisikan fungsionalitas yang bisa dibagi antar komponen <br>
    3. Menggunakan library luar untuk tugas-tugas umum agar mengurangi duplikasi kode <br>

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic? <br>

    Dalam mengimplementasikan MyPaymentService untuk pemrosesan pembayaran yang lebih kompleks, terdapat langkah-langkah tambahan yang bisa membantu: <br>
    a. Validasi data untuk memastikan semua informasi pembayaran yang masuk adalah valid <br>
    b. Sistem harus terintegrasi dengan aman dengan *gateaway* pembayaran untuk memproses transaksi <br>
    c. Penggantian implementasi dengan menggunakan server streaming agar mendukung proses transaksi data dalam jumlah yang besar secara besar dan terus menerus secara bersamaan. <br>

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms? <br>

    Penggunaan gRPC sebagai protokol komunikasi memberikan dampak signifikan pada arsitektur dan desain sistem terdistribusi. Dengan mengandalkan definisi yang jelas di file proto, gRPC mempermudah interaksi antarmodul tanpa perlu konfigurasi HTTP method secara manual, seperti yang sering diperlukan dalam pendekatan REST. Dengan beroperasi di atas HTTP/2, gRPC memfasilitasi komunikasi yang lebih efisien dan cepat antar layanan dengan overhead yang rendah dan kemampuan streaming yang superior. Fitur-fitur ini membuat sistem yang menggunakan gRPC dapat berkomunikasi lebih efektif dan efisien, meningkatkan kinerja dan kemudahan integrasi antar berbagai komponen sistem. <br>

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs? <br>
    Kelebihan: <br>
    Kemampuan dalam mengelola *multiple request* dan *responses* melalui satu koneksi saja menggunakan teknik multiplexing. Dengan teknik tersebut, dapat secara efektif mengurangi latency dan meningkatkan performa aplikasi karena jika dibandingkan dengan HTTP/1.1, HTTP/2 memungkinkan multiple requests atau responses untuk dikirim melalui satu koneksi TCP yang sama secara bersamaan. <br>

    Kekurangan: <br>
    Implementasi HTTP/2 cenderung lebih kompleks dan membutuhkan enkripsi SSL/TLS, yang dapat menambah overhead administratif dan biaya. Di sisi lain, WebSocket pada HTTP/1.1 memungkinkan komunikasi dua arah dan real-time yang efektif, yang dalam beberapa kasus dapat lebih sederhana untuk diatur dibandingkan dengan setup pada HTTP/2. <br>

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness? <br>

    Model request-response pada REST API berbeda secara signifikan dari bidirectional streaming pada gRPC, terutama dalam hal komunikasi real-time dan responsivitas. Dalam REST API, yang menggunakan HTTP/1.1, komunikasi dibatasi pada pola di mana klien mengirim satu request dan menunggu satu response dari server. Hal ini bisa membatasi dalam aplikasi real-time karena setiap interaksi memerlukan round trip yang terpisah, yang dapat menyebabkan latensi yang lebih tinggi. Sebaliknya, gRPC menggunakan HTTP/2 yang mendukung multiplexing, memungkinkan transmisi simultan dari banyak requests dan responses dalam satu koneksi yang sama. Ini mengurangi latensi secara signifikan dan meningkatkan efisiensi komunikasi, membuat gRPC sangat cocok untuk aplikasi yang memerlukan pertukaran data secara real-time dan responsif antara klien dan server. <br>

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads? <br>

    Penggunaan pendekatan berbasis skema dalam gRPC dengan Protocol Buffers berbeda signifikan dari pendekatan tanpa skema yang lebih fleksibel dari JSON dalam REST API. Protocol Buffers dalam gRPC menyediakan validasi dan optimisasi data secara otomatis, membantu dalam mengurangi bandwith dan meningkatkan kecepatan pemrosesan data. Ini sangat berguna untuk aplikasi yang memerlukan efisiensi tinggi dan format data yang konsisten. Sedangkan JSON dalam REST API menawarkan fleksibilitas lebih dalam penanganan data yang dinamis dan berubah-ubah, memudahkan adaptasi dengan kebutuhan yang beragam tanpa perlu mendefinisikan skema terlebih dahulu. Namun, pendekatan ini bisa lebih lambat dan memakan lebih banyak sumber daya dibandingkan dengan pendekatan berbasis skema karena JSON harus diparse setiap kali data diterima atau dikirim. <br>