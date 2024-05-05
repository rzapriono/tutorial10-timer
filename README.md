# 1.2 Understanding how it works.
![1.2](./img/experiment_1.2.png)
Dari output yang muncul saat menjalankan program, terlihat bahwa teks "Reza Apriono's computer: hey hey" diprint sebelum teks "Reza Apriono's computer: howdy!" dan "Reza Apriono's computer: done!". Task asynchronous yang mencetak “Reza Apriono's computer: howdy!”, menunggu selama dua detik, dan kemudian mencetak “Reza Apriono's computer: done!” sudah dispawn. Namun, karena sifat asynchronousnya, task ini tidak langsung dijalankan. Dengan adanya `spawner.spawn(async { ... });`, task tersebut akan dieksekusi secara asynchronous, yang artinya tidak menunggu task sebelumnya selesai sebelum melanjutkan ke perintah berikutnya. Oleh karena itu, "Reza Apriono's computer: hey hey" yang berada di luar fungsi async akan diprint terlebih dahulu karena task yang bertugas mencetak "Reza Apriono's computer: howdy!" dan "Reza Apriono's computer: done!" masih menunggu hasil dari future, sementara  fungsi main melanjutkan eksekusi selagi task tersebut menunggu. Jadi, pesan "Reza Apriono's computer: hey hey" muncul lebih dulu karena dicetak sebelum task yang di-spawn selesai dijalankan.

# 1.3  Multiple Spawn and removing drop.
Multiple spawn
![1.3(1)](./img/experiment_1.3(1).png)

Menghilangkan statement: drop(spawner);
![1.3(2)](./img/experiment_1.3(2).png)

Menambahkan kembali statement: drop(spawner);
![1.3(3)](./img/experiment_1.3(3).png)

Dari output yang muncul saat menjalankan program, terlihat bahwa pada gambar pertama menggunakan multiple spawn. Adanya beberapa spawner ini menyebabkan terdapat beberapa task yang dijadwalkan untuk dijalankan melalui spawner dan dieksekusi dengan executor. Task-task tersebut masuk ke dalam semacam queue untuk dijalankan dan dieksekusi. Urutan pencetakan “Reza Apriono's computer: done!” bisa berbeda-beda karena urutan eksekusinya bergantung pada executor yang menjalankannya dan setiap task tersebut memiliki waktu untuk wait yang sama.
Selanjutnya, pada gambar kedua, dapat terlihat jika `drop(spawner);` dihilangkan maka executor tidak akan berhenti karena tidak menerima kabar apakah akan terjadi suatu pengiriman data lagi oleh spawner dan apakah masih ada task baru yang akan datang atau semua task sudah selesai dijalankan. Hal ini menyebabkan executor dan program akan terus berjalan.
Lalu pada gambar ketiga, terlihat bahwa dengan menambahkan kembali `drop(spawner);` maka program akan berjalan normal karena executor akan mengambil satu task dari task sender dan mengeksekusinya, kemudian mengulang proses tersebut hingga task pada queue habis dan pada akhirnya spawner didrop sehingga memungkinkan executor untuk berhenti.