# Tugas Praktikum UAS { Pertemuan ke 16 } <img src=https://logos-download.com/wp-content/uploads/2016/05/MySQL_logo_logotype.png width="130px" >

|**Nama**|**NIM**|**Kelas**|**Matkul**|
|----|---|-----|------|
|Gladis Toti Anggraini |312310566|TI.23.A5|Basis Data|


## INPUT DATA

```
CREATE TABLE Perusahaan(
id_p VARCHAR(10) PRIMARY KEY,
nama VARCHAR(45) NOT NULL,
alamat VARCHAR(45)
);

INSERT INTO Perusahaan VALUES
('P01', 'Kantor Pusat', NULL),
('P02', 'Cabang Bekasi', NULL);

SELECT * FROM Perusahaan;

CREATE TABLE Departemen(
id_dept VARCHAR(10) PRIMARY KEY,
nama VARCHAR(45) NOT NULL,
id_p VARCHAR(10) NOT NULL,
manajer_nik VARCHAR(10) DEFAULT NULL
);

SELECT * FROM Perusahaan;

INSERT INTO Departemen VALUES
('D01', 'Produksi', 'P02', 'N01'),
('D02', 'Marketing', 'P01', 'N03'),
('D03', 'RnD', 'P02', NULL),
('D04', 'Logistik', 'P02', NULL);
SELECT * FROM Departemen;

CREATE TABLE Karyawan (
nik VARCHAR(10) PRIMARY KEY,
nama VARCHAR(50),
id_dept VARCHAR(10),
sup_nik VARCHAR(10),
gaji_pokok INT,
FOREIGN KEY (id_dept) REFERENCES Departemen(id_dept),
FOREIGN KEY (sup_nik) REFERENCES Karyawan(nik)
);

INSERT INTO Karyawan (nik, nama, id_dept, sup_nik, gaji_pokok) VALUES
('N01', 'Ari', 'D01', NULL, 2000000),
('N02', 'Dina', 'D01', NULL, 2500000),
('N03', 'Rika', 'D03', NULL, 2400000),
('N04', 'Ratih', 'D01', 'N01', 3000000),
('N05', 'Riko', 'D01', 'N01', 2800000),
('N06', 'Dani', 'D02', NULL, 2100000),
('N07', 'Anis', 'D02', 'N06', 5000000),
('N08', 'Dika', 'D02', 'N06', 4000000),
('N09', 'Raka', 'D03', 'N06', 2000000);

SELECT * FROM Karyawan;

CREATE TABLE Project(
id_proj VARCHAR(10) PRIMARY KEY,
nama VARCHAR(45) NOT NULL,
tgl_mulai DATE,
tgl_selesai DATE,
status INT
);

INSERT INTO Project VALUES
('PJ01', 'A', '2019-01-10', '2019-03-10', '1'),
('PJ02', 'B', '2019-02-15', '2019-04-10', '1'),
('PJ03', 'C', '2019-03-21', '2019-05-10', '1');

SELECT * FROM Project;

CREATE TABLE Project_detail(
id_proj VARCHAR(10),
nik VARCHAR(10),
PRIMARY KEY (id_proj, nik),
FOREIGN KEY (id_proj) REFERENCES Project(id_proj),
FOREIGN KEY (nik) REFERENCES Karyawan(nik)
);


INSERT INTO Project_detail VALUES
('PJ01', 'N01'),
('PJ01', 'N02'),
('PJ01', 'N03'),
('PJ01', 'N04'),
('PJ01', 'N05'),
('PJ01', 'N07'),
('PJ01', 'N08'),
('PJ02', 'N01'),
('PJ02', 'N03'),
('PJ02', 'N05'),
('PJ03', 'N03'),
('PJ03', 'N07'),
('PJ03', 'N08');

SELECT * FROM Project_detail;
```

## SOAL
Berdasarkan ERD dan Sampel Data diatas buatla Query SQL untuk:

### 1. Menampilkan Nama Karyawan yang Berada di Departemen yang Dipimpin oleh Manajer dengan Nama 'Rika'

```
SELECT Karyawan.nik, Karyawan.nama
FROM Karyawan
JOIN Departemen ON Karyawan.id_dept = Departemen.id_dept
WHERE Departemen.manajer_nik = (
    SELECT nik FROM Karyawan WHERE nama = 'Rika'
);
```

### 2. Menampilkan Nama Proyek yang dikerjakan oleh Karyawan dari Departemen 'RnD'

```
SELECT DISTINCT Project.nama
FROM Project
JOIN Project_detail ON Project.id_proj = Project_detail.id_proj
JOIN Karyawan ON Project_detail.nik = Karyawan.nik
JOIN Departemen ON Karyawan.id_dept = Departemen.id_dept
WHERE Departemen.nama = 'RnD';
```

### 3. Menampilkan Nama Karyawan yang Terlibat dalam Lebih dari Satu Proyek

```
SELECT Karyawan.nik, Karyawan.nama, COUNT(Project_detail.id_proj) AS jumlah_proyek
FROM Karyawan
JOIN Project_detail ON Karyawan.nik = Project_detail.nik
GROUP BY Karyawan.nik, Karyawan.nama
HAVING COUNT(Project_detail.id_proj) > 1;
```

### 4. Menampilkan Nama Proyek yang melibatkan Karyawan terbanyak.

```
SELECT Project.nama, COUNT(Project_detail.nik) AS jumlah_karyawan
FROM Project
JOIN Project_detail ON Project.id_proj = Project_detail.id_proj
GROUP BY Project.nama
ORDER BY jumlah_karyawan DESC
LIMIT 1;
```

### 5. Menampilkan Nama Proyek yang Diikuti oleh Karyawan dengan Gaji Pokok Kurang dari 3 Juta

```
SELECT DISTINCT Project.nama
FROM Project
JOIN Project_detail ON Project.id_proj = Project_detail.id_proj
JOIN Karyawan ON Project_detail.nik = Karyawan.nik
JOIN Gaji ON Karyawan.nik = Gaji.pokok
WHERE Gaji.gaji_pokok < 3000000;
```
