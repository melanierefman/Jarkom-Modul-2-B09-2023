# Praktikum Jarkom Modul 1 Kelompok B09

Anggota Kelompok B09:
| Nama | NRP |
| ---------------------- | ---------- |
| Melanie Sayyidina Sabrina Refman | 5025211029 |
| I Gusti Agung Ngurah Adhi Sanjaya | 5025211056 |

----
## Soal No.1
User melakukan berbagai aktivitas dengan menggunakan protokol FTP. Salah satunya
adalah mengunggah suatu file.
- a. Berapakah sequence number (raw) pada packet yang menunjukkan aktivitas tersebut?
- b. Berapakah acknowledge number (raw) pada packet yang menunjukkan aktivitas tersebut?
- c. Berapakah sequence number (raw) pada packet yang menunjukkan response dari aktivitas tersebut?
- d. Berapakah acknowledge number (raw) pada packet yang menunjukkan response dari aktivitas tersebut?
### Penyelesaian
a.
- Pertama mendownload capture file dari soal
- Mendownload capture file dan membukanya pada wireshark
- Kemudian filter menggunakan `ftp`
  ![image](https://github.com/melanierefman/Jarkom-Modul-1-B09-2023/assets/87845735/bb6dcfa1-39d4-4053-a20a-5a99036ea7f3)
- Lalu setelah keluar packet yang menggunakan protocol ftp, langkah selanjutnya adalah mencari packet yang terdapat request `STOR` yang dimana request ini artinya menggungah sesuatu.
  ![image](https://github.com/melanierefman/Jarkom-Modul-1-B09-2023/assets/87845735/58852693-005c-445a-9107-86b2dc3a3c2e)
- Setelah mendapatkan lalu membuka detail `TCP` atau `Transmission Control Protocol` dari packet ini.
![image](https://github.com/melanierefman/Jarkom-Modul-1-B09-2023/assets/87845735/234ce126-1fa0-490e-b17f-3e15289d48ea)
- Setelah itu kita bisa menemukan Sequence Number (raw) dari packet request `STOR` ini.
- Jawabannya : 258040667
  
b.
- Dengan cara yang sama pada bagian a, kita akan menemukan Acknowledeg number (raw) dari packet ini.
- Jawabannya : 1044861039
  
c.